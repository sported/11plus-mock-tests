# Mock Test Generation Guide

This is the standard process I follow whenever asked to create a new mock test for the 11+ platform. It covers what to ask before generating, and the quality bar to hold content to once generating.

---

## 1. Before generating — required info

Never generate a test on assumptions alone. If any of these are missing from the request, ask:

| Field | Why it's required | Example |
|---|---|---|
| **Subject** | Must be exactly one of: `maths`, `english`, `verbal_reasoning`, `non_verbal_reasoning`, `spellings` | "Maths" |
| **Level / stage** | Drives both difficulty and vocabulary. Ask for year group *and* where in the year, e.g. "Year 4, late" or "Year 5, first term" — not just "Year 4" | "Year 5, first term" |
| **Target vs. current ability** | 11+ prep often means working *above* current school year. Ask: should this match where he currently is, or stretch toward 11+ exam standard? | "Stretch — 11+ standard, a bit above his current year" |
| **Topics to focus on (or avoid)** | Prevents accidental overlap with existing tests and lets the parent target a known weak spot | "Focus on fractions and ratio, skip algebra for now" |

## 1a. Before generating — check his actual recent performance (always, automatically)

This is not something to ask about — it's a step I do myself, every time, before drafting any questions. Explicit topic requests from the parent (above) take priority over this; this step fills in the gaps and weights the rest.

**Query recent performance for the subject in question:**

```sql
select q.topic,
  count(*) as questions_answered,
  count(*) filter (where aa.is_correct) as correct,
  round(100.0 * count(*) filter (where aa.is_correct) / count(*), 1) as accuracy_pct
from attempt_answers aa
join questions q on q.id = aa.question_id
join attempts a on a.id = aa.attempt_id
join students s on s.id = a.student_id
join mock_tests mt on mt.id = q.mock_test_id
where s.name = '<student name>'
  and mt.subject = '<subject>'
  and a.submitted_at > now() - interval '60 days'
group by q.topic
order by accuracy_pct asc;
```

**How to use the result — two dimensions, quantity AND difficulty:**

- **Under ~10 total answered questions in that subject** (not enough signal yet) — skip weighting, generate a normal balanced spread across topics at standard difficulty, and say so in the summary ("not enough attempt history yet to weight this one toward weak spots").
- **Topics below ~70% accuracy** — more questions (roughly 1.5–2× their normal share), at standard difficulty for the stated level. The goal here is fluency and consolidation, not stretching — don't make weak-topic questions harder, that just compounds the struggle.
- **Topics at 90%+ accuracy** — fewer questions, but noticeably **harder** ones, not just the same difficulty in smaller quantity. He's already demonstrated mastery of the basics here, so questions in a strong topic should stretch him rather than recap what he's already proven he can do. Concretely, "harder" means one or more of:
  - Multi-step versions instead of single-step (e.g. a two-part journey speed problem instead of one direct speed calculation)
  - Less friendly numbers (decimals, larger values, non-round percentages) instead of clean ones
  - Combining the topic with a second skill (e.g. a ratio question embedded inside a money word problem)
  - Defaulting these to the boss round (worth double points/XP) rather than the normal-difficulty section, since they're functioning as the stretch content for that paper
  - Less common vocabulary or less obvious phrasing, where relevant (verbal reasoning especially)
- **Topics never attempted before** — treat as normal/unweighted at standard difficulty, since there's no evidence either way.
- Only weight *within* the subject being generated — being weak at Spellings shouldn't skew a Maths paper's topic mix.
- This query groups by exact `topic` string — it only works if topic names are used consistently across tests. Always draft new questions using the canonical topic names in section 1c, not ad-hoc labels.

**Report both dimensions in the summary** when handing back the finished test, e.g.: "Weighted toward Fractions and Ratio (58% and 63% accuracy) with standard-difficulty questions to build fluency. Angles (94% accuracy) got fewer questions but pushed harder — multi-step problems in the boss round instead of single-step ones." This makes the adaptivity visible rather than silent, on both axes.

## 1b. Reference material — a real 11+ paper for calibration

