---
name: foreverlm-ai-cost-review
description: Analyze what the learner spends on AI in ForeverLM from the app's own usage ledger, find the tasks and models that dominate the bill, and propose concrete Settings changes that lower it, such as a cheaper catalogued or free model for background work. Use when the user asks to analyze their AI costs, what they are spending on AI or on models, why their balance drains, how to reduce AI costs, or whether to switch to cheaper models. Apply a change only after the learner approves it.
---

# ForeverLM AI cost review

The learner's spend lives in ForeverLM's AI usage ledger, and the model every task runs on is a setting in Settings > AI Models. This skill reads both through the plugin, proposes cheaper settings with an estimated saving, and applies only what the learner approves. Never read `knowledge.db` or any other app file directly; the plugin is the route, and a plugin failure is reported, not worked around.

## Read before proposing

1. Call `get_mcp_status`. Both readers and the writer are answered by the learner's Mac; if the Mac is not reachable, say so and stop.
2. Call `get_ai_usage_report`. Default to the last 30 days; use `days`, or `from` and `to` (local `YYYY-MM-DD`, `to` inclusive), when the learner names a period. Read `totals`, `by_task` (sorted by spend, each with the models it ran, `per_call` averages and `current_route`), `by_model` and `by_day`.
3. Call `get_ai_task_settings`. It is Settings > AI Models as data: `default_model`, `background_text`, `background_multimodal`, every background task in `tasks` with the model it runs on and what it `follows` (`background_text`, `background_multimodal`, or `task_model` when the learner gave it a model of its own), `free_models`, and the `catalogue` with each model's `price` per million input, output, cached-input and cache-write tokens, `supports_vision` and `selectable`. Every task that follows `background_text` runs on that one model, every task that follows `background_multimodal` on the other, and a task that follows `task_model` on its own.

## What the numbers mean

- `cost_usd` is what the provider billed when it reported an amount, else the list-price estimate stamped when the call was recorded. `unpriced_calls` were excluded from every total: a total with unpriced calls beside it is a floor, and you say so.
- `per_call.cost_usd` divides the priced total by the priced calls only. `per_call.input_tokens`, `per_call.output_tokens` and `per_call.cache_read_tokens` are averages over all of the task's calls.
- `current_route` is what the task would run on now. It may differ from the models in the task's `models` list, which are what actually ran during the period; base the saving on the current route's price, and mention when a recent settings change already moved the task.
- A task with `configurable: false` (for example `read_aloud` or `chat_quiz_grading`) follows the feature that made it and has no setting of its own.
- `price_known: false` means the pricing table has no row for that model. Never fill the gap with a guess; write "unknown" and leave that row out of the arithmetic.

## Find what dominates

Rank `by_task` by `cost_usd` and name the tasks that make up most of the spend. For each, note its call count, average tokens in and out, cache read share, and the route it runs on. Then look at `by_model` for a frontier model that several small background tasks share: that is usually where the saving is.

## Propose cheaper settings

Background work has two shared settings, and a task can also have a model of its own. Group the dominating tasks by what they `follow` and consider, in this order:

1. **A cheaper catalogued model for that Background model.** Only a model from the `catalogue` with `selectable: true`, on the same or a comparable class (a Flash, Haiku, mini, or an open-weight model on OpenRouter). `background_multimodal` takes only a model whose `supports_vision` is `true`. The change moves every task that follows it, so weigh it against the most demanding of them, not only the most expensive. When one task dominates on its own, or already follows `task_model`, propose a cheaper catalogued model for that task alone instead; a task with `requires_vision` takes only a vision model.
2. **A free model from `free_models`.** It costs nothing for now, but its providers may keep and train on what is sent, it is often busy or rate-limited, and it may be free only for a limited time; say all three. Meeting tasks stay on a paid model while `meetings_may_use_free` is false, so moving `background_text` to a free model does not move them.

Never propose a model the catalogue does not list, a provider that is not in `accepts.providers`, or a retired model. A task that follows `default_model` runs on the chat's model; leave that alone unless the learner asks about chat itself, since a cheaper chat model changes the quality of every conversation.

## Estimate the saving honestly

For a model change, take each affected task's `per_call.input_tokens` and `per_call.output_tokens`, multiply by the price difference per million tokens between the current route and the proposed model, multiply by the period's `calls`, and add up the tasks that follow the same Background model. Use `cache_read_tokens` with the cached-input price when both models publish one. Write the result as "about $X over the last N days at the same usage" and show the arithmetic. When either price is unknown, or the task's calls are mostly unpriced, say plainly that the saving cannot be estimated for that row.

## Present, then ask

Present the proposal as a table before touching anything:

| Setting | Tasks on it | Calls | Spend | Runs on | Proposed | Estimated saving |
|---|---|---|---|---|---|---|

Under it, note the trade-off of each row in one line (a smaller model reads fewer nuances; a free model may keep what is sent). Then ask which rows to apply. Do not apply anything the learner has not approved, and do not treat a general "reduce my costs" as approval of a specific model change.

## Apply what was approved

For each approved row call `set_ai_task_settings` once:

- A Background model: `target: "background_text"` or `"background_multimodal"` with `provider` and `model`.
- One task's own model: `target: "task_model"` with `task_id`, `provider` and `model`; with neither `provider` nor `model` the task goes back to following its Background model.
- The Default Model: `target: "default_model"` with `provider` and `model`, only when the learner asked for it.

The reply carries the resulting settings and `changed`; read them back and confirm each applied row against what was approved. A refused selection comes back as an error naming the rule that refused it; report it rather than retrying with a different model on your own.

## Report

Close with the period, the total spend and how many calls were unpriced, the rows applied, the rows the learner declined, and the estimated saving of what was applied. Suggest running the same report after a week to compare.
