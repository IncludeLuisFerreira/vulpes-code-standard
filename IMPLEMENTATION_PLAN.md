# Vulpes Code Standard v1.0.0
# Implementation Plan

> Documento técnico de planejamento. **Nenhum código de implementação é produzido nesta etapa.**
> Fonte de requisitos: `PRD-code-standard.md` (na raiz do repositório).
> Regra de ouro: cada requisito do PRD é mapeado para uma ação planejada e um método de validação objetivo.

---

## 1. Executive Summary

O repositório analisado encontra-se **praticamente vazio**: contém apenas o arquivo `PRD-code-standard.md`. Não há repositório Git inicializado, nem diretórios, configurações, documentação, CI/CD, README ou LICENSE.

Portanto, a implementação do **Vulpes Code Standard v1.0.0** é quase integralmente **greenfield**: 11 dos 12 artefatos obrigatórios estão `A IMPLEMENTAR`, e 1 (`PRD-code-standard.md`) é `EXISTENTE` e deve ser preservado.

O produto final **não é uma aplicação executável**, mas um **repositório de referência normativa**: um conjunto de configurações distribuíveis (`configs/`), documentação publicada via MkDocs (`docs/`), um site com Material for MkDocs (`mkdocs.yml`) e automação de publicação no GitHub Pages (`.github/workflows/deploy-docs.yml`).

**Decisões centrais deste plano:**

1. As camadas da Den Architecture (`den/`, `senses/`, `adapters/`, `vanguard/`, `kit/`) são **conteúdo normativo/documental**, não diretórios a serem criados neste repositório. A árvore-alvo do PRD (seção 3) não as inclui. Esta decisão é explicitada na seção 4.
2. Os arquivos em `configs/` são **templates distribuíveis**, não configurações ativas do próprio repositório. Ferramentas como clang-format, EditorConfig, Ruff e Black não os descobrem automaticamente na raiz. A validação deve, portanto, apontar explicitamente para `configs/` ou usar arquivos-amostra temporários. Esta é a divergência técnica mais relevante e é tratada na seção 3.1 e na seção 10.
3. O deploy de GitHub Pages usa a **abordagem oficial moderna** (`actions/upload-pages-artifact` + `actions/deploy-pages`), e não `mkdocs gh-deploy` para a branch `gh-pages`. O PRD não prescreve o mecanismo; a divergência é registrada na seção 8.
4. A mascote (`assets/vulpes-mascot.png`) e o titular de copyright da LICENSE **não existem e não podem ser inventados**. São dependências humanas, listadas na seção 15.

**Estimativa de esforço:** 6 fases (0–6), 14 arquivos criados, 1 arquivo preservado, 1 arquivo opcional (`requirements.txt`), 1 arquivo opcional de exemplo C para validação.

---

## 2. Repository Assessment

### 2.1 Current Structure

Árvore real do repositório (inspeção via `find . -mindepth 1 -maxdepth 3`):

```text
Vulpes-code-stardard/
└── PRD-code-standard.md        # único arquivo presente
```

Evidências:
- `ls -la` na raiz lista somente `PRD-code-standard.md` (13.136 bytes).
- `find . -type f -name ".*"` não retorna nada → **não há arquivos ocultos**.
- `git rev-parse --is-inside-work-tree` → `fatal: not a git repository` → **não há repositório Git**.
- Não existem `.github/`, `assets/`, `configs/`, `docs/`, nem `mkdocs.yml`, `README.md`, `LICENSE`.

**Observação de nomenclatura (não bloqueante):** o diretório local chama-se `Vulpes-code-stardard` (erro de digitação de "standard"). O nome do diretório não é um arquivo versionado e não afeta a implementação; se o projeto for publicado no GitHub, recomenda-se que o repositório remoto seja `vulpes-code-standard`. Registrado na seção 15.

### 2.2 Existing Configuration

**Nenhuma.** Não há `.editorconfig`, `.clang-format`, `pyproject.toml`, `Makefile`, `CMakeLists.txt`, `requirements.txt`, `package.json`, `.gitignore` ou qualquer manifest.

Ferramentas disponíveis no ambiente (evidência de viabilidade, não requisito de versão do projeto):
- `python3 --version` → Python 3.14.7 (atende ao requisito Python 3.10+).
- `clang-format --version` → 18.1.8 (suporta `Standard: c11`; ver seção 5.2).
- `ruff --version` → 0.11.13.
- `mkdocs --version` → 1.6.1.
- `git --version` → 2.53.0.

### 2.3 Existing Documentation

Apenas `PRD-code-standard.md`. Não existe `docs/`, `README.md`, `index.md`, `architecture.md`, `c-standard.md` ou `python-standard.md`.

### 2.4 Existing CI/CD

**Nenhum.** Não existe `.github/workflows/` nem qualquer pipeline.

### 2.5 Gaps

| # | Gap | Severidade | Requisito PRD |
|---|-----|-----------|---------------|
| G-01 | Repositório Git não inicializado | Alta | §3, CA-01 (commit principal) |
| G-02 | Estrutura de diretórios ausente | Alta | §3, CA-01 |
| G-03 | Configurações de C/Python/Editor ausentes | Alta | §4, §5, CA-03 |
| G-04 | Documentação ausente | Alta | §6, CA-04 |
| G-05 | `mkdocs.yml` ausente | Alta | §7 |
| G-06 | Workflow de CI/CD ausente | Alta | §8, CA-02 |
| G-07 | Mascote ausente | Média | §3, §10 |
| G-08 | LICENSE ausente | Média | §3, §11 |
| G-09 | README ausente | Média | §12 |
| G-10 | Mecanismo de dependências de docs indefinido | Média | §8, §9 |

---

## 3. Requirement Status

Classificação de cada requisito do PRD: `EXISTENTE`, `PARCIAL`, `A IMPLEMENTAR`, `CONFLITANTE`, `NÃO APLICÁVEL`.