A real CGP 11+ Verbal Reasoning paper ("Assessment Test 7") was provided 2026-07-09 and is saved in the GitHub repo at `reference-material/` (index in `reference-material/README.md`). **Check this before generating any Verbal or Non-Verbal Reasoning test** — it's the actual target difficulty and format, and it's noticeably more varied than plain synonym/antonym/analogy multiple choice.

**Rule: reference for style, format and difficulty only. Never copy the specific words, letters, numbers, or scenarios from these images into a generated test — always write fresh original content in the same pattern.**

**Puzzle types shown, and how each maps onto this platform's mcq/input format** (this platform only supports single-answer mcq or single-value input, so some real puzzle types need adapting):

| Real puzzle type | Platform mapping |
|---|---|
| Missing number in an equation | `input`, numeric answer |
| "Mark two words, one from each bracket, that are most opposite" | Simplify to `mcq`: "Which word means the opposite of X?" with 4 options — same skill, one less step |
| Letter-value arithmetic (A=2, B=4, work out C×A−D) | `input`, numeric answer |
| 3-letter word completes a capitalised fragment, sentence must make sense | `input`, the 3-letter answer |
| "Which words are NOT related" from a group of 5 (2 unrelated) | Simplify to `mcq`, 5 options, single odd-one-out — avoids a fragile two-part answer |
| Letter-pair alphabet sequences (apply a consistent shift rule) | `input`, 2-letter answer — write an original shift rule, don't reuse the book's exact letters |
| Remove one letter from word A, add to word B, name both new words | Simplify to `mcq`: "which letter moves?" — avoids a fragile multi-part text answer |
| Word-to-number code (shared letters across several words) | `input` — maps directly, no adaptation needed |
| Logic deduction, 5 statements, "only one can/must be true" | `mcq` with 5 options — maps directly, this platform has no fixed option-count limit |
| Word transformation (infer a rule from 2 examples, apply to a 3rd) | `input` — maps directly |

When a generation request doesn't specify strict MCQ, default to this richer mix rather than only synonym/antonym/analogy MCQs — it's a better match for the real exam.

## 1c. Canonical topic list — reuse these names, don't invent near-duplicates

This is the actual list of topic names used across all tests so far (cleaned up 2026-07-10 — "Word problems"/"Word Problems", "Opposite Meaning"/"Antonyms", and "Fractions"/"Fractions & decimals" had already fragmented into separate topics before this list existed, which silently broke the weighting logic in 1a since it groups by exact topic string). **Always check this list first and reuse an existing name if the skill matches, rather than inventing a new label.** If a genuinely new topic is needed, add it here too so the next test reuses it.

**Maths:** Arithmetic, Place value, Rounding, Addition & subtraction, Multiplication & Division, Times tables, Fractions, Percentages, Ratio, Algebra, Sequences, Perimeter & Area, Shape, Angles, Measurement, Time, Speed, Distance & Time, Money, Averages, Statistics, Word Problems, Negative numbers

**Verbal Reasoning:** Synonyms, Antonyms, Analogies, Odd One Out, Word Groups, Hidden Words, Word Completion, Word Transformation, Letter Sequences, Letter-Value Arithmetic, Letter Move, Missing Number, Coded Words, Logic, Number Sequences, Compound Words, Number Analogies, Insert a Letter

**English:** Reading Comprehension, Poetry, Vocabulary & Technique, Grammar, Punctuation, Spelling, Cloze & Word Choice

**Non-Verbal Reasoning** (provisional — no tests generated yet, refine once real content exists): Sequences, Odd One Out, Analogies, Pattern Rules, Coded Patterns, Grouping

**Spellings** (provisional — no tests generated yet, refine once real content exists): Common Misspellings, Homophones, Silent Letters, Plurals, Prefixes & Suffixes, Tricky Words, Compound Words

## 1d. Target schools — what they actually test (researched 2026-07-10)

The stated targets are Kent grammar schools, St Olave's (Orpington), Eltham College, and Sevenoaks — researched via official school/council pages, independent 11+ guides, and forum/parent discussion (The Student Room, tutoring-company breakdowns of past papers). Findings below, so content is pitched at what he'll actually sit rather than generic 11+ material.

