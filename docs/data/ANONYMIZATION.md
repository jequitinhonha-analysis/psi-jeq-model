# Procedimento de anonimização / agregação (PSI_YÉKIT 1.0)

## Objetivo
Garantir que o conjunto de dados publicado seja reutilizável e auditável sem expor informações pessoais, sensíveis ou identificáveis.

## Estratégia aplicada
Descreva explicitamente o que foi feito:

### 1) Remoção de identificadores diretos
- [ex.: nomes, documentos, endereços, IDs individuais]

### 2) Generalização / agregação
- Granularidade espacial: [ex.: município / sub-bacia / grade]
- Granularidade temporal: [ex.: mensal / trimestral]
- Regras de agregação: [ex.: soma, média, mediana]

### 3) Supressão por baixo N (k-anonymity operacional)
- Regra: suprimir/agrup. quando contagem < k, com k = [preencher]
- Tratamento de outliers: [preencher]

### 4) Transformações para reduzir reidentificação por ligação
- [ex.: truncamento de datas, bins, ruído controlado se houver]

## Avaliação de risco residual
- Vetores considerados (linkage, singularidade, inferência):
  - [preencher]
- Decisão final: publicado como Open Access sob CC BY 4.0.

## Compatibilidade com reprodutibilidade
- O dataset publicado permite reproduzir:
  - [preencher: ajuste do modelo / validação / plots-chave]
- O que não é publicado (por risco/termos):
  - [preencher]

## Contato
info@yekit.org
