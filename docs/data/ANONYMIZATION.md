# Anonymization and Privacy Protection

**Dataset:** PSI_YÉKIT 1.0  
**Compliance:** Lei Geral de Proteção de Dados (LGPD, Lei 13.709/2018)  
**Method:** k-anonymity with k ≥ 5  
**Version:** 1.0  
**Date:** 2026-01-20

## Objetivo

Este documento descreve os procedimentos de **anonimização e agregação** aplicados ao dataset PSI_YÉKIT 1.0 para garantir conformidade com a **Lei Geral de Proteção de Dados (LGPD)** e proteger a privacidade de indivíduos.

## Princípios de privacidade

O dataset PSI_YÉKIT 1.0 segue os seguintes princípios:

1. **k-anonymity (k ≥ 5)**: Cada combinação de atributos quasi-identificadores (município, ano-mês) deve conter pelo menos 5 indivíduos/domicílios.
2. **Agregação territorial**: Todos os dados são agregados em nível **municipal-mensal**. Não há dados de bairros, setores censitários ou indivíduos.
3. **Supressão**: Valores que violam k-anonymity são **suprimidos** (marcados como `NA` com `quality_flag = "suppressed"`).
4. **Temporal aggregation**: Quando a supressão mensal é necessária, aplicamos médias móveis de 3 anos para variáveis sensíveis (ex: mortalidade infantil).

## Variáveis sensíveis e critérios de supressão

As seguintes variáveis são consideradas **sensíveis** e estão sujeitas a supressão quando k < 5:

| Variável | Critério de supressão | Justificativa |
|----------|----------------------|---------------|
| `V_income_percapita_brl` | Suprimida se população estimada < 5 no município-mês | Pode revelar renda de poucos domicílios |
| `V_poverty_rate_pct` | Suprimida se população estimada < 5 no município-mês | Pode revelar condição de poucos domicílios |
| `V_infant_mortality_rate` | Suprimida se nascidos vivos < 5 no município-ano | Pode revelar identidade de famílias |
| `E_water_access_pct` | Suprimida se domicílios < 5 no município-mês (anos censitários) | Pode revelar condição de poucos domicílios |

**Nota:** Variáveis agregadas de fonte oficial (ex: IBGE, DATASUS) já seguem regras de supressão na origem. Nosso procedimento adiciona camadas de proteção ao interpolar ou derivar valores.

## Procedimento de k-anonymity

### 1. Identificação de quasi-identificadores

Os **quasi-identificadores** no dataset são:
- `ibge_code` (código do município)
- `year_month` (ano-mês de referência)

Esses atributos, sozinhos, não identificam indivíduos, mas combinados com variáveis sensíveis podem reduzir o conjunto de possíveis indivíduos.

### 2. Contagem de indivíduos/domicílios

Para cada município-mês, estimamos o número de indivíduos ou domicílios relevantes:
- **População:** `E_pop_total` (IBGE)
- **Domicílios:** Estimado a partir do Censo (razão população/domicílios aplicada a estimativas intercensitárias)
- **Nascidos vivos:** SINASC (DATASUS)

### 3. Aplicação de supressão

Se o número de indivíduos/domicílios for **< 5**:
- O valor da variável sensível é substituído por `NA`.
- O campo `quality_flag` é marcado como `"suppressed"`.

### 4. Temporal aggregation (fallback)

Para a variável `V_infant_mortality_rate`, que depende de nascidos vivos (eventos raros em municípios pequenos):
- Aplicamos **média móvel de 3 anos** (janela centrada) antes de calcular a taxa.
- Isso aumenta k e reduz a necessidade de supressão.
- Ainda assim, se nascidos vivos acumulados em 3 anos < 5, o valor é suprimido.

## Vocabulário de `quality_flag`

O campo `quality_flag` indica a origem e qualidade do dado. Os valores possíveis são:

| Flag | Descrição | Exemplo |
|------|-----------|---------|
| `observed` | Dado diretamente observado, sem transformação | Precipitação de estação meteorológica |
| `estimated` | Estimativa oficial de fonte autorizada | Estimativa populacional IBGE (intercensitária) |
| `interpolated` | Interpolação temporal entre observações | Renda per capita interpolada entre censos |
| `calculated` | Derivado de outras variáveis por cálculo | SPI (calculado a partir de precipitação) |
| `suppressed` | Suprimido por k-anonymity (k < 5) | Renda em município com pop. < 5 |
| `census` | Dado de censo (observado, mas apenas em anos censitários) | Acesso à água em 2000, 2010, 2022 |

**Nota:** Uma variável pode ter múltiplos flags ao longo do tempo. Por exemplo:
- `E_water_access_pct` em 2010: `quality_flag = "census"` (observado no Censo 2010)
- `E_water_access_pct` em 2015: `quality_flag = "interpolated"` (interpolado entre Censos 2010 e 2022)
- `E_water_access_pct` em 2015, município pequeno: `quality_flag = "suppressed"` (k < 5)

## Tratamento de dados censitários

Variáveis derivadas de censos (2000, 2010, 2022) são:
- **Observadas** nos anos censitários: `quality_flag = "census"`
- **Interpoladas** linearmente nos anos intercensitários: `quality_flag = "interpolated"`
- **Suprimidas** quando k < 5: `quality_flag = "suppressed"`

A interpolação linear assume variação uniforme entre censos, o que é uma **simplificação**. Para análises que exigem maior precisão temporal, recomenda-se usar apenas valores censitários (`quality_flag = "census"`).

## Limitações e advertências

1. **k-anonymity não é anonimização perfeita**: k ≥ 5 reduz o risco de re-identificação, mas não o elimina completamente. Combinações com dados externos podem ainda permitir inferência.
2. **Agregação municipal**: Municípios muito pequenos (pop. < 1000) podem ter poucas observações mesmo após agregação temporal. Nesses casos, a supressão é frequente.
3. **Interpolação temporal**: Variáveis interpoladas (`quality_flag = "interpolated"`) não refletem variações reais entre censos. Use com cautela.
4. **Sem identificadores diretos**: O dataset **não contém** nomes, CPFs, endereços, coordenadas de domicílios ou qualquer identificador direto.

## Conformidade com a LGPD

O dataset PSI_YÉKIT 1.0 **não contém dados pessoais sensíveis** conforme definido pela LGPD (Art. 5º, II). Todos os dados são:
- **Agregados** em nível municipal (unidade territorial oficial).
- **Anonimizados** por supressão quando k < 5.
- **Derivados de fontes públicas** (IBGE, DATASUS, INMET, ANA, MapBiomas) que já aplicam suas próprias regras de proteção.

A base legal para o tratamento desses dados é:
- **Art. 7º, III (LGPD)**: Execução de políticas públicas (avaliação de risco hídrico).
- **Art. 7º, VIII (LGPD)**: Proteção da saúde (em colaboração com autoridades sanitárias).
- **Art. 11, II, alínea a (LGPD)**: Dados tornados públicos pelo titular (censos e registros públicos).

## Rastreabilidade

Todos os procedimentos de supressão e agregação estão documentados em:
- [PROVENANCE.md](PROVENANCE.md): Decisões de derivação e transformações.
- [DATA_DICTIONARY.csv](../data/DATA_DICTIONARY.csv): Coluna `derivation_method` descreve cada transformação.
- Scripts de processamento (disponíveis no repositório do software): [10.5281/zenodo.SOFTWARE_DOI_AQUI]

## Contato

Dúvidas sobre procedimentos de anonimização ou privacidade:

**E-mail:** [info@yekit.org](mailto:info@yekit.org)  
**Organização:** Yékit  
**Localização:** Medina, Minas Gerais, Brasil

## Referências

- Brasil. **Lei Geral de Proteção de Dados (LGPD)**, Lei nº 13.709, de 14 de agosto de 2018. Disponível em: [http://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm](http://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm)
- Sweeney, L. (2002). k-anonymity: A model for protecting privacy. *International Journal of Uncertainty, Fuzziness and Knowledge-Based Systems*, 10(5), 557-570.
- IBGE. **Normas de disseminação de dados individualizados**. Disponível em: [https://www.ibge.gov.br/](https://www.ibge.gov.br/)

