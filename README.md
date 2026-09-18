# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

This system answers factual questions about student life at a university, drawing on the campus_life corpus: 88 short posts (averaging 317 characters) written in the register of one student answering another. The posts cover dining halls, dorm buildings (laundry costs, noise levels), individual courses (exam formats, weekly workload), and roughly 26 one-off administrative topics such as the housing lottery, printing quotas, and add/drop deadlines.

The system is built for narrow, factual lookups where the answer lives in a sentence or two — the lunch wait at a specific dining hall, whether a course is curved, what laundry costs in a given building. It does not handle comparative or subjective questions, since the corpus captures individual students' isolated observations rather than any aggregated ranking or consensus.

## Chunking Strategy

**Chunk size:** 800
**Overlap:** 120

One document = one chunk. My corpus is 88 short posts — 178 characters at the
shortest, 305 median, 549 at the longest — and each one is a heading followed
by two or three paragraphs answering a single question. The Kestrel Commons
post gives the lunch wait, the thing worth ordering, and the opening hours,
and that's the entire document. At 317 characters on average a post is already
about one thought, so I picked numbers that keep it intact: 600 is a ceiling
that sits above my longest document, and overlap is 0 because nothing splits,
so there is no shared window to describe. Declaring an overlap I don't use
would be describing an index I don't have.

I changed my mind on this. The starter ships 800/120 and I assumed 800 was
chosen to exceed documents like mine. It isn't doing that. `fallback_split`
advances by `chunk_size - overlap`, so what triggers a split is the step, not
the size — at 800/120 the step is 680, and that's what clears my 549-character
maximum. I found it by running the size down: at 550 I got 91 chunks out of 88
documents, and the three extras were pure overlap tail cut mid-word, one of
them 31 characters reading "kends, no enforced quiet hours." With overlap 120
I'd have needed 669, not 550, for nothing to split. Setting overlap to 0
removes that trap instead of leaving me to remember it.

What I gave up: some posts cover more than one thing. `housing_innisfree_hall.txt`
holds the building description, the laundry prices and the noise assessment in
a single post, so a laundry question retrieves the noise text along with it. A
paragraph-aware splitter would separate those and my numbers would be
different. I took the other side — at this document length, cutting a post in
half costs more than the precision I'd gain.


**Chunk 1** — source: `thread_bike_commute.txt#0` — produced by: `chunker.py::fallback_split`

26 chunks total. Showing 1, spread across the corpus.

THREAD: Is a bike worth it for a 20 minute walk commute?

--- reply 1 (14 votes) ---
Yeah. Cuts an 18 minute walk to about 6. The thing nobody mentions is storage — covered bike parking exists at three buildings and is full by 9am at all three.

--- reply 2 (9 votes) ---
Counterpoint, I sold mine. Between November and March the paths are either icy or salted and salt destroys a drivetrain in one season.

--- reply 3 (22 votes) ---
Both true. I keep a cheap bike for September to November and walk the rest of the year. Total cost was about $120 for the bike and I don't care what happens to it.

--- reply 4 (5 votes) ---
If you do get one, the campus does free registration and it's the only reason I got mine back after it was taken.

For each one, ask: could someone answer a question using only this,
without reading what came before or after?

**Chunk 2** — source: `admin_add_drop_deadline.txt#0` — produced by: `chunker.py::fallback_split`

88 chunks total. Showing 1, spread across the corpus.

Paste these into your README under Sample Chunks. The rubric asks
for the source file and the function that produced them — both are
printed for you below.

On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other

**Chunk 3** — source: `guide_accessibility.md#0` — produced by: `chunker.py::fallback_split`

51 chunks total. Showing 1, spread across the corpus.

Paste these into your README under Sample Chunks. The rubric asks
for the source file and the function that produced them — both are
printed for you below.

Getting around the region with limited mobility

An honest assessment rather than a promotional one. Some of these places are
difficult and it is better to know in advance.

Straightforward

Thornby Wells is the easiest town in the region. It is flat, compact, and
everything is within three minutes of everything else. Parking is free for two
hours anywhere in town and the station is central. The pump room and gardens
are level throughout.

Marchwood has a modern tram network with level boarding on all four lines,
running every 8 minutes on weekdays. The city museum and covered market are both
step-free. The distances between districts are the main consideration.

Brightwater is level along the river and through the centre. The mill museum
is step-free. The station is a 15-

For each one, ask: could someone answer a question using only this,
without reading what came before or after?

**Chunk 4** — source: `` — produced by: ``

```
```

**Chunk 5** — source: `` — produced by: ``

```
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:**
0.8246  What is the capital of Mongolia?

**Answer:**

Ulaanbaatar

```
.venv/Scripts/python.exe -c "import questions; from store import search; from config import CORPUS, TOP_K; [print(f'{search(q, TOP_K, CORPUS)[0].distance:.4f}  {q}') for q in questions.OUT_OF_SCOPE]"
```

**My relevance cutoff:**

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---|
| Is the housing lottery random? | Yes | 0.2541 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8442 |
| What is the capital of Mongolia? | No | 0.8246 |
| Who won the 1994 World Cup? | No | 0.8859 |
| How do I write a for loop in Rust? | No | 0.8960 |
| How do I change the oil in a diesel engine? | No | 0.9340 |

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.**
Moment 1: Catching Python Version Incompatibility Before Package Installation
   * What I asked for: I requested assistance setting up the virtual environment (.venv) for the starter repository.

   * What came back: Inspection of .venv/pyvenv.cfg revealed that the environment was created using Python 3.14.7, which violated the Python version bounds in requirements.txt due to binary dependencies in several core packages.

   * What I changed: Rather than attempting to force unsupported package installations, I deleted the environment and recreated it using Python 3.11.9. Re-running python test.py on the correct interpreter cleared every environment and package check (8 out of 10), leaving only the API key configuration outstanding.
   * 
**2.**
  Moment 2: Validating Test Questions Against Corpus Entities and Future Evaluation Logic**
  * What I asked for: I asked the AI to review my draft test questions for
    `questions.py` against the contents of the `campus_life` corpus.
  * What came back: The review pointed out that several questions referenced
    entities absent from `campus_life` (such as specific professor names,
    "West Hall," and "Library Cafe") and noted that target phrases like
    `"20 minutes"` do not match text explicitly stating `"20 to 25 minutes"`,
    which would make evaluation scoring unreliable in Unit 2.
  * What I changed: I replaced the placeholder questions and wrote 5 specific
    test questions and `expects` target phrases derived directly from verified
    entities, room reservation limits, and exact text in the `campus_life`
    source files.
    
<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
