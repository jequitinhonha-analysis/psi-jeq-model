# PSI_YÉKIT 1.0 Dataset

**DOI:** `10.5281/zenodo.DATASET_DOI_AQUI`  
**Software DOI:** `10.5281/zenodo.SOFTWARE_DOI_AQUI`  
**License:** Creative Commons Attribution 4.0 International (CC BY 4.0)  
**Version:** 1.0  
**Release Date:** 2026-01-20

## Descrição

O **PSI_YÉKIT 1.0** é um dataset mensal, em nível municipal, para o Vale do Jequitinhonha (Minas Gerais, Brasil), cobrindo o período de **2000-01 a 2023-12**. Ele foi projetado para alimentar o modelo bayesiano de espaço de estados PSI_YÉKIT, que estima risco sistêmico hídrico e seus efeitos sociais.

O dataset organiza variáveis observáveis em **quatro dimensões**:

- **H (Hazard/Perigo)**: Precipitação, temperatura, índices de seca (SPI).
- **E (Exposure/Exposição)**: População total, população rural, acesso à água.
- **V (Vulnerability/Vulnerabilidade)**: Renda per capita, taxa de pobreza, mortalidade infantil.
- **L (Land use upstream/Uso do solo a montante)**: Área de silvicultura (eucalipto) a montante.

O modelo estima uma variável latente **Ψ(t)** (estado de risco sistêmico), que **não é observada diretamente** mas inferida pelo modelo bayesiano. **Ψ(t) é distribuída separadamente** (`psi_latent.csv`) para deixar clara sua natureza inferida.

## Estrutura dos arquivos

```
data/
├── psi_yekit_v1_observables.csv   # Variáveis observáveis (H, E, V, L)
├── psi_yekit_v1_psi_latent.csv    # Estado latente Ψ(t) (inferido)
├── DATA_DICTIONARY.csv            # Dicionário completo de variáveis
├── sources_metadata.csv           # Metadados das fontes de dados
├── units_metadata.csv             # Metadados de unidades de medida
└── checksums_sha256.txt           # Checksums SHA-256 dos arquivos
```

### Formato dos dados

Todos os CSVs seguem o formato **tidy/long**:

- **psi_yekit_v1_observables.csv**: Uma linha por município-mês, com colunas `ibge_code`, `municipality_name`, `year_month`, variáveis H/E/V/L, e `quality_flag`.
- **psi_yekit_v1_psi_latent.csv**: Uma linha por município-mês, com colunas `ibge_code`, `municipality_name`, `year_month`, `psi_mean`, `psi_sd`, `psi_q025`, `psi_q975`.

**Codificação:** UTF-8  
**Separador:** vírgula (`,`)  
**Decimal:** ponto (`.`)  
**Missing values:** `NA`

## Variável latente Ψ(t)

⚠️ **ATENÇÃO:** A variável **Ψ(t)** (estado latente de risco sistêmico) **não é uma observação direta**. Ela é **inferida** pelo modelo bayesiano PSI_YÉKIT a partir das variáveis observáveis (H, E, V, L).

- **Ψ(t)** reflete a acumulação de riscos ao longo do tempo, modelada como um processo de espaço de estados.
- Os valores fornecidos em `psi_latent.csv` são **estatísticas a posteriori** do modelo (média, desvio-padrão, intervalo de credibilidade 95%).
- **Não utilize Ψ(t) como se fosse uma medida empírica direta.** Ela é uma variável latente, sujeita a incerteza de modelo.

Para detalhes sobre o modelo, veja o repositório do software: `10.5281/zenodo.SOFTWARE_DOI_AQUI`

## Qualidade dos dados (`quality_flag`)

Cada registro possui um campo `quality_flag` que indica a origem/método do dado:

- `observed`: Dado diretamente observado (ex: estação meteorológica, censo).
- `estimated`: Estimativa oficial (ex: IBGE estimativas populacionais).
- `interpolated`: Interpolação temporal (ex: linear entre censos).
- `calculated`: Derivado de outras variáveis (ex: SPI, percentuais).
- `suppressed`: Suprimido por regras de anonimização (k < 5).
- `census`: Dado de censo (observado, mas disponível apenas em anos censitários).

Consulte [ANONYMIZATION.md](ANONYMIZATION.md) para detalhes sobre supressão e k-anonymity.

## Anonimização e LGPD

O dataset segue procedimentos de **k-anonymity** (k ≥ 5) conforme a Lei Geral de Proteção de Dados (LGPD, Lei 13.709/2018):

- Variáveis sensíveis (renda, pobreza, mortalidade infantil, acesso à água) foram **suprimidas** quando o número de indivíduos/domicílios no município-mês era menor que 5.
- A agregação é sempre em nível **municipal-mensal**; não há dados identificáveis de indivíduos.
- Consulte [ANONYMIZATION.md](ANONYMIZATION.md) para detalhes completos.

## Proveniência e rastreabilidade

Todas as variáveis derivadas (interpolações, cálculos, agregações upstream) estão documentadas em:

- [PROVENANCE.md](PROVENANCE.md): Decisões de derivação e rastreabilidade.
- [DATA_DICTIONARY.csv](../data/DATA_DICTIONARY.csv): Coluna `derivation_method` descreve cada transformação.
- [sources_metadata.csv](../data/sources_metadata.csv): Licenças e URLs das fontes originais.

## Uso e citação

**Citação recomendada:**

> Yékit (2026). PSI_YÉKIT 1.0 Dataset: Municipal-level water risk indicators for the Jequitinhonha Valley, Brazil (2000-2023). Zenodo. DOI: `10.5281/zenodo.DATASET_DOI_AQUI`

**Software associado:**

> Yékit (2026). PSI_YÉKIT: Bayesian state-space model for systemic water risk in the Jequitinhonha Valley. Zenodo. DOI: `10.5281/zenodo.SOFTWARE_DOI_AQUI`

## Licença

Este dataset é licenciado sob **Creative Commons Attribution 4.0 International (CC BY 4.0)**.

Você é livre para:
- **Compartilhar** — copiar e redistribuir o material em qualquer meio ou formato.
- **Adaptar** — remixar, transformar e criar a partir do material para qualquer fim, mesmo comercial.

Desde que:
- **Atribuição** — Você deve dar o crédito apropriado, fornecer um link para a licença e indicar se mudanças foram feitas.

Ver [LICENSE](../../LICENSE) e [LICENSES.md](../../LICENSES.md) para detalhes.

## Fontes de dados

As principais fontes de dados utilizadas são:

- **IBGE** (Censo Demográfico, Estimativas Populacionais, Códigos Geográficos): CC BY 4.0
- **INMET/ANA** (Precipitação, Temperatura): CC BY 4.0
- **DATASUS** (SIM, SINASC): CC BY 4.0
- **MapBiomas** (Land Use/Land Cover Collection 8): **CC BY 4.0 ou CC BY-SA 4.0** (a ser confirmado)

⚠️ **NOTA SOBRE MAPBIOMAS:** A licença específica da Coleção 8 precisa ser verificada. Se for CC BY-SA 4.0, o dataset derivado também deve ser CC BY-SA 4.0. Consulte [sources_metadata.csv](../data/sources_metadata.csv) para URLs e versões.

## Contato

**Organização:** Yékit  
**Portal:** [modelo.yekit.org](https://modelo.yekit.org)  
**E-mail:** [info@yekit.org](mailto:info@yekit.org)  
**Localização:** Medina, Minas Gerais, Brasil

## Changelog

### v1.0 (2026-01-20)
- Primeira versão pública do dataset PSI_YÉKIT.
- Cobertura: 2000-01 a 2023-12, municípios do Vale do Jequitinhonha.
- Variáveis: H (3), E (3), V (3), L (2), Ψ (1 latente).
- Formato tidy/long, k-anonymity k≥5, documentação completa.
