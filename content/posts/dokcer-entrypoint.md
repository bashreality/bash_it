+++
author = "Albert Milewski"
title = "Docker Entrypoint"
date = "2024-05-16"
description = "Sample article showcasing basic Markdown syntax and formatting for HTML elements."
tags = [
    "markdown",
    "css",
    "html",
]
categories = [
    "themes",
    "syntax",
]
series = ["Themes Guide"]
aliases = ["migrate-from-jekyl"]
+++


# Używanie ENTRYPOINT w docker-compose

Możesz także nadpisać ENTRYPOINT w pliku `docker-compose.yml`. Jest to przydatne, gdy chcesz uruchomić kontener z niestandardowym poleceniem startowym.

## Przykład

```yaml
version: '3'
services:
  my_service:
    image: my_image
    entrypoint: /path/to/my_entrypoint.sh
    volumes:
      - ./my_entrypoint.sh:/path/to/my_entrypoint.sh
```

## Przykład praktyczny: Ustawienie konfiguracji Git w kontenerze

Utwórz skrypt `entrypoint.sh` w katalogu z `docker-compose.yml`:

```sh
#!/bin/sh

# Ustawienia Git
git config --global http.sslVerify false

# Uruchomienie głównej aplikacji
exec /usr/local/bin/semaphore-wrapper /usr/local/bin/semaphore server --config /etc/semaphore/config.json
```

Nadaj skryptowi uprawnienia do wykonywania:

```sh
chmod +x entrypoint.sh
```

Zaktualizuj `docker-compose.yml`, aby używał skryptu entrypoint:

```yaml
version: '3'
services:
  semaphore:
    image: semaphoreui/semaphore:v2.9.75
    entrypoint: /entrypoint.sh
    ports:
      - "3000:3000"
    volumes:
      - ./entrypoint.sh:/entrypoint.sh
```

Uruchom ponownie kontenery:

```sh
docker-compose up -d
```

## Wyjaśnienie

- Skrypt `entrypoint.sh` ustawia konfigurację Git i uruchamia główną aplikację Semaphore.
- Plik `entrypoint.sh` powinien znajdować się w tym samym katalogu co `docker-compose.yml` i mieć uprawnienia do wykonywania.
- W pliku `docker-compose.yml` ustawiamy `entrypoint`, aby wskazywał na nasz skrypt i montujemy ten skrypt do kontenera za pomocą `volumes`.
- To zapewnia, że konfiguracja Git zostanie ustawiona przed uruchomieniem głównej aplikacji w kontenerze.
