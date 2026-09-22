# Memory Management

> Política canônica de gerenciamento de memória do **Vulpes Code Standard v1.1.0**.
> Esta é a referência normativa completa. O resumo está em
> [Padrão C](c-standard.md) e a integração arquitetural em
> [Den Architecture](architecture.md).

Este documento usa linguagem normativa:

| Termo | Significado |
| ----- | ----------- |
| **MUST** / **DEVE** | Requisito obrigatório |
| **MUST NOT** / **NÃO DEVE** | Proibição obrigatória |
| **SHOULD** / **DEVERIA** | Recomendação forte; desvios exigem justificativa |
| **SHOULD NOT** / **NÃO DEVERIA** | Desaconselhado; desvios exigem justificativa |
| **MAY** / **PODE** | Opcional |

> **Esta política não proíbe `malloc`, `calloc`, `realloc` ou `free`.** Ela
> estabelece quando a **arena allocation** é preferencial e como usá-la com
> ownership e lifetime explícitos.

---

## 1. Princípio de Lifetime

**Objetos que possuem o mesmo lifetime devem, quando possível, compartilhar um
contexto de memória.**

*Um contexto de memória com lifetime comum é chamado, nesta política, de
**arena**.*

A relação central é:

```text
ALLOCATION LIFETIME ≈ ARENA LIFETIME
```

Uma alocação **DEVE** (MUST) ser feita em um contexto cujo lifetime seja
compatível com o do objeto. Alocações que nascem e morrem juntas **DEVERIAM**
(SHOULD) compartilhar o mesmo contexto.

**Arena allocation é preferencial quando o lifetime dos objetos pode ser
agrupado.**

---

## 2. Arena Allocation

Uma arena é um **modelo de ownership e lifetime**, não um "malloc melhor" e não
apenas uma otimização de performance.

```text
Arena
  ├── allocation A
  ├── allocation B
  ├── allocation C
  └── reset / destroy          (liberação em lote)
```

Em contraste com o modelo de liberação individual:

```text
malloc A → free A
malloc B → free B
malloc C → free C
```

O benefício arquitetural principal é **tornar ownership e lifetime explícitos**.
Ganhos de performance são uma **possível consequência**, nunca o único objetivo
e nunca uma garantia.

> **NÃO DEVE** (MUST NOT) ser afirmado que "arena sempre é mais rápida". O
> critério de adoção é o **lifetime**, não a performance.

---

## 3. Preferred Allocation

**Arena allocation DEVERIA (SHOULD) ser preferida** para objetos cujo lifetime
pode ser agrupado, incluindo:

- request-scoped data;
- parser state;
- ASTs;
- estruturas temporárias;
- batch processing;
- command execution contexts;
- contextos de requisição HTTP/API;
- grafos de vida curta;
- buffers temporários;
- objetos com lifetime comum.

```text
Request → parse → validate → process → response → arena_reset()
Job     → allocations → processing → job destruction → arena_destroy()
```

---

## 4. Acceptable Allocation

`malloc`, `calloc`, `realloc` e `free` **continuam permitidos** e são a escolha
**adequada** para:

- objetos de lifetime independente;
- estruturas que sobrevivem à arena;
- memória compartilhada entre subsistemas;
- recursos cujo ownership exige liberação individual;
- estruturas que crescem/reduzem independentemente;
- integração com APIs externas que exigem allocator específico;
- casos em que a arena introduziria desperdício ou lifetime incorreto.

O uso individual **não** exige justificativa especial quando o lifetime é
genuinamente independente. O que se exige é que a escolha seja **consciente**
quanto a lifetime e ownership.

---

## 5. Discouraged Allocation

**DEVERIA** (SHOULD) ser evitado, sem justificativa, o padrão de `malloc`/
`calloc`/`free` repetidos em loops ou caminhos de alta frequência quando as
alocações possuem lifetime comum.

```c
/* Desencorajado: alocações com lifetime comum, liberadas uma a uma */
for (size_t i = 0; i < count; i++) {
    item = malloc(sizeof(*item));
    /* ... */
    free(item);
}
```

Quando os objetos poderiam ser alocados em uma arena e liberados em lote, o
padrão acima **DEVERIA** ser substituído por alocação em contexto.

Isto **não é** uma proibição: se cada `item` tiver lifetime genuinamente
independente, `malloc`/`free` individuais são aceitáveis.

---

## 6. Ownership Rules

1. Toda arena **DEVE** (MUST) possuir um **owner explícito**.
2. O owner **define** o lifetime da arena.
3. As allocations feitas pela arena **pertencem à arena**.
4. Memória pertencente à arena **NÃO DEVE** (MUST NOT) receber `free()`
   individual.
5. O owner **DEVE** executar `reset` ou `destroy`.
6. Ponteiros para memória da arena **NÃO DEVEM** (MUST NOT) sobreviver ao
   lifetime da arena.