| Requirement | Status | Evidence | Action |
|---|---|---|---|
| PRD `PRD-code-standard.md` | EXISTENTE | Arquivo presente na raiz (13.136 bytes) | Preservar sem alteração |
| Estrutura de diretórios (`configs/`, `docs/`, `assets/`, `.github/workflows/`) | A IMPLEMENTAR | Ausentes | Criar (Fase 1) |
| `configs/.editorconfig` | A IMPLEMENTAR | Ausente | Criar (Fase 2) |
| `configs/.clang-format` | A IMPLEMENTAR | Ausente | Criar (Fase 2) |
| `configs/pyproject.toml` (Ruff/Black/Pytest) | A IMPLEMENTAR | Ausente | Criar (Fase 2) |
| `docs/index.md` | A IMPLEMENTAR | Ausente | Criar (Fase 3) |
| `docs/architecture.md` | A IMPLEMENTAR | Ausente | Criar (Fase 3) |
| `docs/c-standard.md` | A IMPLEMENTAR | Ausente | Criar (Fase 3) |
| `docs/python-standard.md` | A IMPLEMENTAR | Ausente | Criar (Fase 3) |
| `mkdocs.yml` (Material, slate, cyan/teal) | A IMPLEMENTAR | Ausente | Criar (Fase 4) |
| Dependências de documentação (`requirements.txt`) | A IMPLEMENTAR | Ausente; PRD §8 pede avaliação | Criar (Fase 4) — decisão técnica |
| `.github/workflows/deploy-docs.yml` | A IMPLEMENTAR | Ausente | Criar (Fase 5) |
| `assets/vulpes-mascot.png` | A IMPLEMENTAR (bloqueado) | Ausente; PRD §10 proíbe inventar | Aguardar asset humano (Fase 1/6) |
| `LICENSE` (MIT) | A IMPLEMENTAR (bloqueado) | Ausente; titular de copyright desconhecido | Aguardar dado humano (Fase 1/6) |
| `README.md` | A IMPLEMENTAR | Ausente | Criar (Fase 3) |
| Diretórios de camada `den/ senses/ adapters/ vanguard/ kit/` | NÃO APLICÁVEL (como diretórios) | Não constam na árvore-alvo do PRD §3 | Documentar como norma em `docs/architecture.md` (Fase 3) |
| Regras de isolamento arquitetural | A IMPLEMENTAR (documental) | Sem documentação | Escrever em `docs/architecture.md` (Fase 3) |
| Deploy automático GitHub Pages | A IMPLEMENTAR | Sem workflow | Implementar via Pages Actions (Fase 5) |
| Configuração de repositório GitHub (Pages = GitHub Actions) | A IMPLEMENTAR (manual) | Não há repositório remoto | Configuração humana pós-push (Fase 5/6) |

### 3.1 Divergências e Conflitos

Formato obrigatório: `PRD / Problema / Proposta / Impacto / Decisão necessária`.

---

**D-01 — Localização dos arquivos de configuração vs. descoberta automática por ferramentas**

```text
PRD:
§3 e §5 exigem .editorconfig, .clang-format e pyproject.toml dentro de configs/.
CA-03 exige que sejam "reconhecido[s] por IDEs como VS Code e CLion",
"reconhecido[s] pelo Ruff/Black" e que o Pytest os reconheça.

Problema:
EditorConfig, clang-format, Ruff, Black e Pytest descobrem suas configurações
subindo a árvore de diretórios a partir do arquivo-alvo, procurando os arquivos
na RAIZ do projeto. Arquivos sob configs/ NÃO são descobertos automaticamente.
Logo, se os arquivos ficarem apenas em configs/, o próprio repositório não é
formatado por eles e a "descoberta" só funciona quando um projeto consumidor
copiar o arquivo para a própria raiz.

Proposta:
Tratar configs/ como pasta de TEMPLATES DISTRIBUÍVEIS (fonte canônica), e validar
cada template apontando explicitamente para ele:
- clang-format:  clang-format --style=file:configs/.clang-format ...
- ruff:          ruff check --config configs/pyproject.toml <amostra>
- black:         black --config configs/pyproject.toml --check <amostra>
- pytest:        pytest -c configs/pyproject.toml --collect-only
- editorconfig:  editorconfig-checker -config configs/.editorconfig
NÃO duplicar na raiz (evita duas fontes de verdade). A documentação explicará
ao consumidor que deve copiar o arquivo para a raiz do seu projeto.

Impacto:
O repositório vulpes-code-standard não fica auto-formatado (não possui fontes
C/Python). O consumidor ganha um template reutilizável e a validação continua
objetiva via flag --config.

Decisão necessária:
Confirmar a abordagem "template sob configs/ + validação com --config explícito"
em vez de "cópias/symlinks na raiz". (Ver Open Question OQ-04.)
```

---

**D-02 — Mecanismo de deploy do GitHub Pages**

```text
PRD:
§8 exige publicar o site no GitHub Pages a partir de um push na main. Não
especifica o mecanismo. A redação sugere "compilar" e "publicar".

Problema:
A abordagem histórica (mkdocs gh-deploy / branch gh-pages) está desatualizada
frente ao fluxo oficial atual do GitHub Pages, baseado em artifacts
(actions/configure-pages + actions/upload-pages-artifact + actions/deploy-pages),
que exige permissões pages: write e id-token: write e um environment github-pages.

Proposta:
Adotar o fluxo oficial com artifacts e deploy-pages, mantendo o requisito
funcional (deploy automático em push na main). Configurar Pages source como
"GitHub Actions" nas settings do repositório.

Impacto:
Requer configuração manual única nas settings do repositório. Não requer branch
gh-pages. É o mecanismo recomendado e suportado oficialmente.

Decisão necessária:
Confirmar fluxo com artifacts (recomendado) vs. gh-deploy para gh-pages.
```

---

**D-03 — Ruff format vs. Black (formatters sobrepostos)**

```text
PRD:
§4.2 exige compatibilidade simultânea com Ruff e Black, line-length 88.

Problema:
Ruff possui formatter Black-compatible e Black também formata. Rodar ambos de
forma concorrente é redundante e pode gerar diffs/loops se configurações
divergirem.

Proposta:
Configurar AMBOS com line-length 88 e target py310, de modo idêntico e
compatível. Documentar que o time deve escolher UM formatador por projeto
(recomendação: ruff format, por unificar lint+format), mantendo Black suportado
para consumidores que já o adotam.

Impacto:
Nenhum conflito de configuração; ambos permanecem reconhecíveis (CA-03).

Decisão necessária:
Confirmar que ambos serão mantidos na configuração (requisito) e qual é o
formatador PADRÃO recomendado (proposta: ruff format).
```

---

**D-04 — Ausência de fontes C/Python para validar os linters**

```text
PRD:
CA-03 exige que .clang-format formate arquivos .c sem erros e que pyproject.toml
seja reconhecido por Ruff/Black/Pytest.

Problema:
O repositório-alvo não contém código C nem testes Python. Pytest sem testes sai
com código 5 ("no tests ran"). clang-format sem arquivo-alvo não prova nada.

Proposta:
Validação por meio de artefatos efêmeros/amostras de validação:
- criar um arquivo-amostra C temporário em /tmp e formatá-lo com
  --style=file:configs/.clang-format;
- validar TOML com tomllib;
- executar ruff/black --check sobre uma amostra temporária;
- executar pytest -c configs/pyproject.toml --collect-only e aceitar exit 5 como
  "configuração válida, sem testes".
Opcional: adicionar `kit/smoke/test_config.py` mínimo para dar ao Pytest um alvo
real (cria a camada kit/ e materializa o padrão). Requer aprovação (OQ-05).

Impacto:
Validação objetiva sem poluir o repositório. Se OQ-05 for aprovado, adiciona 1
diretório e 1 arquivo de teste.

Decisão necessária:
Aprovar amostras efêmeras (recomendado) e/ou criação de kit/ smoke test.
```

---

**D-05 — Mascote e LICENSE dependem de dados humanos**

```text
PRD:
§3 exige assets/vulpes-mascot.png (mascote oficial sem fundo). §11 exige LICENSE
MIT. Regras do plano proíbem inventar conteúdo da mascote e nome de copyright.

Problema:
Nenhum dos dois existe, e ambos exigem informação que só o mantenedor possui.

Proposta:
Bloquear a conclusão de CA-01 até que (a) o PNG seja fornecido pelo mantenedor e
(b) o titular de copyright e o ano sejam informados. O plano especifica as
propriedades a validar e o template MIT, sem preencher autoria.

Impacto:
CA-01 e a "Definição de Pronto" permanecem incompletos até a entrega humana.

Decisão necessária:
Fornecer o PNG da mascote e o copyright holder (pessoa ou organização) + ano.
```

