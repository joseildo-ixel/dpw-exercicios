# Evidência — Desfazer alterações e commits (E04)

## Tabela de comandos usados

| Cenário | Comando principal | O que faz |
|---|---|---|
| 1 — Descartar edição não staged | `git restore <arquivo>` | Descarta alterações no working directory, volta ao último commit |
| 2 — Tirar do stage | `git restore --staged <arquivo>` | Remove do stage, mas mantém a edição no arquivo |
| 3 — Corrigir mensagem do último commit | `git commit --amend -m "..."` | Substitui o último commit por um novo, com mensagem corrigida |
| 4 — Desfazer commit mantendo alterações | `git reset --soft HEAD~1` | Remove o commit do histórico, mas preserva as mudanças staged |
| 5 — Reverter commit já enviado ao remoto | `git revert <hash>` | Cria um novo commit que desfaz as mudanças de um commit anterior, sem reescrever histórico |

---

## Cenário 1 — Descartar edição não staged

### Status ANTES
\`\`\`
On branch main
Your branch is up to date with 'origin/main'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")
\`\`\`

### Comando
\`\`\`
git restore README.md
\`\`\`

### Status DEPOIS
\`\`\`
On branch main
Your branch is up to date with 'origin/main'.
nothing to commit, working tree clean
\`\`\`

---

## Cenário 2 — Tirar do stage

### Status ANTES (após git add)
\`\`\`
On branch main
Your branch is up to date with 'origin/main'.
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   README.md
\`\`\`

### Comando
\`\`\`
git restore --staged README.md
\`\`\`

### Status DEPOIS
\`\`\`
On branch main
Your branch is up to date with 'origin/main'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")
\`\`\`

---

## Cenário 3 — Corrigir mensagem do último commit

### Log ANTES
\`\`\`
3a7d29f (HEAD -> main) mensagem errada
28b6ed2 mensagem corrigida
57c01d1 (origin/main, origin/HEAD) E00.3: evidencia do conflito de merge
\`\`\`

### Comando
\`\`\`
git commit --amend --allow-empty -m "mensagem corrigida"
\`\`\`

### Log DEPOIS
\`\`\`
60cf468 (HEAD -> main) mensagem corrigida
28b6ed2 mensagem corrigida
57c01d1 (origin/main, origin/HEAD) E00.3: evidencia do conflito de merge
\`\`\`

---

## Cenário 4 — Desfazer último commit mantendo alterações

### Log ANTES
\`\`\`
b27cb14 (HEAD -> main) commit para desfazer depois
60cf468 mensagem corrigida
28b6ed2 mensagem corrigida
\`\`\`

### Comando
\`\`\`
git reset --soft HEAD~1
\`\`\`

### Log DEPOIS
\`\`\`
60cf468 (HEAD -> main) mensagem corrigida
28b6ed2 mensagem corrigida
57c01d1 (origin/main, origin/HEAD) E00.3: evidencia do conflito de merge
\`\`\`

### Status DEPOIS (alteração preservada, staged)
\`\`\`
On branch main
Your branch is ahead of 'origin/main' by 2 commits.
  (use "git push" to publish your local commits)
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   README.md
\`\`\`

---

## Cenário 5 — Reverter commit já enviado ao remoto

### Log ANTES
\`\`\`
60cf468 (HEAD -> main) mensagem corrigida
28b6ed2 mensagem corrigida
57c01d1 (origin/main, origin/HEAD) E00.3: evidencia do conflito de merge
\`\`\`

### Comando
\`\`\`
git revert 21bb69b --no-edit
\`\`\`

Obs: houve conflito em `evidencias/e1-ambiente.md`, resolvido manualmente com `git add` + `git revert --continue`.

### Log DEPOIS
\`\`\`
96c7841 (HEAD -> main, origin/main, origin/HEAD) Revert "docs: evidência de reprodutibilidade do ambiente (E00.1)"
4d4b3cc docs: atualiza README (cenario 4)
60cf468 mensagem corrigida
\`\`\`

### Link permanente do commit de revert
[96c7841]( https://github.com/joseildo-ixel/dpw-exercicios/commit/96c7841)

---

## Reflog final (`git reflog -10`)

\`\`\`
96c7841 (HEAD -> main, origin/main, origin/HEAD) HEAD@{0}: commit: Revert "docs: evidência de reprodutibilidade do ambiente (E00.1)"
4d4b3cc HEAD@{1}: commit: docs: atualiza README (cenario 4)
60cf468 HEAD@{2}: reset: moving to HEAD~1
b27cb14 HEAD@{3}: commit: commit para desfazer depois
60cf468 HEAD@{4}: commit (amend): mensagem corrigida
3a7d29f HEAD@{5}: commit: mensagem errada
28b6ed2 HEAD@{6}: commit (amend): mensagem corrigida
91fcf77 HEAD@{7}: commit: mensagem errada
57c01d1 HEAD@{8}: commit: E00.3: evidencia do conflito de merge
8a9fdf7 (a) HEAD@{9}: commit (merge): Merge branch 'feat/titulo-b'
\`\`\`

---

## Por que o Cenário 5 é diferente do Cenário 4

O Cenário 4 (`reset --soft`) reescreve o histórico local antes de qualquer compartilhamento — é seguro porque o commit desfeito nunca foi enviado ao remoto, então ninguém mais tem essa referência. Já o Cenário 5 (`revert`) preserva todo o histórico existente e apenas adiciona um commit novo que desfaz as mudanças de um commit anterior. Essa abordagem é essencial quando o commit já foi enviado ao remoto (`push`), pois outras pessoas podem já ter baixado aquele histórico, e reescrevê-lo (com `reset` ou `amend`) causaria divergência e exigiria `push --force`, arriscando sobrescrever o trabalho de outros colaboradores.