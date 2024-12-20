---
layout: post
title:  "Scoring Bowling Game in Python"
author: jay
tags: [  ] 
image: assets/images/headers/bowling_score.png
description: "Scoring Bowling Game in Python"
featured: false
hidden: false
comments: false
---

<p>A few years back, I remember one of my former coworkers used to ask candidates to write a program to score a bowling game. I remember that it usually took folks a little bit to get all the pieces.</p>

<p style="text-align: center;">
<img src="{{ site.baseurl }}/assets/images/2023_review/jenna_bowling.png" alt="" /><br/>
<small>Jenna bowling a strike in her first match.</small></p>

<p>My 15 year old daughter Jenna recently starting bowling and joined her school's JV bowling team. She's been having a lot of fun and I've been enjoying watching her bowl. So I got inspired and thought it would be fun to write a script to complete my former coworker's challenge:</p>

<h2>Rules for scoring a bowling match</h2>
<p><b>Strike</b><br>
<br>
If you knock down all 10 pins in the first shot of a frame, you get a strike.<br>
<u>How to score:</u> A strike earns 10 points plus the sum of your next two shots.</p>

<p><b>Spare</b><br>
<br>
If you knock down all 10 pins using both shots of a frame, you get a spare.<br>
<u>How to score:</u> A spare earns 10 points plus the sum of your next one shot.</p>

<p><b>Open Frame</b><br>
<br>
If you do not knock down all 10 pins using both shots of your frame (9 or fewer pins knocked down), you have an open frame.<br>
<u>How to score:</u> An open frame only earns the number of pins knocked down.</p>

<p><b>The 10th Frame</b><br>
<br>
The 10th frame is a bit different:<br>
If you roll a strike in the first shot of the 10th frame, you get 2 more shots.<br>
If you roll a spare in the first two shots of the 10th frame, you get 1 more shot.<br>
If you leave the 10th frame open after two shots, the game is over and you do not get an additional shot.<br>
<u>How to score:</u> The score for the 10th frame is the total number of pins knocked down in the 10th frame.</p>


<h2>The assignment</h2>

You have the following array of data which represents a bowling match. The array is composed of 10 "frames". Each frame has one or two elements corresponding to the number of pins knocked down in each shot. The array is structured as follows:

```python
[
  [1, 9], # First frame
  [7, 3], # Second frame
  [8, 2], # Third frame
  [9, 1], # Fourth frame
  [8, 2], # Fifth frame
  [9, 0], # Sixth frame
  [9, 1], # Seventh frame
  [6, 4], # Eighth frame
  [7, 2], # Ninth frame
  [6, 2]  # Tenth frame
]
```

<p>Write a function that calculates the score of a 10 frame bowling match given a two dimensional array representing the pins knocked down per frame.</p>

<h2>The script</h2>

```python
class BowlingGame:
    def __init__(self):
        self.rolls = []

    def roll(self, pins):
        self.rolls.append(pins)

    def score(self):
        total_score = 0
        roll_index = 0

        for _ in range(10):
            if self.rolls[roll_index] == 10:  # Strike
                total_score += 10 + self.rolls[roll_index + 1] + self.rolls[roll_index + 2]
                roll_index += 1
            elif self.rolls[roll_index] + self.rolls[roll_index + 1] == 10: 
             # Spare
                total_score += 10 + self.rolls[roll_index + 2]
                roll_index += 2
            else:
                total_score += self.rolls[roll_index] + self.rolls[roll_index + 1]
                roll_index += 2

        return total_score
```

<h2>Testing it works</h2>

<p>First I created a few easy tests where all the rolls are the same. Then I used the first picture above that came from a recent session when I took Jenna bowling at a nearby bowling place (<a href="https://bowlero.com/location/bowlero-fair-lawn" target="_blank">Bowlero</a>).</p>

```python
class TestBowlingGame(unittest.TestCase):
    def setUp(self):
        self.game = BowlingGame()

    def roll_many(self, rolls, pins):
        for _ in range(rolls):
            self.game.roll(pins)

    def test_gutter_game(self):
        self.roll_many(20, 0)
        self.assertEqual(self.game.score(), 0)

    def test_all_ones_game(self):
        self.roll_many(20, 1)
        self.assertEqual(self.game.score(), 20)

    def test_all_fives_game(self):
        self.roll_many(21, 5)
        self.assertEqual(self.game.score(), 150)

    def test_perfect_game(self):
        self.roll_many(12, 10)
        self.assertEqual(self.game.score(), 300)

    def test_test_jennas_game(self):
        frames = [
            [1, 9], # First frame
            [7, 3], # Second frame
            [8, 2], # Third frame
            [9, 1], # Fourth frame
            [8, 2], # Fifth frame
            [9, 0], # Sixth frame
            [9, 1], # Seventh frame
            [6, 4], # Eighth frame
            [7, 2], # Ninth frame
            [6, 2]  # Tenth frame
        ]
        for frame in frames:
            for shot in frame:
                self.game.roll(shot)
        self.assertEqual(self.game.score(), 150)

if __name__ == '__main__':
    unittest.main(argv=[''], verbosity=2, exit=False)

```