---

## 4. Architecture Plan

### 4.1 Den Architecture

A **Den Architecture** organiza software em **5 camadas estritas**, com fluxo de dependência **unidirecional de fora para dentro**. Correspondência oficial:

| Diretório | Conceito Vulpes | Camada Tradicional | Responsabilidade |
|---|---|---|---|
| `den/` | A Toca | Domain Core | Regras de negócio puras, estruturas centrais, algoritmos. Zero dependências externas. |
| `senses/` | O Faro | Contracts & Validation | Validação, sanitização, limites de buffer, schemas, autorização, telemetria/logging, normalização. |
| `adapters/` | A Caça | Infrastructure & I/O | Filesystem, banco de dados, APIs externas, drivers, sockets, serviços externos. |
| `vanguard/` | A Vanguarda | Application Entry | Entrypoints, CLI, handlers de API, inicialização, ciclo de vida, `main`, orquestração. |
| `kit/` | A Ninhada | Testing & QA | Testes unitários/integração, mocks, fixtures, testes de carga. |

**Decisão arquitetural A-01:** Neste repositório, as camadas **não são criadas como diretórios**. O `vulpes-code-standard` é a **norma**, não uma aplicação; a árvore-alvo oficial (PRD §3) contém apenas `.github/`, `assets/`, `configs/`, `docs/`, `mkdocs.yml`, `README.md` e `LICENSE`. As camadas são **documentadas** em `docs/architecture.md` e adotadas nos repositórios consumidores.

**Justificativa:** criar diretórios vazios de camadas aqui violaria YAGNI, criaria falsas expectativas de código e divergiria da árvore oficial do PRD. A opção de criar `den/`…`kit/` como diretórios-exemplo é registrada como alternativa em OQ-05 (somente `kit/` faz sentido, para testes de validação).

### 4.2 Dependency Rules

Fluxo permitido (de fora para dentro):

```text
vanguard/  →  senses/  →  den/
vanguard/  →  adapters/ →  den/
vanguard/  →  den/
```

Regras normativas a documentar:

1. `den/` **não pode** importar `senses/`, `adapters/` nem `vanguard/`. Deve evitar dependências externas.
2. `senses/` valida/sanitiza e pode depender de `den/`; não deve depender de `adapters/` nem de `vanguard/`.
3. `adapters/` implementa I/O e pode depender de `den/`; não deve depender de `vanguard/`.
4. `vanguard/` é a única camada autorizada a orquestrar todas as demais e a possuir o `main`.
5. `kit/` pode importar qualquer camada (é a suíte de testes), mas nenhuma camada de produção pode importar `kit/`.
6. Dados só chegam a `den/` **após** validação em `senses/`.

### 4.3 Directory Responsibilities

- **`den/` — A Toca:** funções puras, sem efeitos colaterais, sem I/O, testável sem mocks.
- **`senses/` — O Faro:** fronteira de confiança; toda entrada externa é validada aqui, inclusive limites de buffer (relevante para C).
- **`adapters/` — A Caça:** único ponto de contato com o mundo externo; substituível por mocks em testes.
- **`vanguard/` — A Vanguarda:** composição e ciclo de vida; "cola" entre camadas, sem regra de negócio.
- **`kit/` — A Ninhada:** qualidade; não participa do binário/artefato de produção.

**Nota sobre `vanguard/`:** o PRD exige explicitamente que sua função como *Application Entry* seja destacada — `docs/architecture.md` deve dedicar uma subseção a isso (ver 6.2).

---

## 5. Configuration Plan

> Todos os arquivos desta seção são **templates distribuíveis** (ver D-01). O conteúdo abaixo especifica as opções e as decisões; a implementação (Fase 2) os materializa.

### 5.1 EditorConfig — `configs/.editorconfig`

**Requisito PRD (§5/E1, §6):** `end_of_line = lf`, `insert_final_newline = true`, `indent_size = 4`.

**Decisão técnica EC-01:** `root = true` no topo, para que o template, quando copiado para um projeto consumidor, não herde configurações acima dele.

**Regras globais e por tipo de arquivo (proposta):**

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 4

[*.{c,h}]
indent_size = 4

[*.py]
indent_size = 4

[*.{md,markdown}]
indent_size = 2
trim_trailing_whitespace = false   # dois espaços finais são quebra de linha em Markdown

[*.{yml,yaml}]
indent_size = 2

[*.{toml,ini}]
indent_size = 2

[*.json]
indent_size = 2

[Makefile]
indent_style = tab                  # Makefiles exigem tabs por especificação
```

**Decisões e justificativas:**
- `indent_size = 4` global atende ao requisito; 2 espaços para Markdown/YAML/TOML/JSON segue convenção do ecossistema e do MkDocs.
- `Makefile` é a **única exceção** a "tabs proibidos": a sintaxe do Make exige tab. Documentar essa exceção em `docs/c-standard.md`.
- `trim_trailing_whitespace = false` em Markdown evita remover quebras de linha significativas.

**Validação:** `editorconfig-checker -config configs/.editorconfig <amostra>` (ou parser INI via Python), conforme seção 10.

### 5.2 Clang Format — `configs/.clang-format`

**Requisitos PRD (§4.1, §5/E1):** base **LLVM**; 4 espaços; tabs proibidos; Attach/K&R/OTBS; `snake_case` (funções/variáveis); `UPPER_SNAKE_CASE` (macros/constantes); tipos com sufixo `_t`; ponteiro alinhado à variável; segurança (`strcpy`/`sprintf` proibidos; preferir `strncpy`/`snprintf` com validação em `senses/`).

> Importante: clang-format **formata**, não aplica convenções de nomenclatura nem proíbe funções. Nomenclatura e segurança são regras **documentais** (`docs/c-standard.md`) e podem ser parcialmente automatizadas por linters/CI (ver seção 10 e OQ-06).

**Decisão técnica CF-01 — ColumnLimit:** o PRD diz "conforme configuração". Recomendação: **100 colunas**. Justificativa: equilíbrio entre legibilidade, `snake_case` e nomes de domínio; 80 é restritivo para C moderno e 120 dificulta revisão lado a lado. Alternativa registrada em OQ-03.

**Decisão técnica CF-02 — Standard:** usar `Standard: c11`. O ambiente possui clang-format 18.1.8, que suporta esse valor. Se o time usar clang-format < 16, omitir a linha (documentar requisito de versão).

**Opções propostas (a materializar na Fase 2):**

| Opção | Valor | Motivo |
|---|---|---|
| `BasedOnStyle` | `LLVM` | Exigido pelo PRD |
| `Language` | `Cpp` | clang-format usa a gramática C++ para arquivos `.c` |
| `Standard` | `c11` | Requisito C11+ (clang-format ≥ 16) |
| `IndentWidth` | `4` | Requisito |
| `TabWidth` | `4` | Consistência |
| `UseTab` | `Never` | Tabs proibidos |
| `BreakBeforeBraces` | `Attach` | K&R/OTBS |
| `PointerAlignment` | `Right` | `void *ptr;` alinhado à variável |
| `ColumnLimit` | `100` | CF-01 |
| `AllowShortFunctionsOnASingleLine` | `None` | Legibilidade/revisão |
| `AllowShortIfStatementsOnASingleLine` | `Never` | Consistência |
| `AllowShortLoopsOnASingleLine` | `false` | Consistência |
| `IndentCaseLabels` | `false` | Estilo K&R |
| `KeepEmptyLinesAtTheStartOfBlocks` | `false` | Limpeza |
| `MaxEmptyLinesToKeep` | `1` | Limpeza |
| `SortIncludes` | `CaseSensitive` | Determinismo |
| `IncludeBlocks` | `Preserve` | Não reordenar grupos sem intenção |
| `SpaceAfterCStyleCast` | `false` | Estilo LLVM |
| `AlignConsecutiveAssignments` | `false` | Simplicidade (evita diffs amplos) |
| `AlignConsecutiveDeclarations` | `false` | Simplicidade |
| `AlignConsecutiveMacros` | `false` | Simplicidade |
| `Cpp11BracedListStyle` | `true` | Padrão moderno |
| `ReflowComments` | `true` | Consistência |
| `BinPackArguments` / `BinPackParameters` | `true` | Padrão LLVM |

**Validação:** `clang-format --style=file:configs/.clang-format --dump-config` (prova que o arquivo é parseável) e formatação de amostra `.c` (seção 10).

### 5.3 Python / Ruff / Black / Pytest — `configs/pyproject.toml`

**Requisitos PRD (§4.2, §5/E1):** Python 3.10+; Ruff; Black; Pytest; 88 colunas; type hints obrigatórios; `snake_case`/`PascalCase`/`UPPER_SNAKE_CASE`.

**Estrutura proposta do `pyproject.toml`:**

```toml
[project]
name = "vulpes-code-standard"
version = "1.0.0"
description = "Padrão de código e arquitetura Vulpes (C e Python)."
requires-python = ">=3.10"

