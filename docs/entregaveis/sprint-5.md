# Sprint 5 — 08/09 a 14/09

!!! warning "Registro histórico"
    Esta página preserva o conteúdo do checkpoint e sua consolidação final até 17/09. Decisões de arquitetura vigentes estão na [documentação atual](../arquitetura/index.md).

## Consolidação final até 17/09

## 1. Revisão geral do escopo

Na última sprint consolidamos o fluxo de ponta a ponta: localização dos data centers, imagens, índices espectrais, classificação de cobertura, temperatura de superfície, grupos de controle e indicadores.

Também revisamos endereços, proveniência e modelagem, expandindo a análise complementar para **31 data centers de tratamento e 31 controles** no Brasil e nos Estados Unidos.

Na consolidação até 17/09, avançamos na produtização: ajustes no workload de classificação, infraestrutura própria para o `etl-map-datacenters`, imagem no ECR e orquestração pelo MWAA Serverless com execução efêmera em EC2 Spot.

## 2. Finalização e revisão da documentação

Revisamos a documentação técnica e reorganizamos os repositórios para deixar claras as responsabilidades de governança, Lakehouse, infraestrutura, ETLs, modelos e produtos analíticos.

A arquitetura de dados foi consolidada em **Bronze, Silver e Gold**, com tabelas **Apache Iceberg sobre Parquet** no S3 e catálogo no AWS Glue. Os diagramas também foram alinhados às visões conceitual, AWS e ciclo de vida das aplicações.

Por fim, criamos o `projeto-docs` e publicamos a documentação consolidada no **GitHub Pages**, mantendo o Notion como documentação de trabalho e referência do projeto.

## 3. Criação da apresentação final

A apresentação final foi estruturada como uma narrativa curta: problema de negócio → imagens de satélite → classificação por Random Forest → evolução temporal → grupo de controle → efeito líquido → resultados.

Mantivemos como pontos centrais a transformação do pixel em dado estruturado, a comparação tratamento-controle e a expansão para **31 data centers e seus pares**.

### Referência visual — evolução temporal

![Evolução temporal — Ascenty Vinhedo](../assets/images/ascenty_vinhedo_evolucao.png)

*Exemplo de evolução temporal utilizado para comunicar a mudança de cobertura do solo ao longo dos períodos pré-obra, obra e pós-obra.*

Na parte final resumimos arquitetura, CI/CD e governança, mostrando como a metodologia foi transformada em uma solução reproduzível.

A apresentação foi ajustada entre 15 e 17/09 para caber no limite de aproximadamente 15 minutos e terminou destacando possíveis evoluções em água, energia e ruído.

## Referências visuais e relacionadas

![Arquitetura AWS](../assets/images/Arquitetura_AWS.png)

*Arquitetura AWS consolidada utilizada na documentação final do projeto.*

- [Arquitetura atual](../arquitetura/index.md)
- [Sprint 5 publicada no GitHub Pages](https://sentinela-verde.github.io/projeto-docs/entregaveis/sprint-5/)
- [Fonte Markdown da Sprint 5](https://github.com/Sentinela-Verde/projeto-docs/blob/main/docs/entregaveis/sprint-5.md)
- [Documentação completa no GitHub Pages](https://sentinela-verde.github.io/projeto-docs/)
- [Repositório `pipeline-dados`](https://github.com/Sentinela-Verde/pipeline-dados) — implementação científica integrada e base da metodologia final.
- [Repositório `modelo-imagens-satelite`](https://github.com/Sentinela-Verde/modelo-imagens-satelite) — experimentos, validações e evolução da modelagem geoespacial.
- [Repositório `projeto-docs`](https://github.com/Sentinela-Verde/projeto-docs) — fonte da documentação pública.
- [Lucid — Arquitetura AWS](https://lucid.app/lucidchart/dcfc98c9-24cd-4f84-9ea1-5b53f141a5f1/edit)
- [Lucid — Arquitetura de Engenharia de Dados](https://lucid.app/lucidchart/d8f69c0c-1a9f-4b8c-b99d-56c7ca8f35a3/edit)
- [Lucid — Ciclo de Vida das Aplicações](https://lucid.app/lucidchart/4d3ccf8c-38b7-4a9a-a9f8-412432af2207/edit)