**Kent Test** (all 32 Kent grammar schools, single registration covers all of them):
- Provider: GL Assessment. Two one-hour multiple-choice papers plus an unmarked writing task (only reviewed for borderline/appeal cases).
- Subjects: **English, Maths, and Verbal Reasoning only — no Non-Verbal Reasoning.** This is a genuine Kent-specific quirk; most other GL regions test all four.
- Paper 1: one hour, split into two 25-minute sections (English + Maths), each with a 5-minute practice exercise first.
- Paper 2: one hour, Reasoning, split into three sections.
- Pass threshold: total aggregate score of 332+, with no individual subject below 106 (2026 figures — thresholds are recalculated yearly and not published in advance).
- Maths goes beyond standard Year 6 curriculum into ratio, algebra, and percentage change. English follows GL's standard 8-comprehension-question-type format plus standalone vocabulary/spelling/punctuation/grammar sections.
- A few individual Kent schools (Weald of Kent, Dover Boys/Girls, Harvey Grammar, Folkestone Girls) offer bespoke alternative tests instead of/alongside the Kent Test — not relevant unless specifically asked about.

**St Olave's Grammar School, Orpington** (boys, highly selective, no catchment area):
- Two-stage process, own bespoke test (not part of the Kent consortium).
- **Stage 1 (SET):** one hour, ~60 multiple-choice questions across 4 sections covering **English, Maths, Verbal Reasoning, and Non-Verbal Reasoning together.** Top ~450 scorers advance.
- **Stage 2:** two one-hour papers same day (15-min break between). English paper: reading comprehension (multiple choice) + a creative/persuasive writing task. Maths paper: KS2 content but harder than usual, and — importantly — **not multiple choice, full written answers required.** Forum/tutor sources consistently flag Stage 2 maths as "questions harder than they're used to."
- Tie-break order: maths score, then writing, then English comprehension, then the original SET.
- English comprehension specifically tests vocabulary, grammar, inference, and factual recall — matches the reference paper style already saved.

**Eltham College, Mottingham** (co-ed independent day school, own bespoke exam):
- Written English paper (45 min): comprehension + analysis of a text passage, plus a creative writing task.
- Written Maths paper (45 min): **mixed format** — a multiple-choice section plus longer problem-solving questions requiring full written working. No calculators allowed.
- Verbal and Non-Verbal Reasoning: **CEM Select, a computer-adaptive test** (question difficulty adjusts based on previous answers; students aren't expected to finish every question). This is a genuinely different testing model from GL's fixed-paper format — can't be replicated exactly on this platform, but the underlying VR/NVR skill content is the same.
- Maths prep sources specifically flag algebra, geometry, and number theory as going beyond the basics.

**Sevenoaks School** (co-ed independent, day and boarding):
- Pre-test stage: **ISEB Common Pre-Test** (online adaptive, covers Maths/English/VR/NVR) — or a Kent Test/other grammar school score can be substituted if the child's school doesn't offer ISEB.
- Second stage (assessment day): written Maths and English papers, plus a 40-minute group discussion (not something this platform can replicate).
- **Distinctive and important: Sevenoaks' own papers are NOT multiple choice.** "Simple question and answer format... pupils have to work out the actual answer themselves" — this is explicitly different from the Kent Test/ISEB style, and is the strongest real-world justification on this platform for keeping meaningful input-type (not just MCQ) questions in maths and English, especially for anything pitched at this specific school.
- English paper has a substantial written composition component (40-50% of marks) — beyond what this platform's format can assess directly, but comprehension and technical English (grammar/vocabulary) remain directly relevant.
- No Non-Verbal Reasoning in Sevenoaks' own papers (only in the ISEB pre-test stage, which is a different provider's test).

