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

- **Game's purpose:** A simple Streamlit number-guessing game.
The app picks a secret number in a range based on the chosen difficulty, and the player
guesses until they find it. After each guess the game gives a directional hint (go higher
or go lower), tracks attempts and score, and ends on a correct guess or when attempts run
out. A "Developer Debug Info" panel reveals the secret number for testing.

- **Bugs found:**
1. **Reversed hints** — `check_guess` told the player to "Go HIGHER" when their guess was
   too high and "Go LOWER" when too low, leading them away from the answer.
2. **Secret cast to a string on even attempts** — `app.py` ran
   `secret = str(st.session_state.secret)` on even-numbered attempts, breaking numeric
   comparisons. Python then compared the numbers as text, character by character
   (so "7" counts as bigger than "10", the way words are ordered alphabetically),
   which made the hints nonsense.
3. **"New Game" didn't fully reset** — it reset attempts and the secret but never set
   `status` back to `"playing"`, leaving a finished game stuck on the "You already won" /
   "Game over" screen.

- **Fixes applied:**
1. Refactored the four core functions out of `app.py` into `logic_utils.py`, and fixed
   `check_guess` so a too-high guess returns "Too High" / "Go LOWER" and a too-low guess
   returns "Too Low" / "Go HIGHER".
2. Removed the even-attempt string cast in `app.py` so `check_guess` always receives the
   integer secret. This had to ship together with fix #1 — fixing the hints alone would
   have crashed on even attempts once the old string fallback was removed.
3. Reset `st.session_state.status = "playing"` in the New Game handler so a finished game
   becomes playable again.


## 📸 Demo Walkthrough

Describe your fixed game in numbered steps so a reader can follow along without watching a video:

1. App launches on default difficulty "Normal" (secret is between 1 and 100).
2. User enters a guess of 40, which is below the secret → game returns "Too Low" with the
   hint "📈 Go HIGHER!"
3. User enters a guess of 70, which is above the secret → game returns "Too High" with the
   hint "📉 Go LOWER!"
4. User enters a guess of 50, still below the secret → "Too Low" / "📈 Go HIGHER!"
5. User enters the exact secret → "🎉 Correct! You won!", balloons appear, and the final
   score is displayed.
6. User clicks "New Game" → the game resets with a new secret and is immediately playable
   again.

**Screenshot** *(optional)*: 

## 🧪 Test Results

```
...                                                                    [100%]
3 passed in 0.00s
​```

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, describe the Enhanced UI changes here — a screenshot is optional]
