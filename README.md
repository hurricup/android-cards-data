# Android Cards — questionary data

XML flashcard decks ("questionaries") for the [Android Cards](https://github.com/hurricup/android-cards)
app. Mounted in the app repo at `app/src/main/assets/xml/` (this is a separate git repo). The app parses
these files on startup and turns each into a deck of cards.

## Layout

Files are organized into language subdirectories; the app scans them **recursively**, so nesting depth
is free to change:

```
am/   Armenian decks (alphabet, numbers, words, grammar, classes, texts, …)
en/   English decks (Oxford A1–C1)
ru/   Russian decks (A1–B2)
```

## File format

Each file is one or more `<questionary>` elements. A questionary either lists its own cards **or**
references other questionaries (composite) — not both.

### Card deck

```xml
<?xml version="1.0" encoding="utf-8"?>
<questionary>
    <title>Глаголы (арм.)</title>   <!-- shown on the button -->
    <id>Армянские глаголы</id>       <!-- optional; stats key, defaults to <title> -->
    <cards>
        <card>
            <question>փորձել</question>
            <answer>пробовать</answer>
        </card>
    </cards>
</questionary>
```

- **`<title>`** — required, the button label.
- **`<id>`** — optional. The stable key statistics are stored under. **Defaults to `<title>`.** Once a
  deck has been used, keep its `id` stable or existing progress is orphaned. `id` also identifies the
  deck when referenced by a composite.
- **`<question>` / `<answer>`** — the card. `<answer>` may be omitted (prompt-only cards).
- **`<questionNote>` / `<answerNote>`** — optional help text shown in small print under the
  question/answer. They don't affect stats and are swapped along with question/answer in reverse decks.

### Variants (`|`)

A `|` in a question or answer expands into a cross-product of variants:

```xml
<card><question>кот|кошка</question><answer>կատու</answer></card>
```

produces cards for both `кот → կատու` and `кошка → կատու`. Cards that end up with the same key side are
merged and the other side joined with `; `. A note either has a **single** variant (broadcast to all
variants of its side) or **exactly one variant per field variant** — any other count rejects the file.

### Composite deck

Aggregates other questionaries by `id` (they keep their own stats):

```xml
<questionary>
    <title>Армянский (всё)</title>
    <id>Армянский (микс)</id>
    <questionaries>
        <id>Армянские глаголы</id>
        <id>Армянские числа</id>
    </questionaries>
</questionary>
```

Missing references are skipped; reference cycles are broken.

## Notes

- Every deck automatically gets a **reverse** variant (question/answer swapped) and a **mixed** variant
  in the app — no markup needed.
- The app applies light typography: `--` → `—`, `...` → `…`.
- Consistency (duplicate questions/answers across files, including variant overlaps) is checked by
  `XmlConsistencyTest` in the app repo.
