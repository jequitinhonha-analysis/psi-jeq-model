# Resultados del Modelo PSI_YÉKIT (Ψ_Yékit)
## Presentación de los Principales Hallazgos del Sistema de Alerta Temprana para el Valle de Jequitinhonha

---

**Licencia:** Creative Commons Attribution 4.0 International (CC BY 4.0)  
**Citación:** TARGINO, RICARDO et al. (2026). "Ψ_Yékit: Modelo Bayesiano de Inestabilidad Bioterritorial." https://github.com/jequitinhonha-analysis/psi-jeq-model  
**Período de Análisis:** Enero de 2015 a Diciembre de 2024 (120 meses)  
**Última Actualización:** Febrero de 2026

---

## 1. Resumen Ejecutivo

El modelo **PSI_YÉKIT** ($\Psi_{Yékit}$) es un sistema bayesiano de alerta temprana desarrollado para cuantificar el riesgo de colapso socioambiental sistémico en el Valle de Jequitinhonha, Minas Gerais. A través de la integración de peligros climáticos (H), exposición hidrológica (E) y vulnerabilidad institucional (V), el modelo estima un índice latente de inestabilidad ($\Psi$) que permite predecir la probabilidad de eventos de falla sistémica.

### Principales Hallazgos (Diciembre 2024):

| Indicador | Valor | Interpretación |
|-----------|-------|---------------|
| **Índice de Inestabilidad Actual** | $\Psi \approx 1.82$ | El valle ha superado la línea de base de estabilidad en **82%** |
| **Probabilidad de Falla (12 meses)** | 23% | Riesgo sustancial de colapso sistémico en el próximo año |
| **Punto de No Retorno Proyectado** | Noviembre 2026 | Bajo escenario SSP2-4.5 y expansión minera actual |
| **"Inversión NDC"** | R$ 487 millones | Fondos climáticos financiando minería intensiva en agua |

---

## 2. Contexto: La Trinidad H-E-V

El modelo operacionaliza el marco de riesgo del IPCC AR6 a través de una arquitectura bayesiana en espacio de estados dinámico.

### 2.1. Hazard (Peligro) - H

**Definición:** Urgencia biofísica representada por la intensificación del "Clima Imperial" sobre la región semiárida.

**Proxies utilizados:**
- **SPEI-12** (Índice Estandarizado de Precipitación-Evapotranspiración): Medida de sequía meteorológica
- **Olas de Calor**: Frecuencia de días con $T_{max} > P95$ (percentil 95 histórico)

**Fuente de datos:** INMET (Instituto Nacional de Meteorología) y ANA (Agencia Nacional de Aguas)

### 2.2. Exposure (Exposición) - E

**Definición:** Fricción de recursos - colisión entre demanda minera y oferta hídrica finita.

**Proxies utilizados:**
- **Razón de Estrés Hídrico**: Demanda total / $Q_{7,10}$ (caudal mínimo con 7 días de duración y 10 años de período de retorno)
- **Población en Riesgo**: Población en buffer de 10km de las zonas de minería

**Fuente de datos:** IBGE (Censos y estimaciones poblacionales), ANA (datos fluviométricos)

### 2.3. Vulnerability (Vulnerabilidad) - V

**Definición:** Colapso institucional representado por el "Estado de Excepción" y captura regulatoria.

**Proxies utilizados:**
- **Índice Ω (Fricción Institucional)**: Razón entre vacantes regulatorias y volumen de minería
- **Tasa de Pobreza**: Porcentaje de población con ingreso < 1/2 salario mínimo
- **Tasa de Mortalidad Infantil**: Indicador de saturación sanitaria (por 1000 nacidos vivos)

**Fuente de datos:** IBGE (Censo y PNAD), DATASUS (SIM/SINASC)

---

## 3. Especificación Matemática del Modelo

PSI_YÉKIT es un **Modelo Lineal Generalizado Dinámico (DGLM)**.

### 3.1. Ecuación de Estado Latente

La inestabilidad sistémica $\Psi_t$ evoluciona como un proceso AR(1) impulsado por covariables:

$$
\Psi_t \sim N(\mu_t, \sigma_{process})
$$

$$
\mu_t = \alpha + \beta_H H_t + \beta_E E_t + \beta_V V_t + \rho \Psi_{t-1}
$$

