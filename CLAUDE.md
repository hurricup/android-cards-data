# Working in this repo (questionary data)

This is a **separate git repo** mounted inside the Android Cards app at `app/src/main/assets/xml/`.
It holds only the flashcard data (XML decks). Code lives in the app repo. Commits to the XML files go
**here**, not in the app repo.

See `README.md` for the full file format. Key rules to respect when editing:

## Editing rules

- **Never change a deck's `id` once it's in use.** Stats are keyed by `id` (falling back to `<title>`
  when `<id>` is absent), per question text. Changing `id` — or changing `<title>` on a deck without an
  explicit `<id>` — orphans all existing progress for that deck. Rename the `<title>` freely; keep the
  `<id>` fixed.
- **Question text is the per-card stats key.** Editing a question's text is effectively a new card
  (loses that card's progress). Fixing an answer or a note is safe.
- `<questions>`/`<cards>` and `<questionaries>` (composite) are **mutually exclusive** in one
  questionary — the parser ignores own cards if references are present.
- **`|` is a variant operator**, not a literal. To write a literal pipe, don't — pick different wording.
- **Notes must align**: a `<questionNote>`/`<answerNote>` has either a single variant (broadcast) or
  exactly one per `|`-variant of its side; anything else makes the app reject the whole file.
- Keep files valid UTF-8. Armenian marks (emphasis `՛`, question `՞`, combining accents) may appear
  inside words; that's fine.
- Organize files under language subdirs (`am/`, `en/`, `ru/`, …). The app scans **recursively**, so new
  subfolders are picked up automatically.

## Cross-references (app repo)

- Parser + format authority: `model/Questionary.kt` (`parseQuestionary`, `processVariants`).
- Stats key routing: `model/TierScheduler.kt` / `TierStore.kt` (keyed by questionary `id` → question text).
- Consistency guard: `XmlConsistencyTest` — flags duplicate question/answer pairs across all files,
  including `|`-variant overlaps. Run the app's tests after bulk edits.

## Commits

- Atomic, one logical change each, IntelliJ-style messages (see the app repo's global conventions).
- This repo has its own history/remote; push it separately from the app repo.

## Helper

- `tools/new_words.sh` in the app repo extracts words from stdin text that are **not** yet present in
  these decks (with Armenian morphology-aware matching) — useful when adding vocabulary.
