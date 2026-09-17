# Governança de Dados — Revisão e Estrutura Final

!!! warning "Registro histórico"
    Esta página preserva o conteúdo do checkpoint em seu estado original. Decisões de arquitetura vigentes estão na [documentação atual](../arquitetura/index.md).

!!! info "📘 Nota"
    **Documento para revisão do responsável de Governança — Time Plataforma**
    Este documento confronta a governança originalmente documentada com as decisões finais do projeto Sentinela-Verde. A estrutura foi organizada de forma compatível, quando aplicável, com práticas do DAMA-DMBOK: Data Governance, Data Architecture, Metadata Management, Data Quality, Data Security, Data Integration & Interoperability e Data Storage & Operations.

## 1. Changes made — Alterações realizadas

### 1.1 Lakehouse e arquitetura de armazenamento

1. **Original:** separação conceitual e de infraestrutura entre Bronze, Silver e Gold, com repositórios `plataforma-lakehouse-bronze`, `plataforma-lakehouse-silver` e `plataforma-lakehouse-gold`.
**Novo:** um único bucket físico de Lakehouse e um único repositório `plataforma-lakehouse`. Bronze, Silver e Gold permanecem como camadas lógicas e namespaces no Glue Catalog.
2. **Original:** estrutura física não possuía contrato único de localização das tabelas.
**Novo:** toda tabela Apache Iceberg segue `s3://<lakehouse-bucket>/<layer>/<table-name>/`. Tudo abaixo do table root é gerenciado pelo Iceberg e não pode ser manipulado diretamente por aplicações.
3. **Original:** existência de prefixes experimentais como `raw/` e `test/` e buckets separados por camada.
**Novo:** `raw/` e `test/` são removidos. Os três buckets antigos podem ser excluídos após a criação e validação do bucket único, pois não possuem dados produtivos a migrar.
4. **Original:** RAW/Bronze descrito como não sobrescrevível e imutável.
**Novo:** Bronze passa a ser **source-faithful**, não absolutamente imutável. Correções são permitidas somente para republicação/correção da fonte, erro comprovado de ingestão, duplicidade ou necessidade de conformidade com o contrato source-aligned. Harmonização de negócio permanece responsabilidade da Silver.
5. **Original:** versionamento de datasets descrito de forma genérica.
**Novo:** no Lakehouse, o histórico é responsabilidade do Apache Iceberg: S3 Versioning desabilitado, retenção padrão de snapshots por 90 dias, mínimo de 3 snapshots, expiração e limpeza de orphan files via manutenção Iceberg. O bucket de artifacts mantém S3 Versioning habilitado.

### 1.2 Catálogo, namespaces e nomenclatura

1. **Original:** catálogo definia metadados de identificação, origem, características geoespaciais e governança, sem gramática técnica única para tabelas e colunas.
**Novo:** exatamente três Glue databases: `bronze`, `silver`, `gold`.
2. **Original:** nomenclatura de datasets não estabelecia separação determinística entre fonte, entidade e produto analítico.
**Novo:** Bronze usa `<source>_<entity>`; Silver usa `<entity>` ou `<entity>_<concept>`; Gold usa `<subject>_<analytical_product>`.
3. **Original:** não havia idioma técnico obrigatório para identifiers.
**Novo:** databases, tabelas, colunas e valores categóricos governados utilizam inglês, lowercase `snake_case`. Termos de implementação como `raw`, `final`, `v2`, `etl`, `table` e formatos de arquivo são proibidos em nomes de tabelas.
4. **Original:** não havia regra explícita para versionamento do contrato.
**Novo:** toda tabela possui `contract_version` inteiro. Versão não aparece no nome da tabela. Mudanças fundamentais de grain ou business key originam novo contrato/tabela.
5. **Original:** status do dataset era um atributo de catálogo sem ciclo formal.
**Novo:** lifecycle padronizado em `draft`, `active`, `deprecated`, `retired`, com `replacement_table`, `deprecation_date` e `retirement_date` quando aplicável.

