# Sentinela Verde

O **Sentinela Verde** é um projeto do MBA em Engenharia de Dados voltado ao monitoramento dos impactos territoriais e ambientais no entorno de data centers por meio de imagens de satélite, engenharia de dados, modelagem e análise estatística.

Esta página do repositório apresenta apenas uma visão introdutória. A documentação completa do projeto, incluindo arquitetura, governança, metodologia, resultados e entregáveis acadêmicos, está publicada em:

**[sentinela-verde.github.io/projeto-docs](https://sentinela-verde.github.io/projeto-docs/)**

A organização do projeto no GitHub está disponível em **[github.com/Sentinela-Verde](https://github.com/Sentinela-Verde)**.

## Repositórios do projeto

| Repositório | Visibilidade | Finalidade |
| --- | --- | --- |
| [`projeto-docs`](https://github.com/Sentinela-Verde/projeto-docs) | Público | Documentação oficial do projeto e código-fonte do site publicado no GitHub Pages. |
| [`pipeline-dados`](https://github.com/Sentinela-Verde/pipeline-dados) | Público | Implementação científica integrada de referência, cobrindo o pipeline de ponta a ponta e a validação da metodologia. |
| [`modelo-imagens-satelite`](https://github.com/Sentinela-Verde/modelo-imagens-satelite) | Público | Frente de modelagem geoespacial e Machine Learning sobre séries temporais de imagens de satélite. |
| [`data-pipeline-model`](https://github.com/Sentinela-Verde/data-pipeline-model) | Público | Implementação integrada anterior do pipeline, com extração, transformação, modelagem e analytics. |
| [`modulos-terraform`](https://github.com/Sentinela-Verde/modulos-terraform) | Público | Módulos Terraform reutilizáveis para padronizar o provisionamento dos recursos AWS. |
| `automacao-cicd` | **Ainda não público** | Workflows reutilizáveis de CI/CD compartilhados entre os repositórios da organização. |
| `plataforma-fundacao` | **Ainda não público** | Infraestrutura base da plataforma, incluindo rede, segurança e componentes compartilhados de execução. |
| `plataforma-lakehouse` | **Ainda não público** | Infraestrutura e configuração do Lakehouse governado em Amazon S3, Glue e Athena. |
| `governanca-dados` | **Ainda não público** | Contratos de dados, regras de governança e definições das camadas Bronze, Silver e Gold. |
| `servico-runtime` | **Ainda não público** | Serviço responsável pelo controle do estado operacional e rastreabilidade das execuções. |
| `etl-template` | **Ainda não público** | Template padronizado para criação de novos workloads de ETL. |
| `modelo-template` | **Ainda não público** | Template padronizado para criação de novos workloads de modelos. |
| `etl-map-datacenters` | **Ainda não público** | ETL responsável pela coleta governada de informações de data centers a partir do DataCenterMap. |
| `etl-geocoding-datacenters` | **Ainda não público** | ETL de geocodificação e padronização territorial dos data centers coletados. |
| `classificacao-cobertura` | **Ainda não público** | Workload de classificação de cobertura do solo executado de forma efêmera na plataforma AWS. |
| `dashboard` | **Ainda não público** | Aplicação de visualização e exploração dos resultados analíticos do projeto. |
| `glue_jobs_validacao` | **Ainda não público** | Repositório de validação de jobs e padrões de processamento com AWS Glue. |
| `lambda_validacao` | **Ainda não público** | Repositório de validação de funções AWS Lambda e padrões de integração da plataforma. |
| `datacenter-extracao-modelos` | **Ainda não público — depreciado** | Repositório legado de extração/modelagem mantido apenas como referência histórica. |

> A visibilidade indicada acima corresponde ao estado atual da organização. Repositórios marcados como **Ainda não público** existem no projeto, mas ainda não estão disponíveis para acesso público.

## Desenvolvimento local da documentação

Requer Python 3.11 ou superior.

### Windows (PowerShell)

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
mkdocs serve
```

### Linux ou macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
mkdocs serve
```

Para validar o site:

```bash
mkdocs build --strict
```

## Publicação

O workflow [`.github/workflows/deploy-pages.yml`](.github/workflows/deploy-pages.yml) constrói e publica a documentação no GitHub Pages a partir da branch `main`.
