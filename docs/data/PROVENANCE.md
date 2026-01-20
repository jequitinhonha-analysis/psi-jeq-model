# Data Provenance and Traceability

**Dataset:** PSI_YÉKIT 1.0  
**Version:** 1.0  
**Date:** 2026-01-20

## Objetivo

Este documento rastreia a **proveniência** de todas as variáveis do dataset PSI_YÉKIT 1.0, incluindo:
- Fontes originais (instituição, produto, licença).
- Transformações aplicadas (limpeza, interpolação, agregação, cálculos).
- Decisões de derivação e rastreabilidade.

## Fontes de dados

Todas as fontes utilizadas são **públicas e institucionais**. Consulte [sources_metadata.csv](../data/sources_metadata.csv) para URLs completas, versões e datas de acesso.

| Source ID | Institution | Product | License | Variables Derived |
|-----------|-------------|---------|---------|-------------------|
| IBGE_GEO | IBGE | Códigos Geográficos | CC0 1.0 | `ibge_code`, `municipality_name` |
| INMET | INMET | Estações meteorológicas | CC BY 4.0 | `H_precipitation_mm`, `H_temperature_max_c` |
| ANA | ANA | Precipitação gridded | CC BY 4.0 | `H_precipitation_mm` |
| IBGE_CENSO | IBGE | Censo Demográfico (2000, 2010, 2022) | CC BY 4.0 | `E_pop_total`, `E_pop_rural_pct`, `E_water_access_pct`, `V_income_percapita_brl`, `V_poverty_rate_pct` |
| IBGE_EST | IBGE | Estimativas Populacionais | CC BY 4.0 | `E_pop_total` |
| DATASUS | DATASUS | SIM (mortalidade), SINASC (nascidos vivos) | CC BY 4.0 | `V_infant_mortality_rate` |
| MAPBIOMAS | MapBiomas | Land Use/Land Cover Collection 8 | **CC BY 4.0 ou CC BY-SA 4.0** | `L_eucalyptus_area_ha`, `L_eucalyptus_pct_catchment` |

⚠️ **NOTA SOBRE MAPBIOMAS:** A licença da Coleção 8 varia entre portais de distribuição. Verificar documentalmente antes de publicar o dataset. Se for CC BY-SA 4.0, o dataset deve ser CC BY-SA 4.0.

## Transformações por variável

### 1. Identificadores (sem transformação)

| Variable | Source | Derivation | Notes |
|----------|--------|------------|-------|
| `ibge_code` | IBGE_GEO | Direct mapping | Código oficial de 7 dígitos |
| `municipality_name` | IBGE_GEO | Direct mapping | Nome oficial (legibilidade) |
| `year_month` | Observation timestamp | Direct mapping | Formato YYYY-MM (ISO 8601) |

### 2. Perigos climáticos (H - Hazard)

#### `H_precipitation_mm` (Precipitação acumulada mensal)

- **Fontes:** INMET (estações) + ANA (gridded).
- **Transformações:**
  1. **Spatial averaging:** Interpolação de estações/grids para centroides municipais (Inverse Distance Weighting, raio 50 km).
  2. **Gap filling:** Interpolação linear temporal para meses faltantes (< 10% dos registros).
  3. **Quality flag:**
     - `observed`: Mês com estação ativa no município.
     - `interpolated`: Mês preenchido por interpolação temporal.
     - `estimated`: Mês derivado de grid ANA (sem estação local).
- **Período:** 2015-01 a 2024-12.
- **Unidade:** mm (milímetros).

#### `H_temperature_max_c` (Temperatura máxima média mensal)

- **Fonte:** INMET (estações).
- **Transformações:**
  1. **Spatial averaging:** Interpolação de estações para centroides municipais (IDW, raio 100 km).
  2. **Gap filling:** Exponential Moving Average (EMA, α=0.3) para meses faltantes.
  3. **Quality flag:**
     - `observed`: Mês com estação ativa no município.
     - `estimated`: Mês preenchido por EMA.
- **Período:** 2015-01 a 2024-12.
- **Unidade:** °C (graus Celsius).

#### `H_spi_3m` (Índice de Precipitação Padronizado, 3 meses)

- **Fonte:** Derivado de `H_precipitation_mm`.
- **Transformações:**
  1. **Cálculo do SPI:** Distribuição Gamma ajustada a janelas de 3 meses, padronização (média 0, SD 1).
  2. **Interpretação:** Valores < -1.5 indicam seca severa; > 1.5 indicam precipitação extrema.
  3. **Quality flag:** `calculated` (derivado de precipitação).
- **Período:** 2015-04 a 2024-12 (primeiros 3 meses não calculáveis).
- **Unidade:** Adimensional.