### 1.3 Bronze, Silver e Gold

1. **Original:** Bronze definido genericamente como Raw.
**Novo:** Bronze é explicitamente orientado à fonte. Exemplo: `bronze.datacenter_map_datacenters`. Preserva identidade, semântica e proveniência da fonte.
2. **Original:** Silver definida como Curated, responsável por transformação, padronização, validação, qualidade, georreferenciamento, deduplicação e lineage.
**Novo:** essas responsabilidades são preservadas e formalizadas como modelo canônico de domínio. Exemplo: `silver.datacenters`.
3. **Original:** Gold definida como Business/Analytics e fonte para KPI/BI com rastreabilidade.
**Novo:** Gold é formalmente orientada a analytical products. Cada tabela deve declarar caso de uso, grain, business key, measures, dimensions, consumidores e freshness. Exemplo: `gold.datacenter_environmental_impact`.

### 1.4 Identidade, grain e schema

1. **Original:** não havia business key obrigatória por contrato.
**Novo:** Silver e Gold devem declarar business key lógica, simples ou composta. Bronze não exige chave canônica.
2. **Original:** identificadores não tinham padrão global.
**Novo:** IDs canônicos usam `<entity>_id`, são `string` e UUID-based; geração determinística é preferida para entidades estáveis. `source_record_id` permanece separado.
3. **Original:** evolução de schema não possuía política formal.
**Novo:** backward-compatible by default. Adição nullable e widening compatível são permitidos; remoções e mudanças incompatíveis exigem migração; mudança fundamental de grain/business key cria novo contrato.
4. **Original:** particionamento não possuía contrato formal.
**Novo:** `partition_strategy` e `partition_rationale` são declarados por tabela. `none` é válido e padrão para tabelas pequenas. Particionamento é propriedade Iceberg; paths Hive manuais são proibidos.
5. **Original:** write semantics não eram declaradas por tabela.
**Novo:** cada contrato declara `append`, `merge` ou `overwrite`. `merge` é preferido quando possível; `overwrite` quando merge não é viável; `append` para eventos/observações imutáveis. Escritas devem ser idempotentes quando possível.

### 1.5 Metadados, temporalidade e lineage

1. **Original:** Bronze exigia origem e data de ingestão, sem conjunto técnico padronizado.
**Novo:** Bronze utiliza, quando aplicável, `ingested_at`, `source_system`, `source_record_id`.
2. **Original:** Silver/Gold não tinham audit metadata padrão.
**Novo:** Silver e Gold utilizam `created_at`, `updated_at`, `execution_id`.
3. **Original:** lineage era princípio e responsabilidade, mas sem representação mínima obrigatória.
**Novo:** lineage de contrato é obrigatório por `upstream` e `downstream`; `execution_id` fornece correlação operacional. Não será introduzida uma plataforma dedicada de lineage no MVP.
4. **Original:** timestamps não tinham timezone canônico.
**Novo:** timezone canônico `America/Sao_Paulo`, atualmente UTC-03:00. Campos timestamp usam `<event>_at`; campos date usam `<event>_date`.

### 1.6 Qualidade de dados

1. **Original:** dimensões DAMA de qualidade e Data Quality Score já definidos, incluindo Accuracy, Completude, Consistência, Atualidade, Conformidade, Disponibilidade e Lineage.
**Novo:** essas dimensões são preservadas; adicionalmente, regras técnicas passam a ter severidade e comportamento operacional explícitos.
2. **Original:** DQ Score \< 80 bloqueia modelo de produção sem aprovação do Data Steward.
**Novo:** regra preservada. Para execução automatizada, severidades são `critical`, `error`, `warning`, `info`.
3. **Original:** comportamento de falha não diferenciava dataset inteiro de registros isoláveis.
**Novo:** `critical` falha o pipeline; `error` envia registros inválidos para quarantine quando isoláveis e falha a escrita quando não for seguro isolar; `warning` permite publicação com registro; `info` é observabilidade. Registros inválidos nunca são descartados silenciosamente.
4. **Original:** qualidade específica de imagens já contemplava cloud coverage, resolução, pixels válidos, qualidade geométrica e temporal.
**Novo:** preservada e passa a integrar `quality_rules` do contrato correspondente.

