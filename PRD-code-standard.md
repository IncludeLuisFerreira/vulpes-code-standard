# PRD — Implementação do Vulpes Code Standard v1.1.0

## 1. Visão Geral e Objetivos

O **Vulpes Code Standard** visa resolver a falta de consistência arquitetural e estilística em bases de código em **C** e **Python**.

A solução adota a **Den Architecture**, um padrão conceitual de software inspirado em arquitetura em camadas e arquitetura hexagonal, traduzido para um vocabulário técnico e maduro.

### Objetivos Principais

* **Padronização Estilística:** fornecer ficheiros de configuração prontos a usar (`.editorconfig`, `.clang-format`, `pyproject.toml`).
* **Arquitetura Unificada:** impor a estrutura em camadas da Den Architecture nos repositórios da equipa.
* **Documentação Automatizada:** publicar um site dinâmico e responsivo via GitHub Pages usando MkDocs.
* **Verificação e Adoção:** garantir validação automática por CI/CD e suporte para automação em IDEs.

---

# 2. Especificação Arquitetural: Den Architecture

A **Den Architecture** organiza o software em **5 camadas estritas**.

O fluxo de dependência é **estritamente unidirecional: de fora para dentro**.

| Diretoria   | Conceito Vulpes | Camada de Software     | Responsabilidade Técnica                                                                        |
| ----------- | --------------- | ---------------------- | ----------------------------------------------------------------------------------------------- |
| `den/`      | **A Toca**      | Domain Core            | Regras de negócio puras, estruturas de dados centrais e algoritmos. Zero dependências externas. |
| `senses/`   | **O Faro**      | Contracts & Validation | Sanitização de dados, validação de limites de buffer, esquemas, autorização e telemetria/logs.  |
| `adapters/` | **A Caça**      | Infrastructure & I/O   | Comunicação com bases de dados, APIs de terceiros, drivers de hardware e sistema de ficheiros.  |
| `vanguard/` | **A Vanguarda** | Application Entry      | Pontos de entrada, interfaces CLI, handlers de API, inicialização e ciclo de vida (`main`).     |
| `kit/`      | **A Ninhada**   | Testing & QA           | Suíte de testes unitários e de integração, mocks, fixtures e testes de carga.                   |

## 2.1. Regras de Isolamento

### `den/` — A Toca

A camada `den/` representa o núcleo do domínio e deve permanecer completamente isolada de detalhes externos.

**Não pode importar:**

* `senses/`
* `adapters/`
* `vanguard/`

Também não deve possuir dependências externas desnecessárias.

### `senses/` — O Faro

Responsável por validar, sanitizar e preparar os dados antes que estes alcancem o domínio.

Exemplos:

* Validação de entrada;
* Limites de buffers;
* Validação de schemas;
* Autorização;
* Telemetria;
* Logging;
* Normalização de dados.

### `adapters/` — A Caça

Todo código que interage diretamente com sistemas externos deve residir exclusivamente em `adapters/`.

Exemplos:

* Sistema de ficheiros;
* Bases de dados;
* APIs externas;
* Drivers de hardware;
* Sockets;
* Serviços externos.

### `vanguard/` — A Vanguarda

A camada `vanguard/` funciona como ponto de entrada e orquestração da aplicação.

É responsável por:

* Inicialização da aplicação;
* Interfaces CLI;
* Handlers de API;
* Ciclo de vida;
* Função `main`;
* Orquestração entre `senses/`, `adapters/` e `den/`.

A `vanguard/` deve enviar para `den/` apenas dados devidamente tratados e validados.

### `kit/` — A Ninhada

Contém os mecanismos de qualidade e validação do projeto.

Inclui:

* Testes unitários;
* Testes de integração;
* Mocks;
* Fixtures;
* Testes de carga.

---

# 3. Estrutura Padrão de Ficheiros do Repositório

```text
vulpes-code-standard/
│
├── .github/
│   └── workflows/
│       └── deploy-docs.yml      # Pipeline CI/CD para GitHub Pages
│
├── assets/
│   └── vulpes-mascot.png        # Mascote oficial sem fundo
│
├── configs/
│   ├── .editorconfig            # Regras gerais de espaçamento
│   ├── .clang-format            # Regras de formatação C
│   └── pyproject.toml           # Regras de linter/formatter Python
│
├── docs/
│   ├── index.md                 # Visão geral do padrão
│   ├── architecture.md          # Especificação da Den Architecture
│   ├── c-standard.md            # Guia de estilo C
│   ├── memory-management.md     # Política de gerenciamento de memória
│   └── python-standard.md       # Guia de estilo Python
│
├── mkdocs.yml                   # Configuração do site de documentação
├── README.md                    # Capa do repositório
└── LICENSE                      # Licença MIT
```

