# Sprint 1 — 11/08 a 17/08

!!! warning "Registro histórico"
    Esta página preserva o conteúdo do checkpoint em seu estado original. Decisões de arquitetura vigentes estão na [documentação atual](../arquitetura/index.md).

??? note "Comentários professor sobre entrega (2026-08-18)"
    ## **1. Cuidado com a palavra “impacto”**
    Não afirmar que o data center **causou** determinada alteração ambiental.
    - O data center pode estar associado a mudanças na região.
    - As mudanças podem ter sido causadas por um processo maior de desenvolvimento urbano.
    - Preferir termos como **“mudanças”, “transformações” ou “variações ambientais e territoriais”**.
    **Exemplo:**
    Em vez de “medir o impacto ambiental do data center”, usar **“identificar e quantificar mudanças ambientais e territoriais no entorno do data center”.**
    ## **2. Criar uma área de comparação**
    Além de comparar **antes × depois**, utilizar uma **área semelhante que não recebeu um data center**.
    A ideia é responder:
    > A região com data center mudou mais do que uma região semelhante sem data center?
    Também manter a análise por diferentes distâncias:
    - 1 km
    - 5 km
    - 10 km
    ## **3. Considerar a amostra pequena**
    Com apenas **4 a 6 data centers**, o projeto deve ser apresentado como **exploratório**, sem buscar validação ou generalização estatística para todos os data centers.
    Isso precisa ser uma limitação explícita do projeto.
    ## **4. Cuidado com o modelo de Machine Learning**
    Mesmo gerando muitos patches, eles vêm de poucos data centers. Existe o risco de o modelo aprender características específicas desses locais.
    Por isso, é importante avaliar:
    > **O modelo consegue generalizar para um data center que não participou do treinamento ou precisa ser recalibrado?**
    Idealmente, separar treinamento e teste por **data center**, e não apenas por patch.
    ## **5. Controlar a sazonalidade**
    As diferenças entre imagens podem ser causadas pela **época do ano**, e não por uma mudança real.
    Por isso:
    - comparar períodos semelhantes do ano;
    - controlar diferenças climáticas quando possível;
    - preferir composições de várias imagens em vez de uma única imagem.

??? note "Requisitos"
    - [x] Definição do problema e escolha do caso de uso.
    - [x] Criação do Kanban no Trello ou Jira para organização das tarefas.
    - [x] Desenho inicial da arquitetura.
    - [x] Seleção e avaliação das bases de dados.
    - [x] Escolha das ferramentas e tecnologias a serem utilizadas.

**BRIEFING DO PROJETO INTEGRADOR**
**Disciplina: Hands-On Fundamentos de Dados e Analytics**
Módulo 1 – MBA em Engenharia de Dados – Universidade Presbiteriana Mackenzie
**Mentoria: **Prof. Gustavo Ferreira

## 1. IDENTIFICAÇÃO DO GRUPO

**Nome do Projeto: Sentinela Verde — Monitoramento Geoespacial de Data Centers**
**Setor de Atuação: **Setor Público / Meio Ambiente
**Integrantes:**
- Fabio Olivetto Mesquita 10747149
- Fernando Sousa Silva 10739880
- Gabriel Barbosa de Souza 10747173
- Guilherme Giovanetti Cuzner 10204618
- Natan Milanez Polly 10747137
- Taimara Liz de Souza 10369003

## 2. DESCRIÇÃO DO PROBLEMA DE NEGÓCIO

#### 2.1 Qual é o problema que vocês pretendem resolver?

Analisar a transformação ambiental e territorial de uma área antes, durante e após a criação de um data center, utilizando imagens de satélite e um pipeline de Engenharia de Dados. Esses empreendimentos ocupam grandes extensões de terreno e demandam infraestrutura elétrica e hídrica intensiva, gerando impactos ambientais no entorno dessas instalações, como a perda de cobertura vegetal e alterações em corpos d'água próximos.

#### 2.2 Qual é o impacto do problema para o negócio?

