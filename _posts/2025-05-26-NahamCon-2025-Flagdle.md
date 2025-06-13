---
title: "Nahamcon 2025 Flagdle"
date: 2025-05-24T15:34:30-04:00
categories:
  - Blog
tags:
  - CTF
  - Nahamcon2025
---
This challenge is just the wordle game using the flag format instead.However we have unlimited attempts.
We can use chatgpt to write a python program to send the request using python

```python

import requests

URL = "http://challenge.nahamcon.com:32123/guess"

def send_guess(guess):
    if not guess.startswith("flag{") or not guess.endswith("}") or len(guess) != 38:
        raise ValueError("Guess must be in format flag{[32 hex characters]}")

    response = requests.post(URL, json={"guess": guess})
    
    if response.ok:
        data = response.json()
        result = data.get("result", "")
        print(f"Guess: {guess}")
        print(f"Feedback: {result}")  # Properly decoded emojis
    else:
        print("Error:", response.status_code, response.text)

# Example usage
send_guess("flag{1234567890abcdef0000000000000000}")
```

This gives us the result.
Guess: flag{1234567890abcdef0000000000000000}
Feedback: 🟨🟨⬛🟩🟨🟨🟩🟨🟨⬛🟨🟨🟨🟨🟨⬛⬛⬛⬛🟩⬛🟩
The python program can have further logic added and automate the attempts

```python

import requests
import time
import random

URL = "http://challenge.nahamcon.com:32123/guess"
HEX_CHARS = "0123456789abcdef"

def send_guess(guess):
    response = requests.post(URL, json={"guess": guess})
    if response.ok:
        return response.json().get("result", "")
    else:
        print("Error:", response.status_code, response.text)
        return None

def count_matches(feedback):
    return sum(1 for c in feedback if c in ['🟩', '🟨'])

def phase1_find_char_frequencies():
    print("🔍 Starting Phase 1: Character Frequency Discovery")
    char_counts = {}

    for c in HEX_CHARS:
        guess = f"flag{{{c * 32}}}"
        feedback = send_guess(guess)
        if feedback:
            match_count = count_matches(feedback)
            char_counts[c] = match_count
            print(f"{c}: {match_count} matches -> {feedback}")
        time.sleep(0.5)  # avoid hammering server

    print("\n✅ Phase 1 Complete!")
    print("Discovered character counts:")
    for k, v in sorted(char_counts.items()):
        print(f"{k}: {v}")
    return char_counts

def solve_positions(char_freq):
    print("\n🚀 Starting Phase 2: Position Deduction")

    known_chars = []
    for c, count in char_freq.items():
        known_chars.extend([c] * count)

    if len(known_chars) != 32:
        raise ValueError("Total known characters ≠ 32! Phase 1 might be incomplete.")

    random.shuffle(known_chars)
    guess = known_chars.copy()
    confirmed = [''] * 32
    confirmed_mask = [False] * 32

    attempts = 0
    while not all(confirmed_mask):
        guess_str = "flag{" + ''.join(guess) + "}"
        feedback = send_guess(guess_str)
        if feedback is None:
            continue

        attempts += 1
        print(f"[{attempts}] {guess_str} -> {feedback}")

        for i in range(32):
            if feedback[i] == '🟩' and not confirmed_mask[i]:
                confirmed[i] = guess[i]
                confirmed_mask[i] = True

        # Shuffle only unconfirmed positions
        unconfirmed_indices = [i for i, confirmed_flag in enumerate(confirmed_mask) if not confirmed_flag]
        remaining_chars = [guess[i] for i in unconfirmed_indices]
        random.shuffle(remaining_chars)
        for idx, i in enumerate(unconfirmed_indices):
            guess[i] = remaining_chars[idx]

        time.sleep(0.5)

    flag = "flag{" + ''.join(confirmed) + "}"
    print("\n🎉 Flag Found!")
    print(f"✅ {flag}")
    return flag

if __name__ == "__main__":
    char_freq = phase1_find_char_frequencies()
    solve_positions(char_freq)

```

running this gives the following output

![alt text](/assets/images/image.png)
![alt text](/assets/images/image-1.png)

with the final result of

![alt text](/assets/images/image-2.png)
the flag being  **flag{bec42475a614b9c9ba80d0eb7ed258c5}**