---

# 4. Convenções e Requisitos Técnicos

## 4.1. Linguagem C

### Versão

* **C11 ou superior**

### Formatação

* Indentação: **4 espaços**
* Uso de tabulações: **proibido**
* Estilo de chaves: **Attach / K&R / OTBS**
* Comprimento de linha: conforme configuração do `.clang-format`

### Nomenclatura

#### Funções e variáveis

Utilizar `snake_case`.

```c
vanguard_init_server();
```

#### Constantes e macros

Utilizar `UPPER_SNAKE_CASE`.

```c
#define DEN_MAX_BUFFER_SIZE 4096
```

#### Tipos e structs

Utilizar o sufixo `_t`.

```c
typedef struct {
    int id;
    char name[64];
} den_user_session_t;
```

#### Ponteiros

O asterisco deve ficar alinhado à variável.

```c
void *ptr;
char *buffer;
```

### Segurança

É proibida a utilização de funções consideradas inseguras ou propensas a overflow de buffer.

#### Proibido

```c
strcpy();
sprintf();
```

#### Preferencial

```c
strncpy();
snprintf();
```

O uso de funções com limite de memória deve ser acompanhado de validação adequada na camada `senses/`.

---

# 4.2. Linguagem Python

### Versão

* **Python 3.10 ou superior**

### Linter e Formatter

O projeto deve ser compatível com:

* **Ruff**
* **Black**

### Comprimento de Linha

O padrão de comprimento de linha é de **88 caracteres**, alinhado ao padrão Black.

### Type Hinting

Type hints são **obrigatórios em 100% dos argumentos e retornos de funções**, em todas as camadas.

Exemplo:

```python
def calculate_buffer_size(data: bytes) -> int:
    ...
```

### Nomenclatura

| Elemento   | Convenção          | Exemplo              |
| ---------- | ------------------ | -------------------- |
| Módulos    | `snake_case`       | `user_service.py`    |
| Variáveis  | `snake_case`       | `buffer_size`        |
| Funções    | `snake_case`       | `validate_request()` |
| Classes    | `PascalCase`       | `UserSession`        |
| Constantes | `UPPER_SNAKE_CASE` | `MAX_BUFFER_SIZE`    |

---

# 4.3. Gerenciamento de Memória (Arena Allocation)

O padrão adota **preferência por alocação baseada em lifetime**: objetos que
possuem o mesmo lifetime devem, quando possível, compartilhar um contexto de
memória (uma **arena**).

```text
ALLOCATION LIFETIME ≈ ARENA LIFETIME
```

Arena allocation é **preferencial** quando o lifetime dos objetos pode ser
agrupado. `malloc`, `calloc`, `realloc` e `free` **não** são proibidos: continuam
permitidos quando forem semanticamente apropriados.

### Preferred

Arena allocators para: request-scoped data, parser state, ASTs, estruturas
temporárias, batch processing, command execution contexts, contextos HTTP/API,
grafos de vida curta, buffers temporários e objetos com lifetime comum.

### Acceptable

`malloc`/`calloc`/`realloc`/`free` para: objetos de lifetime independente,
estruturas que sobrevivem à arena, memória compartilhada entre subsistemas,
recursos cujo ownership exige liberação individual, estruturas que
crescem/reduzem independentemente e integração com APIs externas que exigem
allocator específico.

### Discouraged

Evitar, sem justificativa, `malloc`/`calloc`/`free` repetidos em loops ou
caminhos de alta frequência quando as alocações possuem lifetime comum.

### Ownership

1. Toda arena deve possuir owner explícito.
2. O owner define o lifetime da arena.
3. Allocations pertencem à arena.
4. Memória pertencente à arena não deve receber `free()` individual.
5. O owner executa `reset`/`destroy`.
6. Ponteiros não podem sobreviver ao lifetime da arena.
7. Não retornar ponteiros para arena destruída antes do uso.
8. Memória request-scoped não deve virar referência de longa duração.
9. Arena não pode ser usada para mascarar ownership indefinido.

### Segurança

Toda implementação de arena deve tratar: `size_t` overflow, integer overflow,
multiplication overflow, alignment, padding, alignment overflow, OOM, `NULL`,
exhaustion, invalid lifetime, use-after-reset, dangling pointers e double
destruction. É explicitamente rejeitada uma implementação equivalente a
`arena->used += size;` sem validação de overflow e bounds.

### Quando arena não é adequada

Globals, singletons, caches de longa duração, lifetimes independentes, memória
que exige `free` individual, objetos de lifetime muito variável, objetos
compartilhados entre threads com lifetime independente, APIs externas com
ownership específico e recursos com destrutores próprios.

