# Sprint 4 — 01/09 a 07/09

!!! warning "Registro histórico"
    Esta página preserva o conteúdo do checkpoint conforme o estado do projeto até 07/09. Decisões de arquitetura vigentes estão na [documentação atual](../arquitetura/index.md).

## 1. Finalização do Desenvolvimento

Nesta sprint fechamos uma primeira versão funcional das frentes de dados, modelagem e infraestrutura. Ampliamos a amostra, processamos as imagens dos data centers selecionados, iniciamos a integração com dados socioeconômicos e preparamos as saídas de cobertura do solo.

Também avançamos nos módulos Terraform e no CI/CD, com os primeiros provisionamentos reais na AWS e o início da estrutura do serviço de runtime.

## 2. Análise dos resultados

A análise esteve concentrada na qualidade do Random Forest, na consistência dos dados e na comparação entre versões do modelo e sensores.

Também geramos os primeiros indicadores de cobertura do solo. A análise estatística consolidada entre tratamento e controle ainda não estava finalizada neste checkpoint.

## 3. Testes e validação funcional

Validamos o pipeline de ingestão, features, treinamento e inferência, além do comportamento do modelo em dados de validação e entre sensores.

Na infraestrutura, os pipelines centralizados passaram a validar Python, Terraform e GitHub Actions, e corrigimos os principais pontos encontrados nos primeiros testes reais na AWS.

## 4. Modelagem Preditiva ou Classificatória

Consolidamos o Random Forest para classificação da cobertura do solo a partir das bandas e índices espectrais. A amostra foi ampliada, a rotulagem manual passou a complementar os rótulos automáticos e realizamos ajustes de hiperparâmetros.

A inferência em lote passou a gerar mapas classificados e indicadores quantitativos para as análises temporais.

### Referência visual — pipeline do modelo

![Pipeline de modelagem](../assets/images/05_pipeline_modelo.png)

*Pipeline de modelagem utilizado para conectar dados espectrais, classificação e geração dos indicadores.*

## 5. Dashboard de resultados

Nesta sprint também consolidamos um **dashboard em Power BI** para apresentar o panorama dos data centers analisados no Brasil.

A visão reúne filtros por estado, operadora, ano de operação e status, além de indicadores como quantidade de data centers, distribuição geográfica, capacidade construída, área construída e perfil operacional.

No recorte atualmente publicado, o dashboard apresenta **242 data centers**, distribuídos em **18 estados** e **103 operadoras**, com **5.803,70 MW** de capacidade construída e **479.087 m²** de white space construído.

[Consultar dashboard no Power BI](https://app.powerbi.com/view?r=eyJrIjoiODIwOGFkN2YtYjg4Zi00YjM5LWI0OTYtZmU0MGYyYTZlOWE2IiwidCI6IjY1OWNlMmI4LTA3MTQtNDE5OC04YzM4LWRjOWI2MGFhYmI1NyJ9)

## Referências visuais e relacionadas

!!! info
    A arquitetura abaixo representa a visão consolidada na documentação final e é incluída como referência de evolução. Ela não deve ser interpretada como um retrato exato de todos os componentes já disponíveis em 07/09.

![Arquitetura conceitual](../assets/images/Arquitetura_Conceitual.png)

*Visão conceitual consolidada do fluxo de dados do Sentinela Verde.*

- [Arquitetura atual](../arquitetura/index.md)
- [Dashboard — Power BI](https://app.powerbi.com/view?r=eyJrIjoiODIwOGFkN2YtYjg4Zi00YjM5LWI0OTYtZmU0MGYyYTZlOWE2IiwidCI6IjY1OWNlMmI4LTA3MTQtNDE5OC04YzM4LWRjOWI2MGFhYmI1NyJ9)
- [Sprint 4 publicada no GitHub Pages](https://sentinela-verde.github.io/projeto-docs/entregaveis/sprint-4/)
- [Fonte Markdown da Sprint 4](https://github.com/Sentinela-Verde/projeto-docs/blob/main/docs/entregaveis/sprint-4.md)
- [Repositório `modelo-imagens-satelite`](https://github.com/Sentinela-Verde/modelo-imagens-satelite) — modelagem geoespacial e experimentos de classificação.
- [Repositório `data-pipeline-model`](https://github.com/Sentinela-Verde/data-pipeline-model) — implementação integrada anterior das etapas de extração, transformação e modelagem.
- [Repositório `modulos-terraform`](https://github.com/Sentinela-Verde/modulos-terraform) — módulos de infraestrutura utilizados na evolução da plataforma.
- [Lucid — Arquitetura de Engenharia de Dados](https://lucid.app/lucidchart/d8f69c0c-1a9f-4b8c-b99d-56c7ca8f35a3/edit)
