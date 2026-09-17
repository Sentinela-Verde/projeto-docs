# Plataforma AWS

## Objetivo

Documentar a plataforma AWS que sustenta ingestão, processamento, modelagem, armazenamento, consulta e observabilidade do Sentinela Verde.

## Componentes principais

```text
GitHub
  │
  ▼
GitHub Actions ──► Amazon ECR
                     │
                     ▼
              MWAA Serverless
                     │
                     ▼
              EC2 Spot efêmera
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
       S3           Glue       CloudWatch
        │             │
        └──────► Athena
```
A plataforma prioriza serviços gerenciados e recursos efêmeros. Os workloads de ETL e modelos são empacotados em imagens Docker imutáveis e executados em instâncias EC2 Spot somente durante o processamento. A orquestração é realizada pelo Amazon MWAA Serverless.

## Rede e segurança

Os workloads executam em sub-rede privada, sem IP público e sem NAT Gateway. O acesso a S3, ECR e CloudWatch Logs ocorre por VPC Endpoints. Conectividade externa necessária para fontes como DataCenterMap, Google APIs ou Google Earth Engine deve ser tratada explicitamente por workload, por meio de solução de egress/proxy aprovada.
O acesso aos recursos é controlado por IAM Roles, permission boundary e princípio de menor privilégio. Cada workload recebe apenas as permissões necessárias para suas fontes, tabelas, artifacts e logs.

## Persistência

O armazenamento é separado por responsabilidade:
- **Lakehouse S3:** tabelas governadas Bronze, Silver e Gold;
- **Artifacts S3:** modelos, checkpoints, outputs temporários, resultados do Athena e Terraform state;
- **MWAA S3:** definições de workflows;
- **DynamoDB:** estado operacional do `servico-runtime`, enquanto aplicável;
- **CloudWatch:** logs e telemetria operacional.

## Automação

A infraestrutura é definida com Terraform e o CI/CD é centralizado em GitHub Actions. Imagens de workloads são publicadas no ECR com referência imutável, evitando dependência de tags mutáveis como `latest`.
