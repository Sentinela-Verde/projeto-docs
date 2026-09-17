# Arquitetura Inicial

!!! warning "Registro histórico"
    Esta página preserva o conteúdo do checkpoint em seu estado original. Decisões de arquitetura vigentes estão na [documentação atual](../arquitetura/index.md).

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
