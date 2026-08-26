## E00.1 — Reprodutibilidade do ambiente

https://github.com/joseildo-ixel/dpw-exercicios/blob/8ee593a94866c9d417297c354e0f854814042d54/.gitignore#L1

Comando executado:
\`\`\`powershell
Remove-Item -Recurse -Force node_modules ; pnpm install --frozen-lockfile ; git status --short
\`\`\`

Saída:
\`\`\`
✓ Lockfile passes supply-chain policies (verified 7m ago)
Lockfile is up to date, resolution step is skipped
Packages: +1
+
Packages are hard linked from the content-addressable store to the virtual store.
  Content-addressable store is at: C:\Users\lucas\AppData\Local\pnpm\store\v11
  Virtual store is at:             node_modules/.pnpm
Progress: resolved 1, reused 1, downloaded 0, added 1, done
devDependencies:
+ prettier 3.9.6
Done in 1.3s using pnpm v11.23.0
\`\`\`

## Lock vs node_modules

O arquivo `pnpm-lock.yaml` é versionado no repositório porque ele registra as versões exatas de cada dependência (e de suas subdependências), garantindo que qualquer pessoa que clone o projeto instale exatamente os mesmos pacotes, evitando o problema de "funciona na minha máquina". Já a pasta `node_modules` não é versionada porque ela é gerada automaticamente a partir do `pnpm-lock.yaml` — pode ser apagada e recriada a qualquer momento com `pnpm install --frozen-lockfile`, sem perder nada, o que a torna redundante e desnecessariamente pesada para manter no controle de versão.

(Observação: o comando `git status --short` não imprimiu nenhuma linha, confirmando que recriar `node_modules` não gerou nenhuma alteração rastreada pelo git.)