# Sprint 4 — 01/09 a 07/09

!!! warning "Registro histórico"
    Esta página preserva o conteúdo do checkpoint conforme o estado do projeto até 07/09. Decisões de arquitetura vigentes estão na [documentação atual](../arquitetura/index.md).


## 1. Finalização do Desenvolvimento

Durante esta sprint avançamos na integração das principais frentes do projeto e fechamos uma primeira versão funcional dos componentes de dados, modelagem e infraestrutura.

Na frente de dados, ampliamos a amostra utilizada pelo modelo, executamos a ingestão e o processamento das imagens para os data centers selecionados e estruturamos as etapas de extração, transformação, modelagem e analytics. Também iniciamos a integração de dados externos, como informações do IBGE e dados socioeconômicos.

Na modelagem, concluímos a geração do dataset de treinamento, a classificação em lote das imagens e a preparação das saídas de cobertura do solo para consumo pelas etapas seguintes.

Na infraestrutura, evoluímos os módulos Terraform e os pipelines centralizados de CI/CD. Foram criados workflows para validação e aplicação da infraestrutura, proteção dos repositórios e publicação dos componentes. Ao final da sprint já tínhamos realizado o primeiro provisionamento real da infraestrutura na AWS e iniciado também a estrutura do serviço de runtime.

## 2. Análise dos resultados

Nesta etapa, a análise estava concentrada principalmente na qualidade do modelo de classificação e na consistência dos dados gerados.

Foram avaliados os resultados do Random Forest em dados separados para validação, realizadas comparações entre diferentes versões do modelo e executada uma validação entre os sensores utilizados no projeto.

Também foram gerados os primeiros indicadores derivados da classificação das imagens, principalmente a distribuição das classes de cobertura do solo por área analisada. Essas saídas passaram a servir de base para a etapa posterior de comparação temporal entre os data centers e seus respectivos grupos de controle.

A análise estatística consolidada do efeito entre tratamento e controle ainda não estava finalizada nesta sprint.

## 3. Testes e validação funcional

Foram realizados testes tanto na camada de dados e modelagem quanto na infraestrutura.

Na parte de Ciência de Dados, validamos o pipeline de ingestão, geração de features, treinamento e inferência, além de testes do modelo em dados não utilizados durante o treinamento e validações entre diferentes sensores.

Na infraestrutura, utilizamos os pipelines centralizados para executar validações de Python, Terraform e workflows do GitHub Actions. Durante o provisionamento real da AWS também foram corrigidos pontos relacionados a IAM, permissions boundary, volumes das instâncias e execução dos workflows reutilizáveis.

Essas validações foram importantes para sair de uma arquitetura apenas desenhada e começar a testar efetivamente os componentes no ambiente AWS.

## 4. Modelagem Preditiva ou Classificatória

O modelo de classificação de cobertura do solo foi consolidado utilizando Random Forest.

Cada pixel das imagens passou a ser representado pelas bandas espectrais e pelos índices derivados utilizados pelo projeto. A partir dessas informações, o modelo classifica os pixels nas categorias de cobertura do solo definidas para a análise.

Durante a sprint também ampliamos o conjunto de áreas utilizadas no desenvolvimento do modelo, adicionamos rotulagem manual para complementar os rótulos automáticos e realizamos ajustes de hiperparâmetros.

A inferência em lote passou a gerar mapas classificados para as imagens processadas, permitindo transformar o dado raster em indicadores quantitativos que poderiam ser comparados ao longo do tempo.

### Referência visual — pipeline do modelo

![Pipeline de modelagem](../assets/images/05_pipeline_modelo.png)

*Pipeline de modelagem utilizado para conectar dados espectrais, classificação e geração dos indicadores.*

## 5. Dashboard de resultados

O dashboard ainda não estava finalizado ao término desta sprint.

O principal trabalho realizado nesta etapa foi preparar as saídas que alimentariam a visualização: classificações das imagens, percentuais das classes de cobertura do solo e dados socioeconômicos associados aos locais analisados.

Com essas informações estruturadas, a implementação e consolidação do dashboard ficaram para a sprint seguinte.

## Referências visuais e relacionadas

!!! info
    A arquitetura abaixo representa a visão consolidada na documentação final e é incluída como referência de evolução. Ela não deve ser interpretada como um retrato exato de todos os componentes já disponíveis em 07/09.

![Arquitetura conceitual](../assets/images/Arquitetura_Conceitual.png)

*Visão conceitual consolidada do fluxo de dados do Sentinela Verde.*

- [Arquitetura atual](../arquitetura/index.md)
- [Sprint 4 publicada no GitHub Pages](https://sentinela-verde.github.io/projeto-docs/entregaveis/sprint-4/)
- [Fonte Markdown da Sprint 4](https://github.com/Sentinela-Verde/projeto-docs/blob/main/docs/entregaveis/sprint-4.md)
- [Repositório `modelo-imagens-satelite`](https://github.com/Sentinela-Verde/modelo-imagens-satelite) — modelagem geoespacial e experimentos de classificação.
- [Repositório `data-pipeline-model`](https://github.com/Sentinela-Verde/data-pipeline-model) — implementação integrada anterior das etapas de extração, transformação e modelagem.
- [Repositório `modulos-terraform`](https://github.com/Sentinela-Verde/modulos-terraform) — módulos de infraestrutura utilizados na evolução da plataforma.
- [Lucid — Arquitetura de Engenharia de Dados](https://lucid.app/lucidchart/d8f69c0c-1a9f-4b8c-b99d-56c7ca8f35a3/edit)