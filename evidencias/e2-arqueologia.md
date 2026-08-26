# E00.2 — Investigação do histórico do repositório NestJS

## Pergunta 1 — Quantos commits

Comando:
\`\`\`powershell
git rev-list --count HEAD
\`\`\`

Saída:
\`\`\`
21659
\`\`\`

**Resposta:** O repositório tem 21.659 commits alcançáveis a partir do HEAD atual (branch principal).

## Pergunta 2 — Primeiro commit e data

Comando:
\`\`\`powershell
git log --reverse --pretty=format:"%h %ad %s" --date=short | Select-Object -First 1
\`\`\`

Saída:
\`\`\`
f7c8d10fb 2017-01-08 Initial commit
\`\`\`

**Resposta:** O primeiro commit do repositório é de 08/01/2017, com hash `f7c8d10fb` e mensagem "Initial commit".

## Pergunta 3 — Quem mais mexeu em injector.ts

Comando:
\`\`\`powershell
git shortlog -sn -- packages/core/injector/injector.ts
\`\`\`

Saída:
\`\`\`
    90  Kamil Myśliwiec
    12  Jay McDoniel
     6  Kamil Mysliwiec
     4  Jean-Baptiste Pionnier
     4  Livio Brunner
     3  Micael Levi (lab)
     2  Jiri Hajek
     2  Micael Levi L. Cavalcante
     2  mag123c
     1  Elies Lou
     1  Lee Donghyun
     1  Livio
     1  Lutz
     1  Nathan Knight
     1  Sergei Yudin
     1  Tony133
     1  codytseng
     1  cojack
     1  coti-z
     1  jacob87o2
     1  malekelkssas
     1  tooleks
     1  youmoo
\`\`\`

**Resposta:** Kamil Myśliwiec é quem mais mexeu no arquivo, com 90 commits (considerando também as 6 entradas sob a variação sem acento "Kamil Mysliwiec", provavelmente o mesmo autor com configuração de git diferente, totalizando 96).

## Pergunta 4 — Última mudança no arquivo

Comandos:
\`\`\`powershell
git log -1 --pretty=format:"%H" -- packages/core/injector/injector.ts
git show 45485b54210e06a517c1ebf86b42b1ea99fc3fe2 -- packages/core/injector/injector.ts
\`\`\`

Saída:
\`\`\`
commit 45485b54210e06a517c1ebf86b42b1ea99fc3fe2
Author: Kamil Myśliwiec <mail@kamilmysliwiec.com>
Date:   Tue Aug 25 12:48:22 2026 +0200

    fix(core): circular durable providers issue #17562

diff --git a/packages/core/injector/injector.ts b/packages/core/injector/injector.ts
index 32b9f2850..aad3a899c 100644
--- a/packages/core/injector/injector.ts
+++ b/packages/core/injector/injector.ts
@@ -581,7 +581,7 @@ export class Injector {
       * that eventual lazily created instance will be merged with the prototype
       * instantiated beforehand.
       */
-      instanceHost.donePromise &&
+      if (instanceHost.donePromise) {
         void instanceHost.donePromise
           .then(() =>
             this.loadProvider(instanceWrapper, moduleRef, resolutionContext),
@@ -589,6 +589,20 @@ export class Injector {
           .catch(err => {
             instanceWrapper.settlementSignal?.error(err);
           });
+      } else {
+        /**
+         * No load has ever been scheduled for this context (e.g., request-scoped
+         * providers are no longer instantiated during static bootstrap, so a fresh
+         * durable/request sub-tree host has no inherited `donePromise`).
+         * Load it now; if a circular dependency is truly in-flight, the nested
\`\`\`

**Resposta:** A última mudança foi feita por Kamil Myśliwiec em 25/08/2026, no commit `45485b5`, corrigindo um problema de circular durable providers (issue #17562).

## Pergunta 5 — Commits nos últimos 90 dias

Comando:
\`\`\`powershell
git log --since="90 days ago" --oneline | Measure-Object -Line
\`\`\`

Saída:
\`\`\`
Lines Words Characters Property
----- ----- ---------- --------
  692
\`\`\`

**Resposta:** Foram feitos 692 commits nos últimos 90 dias.