### 3. Exposição (E - Exposure)

#### `E_pop_total` (População total estimada)

- **Fontes:** IBGE_CENSO (2000, 2010, 2022) + IBGE_EST (estimativas anuais).
- **Transformações:**
  1. **Interpolação temporal:** Linear mensal entre censos e estimativas anuais.
  2. **Quality flag:**
     - `census`: Anos censitários (2000, 2010, 2022).
     - `estimated`: Estimativas oficiais IBGE (anos intercensitários).
     - `interpolated`: Interpolação mensal.
- **Período:** 2015-01 a 2024-12.
- **Unidade:** Pessoas (inteiro).

#### `E_pop_rural_pct` (Percentual da população rural)

- **Fonte:** IBGE_CENSO (2000, 2010, 2022).
- **Transformações:**
  1. **Cálculo:** `(pop_rural / pop_total) * 100`.
  2. **Interpolação temporal:** Linear mensal entre censos.
  3. **Quality flag:**
     - `census`: Anos censitários.
     - `interpolated`: Interpolação mensal.
- **Período:** 2015-01 a 2024-12.
- **Unidade:** % (percentual).

#### `E_water_access_pct` (Percentual de domicílios com acesso à rede de água)

- **Fonte:** IBGE_CENSO (2000, 2010, 2022).
- **Transformações:**
  1. **Cálculo:** `(domicílios com rede de água / domicílios totais) * 100`.
  2. **Interpolação temporal:** Linear mensal entre censos.
  3. **Supressão:** Valores suprimidos se domicílios < 5 (k-anonymity).
  4. **Quality flag:**
     - `census`: Anos censitários.
     - `interpolated`: Interpolação mensal.
     - `suppressed`: Valores suprimidos (k < 5).
- **Período:** 2015-01 a 2024-12.
- **Unidade:** % (percentual).

### 4. Vulnerabilidade (V - Vulnerability)

#### `V_income_percapita_brl` (Renda per capita média mensal)

- **Fonte:** IBGE_CENSO (2000, 2010, 2022).
- **Transformações:**
  1. **Deflação:** Valores convertidos para BRL 2024 usando IPCA (IBGE).
  2. **Interpolação temporal:** Linear mensal entre censos.
  3. **Supressão:** Valores suprimidos se população < 5 (k-anonymity).
  4. **Quality flag:**
     - `census`: Anos censitários.
     - `interpolated`: Interpolação mensal.
     - `suppressed`: Valores suprimidos (k < 5).
- **Período:** 2015-01 a 2024-12.
- **Unidade:** BRL (Reais, 2024).

#### `V_poverty_rate_pct` (Taxa de pobreza)

- **Fonte:** IBGE_CENSO (2000, 2010, 2022).
- **Transformações:**
  1. **Definição:** Percentual de pessoas com renda domiciliar per capita < 1/2 salário mínimo (vigente).
  2. **Interpolação temporal:** Linear mensal entre censos.
  3. **Supressão:** Valores suprimidos se população < 5 (k-anonymity).
  4. **Quality flag:**
     - `census`: Anos censitários.
     - `interpolated`: Interpolação mensal.
     - `suppressed`: Valores suprimidos (k < 5).
- **Período:** 2015-01 a 2024-12.
- **Unidade:** % (percentual).

#### `V_infant_mortality_rate` (Taxa de mortalidade infantil)

- **Fonte:** DATASUS (SIM + SINASC).
- **Transformações:**
  1. **Cálculo:** `(óbitos < 1 ano / nascidos vivos) * 1000`.
  2. **Agregação temporal:** Média móvel de 3 anos (janela centrada) para reduzir variabilidade e garantir k ≥ 5.
  3. **Supressão:** Valores suprimidos se nascidos vivos acumulados (3 anos) < 5 (k-anonymity).
  4. **Quality flag:**
     - `observed`: Calculado com dados anuais completos.
     - `suppressed`: Valores suprimidos (k < 5).
- **Período:** 2016-01 a 2024-12 (dados disponíveis até 2024; janela de 3 anos inicia em 2016).
- **Unidade:** por 1000 nascidos vivos.

### 5. Uso do solo a montante (L - Land use upstream)

#### `L_eucalyptus_area_ha` (Área de silvicultura/eucalipto a montante)

- **Fonte:** MAPBIOMAS Collection 8 (classe 9: silvicultura).
- **Transformações:**
  1. **Delimitação de bacias:** Direção de fluxo (FlowDir) a partir de DEM SRTM (30m).
  2. **Agregação upstream:** Acumulação de pixels de eucalipto a montante de cada município (seguindo rede de drenagem).
  3. **Resolução temporal:** Anual (MapBiomas fornece mapas anuais; utilizados 2015-2024).
  4. **Replicação mensal:** Valor anual replicado para todos os meses do ano.
  5. **Quality flag:**
     - `observed`: Ano com mapa MapBiomas disponível.
     - `estimated`: Interpolação para anos sem dados (se aplicável).
