# Padrão Python

Este documento define o padrão de código **Python** do Vulpes Code Standard. A
configuração correspondente é o template `configs/pyproject.toml`.

## Versão da linguagem

- **Python 3.10 ou superior.**

O requisito mínimo é declarado em `requires-python = ">=3.10"`. Não utilize
recursos que exijam uma versão mais nova sem justificativa explícita.

## Ferramentas

| Ferramenta | Papel |
| ---------- | ----- |
| **Ruff** | Linter (e formatador compatível com Black) |
| **Black** | Formatador |
| **Pytest** | Framework de testes |

Ruff e Black são configurados de forma **idêntica e compatível** (mesmo
comprimento de linha e mesmo `target-version`). Recomenda-se adotar **`ruff
format`** como formatador padrão do dia a dia por unificar lint e formatação;
Black permanece suportado para projetos que já o utilizam.

## Comprimento de linha

O padrão é de **88 caracteres**, alinhado ao Black.

## Type hints

Type hints são **obrigatórios em 100% dos argumentos e retornos de funções**, em
todas as camadas.

```python
def calculate_buffer_size(data: bytes) -> int:
    ...
```

A regra de lint `ANN` (flake8-annotations) faz parte da configuração do Ruff e
**falha** quando anotações estão ausentes.

## Nomenclatura

| Elemento | Convenção | Exemplo |
| -------- | --------- | ------- |
| Módulos | `snake_case` | `user_service.py` |
| Variáveis | `snake_case` | `buffer_size` |
| Funções | `snake_case` | `validate_request()` |
| Classes | `PascalCase` | `UserSession` |
| Constantes | `UPPER_SNAKE_CASE` | `MAX_BUFFER_SIZE` |

A regra `N` (pep8-naming) valida essas convenções automaticamente.

## Organização dos módulos

A estrutura de pacotes segue a [Den Architecture](architecture.md):

```text
meu-projeto/
├── den/          # A Toca — domínio puro (sem I/O)
├── senses/       # O Faro — validação e contratos
├── adapters/     # A Caça — I/O e integrações
├── vanguard/     # A Vanguarda — entrypoints e orquestração
└── kit/          # A Ninhada — testes
```

- `den/` não importa `senses/`, `adapters/` nem `vanguard/`.
- I/O pertence exclusivamente a `adapters/`.
- A validação de entrada pertence a `senses/`.
- Os testes residem em `kit/`, refletido em `testpaths = ["kit"]`.

## Uso do `pyproject.toml`

O arquivo `configs/pyproject.toml` é um **template distribuível**. Copie-o para a
**raiz** do seu projeto consumidor:

```bash
cp configs/pyproject.toml meu-projeto/pyproject.toml
```

Depois, instale as ferramentas e execute:

```bash
ruff check .
ruff format --check .
pytest
```

## Configuração de lint

A configuração habilita as seguintes famílias de regras:

| Código | Origem | Finalidade |
| ------ | ------ | ---------- |
| `E`, `F`, `W` | pycodestyle / pyflakes | Erros e avisos básicos |
| `I` | isort | Ordenação de imports |
| `N` | pep8-naming | Convenções de nomes |
| `UP` | pyupgrade | Sintaxe moderna |
| `B` | flake8-bugbear | Bugs prováveis |
| `A` | flake8-builtins | Sombra de builtins |
| `C4` | flake8-comprehensions | Comprehensions |
| `SIM` | flake8-simplify | Simplificações |
| `ANN` | flake8-annotations | Type hints obrigatórios |
| `TID` | flake8-tidy-imports | Higiene de imports |
| `RUF` | Ruff | Regras específicas do Ruff |

## Configuração do Pytest

O Pytest é configurado na seção `[tool.pytest.ini_options]`:

- `testpaths = ["kit"]` — os testes residem em `kit/`;
- `python_files = ["test_*.py"]`;
- `addopts = "-ra --strict-markers --strict-config"`.
