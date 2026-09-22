<p align="center">
  <img src="assets/vulpes-banner.jpeg" alt="Vulpes Code Standard — banner oficial" />
</p>

# Vulpes Code Standard


Padrão oficial de código e arquitetura da equipe para projetos em **C** e
**Python**, com documentação publicada automaticamente via GitHub Pages.

## Propósito

Eliminar a falta de consistência arquitetural e estilística entre bases de
código, fornecendo um contrato técnico único para formatação, nomenclatura,
organização em camadas e validação automatizada.

## Visão geral

O Vulpes Code Standard reúne:

- **configurações prontas** para EditorConfig, clang-format, Ruff, Black e Pytest;
- a **Den Architecture**, um padrão de organização em cinco camadas;
- **documentação técnica** publicada com MkDocs Material;
- **CI/CD** que compila e publica o site no GitHub Pages.

## Den Architecture

Cinco camadas com fluxo de dependência **unidirecional, de fora para dentro**:

| Diretório | Conceito Vulpes | Camada | Responsabilidade |
| --------- | --------------- | ------ | ---------------- |
| `den/` | A Toca | Domain Core | Regras de negócio puras e algoritmos |
| `senses/` | O Faro | Contracts & Validation | Validação, sanitização, autorização, logs |
| `adapters/` | A Caça | Infrastructure & I/O | Filesystem, banco de dados, APIs, drivers |
| `vanguard/` | A Vanguarda | Application Entry | Entrypoints, CLI, ciclo de vida, `main` |
| `kit/` | A Ninhada | Testing & QA | Testes, mocks, fixtures, carga |

Regras centrais:

- `den/` **não pode** depender de `senses/`, `adapters/` ou `vanguard/`.
- I/O pertence **exclusivamente** a `adapters/`.
- `vanguard/` é o ponto de entrada e orquestração.
- Dados só chegam a `den/` após validação em `senses/`.

Detalhes em [docs/architecture.md](docs/architecture.md).

## Estrutura do repositório

```text
vulpes-code-standard/
├── .github/workflows/deploy-docs.yml   # CI/CD para GitHub Pages
├── assets/                             # Identidade visual (mascote)
├── configs/                            # Templates de configuração
│   ├── .editorconfig
│   ├── .clang-format
│   └── pyproject.toml
├── docs/                               # Documentação
│   ├── index.md
│   ├── architecture.md
│   ├── c-standard.md
│   └── python-standard.md
├── mkdocs.yml                          # Configuração do site
├── requirements.txt                    # Dependências da documentação
├── README.md
└── LICENSE                             # MIT
```

## Padrões

### C

- C11 ou superior; indentação de 4 espaços; tabs proibidos; chaves Attach/K&R.
- `snake_case` (funções/variáveis), `UPPER_SNAKE_CASE` (macros/constantes),
  tipos com sufixo `_t`; ponteiros alinhados à variável.
- Proibição de `strcpy`/`sprintf`; preferência por `strncpy`/`snprintf` com
  validação em `senses/`.
- **Memória:** preferência por alocação baseada em lifetime — arena allocation é
  preferencial quando os objetos compartilham um lifetime comum. `malloc`,
  `calloc`, `realloc` e `free` **não** são proibidos; continuam permitidos quando
  semanticamente apropriados.

Guias completos: [docs/c-standard.md](docs/c-standard.md) e
[docs/memory-management.md](docs/memory-management.md).

### Python

- Python 3.10+; Ruff, Black e Pytest; linha de 88 caracteres.
- Type hints obrigatórios em 100% de argumentos e retornos.
- `snake_case` (módulos/variáveis/funções), `PascalCase` (classes),
  `UPPER_SNAKE_CASE` (constantes).

Guia completo: [docs/python-standard.md](docs/python-standard.md).

## Configurações

Os arquivos em `configs/` são **templates distribuíveis**. Eles **não** são
aplicados automaticamente a este repositório, e as ferramentas **não** os
descobrem em `configs/` — EditorConfig, clang-format, Ruff, Black e Pytest
procuram suas configurações na **raiz** do projeto.

Para adotar o padrão em um projeto consumidor, copie o template para a raiz:

```bash
cp configs/.editorconfig   meu-projeto/.editorconfig
cp configs/.clang-format   meu-projeto/.clang-format
cp configs/pyproject.toml  meu-projeto/pyproject.toml
```

## Documentação

A documentação é construída com **MkDocs Material**. Para pré-visualizar
localmente:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve
```

Build estrito (mesmo usado no CI):

```bash
mkdocs build --strict
```

## CI/CD

O workflow [`.github/workflows/deploy-docs.yml`](.github/workflows/deploy-docs.yml)
executa em `push` para a branch `main`: configura o Python, instala as
dependências, executa `mkdocs build --strict`, gera o artifact e publica no
GitHub Pages usando `upload-pages-artifact` e `deploy-pages`.

> **Requisito de configuração:** em *Settings → Pages*, defina a origem como
> **GitHub Actions**.

## Licença

Distribuído sob a licença **MIT**. Consulte [LICENSE](LICENSE).