A expansão dos data centers, impulsionada pelo crescimento da computação em nuvem, da inteligência artificial e do volume de dados, demanda grandes quantidades de energia, água e infraestrutura territorial. Esse crescimento gera impactos ambientais e socioeconômicos nas regiões onde são instalados, criando riscos relacionados à disponibilidade de recursos, à sustentabilidade, à infraestrutura local e à aceitação social. É importante que as empresas se responsabilizem, compreendam e mensurem esses impactos para tomar decisões de localização, planejamento e expansão de data centers de forma mais sustentável e responsável.

## 3. OBJETIVOS DO PROJETO

#### 3.1 Objetivo Geral:

Desenvolver um produto de dados para analisar dados geoespaciais, integrado a técnicas de Machine Learning, com o objetivo de identificar e quantificar alterações ambientais e territoriais no entorno de data centers, a partir da análise comparativa de imagens de satélite anteriores e posteriores à sua instalação, considerando mudanças na cobertura vegetal, nos corpos d'água e na área construída.

#### 3.2 Objetivos Específicos:

1. Levantamento dos casos: Identificar e caracterizar estudos de caso de data centers, considerando localização geográfica, período aproximado de instalação e disponibilidade de dados públicos e imagens de satélite.
2. Aquisição dos dados: Obter, por meio do Google Earth Engine, imagens históricas dos satélites Sentinel-2 e Landsat correspondentes aos períodos anterior e posterior à instalação dos data centers selecionados.
3. Construção do pipeline: Desenvolver um pipeline de ingestão, tratamento, padronização e transformação dos dados geoespaciais, convertendo as informações provenientes das imagens de satélite em dados estruturados e indicadores quantitativos.
4. Treinar e validar um modelo de deep learning (rede neural convolucional siamesa) capaz de detectar automaticamente essas mudanças a partir de pares de imagens de satélite, comparando seu desempenho com os indicadores de referência.
5. Consolidar os resultados em um dashboard analítico que apresente, por data center estudado, os indicadores de impacto ambiental antes e depois da instalação.

## 4. DADOS UTILIZADOS

#### 4.1 Fonte de Dados:

