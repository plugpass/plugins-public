---
metadata:
  plugpass-component-id: skill_6Z5sZpBN
name: deep-brainstorm
description: Generate a wide range of ideas across multiple exploration angles, then refine down to the strongest few. Use when the user wants richer, more curated brainstorm results than `quick-brainstorm` produces.
allowed-tools: AskUserQuestion, Task
---

## Step 1: Get the topic

Read the user's most recent message. If a brainstorm topic is stated, use it. Otherwise call `AskUserQuestion`:

> What would you like to do a deep brainstorm on?

with starter options like "Product names", "Feature concepts", "Marketing taglines", "Project directions". The user can also type their own answer.

## Step 2: Generate 4 exploration angles

Produce 4 distinct angles that re-frame the topic, each pulling ideas from a different conceptual neighborhood. Examples for "productivity-app startup names":

- "unusual real-world words"
- "compound made-up words"
- "metaphors from nature"
- "tech-adjacent puns and wordplay"

Phrase each angle as a one-line instruction the agent can follow.

## Step 3: Generate ideas across all 4 angles in parallel

Send a single message containing 4 parallel `Task` tool calls — one per angle. Each call:

- `agent_type`: `"idea-generator"`
- `description`: the angle in 3–5 words
- `prompt`: `"Generate 5 brainstorm ideas about: <topic>. Exploration direction: <angle>. Return a numbered list with one short rationale per idea."`

Sending them in one message is what makes them run concurrently. The result: 20 candidate ideas across 4 angles.

## Step 4: Refine to the top 10

Spawn the `idea-refiner` agent via `Task`, passing all 20 candidates (clearly attributed to their angle of origin) and the topic:

- `agent_type`: `"idea-refiner"`
- `description`: `"Refine brainstorm candidates"`
- `prompt`: A clear instruction including the topic, the 20 candidates with their angles, and `"Filter down to the top 10 by novelty, fit-to-topic, and memorability. Return a ranked list with the originating angle noted next to each."`

## Step 5: Surface the ranked list

Show the refiner's response to the user verbatim. Then end the skill.
