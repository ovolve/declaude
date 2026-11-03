---
name: declaude
description: >-
  Rewrite Claude-generated or LLM-styled text (documentation, articles, READMEs,
  implementation plans, figure captions, and code comments) into natural, clear,
  grounded technical prose. Use when the user asks to "declaude" text, fix
  Claude-ese/AI prose, or rewrite unnatural LLM writing into human-sounding
  technical communication.
---

# De-Claude: Rewriting LLM Prose into Natural Technical Writing

Use this skill when rewriting existing prose—articles, READMEs, implementation plans, design specs, UI annotations, figure captions, or code comments—that exhibits Claude's characteristic syntactic and rhetorical tics.

Your goal is to transform stiff, faux-plain, or artificially dramatic LLM text into the natural, grounded register of a clear textbook, RFC, or experienced systems engineer, while keeping every technical fact, equation, code block, and markup element intact.

---

## Rewriting Workflow

### Step 1: Identify All Prose Surfaces and Preserve Structure
- When rewriting a document or UI specimen, check both the primary markup/markdown file and any companion scripts or data files that hold user-facing prose (such as figure captions, tooltips, or move/step annotations).
- Preserve all structural elements, HTML tags, CSS classes, IDs, math blocks, and code blocks unless asked to change them.

### Step 2: Abandon the Original Sentence Skeletons (Do Not Just Patch Words)
Swapping vocabulary or punctuation inside a Claude sentence skeleton leaves the ghost of Claude's syntax behind. Claude habitually:
1. **Chops a single continuous thought into two or three disconnected `Subject–Verb–Object` sentences** (`"The C engine uses one rule... That rule comes from... There it only caps..."`).
2. **Inverts information order**, cramming prerequisite context into a backward-looking relative clause at the end (`"...only caps a search whose length the machine's level already sets"`) or wedging multi-token code syntax into the subject before explaining what it does (`"The return err from load statement..."`).

When rewriting:
- **Merge choppy sentence chains** and reorder clauses so the **known premise or baseline mechanism comes first**, followed by the qualifier or consequence.
- **State the concept before the syntax:** Describe what the program or programmer can do in plain English first, and place the code expression at the right edge of the clause (`"...using return err from load"`).

- **Claude Original:**
  > The C engine in `decomp/` uses one rule to turn the GUI's clock into a time limit for each search. That rule comes from the Native mode of the emulated engine in `engine/`. There it only caps a search whose length the machine's level already sets.
