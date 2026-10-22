+++
date = '2026-10-22T18:00:00+02:00'
draft = false
title = 'Git: rimuovere un file da tutta la storia (rebase + force push)'
tags = ['git', 'til']
description = 'Come togliere un file committato per errore da tutti i commit con un rebase interattivo, senza installare nulla, e come allineare i colleghi.'
+++

Come togliere un file da tutti i commit con un rebase interattivo.

## 1. Controlla lo stato e fai un backup del file

```bash
git status -sb
git log --oneline
cp percorso/del/file.json ~/file.json.bak
```

L'albero di lavoro deve essere pulito (i file non tracciati vanno bene). Il backup serve perché, se sbagli, il file locale è l'unica copia.

## 2. Avvia il rebase e fermati sul commit giusto

Se il file è nel primo commit:

```bash
GIT_SEQUENCE_EDITOR="sed -i '' '1s/^pick/edit/'" git rebase -i --root
```

Segna il primo commit come `edit` senza aprire l'editor (`sed -i ''` è la forma per macOS). Git si ferma su quel commit.

Se il file è in un commit più avanti, usa il commit precedente come base e metti `edit` sul commit giusto:

```bash
git rebase -i HASH_DEL_COMMIT^
```

## 3. Togli il file dal commit

```bash
git rm --cached percorso/del/file.json
git commit --amend --no-edit
git rebase --continue
```

- `git rm --cached` toglie il file dal tracciamento ma lo lascia sul disco.
- `git commit --amend --no-edit` riscrive il commit corrente senza il file, tenendo lo stesso messaggio.
- `git rebase --continue` riapplica i commit successivi sopra quello riscritto.

Se un commit successivo modifica lo stesso file, git segnala un conflitto: risolvilo (di solito basta `git rm --cached` anche lì) e ripeti `git rebase --continue`.

## 4. Verifica

```bash
git log --oneline --stat
git log develop -p -- percorso/del/file.json
```

Il primo non deve mostrare il file in nessun commit. Il secondo (sostituisci `develop` con la tua branch) non deve stampare niente. Se usi `--all` vedrai ancora i vecchi commit finché `origin/...` punta alla vecchia storia: è normale fino al push.

## 5. Ignoralo da ora in poi

```bash
printf '\npercorso/del/file.json\n' >> .gitignore
git add .gitignore
git check-ignore -v percorso/del/file.json
git commit -m "ignore percorso/del/file.json"
```

Il `\n` iniziale evita che il percorso si attacchi all'ultima riga di `.gitignore` quando il file non finisce con un a capo. `git check-ignore -v` stampa la regola che ignora il file: se non stampa niente, la regola non funziona.

## 6. Force push

```bash
git push --force-with-lease origin NOME_BRANCH
```

`--force-with-lease` sovrascrive il remoto solo se nessuno ha pushato nel frattempo. Se la branch è protetta, serve consentire temporaneamente il force push nelle impostazioni del repository.

## 7. Riallinea gli altri cloni

Chi ha già clonato il repository:

```bash
git fetch
git reset --hard origin/NOME_BRANCH
```

Salvando prima il lavoro non pushato, perché `reset --hard` lo scarta.

## Cose da sapere

- I commit ricevono **nuovi hash**: branch, merge request e tag basati sui vecchi commit vanno rifatti. Avvisa prima chi lavora sul repository.
- Chi ha già scaricato la vecchia storia ha ancora il file sul disco, e sul server i vecchi oggetti possono restare raggiungibili per hash finché non gira la garbage collection.
- Per repository con molti commit esiste `git filter-repo --path percorso/del/file.json --invert-paths`, più comodo ma da installare (`brew install git-filter-repo`).
