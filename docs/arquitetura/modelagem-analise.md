# Modelagem e Análise

## Objetivo

Documentar como os dados preparados são transformados em classificações, grupos de controle, métricas ambientais e resultados estatísticos utilizados pelo produto analítico.

## Fluxo de modelagem

```text
DADOS SILVER
  │
  ├── imagens e features espectrais
  ├── labels de cobertura do solo
  ├── temperatura de superfície
  └── dados socioeconômicos
  │
  ▼
MODELOS E PROCESSOS ANALÍTICOS
  │
  ├── classificação de imagens
  ├── seleção/atribuição de grupo de controle
  └── expansão da amostra
  │
  ▼
SAÍDAS TÉCNICAS EM SILVER
  │
  ▼
CONSOLIDAÇÃO E REGRAS DE NEGÓCIO
  │
  ▼
GOLD
  │
  ├── impacto ambiental
  ├── resultados estatísticos
  └── datasets para dashboard e análises
```

## Classificação de cobertura do solo

O modelo de classificação utiliza imagens de satélite, índices espectrais e labels governados para produzir classes de cobertura do solo. As predições, probabilidades, scores e demais resultados técnicos reutilizáveis permanecem na camada Silver. O artefato binário do modelo é armazenado no bucket de artifacts e é versionado independentemente do dataset.

## Grupo de controle

A seleção do grupo de controle utiliza atributos socioeconômicos e características territoriais para identificar candidatos comparáveis. Os candidatos, scores e atribuições são resultados técnicos e, portanto, permanecem em Silver. A interpretação tratamento × controle e as regras analíticas derivadas dessas relações são publicadas em Gold.

## Análise estatística

Os produtos Gold consolidam as variáveis ambientais e socioeconômicas necessárias à análise de impacto. Event study, diferença-em-diferenças, testes de placebo, permutação e correções por múltiplas comparações são aplicados sobre conjuntos governados e rastreáveis.

## Reprodutibilidade

Cada execução de modelo deve permitir relacionar o resultado ao código, commit Git, contrato, dados de entrada, versão do artefato de modelo, parâmetros relevantes e `execution_id`. A governança não depende de uma ferramenta específica de experiment tracking.
