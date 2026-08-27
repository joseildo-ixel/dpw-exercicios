# Evidência — Diagnóstico de pacote ausente (E05)

## 1. Tabela de diagnóstico (do mais barato ao mais caro)

| # | Diagnóstico | Comando | O que verifica |
|---|---|---|---|
| 1 | O pacote está declarado no `package.json`? | `type package.json \| findstr <pacote>` | Se a dependência foi realmente adicionada como dependency/devDependency |
| 2 | O pacote existe de fato em `node_modules`? | `dir node_modules\<pacote>` | Se a instalação física está presente, e não só listada no `package.json` |
| 3 | O import usa o nome certo? | Conferir manualmente a linha `import`/`require` no arquivo | Erros de maiúsculas/minúsculas, escopo (`@org/pacote`) ou nome digitado errado |
| 4 | Você está na pasta certa do projeto? | `cd` (sozinho, no CMD) | Se o comando não está sendo rodado numa subpasta errada ou fora do repositório |
| 5 | Reinstalar do zero (último recurso) | `rm -rf node_modules && pnpm install` (ou `pnpm add -D <pacote>` se for só um pacote) | Corrige cache corrompido, lockfile desatualizado ou instalação incompleta |

---

## 2. Provocar a falha de propósito

Pacote escolhido: **prettier** (usado como devDependency no projeto).

### Antes da quebra

`package.json`:
```json
"devDependencies": {
  "prettier": "^3.9.6"
}
```

`node_modules/prettier`: pasta existente com todos os arquivos do pacote.

### Comando usado para quebrar

pnpm remove prettier


Saída:
✓ Lockfile passes supply-chain policies (verified 21h ago)
Packages: -1

devDependencies:

prettier 3.9.6
Done in 1.6s using pnpm v11.23.0

### Depois da quebra

`package.json` (devDependencies removido):
```json
{
  "name": "dpw-exercicios",
  "version": "1.0.0",
  "private": true,
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "verificar": "node --version && pnpm --version"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

`node_modules/prettier`:

C:\dev\dpw-exercicios>dir node_modules\prettier
Pasta de C:\dev\dpw-exercicios\node_modules
Arquivo não encontrado


`npx prettier --version`:

Need to install the following packages:
prettier@3.9.6
Ok to proceed? (y) n
npm error canceled


---

## 3. Rodar o roteiro contra o ambiente quebrado

Roteiro aplicado na ordem, sem espiar a causa antes:

### Passo 1 — pacote declarado no `package.json`?

Comando:

type package.json | findstr prettier


Saída: *(vazia — nenhuma linha encontrada)*

**Conclusão: causa raiz identificada aqui.** O pacote não está mais declarado em `package.json`.

### Passo 2 — está em `node_modules` de verdade?

Comando:

dir node_modules\prettier


Saída:

Arquivo não encontrado


**Confirma o Passo 1** — o pacote também não existe fisicamente instalado.

### Passos 3, 4 e 5 — não foram necessários

A causa já havia sido identificada no passo mais barato do roteiro (Passo 1), confirmada no Passo 2. Não foi preciso verificar o nome do import, a pasta de execução, nem reinstalar tudo do zero — apenas reinstalar o pacote específico.

---

## 4. Correção do ambiente

Comando:

pnpm add -D prettier


### Depois da correção

`package.json`:
```json
"devDependencies": {
  "prettier": "^3.9.6"
}
```

`node_modules/prettier`: pasta restaurada com todos os arquivos.

`npx prettier --version`:

3.9.6


---

## 5. Conclusão

O roteiro de diagnóstico em 5 passos, ordenado do mais barato ao mais caro, permitiu identificar a causa real do problema (pacote removido do projeto) já no primeiro passo — uma simples checagem no `package.json` — sem precisar recorrer a diagnósticos mais custosos como verificar o import, o diretório de execução, ou reinstalar todo o `node_modules`. Isso demonstra a importância de seguir uma ordem de verificação crescente em custo/tempo antes de partir para soluções mais drásticas.