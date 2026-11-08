---
name: foreverlm-walkthrough
description: Write a walkthrough of a paper, book chapter or article in ForeverLM that explains it the way a good textbook does — simply and step by step, one point at a time — with short questions woven in right after the points they test, in the style of quantum.country's mnemonic medium, so reading and remembering happen in one pass; and save it with save_walkthrough. Use when the user asks to make, write or redo a walkthrough, to be walked or talked through a source, or for reading with questions along the way, quantum.country style, mnemonic medium, or spaced-repetition prompts in the text.
---

# ForeverLM walkthrough with questions along the way

A walkthrough is a deck the learner reads beside the source, one point a card. This skill writes one the way quantum.country's *Quantum computing for the very curious* is written: the explanation stops every few points to ask a few small questions about what was just said, the learner answers in their head, turns the card, and marks Forgot or Remembered. The questions are not a test at the end. They are part of the reading, and they are what makes the reading stick.

The deck, its cards and the recall prompt are ForeverLM's; this skill writes the content and saves it with `save_walkthrough`. Never read `knowledge.db` or any other app file directly; a plugin failure is reported, not worked around.

## Read before writing

1. Find the source (`search_sources_keyword`, `search_sources_semantic` or `list_sources`) and call `get_walkthrough` on it. If one exists, read it: a rewrite replaces it, so keep what already works.
2. Read the whole text with `get_source_content`. For a paper or a chapter split from a PDF, look at the figures that carry the argument with `inspect_paper_figure`, which also lists each figure's panels. An article or an EPUB chapter has no figures to point at, so its steps anchor to passages alone.
3. Write down the argument before any card: three to seven claims in teaching order, each one sentence the learner should be able to say back — in plain words, about twenty at most, one point — and specific enough to be wrong. Where the source argues rather than reports (an essay, a letter, a proposal), say whose each claim is, and keep what is established apart from what is proposed.
4. Read the learner's About you (`get_app_settings`, `account.about_you`). When it is set, pitch the walk to them. When it is not, write for a newcomer to the field. These become the chapters and their `claim` recap cards. Order them so the walk meets the figures as the source prints them, Figure 1 before Figure 2 and panel a before panel b, unless the argument cannot be taught that way. A headline the abstract states first is set up in words, and its figure is shown where that figure falls.

## Explain it the way a good textbook does

A walk that restates the source in its own vocabulary, one sentence a card, can be faithful to every line and still be unintelligible to anyone who did not already know that vocabulary. Write for a learner who is bright and willing but new to the field, unless their About you says otherwise. Too much help costs someone who knows the field a moment; too little loses a newcomer for the rest of the walk.

- **Concrete first.** Start an abstract claim (a formalism, a principle, an analogy between fields) from a case the learner can picture: the system the source studies, a familiar mechanism, an everyday example. Name the general idea only after it.
- **Parts before process.** Before saying how something works, name its few parts (the molecules, quantities, players or stages) and what each one does.
- **Terms in the cards, before they are used.** Teach every term the argument needs in a beat of its own, in plain words, where it first matters and before the step that uses it, with an example where that helps. Never leave a needed term to a `more` chip alone. A short run of such beats may open a chapter.
- **One or two new things a card.** At most one or two new terms or names on a card. Leave out a name, a coinage or a company the walk never uses again ("a DeepMind researcher argues…" is often enough).
- **Translate, don't reword.** Never copy the source's sentences onto a card, and never reword them into sentences just as dense. Say in plain words what they mean, then give the source's term once the idea is clear.
- **Say how it follows.** Let each beat carry on from the one before in words ("So…", "That is why…", "But…", "In other words…"), so that a run of cards reads as one explanation rather than a list.
- **Processes in order.** Tell a process a step at a time, each step saying what it causes.
- **Analogies labelled.** Say when something is an analogy, and where it stops holding.
- **Established or proposed.** Where the source mixes established results with its own proposal or speculation, say which is which.
- **Your own knowledge.** Beyond the opening and closing field context, it enters only as textbook background you are certain of: what a term means, a well-established mechanism the source takes for granted, a concrete example. Never pass it off as the source's finding.