> A especificação completa desta política está em `docs/memory-management.md`.

---

# 5. Especificação de Entregáveis

## E1. Arquivos de Configuração

Diretório:

```text
configs/
```

### `.editorconfig`

Deve definir regras gerais de edição, incluindo:

```ini
end_of_line = lf
insert_final_newline = true
indent_size = 4
```

### `.clang-format`

Deve conter a especificação visual para C baseada no estilo **LLVM**, com ajustes específicos para:

* Indentação;
* Alinhamento;
* Chaves;
* Ponteiros;
* Comprimento de linha;
* Formatação de declarações.

### `pyproject.toml`

Deve centralizar as configurações utilizadas pelo ecossistema Python, incluindo:

* Ruff;
* Black;
* Pytest.

---

# 6. Documentação e Interface

## E2. Documentação e Site

A documentação será mantida no diretório:

```text
docs/
```

### Páginas obrigatórias

#### `docs/index.md`

Deve apresentar:

* O Vulpes Code Standard;
* Objetivos do projeto;
* Filosofia geral;
* Visão geral da Den Architecture.

#### `docs/architecture.md`

Deve documentar:

* As cinco camadas;
* Responsabilidades de cada camada;
* Fluxo de dependências;
* Regras de isolamento;
* Convenção de nomenclatura Vulpes;
* Correspondência entre os nomes tradicionais e a Den Architecture.

Em particular, deve explicitar a utilização de `vanguard/` como camada de **Application Entry**.

#### `docs/c-standard.md`

Deve documentar:

* Padrões de C;
* Convenções de nomenclatura;
* Formatação;
* Segurança;
* Resumo da política de memória, com link para `docs/memory-management.md`;
* Uso do `.clang-format`.

#### `docs/memory-management.md`

Deve ser a referência normativa completa da política de gerenciamento de memória:

* Princípio de lifetime (`ALLOCATION LIFETIME ≈ ARENA LIFETIME`);
* Arena allocation;
* Preferred, Acceptable e Discouraged;
* Ownership;
* Safety requirements;
* Casos em que arena não é adequada;
* Recursos externos e destrutores;
* Threading e reallocation;
* Integração com a Den Architecture;
* Estratégia de migração;
* Exemplos e anti-patterns.

#### `docs/python-standard.md`

Deve documentar:

* Convenções Python;
* Ruff;
* Black;
* Type hints;
* Nomenclatura;
* Organização dos módulos.

---

# 7. MkDocs

O site de documentação deve utilizar **Material for MkDocs**.

O ficheiro principal será:

```text
mkdocs.yml
```

## Tema

O site deve utilizar:

* **Material for MkDocs**
* Modo escuro nativo;
* Paleta `slate`;
* Destaques em **Cyan/Teal**;
* Layout responsivo.

A navegação deve expor, no mínimo:

```yaml
nav:
  - Início: index.md
  - Arquitetura: architecture.md
  - Padrão C: c-standard.md
  - Padrão Python: python-standard.md
```

---

# 8. Automação CI/CD

## E3. GitHub Actions

O workflow será definido em:

```text
.github/workflows/deploy-docs.yml
```

## Comportamento

O workflow deve:

1. Ser executado em `push` para a branch `main`;
2. Fazer checkout do repositório;
3. Configurar o ambiente Python;
4. Instalar MkDocs e Material for MkDocs;
5. Compilar a documentação;
6. Publicar o site no GitHub Pages.

### Trigger

```yaml
on:
  push:
    branches:
      - main
```

O deploy deve ser automatizado sem necessidade de intervenção manual após um `push` válido na branch `main`.

---

# 9. Roadmap de Execução

| Fase       | Descrição                     | Entregáveis                                                        | Estimativa |
| ---------- | ----------------------------- | ------------------------------------------------------------------ | ---------- |
| **Fase 1** | Setup do Repositório e Assets | Criação das pastas, commit do mascote e ficheiro `LICENSE`         | Dia 1      |
| **Fase 2** | Criação das Configurações     | `.editorconfig`, `.clang-format` e `pyproject.toml`                | Dia 1      |
| **Fase 3** | Redação do Conteúdo           | Documentação de C, Python e Den Architecture                       | Dia 2      |
| **Fase 4** | Automação e Site              | `mkdocs.yml` e workflow do GitHub Actions funcional                | Dia 2      |
| **Fase 5** | Validação                     | Revisão de código, teste do site e abertura oficial do repositório | Dia 3      |

---

# 10. Critérios de Aceitação

## CA-01 — Repositório Estruturado