### 1.7 Segurança, classificação e LGPD

1. **Original:** classificação de sensibilidade e Least Privilege eram princípios, sem taxonomia técnica obrigatória por coluna.
**Novo:** classificação obrigatória por coluna: `public`, `internal`, `confidential`, `personal`, `sensitive_personal`. A tabela herda a classificação mais restritiva aplicável.
2. **Original:** políticas de acesso eram definidas no nível do dataset.
**Novo:** cada tabela possui exatamente um writer declarado. Readers são concedidos explicitamente. Default é no access. IAM é scoped ao table root no S3 e aos recursos correspondentes no Glue.
3. **Original:** LGPD priorizava dados agregados, anonimizados ou não pessoais.
**Novo:** princípio preservado e operacionalizado: personal/sensitive_personal exigem least privilege, finalidade documentada e, quando aplicável, masking, tokenization, pseudonymization ou aggregation antes de consumo amplo.

### 1.8 Geoespacial e unidades

1. **Original:** CRS, resolução, cobertura, geometria e unidade eram metadados de catálogo.
**Novo:** CRS canônico de Silver/Gold é `EPSG:4326` / WGS 84; `latitude` e `longitude` são `double` com ranges válidos. Bronze pode preservar CRS da fonte.
2. **Original:** representação de geometrias complexas não era fixada.
**Novo:** para o MVP, geometrias complexas usam `geometry_wkt` e `geometry_crs`; Silver/Gold normalizam para `EPSG:4326`.
3. **Original:** unidades eram metadados, sem naming rule obrigatória.
**Novo:** medidas carregam unidade no nome, por exemplo `area_m2`, `distance_km`, `power_mw`, `energy_mwh`, `emissions_kg_co2e`. Silver normaliza unidades.

### 1.9 Retenção, deleção e lifecycle

1. **Original:** retenção era um atributo de governança sem política por tabela formalizada.
**Novo:** cada contrato declara `indefinite`, `duration` ou `source_aligned`, com rationale. Retenção de business data é distinta de snapshot retention do Iceberg.
2. **Original:** não havia soft deletion padrão.
**Novo:** quando aplicável, `is_deleted` e `deleted_at`; soft deletion é opcional por contrato e não substitui política de retenção.
3. **Original:** retirada de datasets não tinha procedimento completo.
**Novo:** sequência formal: `deprecated` → bloqueio de novos consumidores → remoção de dependências → `retired` → revogação IAM → expiração do histórico → deleção física após período de rollback → preservação do contrato no Git.

### 1.10 Contracts, ownership e change management

1. **Original:** `governanca-dados` já era responsável por contratos, glossário, políticas, qualidade e lineage.
**Novo:** `governanca-dados` torna-se formalmente o authoritative source of truth dos data contracts versionados em YAML.
2. **Original:** Owner, Steward e demais papéis eram definidos conceitualmente/RACI.
**Novo:** cada contrato também declara ownership operacional: `repository` e `contact`. Contato padrão do projeto: **Time Plataforma**.
3. **Original:** não havia processo técnico obrigatório de aprovação de contrato.
**Novo:** alterações são feitas por Pull Request em `governanca-dados`, com validação CI/CD, pelo menos um reviewer diferente do autor, incremento de `contract_version` quando aplicável e migration plan para breaking changes.
4. **Original:** contrato não possuía schema machine-readable único.
**Novo:** contratos YAML são validados por JSON Schema versionado em `governanca-dados`. Falha de validação bloqueia deployment.