- Posição de data center: [https://www.datacentermap.com/](https://www.datacentermap.com/)
- Download de dados brutos: WRI Aqueduct Platform
- Histórico de métricas de nuvem: Microsoft Sustainability Report
- Google Earth Engine (imagens Sentinel-2 e Landsat)
- Hansen Global Forest Change (University of Maryland)
- JRC Global Surface Water (Comissão Europeia)
- Global Human Settlement Layer – GHSL (Comissão Europeia)
- OpenStreetMap (infraestrutura e localização)

#### 4.2 Descrição da base:

**Número de registros: **Estimativa inicial de 4 a 6 data centers como estudos de caso, recortados em múltiplos patches de imagem (ex.: 256 × 256 pixels) dentro de anéis de 10 km de raio, gerando uma base estimada de algumas centenas a poucos milhares de patches para treinamento.
**Número de atributos: **3 a 4 bandas espectrais por patch (RGB e infravermelho próximo); cerca de 8 a 10 variáveis analíticas tabulares derivadas por área estudada.
**Período coberto: **variável por data center, conforme a data de instalação; Sentinel-2 disponível desde 2015 e Landsat desde a década de 1980, permitindo comparações de longo prazo quando necessário.

#### 4.3 Principais variáveis:

- Identificação e localização (latitude/longitude) do data center
- Data aproximada de instalação
- Raio do anel de análise (buffer: 1 km, 5 km e 10 km)
- % de cobertura vegetal, antes e depois
- % de área de corpos d'água, antes e depois
- % de área construída, antes e depois
- Variação (delta) de cada indicador entre os dois períodos
- Classe de mudança prevista pelo modelo (por pixel/patch)

## 5. ARQUITETURA E GOVERNANÇA

- [Arquitetura Inicial](sprint-1-arquitetura-inicial.md)

[https://mermaid.ai/d/e548e6a5-baf0-4c08-82cc-1b343861bce0](https://mermaid.ai/d/e548e6a5-baf0-4c08-82cc-1b343861bce0)

```mermaid
flowchart TB
    classDef MinDel fill:#27D3F5,stroke:#19A86A,color:#000000;
    classDef MayDel fill:#CCCCCC,stroke:#1995A8,color:#000000;
    classDef PlsDel fill:#F2F527,stroke:#A1A819,color:#000000;
    %% =========================================================
    %% FONTES DE DADOS
    %% =========================================================
    subgraph FONTES["Fontes de Dados Externas"]

        GEE["Google Earth Engine<br/>Sentinel-2 + Landsat"]:::MinDel

        DCM["Data Center Map<br/>Localização dos Data Centers"]:::MinDel

        OSM["OpenStreetMap<br/>Infraestrutura e Localização"]:::PlsDel

        WRI["WRI Aqueduct<br/>Risco Hídrico / Recursos Hídricos"]:::PlsDel

        HANSEN["Hansen Global Forest Change<br/>Cobertura Florestal / Vegetação"]:::MinDel

        JRC["JRC Global Surface Water<br/>Corpos d'Água"]:::MinDel

        GHSL["Global Human Settlement Layer<br/>Área Construída / Ocupação Urbana"]:::PlsDel

        MS["Microsoft Sustainability Reports<br/>Métricas de Sustentabilidade"]:::MayDel
    end

    %% =========================================================
    %% INGESTÃO
    %% =========================================================
    subgraph INGESTAO["1. Camada de Ingestão"]

        INGEST_GEE["Ingestão de Imagens<br/>de Satélite"]:::MinDel

        INGEST_VECTOR["Ingestão de Dados<br/>Vetoriais / Geoespaciais"]:::MinDel

        INGEST_PUBLIC["Ingestão de Indicadores<br/>e Dados Públicos"]:::MinDel

    end

    GEE --> INGEST_GEE
    DCM --> INGEST_VECTOR
    OSM --> INGEST_VECTOR

    WRI --> INGEST_PUBLIC
    HANSEN --> INGEST_PUBLIC
    JRC --> INGEST_PUBLIC
    GHSL --> INGEST_PUBLIC
    MS --> INGEST_PUBLIC

    %% =========================================================
    %% RAW
    %% =========================================================
    subgraph RAW["Camada Raw — Dados Brutos e Imutáveis"]

        RAW_RASTER["Dados Raster Brutos<br/>GeoTIFF / Imagens de Satélite"]:::MinDel

        RAW_VECTOR["Dados Vetoriais Brutos<br/>GeoJSON / Shapefile / OSM"]:::MinDel

        RAW_REFERENCE["Dados de Referência Brutos<br/>CSV / JSON / Tabelas"]:::MinDel

    end

    INGEST_GEE --> RAW_RASTER
    INGEST_VECTOR --> RAW_VECTOR
    INGEST_PUBLIC --> RAW_REFERENCE

    %% =========================================================
    %% PREPARAÇÃO / PROCESSAMENTO
    %% =========================================================
    subgraph PREP["2. Preparação e Processamento"]

        AOI["Definição da Área de Interesse<br/>Data Center + Área de Entorno"]:::MinDel

        TEMPORAL["Seleção Temporal<br/>Antes / Durante / Depois"]:::MinDel

        CLOUD["Filtragem de Nuvens<br/>Máscara de Qualidade"]:::MinDel

        ALIGN["Alinhamento Espacial<br/>Projeção / Resolução"]:::MinDel

        PATCH["Divisão em Blocos de Imagem<br/>Geração de Patches"]:::MinDel

        NORMALIZE["Normalização Espectral"]:::MinDel

        ZONAL["Cálculo de Estatísticas Zonais"]:::MinDel

        LABEL["Geração de Rótulos de Mudança<br/>Vegetação / Água / Área Construída"]:::MinDel

    end

    RAW_RASTER --> AOI
    RAW_VECTOR --> AOI

    AOI --> TEMPORAL
    TEMPORAL --> CLOUD
    CLOUD --> ALIGN
    ALIGN --> PATCH
    PATCH --> NORMALIZE

    RAW_REFERENCE --> ZONAL
    RAW_VECTOR --> ZONAL
    RAW_RASTER --> ZONAL

    ZONAL --> LABEL

    %% =========================================================
    %% SILVER
    %% =========================================================
    subgraph SILVER["Camada Silver — Dados Geoespaciais Tratados"]

        SILVER_PATCH["Patches de Imagens<br/>Alinhados e Normalizados"]:::MinDel

        SILVER_LABEL["Rótulos de Mudança"]:::MinDel

        SILVER_STATS["Estatísticas Zonais<br/>Vegetação / Água / Área Construída"]:::MinDel

    end

    NORMALIZE --> SILVER_PATCH
    LABEL --> SILVER_LABEL
    ZONAL --> SILVER_STATS

    %% =========================================================
    %% GOLD
    %% =========================================================
    subgraph GOLD["Camada Gold — Dados Analíticos"]

        INDICATORS["Indicadores Ambientais"]:::PlsDel

        DC_METRICS["Métricas dos Data Centers<br/>por Período"]:::MinDel

        CHANGE_METRICS["Métricas de Mudança<br/>Antes × Depois"]:::MinDel

    end

    SILVER_STATS --> INDICATORS
    SILVER_LABEL --> INDICATORS

    INDICATORS --> DC_METRICS
    DC_METRICS --> CHANGE_METRICS

    %% =========================================================
    %% MACHINE LEARNING
    %% =========================================================
    subgraph ML["3. Aprendizado de Máquina / Aprendizado Profundo"]

        DATASET["Conjunto de Dados de Treinamento<br/>Par de Imagens + Rótulo"]:::MinDel

        SPLIT["Divisão dos Dados<br/>Treinamento / Validação / Teste"]:::MinDel

        SIAMESE["Rede Neural Convolucional Siamesa<br/>Siamese CNN"]:::PlsDel

        TRAIN["Treinamento do Modelo"]:::PlsDel

        EVAL["Avaliação do Modelo"]:::PlsDel

        PREDICT["Predição de Mudanças"]:::PlsDel

    end

    SILVER_PATCH --> DATASET
    SILVER_LABEL --> DATASET

    DATASET --> SPLIT
    SPLIT --> TRAIN
    TRAIN --> SIAMESE

    SIAMESE --> EVAL

    SILVER_PATCH --> PREDICT
    SIAMESE --> PREDICT

    PREDICT --> CHANGE_METRICS

    %% =========================================================
    %% CONSUMO ANALÍTICO
    %% =========================================================
    subgraph CONSUMO["4. Consumo Analítico"]

        DATASET_GOLD["Conjunto de Dados Analítico"]:::MinDel

        DASHBOARD["Dashboard de Impacto Ambiental"]:::MinDel

        USERS["Pesquisadores / Engenheiros de Dados<br/>Analistas Ambientais / Tomadores de Decisão"]:::MayDel
    end

    CHANGE_METRICS --> DATASET_GOLD
    EVAL --> DATASET_GOLD

    DATASET_GOLD --> DASHBOARD
    DASHBOARD --> USERS

    %% =========================================================
    %% GOVERNANÇA
    %% =========================================================
    subgraph GOVERNANCA["Governança e Qualidade de Dados"]

        QUALITY["Qualidade dos Dados<br/>Completude / Precisão"]:::MinDel

        CATALOG["Metadados / Catálogo de Dados"]:::MinDel

        VERSION["Versionamento<br/>Dados e Modelos"]:::MinDel

        REPRO["Reprodutibilidade / Linhagem de Dados"]:::MinDel

    end

    INGESTAO -.-> QUALITY
    RAW -.-> CATALOG
    SILVER -.-> VERSION
    GOLD -.-> REPRO
    ML -.-> VERSION
```

#### 5.1 Representação do fluxo de dados (camadas):

1. Ingestão (Google Earth Engine, OpenStreetMap e fontes públicas de localização)
2. Preparação (recorte de imagens, geração de patches, cálculo de estatísticas zonais e rótulos de mudança)
3. Armazenamento (arquivos raster/GeoTIFF e tabelas Parquet/CSV)
4. Modelagem (treinamento da rede neural siamesa)
5. Visualização (dashboard com indicadores por data center)

#### 5.2 Simulação de camadas de dados:

- Camada bruta (raw): imagens de satélite originais e vetores do OpenStreetMap, sem tratamento.
- Camada tratada (silver): patches recortados, alinhados espacialmente e normalizados, com rótulos de mudança associados.
- Camada analítica (gold): indicadores agregados por data center e período, prontos para consumo do dashboard e da etapa de modelagem.

#### 5.3 Princípios de Governança aplicados:

- Catálogo de dados: Glue Data Catalog
- Políticas de acesso: IAM / Lake Formation
- LGPD (simulado): não se aplica diretamente, pois não há dados pessoais envolvidos, apenas dados geoespaciais públicos.
- Classificação de sensibilidade

## 6. ANÁLISE EXPLORATÓRIA

#### 6.1 Técnicas utilizadas:

- Estatística descritiva: médias, medianas e desvios dos indicadores de vegetação, água e área construída, antes e depois da instalação.
- Visualização gráfica: séries antes/depois por data center.
- Correlação entre variáveis (ex.: relação entre crescimento urbano e perda de vegetação).
- Análise espacial por zonas de influência (buffer), comparando o impacto em diferentes raios de distância do data center: 1 km, 5 km e 10 km.

#### 6.2 Ferramentas utilizadas:

- Python como linguagem principal, com bibliotecas de dados geoespaciais (GeoPandas, Rasterio, Shapely)
- Google Earth Engine (earthengine-api, geemap)
- Extração de dados do OpenStreetMap (osmnx)
- Análise e visualização (Pandas, NumPy, Matplotlib, Seaborn, scikit-image)
- Modelagem (TensorFlow/Keras para a rede siamesa, scikit-learn para o Random Forest)

## 7. MODELAGEM PREDITIVA

#### 7.1 Tipo de problema modelado:

- Classificação
- Regressão

#### 7.2 Algoritmo(s) utilizados:

- Rede Neural Convolucional Siamesa (Change Detection)

#### 7.3 Métricas utilizadas para validação:

- Accuracy
- Precision
- Recall
- F1 Score
- MAE
- RMSE

## 8. DASHBOARD FINAL

#### 8.1 Ferramenta utilizada:

- Looker Studio ou Preset.io

#### 8.2 Público-alvo do dashboard:

- Diretoria
- Stakeholders técnicos

#### 8.3 Principais KPIs apresentados:

- % de perda/ganho de cobertura vegetal no entorno do data center
- Variação percentual da área de corpos d'água
- Taxa de crescimento da área construída (urbanização)
- Número de data centers monitorados no estudo
- Grau de concordância (acurácia) entre o modelo treinado e os datasets de referência

## 9. DOCUMENTAÇÃO E APRESENTAÇÃO FINAL

#### 9.1 Repositório GitHub do projeto (link):

- Pipeline de dados:
- Modelo:
- Infraestrutura:
- Kanban: [https://github.com/users/BigDataNatan/projects/3/views/3](https://github.com/users/BigDataNatan/projects/3/views/3)

#### 9.2 Componentes do repositório:

☒ README com visão geral
☒ Código-fonte (Python, SQL, etc.)
☒ Notebook técnico
☒ Slides de apresentação
☒ Artefatos complementares (ex.: GeoJSON dos buffers, CSV dos indicadores)

#### 9.3 Observações e aprendizados do grupo:
