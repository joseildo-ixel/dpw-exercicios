## E00.1 — Reprodutibilidade do ambiente

https://github.com/joseildo-ixel/dpw-exercicios/blob/8ee593a94866c9d417297c354e0f854814042d54/.gitignore#L1

Comando executado:
```powershell
Remove-Item -Recurse -Force node_modules ; pnpm install --frozen-lockfile ; git status --short
```

Saída do `pnpm install`:
```
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
```

Saída de `git status --short` (vazia - prova de reprodutibilidade):
```
(sem saída - working tree clean)
```

## Lock vs node_modules

O arquivo `pnpm-lock.yaml` é versionado no repositório porque ele registra as versões exatas de cada dependência (e de suas subdependências), garantindo que qualquer pessoa que clone o projeto possa reinstalar o mesmo ambiente com as mesmas versões. O `node_modules/` NÃO é versionado porque é reconstruído automaticamente a partir do lock file — é um artefato descartável. Isso garante que o repositório seja leve e reprodutível em qualquer máquina.

(Observação: o comando `git status --short` não imprimiu nenhuma linha, confirmando que recriar `node_modules` não gerou nenhuma alteração rastreada pelo git.)

## Commit de configuração (.gitattributes)

https://github.com/joseildo-ixel/dpw-exercicios/commit/2e3abf56722d4ca5bc1d1ca34df439f4859e1a3a