### 1.11 Artifacts, Athena e infraestrutura operacional

1. **Original:** artifacts e configurações estavam distribuídos entre buckets/prefixes de diferentes serviços.
**Novo:** bucket central `sentinela-verde-artifacts-<account-id>-<region>` com `terraform/`, `athena/`, `mwaa/`, `models/`, `checkpoints/`, `executions/`.
2. **Original:** Athena não possuía contrato final de result location.
**Novo:** um único workgroup `sentinela-verde`, com resultados obrigatoriamente em `artifacts/athena/`; lakehouse não recebe query results.
3. **Original:** Terraform state já utilizava artifacts + DynamoDB locking, sem nomenclatura final formalizada.
**Novo:** `terraform/<repository>/<environment>/terraform.tfstate`; artifacts versionado; application workloads não acessam state.
4. **Original:** MWAA Serverless utiliza bucket dedicado.
**Novo:** preferencialmente `artifacts/mwaa/<workflow-name>/`; se o serviço exigir bucket dedicado, ele é classificado como managed/control-plane storage exception, isolado de workloads não relacionados.
5. **Original:** encryption AES256 já era utilizada em buckets existentes.
**Novo:** SSE-S3 (AES-256) é o padrão do projeto para evitar complexidade/custo de customer-managed KMS sem requisito regulatório específico.
6. **Original:** auditoria S3 não tinha política final.
**Novo:** CloudTrail management events como baseline e S3 data events somente de forma seletiva quando o valor de auditoria justificar o custo. Não habilitar logging object-level indiscriminadamente.
7. **Original:** não havia política formal de quarantine.
**Novo:** `artifacts/executions/quarantine/<table>/<execution_id>/`, isolada dos consumidores, com retenção padrão de 30 dias e classificação herdada da origem.
8. **Original:** `test/` existia no Lakehouse experimental.
**Novo:** testes não entram no Lakehouse produtivo. Fixtures pequenas e sintéticas podem ficar no Git; artifacts temporários usam `artifacts/executions/test/<execution_id>/` com retenção padrão de 7 dias.
9. **Original:** ambientes não estavam formalmente delimitados.
**Novo:** não haverá persistent dev environment no MVP. Produção é o ambiente de apresentação; testes são locais/automatizados/efêmeros. Se dev persistente for criado futuramente, deverá ter storage/catalog isolados.

### 1.12 Repositórios e workloads

1. **Original:** `etl-datacenters` possuía intenção de escrever `bronze.datacenters`.
**Novo:** será renomeado para `etl-map-datacenters`, será Bronze-only e owner/writer de `bronze.datacenter_map_datacenters`.
2. **Original:** Silver canônica de datacenters não possuía ownership final separado.
**Novo:** `silver.datacenters` deverá possuir workload/repositório writer próprio quando implementada; `etl-map-datacenters` não será seu writer.
3. **Original:** Runtime API + DynamoDB mantêm execution state próprio.
**Novo:** decisão permanece condicional ao teste de `etl-map-datacenters`. Se operar adequadamente sem complexidade relevante, Runtime API é preservada. Não alterar antes do teste.
4. **Original:** termination da EC2 poderia ser confundida com sucesso caso Runtime fosse removido.
**Novo:** se Runtime for removido, passa a existir success manifest imutável em `artifacts/executions/<workload>/<execution_id>/manifest.json`, gravado somente após commit bem-sucedido dos outputs governados.
---

## 2. Final New Governance — Estrutura final de Governança

### 2.1 Princípios de governança