Todos os ficheiros e diretórios definidos na árvore do repositório devem estar presentes no commit principal.

A estrutura mínima esperada é:

```text
.github/workflows/deploy-docs.yml
assets/vulpes-mascot.png
configs/.editorconfig
configs/.clang-format
configs/pyproject.toml
docs/index.md
docs/architecture.md
docs/c-standard.md
docs/memory-management.md
docs/python-standard.md
mkdocs.yml
README.md
LICENSE
```

---

## CA-02 — Deploy Automático Operacional

O pipeline do GitHub Actions deve:

* Executar sem erros;
* Compilar a documentação;
* Publicar o site;
* Disponibilizar o conteúdo através do GitHub Pages.

---

## CA-03 — Validação dos Linters

### C

O ficheiro `.clang-format` deve:

* Ser válido;
* Formatar ficheiros `.c` sem erros;
* Ser reconhecido por IDEs como VS Code e CLion.

### Python

O `pyproject.toml` deve:

* Ser válido;
* Ser reconhecido pelo Ruff;
* Ser reconhecido pelo Black;
* Conter a configuração do Pytest.

---

## CA-04 — Isolamento Arquitetural

O ficheiro:

```text
docs/architecture.md
```

deve explicitar claramente:

* As cinco camadas da Den Architecture;
* O fluxo de dependência;
* As regras de isolamento;
* A correspondência entre os nomes tradicionais e o vocabulário Vulpes;
* A função da camada `vanguard/` como **Application Entry**.

---

## CA-05 — Política de Memória Documentada

O ficheiro:

```text
docs/memory-management.md
```

deve ser a referência normativa completa da política de memória, cobrindo:

* o princípio de lifetime (`ALLOCATION LIFETIME ≈ ARENA LIFETIME`);
* arena como mecanismo preferencial;
* Preferred, Acceptable e Discouraged;
* a relação com a Den Architecture;
* a estratégia de migração.

O ficheiro `docs/c-standard.md` deve conter um resumo da política e um link para
a documentação canônica.

---

## CA-06 — Ownership e Casos Não Adequados

A documentação da política de memória deve explicitar:

* as regras de ownership (owner explícito, reset/destroy, proibição de `free()`
  individual em memória da arena, proibição de ponteiros sobreviventes);
* os requisitos de segurança (overflow, alignment, OOM, use-after-reset,
  dangling pointers, double destruction);
* os casos em que arena **não** é adequada;
* o tratamento de recursos externos e destrutores.

---

## CA-07 — Exemplos de Alocação

A documentação deve apresentar exemplos conceituais de:

* alocação desencorajada (alocações independentes com o mesmo lifetime);
* alocação preferencial (arena com lifetime agrupado);
* alocação aceitável (lifetime independente).

Os exemplos são conceituais: nenhuma arena allocator deve ser implementada no
repositório do Standard.

---

# 11. Definição de Pronto

A implementação do **Vulpes Code Standard v1.1.0** será considerada concluída quando:

* [ ] A estrutura oficial do repositório estiver criada;
* [ ] Os ficheiros de configuração estiverem implementados;
* [ ] A documentação das cinco camadas estiver completa;
* [ ] A documentação de C estiver completa;
* [ ] A documentação de Python estiver completa;
* [ ] A política de gerenciamento de memória estiver documentada;
* [ ] As regras de ownership e os casos em que arena não é adequada estiverem documentados;
* [ ] A integração da política de memória com a Den Architecture estiver documentada;
* [ ] O `mkdocs.yml` estiver configurado;
* [ ] O tema Material estiver operacional;
* [ ] O workflow de GitHub Actions estiver funcional;
* [ ] O GitHub Pages estiver publicado;
* [ ] Ruff validar o `pyproject.toml`;
* [ ] Black reconhecer a configuração;
* [ ] Pytest reconhecer a configuração;
* [ ] `.clang-format` formatar código C corretamente;
* [ ] As regras de isolamento arquitetural estiverem documentadas;
* [ ] A revisão final do repositório estiver concluída.

---

# 12. Resultado Esperado

Ao final da implementação, o repositório **Vulpes Code Standard** deverá funcionar como a fonte oficial de referência para:

1. **Estilo de código** em C e Python;
2. **Organização arquitetural** baseada na Den Architecture;
3. **Configuração de ferramentas de desenvolvimento**;
4. **Validação automática de qualidade**;
5. **Documentação técnica centralizada**;
6. **Integração com IDEs e pipelines CI/CD**;
7. **Política de gerenciamento de memória baseada em lifetime e arena allocation**.

A versão **v1.1.0** estabelece, portanto, o contrato técnico para a criação e manutenção de projetos da equipa sob o padrão Vulpes.