[tool.ruff]
line-length = 88
target-version = "py310"

[tool.ruff.lint]
select = ["E", "F", "W", "I", "N", "UP", "B", "A", "C4", "SIM", "ANN", "TID", "RUF"]
# ANN (flake8-annotations) impõe type hints; alinhado ao requisito "100% de type hints".
ignore = ["ANN401"]  # permitir *args/**kwargs sem tipo, se necessário

[tool.ruff.lint.per-file-ignores]
"kit/**" = ["S101"]  # asserts em testes

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
line-ending = "lf"
docstring-code-format = true

[tool.black]
line-length = 88
target-version = ["py310"]

[tool.pytest.ini_options]
minversion = "7.0"
testpaths = ["kit"]
python_files = ["test_*.py"]
addopts = "-ra --strict-markers --strict-config"
```

**Decisões técnicas PY-01:**
- **Regras de lint:** `E/F/W` (pycodestyle/pyflakes), `I` (isort), `N` (pep8-naming → valida `snake_case`/`PascalCase`/`UPPER_SNAKE_CASE`), `UP` (pyupgrade), `B` (bugbear), `A` (builtins), `C4` (comprehensions), `SIM` (simplify), `ANN` (type hints), `TID` (import tidy), `RUF`. `ANN` é o que materializa o requisito de type hints obrigatórios.
- **`target-version = "py310"`** garante que Ruff/Black não sugiram sintaxe acima de 3.10 (compatibilidade mínima exigida).
- **`testpaths = ["kit"]`** alinha o Pytest à Den Architecture (testes em `kit/`).
- **Plugins/dependências:** nenhum plugin de lint adicional é necessário. Ruff é binário independente; Black e Pytest são dependências de desenvolvimento. **Evitar adicionar dependências sem necessidade** (regra 15 do plano).
- **Pytest sem testes:** `--strict-config` faz o Pytest falhar se a seção `[tool.pytest.ini_options]` for inválida — útil para validação. Sem testes, o exit code é 5 (esperado; ver D-04).

**Validação:**
- TOML válido: `python -c "import tomllib; tomllib.load(open('configs/pyproject.toml','rb'))"`.
- Ruff reconhece: `ruff check --config configs/pyproject.toml --no-cache <amostra>`.
- Black reconhece: `black --config configs/pyproject.toml --check <amostra>`.
- Pytest reconhece: `pytest -c configs/pyproject.toml --collect-only` (exit 0 ou 5).

---

## 6. Documentation Plan

### 6.1 `docs/index.md`

**Objetivo:** porta de entrada do site. Conteúdo esperado:
1. Título e resumo do **Vulpes Code Standard**.
2. Objetivos (padronização estilística, arquitetura unificada, documentação automatizada, verificação/adoção).
3. Filosofia geral (consistência, baixo acoplamento, reprodutibilidade, automação).
4. Visão geral resumida da Den Architecture, com link para `architecture.md`.
5. Mascote (`assets/vulpes-mascot.png`) como elemento de identidade — **somente se o asset existir** (ver D-05).
6. Índice de navegação para os guias C e Python.

### 6.2 `docs/architecture.md`

**Documento normativo central (CA-04).** Deve conter, explicitamente:
1. O conceito da **Den Architecture** e sua inspiração (arquitetura em camadas + hexagonal).
2. As **cinco camadas**, com tabela Diretório / Conceito Vulpes / Camada Tradicional / Responsabilidade.
3. **Fluxo de dependências** (diagrama textual/mermaid) e a regra de unidirecionalidade "de fora para dentro".
4. **Regras de isolamento** por camada (o que `den/` não pode importar; `senses/`; `adapters/`; `vanguard/`; `kit/`).
5. **Nomenclatura Vulpes** (A Toca, O Faro, A Caça, A Vanguarda, A Ninhada) e sua correspondência com os termos tradicionais (Domain Core, Contracts & Validation, Infrastructure & I/O, Application Entry, Testing & QA).
6. **Papel específico de `vanguard/` como Application Entry**, em subseção dedicada: entrypoints, CLI, handlers de API, inicialização, ciclo de vida, `main`, orquestração; e a regra de que só dados validados chegam a `den/`.
7. Exemplos ilustrativos de fluxo (ex.: `vanguard → senses → den`).
8. **Sem camadas adicionais** sem justificativa técnica (restrição do prompt). Se alguma for cogitada, registrar decisão.

### 6.3 `docs/c-standard.md`

Conteúdo esperado:
- Versão C11+.
- Formatação: 4 espaços, tabs proibidos, Attach/K&R/OTBS, comprimento de linha (100, ver CF-01).
- Nomenclatura: funções/variáveis `snake_case`; macros/constantes `UPPER_SNAKE_CASE`; tipos com sufixo `_t`; ponteiros alinhados à variável (com exemplos).
- Segurança: proibição de `strcpy`/`sprintf`; preferência por `strncpy`/`snprintf`; obrigação de validação de limites na camada `senses/`.
- Gestão de memória (inicialização, liberação, uso de `sizeof`, verificação de retornos).
- Uso do `.clang-format`: como copiar de `configs/` para a raiz do projeto e formatar (`clang-format -i`).
- Exceção de tabs em `Makefile`.

### 6.4 `docs/python-standard.md`

Conteúdo esperado:
- Versão Python 3.10+.
- Ruff e Black (e a decisão D-03 sobre formatador padrão).
- Comprimento de linha 88.
- Type hints obrigatórios em 100% de argumentos e retornos, com exemplos.
- Tabela de nomenclatura (Módulos/Variáveis/Funções `snake_case`; Classes `PascalCase`; Constantes `UPPER_SNAKE_CASE`).
- Organização dos módulos segundo a Den Architecture.
- Como usar `configs/pyproject.toml` em um projeto consumidor.

---

## 7. MkDocs Plan

**Arquivo:** `mkdocs.yml` (raiz). **Tema:** Material for MkDocs.

**Configuração proposta (especificação para a Fase 4):**

| Chave | Valor | Observação |
|---|---|---|
| `site_name` | `Vulpes Code Standard` | |
| `site_description` | Descrição curta do padrão | |
| `site_url` | `https://<owner>.github.io/vulpes-code-standard/` | **Depende do owner** (OQ-02) |
| `repo_url` / `repo_name` | URL do repositório | **Depende do owner** (OQ-02) |
| `docs_dir` | `docs` | Padrão |
| `theme.name` | `material` | |
| `theme.language` | `pt` | Conteúdo em português |
| `theme.logo` / `theme.favicon` | `assets/vulpes-mascot.png` | Somente se o asset existir (D-05) |
| `theme.palette.scheme` | `slate` | Modo escuro nativo (PRD) |
| `theme.palette.primary` | `teal` | Destaque Teal |
| `theme.palette.accent` | `cyan` | Destaque Cyan |
| `theme.features` | `navigation.instant`, `navigation.top`, `search.suggest`, `content.code.copy` | Navegação/usabilidade |
| `nav` | Início / Arquitetura / Padrão C / Padrão Python | Conforme PRD §7 |
| `plugins` | `search` | Built-in; não requer dependência extra |
| `markdown_extensions` | `admonition`, `attr_list`, `md_in_html`, `tables`, `toc` (permalink), `pymdownx.details`, `pymdownx.superfences` (com fence `mermaid`), `pymdownx.highlight`, `pymdownx.tabbed` | Suficiente para os docs; `superfences` habilita diagramas de arquitetura |
| `strict` | Habilitado via CLI (`mkdocs build --strict`) | Não é chave do YAML |