A governança do Sentinela-Verde segue os princípios já estabelecidos: **Data is an Asset**, **Data is Shared**, **Data is Governed**, **Data Quality**, **Metadata**, **Security by Design**, **Traceability**, **Purpose Limitation**, **Least Privilege** e **Accountability**.
A aplicação prática está organizada segundo áreas de conhecimento compatíveis com DAMA-DMBOK:
- **Data Governance:** ownership, stewardship, accountability, contratos, aprovação e lifecycle.
- **Data Architecture:** arquitetura Medallion, namespaces e separação entre Lakehouse e artifacts.
- **Data Storage & Operations:** S3, Iceberg, snapshots, compaction, retenção e recuperação.
- **Metadata Management:** Glue Catalog, contratos YAML, lineage, classificação e technical metadata.
- **Data Quality:** dimensões, regras, severidades, quarantine e freshness.
- **Data Security:** least privilege, classificação, LGPD, IAM e auditoria.
- **Data Integration & Interoperability:** source-aligned Bronze, canonical Silver, analytical Gold e padronização de tipos/unidades.
> **Princípio de reprodutibilidade:** nenhum resultado analítico deve ser considerado oficial se não for possível rastrear e reproduzir sua origem a partir das versões de código, dados, features/modelos quando aplicável, contrato e execução.

### 2.2 Arquitetura lógica do Lakehouse

Existem três namespaces no Glue Catalog:
```text
bronze
silver
gold
```

#### Bronze

**Objetivo:** preservar identidade, semântica e proveniência da fonte.
Naming:
```text
bronze.<source>_<entity>
```
Exemplos:
```text
bronze.datacenter_map_datacenters
bronze.sentinel2_scenes
bronze.ibge_municipalities
```
Technical metadata, quando aplicável:
```text
ingested_at
source_system
source_record_id
```
Bronze é source-faithful. Não recebe harmonização de negócio. Correções somente ocorrem quando justificadas por correção da fonte ou da ingestão.

#### Silver

**Objetivo:** estabelecer entidades e observações canônicas do domínio Sentinela-Verde.
Naming:
```text
silver.<entity>
silver.<entity>_<concept>
```
Exemplos:
```text
silver.datacenters
silver.municipalities
silver.satellite_observations
```
Technical metadata:
```text
created_at
updated_at
execution_id
```
Silver executa padronização, deduplicação, canonicalization, georreferenciamento, normalização de unidades, mapeamento de categorical domains e quality validation.

#### Gold

**Objetivo:** representar analytical products orientados a casos de uso e consumo.
Naming:
```text
gold.<subject>_<analytical_product>
```
Exemplos:
```text
gold.datacenter_environmental_impact
gold.datacenter_land_cover_change
gold.municipality_datacenter_indicators
```
Cada Gold declara analytical use case, grain, business key, measures, dimensions, consumidores e freshness.

### 2.3 Estrutura física

Bucket:
```text
sentinela-verde-lakehouse-<account-id>-<region>
```
Table root:
```text
s3://<lakehouse-bucket>/<layer>/<table-name>/
```
Exemplo:
```text
s3://sentinela-verde-lakehouse-149465616406-us-east-1/bronze/datacenter_map_datacenters/
```
A estrutura abaixo do table root é exclusivamente Iceberg-managed. Nenhum workload deve depender diretamente de `data/`, `metadata/`, manifests ou physical partition paths.

### 2.4 Naming e tipos

Identifiers técnicos utilizam inglês e lowercase `snake_case`.
Regras principais:
- IDs: `<entity>_id`, tipo `string`, UUID-based.
- Timestamp: `<event>_at`.
- Date: `<event>_date`.
- Boolean: `is_*`, `has_*` ou verbo semântico apropriado.
- Measurement: unidade explícita no nome.
- Categorical domains: valores controlados em inglês e lowercase `snake_case`.
- Timezone: `America/Sao_Paulo`.
- CRS canônico: `EPSG:4326` / WGS 84.
Tipos padrão:
```text
Identifiers         -> string
Names/labels        -> string
Boolean flags       -> boolean
Whole quantities    -> bigint
Measurements        -> double
Money/precision     -> decimal(p,s)
Dates                -> date
Timestamps           -> America/Sao_Paulo semantics
Coordinates          -> double
Structured lists     -> array<...>
Structured objects   -> struct<...>
```
Nullability:
- `NULL` = unknown/unavailable/absent.
- `0` = zero conhecido.
- `false` = condição negativa conhecida.
- `''`, `N/A`, `null`, `unknown` e `-` não substituem missing values em Silver/Gold.

