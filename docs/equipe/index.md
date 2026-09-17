# Equipe

A equipe está organizada de forma horizontal, com responsabilidades distribuídas por frentes de trabalho. Cada integrante possui uma atuação principal e pode colaborar em frentes relacionadas sempre que houver dependências técnicas, necessidade de integração ou benefício direto para a entrega.

## Estrutura de trabalho

```text
                        👥 6 PESSOAS
                             │
         ┌───────────────────┼───────────────────┐
         ▼                   ▼                   ▼
  🛰️ Dados /           🔧 Engenharia        ☁️ Cloud /
Geoprocessamento          de Dados             DevOps
         │                   │                   │
         └───────────────────┼───────────────────┘
                             ▼
                      📦 DATASET CURADO
                             │
                   ┌─────────┴─────────┐
                   ▼                   ▼
             🤖 MODELAGEM        📊 INDICADORES
                   │                   │
                   └─────────┬─────────┘
                             ▼
                      📈 RESULTADOS
                             │
                             ▼
                   📝 INTEGRAÇÃO /
                     DOCUMENTAÇÃO
```

## Responsabilidades

| Frente | Responsabilidade principal | Participação relacionada | — |
| --- | --- | --- | --- |
| 🛰️ Dados / Geoprocessamento | Fontes geoespaciais, área de estudo, preparação e validação espacial | Features / Ciência de Dados | — |
| 🔧 Engenharia de Dados | Ingestão, organização, processamento, qualidade e disponibilização | Cloud / DataOps | — |
| ☁️ Cloud / DevOps | Plataforma AWS, infraestrutura, automação, versionamento e execução | Engenharia de Dados | — |
| 🤖 Ciência de Dados / Modelagem | Features, dataset de modelagem, treinamento, avaliação e classificação | Dados / Geoprocessamento | — |
| 📊 Indicadores / Análise | Métricas, áreas, percentuais, comparações e análise temporal | Ciência de Dados | — |
| 📈 Resultados / Documentação | Integração dos resultados, mapas, gráficos, documentação e apresentação | Todas as frentes | — |

| WorkstreamsMembers | ☁️ Cloud / DevOps | Data Governance | 🔧 Engenharia de Dados | 🤖 Ciência de Dados / Modelagem | 📊 Indicadores / Análise |
| --- | --- | --- | --- | --- | --- |
| Fabio | 🟡 | 🟢 | 🔵 | 🟡 | 🟢 |
| Fernando | 🟢 | 🔵 | 🔵 | 🟡 | 🟡 |
| Gabriel | 🟡 | 🔵 | 🔵 | 🟢 | 🟡 |
| Guilherme | 🟡 | 🟡 | 🔵 | 🟢 | 🔵 |
| Natan | 🔵 | 🔵 | 🟢 | 🟡 | 🟡 |
| Taimara | 🟡 | 🟡 | 🔵 | 🟢 | 🔵 |

- 🟢 participação principal
- 🔵 apoio/participação secundária 
- 🟡 somente se necessário

## Colaboração direcionada

### 🛰️ Dados ↔ 🤖 Modelagem

Pair programming e alinhamento conjunto na definição de features, rótulos, alinhamento espacial e dataset para ML.

### 🔧 Engenharia de Dados ↔ ☁️ Cloud / DevOps

Pair programming durante a implementação da infraestrutura, armazenamento, execução, automação e CI/CD.

### 🤖 Modelagem ↔ 📊 Indicadores

Colaboração na definição das saídas do modelo e dos indicadores que serão derivados delas.

## Regra de paralelização

> Cada frente deve iniciar assim que possuir os insumos mínimos necessários, sem aguardar a conclusão completa das demais. Quando duas frentes possuem uma interface crítica, os responsáveis devem trabalhar em conjunto durante a definição e implementação dessa interface.

## Organização da execução

A estrutura de trabalho foi definida para garantir cobertura das frentes essenciais do projeto ao longo das cinco sprints, com colaboração explícita nas interfaces críticas entre dados, plataforma, modelagem e análise.
A distribuição de responsabilidades pode evoluir conforme as necessidades do projeto, preservando o ownership principal de cada frente e a colaboração entre especialidades.

- [Responsabilidades](responsabilidades.md)

- [Workstreams](workstreams.md)

- [Fluxo de trabalho](fluxo-trabalho.md)

- [Colaboração / Pair Programming](colaboracao.md)
