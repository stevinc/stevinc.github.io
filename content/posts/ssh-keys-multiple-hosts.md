+++
date = '2026-03-13T07:30:53+01:00'
draft = false
title = 'SSH keys per host multipli (GitHub, GitLab, Bitbucket)'
tags = ['git', 'ssh', 'til']
description = 'Come configurare SSH keys separate per GitHub, GitLab, Bitbucket e altri host senza che si pestino i piedi.'
+++

Quando hai più account git su host diversi (GitHub personale, GitLab aziendale, Bitbucket, ecc.) la soluzione è usare una key dedicata per ciascuno e configurare `~/.ssh/config` per dire a SSH quale usare.

## 1. Crea una key per ogni host

```bash
ssh-keygen -t ed25519 -C "tua@email.com" -f ~/.ssh/id_ed25519_github_personal
ssh-keygen -t ed25519 -C "lavoro@azienda.com" -f ~/.ssh/id_ed25519_gitlab_work
```

## 2. Aggiungile all'agente

```bash
ssh-add ~/.ssh/id_ed25519_github_personal
ssh-add ~/.ssh/id_ed25519_gitlab_work
```

## 3. Configura `~/.ssh/config`

```
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_github_personal

Host gitlab.azienda.com
  HostName gitlab.azienda.com
  User git
  IdentityFile ~/.ssh/id_ed25519_gitlab_work
```

## 4. Aggiungi la chiave pubblica all'host

Copia il contenuto della chiave pubblica:

```bash
cat ~/.ssh/id_ed25519_github_personal.pub
```

Poi incollala nell'interfaccia web dell'host:
- **GitHub**: `Settings → SSH and GPG keys → New SSH key`
- **GitLab**: `Preferences → SSH Keys → Add new key`
- **Bitbucket**: `Personal settings → SSH keys → Add key`

## 5. Testa la connessione

```bash
ssh -T git@github.com
# Hi stevinc! You've successfully authenticated...

ssh -T git@gitlab.azienda.com
# Welcome to GitLab, @stevinc!
```

Da questo momento ogni `git push/pull` usa automaticamente la key giusta in base all'host.