### 2.5 Data Contract

Authoritative source of truth:
```text
governanca-dados/contracts/
```
Estrutura:
```text
contracts/
  bronze/
  silver/
  gold/
```
Cada tabela possui um contrato YAML validado por JSON Schema.
Campos mínimos:
```yaml
contract_version: 1
table: silver.datacenters
description: ""
status: active
classification: internal
owner:
  repository: ""
  contact: "Time Plataforma"
grain: ""
business_key: []
write_mode: merge
partition_strategy: none
partition_rationale: ""
freshness: {}
retention: {}
iceberg_maintenance: {}
upstream: []
downstream: []
columns: []
quality_rules: []
```
Além desses campos, o contrato deve registrar required columns, nullability, quality rules, owner/writer, readers, upstream/downstream, classification, lifecycle e retention.

### 2.6 Ownership, RACI e acesso

Os papéis DAMA já documentados permanecem válidos: Data Owner, Data Steward, Data Custodian, Data Engineer, Data Scientist, Data Consumer, Cloud/DevOps e Security/LGPD.
Regra operacional adicional:
> Cada tabela governada possui exatamente **um writer** declarado.
- Writer recebe acesso de escrita ao table root e recursos Glue necessários.
- Readers recebem somente leitura quando explicitamente requeridos.
- Default é no access.
- MWAA, Athena, APIs, models, humanos e ETLs não recebem escrita implícita.
- `owner.repository` identifica o workload responsável pela implementação.
- `owner.contact` usa **Time Plataforma** como contato padrão do projeto.

### 2.7 Business key e grain

Silver e Gold devem declarar business key lógica, simples ou composta.
Exemplos:
```text
silver.datacenters
business_key: datacenter_id
```
```text
gold.datacenter_environmental_impact
business_key: datacenter_id + reference_date
```
Bronze não exige business key canônica; source identity é representada por `source_system` e `source_record_id` quando disponíveis.
Mudança fundamental de grain ou business key significa mudança de identidade do dataset e exige novo contrato/tabela.

### 2.8 Write semantics e schema evolution

Cada tabela declara:
```text
append
merge
overwrite
```
- `merge`: preferido para entidades/estados mutáveis.
- `append`: observações/eventos imutáveis.
- `overwrite`: quando merge não for possível ou a tabela for reconstruída deterministicamente.
Schema evolution é backward-compatible by default. Não são permitidos nomes como `_v2`, `_new`, `_old`, `_final`.

### 2.9 Data Quality

Dimensões existentes permanecem:
- Accuracy;
- Completude;
- Consistência;
- Atualidade;
- Conformidade;
- Disponibilidade;
- Lineage/proveniência.
Data Quality Score e Image Quality Score permanecem aplicáveis conforme o dataset.
Severidades:
```text
critical
error
warning
info
```
Comportamento:
- `critical`: pipeline falha e output inválido não é publicado.
- `error`: registros isoláveis vão para quarantine; se não for seguro isolar, a escrita falha.
- `warning`: output válido pode ser publicado, com ocorrência registrada.
- `info`: observabilidade.
DQ Score \< 80 não alimenta modelo de produção sem aprovação do Data Steward.
Quarantine:
```text
s3://<artifacts-bucket>/executions/quarantine/<table>/<execution_id>/
```
Retenção padrão: 30 dias.

### 2.10 Freshness

Freshness é definida por tabela:
```yaml
freshness:
  expected_interval: "24h"
  maximum_delay: "2h"
  measured_from: "successful_publish"
```
Os valores são definidos conforme a cadência real de cada fonte/produto. Não existe SLA temporal único para todo o Lakehouse.

### 2.11 Data Classification e LGPD

