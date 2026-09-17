# Arquitetura de Dados

## Objetivo

Documentar como os dados percorrem o Lakehouse desde a ingestão das fontes até os conjuntos preparados para modelagem e consumo analítico.

## Fluxo de dados

```text
FONTES
  │
  ▼
INGESTÃO
  │
  ▼
BRONZE
Dados source-aligned/source-faithful
  │
  ▼
CURADORIA E ENRIQUECIMENTO
  │
  ▼
SILVER
Entidades canônicas, features e saídas técnicas de modelos
  │
  ▼
REGRAS DE NEGÓCIO E CONSOLIDAÇÃO ANALÍTICA
  │
  ▼
GOLD
Produtos analíticos e datasets de consumo
```

## Organização do Lakehouse

As tabelas tabulares governadas utilizam Apache Iceberg sobre arquivos Parquet e seguem o padrão:
```text
s3://<lakehouse-bucket>/<layer>/<table-name>/
```
Tudo abaixo do root da tabela é gerenciado pelo Iceberg. A aplicação não cria partições Hive manualmente nem manipula diretamente arquivos internos da tabela.
Rasters de grande volume, como GeoTIFF, podem permanecer como objetos imutáveis em prefixos governados quando Iceberg não for a representação adequada. Nesses casos, tabelas catalogadas registram URI, proveniência, checksum, cobertura e demais metadados necessários à rastreabilidade.

## Controles de engenharia

### Contratos

Cada tabela possui contrato versionado em `governanca-dados`, com grain, business key, schema, quality rules, writer, write mode, particionamento e lifecycle.

### Ownership

Cada tabela possui exatamente um workload writer autoritativo. Readers e writers são concedidos explicitamente por IAM.

### Qualidade

Validações de schema, integridade, nulabilidade, unicidade, qualidade geoespacial e regras específicas da fonte são executadas conforme o contrato.

### Lineage

Resultados oficiais devem permitir relacionar `execution_id`, repositório, commit Git, versão do contrato, snapshots de entrada e saída e, quando aplicável, versão do modelo utilizado.

## Execução

ETLs são executados como containers efêmeros em EC2 Spot, orquestrados pelo MWAA Serverless. O estado persistente não depende do disco local da instância.
