# Tinkerer — stevinc.github.io

Blog personale su AI, LLM e software engineering. Costruito con [Hugo](https://gohugo.io) e il tema [PaperMod](https://github.com/adityatelange/hugo-PaperMod).

## Installazione

```bash
brew install hugo
```

Clona il repo con i sottomoduli (il tema è un submodule):

```bash
git clone --recurse-submodules https://github.com/stevinc/stevinc.github.io.git
cd stevinc.github.io
```

## Uso

**Anteprima locale:**

```bash
hugo server -D
# apri http://localhost:1313
```

**Nuovo articolo:**

```bash
hugo new content/posts/nome-articolo.md
```

**Build statica:**

```bash
hugo
# output in ./public/
```

## Articoli

| Titolo | Tag |
|---|---|
| [Hello World](content/posts/hello-world.md) | meta, intro |
| [SSH keys per host multipli](content/posts/ssh-keys-multiple-hosts.md) | git, ssh, til |
| [Git: aggiornare un token HTTPS per un singolo repository](content/posts/git-token-per-repository.md) | git, til, sicurezza |
