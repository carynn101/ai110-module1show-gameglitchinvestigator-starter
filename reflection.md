# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?

  The app launched fine in the browser at localhost:8501 and looked like a normal
  Streamlit number guessing game. It had a title, a difficulty selector, a guess box, and
  Submit / New Game buttons. Nothing crashed. But once I opened the Developer Debug
  Info panel (which shows the secret number) and started playing, the game's responses
  didn't match reality. The directional hints contradicted each other, and I couldn't
  win even when I guessed the exact secret number.

- List at least two concrete bugs you noticed at the start  

1. The Higher/Lower hints are backwards. With the secret showing as 10, guessing 8 (which is below 10) told me to "Go LOWER" instead of "Go HIGHER."
2. Two guesses that are both below the secret gave contradictory directions. Guessing 7 said "Go HIGHER" but guessing 8 said "Go LOWER," even though the secret stayed 10 the whole time.
3. I couldn't win on certain attempts even when I guessed the exact secret. Typing the correct number on an even-numbered attempt was not registered as a win and the game just continued.
4. The "New Game" button doesn't fully reset the game. After winning a round, clicking New Game left me stuck on the "You already won" screen instead of starting fresh.


**Bug Reproduction Log**

Document at least 3 bugs you found. Add rows as needed.

| Input | Expected Behavior | Actual Behavior | Console Output / Error |
|-------|-------------------|-----------------|------------------------|
| Guess 8 when secret is 10 | "📈 Go HIGHER!" (8 is below 10) | "📉 Go LOWER!" shown | none |
| Guess 7 then 8, secret stays 10 | Both guesses say "Go HIGHER" | 7 → Go HIGHER, 8 → Go LOWER (contradictory) | none |
| Guess exactly 10 on an even-numbered attempt | "🎉 Correct!" / win registered | Not counted as a win; game continues | none |
| Click "New Game" after winning a round | Game resets and is playable | Stuck on "You already won" screen | none |
 

---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?

I used Claude code and Claude chat. I tried using Copilot but preferred Claude's interface more.

- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).

It identified the reversed Higher/Lower hints in check_guess. That "too high" returns "Go HIGHER" when it should say "Go LOWER." How I verified: I'd already seen this live with the secret at 10, when guessing an 8 (below it) told me to "Go LOWER." The AI's diagnosis matched the game's actual behavior.

- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

The AI described winning on even-numbered attempts as "inconsistent" and only "incidental" caught by accident in an error handler. How I verified: I tested it directly. I started a new game, saw the secret was 50, and guessed 50 on my first (even) attempt. The game registered the win cleanly by displaying balloons, "You won," final score 70. So the win detection is not unreliable the way the AI implied; the except TypeError block catches an exact match every time. The thing that actually makes the game feel unwinnable is the backwards hints leading you away from the answer, not the win check.

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?

I didn't trust any single signal. For each fix I checked it two ways: an automated
pytest test for the pure logic, and a live run of the game in the browser to confirm
the behavior end to end. A bug only counted as "fixed" when both agreed. For example,
pytest confirmed check_guess(60, 50) returns "Too High" with a "Go LOWER" hint, and
then I played the game and saw that guessing below the secret now correctly says
"Go HIGHER." For the New Game fix, pytest couldn't cover it (it's UI state, not pure
logic), so I verified it manually: I won a round, clicked New Game, and confirmed the
game actually restarted instead of staying stuck on the "You already won" screen.

- Describe at least one test you ran (manual or using pytest) and what it showed you about your code.

I ran `python -m pytest -q` against tests/test_game_logic.py. The first run actually taught me something important: all three starter tests FAILED, but not because my code was wrong. The failures showed `assert ('Win', '🎉 Correct!') == 'Win'`, my check_guess function correctly returns a tuple of (outcome, message), but the starter tests were written to compare against only the string "Win". So the tests had the wrong expectation. I fixed the tests to unpack the tuple (`outcome, message = check_guess(...)`) and assert on the outcome, and added a check that the message points in the right direction ("LOWER" for a too-high guess). After that, all 3 passed!
This was a good reminder that a failing test doesn't automatically mean the code is
broken. Sometimes the test itself is wrong, and it's my job to decide which.

- Did AI help you design or understand any tests? How?

Yes. I used Claude Code to do the refactor (moving the four functions into
logic_utils.py and fixing the reversed hints), and I reviewed every diff before
approving it rather than accepting the edits blindly. I used Claude chat to help me
design the pytest cases — specifically to assert on both the outcome and the hint
direction, so the test actually proves the reversal bug is fixed rather than just
checking the label. AI also helped me diagnose two non-code issues: a
`ModuleNotFoundError: No module named 'logic_utils'` (a pytest path problem, solved by
running `python -m pytest` from the project root) and the failing-tests situation above,
where I had to verify in the code that the tuple return was correct before changing the
tests.

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.