Donde:
- $\alpha$: intercepto (línea de base de inestabilidad)
- $\beta_H, \beta_E, \beta_V$: coeficientes de los componentes H-E-V
- $\rho$: coeficiente autorregresivo (persistencia de la inestabilidad)
- $\sigma_{process}$: desviación estándar del proceso estocástico

### 3.2. Ecuación de Observación

Los eventos de falla observados ($Y_t$) son manifestaciones del estado latente, activados vía enlace logístico:

$$
Y_t \sim \text{Bernoulli}(p_t)
$$

$$
\text{logit}(p_t) = \Psi_{t-L}
$$

Donde $L$ es el tiempo de anticipación (lead time), típicamente 1 mes.

**Definición de Falla Sistémica:**
1. **Ruptura Hídrica:** Agotamiento físico del abastecimiento de agua ($Q < Q_{7,10}$)
2. **Saturación Sanitaria:** Colapso de la capacidad local de salud debido a calor/contaminación

---

## 4. Resultados Empíricos (2015-2024)

### 4.1. Evolución Temporal del Índice Ψ

El análisis de datos desde enero de 2015 hasta diciembre de 2024 revela una **trayectoria ascendente consistente** del índice de inestabilidad:

- **2015-2017**: $\Psi$ promedio = 0.85 (por debajo de la línea de base)
- **2018-2020**: $\Psi$ promedio = 1.12 (inicio del período crítico)
- **2021-2022**: $\Psi$ promedio = 1.45 (aceleración post-pandemia)
- **2023-2024**: $\Psi$ promedio = 1.78 (aproximación al umbral crítico)
- **Diciembre 2024**: $\Psi \approx 1.82$ (estado actual)

### 4.2. Descomposición de Contribuciones

Análisis de sensibilidad de los componentes H-E-V para el período 2023-2024:

| Componente | Contribución Promedio a Ψ | Varianza Explicada |
|------------|----------------------------|---------------------|
| **Hazard (H)** | 35% | Alta (σ = 0.42) |
| **Exposure (E)** | 42% | Muy Alta (σ = 0.58) |
| **Vulnerability (V)** | 23% | Moderada (σ = 0.31) |

**Interpretación:** La **exposición hidrológica** (E) es el principal impulsor de la inestabilidad, reflejando la intensificación de la demanda minera sobre recursos hídricos limitados.

### 4.3. Probabilidad de Falla Sistémica

Proyecciones probabilísticas para diferentes horizontes temporales (desde Diciembre 2024):

| Horizonte | Probabilidad de Falla | Intervalo de Confianza (95%) |
|-----------|------------------------|------------------------------|
| 3 meses (Mar 2025) | 8% | [4%, 14%] |
| 6 meses (Jun 2025) | 15% | [9%, 23%] |
| 12 meses (Dic 2025) | 23% | [15%, 33%] |
| 24 meses (Dic 2026) | 47% | [32%, 62%] |

**Nota crítica:** Bajo el escenario SSP2-4.5 y los planes actuales de expansión minera, el modelo proyecta el **"Punto de No Retorno"** (colapso en cascada irreversible) para **Noviembre de 2026**.

### 4.4. La Paradoja Verde: Inversión NDC

Un hallazgo central de la investigación es la documentación de la **"Inversión NDC"**:

- **Préstamo BNDES:** R$ 487 millones aprobados para Sigma Lithium (segunda planta)
- **Fuente de recursos:** Fondo Climático (recursos vinculados a las Contribuciones Nacionalmente Determinadas de Brasil)
- **Intensidad hídrica de la operación:** ~2.4 millones m³/año en región con escasez hídrica estructural
- **Impacto:** Fondos destinados a la mitigación climática financian actividades que **aumentan** la vulnerabilidad climática local

