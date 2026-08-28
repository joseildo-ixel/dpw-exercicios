## E00.3 — Conflito de merge, provocado sozinho

### Saída do git merge que acusou o conflito

Comando:
```powershell
git merge feat/titulo-b
```

Saída:
```
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

### Conteúdo do arquivo durante o conflito (com marcadores)

Arquivo `README.md` durante o conflito:
```
<<<<<<< HEAD
# Exercicios de DPW — Modulo M00 (versao A)
=======

>>>>>>> feat/titulo-b
# Exercicios individuais — DPW M00 (versao B)
- **E00.1 — Ambiente reprodutível:** [commit 2e3abf5](https://github.com/joseildo-ixel/dpw-exercicios/commit/2e3abf5)
```

(A `HEAD` tinha editado a linha 1 de um jeito; `feat/titulo-b` havia editado a mesma linha de outro jeito — conflito no mesmo arquivo, mesma linha, duas versões diferentes.)

### Saída de git log --graph --oneline --all

```
*   8a9fdf7 (HEAD -> main) Merge branch 'feat/titulo-b'
|\
| * 6fefc3e (feat/titulo-b) altera titulo, versao B
* | 4aea20e (main) altera titulo, versao A
|/
* b5c388c docs: evidência da investigação do histórico do NestJS (E00.2)
* 7ce38d3 docs: linka commit do E00.1 no README e evidencia
* 2e3abf5 chore: configura .gitattributes com regra de normalização de texto
* 21bb69b docs: evidência de reprodutibilidade do ambiente (E00.1)
* 8ee593a inicializa pacote com pnpm, adiciona prettier e script verificar
```

### Links permanentes

- **Commit de merge:** https://github.com/joseildo-ixel/dpw-exercicios/commit/8a9fdf75b8a94d8a4b96937af8f3f1fba56e2710
- **Grafo de branches:** https://github.com/joseildo-ixel/dpw-exercicios/network

### Por que o Git não conseguiu resolver sozinho?

Git falhou na resolução automática porque a mesma linha (linha 1 do README.md) foi modificada em **ambas as branches**, em pontos diferentes do histórico. A `main` tinha `# Exercicios de DPW — Modulo M00 (versao A)` enquanto `feat/titulo-b` tinha `# Exercicios individuais — DPW M00 (versao B)`. Como não há marcadores visuais que indiquem qual versão é "correta", Git não pode adivinhar — exige intervenção manual de quem sabe qual mudança deve prevalecer.