**Modo claro/escuro:** o PRD exige "modo escuro nativo" e "paleta slate". Recomendação: definir `slate` como esquema padrão. Opcionalmente, adicionar um segundo bloco de paleta com `toggle` (light/dark) — **opcional**, registrado como decisão menor; não altera o requisito.

**Mecanismo de dependências (PRD §8 pede avaliação):**

| Opção | Prós | Contras | Recomendação |
|---|---|---|---|
| `requirements.txt` + pip | Simples, universal, funciona direto no CI | Sem lock determinístico | **Recomendado** |
| `requirements-dev.txt` | Separa docs de dev | O projeto não tem outras deps Python | Desnecessário (YAGNI) |
| `uv` + `uv.lock` | Rápido, lock determinístico | Ferramenta extra; CI mais complexo | Alternativa futura |

**Decisão técnica MK-01:** criar um único **`requirements.txt`** na raiz com `mkdocs-material` (que traz `mkdocs` e `pymdown-extensions`) e um pin de faixa maior, por exemplo:

```text
mkdocs-material>=9.5,<10
```

Justificativa: simplicidade, reprodutibilidade razoável, compatível com `pip` e com `actions/setup-python`. Evita adicionar `uv` sem necessidade. O arquivo não consta na árvore oficial do PRD, mas o próprio PRD §8 autoriza avaliá-lo; é adicionado por necessidade objetiva do CI.

**Validação:** `mkdocs build --strict` (falha em links quebrados, warnings e `site_url` ausente/inválido).

---

## 8. CI/CD Plan

**Arquivo:** `.github/workflows/deploy-docs.yml`.

**Decisão técnica CI-01 (ver D-02):** usar o fluxo oficial de GitHub Pages com artifacts, em vez de `mkdocs gh-deploy`.

**Especificação do workflow:**

```yaml
name: Deploy Docs
on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: pip
      - run: pip install -r requirements.txt
      - run: mkdocs build --strict
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: site

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

**Elementos e justificativas:**
- **Permissões:** `contents: read` (checkout), `pages: write` (publicar), `id-token: write` (OIDC exigido por `deploy-pages`).
- **Ambiente:** `github-pages` com `url` do deployment.
- **Artifacts:** `upload-pages-artifact` empacota `site/`; `deploy-pages` publica.
- **Dependências:** `requirements.txt` (MK-01).
- **Concorrência:** evita deploys simultâneos.
- **Versão de Python no CI:** `3.12` (estável e amplamente suportada; o projeto exige 3.10+, e o CI não precisa da versão mínima).
- **Requisito de configuração do repositório (manual):** Settings → Pages → Source = **GitHub Actions**. Sem isso, o deploy falha.
- **`mkdocs build --strict`** garante que o build falha em warnings (links quebrados, páginas faltantes), atendendo à validação da seção 10.

**Alternativa registrada:** `mkdocs gh-deploy --force` publicando na branch `gh-pages` (simples, mas legado e com histórico de commits no branch). Não recomendada.

**Validação do YAML:** `python -c "import yaml,sys; yaml.safe_load(open('.github/workflows/deploy-docs.yml'))"` (ou `actionlint`, se disponível). Validar a ação executando o workflow em um push de teste na `main` e verificando a aba Actions + URL do Pages.

---

## 9. Asset and License Plan

### 9.1 Mascote — `assets/vulpes-mascot.png`

**Estado:** **AUSENTE**. Confirmado por inspeção da árvore.

- **Não gerar nem inventar o asset** (regra 9 do prompt e PRD §10).
- **Local obrigatório:** `assets/vulpes-mascot.png`.
- **Propriedades a validar na entrega:**
  - Formato PNG válido (`file assets/vulpes-mascot.png` → `PNG image data`).
  - **Fundo transparente** (canal alpha) — exigência do PRD ("sem fundo").
  - Resolução mínima adequada a logo/cabeçalho (recomendado ≥ 512×512) e tamanho de arquivo razoável.
  - Nome exatamente `vulpes-mascot.png` (minúsculas, hífen).
- **Onde pode ser usado:**
  - `mkdocs.yml` → `theme.logo` e `theme.favicon` (favicon idealmente quadrado; PNG é aceito).
  - `docs/index.md` → elemento de identidade/hero.
  - `README.md` → logotipo no topo.

### 9.2 LICENSE — MIT

**Estado:** **AUSENTE**.

- **Ação:** criar `LICENSE` na raiz com o texto padrão da **MIT License**.
- **Conteúdo necessário:** cabeçalho `MIT License`, linha de copyright com `Copyright (c) <ANO> <TITULAR>` e o texto-padrão MIT.
- **Informação obrigatória do mantenedor:** **nome do titular de copyright** (pessoa ou organização) e **ano**. **Não inventar** (regra 8). Ver OQ-01.
- **Formato:** texto plano, `LICENSE` sem extensão (reconhecimento automático pelo GitHub).
- **Validação:** arquivo presente; primeira linha/estrutura compatível com MIT; GitHub detecta a licença como "MIT" na página do repositório.

---

## 10. Validation Strategy

Estratégia objetiva, executável e independente de decisões implícitas. Todas as validações usam caminhos explícitos por causa de D-01.

### 10.1 Estrutura

```bash
# Arquivos obrigatórios presentes
for f in \
  .github/workflows/deploy-docs.yml \
  configs/.editorconfig \
  configs/.clang-format \
  configs/pyproject.toml \
  docs/index.md \
  docs/architecture.md \
  docs/c-standard.md \
  docs/python-standard.md \
  mkdocs.yml \
  README.md \
  LICENSE \
  requirements.txt ; do
  test -f "$f" && echo "OK  $f" || echo "MISSING  $f"