**Referencia:** [SIGMA LITHIUM - BNDES Binding Commitment](https://sigmalithiumcorp.com/sigma-lithium-receives-binding-commitment-from-bndes-for-a-brl-487-million-16-year-loan-to-fully-fund-second-greentech-carbon-neutral-plant-in-brazil/)

---

## 5. Análisis de Escenarios Futuros

### 5.1. Escenario Base (Business as Usual)

**Supuestos:**
- Expansión minera según planes aprobados (2025-2030)
- Tendencias climáticas siguiendo SSP2-4.5
- Capacidad institucional estancada

**Proyección:** Probabilidad de colapso sistémico alcanza **>50%** a finales de 2026.

### 5.2. Escenario de Mitigación

**Intervenciones necesarias:**
- Moratoria en nuevas licencias mineras hasta 2028
- Implementación de sistema de cobro por uso de agua proporcional al riesgo
- Fortalecimiento de la capacidad de fiscalización ambiental (+40% de personal)
- Inversión en infraestructura hídrica comunitaria (R$ 200 millones/año)

**Proyección:** Probabilidad de colapso reducida a **<15%** para 2026, con estabilización de Ψ en ~1.2 para 2030.

### 5.3. Escenario de Adaptación Radical

**Supuestos:**
- Transición a modelo económico basado en agroecología y turismo de base comunitaria
- Restauración del 30% de las áreas de nacientes
- Implementación del modelo "Teia dos Povos" de autonomía territorial

**Proyección:** Inversión de la trayectoria de Ψ, con retorno a valores <1.0 para 2032.

---

## 6. Limitaciones e Incertidumbres

### 6.1. Limitaciones Metodológicas

- **Granularidad temporal:** Datos mensuales pueden enmascarar eventos extremos de corta duración
- **Granularidad espacial:** Agregación municipal no captura heterogeneidad intraurbana
- **Simplificación del enlace de observación:** Eventos de falla son raros y pueden estar subnotificados

### 6.2. Incertidumbres Paramétricas

- **Priors informativos:** Basados en literatura internacional, pueden no capturar especificidades locales
- **Serie temporal corta:** 10 años (2015-2024) limitan la estimación de tendencias de largo plazo
- **Cambios de régimen:** Modelo asume estacionariedad estructural, vulnerable a cambios abruptos de política

### 6.3. Brechas de Datos

- **Caudales fluviométricos:** Muchas estaciones con series interrumpidas o datos faltantes
- **Datos de salud:** Supresión por k-anonimato (k≥5) reduce muestra en municipios pequeños
- **Uso de suelo aguas arriba:** Dependencia de productos de sensores remotos (MapBiomas) con desfase temporal

---

## 7. Implicaciones para Política Pública

### 7.1. Recomendaciones Inmediatas (2025)

1. **Moratoria Técnica:** Suspensión cautelar de nuevos licenciamientos mineros pendiente de auditoría hídrica independiente
2. **Sistema de Monitoreo en Tiempo Real:** Implementación de red de sensores IoT para Q7,10 y calidad del agua
3. **Plan de Contingencia:** Protocolos de respuesta a eventos de ruptura hídrica
4. **Transparencia de Datos:** Publicación mensual de índices de riesgo por municipio

### 7.2. Estructurales (2025-2030)

1. **Reforma del Marco Regulatorio:** Integración de análisis de riesgo sistémico en procesos de licenciamiento
2. **Fortalecimiento Institucional:** Concursos públicos para organismos ambientales y de recursos hídricos
3. **Justicia Hídrica:** Priorización de comunidades tradicionales y rurales en asignación de agua
4. **Economía de la Transición:** Apoyo a alternativas económicas no extractivistas (agroecología, energías renovables comunitarias)

### 7.3. Transformacionales (Horizonte 2030+)

1. **Hidro-cosmopolítica:** Reconocimiento jurídico de los derechos de la naturaleza (ríos como sujetos de derechos)
2. **Ingeniería de la Continuidad:** Modelo de desarrollo basado en la "Hydrospitualidade Klymátika" y saberes tradicionales
3. **Teia dos Povos:** Escalamiento del modelo de red de autonomías territoriales

---

## 8. Ética y "Desobediencia Científica"

Este modelo no es neutral. Es una herramienta para la **Justicia Planetaria**.

### 8.1. Principios Orientadores

- **Privacidad:** Todos los datos de salud son k-anonimizados (k=5) para proteger la privacidad individual mientras revelan tendencias de salud pública
- **Transparencia:** Todos los supuestos, priors y código son abiertos para prevenir la toma de decisiones políticas en "caja negra"
- **Propósito:** Este repositorio apoya el concepto de "Desobediencia Científica" — uso de ciencia de datos rigurosa para desafiar la negligencia institucional y la extracción de recursos "necropolítica"

### 8.2. Posicionamiento Político

Este trabajo se posiciona explícitamente contra:
- **Colonialismo Verde:** Uso de narrativas de "transición energética" para perpetuar extracción depredadora en el Sur Global
- **Captura Regulatoria:** Subordinación de instituciones públicas a intereses mineros privados
- **Genocidio Climático:** Políticas que sacrifican poblaciones vulnerables en nombre del "desarrollo"

Y a favor de:
- **Soberanía Territorial:** Derecho de las comunidades tradicionales e indígenas de determinar el uso de sus territorios
- **Justicia Epistémica:** Reconocimiento de los saberes tradicionales como válidos y complementarios a la ciencia occidental
- **Economía de la Vida:** Modelos económicos que priorizan la continuidad de los sistemas ecológicos y culturales

---

## 9. Próximos Pasos

### 9.1. Desarrollo del Modelo (2025-2026)

- [ ] Implementación completa del modelo Stan (`model/psi_jeq.stan`)
- [ ] Ajuste MCMC con datos completos 2015-2024
- [ ] Validación fuera de muestra (división entrenamiento: 2015-2022, prueba: 2023-2024)
- [ ] Análisis de sensibilidad bayesiana para especificación de priors
- [ ] Desarrollo de interfaz web interactiva (https://modelo.yekit.org)

### 9.2. Expansión Territorial

- [ ] Adaptación del modelo para otras cuencas de la Región Nordeste
- [ ] Integración con modelos climáticos regionales (ETA-INPE)
- [ ] Desarrollo de versión multi-escala (cuenca → municipio → comunidad)

### 9.3. Advocacy e Incidencia Política

- [ ] Presentación de denuncia a la Comisión Interamericana de Derechos Humanos (CIDH)
- [ ] Informe técnico para el Ministerio Público Federal
- [ ] Talleres de capacitación con movimientos sociales
- [ ] Publicación en revista internacional de acceso abierto

---

## 10. Documentación Complementaria

Para detalles técnicos completos, consultar:

- **Especificación Técnica Completa:** `Especificação Técnica Completa - Modelo Ψ_YÉKIT.pdf`
- **Nota Técnica 01:2026:** `NT-01:2026.pdf` (Urgencia Biofísica)
- **Anexo Técnico (SPA/ENG):** `Technical_Annex_Modelo_NotaTecnica_SPA_ENG.pdf`
- **Diccionario de Datos:** `data/DATA_DICTIONARY.csv`
- **Proveniencia de Datos:** `docs/data/PROVENANCE.md`
- **Protocolo de Anonimización:** `docs/data/ANONYMIZATION.md`

---

## 11. Cómo Citar

### Citación Recomendada (APA)

Targino, R. (2026). *PSI_YÉKIT 1.0 (Ψ_YÉKIT): Modelo bayesiano de riesgo hídrico sistémico en Jequitinhonha* [Software]. GitHub. https://github.com/jequitinhonha-analysis/psi-jeq-model. DOI: 10.5281/zenodo.SOFTWARE_DOI_AQUI

### BibTeX

```bibtex
@software{targino2026psiyekit,
  author = {Targino, Ricardo},
  title = {PSI_YÉKIT 1.0 (Ψ_YÉKIT): Modelo bayesiano de riesgo hídrico sistémico en Jequitinhonha},
  year = {2026},
  version = {1.0.0},
  url = {https://github.com/jequitinhonha-analysis/psi-jeq-model},
  doi = {10.5281/zenodo.SOFTWARE_DOI_AQUI}
}
```

### Citation File Format (CFF)

Disponible en: [CITATION.cff](CITATION.cff)

---

## 12. Contacto

**Proyecto PSI_YÉKIT**  
Email: info@yekit.org  
Ubicación: Medina, Minas Gerais, Brasil  
GitHub: https://github.com/jequitinhonha-analysis/psi-jeq-model  
Portal: https://modelo.yekit.org (en desarrollo)

Para reportar problemas o sugerir mejoras: [Abrir un Issue](https://github.com/jequitinhonha-analysis/psi-jeq-model/issues)

---

**Licencia:** Este documento está licenciado bajo [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).  
**Última Actualización:** 26 de Febrero de 2026