- **Período:** 2015-01 a 2024-12.
- **Unidade:** ha (hectares).

⚠️ **ATENÇÃO:** A licença do MapBiomas Collection 8 varia. Se for CC BY-SA 4.0, o dataset deve ser CC BY-SA 4.0.

#### `L_eucalyptus_pct_catchment` (Percentual de eucalipto na bacia a montante)

- **Fonte:** Derivado de `L_eucalyptus_area_ha` + área total da bacia a montante (SRTM).
- **Transformações:**
  1. **Cálculo:** `(L_eucalyptus_area_ha / área total upstream) * 100`.
  2. **Quality flag:** `calculated`.
- **Período:** 2015-01 a 2024-12.
- **Unidade:** % (percentual).

### 6. Variável latente (Ψ - Latent state)

⚠️ **ATENÇÃO:** A variável **Ψ(t)** **não é observada diretamente**. Ela é **inferida** pelo modelo bayesiano PSI_YÉKIT.

- **Fonte:** Modelo bayesiano de espaço de estados (PSI_YÉKIT).
- **Inputs:** H, E, V, L (variáveis observáveis).
- **Método:** Inferência bayesiana via Stan (MCMC).
- **Output:** Estatísticas a posteriori (`psi_mean`, `psi_sd`, `psi_q025`, `psi_q975`).
- **Arquivo separado:** `psi_latent.csv` (para deixar clara a natureza inferida).
- **Quality flag:** Não aplicável (variável latente).

## Decisões de derivação

### Interpolação temporal linear

Usada para variáveis censitárias (E, V) nos anos intercensitários. Assume variação uniforme entre censos, o que é uma **simplificação**. Para análises que exigem maior precisão temporal, recomenda-se usar apenas valores censitários (`quality_flag = "census"`).

### Média móvel de 3 anos (mortalidade infantil)

Aplicada para reduzir variabilidade e garantir k ≥ 5 em municípios pequenos. A janela de 3 anos (centrada) suaviza flutuações anuais, mas **reduz a resolução temporal**. Valores pontuais de anos individuais não estão disponíveis para municípios com poucos nascidos vivos.

### Agregação upstream (eucalipto)

Baseada em direção de fluxo (SRTM). Assume que a silvicultura a montante afeta o município a jusante (hipótese ecológica). A delimitação de bacias é aproximada (resolução 30m); bacias muito pequenas ou planas podem ter erros de direção de fluxo.

### Supressão por k-anonymity

Aplicada quando indivíduos/domicílios < 5. A supressão **reduz a completude do dataset**, especialmente para municípios pequenos. Valores suprimidos são marcados como `NA` com `quality_flag = "suppressed"`.

## Rastreabilidade completa

Todos os scripts de processamento estão disponíveis no repositório do software PSI_YÉKIT:

**DOI:** `10.5281/zenodo.SOFTWARE_DOI_AQUI`

Os scripts incluem:
- `01_download_sources.R` — Download das fontes originais.
- `02_process_climate.R` — Processamento de H (precipitação, temperatura, SPI).
- `03_process_census.R` — Processamento de E e V (censo, interpolação, supressão).
- `04_process_landuse.R` — Processamento de L (MapBiomas, agregação upstream).
- `05_quality_flags.R` — Atribuição de `quality_flag` e verificação de k-anonymity.
- `06_export_dataset.R` — Exportação dos CSVs finais e checksums.

## Contato

Dúvidas sobre proveniência ou transformações:

**E-mail:** [info@yekit.org](mailto:info@yekit.org)  
**Organização:** Yékit  
**Localização:** Medina, Minas Gerais, Brasil

## Referências

- IBGE. **Censo Demográfico**. Disponível em: [https://www.ibge.gov.br/](https://www.ibge.gov.br/)
- INMET. **Estações meteorológicas**. Disponível em: [https://portal.inmet.gov.br/](https://portal.inmet.gov.br/)
- ANA. **Sistema Nacional de Informações sobre Recursos Hídricos**. Disponível em: [https://www.snirh.gov.br/](https://www.snirh.gov.br/)
- DATASUS. **Sistemas de Informação em Saúde**. Disponível em: [https://datasus.saude.gov.br/](https://datasus.saude.gov.br/)
- MapBiomas. **Coleção 8 de Uso e Cobertura da Terra**. Disponível em: [https://brasil.mapbiomas.org/](https://brasil.mapbiomas.org/)