7. **NÃO DEVE** (MUST NOT) ser retornado um ponteiro para memória de uma arena
   que será destruída antes de o consumidor terminar de usá-lo.
8. Referências de longa duração para memória request-scoped **NÃO DEVEM**
   (MUST NOT) ser armazenadas.
9. Uma arena **NÃO DEVE** (MUST NOT) ser usada para **mascarar ownership
   indefinido**.

> Uma arena cujo owner não está claro é um anti-pattern, não uma solução.

---

## 7. Arena Lifecycle

Ciclo de vida conceitual, sempre controlado pelo owner:

```text
arena_init()      cria o contexto e define o owner
      │
arena_alloc()     aloca memória pertencente à arena
arena_calloc()    aloca e zera memória pertencente à arena
      │
arena_reset()     reutiliza o contexto; invalida as allocations anteriores
      │
arena_destroy()   libera o contexto e toda a memória associada
```

- `arena_reset()` **invalida** todos os ponteiros previamente entregues pela
  arena. Uso após o reset é **use-after-reset** (ver seção 8).
- `arena_destroy()` encerra o contexto; a arena **não** pode ser usada depois.
- As funções `arena_*` acima são **nomenclatura conceitual** desta política.
  Este repositório **não** implementa uma arena.

---

## 8. Safety Requirements

Toda implementação de arena **DEVE** (MUST) tratar, de forma centralizada na
sua API:

- `size_t` overflow;
- integer overflow;
- multiplication overflow (ex.: `count * size`);
- alignment;
- padding;
- alignment overflow;
- OOM (out-of-memory);
- `NULL`;
- exhaustion (esgotamento da arena);
- invalid lifetime;
- use-after-reset;
- dangling pointers;
- double destruction.

É **explicitamente rejeitada** uma implementação equivalente a:

```c
/* PROIBIDO: sem validação de overflow e bounds */
arena->used += size;
```

A implementação **DEVE** validar bounds e overflow **antes** de avançar o
ponteiro de uso, e **DEVE** retornar erro/`NULL` de forma consistente em falha.

---

## 9. When NOT to Use an Arena

Arena allocation **não** é universalmente apropriada. Uma arena **NÃO DEVERIA**
(SHOULD NOT) ser usada para:

- objetos globais;
- singletons;
- caches de longa duração;
- estruturas com lifetime independente;
- memória que precisa ser liberada individualmente;
- objetos transferidos entre threads com lifetime independente;
- grandes objetos cujo lifetime individual varia significativamente;
- APIs externas que exigem ownership específico;
- recursos que não são memória simples e precisam de destrutores
  individualizados.

> "Use arena" **não** é dogma. Forçar um objeto em uma arena com lifetime
> incompatível é um defeito de arquitetura.

---

## 10. External Resources and Destructors

`arena_destroy()` **libera memória**. Ele **não** garante que recursos externos
associados tenham sido destruídos.

Recursos como:

- `FILE*`;
- file descriptor;
- socket;
- mutex;
- database connection;
- GPU resource;
- external handle.

exigem **cleanup semântico próprio**.

**DEVE** (MUST) ficar explícito que o cleanup desses recursos ocorre **antes**
do `reset`/`destroy` da arena, quando necessário:

```text
fclose(file);            /* cleanup semântico do recurso */
close(fd);
mutex_destroy(&lock);
      │
arena_destroy(&arena);   /* libera apenas a memória da arena */
```

Para a **v1.1.0 não** existe um sistema de destructors registrados na arena.
Isso é registrado como **possível evolução futura**.

---

## 11. Threading

- Uma arena **DEVE** (MUST) ter owner explícito.
- Uma arena **NÃO DEVE** (MUST NOT) ser compartilhada entre threads sem
  **sincronização explícita**.
- **DEVERIA** (SHOULD) preferir-se arena **thread-local** ou **request/job-local**
  quando apropriado.
- Locks internos **NÃO DEVERIAM** (SHOULD NOT) ser introduzidos sem necessidade
  quando a arquitetura garante ownership exclusivo.

> Uma arena **não é automaticamente thread-safe**. A segurança de concorrência
> é responsabilidade do owner e do desenho, não um atributo implícito da arena.

---

## 12. Reallocation

A arena **NÃO DEVE** (MUST NOT) tentar reproduzir ingenuamente a semântica de
`realloc()` individual — a arena não libera blocos individualmente.

Alternativas documentadas:

- **allocate + copy**: alocar novo bloco na arena e copiar os dados; o bloco
  antigo permanece ocupado até o reset/destroy;
- **growth region**: região de crescimento dedicada para estruturas que crescem;
- **dedicated allocation**: alocar fora da arena, com ownership individual;
- **allocator tradicional**: usar `malloc`/`realloc` quando o lifetime **não**
  for compatível com arena.

Se um objeto cresce e reduz com frequência, seu lifetime provavelmente **não**
é adequado a arena — prefira as alternativas acima.

---

## 13. Den Architecture Integration

A memória e o seu ownership distribuem-se pelas camadas sem criar dependências
arquiteturais indevidas:

| Camada | Papel em relação à memória |
| ------ | -------------------------- |
| `den/` | **Domain Core** — independente de qualquer allocator concreto. Pode trabalhar com memória fornecida pelo chamador. |
| `senses/` | **Contracts & Validation** — valida tamanhos, limites, contagens e overflow **antes** da alocação. Não é um *memory manager*. |
| `adapters/` | **Infrastructure & I/O** — **pode** conter os detalhes concretos de infraestrutura de memória quando aplicável. |
| `vanguard/` | **Application Entry** — define e orquestra o lifetime de request/job/contexto. |
| `kit/` | **Testing & QA** — testa propriedades de lifetime e segurança dos consumidores. |

**Ponto normativo:** a implementação concreta de um allocator é um **detalhe de
infraestrutura** e **não deve ser acoplada ao Domain Core**.

A Den Architecture **não** obriga que toda implementação de arena resida em
`adapters/` em todos os projetos. Um projeto consumidor **pode** colocar a
implementação concreta em `adapters/` — o que é compatível com a arquitetura —
mas o requisito é o **isolamento do Domain Core**, não uma localização
obrigatória.

O `den/` **NÃO DEVE** (MUST NOT) conhecer `arena_t`, `arena_alloc()` ou
qualquer dependência de infraestrutura. Nesta versão **não** é introduzido
nenhum contrato de allocator no domínio.

---

## 14. Migration Strategy

Para adotar a política em um projeto consumidor:

1. Identificar **grupos de lifetime** (request, job, parse, batch).
2. Introduzir a arena no limite de `vanguard/` (escopo de request/job).
3. Mover I/O para `adapters/`, preservando o ownership externo.
4. Validar tamanhos, limites e overflow em `senses/` antes de alocar.
5. Migrar de forma **incremental**; não reescrever tudo de uma vez.
6. Manter `malloc`/`free` onde o lifetime é independente.
7. Garantir o cleanup de recursos externos **antes** de `arena_reset`/`arena_destroy`.

---

## 15. Examples

### Desencorajado — alocações independentes com o mesmo lifetime

```c
/* Objetos que nascem e morrem juntos, mas são gerenciados um a um. */
User    *user    = malloc(sizeof(*user));
Session *session = malloc(sizeof(*session));

/* ... */

free(session);
free(user);
```

### Preferencial — arena com lifetime agrupado

```c
Arena arena;

arena_init(&arena, ARENA_DEFAULT_CAPACITY);

User    *user    = arena_alloc(&arena, sizeof(*user));
Session *session = arena_alloc(&arena, sizeof(*session));
Buffer  *buffer  = arena_alloc(&arena, sizeof(*buffer));

/* ... */

arena_destroy(&arena);
```

### Aceitável — lifetime independente

```c
/* Objeto que sobrevive ao contexto atual: ownership individual é adequado. */
Config *config = malloc(sizeof(*config));
if (config == NULL) {
    return -1;
}

/* ... */

free(config);
```

> Os exemplos acima são **conceituais**. Este repositório **não** implementa
> `arena_init`, `arena_alloc`, `arena_calloc`, `arena_reset` ou `arena_destroy`.

---

## 16. Anti-patterns

| Anti-pattern | Por que é errado |
| ------------ | ---------------- |
| Arena sem owner explícito | Esconde o lifetime; ownership indefinido |
| `free()` em memória pertencente à arena | Mistura dois modelos de ownership |
| Retornar ponteiro de arena que será destruída | Dangling pointer garantido |
| Guardar referência de longa duração a memória request-scoped | Use-after-reset |
| `arena->used += size;` sem validação | Overflow e corrupção de memória |
| Usar arena para objetos de lifetime independente | Lifetime incorreto; desperdício |
| Assumir que `arena_destroy()` fecha recursos externos | Vazamento de `FILE*`, sockets, fds, locks |
| Compartilhar arena entre threads sem sincronização | Data race |
| Tratar arena como "malloc mais rápido" | Ignora o critério de ownership/lifetime |
| Usar arena para mascarar ownership indefinido | Adia o problema, não o resolve |

---

## Future Enforcement (post-v1.1.0)

O enforcement automático **não** faz parte da v1.1.0. **NÃO** é criado, nesta
versão:

- `clang-tidy`;
- CodeQL;
- scripts de `grep`;
- regras frágeis;
- bloqueio global de `malloc`/`calloc`/`free`.

Fica registrado como evolução futura, preferencialmente por:

- **clang-tidy** — checagens semânticas customizadas;
- **CodeQL** — consultas de fluxo de alocação/lifetime;
- **análise de `malloc`/`free` em loops** — detecção de padrões suspeitos;
- **análise de camada** — alocação em camadas indevidas;
- **regras específicas de projeto** — políticas locais.

Qualquer enforcement futuro **DEVE** detectar **padrões suspeitos**, e não
proibir `malloc`/`calloc`/`free` de forma indiscriminada.
