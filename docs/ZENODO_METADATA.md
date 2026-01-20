# Metadados e Textos para Zenodo

Este arquivo centraliza os textos prontos para cópia/cola nos depósitos do Zenodo.

---

## 1) Depósito de Software (PSI_YÉKIT 1.0)

### Campos Zenodo

**Title:**
PSI_YÉKIT 1.0 (Ψ_YÉKIT): Modelo bayesiano de risco sistêmico hídrico no Jequitinhonha

**Upload type:** Software

**Version:** 1.0.0

**License:** CC BY 4.0

**Description:**
```
PSI_YÉKIT 1.0 (Ψ_YÉKIT) é um modelo bayesiano em espaço de estados para estimar um estado latente de instabilidade hídrica (Ψ) e derivar probabilidades de falha sistêmica ao longo do tempo na bacia do Jequitinhonha, com foco em auditabilidade, rastreabilidade e validação preditiva.

Este depósito corresponde à versão 1.0.0 (release) do software e deve ser citado pelo DOI de versão para reprodutibilidade.

Recursos:
- Especificação técnica e notas metodológicas incluídas no repositório.
- Política de licenciamento por artefato (textos/relatórios CC BY 4.0; código/dados CC0 quando aplicável) documentada em LICENSES.md.
- Contato: info@yekit.org (Medina, MG)

Relacionados:
- Dataset (dados anonimizados, CC BY 4.0): 10.5281/zenodo.DATASET_DOI_AQUI (preencher após publicação).
- Portal: https://modelo.yekit.org
```

**Keywords (Zenodo):**
Jequitinhonha; Bayesian; state-space model; hydrology; water security; Minas Gerais; risk model; drought

**Related/Identifiers:**
- *IsSupplementedBy* → DOI do Dataset (quando existir)

---

## 2) Depósito de Dataset (dados anonimizados, Zenodo)

### Campos Zenodo

**Title:**
PSI_YÉKIT 1.0 — Dataset anonimizados para modelagem Ψ_YÉKIT (Jequitinhonha)

**Upload type:** Dataset

**Version:** 1.0.0 (ou `2026.01` se preferirem versionamento por data)

**License:** CC BY 4.0

**Description:**
```
Este depósito disponibiliza o conjunto de dados anonimizados e/ou agregados utilizado para alimentar e validar o PSI_YÉKIT 1.0 (Ψ_YÉKIT), um modelo bayesiano em espaço de estados para risco sistêmico hídrico no Jequitinhonha.

Conteúdo:
- Dados processados (anonimizados/agregados) adequados para reuso e replicação do pipeline.
- Dicionário de variáveis (DATA_DICTIONARY.csv) e documentação de proveniência (PROVENANCE.md).
- Descrição do procedimento de anonimização (ANONYMIZATION.md) e checksums para integridade.

Licença:
- Creative Commons Attribution 4.0 International (CC BY 4.0). Ao reutilizar, cite este DOI.

Relacionados:
- Software PSI_YÉKIT 1.0 (release): 10.5281/zenodo.SOFTWARE_DOI_AQUI (preencher após emissão do DOI do software).
- Contato: info@yekit.org (Medina, MG)
```

**Keywords (Zenodo):**
Jequitinhonha; hydrology; drought; Bayesian; water security; Minas Gerais; time series; risk

**Related/Identifiers:**
- *IsSupplementTo* → DOI do software (quando existir)

---

## 3) Checklist de Execução

### Fase 1: Software (agora)
- [ ] Merge do PR #3 (licenciamento por artefato)
- [ ] Criar release `v1.0.0` no GitHub
- [ ] Verificar que Zenodo gerou **DOI de versão** + **Concept DOI**
- [ ] Copiar DOI do Zenodo para `CITATION.cff` (campos `identifiers` e `preferred-citation`)
- [ ] Fazer commit e push da atualização `CITATION.cff`

### Fase 2: Dataset
- [ ] Finalizar anonimização dos dados
- [ ] Completar `ANONYMIZATION.md`, `PROVENANCE.md`, `DATA_DICTIONARY.csv`
- [ ] Gerar `CHECKSUMS.txt` (rodar: `sha256sum data/processed/* > CHECKSUMS.txt`)
- [ ] Criar zip com: `data/processed/`, `README_DATA.md`, `ANONYMIZATION.md`, `PROVENANCE.md`, `CHECKSUMS.txt`, `DATA_DICTIONARY.csv`
- [ ] Subir no Zenodo como **Dataset**, licença **CC BY 4.0**
- [ ] Copiar DOI para `PROVENANCE.md` (relacionados)

### Fase 3: Ligação entre Software e Dataset
- [ ] Após ter ambos os DOIs, atualizar:
  - `CITATION.cff` (se não feito na Fase 1)
  - Descrição do Dataset no Zenodo (campo "Related identifier" → DOI do software)
  - Descrição do Software no Zenodo (campo "Related identifier" → DOI do dataset)

---

## 4) Notas Adicionais

- Se houver mais autores, adicione à seção `authors` do `CITATION.cff` (formato: family-names, given-names, affiliation).
- Se vocês usarem `.zenodo.json`, ele pode "ganhar" do `CITATION.cff` em alguns campos; mantenha um como fonte de verdade.
- O DOI do Zenodo será emitido **automaticamente** quando a release `v1.0.0` do GitHub for feita (se GitHub↔Zenodo integrados).
- Para o dataset, o upload é **manual** no Zenodo (não integrado automaticamente).
