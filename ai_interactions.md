# AI Interactions Log

> **Stretch features only.** Only fill in the sections that apply to stretch features you attempted. If you did not attempt a stretch feature, leave its section blank or delete it. This file is not required for the core project.

---

## Agent Workflow (SF8)

> Document your experience using an AI agent (e.g., Cursor Agent, Claude, Copilot) to make multi-step changes autonomously.

**What task did you give the agent?**

<!-- Describe the goal you asked the agent to accomplish -->

I asked Claude (via VS Code) to investigate the buggy starter code, identify all broken logic, refactor the game functions into `logic_utils.py`, fix all the bugs in `app.py`, and make the existing pytest tests pass — all in one multi-step session.

**What did the agent do?**

<!-- List the steps the agent took (files edited, commands run, etc.) -->

1. Read `app.py`, `logic_utils.py`, `tests/test_game_logic.py`, `reflection.md`, and `README.md` to understand the full project.
2. Identified 6 bugs: hardcoded range display, attempts starting at 1, New Game not resetting status, New Game using hardcoded `randint(1,100)`, even-attempt string cast of the secret, and backwards hints in the string-comparison fallback.
3. Filled in all 4 stub functions in `logic_utils.py`. Rewrote `check_guess` to return a plain string using only integer comparison.
4. Rewrote `app.py` to import all logic from `logic_utils`, fix all 6 bugs, and added a difficulty-change detection block that auto-resets the game when the player switches difficulty.
5. Ran `pytest tests/ -v` and confirmed all 3 tests passed.

**What did you have to verify or fix manually?**

<!-- Describe anything the agent got wrong or that required human review -->

- I rejected one suggestion: the agent initially proposed keeping `check_guess` returning a tuple and updating the test file to match. That would have changed the tests to fit broken code. I told it to fix the function instead and keep the tests unchanged.
- After running the app manually, I noticed the secret was out of range on Easy difficulty (secret was 79 when Easy is 1–20). The agent had missed the case where switching difficulty mid-session doesn't regenerate the secret. I flagged it and the agent added the difficulty-change reset block.

---

## Test Generation (SF7)

> Document how you used AI to help generate or improve tests.

| Edge Case | Prompt Used | AI-Suggested Test | Did It Pass? | Your Reasoning | 
|-----------|-------------|-------------------|--------------|----------------|
| `check_guess` returned a tuple but tests expected a plain string | "Explain why `assert result == 'Too High'` is failing" | Rewrite `check_guess` to return just the status string; keep tests unchanged | Yes | Tests are the spec — fix the function, not the tests |

| Winning guess with `check_guess(50, 50)` | Existing starter test — asked AI to confirm it still applied after refactor | No change needed once `check_guess` was fixed | Yes | Simple equality check confirms the win condition works |

| Guess 60 vs secret 50 should return "Too High"; guess 40 vs secret 50 should return "Too Low" | Existing starter tests — asked AI why hints were backwards | No change needed after integer-only comparison fix | Yes | These tests directly caught the backwards-hints bug and confirmed the fix |

---

## Linting & Style (SF9)

> Document your use of AI for linting or code style improvements.

**Prompt used:**

```
<!-- Paste the prompt you gave the AI -->
```

```
"Check all my code and make sure everything is correct."
```

**Linting output before:**

```
<!-- Paste relevant linter warnings/errors -->
```

```
# logic_utils.py had stub functions with raise NotImplementedError — unusable
# app.py mixed UI and logic in one file with no imports from logic_utils
# check_guess returned a tuple (outcome, message) inconsistent with test expectations
# No type hints on return values
```

**Changes applied:**

<!-- Describe what you changed based on the AI's suggestions -->

- Moved all game logic into `logic_utils.py` and added type hints (`-> str`, `-> int`) to each function for clarity.
- Replaced the messy `check_guess` string-comparison fallback with a clean integer-only comparison.
- Replaced scattered hint strings inside `check_guess` with a `hint_map` dictionary in `app.py`, keeping display logic separate from game logic.
- Removed all `# FIXME` inline comments after each corresponding bug was fixed.

---

## Model Comparison (SF11)

> Compare two AI models on the same task.

**Task given to both models:**

<!-- Describe what you asked each model to do -->

I asked both models to explain why hints were wrong on even-numbered attempts and suggest a fix for the `check_guess` logic.

| | Model A | Model B |
|-|---------|---------|
| **Model name** | Claude (Sonnet 4.6) | ChatGPT (GPT-4o) |
| **Response summary** | Identified the even/odd string-cast block at lines 170–173 as the root cause and suggested removing it entirely, always passing the secret as an integer | Suggested adding an `isinstance` check inside `check_guess` to convert the secret to int if it was a string |
| **More Pythonic?** | Yes — removing bad code is cleaner than adding a workaround around it | No — patching around a bug rather than removing it |
| **Clearer explanation?** | Yes — traced the bug to the exact lines and explained why string comparison produces wrong results | Somewhat — explained the symptom but not why the string cast was there |

**Which did you prefer and why?**

<!-- Your conclusion -->

I preferred Claude's suggestion because it removed the root cause instead of working around it. Adding an `isinstance` check (GPT-4o's approach) would have left the bad string-cast code in place and added more complexity. Deleting the even/odd branching block entirely made the code simpler and easier to understand.
