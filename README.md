# Wordle Tweet Analysis

In early 2022, people posted their daily Wordle result to Twitter: a puzzle number, a score, and a grid of coloured squares. This project parses **234,186** of those tweets from **164,000 players** into structured data and asks how people actually play.

![Average guesses by puzzle](figures/difficulty_by_puzzle.png)

## Key findings

| | |
|---|---|
| Average guesses to solve | **4.13** |
| Solved in 4 or 5 guesses | **58%** |
| Solved in 3 or fewer | **30%** |
| Hardest puzzle (#245) | 4.82 guesses on average |
| Easiest puzzle (#248) | 3.53 guesses on average |

- **Difficulty swings a lot day to day.** The hardest puzzle took 1.3 more guesses on average than the easiest.
- **The first guess matters.** Each extra green square on guess 1 goes with roughly 0.4 fewer total guesses, from 4.33 with no greens down to 2.51 with four.
- **Yellows peak early.** Players find about one right-but-misplaced letter on each of their first two guesses, then convert them to greens from guess 3 on.

![Average squares per guess](figures/squares_by_guess.png)

## The data

`tweets.csv.zip` has one row per tweet: puzzle id, tweet id, timestamp, username, and the raw tweet text. It covers puzzles #210 to #251 (Jan 16 to Feb 26, 2022).

**Important caveat:** there are **no failed games (X/6)** in the data. Either collection kept only solved games or people don't tweet their losses. Every number above describes solved games that people chose to share, so it makes Wordle look easier than it really is.

## Parsing the tweets

The grid has to be pulled out of free text, and real tweets don't follow the clean format:

| Problem | Example | How it's handled |
|---|---|---|
| Text typed on the same line as the last row | `🟩🟩🟩🟩🟩I did the thing!` | A row is the first 5 characters of a line, if they're all squares |
| A second game pasted after the result | Absurdle or another Wordle clone | Only the first block of consecutive grid rows is read |
| Display settings change the squares | ⬜ in light mode (34% of tweets), 🟧/🟦 in high-contrast | Normalised to 🟩 🟨 ⬛ |
| Invisible emoji variation selectors | `⬛️` (U+FE0F) | Stripped before parsing |

My first version of the parser missed the glued-on text and failed on about **14,000 tweets (6%)**. Fixing it brought the failures down to a couple of hundred.

## Data quality checks

Each tweet is checked for internal consistency before analysis:

| Check | Tweets |
|---|---|
| Grid rows don't match the claimed score | 113 |
| Last row isn't all green | 84 |
| All-green row appears before the end | 14 |
| Same user posting the same puzzle twice | 152 |

After removing these, **233,900 tweets (99.9%)** remain.

## Run it

```bash
pip install pandas numpy matplotlib jupyter
jupyter notebook Wordle_data.ipynb
```

The notebook runs top to bottom in under a minute and saves its charts to `figures/`.

## Next steps

- Add the answer word for each puzzle, so the analysis can look at which letters players find first and whether words with repeated letters are harder.
- Compare players who post regularly against one-time posters, to see whether people improve over the six weeks.

## Tools

Python · pandas · NumPy · regex · Matplotlib · Jupyter
