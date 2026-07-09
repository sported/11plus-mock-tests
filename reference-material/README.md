# Reference Material

Real 11+ verbal reasoning paper (CGP "Assessment Test 7", pages 67–70), provided 2026-07-09 as a calibration reference for question style and difficulty. **These are for style/format/difficulty reference only when generating new tests — never copy the specific words, letters, numbers or scenarios verbatim into a generated test.** Always write fresh, original content inspired by the *pattern* shown.

## What each file shows

### Verbal Reasoning (Assessment Test 7)
- **cgp-assessment-test-7-cover.jpeg** — Test instructions ("Allow 40 minutes"), missing-number equation puzzles (Q1–5), opposite-meaning bracket-selection puzzles (Q6–10), letter-value arithmetic (Q11–15, e.g. "A=1 B=3 C=5 D=6 E=13, C×B−E+B=?").
- **cgp-assessment-test-7-page67.jpeg** — Three-letter word completion puzzles that finish a capitalised word fragment sensibly in a sentence (Q16–21), "which words are NOT related" from a group of five (Q22–26), letter-pair alphabet sequences (Q27–31).
- **cgp-assessment-test-7-page68.jpeg** — Remove-one-letter-and-move-it-to-the-other-word puzzles producing two new words (Q32–36), analogy-via-bracket-selection puzzles (Q37–41), word-to-number code puzzles (Q42–44).
- **cgp-assessment-test-7-page69.jpeg** — More word-to-number code puzzles (Q45–47), multi-statement logic deduction puzzles ("only one of these can/must be true," 5 options, Q48–49).
- **cgp-assessment-test-7-page70.jpeg** — Word-pattern transformation puzzles like removing the first and last letter (Q50–54), more letter-pair alphabet sequences (Q55–60).

### Maths (Assessment Tests 4 and 5)
- **cgp-assessment-test-4-page57.jpeg** — Comparing decimals, table lookup (youngest person), rearranging digits for largest even number, unit conversion (m→mm), reflection/mirror-line shape puzzles, division with remainder, percentage discount, mean from a bar chart, "how many fifths in 5" (inverse fraction).
- **cgp-assessment-test-4-page58.jpeg** — Unit pricing (pence per apple), perpendicular line identification on a diagram, angle estimation, negative number difference (temperature), pictogram comparison, plausibility judgement ("could this weight be true"), perimeter word problem, division fact family, inverse-operation puzzle (multiply then square, work backwards).
- **cgp-assessment-test-4-page59.jpeg** — Angle/rotation on a dial, decimal multiplication, three-way Venn diagram reading, recipe ratio scaling, percentage from a frequency table, fraction of a total, area-left-over word problem, unit conversion (minutes→seconds).
- **cgp-assessment-test-4-page60.jpeg** — Sequence with a two-step rule ("subtract 2, then double"), comparing means of data sets, ratio simplification, coordinate/shape identification, capacity word problem (litres→ml), bar chart total-and-subtract, money word problem (change from a note).
- **cgp-assessment-test-4-page61.jpeg** — Pie chart reading (total minus a category), cuboid volume, bus timetable reading (working backwards from an arrival time), sequence with decimal steps, angle on a straight line, circumference × number of turns, algebraic expression for a real-world total (multiple items, different prices).
- **cgp-assessment-test-5-page62.jpeg** — Large multiplication, division-with-rounding-up (shelving word problem), coordinates on a shape, real-world estimation (plausible height for a given age), table lookup (range: max−min), reading a dial/scale, proportional scaling (unit price), choosing appropriate units for a context.
- **cgp-assessment-test-5-page63.jpeg** — Area word problem (rolls of turf needed, with rounding up), mean of a data set, comparing mixed numbers/decimals, percentage-to-total word problem (reverse percentage), 2×2 sorting table (shape classification by properties), money saving comparison, "divisible by both 3 and 5," reading a two-pet-per-child table.
- **cgp-assessment-test-5-page64.jpeg** — Multi-step word problem (packets → boxes, weeks), rounding word problem (carpet lengths, least waste), coordinate grid direction-reading, counting-back-in-steps sequence, ratio from a menu/table, decimal subtraction, fraction of a shape shaded, change-from-a-note word problem.
- **cgp-assessment-test-5-page65.jpeg** — Reading values off a line graph (rate conversion), profit word problem, reading a "typical values per 100g" nutrition table, geometric sequence (6th term), triangle angle sum with an exterior angle, best-value comparison across 5 product sizes, reverse-percentage word problem.
- **cgp-assessment-test-5-page66.jpeg** — Pie chart with a given angle (45°), volume of a box from given dimensions, bar chart → fraction, weighted word problem (unit cost × quantity), mean of a list, unit conversion (miles→km, then estimate), using a known multiplication fact to derive a related one (56×946=52976, find 946×28), algebraic expression for a real-world charge (fixed + variable).

## Adapting to this platform

This platform is text-only — it cannot render pie charts, bar charts, Venn diagrams, coordinate grids, or images. When a reference question relies on a diagram, **describe the same data as a text list or table in the question_text** instead (e.g. "A survey found: 12 people like apples, 8 like bananas..." rather than an actual pie chart). This is already established practice — see the Foundations Mock Test's survey question for a working example. Don't skip these question *types* just because the platform can't draw the picture; describing the data in words preserves the same reasoning skill (reading and interpreting a data set) that the real question tests.

## Key takeaways for generation

- **Verbal reasoning** is far more varied than synonym/antonym/analogy multiple choice — it includes numerical-letter arithmetic, coded words, logic deduction with 5 answer options, and word-transformation puzzles with a rule to infer from examples. See the "Verbal/Non-Verbal Reasoning puzzle types" section of `mock_test_generation_guide.md` for how each maps onto this platform's mcq/input format.
- **Maths** genuinely mixes input (write-in) and multiple-choice roughly evenly, not MCQ-first — calculation questions naturally suit input, while classification/estimation/comparison questions suit MCQ. Follow that natural split rather than forcing everything to MCQ. Real papers lean heavily on word problems grounded in everyday scenarios (shopping, timetables, recipes, sports data) rather than bare arithmetic — favour that framing.