- **Bad Rewrite (Word-patch keeping Claude's skeleton):**
  > In that mode, the limit acts only as an upper bound on a search whose duration is already governed by the machine's level.
- **Good Rewrite (Re-architected information flow):**
  > To calculate per-move time limits from the GUI clock, the C engine in `decomp/` currently reuses the Native-mode formula from the emulated engine in `engine/`. In the emulator's Native mode, however, the hardware level setting already controls how long a search runs, and the GUI time limit serves only as a safety cap.

---

## Grammatical Tics to Hunt Down and Replace

### 1. Contorted `OSV` Relative Clauses Born of Passive-Voice Phobia
Claude avoids the passive voice even when an inanimate noun is the natural grammatical patient, producing awkward Object–Subject–Verb relative clauses (`that [Subject] [Verb]s` or `whose [Noun] [Subject] [Verb]s`). Convert these into natural passive voice (`is governed by`, `is omitted`) or past-participial modifiers (`selected by`, `used in`, `clamped to`).

- **Claude:** `Times are in seconds of the clock that the Clock option selects. "Remaining time" means the side's clock (wtime or btime) minus Move Overhead, but never less than 0.`
- **Rewrite:** `All durations are measured in seconds on the clock selected by the Clock option. Remaining time is wtime or btime minus Move Overhead, clamped to a minimum of zero.`
- **Claude:** `The emulated engine's README doesn't state the rule that its Managed mode uses.`
- **Rewrite:** `The emulated engine's README does not document the time-allocation formula used in Managed mode.`

### 2. Metonymic Personification of Flags, Levels, Commands, Files, and Docs
Claude forces configuration options, numeric levels, CLI commands, opening books, and table rows to act as sentient subjects of human verbs (`level 8 searches`, `a level that times itself`, `the book answers at once`, `a go that gives no time`, `tc=inf needs`, `the row says`, `whatever Level says`). Restore the actual actor (the engine, the compiler, the caller, the user) or use a passive construction.

- **Claude:** `On level 3 the book would answer at once, but on level 8 the engine waits for stop. ... That is what level 2 does: its book answers at once.`
- **Rewrite:** `At levels 2 and 3, the engine plays opening-book moves immediately, whereas at level 8 it waits for an explicit stop command before returning bestmove.`
- **Claude:** `Managed mode limits a go that gives no time. ... The check doesn't measure the 10 s: the unthrottled clock counts estimated machine time, and 10 s of it passes in far less than a second of real time.`
- **Rewrite:** `When the GUI sends a bare go command without clock parameters, Managed mode defaults to a 10-second search limit. Because the test runs with the unthrottled virtual clock—which advances in simulated machine cycles rather than wall-clock time—those 10 virtual seconds elapse in a fraction of a second of real time.`

### 3. Post-Verbal `no` Negation (`[verb] no [noun]`)
To shave off words and avoid auxiliary verbs, Claude attaches `no` directly to the object of a finite verb (`needs no unwinder`, `sends no movestogo`, `reads no more input`, `gives no time`, `takes no -D of its own`, `produces no warnings`). Aside from fixed idioms (`no longer`, `makes no difference`), replace every instance with standard auxiliary negation (`does not require`, `does not send`, `is not specified`) or `without` (`without stack unwinding`, `when movestogo is omitted`).

- **Claude:** `return err from load returns from a function any number of frames up the call stack. It is checked statically, and needs no unwinder, no Result type and no ? on every call.`
- **Rewrite:** `A helper function can return directly out of an enclosing caller several frames up the call stack using return err from load. Because the compiler verifies at compile time that every call path to the helper originates inside load, errors can propagate across call frames without runtime stack unwinding or Result return types.`
- **Claude:** `During such a search, a position, go, setoption or ucinewgame command makes the main thread wait for the search to end, and the main thread reads no more input while it waits.`
- **Rewrite:** `If the GUI sends position, go, setoption, or ucinewgame while an infinite search is running, the main thread blocks waiting for the search thread to finish and cannot read any further commands—including stop—from standard input.`

### 4. Faux-Plain Baby-Talk Verbs, Placeholder Nouns, and `what`/`whatever` Relatives
Claude replaces normal Latinate/technical vocabulary (*determine*, *allocate*, *omit*, *require*, *cache*, *duration*, *formula*, *edge cases*, *build artifacts*) with colloquial Anglo-Saxon monosyllables (`work out`, `go in`, `leave out`, `put`, `hold`, `show`, `touch`, `buy`, `give up`), elementary count-noun announcements (`three facts`, `two things`, `one rule`, `the thing they said`), and free-relative clauses (`keeps what it builds`, `stored in whatever holds it`, `where the speed comes from`). Replace them with exact technical terminology.

- **Claude:** `A program has the native call stack, static data and N data stacks, where the compiler works out N. A data stack is a large address-space reservation with a bump pointer, and there is no other memory.`
- **Rewrite:** `Instead of a general-purpose heap, a Goose program allocates all dynamic data on a fixed number of bump-pointer stacks, with the number of stacks determined statically by the compiler.`
- **Claude:** `Unpack one anywhere and put its directory on PATH: the compiler finds the standard library, TinyCC's runtime and the packages beside itself, and keeps what it builds under ~/.goose. ... Without them the build leaves the gfx module out and says what to install.`
- **Rewrite:** `Once you extract the archive and add its directory to PATH, the compiler locates the bundled standard library and runtime relative to its own binary and caches compiled artifacts in ~/.goose. ... If the X11 or Wayland headers are missing, CMake skips the gfx module and prints the required package names.`

### 5. Two-Stroke `, so` and `, and` Paratactic Chains
Claude glues flat `Subject–Verb–Object` clauses together with `, so` or `, and` (`[Flat fact], so [flat consequence]`) dozens of times per page instead of subordinating the cause. Convert these into left-branching subordinate clauses (*Because...*, *Since...*, *Although...*, *When...*, *If...*) or split into two coherent sentences where the first establishes the property and the second states what it enables.

- **Claude:** `The tree's links are self-relative, so the tree means the same thing wherever it is stored. Saving it writes its bytes and loading it reads them back, with no serializer, no schema and no pointer fixups.`
- **Rewrite:** `Because links within the tree are stored as relative byte offsets rather than memory addresses, the entire structure is position-independent. It can be written to disk and read back into memory as a contiguous byte buffer without serialization or pointer fixups.`

### 6. Unanchored `A [noun] [verb]` Loops and Determiner-Dropped Bare Plurals
- **Indefinite singular loops:** While `A [noun] [verb]...` is fine once to introduce a formal definition, Claude starts sentence after sentence with a new hypothetical singular specimen (`"A program has... A data stack is... A reference stays... A worker is..."`). Keep the first definition if appropriate, then switch subsequent sentences to definite reference (`the stack`, `it`) or prepositional framing (`"Within each worker thread, ..."`).
- **Telegraphic determiner-dropping:** In captions, comments, and body text, Claude drops articles on specific plural or mass nouns (`"Arrows show the next move."`, `"Evaluations are illustrative."`, `"Data is illustrative."`, `"Engines handle the king case."`). Add definite determiners (`"Each board arrow..."`, `"Both charts..."`) and fold standalone disclaimers (`"Evaluations are illustrative."`) into the preceding sentence. (Compact fragments without articles are acceptable only inside narrow table cells or CLI flag summaries.)

- **Claude:** `The bar beside the board and the curve below the moves both show White's winning chances from equation (2). Arrows show the next move: green when it is sound, red when it is a mistake. Evaluations are illustrative.`
- **Rewrite:** `The vertical gauge beside the board and the chart below the move list both plot White's estimated win probability from equation (2), using illustrative evaluations. On the board, each arrow marks the move about to be played from the current position, colored green for sound moves and red for mistakes.`

### 7. Paratactic Colons, Copular Flatness, and Trailing `, which` Clauses
- **Explanatory colons:** Remove colons used in running prose as a substitute for *because*, *since*, or *namely* (`"Evaluating every leaf is hopeless: with about 35 legal moves..."`). Keep mid-sentence colons **only** when explicitly announcing a set immediately before listing its members (`"The TimeMode option accepts two values: Native and Managed."`), or when introducing display math, code blocks, and vertical lists.
- **Copular flatness:** Replace gerund subjects and linking verbs (`"Plotting W along a game makes blunders easy to spot"`, `"Scoring from the perspective of the side to move gives..."`) with subordinate framing and finite technical verbs.
- **Trailing `, which` and "see-saw" clauses:** Fold sentential `, which` tails (`"...which makes the curve steeper"`, `"...which is rare enough not to matter"`) into the main clause or a participial/appositive phrase, and rewrite mirrored mini-clauses (`"Saving it is a single write, and loading it is a single read"`).

### 8. Rhetorical Antithesis, Coy Headings, and Compulsive Tricolons
- **Negative-positive antithesis (`"Not X. It's Y."`):** Collapse two-sentence dramatic pivots (`"A score of +1.50 does not mean X. It means Y."`, `"Humans do not have principles; they have loyalties."`) into a single subordinated sentence using *although* or *rather than*.
- **Coy or oblique headings:** Replace magazine-style headings (`"Inside the evaluation function"`, `"Search, briefly"`, `"Centipawns are probabilities"`, `"What a better evaluation buys"`, `"Where the speed comes from"`) with descriptive noun phrases or explicit trade-offs (`"Position evaluation in chess engines"`, `"Minimax and alpha-beta pruning"`, `"From centipawns to win probability"`, `"Evaluation speed versus accuracy"`).
- **Editorializing lead-ins:** Delete throat-clearing sentences that brag about simplicity or drama (`"The implementation is shorter than its explanation."`, `"NNUE won that trade decisively."`, `"Read this first."`).
- **Compulsive lists of three:** Do not replace Claude's prose with repetitive three-item lists (`"How X, how Y, and how Z"`). Use a three-item list only when the domain genuinely has three elements (`Windows, macOS, and Linux`), and never place three-part parallel lists in adjacent paragraphs.

---

## Post-Rewrite Audit Checklist

Before finishing a rewrite, scan your output and verify every item:
1. **No leftover sentence skeletons:** Did you actually restructure choppy `S–V–O` chains so premises precede consequences, rather than merely swapping synonyms inside Claude's sentences?
2. **No pre-nominal code stuffing:** Are multi-token code expressions placed at the right edge of the clause rather than wedged between `The` and a noun?
3. **Zero `[verb] no` negations:** Search for `needs no`, `sends no`, `reads no`, `gives no`, `takes no`, `produces no`.
4. **Zero `that the ...` / `whose ...` passive-avoidance clauses:** Ensure inanimate targets use passive or participial modifiers (`selected by`, `governed by`).
5. **Zero personified flags/levels/docs:** Check that levels, options, rows, and commands are not the subjects of `searches`, `answers`, `says`, `gives`, `needs`, or `times itself`.
6. **Zero explanatory colons or bare-plural sentence starts:** Check every colon in body text and ensure specific nouns in captions and prose have determiners (`The`, `Each`, `Both`).
7. **No tricolon pileups:** Verify that your rewrite did not introduce back-to-back `"A, B, and C"` or `"how A, how B, and how C"` lists.