**What this means for generation:**
- English and Maths content should assume KS2-plus-stretch level across the board — all four schools go beyond standard Year 6 curriculum in some way (ratio/algebra/percentage change for Kent; "harder than usual" for St Olave's Stage 2; algebra/geometry/number theory for Eltham; unstructured problem-solving for Sevenoaks).
- Verbal Reasoning is tested by three of the four schools (not Sevenoaks' own papers, though it appears in the ISEB pre-test route) — worth continued strong investment.
- Non-Verbal Reasoning is notably **absent from the Kent Test** specifically — if a test is explicitly "for the Kent Test," NVR can be deprioritised; if it's for St Olave's, Eltham, or general 11+ prep, NVR should stay in rotation.
- Don't default everything to MCQ. Sevenoaks and St Olave's Stage 2 use full written-answer formats for Maths (and Sevenoaks for English too) — `input`-type questions aren't just a grading convenience on this platform, they're a genuine match for what he'll actually face at more than one of these schools.
- Time pressure is extreme in GL-style VR papers specifically (37-45 seconds per question, ~80 questions in 50-60 min) — when timing a VR test, don't be overly generous; tight timing is part of the actual skill being tested.

**The 21 official GL Assessment Verbal Reasoning question types** (this is the authoritative structure behind Kent Test, St Olave's SET, and most GL-based VR papers nationally — cross-check new VR tests against this list for coverage, not just the canonical topic names in 1c):

1. Insert a Letter (completes both a word before and after brackets)
2. Two Odd Ones Out (find 2 of 5 that don't belong — not just 1)
3. Related Words / Alphabet Code (letter-shift ciphers)
4. Closest in Meaning (Synonyms)
5. Hidden Word (a word hidden across two adjacent words in a sentence)
6. Missing Word (3 consecutive letters removed from a capitalised word, sentence must make sense)
7. Letters for Numbers (letter-value arithmetic)
8. Move a Letter (remove from word A, add to word B, forms two new words)
9. Letter Series (alphabet sequences)
10. Word Analogies / Word Connections
11. Number Series
12. Compound Words (combine one word from each of two groups) — **not yet in any generated test, add next time a VR paper is built**
13. Make a New Word (pattern from two example word-pairs, apply to a third)
14. Letter Combinations (paired-letter alphabet relationships)
15. Applying Logic to Written Information
16. Antonyms
17. Find the Sum (missing-number equations)
18. Matching Number Sets (a pattern/operation linking pairs of numbers, apply to a third pair) — **not yet in any generated test, add next time**
19. Word/Number Codes (shared-letter code-breaking across several words)
20. Complete the Word (three word-pairs, same transformation rule, find the missing piece)
21. Same Meaning (pick one word from each of two groups that share a meaning with a target word)

**Guardrail (found by real mistake, 2026-07-10):** the numeric/pattern-flavoured VR types — Missing Number, Letter-Value Arithmetic, Letter Sequences, Number Series — are legitimate official GL types, but a VR paper that leans on them too heavily reads as "just doing sums" rather than verbal reasoning, and crowds out the word-based types (Synonyms, Antonyms, Analogies, Word Groups, Hidden Words) that are the actual core of the subject. A real VR paper generated this way ended up with 40% of its questions numeric-flavoured and zero Synonyms, Analogies, Word Groups, or Hidden Words — not caught until the parent noticed it felt calculation-heavy. **Keep the numeric-flavoured types combined to roughly 15-20% of a VR paper**, and always include at least one question from each of Synonyms, Antonyms, Analogies, Odd One Out, and Hidden Words/Word Groups unless a specific request says otherwise.

## 2. Other questions worth asking (if not specified)

- **Number of questions** — default to 25 unless told otherwise.
- **Time limit** — default to roughly 1 minute/question for straightforward recall (e.g. spellings, arithmetic), longer for multi-step word problems (~90 sec/question). State the assumed default rather than always asking.
- **Title** — I'll propose one (e.g. "11+ Maths Practice Paper 2") unless they want a specific name.
- **Exam style / board** — UK 11+ papers vary by region and board (GL Assessment vs CEM/ISEB style differ especially for verbal/non-verbal reasoning). Worth asking once if it matters to them; otherwise default to a general, widely-used style.
- **For Non-Verbal Reasoning specifically** — flag that this platform is text-only. True non-verbal reasoning is image/pattern-based, which isn't something I can render here. I substitute text-described logic puzzles (sequences, odd-one-out, analogies) as the closest proxy, and should say so explicitly rather than pretend it's equivalent.
- **MCQ vs typed-answer mix** — **default to multiple choice.** MCQ avoids the whole class of grading ambiguity that typed answers invite (units, phrasing, formatting, minor typos — see the units grading bug fixed on 2026-07-07 for a real example). Use typed input only when MCQ genuinely doesn't fit:
  - **Spellings** — the point of the question is typing the word correctly, so input is the right format there.
  - **A handful of calculation questions per test** — some typed arithmetic is fine and useful for practicing working-out rather than pattern-spotting from options, but keep it a minority of the paper, not the default.
  - When using MCQ, write 3–4 plausible distractors (common mistakes a student might make), not obviously-wrong filler options.
- **Boss round** — default yes, last 20% of questions (e.g. 5 of 25), flagged as the timed double-XP round. Only skip if asked to.

If subject and level are both given but nothing else is, it's fine to proceed using the defaults above — just state the assumptions made (question count, time limit, boss round) in the reply so they can correct anything before it's used.

## 3. Content standards (apply every time, regardless of what was asked)

- **Explanations are required for every question**, written for the actual level given — plain, step-by-step, concrete language. No unexplained jargon (avoid things like "highest common factor," "coefficient," raw algebra notation without walking through it in words). Default target: a **9-year-old** should be able to follow the reasoning, even if the question itself is pitched higher. Adjust upward only if the stated level is clearly older (e.g. Year 6+) — but still favour plain wording over formal terminology.
- **One correct answer per question**, unambiguous. Avoid questions where rounding, regional spelling, or interpretation could produce a defensibly different answer.
- **Units in answers:** the grading engine now tolerates a student typing a unit the stored answer doesn't include (e.g. typing "8cm" when `correct_answer` is stored as "8" — common ones like cm, cm², m, kg, mph, %, minutes, pence are auto-stripped before comparing). Even so, when writing a question that expects a bare number, say so explicitly in the question text (e.g. "...in cm?" or "(just the number, e.g. 48)") so the expectation is clear to the student regardless.
- **`topic` field** should be a short, human-readable label used for grouping and badge awarding (e.g. "Fractions," "Ratio," "Odd one out") — reuse consistent topic names across tests in the same subject where possible, rather than inventing near-duplicate labels.
- **No calculator needed** for maths content — keep numbers/steps appropriate for mental or written working at the stated level.
- **Check for overlap** with existing tests before writing new questions — query `mock_tests`/`questions` first so the new paper doesn't just repeat what's already there.

## 4. Technical steps

**Every new mock test must be inserted directly into the Supabase database — that's what makes it "live."** Writing the questions in chat or a document is not sufficient; the site only shows tests that exist as rows in the `mock_tests` and `questions` tables. Nothing needs to be deployed or redeployed for this — the site reads test content live from the database on every page load.

- **Supabase project:** `maths-mock-tests`, project ID `lytwxeietqrtfrszzbcd`
- **Tables:** `mock_tests` (one row per test: `title`, `description`, `subject`, `time_limit_seconds`) and `questions` (one row per question, linked via `mock_test_id`: `order_index`, `topic`, `type`, `question_text`, `options`, `correct_answer`, `is_boss_round`, `xp_value`, `explanation`)
- **XP convention:** `xp_value` = 10 for normal questions, 20 for boss round questions (`is_boss_round = true`)

Steps:
1. Query the student's recent performance for this subject (see 1a above) and query existing `mock_tests` for the subject to check what's already covered.
2. Draft all questions with every field above filled in, including `explanation`, with topic mix weighted per 1a.
3. Insert via a single SQL statement against the project above: `mock_tests` row first, then `questions` rows in the same statement (using a CTE, as done previously) so they're linked correctly.
4. Verify immediately after insert: confirm question count and boss-round count match what was intended, and spot-check one row's `explanation` reads correctly (watch for apostrophes needing escaping in SQL).
5. Report back: title, subject, question count, time limit, the level/difficulty assumptions used, and the weighting rationale from 1a.
