# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start  
  (for example: "the hints were backwards").

The first time I ran the game it looked playable but immediately felt wrong. The hint system was backwards — no matter what I guessed, the game kept telling me to go higher even when my guess was already above the secret number. The "New Game" button also didn't actually restart the game; after winning or losing, clicking it would show the end screen again without letting me play because the status was never reset to "playing". The range display was always "1 and 100" no matter which difficulty I chose, and on every even-numbered attempt the secret was secretly cast to a string, causing completely wrong comparisons.

**Bug Reproduction Log**

Document at least 3 bugs you found. Add rows as needed.

| Input | Expected Behavior | Actual Behavior | Console Output / Error |
|-------|-------------------|-----------------|------------------------|
|-------|Attemptes allowed and attemps left, 8 - 7|8 - 7|------------------------|
|Guess the number bewteen doesnt change when switching difficuly|Range should change when switching difficulty|stuck at 1 - 100|------------------------|
|2------| higher------------| higher----------|None
|50-----| Lower-------------| higher----------|None
|70-----| Lower-------------| higher----------|None
|85-----| Lower-------------| higher----------|None
|90-----| Lower-------------| higher----------|None
|98---- | Lower-------------| higher----------|None
|100----| Lower-------------| higher----------|None
|New Game| restart the game| the game is stuck|----------|None


---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

I used Claude (via VS Code) as my AI coding assistant throughout this project. I attached app.py and logic_utils.py so the AI could see both files and understand how the UI and logic were connected.

I asked Claude to explain why hints were wrong on even-numbered attempts. It correctly identified that lines 170–173 in app.py were casting the secret to a string on even attempts, causing Python's string comparison (`"9" > "10"` is `True`) instead of numeric comparison. It suggested removing the entire even/odd branching block and always passing the secret as an integer. I verified this by running the game and guessing numbers on the second attempt hints were correct every time after the fix.

Claude initially suggested keeping `check_guess` returning a tuple `(outcome, message)` and updating the test file to unpack the tuple. This was misleading because the tests were the specification — changing them to match broken code defeats the purpose of testing. I rejected this and instead rewrote `check_guess` in `logic_utils.py` to return just the status string, and handled display messages separately in `app.py` using a `hint_map` dictionary.

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

I decided a bug was really fixed when two things were both true: the pytest test for that behavior passed, and I could reproduce the scenario manually in the live Streamlit app and see the correct result. Passing tests alone weren't enough because some bugs (like the hardcoded range display) didn't have automated tests covering them.

I ran `pytest tests/ -v` after completing the refactor into `logic_utils.py`. All three tests — `test_winning_guess`, `test_guess_too_high`, and `test_guess_too_low` — passed immediately because `check_guess` now returns plain strings and compares integers correctly. For the New Game bug, I tested manually: I played until I won, clicked New Game, and confirmed the game reset to the playing state instead of locking up on the win screen.

The AI helped me understand what the tests were actually asserting. I asked it to explain why `assert result == "Too High"` was failing, and it walked me through the return type mismatch — the old `check_guess` returned `("Too High", "📉 Go LOWER!")` but the test compared against the bare string `"Too High"`. That explanation made the fix obvious.

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

Streamlit reruns the entire Python script from top to bottom every single time the user interacts with the page — clicking a button, typing in a field, or changing a selectbox all trigger a full rerun. This means any regular Python variable you create gets reset to its starting value on every rerun. Session state (`st.session_state`) is Streamlit's solution: it's a dictionary that persists across reruns for the lifetime of one browser session. You store anything that needs to survive a rerun — like the secret number, the attempt count, or the game status — inside `st.session_state` instead of a plain variable. Think of it like a save file: the script reloads every time, but the save file stays put.

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.

One habit I want to carry forward is marking "crime scenes" in code with `# FIXME` comments before touching anything. Labeling exactly where the broken logic lives — and why it's broken — made it much easier to describe the problem to the AI and get a useful response. Vague prompts get vague answers; pointing at a specific line gets specific help.

Next time I work with AI on a coding task, I would give the AI the test file first and say "make the code pass these tests" rather than describing the desired behavior in prose. Tests are precise; English descriptions leave room for misunderstanding, which is how I ended up with the AI suggesting I change the tests instead of the code.

This project changed how I think about AI-generated code by showing me that AI writes plausible-looking code that can be subtly wrong in ways that only show up during actual use. AI is a fast first draft, not a finished product — you have to read it critically and test it like you would any other code.
