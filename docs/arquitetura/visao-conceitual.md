# Visão Conceitual

## Objetivo

Apresentar o Sentinela Verde em nível conceitual, evidenciando como observações do território são transformadas em dados governados, resultados de modelos e produtos analíticos.

## Visão conceitual

```text
TERRITÓRIO E FONTES EXTERNAS
        │
        ▼
AQUISIÇÃO E INGESTÃO
        │
        ▼
BRONZE
Dados alinhados à fonte e com proveniência preservada
        │
        ▼
SILVER
Dados canônicos, features e saídas técnicas de modelos
        │
        ▼
GOLD
Regras de negócio, métricas e produtos analíticos
        │
        ▼
CONSUMO
Athena, análises, dashboard e documentação
```
A arquitetura conceitual separa claramente o dado de origem, o dado preparado para reutilização técnica e o produto analítico. Essa divisão reduz acoplamento entre etapas e permite que cada workload tenha um único writer autoritativo, contrato próprio e lineage até suas dependências.

## Princípios

- Bronze preserva identidade, semântica e proveniência da fonte.
- Silver concentra transformação, normalização, features e resultados técnicos reutilizáveis de modelos.
- Gold contém exclusivamente visão analítica, regras de negócio, KPIs e datasets orientados ao consumo.
- Artefatos binários de modelos e arquivos operacionais permanecem fora do Lakehouse.
- Resultados oficiais devem ser rastreáveis até código, dados, contratos e modelo utilizados.
