# Sentinela Verde — documentação do projeto

Este repositório publica a documentação final do **Sentinela Verde — Monitoramento Geoespacial de Data Centers** como um site estático no GitHub Pages.

O conteúdo editorial tem como fonte a página Notion `Sentinela Verde — Documentação do Projeto` e todas as suas subpáginas. O site organiza esse material em páginas Markdown, preserva os entregáveis históricos e mantém os diagramas e demais imagens dentro do próprio repositório.

## Pré-visualização local

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

O servidor local fica disponível, por padrão, em `http://127.0.0.1:8000/`.

## Validação

```bash
mkdocs build --strict
```

## Publicação

O workflow [`.github/workflows/deploy-pages.yml`](.github/workflows/deploy-pages.yml) constrói o site e publica o artefato pelo mecanismo oficial do GitHub Pages em cada push para `main` ou por acionamento manual.

Após o GitHub Pages estar habilitado com **GitHub Actions** como origem, o endereço esperado do projeto é:

<https://sentinela-verde.github.io/projeto-docs/>