Classificações obrigatórias por coluna:
```text
public
internal
confidential
personal
sensitive_personal
```
A tabela herda a classificação mais restritiva aplicável.
Regras:
- least privilege;
- purpose limitation;
- personal/sensitive_personal não devem ser expostos em Gold sem necessidade documentada;
- priorizar aggregation, pseudonymization ou masking quando aplicável;
- dados sensíveis não devem ser escritos em logs;
- controles de acesso devem refletir a classificação do contrato.

### 2.12 Geospatial governance

Canonical CRS:
```text
EPSG:4326
WGS 84
```
Coordinates:
```text
latitude  -> double [-90, 90]
longitude -> double [-180, 180]
```
Complex geometry no MVP:
```text
geometry_wkt -> string
geometry_crs -> string
```
Silver/Gold normalizam para `EPSG:4326`. Bronze pode preservar CRS da fonte.

### 2.13 Units e categorical domains

Medidas físicas devem declarar unidade no nome:
```text
area_m2
distance_km
temperature_c
power_mw
energy_mwh
emissions_kg_co2e
resolution_m
```
Silver normaliza unidades e categorical domains. Gold reutiliza os domínios canônicos estabelecidos pela Silver.
`unknown` só é permitido quando possuir significado de negócio explícito; não substitui `NULL`.

### 2.14 Lifecycle, retention e soft deletion

Lifecycle:
```text
draft
active
deprecated
retired
```
Retention por tabela:
```yaml
retention:
  policy: indefinite | duration | source_aligned
  duration: null
  rationale: ""
```
Soft deletion opcional:
```text
is_deleted -> boolean
deleted_at -> timestamp nullable
```
`is_deleted=false` exige `deleted_at=NULL`; `is_deleted=true` exige `deleted_at` preenchido.

### 2.15 Iceberg operations

Padrão:
```yaml
iceberg_maintenance:
  snapshot_retention_days: 90
  minimum_snapshots_to_keep: 3
  orphan_file_cleanup: true
  target_file_size_mb: 512
  compaction: conditional
```
- S3 Versioning desabilitado no Lakehouse.
- Nunca aplicar lifecycle deletion genérico dentro de table roots Iceberg.
- Compaction é condicional à ocorrência material de small files.
- Partition strategy é definida por tabela e pode ser `none`.

### 2.16 Artifacts e operational storage

Bucket:
```text
sentinela-verde-artifacts-<account-id>-<region>
```
Estrutura:
```text
terraform/
athena/
mwaa/
models/
checkpoints/
executions/
```
Regras:
- S3 Versioning habilitado.
- SSE-S3 (AES-256).
- Block Public Access.
- operational logs permanecem no CloudWatch Logs.
- container images permanecem no ECR.
- Runtime state permanece no DynamoDB enquanto Runtime estiver ativo.
Storage exigido exclusivamente por um AWS service é classificado como `managed_control_plane_storage`, isolado e inacessível a workloads não relacionados.

### 2.17 Athena

Um workgroup:
```text
sentinela-verde
```
Query results:
```text
s3://<artifacts-bucket>/athena/
```
A configuração do workgroup deve ser enforced. Athena é read-only para governed lakehouse tables por padrão.

### 2.18 Terraform

State:
```text
s3://<artifacts-bucket>/terraform/<repository>/<environment>/terraform.tfstate
```
- versionado;
- encrypted;
- Block Public Access;
- acessível apenas por deployment principals;
- workloads não acessam Terraform state;
- DynamoDB permanece responsável pelo state locking.

### 2.19 MWAA Serverless

Workflow definitions preferencialmente em:
```text
s3://<artifacts-bucket>/mwaa/<workflow-name>/
```
Se MWAA Serverless exigir bucket dedicado, aplicar managed/control-plane storage exception. A configuração atual pode ser deletada e recriada no padrão final.

### 2.20 Audit e observability

