+++
date = '2026-10-09T09:00:00+02:00'
draft = false
title = 'Git: aggiornare un token HTTPS per un singolo repository'
tags = ['git', 'til', 'sicurezza']
description = 'Come sostituire o rinnovare un Personal Access Token HTTPS limitando la modifica al solo repository interessato.'
+++

Come sostituire un Personal Access Token HTTPS senza toccare le credenziali degli altri repository.

## 1. Controlla (e ripulisci) il remote

```bash
git remote -v
```

L'URL deve essere nella forma `https://git.example.com/gruppo/repository.git`.
Se contiene `utente:token@`, ripuliscilo:

```bash
git remote set-url origin https://git.example.com/gruppo/repository.git
```

## 2. Configura le credenziali solo per questo repository

```bash
git config --local credential.helper osxkeychain
git config --local credential.useHttpPath true
git config --local credential.username IL_TUO_UTENTE
```

`--local` limita tutto al repository corrente. `credential.useHttpPath true` è utile se usi più account sullo stesso server.

Verifica la configurazione locale:

```bash
git config --local --get-regexp '^credential\.'
```

## 3. Rimuovi il token precedente dal Portachiavi

Sostituisci `git.example.com`, `gruppo/repository.git` e `IL_TUO_UTENTE` con i valori reali:

```bash
printf 'protocol=https\nhost=git.example.com\npath=gruppo/repository.git\nusername=IL_TUO_UTENTE\n\n' | git credential reject
```

Questo rimuove solo la credenziale di quel repository.

## 4. Esegui il push

```bash
git push origin NOME_BRANCH
```

Git chiederà username e password (il nuovo token). Il token non sarà visibile mentre lo incolli. Dopo un push riuscito viene salvato nel Portachiavi per quel repository.

Per rinnovare il token in futuro ripeti i passi 3 e 4.
