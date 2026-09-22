# Padrão C

Este documento define o padrão de código **C** do Vulpes Code Standard. A
configuração correspondente é o template `configs/.clang-format`.

## Versão da linguagem

- **C11 ou superior.**

A versão é aplicada pelo **compilador**, por exemplo:

```bash
cc -std=c11 -Wall -Wextra -Wpedantic -o programa programa.c
```

O `.clang-format` formata o código, mas não valida nem impõe a versão da
linguagem.

## Formatação

| Regra | Valor |
| ----- | ----- |
| Indentação | 4 espaços |
| Tabuladores | proibidos |
| Estilo de chaves | Attach / K&R / OTBS |
| Comprimento de linha | 100 colunas |
| Alinhamento de ponteiros | à variável (`void *ptr;`) |

### Exemplo formatado

```c
#include <stdio.h>
#include <string.h>

#define DEN_MAX_BUFFER_SIZE 4096

typedef struct {
    int id;
    char name[64];
} den_user_session_t;

int vanguard_init_server(const char *host, int port) {
    if (host == NULL) {
        return -1;
    }
    printf("listening on %s:%d\n", host, port);
    return 0;
}
```

## Nomenclatura

### Funções e variáveis — `snake_case`

```c
vanguard_init_server();
int buffer_size = 0;
```

### Constantes e macros — `UPPER_SNAKE_CASE`

```c
#define DEN_MAX_BUFFER_SIZE 4096
static const int SENSES_TIMEOUT_MS = 5000;
```

### Tipos e structs — sufixo `_t`

```c
typedef struct {
    int id;
    char name[64];
} den_user_session_t;
```

### Ponteiros — asterisco alinhado à variável

```c
void *ptr;
char *buffer;
```

> **Nota:** clang-format aplica o alinhamento de ponteiros (`PointerAlignment:
> Right`). As demais convenções de nomenclatura são **regras documentais** —
> revise-as em code review, pois a ferramenta não as impõe automaticamente.

## Segurança

É **proibido** o uso de funções propensas a overflow de buffer.

### Proibido

```c
strcpy(dest, src);
sprintf(dest, "%s", src);
```

### Preferencial

```c
strncpy(dest, src, sizeof(dest) - 1);
dest[sizeof(dest) - 1] = '\0';

snprintf(dest, sizeof(dest), "%s", src);
```

O uso de funções com limite de memória deve ser acompanhado de **validação
adequada na camada `senses/`** (limites de buffer, tamanhos e dados de entrada).

## Gestão de memória

- Inicialize variáveis e estruturas antes do uso.
- Verifique o retorno de funções de alocação (`malloc`, `calloc`, `realloc`).
- Libere toda memória alocada (`free`) e atribua `NULL` ao ponteiro liberado.
- Use `sizeof` sobre a variável, não sobre o tipo, sempre que possível.
- Evite vazamentos em caminhos de erro; centralize a limpeza.
- Prefira buffers de tamanho fixo e conhecido em `den/`; alocação dinâmica
  pertence à infraestrutura quando inevitável.

## Uso do `.clang-format`

O arquivo `configs/.clang-format` é um **template distribuível**. Copie-o para a
**raiz** do seu projeto consumidor:

```bash
cp configs/.clang-format meu-projeto/.clang-format
```

Formate um arquivo:

```bash
clang-format -i src/programa.c
```

Verifique sem alterar (útil em CI):

```bash
clang-format --dry-run --Werror src/*.c
```

IDEs como VS Code (extensão C/C++) e CLion reconhecem o `.clang-format` na raiz
do projeto automaticamente.

## Exceção: Makefile

A sintaxe do `Makefile` **exige tabulações**. Essa é a única exceção à regra
"tabs proibidos" e está refletida no `.editorconfig`.