done

# Asset (quando entregue)
test -f assets/vulpes-mascot.png && file assets/vulpes-mascot.png
```

### 10.2 Python

```bash
# TOML válido
python -c "import tomllib; tomllib.load(open('configs/pyproject.toml','rb')); print('pyproject OK')"

# Ruff reconhece a configuração (amostra temporária)
tmp=$(mktemp -d)
printf 'def f(a: int) -> int:\n    return a\n' > "$tmp/sample.py"
ruff check --config configs/pyproject.toml --no-cache "$tmp/sample.py"
ruff format --check --config configs/pyproject.toml "$tmp/sample.py"

# Black reconhece a configuração
black --config configs/pyproject.toml --check "$tmp/sample.py"

# Pytest reconhece a configuração (exit 0 ou 5 = sem testes)
pytest -c configs/pyproject.toml --collect-only; echo "pytest exit: $?"
```

### 10.3 C

```bash
# .clang-format parseável e com as opções esperadas
clang-format --style=file:configs/.clang-format --dump-config | grep -E 'IndentWidth|UseTab|BreakBeforeBraces|PointerAlignment|ColumnLimit'

# Formatação de amostra .c sem erros
tmp=$(mktemp -d)
cat > "$tmp/sample.c" <<'EOF'
#include <stdio.h>
typedef struct { int id; char *name; } den_user_session_t;
int main(void) { int x=1; return x; }
EOF
clang-format --style=file:configs/.clang-format "$tmp/sample.c" >/dev/null && echo "clang-format OK"

# Convenções de estilo (verificação textual da saída formatada)
clang-format --style=file:configs/.clang-format "$tmp/sample.c" | grep -q 'void \*ptr' || true
```

### 10.4 Documentação

```bash
# Build estrito (falha em warnings/links quebrados)
mkdocs build --strict

# Verificações de conteúdo exigidas por CA-04
grep -qi 'den/' docs/architecture.md
grep -qi 'senses/' docs/architecture.md
grep -qi 'adapters/' docs/architecture.md
grep -qi 'vanguard/' docs/architecture.md
grep -qi 'kit/' docs/architecture.md
grep -qi 'Application Entry' docs/architecture.md
grep -qi 'fluxo de depend' docs/architecture.md
```

### 10.5 CI

```bash
# YAML sintaticamente válido
python -c "import yaml; yaml.safe_load(open('.github/workflows/deploy-docs.yml')); print('workflow YAML OK')"

