**English** | [Русский](README.ru.md) | [Українська](README.uk.md)

# Open Mafia Research

Open Mafia Research is an open library of research on sports Mafia.

## What it is for

The library collects studies of the game: statistics of game outcomes, role balance, the behaviour of judging systems, the properties of rating models and anything else that can be measured and checked on real games.

Every study states what data it is based on, how the data was processed and what exactly was found. Conclusions are separated from assumptions, and limitations are stated openly.

The studies use the sports Mafia format: a table of 10 players (1 don, 2 mafia, 1 sheriff, 6 civilians), a team result for every game and an individual score from the judge. A study that uses a different format says so explicitly.

## Studies

| Study | Topic | Date | Authors |
|---|---|---|---|
| *No studies published yet* | | | |

## Structure

```
research/
  short-slug/              one study
    README.md              English version
    README.ru.md           Russian version
    README.uk.md           Ukrainian version
    …                      any other files the study needs (data, figures, code)
```

How to add a study: [CONTRIBUTING.md](CONTRIBUTING.md).

## License

Licensed under the [Apache License 2.0](LICENSE). See [NOTICE](NOTICE) for authorship.
