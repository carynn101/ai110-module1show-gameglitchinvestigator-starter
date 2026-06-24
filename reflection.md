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
- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.
