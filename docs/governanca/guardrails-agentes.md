# Governance Manifest for AI Agents — Compact Guardrails

!!! warning "Registro histórico"
    Esta página preserva o conteúdo do checkpoint em seu estado original. Decisões de arquitetura vigentes estão na [documentação atual](../arquitetura/index.md).

!!! info "🛡️ Nota"
    **Purpose:** compact guardrail for AI coding/provisioning agents working in Sentinela-Verde. This page is intentionally small. It defines boundaries, precedence, and retrieval behavior; it does **not** duplicate full governance or table contracts.

## 1. Authority and precedence

Use this order when sources disagree:
1. **Approved table contract in ****`governanca-dados`** — authoritative for table/schema behavior.
2. **Approved governance documentation / this manifest** — project-wide boundaries.
3. **Approved target architecture decisions** — infrastructure/network/orchestration constraints.
4. **Current repository implementation** — current behavior; deviations from higher rules must be flagged, not normalized.
5. **Legacy code/docs/resources** — historical evidence only unless explicitly retained.
For governed tables, **do not copy schema, quality rules, partitioning, retention, freshness, ownership or write mode from this manifest**. Resolve them from the current contract.
If this manifest, the target contract and repository-local context are sufficient, **do not load the full governance documentation**.

## 2. LOCKED boundaries

These are architectural/governance guardrails. An agent MUST NOT violate them unless an explicit approved governance/architecture change says otherwise.
- One governed physical Lakehouse; logical Glue databases are exactly `bronze`, `silver`, `gold`.
- Governed tables are Apache Iceberg tables. Everything below an Iceberg table root is Iceberg-managed.
- Do not create per-layer Lakehouse buckets or extra layer databases.
- Do not manually create Hive-style partitions or manipulate Iceberg internal files.
- Do not place query results, Terraform state, models, checkpoints, tests, logs or operational artifacts inside governed table roots.
- Each governed table MUST have exactly one **authoritative writer workload**. Technical execution identities may vary, but they MUST NOT create additional write ownership.
- Workload write IAM MUST be limited to owned resources and MUST respect project permission boundaries. Default is no access.
- Do not introduce long-lived AWS credentials into repositories or workflows.
- Do not introduce NAT Gateway as an implicit connectivity workaround.
- Do not introduce MLflow, PostgreSQL or legacy control EC2 into the target architecture without an explicit architecture decision.
- Do not silently discard invalid DQ records.
- Silver/Gold MUST use proper null semantics; placeholders such as `N/A`, `unknown`, `-` or empty strings MUST NOT represent missing values.
- Artifacts and operational outputs remain outside governed Lakehouse table roots.
- Missing governance decisions MUST NOT be converted into permanent conventions by an agent.

## 3. Governed-data behavior

Before writing governed data, an agent MUST resolve the current table contract and confirm:
1. target repository/workload;
2. layer and table;
3. authoritative writer workload;
4. grain and business key;
5. schema/types/nullability;
6. write mode and idempotency expectations;
7. DQ rules and quarantine behavior;
8. IAM/storage/network boundaries;
9. execution metadata/lineage requirements.
Layer semantics:
- **Bronze — Source-Aligned:** preserve source identity, semantics and provenance. Do not add business-domain harmonization.
- **Silver — Canonical/Domain:** standardize, validate, deduplicate, normalize geospatial/domain semantics and enforce quality.
- **Gold — Analytical Product:** governed analytical datasets for a declared analytical/business use case; rendered charts/reports/images are not Gold tables.
DQ behavior:
- `critical` → block publication;
- `error` → quarantine isolated invalid records or block when isolation is unsafe;
- `warning` → publish and record;
- `info` → observe;
- never silently discard failed records.

## 4. Storage and artifact boundaries

Use the governed storage role, not convenience:
- **Git:** code, configuration, contracts, provenance manifests, and small/synthetic fixtures.
- **Lakehouse:** governed tabular datasets.
- **Artifacts storage:** models, checkpoints, quarantine, temporary execution outputs, Athena results, orchestration artifacts and Terraform state according to platform conventions.
- **Raster/object storage:** large raster assets may remain immutable objects when Iceberg is not the appropriate representation; governed metadata must reference provenance, checksum and object location.
- **CloudWatch:** operational logs.
- **ECR:** container images.
Do not invent new persistent storage topology when an approved platform pattern exists.

## 5. Provisioning and change safety

A reversible assumption MAY be used only for local/non-persistent implementation details when the task cannot proceed otherwise.
A reversible assumption MUST NOT create or change any of the following without an explicit approved decision:
- persistent infrastructure topology;
- IAM/security boundaries;
- governed schemas or contracts;
- public interfaces;
- storage topology;
- repository/table ownership;
- network egress architecture.
If one of these is unresolved, surface the missing decision instead of inventing it.
Infrastructure MUST remain Terraform-managed where the project already uses Terraform. CI/CD MUST use short-lived AWS authentication through the approved OIDC pattern; do not add static credentials for convenience.

## 6. CURRENT verified implementation facts

The following are **current implementation facts**, not timeless governance invariants. Agents MAY use them for implementation work, but MUST re-verify them before making architectural changes:
- primary AWS region currently used by the target architecture: `us-east-1`;
- current orchestration target: MWAA Serverless;
- current ephemeral ETL/model compute pattern: EC2 Spot;
- current CI/CD implementation: GitHub Actions with reusable workflows in `automacao-cicd` and AWS OIDC;
- `main` is the integration/deployment branch in currently verified workflows;
- Runtime currently exists and remains under architectural validation; do not redesign or remove it incidentally;
- current `etl-datacenters` migration intent is Bronze ownership only unless governance is explicitly changed.
Treat role names, subnet IDs, endpoint inventories, repository rename plans and other concrete implementation identifiers as repository/platform facts that must be read from current code or approved architecture before provisioning.

## 7. Agent retrieval protocol

Use the smallest context needed:
1. Read this manifest.
2. If governed data is involved, read only the target table contract(s).
3. Read repository-local files relevant to the requested change.
4. Read current architecture/platform files only when infrastructure/network/IAM behavior is involved.
5. Load full governance documentation only for ambiguity, conflict, or a governance-specific task.
Do not preload unrelated contracts, repositories, full governance pages, or historical documentation.

## 8. Explicit deny list

An agent MUST NOT automatically:
- add NAT Gateway;
- add extra Glue layer databases;
- create per-layer Lakehouse buckets;
- write manual files into Iceberg table internals;
- use governed table roots for artifacts/query results/tests/logs;
- give multiple workloads write ownership of one table;
- introduce MLflow/PostgreSQL/legacy control EC2;
- remove or redesign Runtime as a side effect of unrelated work;
- encode table versions as `_v2`, `_final`, `_new`;
- weaken IAM/security boundaries for convenience;
- create permanent architecture from an undocumented assumption;
- rewrite governance to match legacy implementation silently.

!!! success "✅ Nota"
    **Agent rule:** when a higher-level rule conflicts with implementation, flag the conflict and stop the conflicting change. Do not silently normalize legacy behavior into policy.
