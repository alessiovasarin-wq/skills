# Fork di Alessio

Questo repository è un fork di [mattpocock/skills](https://github.com/mattpocock/skills), installato come plugin Claude Code sul PC Windows e sul MacBook.

## Cosa cambia rispetto a Matt

- **Skill nuove**, adattate da [pstack](https://github.com/cursor/plugins/tree/main/pstack) di Lauren Tan (MIT, licenza in `LICENSE-pstack` dentro ogni cartella):
  - `skills/engineering/blast-radius`
  - `skills/engineering/create-verification-skill`
  - `skills/engineering/maintain-verification-skill`
- **Skill di Matt modificate** (solo aggiunte, per agganciare le nuove):
  - `implement`: passo "prove it works" + `/blast-radius`
  - `code-review`: sezione finale "What this doesn't cover"
  - `diagnosing-bugs`: riuso di `verify` in Phase 1, `/blast-radius` in Phase 5, checklist in Phase 6
  - `ask-matt`: main flow aggiornato + sezione "Proof layer"
- **Nomi qualificati**: in `implement`, `ask-matt`, `tdd` e `blast-radius` la review di Matt è chiamata `/mattpocock-skills:code-review`, perché `/code-review` da solo apre la review integrata di Claude Code. Dopo ogni merge da upstream controlla con `grep -rn "/code-review" skills` che non siano tornati riferimenti senza prefisso.
- **Manifest**: marketplace rinominato `alessio`, versione `X.Y.Z-alessio.N` in `.claude-plugin/plugin.json`.

## Regola: si modifica qui, poi commit e push

Le skill installate vivono nella cache dei plugin di Claude Code e vengono sovrascritte a ogni aggiornamento. **Non modificarle lì.** Si lavora sempre nel clone:

- PC: `C:\Users\WKS\code\skills`
- Mac: `~/code/skills`

A ogni modifica:

1. `git pull` (l'altra macchina potrebbe aver pubblicato qualcosa).
2. Modifica la skill.
3. Alza la versione in `.claude-plugin/plugin.json` (`1.2.3-alessio.1` → `1.2.3-alessio.2`): Claude Code usa la versione per decidere se c'è un aggiornamento.
4. `claude plugin validate . --strict`
5. Commit e push:
   ```bash
   git add -A && git commit -m "skill: descrizione breve" && git push
   ```
6. Su entrambe le macchine, per ricevere la modifica:
   ```bash
   claude plugin marketplace update alessio
   claude plugin update mattpocock-skills@alessio
   ```
   e riavvia la sessione di Claude Code.

Se aggiungi, rinomini o togli una skill: aggiorna anche la lista `skills` in `.claude-plugin/plugin.json`, i due `README.md` e la mappa in `ask-matt`.

Convenzione del repo di Matt: niente em-dash (—) nella prosa.

## Prendere gli aggiornamenti di Matt

```bash
git fetch upstream
git merge upstream/main
```

I conflitti possono nascere solo sui file elencati sopra: tieni le parti di Matt e riapplica le aggiunte. Su `plugin.json` tieni la versione di Matt con il suffisso `-alessio.1`. Poi validazione, commit, push e aggiornamento su entrambe le macchine come sopra.

## Installazione su una macchina nuova

```bash
gh auth login
git clone https://github.com/alessiovasarin-wq/skills ~/code/skills
cd ~/code/skills && git remote add upstream https://github.com/mattpocock/skills
claude plugin marketplace add alessiovasarin-wq/skills
claude plugin install mattpocock-skills@alessio
```

Se c'è il plugin originale di Matt, disattivalo: `claude plugin disable mattpocock-skills@mattpocock`.