Before saving, read the cards alone and in order, as someone who has never met the field's words. Wherever you would stop to ask "what does that mean?" or "why does that follow?", a beat is missing.

## The questions

This is the other half of what makes a walk work. The rules below are quantum.country's (Andy Matuschak and Michael Nielsen, and Matuschak's *How to write good prompts*).

**Where they go.**
- Put a question right after the point it tests, while that point is still on screen. Ask two or three together after a short run of points, the way quantum.country stops after a few paragraphs. Don't save them all for the end.
- Every chapter ends on its questions, then its claim.
- Aim for one question every two to three explain or figure steps. ForeverLM allows at most 20 questions and 40 other steps, so a long source gets its questions where the ideas are densest. Don't spread them evenly.
- Ask nothing about a point the cards have not made yet. The exception is `think_first` and `predict`, which ask on purpose before the evidence.

**What each one asks.**
- *One detail each.* One question, one fact, one short answer. If the answer is a list, split it into several questions, or ask for the single item that matters.
- *Precise.* Only one answer should be right. "What does Figure 2 show?" is vague. "In Figure 2b, what happens to membrane potential when FCCP is added?" has one answer.
- *Short to check.* The learner compares their answer with yours at a glance: a word, a number with its unit, a direction (rises / falls), a name, or one clause. The `answer` field leads with that short answer. At most one more sentence follows, saying why.
- *Takes some effort.* Nothing answerable from the question's own wording, no yes/no, and no copying a card's sentence with a word blanked out. The learner should have to pull the idea back up.
- *Consistent.* The same question asked again next week has the same answer. Nothing like "what surprised you".
- *Approach from more than one side.* The important ideas get two or three questions from different angles. Term to meaning ("What does ΔΨm measure?") and meaning to term ("What is the voltage across the inner mitochondrial membrane called?"). Cause and effect both ways. A number and what it is compared with. A method and why it was chosen over the obvious alternative.
- *Aim at the argument.* A question strengthens what it asks and little else, so most questions ask about the ideas the claims rest on: what a panel or passage showed and what that means for the claim, what causes what, the key quantity and what it is compared with, why a control was needed. Some ask **why** ("Why does uncoupling lower ATP output even though oxygen use rises?"). Some are about **application** ("If the authors are right, what should happen to X in condition Y?"). Ask about a term only when the rest of the walk keeps using it, and then ask what it means, never "What does the author call X?" when the idea behind X is what matters. Leave out trivia the argument doesn't need, like author names, dates, sample sizes that carry no claim, which journal, or a list to recite.
- *Context without a giveaway.* A question stands alone and will be read weeks later, away from the cards. So put the needed context in the question itself ("In the MitoSpace paper, …") and keep the answer out of it.

**Which kind.** Use `recall` for nearly all of them, which is quantum.country's kind. Keep the other three for a few moments that earn them. `think_first`: ask before the evidence, when the learner's own guess makes the result land, and about the claim the chapter turns on, since a question asked first helps only with what it asks. `predict`: hide a panel's data until the learner has guessed the curve, where the result is not what a newcomer would expect. `find`: the learner picks the panel that answers, when finding it on the page *is* the skill. All four are open questions. Never multiple choice.

**`key_points`.** Give one to three. For an atomic recall question there is often one, the answer itself. A mechanism question lists the two or three links a complete answer names.

## The cards between the questions

Follow `save_walkthrough`'s own contract; its description is authoritative. In short:

- Pass `chapters`, one per claim. Each has a `title`, its `claim`, and `steps` in reading order.
- The first step places the source in its field: what was known before, what problem or debate it enters, why the question matters beyond its own system. The last step says what changes for the field if the authors are right.
- A step makes one point. Write it as two to six `beats`, one short sentence each. Each beat is a card of its own. The last beat is the takeaway.
- A `figure` step is about one panel (`figure_label` exactly as printed, `panel_label`). Its beats read the panel before interpreting it: where to look, that panel's own axes and units (never a neighbouring panel's), the legend, what the data do, what it means. A panel gets a step of its own because it carries a claim, not because it is printed. But a figure you show has every panel `inspect_paper_figure` lists addressed before the walk leaves it: each of the others gets a beat saying what it shows.
- Walk each figure in one run of steps, its panels in order. If a step outlines a figure ahead of a lower-numbered one the learner has not seen yet, its first beat says why, and when the earlier figures come. Come back to a panel at most once, on purpose. To remind the learner of a figure already walked, give the step a `picture` of it instead of outlining it again.
- An `explain` step anchors to a `passage` only when the point is in the source's own words, copied exactly.
- Use `more` chips for context a newcomer may want and the argument can do without: a method's details, a term's history, a side question. Never for a term the argument needs (above).
- When a step's point rests on a work the source names or cites (the paper it argues with, the book it draws on), put that work in the step's `cites`: `{"title", "authors", "year", "venue", "kind", "doi", "url"}`, with the title and authors as the source or its reference list prints them (authors separated by semicolons). The card shows it under the point as a source the learner can open or add. Give a `doi` or `url` only when the source prints it or you opened the page yourself; never one from memory. Pass `source_id` when the library already holds the work. Never cite the source you are walking through.
- Every point card shows a picture. A figure step shows its panel. Another step gets a `picture` where its point moves on to something the last picture does not show. The picture is either a figure it shows without outlining (`{"figure_label": "Figure 1", "panel_label": "a"}`) or a picture you made yourself and kept with `save_image` (`{"image_id": "…"}`). A step without a picture shows the one before it in its chapter, so one picture serves a run of cards about one idea. An article or an EPUB chapter has no figures, so all of its pictures are ones you make. Make a teaching diagram: one idea, the parts and how they relate, a few short labels, no decoration and no invented data.
- A step's animation gets a card of its own after the step's points, with no figure above it.
- A question is a `quiz` step of its own (`kind: "quiz"`, a short `title`, and `quiz` with `kind`, `question`, `answer`, `key_points`). For `find` it also takes `target_panel`. When the question is about a panel, give it the panel's `figure_label` and `panel_label`, so the card shows it.

The cards should read well without the questions and be remembered because of them. If a card makes a point no question ever comes back to, ask whether the argument needs that point. If a question tests something no card said, the card is missing.

## Save and check

1. Call `save_walkthrough` with `source_id`, `title` (without the word "Walkthrough"), and `chapters`.
2. Read the result's warnings. A figure label or passage that failed its check keeps its words but loses its scroll target. Fix the anchor and save again, and don't leave it silently broken. A warning that a step outlines a figure early, or leaves a panel unaddressed, means the walk jumped ahead or skipped a panel: fix it, or make sure the jump's first beat says why.
3. Report to the learner: the claims in order, how many cards and questions, and anything the warnings left unfixed. The walkthrough opens beside the source in the Reader and on the phone.

## Walking someone through it in conversation

When the learner wants to be walked through a source here in the chat instead of in the app, keep the same rhythm. Use the saved walkthrough (`get_walkthrough`) when there is one, otherwise the plan above. Give one point, then the next. At each question, ask it and **stop**: wait for the learner's answer before giving yours. If the answer is on the right track but short, ask one short follow-up that points at what is missing without giving it away. Then say what was right, what was missing, the reference answer, and in a sentence or two why it is right, pointing back to the card that showed it, and go on. Save the conversation as the ForeverLM plugin's instructions say, with `import_review_chat`, word for word.

## What this does not do yet

Quantum.country brings every question back on an expanding schedule. In ForeverLM, a question the learner marks Forgot on the Mac, or a Find it they get right only on the second try, becomes a Memory item, grounded in the passage of the step it follows, and voice review and `memory_review` bring it back. A question remembered the first time is not scheduled again, the Mac's daily memory review does not ask walkthrough questions yet, and the phone keeps its grades in the walk's chat. Don't promise the learner more than that.
