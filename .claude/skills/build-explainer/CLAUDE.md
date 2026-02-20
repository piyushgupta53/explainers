# CLAUDE.md — Distilled: Writing & Pedagogy Guide

Read this before touching any code. This governs **how to think, structure, and write**
an explainer. The companion `SKILL.md` handles technical implementation.

---

## What This Project Is

A collection of deep, first-principles explainers on technical topics —
machine learning, deep learning, reinforcement learning, mathematics, and computer science —
hosted on GitHub Pages as long-form reading-and-playing experiences.

**The one-sentence test for every explainer:**
After reading, could the reader re-derive the key idea from scratch, on a blank page,
without looking anything up?

Not recite it. Not recognize it. *Re-derive* it.

If the answer is no, the explainer has failed regardless of how good the prose is.

---

## The Reference to Internalize

Before starting: read **paraschopra.github.io/explainers/biology-scaling/**

What makes it work:
- Opens with a specific, jaw-dropping concrete observation — not a definition
- Every abstraction is earned: the math is the *conclusion*, not the setup
- Prose is dense but never padded — every sentence earns its place
- Interactives are placed exactly where the reader needs to touch the concept
- It slows down at the hard part instead of rushing past it
- The ending synthesizes — it doesn't just stop

We follow this approach exactly.

---

## Phase 1: Decompose the Topic Before Writing Anything

This phase happens before any prose, before any code, before any section headings.
Do not skip it. Explainers that skip this phase produce the wrong structure.

### Step 1 — Find the Core Tension

Every good explainer is built around a genuine problem that needs solving.

Ask: **What goes wrong without this concept? What question does it answer?**

The tension is not the same as the topic. The topic is the answer. The tension is the question.

Examples across the full range of topics:
- *Autoencoders* — you have 784 numbers per image and no way to discover structure without labels
- *Backpropagation* — you can define a neural network loss but have no method to compute which weights caused it
- *Attention mechanism* — RNNs must compress an entire sequence into one fixed vector before decoding; long-range dependencies disappear
- *Entropy* — we have an intuitive sense that a coin flip is "less certain" than a loaded die, but no way to measure or compare uncertainty precisely
- *Q-Learning* — an agent must take actions now that only pay off hundreds of steps later; how do you assign credit?
- *Gradient Descent* — you have a loss function over millions of parameters; evaluating every point is impossible, yet you must find the minimum
- *Fourier Transform* — a signal is a mess of overlapping frequencies in time; you cannot identify which frequencies are present by looking at the waveform
- *Convolutional Networks* — fully connected networks apply the same learned features everywhere but images have local spatial structure that should be detected position-independently
- *KL Divergence* — you have two probability distributions and need a single number measuring how different they are in a way that's asymmetric and interpretable

If you cannot state the tension clearly in two sentences, stop. The topic is not scoped yet.

### Step 2 — Find the Conceptual Skeleton

Strip the topic to its irreducible core. Identify **the one insight** without which nothing else makes sense.

Then map:
- What must the reader already know to understand that insight? (prerequisites)
- What does that insight enable or explain? (consequences)

The skeleton is not a section list. It's a dependency graph. From it, the section list will emerge.

**Examples of one-insight statements:**
- Backpropagation: *the chain rule lets you compute ∂Loss/∂w for any weight in any depth network in one backward pass*
- Entropy: *the optimal code length for a symbol with probability p is −log₂(p) bits; entropy is just the expected value of this*
- Attention: *instead of a fixed context vector, let the decoder query all encoder states with a learned similarity function*
- Gradient Descent: *the negative gradient points in the direction of steepest local decrease; taking a small step that way reduces the loss*
- Q-Learning: *if you know the value of every next state, you can define the value of the current state recursively — and then learn that recursively defined value from experience*
- Fourier Transform: *any periodic signal can be decomposed into a sum of sinusoids; the transform reveals the amplitude and phase of each frequency*

### Step 3 — Identify Your Topic Type (for Thinking, Not Structure)

Knowing what *kind* of thing your topic is helps you think about it. It does not determine your section structure — that comes from the topic's own logical dependencies, which you mapped in Steps 1–2.

**Algorithm** — a procedure that solves a problem step-by-step
*Examples: Backpropagation, Q-Learning, Gradient Descent, Dijkstra's algorithm, Viterbi*

**Mathematical Concept** — a formal object with definitions, properties, and theorems
*Examples: Entropy, KL Divergence, Fourier Transform, Eigendecomposition, Convolution*

**Architecture / System** — a constructed system with multiple interacting components
*Examples: Transformers, CNNs, VAEs, LSTMs, Diffusion Models*

**Learning Paradigm** — a framing or philosophy for how learning happens
*Examples: Reinforcement Learning, Self-Supervised Learning, Meta-Learning, Contrastive Learning*

**Principle / Phenomenon** — a universal property that emerges across many systems
*Examples: Bias-Variance Tradeoff, Information Bottleneck, Universal Approximation, Double Descent*

Most topics are primarily one type with elements of another. This taxonomy is useful for *thinking*. It is not a structural prescription.

### Step 4 — Determine the Right Prerequisites

The assumed baseline varies by topic. Be explicit about what you're assuming.

For ML/DL topics, you can generally assume:
- Basic programming intuition (not fluency)
- Vectors and matrices (not eigenvalues, not SVD)
- Has heard of neural networks, loss functions, gradient descent

For math-heavier topics (Fourier, Entropy, Eigendecomposition), you may need to assume:
- Comfort with functions and notation
- Basic calculus (derivatives)
- Possibly: complex numbers, logarithms, probability

For pure algorithm/CS topics:
- Can read pseudocode
- Understands recursion
- Familiarity with graphs or trees if relevant

Whenever a section requires something outside the assumed baseline, either:
a) Explain it in a brief callout: "If you're unfamiliar with X, here's the idea in one paragraph..."
b) Link to its own future explainer

**Never silently assume specialist knowledge.**

### Step 5 — Map the Misconceptions

For each section you're planning, ask: **what would a sharp person who skimmed this section incorrectly believe?**

That is your quiz question. The wrong answers should be those exact beliefs.

Common misconception types across all domains:
- **Mechanism vs. purpose** — knowing what something does without knowing why it works
- **Missing the constraint** — not seeing what forces the interesting behavior
- **Conflating similar-sounding things** — entropy vs. cross-entropy, gradient vs. derivative, policy vs. value function
- **Overgeneralizing a special case** — thinking a result true in one regime holds everywhere
- **Treating a definition as a theorem** — or vice versa
- **The obvious-but-wrong answer** — what sounds right on first intuition before thinking carefully

Write down 3–5 misconceptions before writing quiz questions. Every quiz question should be designed to catch a specific one.

### Step 6 — Find the Interactive Moments

For each planned section, ask: **is there something the reader should feel, not just read?**

A demo is needed when any of these are true:
- There's a **continuous tradeoff** the reader needs to feel (not just be told about)
- There's a **process** that unfolds over time and is clearer animated than described
- The reader can **create or manipulate an input** and see the concept act on it
- A **before/after comparison** is clearer interactive than in prose
- There's a parameter with a regime the reader should discover by exploration

For each planned demo, write one sentence:
> "The reader [does X]. They observe [Y]. The non-obvious thing they discover is [Z]."

If you cannot identify Z — something genuinely surprising — the demo is not worth building.
Cut it and write prose instead. The right number of demos is however many your topic genuinely needs — 2 or 12 or anywhere between.

---

## Phase 2: Structure — There Is No Universal Template

**The most important thing this guide can say about structure:**

There is no universal section order. The autoencoder explainer has a problem section, then
three build-up sections, then math, then comparison, then applications, then extensions.
That is the autoencoder's structure. It is not a template.

Backpropagation's structure is different. Q-Learning's structure is different.
The Fourier Transform's structure is different. Each topic has its own logical dependencies,
and the section order must follow those dependencies.

**What is universal:**
1. Start with the tension — the problem the concept solves
2. Earn every abstraction before stating it — concrete always before abstract
3. Each section introduces one idea, not two
4. The math follows the intuition, not the other way around
5. Every section has a job — if you can't state what job it does, cut it

**What is not universal:**
- Whether there's a standalone "comparison section"
- Whether there's a standalone "applications section"
- Whether extensions get their own section or are woven throughout
- How many sections there are (6 is fine; 12 is fine if each earns its place)
- Whether the math is concentrated in one section or distributed throughout

Use the skeleton you built in Step 2 to determine the right section order for your specific topic.

### How to Open

The opening creates the tension that drives the entire explainer. There is no list of opening types to pick from — the right opening comes from the topic itself. Ask: what's the most specific, concrete, urgent way to make the reader feel the problem this concept solves?

The test: does the reader feel the tension within two paragraphs? If not, the opening is too abstract, too generic, or starting in the wrong place.

Some openings work by stating a fact that demands explanation. Some work by throwing the reader into an interactive before any words. Some work by telling a story. Some work by posing a question so specific the reader can't look away. Some work by presenting the wrong answer first and letting the reader believe it. The form doesn't matter. What matters is that the reader arrives at the end of the opening with a question they need answered — and that question is the one your explainer answers.

What never works: starting with a definition, starting with "In this explainer, we'll explore...", starting with the history of the field, or starting with why the topic is important. These are all throat-clearing. Start with the problem.

### Structural Principles (Not Structural Templates)

There are no section-order templates in this guide. The decomposition you did in Steps 1–5 gives you the logical dependencies; the section order emerges from those.

What these principles provide is a set of *invariants* — things that are true of good explainer structure regardless of topic:

- **The structure should be surprising.** If a reader who's seen one explainer can predict the shape of the next, the structure is too regular. Some explainers need 6 sections. Some need 14. Some need a long unbroken narrative in the middle. Some need three demos in a row with no prose between them.
- **Sections can be radically different lengths.** A section that makes one quick point in two paragraphs is fine. A section that develops a careful derivation over eight paragraphs is also fine. What's not fine: every section being 3–5 paragraphs because that's the "right" length.
- **Not every section needs every element.** Some sections are pure prose — a story, a careful argument, a historical narrative. Some sections are a demo with two sentences of setup. Some are a worked example with no surrounding explanation. Match the form to the content.
- **The order should feel inevitable in retrospect.** The reader should finish and think "of course it had to be explained in this order." But they should NOT have been able to predict the order from the table of contents alone.

---

## Phase 3: Writing

### Opening a Section

The first paragraph of every section does two things:
1. Names the problem or question that *this* section answers
2. Signals to the reader why they should read it — not with "this is important" but by making the question feel urgent

Never open a section by stating a definition. Open by creating the need for one.

### The Math

There will almost always be math. When it appears:

1. Build the intuition in words first — always
2. Then state the math as the compact expression of the intuition you already built
3. Parse every symbol explicitly: "where θ represents... φ represents... and the subscript i indexes..."
4. Give a worked example with concrete numbers whenever possible
5. If there's a derivation, narrate each key step; use "it can be shown that..." for tedious algebra

**The goal:** the reader should see the equation and think "yes, that's exactly what we just said in words." Not "I'll trust this."

Never write a formula and move on.

### Comparisons

Some topics have a natural adjacent concept worth comparing (autoencoders ↔ PCA, entropy ↔ variance, attention ↔ convolution). When this exists, a comparison section is valuable. When it doesn't exist naturally, don't force one.

When writing a comparison:
- Never frame it as "X is better than Y" — they solve different problems
- Find the surprising equivalence or surprising difference — that's what makes the comparison interesting
- Show exactly where they diverge — the precise condition under which they give different answers
- An interactive that demonstrates the difference is ideal

### Prose Style

**Voice:**
Write as if explaining to one sharp friend who doesn't know this yet but will understand completely
if you explain it well. Not to a class. Not to a committee. One person.

Use "you" when addressing the reader. Use "we" when reasoning together.
First person is fine: "Notice that..." / "The key thing is..."

**Sentences:**
Vary length deliberately. The pattern that works: medium, medium, short. Short. Then a longer
one that develops the thought. The short sentence at the end of a paragraph is almost always
the payoff.

**Paragraphs:**
- First sentence: the topic sentence — states what the paragraph is about
- Middle: evidence, explanation, elaboration
- Last sentence: payoff, implication, or pivot to the next idea
- Maximum ~5 sentences. Split if longer.

**Terms:**
When introducing a technical term for the first time: bold it, define it immediately in the
same or next sentence, use it consistently from then on. Never swap synonyms for variety.

**Concreteness:**
- Always give specific numbers. Not "high-dimensional" but "784-dimensional." Not "many iterations" but "10,000 gradient steps." Not "a large reward" but "a reward of +1 at episode termination and 0 everywhere else." The specific number is what makes it real.
- When introducing a formula, always give a worked example with actual numbers substituted in
- When making a comparative claim, always give the limiting case that makes the claim concrete

**Cut:**
- "In today's world of AI..." → cut. Start with the problem.
- "While this is a simplification..." → either explain it properly or put it in a footnote
- "Intuitively speaking..." → if you need this qualifier, the explanation isn't intuitive yet. Fix it.
- "It can be seen that..." / "It should be noted..." → say the thing directly
- "So, what exactly is X?" / "Let's explore..." → opening-sentence waste

### Voice & Personality

The prose style guidelines above handle mechanics. This section handles *spirit*. An explainer should feel like it was written by a specific person with opinions, not assembled by a committee.

**What follows is a toolkit, not a checklist.** A given explainer might lean heavily on humor and opinion but have no tangents. Another might be driven entirely by narrative with no jokes at all. A math-heavy one might need honesty about difficulty but not extended metaphors. Use whichever of these serve YOUR topic. Using all of them in every explainer would create its own kind of sameness.

**Humor as pedagogy**
When humor works: to deflate intimidating concepts, to make a wrong answer feel obviously wrong, to reward the reader for paying attention. When it doesn't: forced puns, humor that interrupts momentum, jokes that exist independently of the explanation. Tim Urban's principle: the humor IS the explanation. The Panic Monster isn't a joke decorating an insight — the joke IS the insight. If you can remove the humor without losing understanding, the humor was decorative. Keep humor that's load-bearing.

**Extended metaphors**
Pick one metaphor early and ride it. Don't introduce five competing analogies — that's a sign you haven't committed to one understanding. If the metaphor strains at some point, that's information — the point where it breaks is often the point where the real concept diverges from intuition. Don't discard the metaphor when it breaks; show the reader *where* it fails and *why*. That's often the most illuminating moment.

**Productive tangents**
A tangent is justified if it builds toward a realization that couldn't be reached on the main path. Signal the tangent ("Here's something that seems unrelated but isn't"), then earn the payoff by connecting it back. If you can't connect it back, cut it. The connection is not optional — it's the whole point.

**Honesty about difficulty**
When something is genuinely confusing, say so. "This is where most explanations hand-wave. We're not going to." is more trustworthy than sailing through as if everything is obvious. Readers can feel when you're glossing over the hard part, and they lose trust. Slowing down and naming the difficulty is a feature, not a failure.

**Opinion**
Take positions. "This notation is bad but we're stuck with it." "The standard explanation of X is misleading because..." "Most tutorials start here, but that's backwards — here's why." Having opinions signals a real mind behind the words. It also helps the reader — knowing that something is widely considered confusing or badly named saves them from thinking the confusion is their fault.

**Narrative instinct**
Who discovered this? What problem were they actually trying to solve? What went wrong first? The story of the idea is often the best structure for explaining it, because the discoverer's path is the path of increasing understanding — which is exactly the path you want the reader to walk.

---

## Interactive Demo Philosophy

### When a Demo Is Necessary

A demo is necessary — not optional — when the concept involves one of:
- A **continuous tradeoff** where the reader needs to feel the curve, not just know it exists
- A **process** that unfolds over time and is clearer animated than described in words
- **Direct manipulation** where the reader creates input and watches the concept act on it
- A **comparison** that is genuinely clearer when you can switch between states
- A **surprising regime** that a reader would not predict from reading alone

If none of these apply, write prose instead. A weak demo is worse than no demo.

### What Every Demo Must Have

1. **A setup instruction** (1–2 sentences before the demo): what to do, and what to notice
2. **Immediate feedback** — any reader action produces a visible response within 50ms
3. **A surprising observation** — something the reader would not have predicted from the prose. If there's no surprise, cut the demo.
4. **A meaningful default state** — the demo should be interesting before the reader does anything

### Demo Placement

One primary demo per section. Always placed *after* the key prose.
Prose explains. Demo deepens.

Exception: an opening hook demo (before any explanation) can work when the goal is to
create the question in the reader's mind. The prose then answers it.

### The Five Demo Types

**1. Slider → Live output**
Reader adjusts a parameter; output updates immediately.
Best for: tradeoffs, sensitivity to hyperparameters, discovering good/bad regimes

**2. Build/Draw → Transform**
Reader constructs an input; the system processes it in real time.
Best for: making input-output relationships tangible, end-to-end pipelines

**3. Toggle A/B**
Reader switches between two states or methods.
Best for: before/after comparisons, method A vs. B, what changes when you add X

**4. Step-through animation**
Reader triggers; watches a process unfold step by step.
Best for: algorithms with discrete steps, iterative processes, convergence over time

**5. Ablate / mask**
Reader removes components to see what breaks.
Best for: understanding which parts matter, what carries information, feature importance

See `SKILL.md` for which interactive implementation to use for each demo type
across different topic domains.

### Sound Design

Sound is **ambient confirmation**, not communication. The reader should be able
to use the page with sound off and lose nothing important.

| Event | Sound intent |
|-------|-------------|
| Slider moved | Pitch tracks value — higher value → higher pitch |
| Process triggered / data flowing | Ascending arpeggio |
| Algorithm step completed | Single soft tone |
| Correct quiz answer | Chord resolution |
| Wrong quiz answer | Slight dissonance |
| Simulation complete | Success chord |
| Button press | Single neutral click |

---

## Length and Pacing

The topic determines the length. A 2,000-word explainer that nails a tight concept is better than a 5,000-word explainer that pads a simple one. A 10,000-word deep dive that sustains tension and rewards attention is better than a truncated version of itself. There is no word count target.

Demos are tools, not decorations. If your topic has 3 natural interactive moments, build 3. If it has 9, build 9. Zero is also a number — some sections are better as pure prose. The test for whether a demo belongs: does it reveal something the reader couldn't have predicted from reading alone?

Quizzes test misconceptions. If a section doesn't create a specific misconception worth catching, it doesn't need a quiz. Some explainers will have 2 quizzes. Some will have 7. The right number is: one per major misconception, no more, no less.

Footnotes add rigor and texture. Use them for citations, mathematical asides, caveats to simplifications, and tangents that would break prose rhythm. Every explainer should have some — this is a rigorous project — but the count depends on the topic.

### Pacing

**The core principle:** alternate between demanding (new concept, derivation, dense reasoning) and rewarding (a story, a demo, a surprising connection, a moment of humor, a worked example). Never put two conceptually heavy sections back-to-back without a reward between them.

**But the reward doesn't have to be the same type every time.** The per-section rhythm should *vary deliberately*:
- Some sections should be all prose — a story, a derivation, a careful argument that builds across paragraphs. No demo, no callout, just the argument.
- Some sections should open with a demo and let the reader discover the concept before you name it.
- Some should be a single worked example with no surrounding prose.
- Some should be short and punchy — two paragraphs and done.
- Some should be long and patient — eight paragraphs building a single careful point.

**The reader should never be able to predict what comes next.** If they can, the structure is too regular. After a dense mathematical section, maybe the reward is a historical anecdote. After a narrative section, maybe the next one opens with a demo. After three sections of progressive complexity, maybe the fourth is deliberately simple. The rhythm should feel musical — not a metronome, but a piece with dynamics.

**Pace by cognitive demand:** after a hard section (dense math, new abstraction, multi-step reasoning), give the reader a reward. But "reward" is a broad category: it could be a demo, a story, a surprising connection to something they already know, a moment of humor, a worked example with concrete numbers, or just a paragraph that synthesizes what they just learned into a single clean sentence.

---

## Callout Types

Four types. Use them precisely — do not mix.

**NOTE** — a factual clarification, definition, or important caveat
Use when: a reader might have a reasonable wrong belief that needs correcting; a term needs definition; there's an important exception to a rule

**INSIGHT** — the single "aha" moment of the section
Use when: you've built up to the key realization and want to crystallize it
Use sparingly — at most one per section, only at the genuine peak

**MATH** — an equation that interrupts prose flow
Use when: you need to show a formula inline but the math block (multi-line) is overkill

**RESULT** — what to remember, a key distinction, a conclusion
Use when: closing a comparison, summarizing what was established, signaling "this is what matters"

These are your core callout types. But if a section needs a sidebar, an anecdote box, a "what would happen if..." thought experiment, or a historical note — build it. The callout system is a starting point, not a ceiling.

Callouts punctuate prose; they don't replace it. Use them when a piece of information is important enough to visually break out of the flow, but wouldn't work as its own section.

---

## The Closing

The last paragraph of every explainer must:
1. Not be a summary list
2. Synthesize rather than recap
3. Zoom out — what does this concept reveal about something *larger* than itself?
4. End with a sentence that feels like arrival, not stopping

**Wrong:**
> "In summary, we covered X, Y, and Z. These are important concepts in ML."

**The right move, illustrated across different topic types:**

For a mathematical concept (Entropy):
> "What makes entropy the right measure of uncertainty isn't that it was decreed — it's that it's the *only* measure satisfying a few reasonable requirements simultaneously. Any function that's continuous, increases with more equally-likely outcomes, and composes correctly over independent events must be entropy. The formula didn't precede the intuition; the intuition forced the formula."

For an algorithm (Backpropagation):
> "The remarkable thing about backpropagation isn't that it's clever — it's that it's the only way to compute gradients efficiently in a deep network. The chain rule doesn't give you one method among many; it gives you the method. Every neural network trained in the past forty years learned through exactly this computation. The scale changed; the idea did not."

For a learning paradigm (Reinforcement Learning):
> "Reinforcement learning doesn't assume the world is stationary, doesn't require labeled examples, and doesn't need to know the rules in advance. It requires only that consequences follow actions, and that some consequences are better than others. That's a remarkably thin description of what 'learning' means — and it's enough."

For each topic, find the sentence that says what this concept's simple statement *of something larger* is.
The closing should make the reader feel they understand not just the concept, but why the concept had to exist.

The "zoom out and synthesize" move above is not the only way to close. The closing could return to the opening with new eyes, or end with what the concept *doesn't* explain, or make a claim the reader now has the tools to evaluate, or end with the person who discovered it. The form doesn't matter. What matters: the reader feels arrival, not stopping. They understand not just the concept, but why it had to exist.

---

## Anti-Patterns

**The Premature Abstraction**
Naming or defining the concept before building the need for it.
Fix: Start with the concrete problem. Let the abstraction emerge from it.

**The Definition Dump**
Opening a section with "A, B, and C are three important concepts. A is... B is..."
Fix: Introduce each concept exactly when it's needed, not before.

**The Unexplained Formula**
Writing a formula and moving on without parsing every symbol.
Fix: Every symbol earns a sentence. Every formula earns a worked example.

**The Demo That Confirms**
An interactive that only shows what the prose already said.
Fix: The demo must reveal something the reader could not have predicted.

**The Shallow Application**
"This concept is used in healthcare / robotics / finance."
Fix: Explain the mechanism. Why does *this* concept solve *this* problem in *this* domain?

**The Orphan Concept**
A fact stated without connection to what comes before or after.
Fix: Every fact builds toward the next concept or explains a prior one.

**The Borrowed Structure**
Applying the section structure of a previous explainer to a new topic because it worked before.
Fix: The structure must emerge from the topic's own logical dependencies. Every time.

**The Forced Comparison**
Adding a "X vs. Y" section because comparisons worked well in a previous explainer.
Fix: Only compare when there is a genuinely illuminating equivalence or difference.
If the comparison doesn't reveal something new, cut it.

**The Passive Voice Hedge**
"It can be seen that..." / "It is worth noting that..."
Fix: Say the thing directly. Active voice, always.

### Anti-Slop Checklist

These are the patterns that make writing feel AI-generated. Read your draft against this list before shipping. If you catch yourself doing more than one or two, the explainer needs structural surgery, not copyediting.

- **Structural uniformity** — Every section has the same shape (intro → explanation → callout → demo → quiz). If the reader can predict the next element type, reshuffle.
- **Paragraph uniformity** — Every paragraph is 3–5 sentences. Vary deliberately: a one-sentence paragraph for impact, a seven-sentence paragraph for a careful argument.
- **Frictionless transitions** — Everything flows too smoothly. No surprise, no "wait, what?", no moments where the reader has to recalibrate. Good explainers have moments of productive friction.
- **Uniform depth** — Everything explained to the same level of detail. No fast parts, no slow parts. Good explainers sprint through easy material and linger on hard material.
- **No personality** — No opinion, no humor, no confessions of difficulty, no moments where the author is visibly a person. If you could swap the byline without anyone noticing, the voice is too neutral.
- **Opening tics** — "Let's explore..." / "In this section, we'll..." / "So, what exactly is X?" / "Have you ever wondered..." — these are throat-clearing, not writing. Cut them.
- **Lists where prose should be** — A list of 5 things is sometimes laziness. Can you weave them into a paragraph that builds an argument? Lists are for reference; prose is for understanding.
- **Equal-weight treatment** — Every concept gets exactly one paragraph of setup, one of explanation, one of consequence. This mechanical balance is a hallmark of generated text. Some concepts need a sentence; some need a page.
- **Decorative demos** — A demo that only confirms what the prose already said. If the demo doesn't reveal something new, cut it and let the prose stand alone.

---

## Topic Selection Criteria

**Good topics:**
- Has a "why does this work?" that most treatments hand-wave or skip entirely
- Has at least one genuinely surprising property or connection
- Is foundational — understanding it unlocks several other concepts
- Has natural interactive moments — concepts that are better felt than read
- The math, properly set up, is elegant

**Poor topics:**
- Pure taxonomy ("there are 12 types of activation functions")
- So broad it needs 30+ sections (cover a sub-concept instead)
- So narrow it connects to almost nothing else
- Topics where the standard explanation is already genuinely excellent (build something different instead)

---

## New Explainer Checklist

1. [ ] State the core tension in two sentences
2. [ ] Write the one-insight statement
3. [ ] Identify the topic type (for thinking, not structure)
4. [ ] Map prerequisites — what must the reader know? What needs explanation?
5. [ ] List the section skeleton — titles + one-line job descriptions
6. [ ] List the key misconceptions for quiz design
7. [ ] Plan demos — each with a "surprising observation Z" that justifies its existence
8. [ ] Write the opening — does the reader feel the tension within two paragraphs?
9. [ ] Read SKILL.md for implementation
10. [ ] Write the opening section first — if you can't make the tension feel urgent, rescope
11. [ ] Build demos alongside prose — they emerge from writing, not after it
12. [ ] Write quizzes last — only once you know which misconceptions the prose creates
13. [ ] Run the anti-slop checklist — if more than two items trigger, restructure
14. [ ] Read the draft aloud — stumble = rewrite
15. [ ] Add card to hub `index.html`

---

## Reference: The Work We Aspire To

**For structure and rigor:**
- **paraschopra.github.io/explainers** — our direct inspiration; clean prose, earned abstractions, well-placed interactives
- **distill.pub** (archived) — the gold standard: academic rigor with interactive diagrams
- **3blue1brown** — earns every abstraction through concrete visuals before any formula
- **Andrej Karpathy's blog** — direct, starts with code/numbers, no motivational padding
- **The Feynman Lectures** — how to make a physical law feel like the only possible conclusion
- **Michael Nielsen** (Neural Networks and Deep Learning) — patient, builds everything from scratch, never hand-waves, treats the reader as capable

**For voice and unpredictability:**
- **WaitButWhy** (Tim Urban) — unpredictable structure, humor as pedagogy, extended metaphors that ARE the explanation, not illustrations of it. The stick figures aren't decorations — they're the argument.
- **Paul Graham** — short, opinionated, first-person essays that make the reader feel like they're thinking alongside the writer. Shows that brevity and depth aren't opposites.
- **The New Yorker longform** (Atul Gawande, James Gleick, Rivka Galchen) — scene-setting, narrative arc, sentences that are beautiful on their own. Shows that technical explanation can also be literature.
- **Geoffrey West's "Scale"** — how to make a hard mathematical idea feel inevitable through accumulated evidence and sheer narrative momentum

**For interactivity and play:**
- **Bret Victor** ("Explorable Explanations", "Up and Down the Ladder of Abstraction") — interactives that ARE the argument, not illustrations of it. The reader doesn't watch — they participate.
- **Nicky Case** (Parable of the Polygons, Evolution of Trust) — playful tone, game-like structure, learning through play. Shows that serious concepts can be explored with joy.

The common thread: they all **slow down at the hard part.** They sit with the confusing thing until it becomes clear. But each does it in a completely different way — different structure, different voice, different ratio of prose to interactivity. That variety is the point.

---

*Companion file: `SKILL.md` — design system, CSS, all interactive component recipes, audio system.*
