# 🎮 Game Glitch Investigator: The Impossible Guesser

## 🚨 The Situation

You asked an AI to build a simple "Number Guessing Game" using Streamlit.
It wrote the code, ran away, and now the game is unplayable. 

- You can't win.
- The hints lie to you.
- The secret number seems to have commitment issues.

## 🛠️ Setup

1. Install dependencies: `pip install -r requirements.txt`
2. Run the broken app: `python -m streamlit run app.py`

## 🕵️‍♂️ Your Mission

1. **Play the game.** Open the "Developer Debug Info" tab in the app to see the secret number. Try to win.
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: *"How do I keep a variable from resetting in Streamlit when I click a button?"*
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

- [ ] Describe the game's purpose.

  This is a number-guessing game built with Streamlit. The player picks a difficulty (Easy: 1–20, Normal: 1–100, Hard: 1–500), then guesses the secret number within a limited number of attempts. Each guess returns a "Too High" or "Too Low" hint and the score updates based on how quickly the player wins.

- [ ] Detail which bugs you found.

  1. Attempts counter started at 1 instead of 0, so "Attempts left" showed one fewer than it should from the start.
  2. The range display was hardcoded as "1 and 100" and never updated when switching difficulty.
  3. On every even-numbered attempt, the secret was cast to a string, causing string comparison instead of numeric — producing wrong hints half the time.
  4. The string-comparison fallback in `check_guess` also had hints backwards.
  5. Clicking "New Game" never reset `status` to `"playing"`, locking the game on the win/loss screen.
  6. "New Game" used a hardcoded `randint(1, 100)` instead of the selected difficulty's range.

- [ ] Explain what fixes you applied.

  Moved all game logic into `logic_utils.py`. Rewrote `check_guess` to return a plain string using only integer comparison. Fixed `app.py` to initialize `attempts` at 0, use `{low}` and `{high}` for the range display, reset `status` to `"playing"` on New Game, use `randint(low, high)`, and removed the even/odd string-cast block entirely.

## 📸 Demo Walkthrough

Describe your fixed game in numbered steps so a reader can follow along without watching a video:

1. User opens the app and selects "Normal" difficulty. The sidebar shows Range: 1–100, Attempts allowed: 8. The banner reads "Guess a number between 1 and 100. Attempts left: 8."
2. User enters 50 and clicks Submit. The game returns "📈 Go HIGHER!" — the secret is above 50. Attempts left drops to 7.
3. User enters 75. The game returns "📉 Go LOWER!" — the secret is below 75. Attempts left drops to 6.
4. User enters 62 and sees "📈 Go HIGHER!" Attempts left: 5.
5. User enters 68 and sees "🎉 Correct!" Balloons appear, the final score is displayed, and the status locks to "won."
6. User clicks "New Game." The game fully resets — new secret, attempts back to 8, score back to 0 — and is immediately playable again.

**Screenshot** *(optional)*: <!-- Insert a screenshot of your fixed, winning game here -->

## 🧪 Test Results

(venv) PS D:\CodePath\AI110\ai110-module1show-gameglitchinvestigator-starter-main> python -m pytest tests/ -v
>> 
============================================ test session starts ============================================
platform win32 -- Python 3.13.5, pytest-9.0.3, pluggy-1.6.0 -- D:\CodePath\AI110\ai110-module1show-gameglitchinvestigator-starter-main\venv\Scripts\python.exe
cachedir: .pytest_cache
rootdir: D:\CodePath\AI110\ai110-module1show-gameglitchinvestigator-starter-main
plugins: anyio-4.13.0
collected 3 items                                                                                            

tests/test_game_logic.py::test_winning_guess PASSED                                                    [ 33%]
tests/test_game_logic.py::test_guess_too_high PASSED                                                   [ 66%]
tests/test_game_logic.py::test_guess_too_low PASSED                                                    [100%]

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, describe the Enhanced UI changes here — a screenshot is optional]