- CloudTrail management events como baseline de auditoria AWS.
- S3 data events somente seletivos, quando necessários e com custo proporcional.
- CloudWatch Logs para operational logs.
- Não duplicar logs operacionais no Lakehouse.

### 2.21 Recovery e continuidade

MVP single-region em `us-east-1`.
Recovery strategy:
- Iceberg snapshots para rollback lógico de tabelas;
- Terraform para reconstrução de infraestrutura;
- re-ingestion/reprocessing a partir das fontes quando possível;
- artifacts críticos protegidos por S3 Versioning.
Não existe active cross-region DR no escopo atual. Iceberg snapshots não são tratados como backup independente contra perda integral do bucket.

### 2.22 Test data

Não utilizar `test/` ou `sample/` dentro do Lakehouse produtivo.
- Fixtures pequenas e sintéticas: Git.
- Artifacts de teste temporários: `artifacts/executions/test/<execution_id>/`.
- Retenção padrão: 7 dias.
- Não existe persistent dev environment no MVP.

### 2.23 Change management

Data contracts são alterados somente via Pull Request em `governanca-dados`.
Requisitos:
1. CI contract validation.
2. Um reviewer diferente do autor.
3. Incremento de `contract_version` quando materialmente aplicável.
4. Migration plan para breaking changes.
5. Merge na default branch representa aprovação técnica do contrato.
CI valida YAML/JSON Schema, required fields, naming, types, business keys, classifications, write mode, writer uniqueness e compatibility.

### 2.24 Glue Catalog metadata

Table parameters/tags mínimos:
```text
project=sentinela-verde
layer=bronze|silver|gold
owner_repository=<repository>
contract_version=<integer>
lifecycle_status=draft|active|deprecated|retired
classification=<level>
managed_by=terraform
```
Glue não duplica o corpo completo do data contract; o authoritative contract permanece em `governanca-dados`.

### 2.25 Repositórios

Lakehouse foundation:
```text
plataforma-lakehouse
```
Owner de contracts:
```text
governanca-dados
```
Data Center Map ingestion:
```text
etl-map-datacenters
-> bronze.datacenter_map_datacenters
```
`etl-map-datacenters` é Bronze-only. `silver.datacenters` deverá ter writer próprio quando implementada.

### 2.26 Runtime API e execution completion

Runtime API + DynamoDB permanecem **provisoriamente mantidos** até o teste de `etl-map-datacenters`.
Critério: se a execução funcionar adequadamente e Runtime não introduzir complexidade problemática, a arquitetura atual é preservada.
Se Runtime for removido, torna-se obrigatório um immutable success manifest:
```text
s3://<artifacts-bucket>/executions/<workload>/<execution_id>/manifest.json
```
O manifest só pode ser criado após commit bem-sucedido de todos os governed outputs. EC2 terminated, isoladamente, não significa workload succeeded.

### 2.27 Procedimento de retirement

1. alterar status para `deprecated`;
2. registrar replacement e retirement date quando aplicável;
3. impedir novos consumers;
4. remover dependências downstream;
5. alterar para `retired`;
6. revogar IAM de readers/writer;
7. expirar histórico Iceberg conforme política;
8. aguardar rollback/retention period;
9. remover dados físicos e Glue metadata de forma controlada;
10. preservar data contract e histórico no Git.

### 2.28 Regra final de governança

> **Uma tabela governada possui identidade, finalidade, grain, business key quando aplicável, owner, exatamente um writer, readers explícitos, schema, qualidade, classificação, lineage, freshness, retention e lifecycle documentados antes de ser considerada um ativo oficial do Sentinela-Verde.**
O Notion é a documentação humana e arquitetural. O repositório `governanca-dados` é o authoritative source of truth dos contratos executáveis/versionados. Glue Catalog é o catálogo técnico operacional. Terraform implementa a infraestrutura e os controles de acesso. Os workload repositories implementam as regras dos contratos sem redefini-los.