# (Opcional, se instalado) lint específico de workflows
actionlint .github/workflows/deploy-docs.yml || true
```

Validação funcional do CI (após push na `main`): workflow conclui com sucesso; artifact `github-pages` gerado; deploy publicado; URL do Pages acessível.

### 10.6 EditorConfig

```bash
# Se disponível
editorconfig-checker -config configs/.editorconfig README.md docs/*.md
# Alternativa sem dependência: verificar chaves obrigatórias
grep -q 'end_of_line = lf' configs/.editorconfig
grep -q 'insert_final_newline = true' configs/.editorconfig
grep -q 'indent_size = 4' configs/.editorconfig
```

---

## 11. Implementation Phases

### Phase 0 — Discovery

- **Objetivo:** confirmar o estado do repositório e as decisões pendentes antes de criar arquivos.
- **Arquivos envolvidos:** `PRD-code-standard.md` (leitura), `IMPLEMENTATION_PLAN.md` (este documento).
- **Operações:** inspeção da árvore; verificação de Git; verificação de toolchain; resolução das Open Questions bloqueantes (OQ-01, OQ-02, OQ-04).
- **Dependências:** nenhuma.
- **Decisões técnicas:** A-01 (camadas como documentação); D-01 (configs como templates).
- **Riscos:** decisões humanas não respondidas bloqueiam fases seguintes.
- **Validações:** `git status` (ou inicialização de repositório), toolchain acessível.
- **Critério de conclusão:** estado atual confirmado e OQs bloqueantes respondidas (ou explicitamente adiadas).

### Phase 1 — Repository Structure

- **Objetivo:** criar a árvore de diretórios e os arquivos de identidade legal.
- **Arquivos envolvidos:** `configs/`, `docs/`, `assets/`, `.github/workflows/` (diretórios); `LICENSE`.
- **Operações:** criar diretórios; criar `LICENSE` MIT **com titular e ano fornecidos**; posicionar `assets/vulpes-mascot.png` **quando entregue**; inicializar Git e `.gitignore` (opcional, ver OQ-07).
- **Dependências:** Fase 0; OQ-01 (copyright) para LICENSE; asset humano para mascote.
- **Decisões técnicas:** `.gitignore` mínimo (`site/`, `__pycache__/`, `.ruff_cache/`, `.pytest_cache/`) — opcional.
- **Riscos:** LICENSE bloqueada sem titular; mascote ausente.
- **Validações:** comando da seção 10.1.
- **Critério de conclusão:** diretórios existem; `LICENSE` criada; asset presente ou formalmente pendente.

### Phase 2 — Tooling Configuration

- **Objetivo:** materializar as três configurações de ferramentas.
- **Arquivos envolvidos:** `configs/.editorconfig`, `configs/.clang-format`, `configs/pyproject.toml`.
- **Operações:** escrever os arquivos conforme seção 5; garantir `root = true` no EditorConfig; usar `BasedOnStyle: LLVM` e opções da tabela 5.2; usar `[tool.ruff]`, `[tool.black]`, `[tool.pytest.ini_options]`.
- **Dependências:** Fase 1.
- **Decisões técnicas:** EC-01, CF-01, CF-02, PY-01, D-03.
- **Riscos:** opções de clang-format inexistentes na versão local; ANN muito estrito; `testpaths = ["kit"]` sem diretório.
- **Validações:** seções 10.2, 10.3 e 10.6.
- **Critério de conclusão:** TOML válido; clang-format parseável; Ruff/Black/Pytest reconhecem; EditorConfig com chaves obrigatórias.

### Phase 3 — Documentation

- **Objetivo:** escrever a documentação normativa.
- **Arquivos envolvidos:** `docs/index.md`, `docs/architecture.md`, `docs/c-standard.md`, `docs/python-standard.md`, `README.md`.
- **Operações:** redigir conforme seção 6; garantir que `architecture.md` cubra os 6 pontos de CA-04, com destaque para `vanguard/` como Application Entry; criar `README.md` como porta de entrada (o que é, objetivo, Den Architecture, estrutura, uso das configs, documentação, licença).
- **Dependências:** Fases 1–2 (referencia as configs).
- **Decisões técnicas:** A-01 (camadas documentadas); nomenclatura e segurança C como regras documentais; D-03.
- **Riscos:** omissão de um dos pontos de CA-04; inconsistência entre README e docs.
- **Validações:** seção 10.4 (greps de conteúdo).
- **Critério de conclusão:** quatro páginas presentes e completas; README criado; CA-04 satisfeito.

### Phase 4 — MkDocs

- **Objetivo:** configurar o site e suas dependências.
- **Arquivos envolvidos:** `mkdocs.yml`, `requirements.txt`.
- **Operações:** escrever `mkdocs.yml` conforme seção 7; criar `requirements.txt` (MK-01); referenciar a mascote apenas se o asset existir.
- **Dependências:** Fase 3 (páginas de `docs/`); OQ-02 (URLs); asset (opcional).
- **Decisões técnicas:** MK-01; palette slate/teal/cyan; `pymdownx.superfences`.
- **Riscos:** `site_url`/`repo_url` incorretos; `--strict` falhando por link ausente; mascote ausente referenciada no tema.
- **Validações:** `mkdocs build --strict`.
- **Critério de conclusão:** build estrito sem erros; `site/` gerado.

### Phase 5 — CI/CD

- **Objetivo:** automatizar o deploy no GitHub Pages.
- **Arquivos envolvidos:** `.github/workflows/deploy-docs.yml`.
- **Operações:** escrever o workflow conforme seção 8; **configurar manualmente** Settings → Pages → Source = GitHub Actions; realizar push de teste na `main`.
- **Dependências:** Fase 4 (`requirements.txt`, build); repositório remoto (OQ-02).
- **Decisões técnicas:** CI-01, D-02.
- **Riscos:** Pages não configurado; permissões insuficientes; falha no `--strict`.
- **Validações:** seção 10.5; execução real do workflow; URL do Pages acessível.
- **Critério de conclusão:** workflow verde; site publicado; URL responde 200.

### Phase 6 — Validation

- **Objetivo:** verificação final integrada e revisão do repositório.
- **Arquivos envolvidos:** todos.
- **Operações:** executar integralmente a seção 10; revisar a árvore contra o PRD §3; revisar `docs/architecture.md` contra CA-04; confirmar README/LICENSE/asset; confirmar deploy.
- **Dependências:** Fases 1–5.
- **Decisões técnicas:** nenhuma nova.
- **Riscos:** regressões de configuração; link quebrado; asset/copyright ausentes.
- **Validações:** toda a seção 10 + checklist da seção 16.
- **Critério de conclusão:** todos os Critérios de Aceitação (seção 13) verificados.

---

## 12. File Change Matrix

| File | Action | Purpose | Dependencies |
|---|---|---|---|
| `PRD-code-standard.md` | Preserve | Fonte de requisitos; não alterar | — |
| `IMPLEMENTATION_PLAN.md` | Create (this artifact) | Plano executável | PRD |
| `LICENSE` | Create | Licença MIT | OQ-01 (titular/ano) |
| `README.md` | Create | Porta de entrada do projeto | Docs, configs |
| `mkdocs.yml` | Create | Site Material (slate/teal/cyan) | `docs/`, asset (opcional), OQ-02 |
| `requirements.txt` | Create | Dependências de documentação (MK-01) | — |
| `configs/.editorconfig` | Create | Regras de edição (LF, indent 4, etc.) | — |
| `configs/.clang-format` | Create | Formatação C (LLVM + ajustes) | CF-01, CF-02 |
| `configs/pyproject.toml` | Create | Ruff + Black + Pytest | PY-01 |
| `docs/index.md` | Create | Visão geral | — |
| `docs/architecture.md` | Create | Den Architecture (CA-04) | — |
| `docs/c-standard.md` | Create | Guia de estilo C | `.clang-format` |
| `docs/python-standard.md` | Create | Guia de estilo Python | `pyproject.toml` |
| `.github/workflows/deploy-docs.yml` | Create | CI/CD GitHub Pages | Fase 4, Pages settings |
| `assets/vulpes-mascot.png` | Create (human-provided) | Identidade visual | Asset externo |
| `.gitignore` | Create (optional) | Ignorar `site/`, caches | OQ-07 |
| `kit/smoke/test_config.py` | Create (optional) | Alvo real para Pytest (D-04) | OQ-05 |

---

## 13. Acceptance Criteria

Cada critério com ID, requisito, método de validação e resultado esperado.

**CA-01 — Repositório Estruturado**
- Requisito: todos os arquivos/diretórios da árvore oficial presentes no commit principal.
- Validação: comando de verificação da seção 10.1 + `git ls-files`.
- Resultado esperado: os 12 itens obrigatórios presentes (exceto asset, se formalmente pendente por OQ-01/OQ-02/asset).

**CA-02 — Deploy Automático Operacional**
- Requisito: pipeline executa sem erros, compila e publica no GitHub Pages.
- Validação: execução real do workflow em push na `main`; inspeção do artifact e da URL do Pages.
- Resultado esperado: workflow verde; site acessível (HTTP 200); conteúdo igual ao build local.

**CA-03 — Validação dos Linters**
- Requisito: `.clang-format` válido e reconhecido por IDEs; `pyproject.toml` válido e reconhecido por Ruff/Black/Pytest.
- Validação: seções 10.2, 10.3 e 10.6.
- Resultado esperado: `clang-format --dump-config` sem erro; formatação de amostra `.c` sem erro; TOML parseável; `ruff check`/`ruff format --check`/`black --check` reconhecem a config; `pytest --collect-only` aceita a config (exit 0 ou 5).

**CA-04 — Isolamento Arquitetural Documentado**
- Requisito: `docs/architecture.md` explicita as cinco camadas, o fluxo de dependência, as regras de isolamento, a correspondência de vocabulário e o papel de `vanguard/` como Application Entry.
- Validação: greps de conteúdo da seção 10.4 + revisão manual.
- Resultado esperado: todos os seis pontos presentes e corretos.

**CA-05 — Documentação Publicável**
- Requisito: site compila com o tema Material, paleta slate, destaques cyan/teal, navegação exigida.
- Validação: `mkdocs build --strict`; inspeção do `site/` gerado.
- Resultado esperado: build sem warnings; nav com as quatro entradas.

**CA-06 — EditorConfig Conforme**
- Requisito: `end_of_line = lf`, `insert_final_newline = true`, `indent_size = 4` presentes e regras por tipo de arquivo.
- Validação: seção 10.6.
- Resultado esperado: chaves obrigatórias presentes; regras específicas por extensão.

**CA-07 — Identidade e Licença**
- Requisito: `assets/vulpes-mascot.png` presente com fundo transparente; `LICENSE` MIT com titular válido.
- Validação: `file` no PNG + inspeção de transparência; verificação do texto MIT e do titular.
- Resultado esperado: PNG válido com alpha; LICENSE MIT completa. (Bloqueado por OQ-01 e asset.)

---

## 14. Risks and Mitigations

| ID | Risco | Probabilidade | Impacto | Mitigação |
|---|---|---|---|---|
| R-01 | Configs em `configs/` não são descobertas automaticamente | Alta | Alto | D-01: validar com `--config` explícito; documentar cópia para a raiz do consumidor |
| R-02 | Titular de copyright da LICENSE desconhecido | Alta | Médio | OQ-01: obter do mantenedor antes de criar a LICENSE |
| R-03 | Mascote ausente | Alta | Médio | D-05: não referenciar no tema/docs até existir; validar ao entregar |
| R-04 | `mkdocs build --strict` falha por `site_url`/links | Média | Alto | OQ-02: definir URL; revisar links relativos; rodar build local antes do push |
| R-05 | GitHub Pages não configurado como "GitHub Actions" | Média | Alto | Passo manual na Fase 5; documentar em README/plano |
| R-06 | Versão de clang-format incompatível com `Standard: c11` | Baixa | Médio | CF-02: exigir clang-format ≥ 16 ou omitir a opção |
| R-07 | Pytest sem testes retorna exit 5 | Alta | Baixo | D-04: aceitar exit 5 ou criar smoke test (OQ-05) |
| R-08 | Conflito Ruff format vs. Black | Média | Médio | D-03: configurações idênticas; eleger um formatador padrão |
| R-09 | Dependências de docs sem pin determinístico | Média | Baixo | MK-01: faixa de versão; opção futura `uv.lock` |
| R-10 | Regras de nomenclatura/segurança C não automatizadas | Alta | Médio | Documentar em `c-standard.md`; opcionalmente linters/CI (OQ-06) |
| R-11 | Repositório sem Git / nome do diretório com typo | Média | Baixo | Inicializar Git; nomear o remoto como `vulpes-code-standard` |

---

## 15. Open Questions

**OQ-01 — Titular e ano do copyright (LICENSE).** Qual o nome da pessoa/organização detentora do copyright e o ano? **Bloqueia:** criação da LICENSE (CA-07). **Recomendação:** fornecer antes da Fase 1.

**OQ-02 — Owner/organização e URL do repositório.** Qual o owner GitHub e o nome do repositório remoto? Necessário para `site_url`, `repo_url` e para o Pages. **Bloqueia:** `mkdocs.yml` final e deploy (CA-02). **Recomendação:** `vulpes-code-standard`.

**OQ-03 — Comprimento de linha em C.** 100 (recomendado), 80 ou 120? **Impacta:** `.clang-format` e `docs/c-standard.md`.

**OQ-04 — Estratégia de configuração (D-01).** Manter apenas templates em `configs/` (recomendado) ou também cópias/symlinks na raiz do repositório? **Impacta:** descoberta automática pelas ferramentas.

**OQ-05 — Criar `kit/` com smoke test (D-04).** Adicionar `kit/smoke/test_config.py` para dar ao Pytest um alvo real, ou aceitar exit 5? **Impacta:** validação do Pytest e materialização parcial da Den Architecture.

**OQ-06 — Automação das regras de nomenclatura/segurança em C.** O PRD exige `snake_case`/`UPPER_SNAKE_CASE`/sufixo `_t` e proíbe `strcpy`/`sprintf`, mas clang-format não cobre isso. Adicionar verificação textual no CI (ex.: `grep`) ou apenas documentar? **Impacta:** escopo do CI e da validação.

**OQ-07 — `.gitignore`.** Incluir um `.gitignore` mínimo (`site/`, caches)? Não consta no PRD, mas é útil. **Impacta:** higiene do repositório.

**OQ-08 — Mascote.** O asset será fornecido? Em caso afirmativo, quando? **Bloqueia:** uso no tema/docs e CA-07.

**OQ-09 — Nome do diretório local.** Corrigir `Vulpes-code-stardard` → `Vulpes-code-standard`? Não afeta arquivos versionados. **Impacta:** apenas a organização local.

---

## 16. Final Implementation Checklist

Checklist derivado da "Definição de Pronto" do PRD (seção 11) + critérios de aceitação deste plano.

**Estrutura e identidade**
- [ ] Estrutura oficial do repositório criada (`configs/`, `docs/`, `assets/`, `.github/workflows/`)
- [ ] `LICENSE` MIT criada com titular/ano reais (OQ-01)
- [ ] `assets/vulpes-mascot.png` presente, PNG com fundo transparente (OQ-08)
- [ ] `README.md` criado como porta de entrada

**Configurações**
- [ ] `configs/.editorconfig` com LF, final newline, indent 4 e regras por tipo
- [ ] `configs/.clang-format` válido (LLVM, 4 espaços, no tabs, Attach, ponteiro à direita)
- [ ] `configs/pyproject.toml` válido com Ruff, Black e Pytest
- [ ] Ruff valida o `pyproject.toml`
- [ ] Black reconhece a configuração
- [ ] Pytest reconhece a configuração
- [ ] `.clang-format` formata código C corretamente

**Documentação**
- [ ] `docs/index.md` completo
- [ ] `docs/architecture.md` cobre as cinco camadas, fluxo, isolamento, vocabulário e `vanguard/` (CA-04)
- [ ] `docs/c-standard.md` completo (formatação, nomenclatura, segurança, memória)
- [ ] `docs/python-standard.md` completo (Ruff, Black, type hints, nomenclatura)

**Site e automação**
- [ ] `mkdocs.yml` configurado (Material, slate, teal/cyan, nav)
- [ ] `requirements.txt` criado
- [ ] `mkdocs build --strict` passa
- [ ] Tema Material operacional
- [ ] `.github/workflows/deploy-docs.yml` funcional
- [ ] Pages configurado como "GitHub Actions"
- [ ] GitHub Pages publicado e acessível

**Validação final**
- [ ] Seção 10 executada integralmente
- [ ] Revisão final do repositório concluída
- [ ] Todos os Critérios de Aceitação (seção 13) verificados

---

## Anexo A — Rastreabilidade PRD → Plano

| Requisito PRD | Onde no plano |
|---|---|
| §1 Objetivos | Seções 1, 6.1, 12 |
| §2 Den Architecture | Seção 4 |
| §2.1 Isolamento | Seções 4.2, 6.2 |
| §3 Estrutura de arquivos | Seções 2.1, 11, 12 |
| §4.1 C | Seções 5.2, 6.3 |
| §4.2 Python | Seções 5.3, 6.4 |
| §5/E1 Configurações | Seção 5 |
| §6/E2 Documentação | Seção 6 |
| §7 MkDocs | Seção 7 |
| §8/E3 GitHub Actions | Seção 8 |
| §9 Roadmap | Seção 11 (fases 0–6) |
| §10 CA-01..CA-04 | Seção 13 (CA-01..CA-07) |
| §11 Definição de Pronto | Seção 16 |
| §12 Resultado Esperado | Seções 1, 4, 6 |
