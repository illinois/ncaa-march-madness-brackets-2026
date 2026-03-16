# NCAA March Madness Brackets Generated for the 2026 Tournament

This repository contains the binary files for the March Madness brackets generated for the 2026 tournament and displayed on https://waf.cs.illinois.edu/visualizations/Exploring-The-Best-NCAA-March-Madness-Brackets/

Each binary file contains 1,000,000 unique brackets.  Each bracket is a 64-bit string, where:
- The first 32 bits represent the outcome of "Round 1" games
- The next 16 bits represent the outcome of "Round 2" games
- The next 8 bits represent the outcome of the "Sweet 16" games
- The next 4 bits represent the outcome of the four "Elite 8" games
- The next 2 bits represent the outcome of the two "Final Four" games
- The next bit represents the outcome of the championship game
- The final bit is always `0`

A bit of `0` represents the visually "upper" team on the bracket winning the match and a `1` represents the other team on the bracket winning the match.

Within each round, the first quarter of bits represents the "East" region; the next quarter represents the "South" region; the next quarter represents the "West" region; and the final quarter represents the "Midwest" region.

Within each region, the first bit represents the top of the region, and each subsequent bit moves toward the bottom of the region as it appears visually on the official NCAA bracket.

On the "Exploring The Best NCAA March Madness Brackets" website, we provide a numeric identifier for each bracket starting with `0`. Each bracket is numbered sequentially and the files are sequential (ex: `brackets.0000.bin` contains brackets #0..#999,999; `brackets.0001.bin` contains brackets #1,000,000..#1,999,999; etc.)

## Sample Bracket

The following shows the first 64 bits of `brackets.0000.bin` (comments/spacing added for clarity):

```
01001001 01011010 01100110 01000000   # First round (E, S, W, then MW)
00000011 01010011                     # Second round
01000001                              # Sweet 16  (With 16 teams, 8 total matches)
0000                                  # Elite 8
10                                    # Final Four (E, S, W, then MW)
1                                     # Championship Game
0                                     # Unused and always `0` (for byte alignment)
```

In the example below, we consider the above bitstring to be stored in `bracket`.


### First Round Games

The bits `bracket[0..7]` represent the first round in the East region:
- `bracket[0] == 0` represents the outcome of the top-most bracket game in the East region, #1 Duke vs. #16 Siena. The `0` bit represents that the bracket has Duke winning and advancing in the tournament.
- `bracket[1] == 1` represents the outcome of the next East region game, #8 Ohio State vs. #9 TCU. The `1` bit represents that the bracket has TCU winning (the team on the "bottom" of the bracket match).
- `bracket[2] == 0` represents #5 St. John's vs. #12 Northern Iowa, with St. John's advancing.
- `bracket[3] == 0` represents #4 Kansas vs. #13 Cal Baptist, with Kansas advancing.
- ...and so on...
- `bracket[7] == 1` represents #2 UConn vs. #15 Furman, with Furman advancing (this bracket predicts a big upset!).

The bits `bracket[8..31]` represent the first rounds in the South, West, and Midwest regions (in that order).


### Second Round Games

The bits `bracket[32..35]` represent the second round games in the East region, and are based on the selection of teams advancing from the first round.

- `bracket[32] == 0` represents the top of the bracket game in the second round. Based on `[0]` and `[1]`, this bracket represents the game of #1 Duke vs. #9 TCU. The `0` bit represents Duke advancing.
- `bracket[33] == 0` represents the next game in the second round. Based on `[2]` and `[3]`, this bracket represents the game of #5 St. John's vs. #4 Kansas. The `0` bit represents St. John's advancing.
- ...and so on...

The bits `bracket[36..47]` represent the second-round games for the South, West, and Midwest regions.


### Third Round Games ("Sweet 16")

The bits `bracket[48..55]` represent the eight "Sweet 16" games played.

- `bracket[48] == 0` represents the top of the bracket game of the "Sweet 16" games.  Based on `[32]` and `[33]`, this bracket represents the game of #1 Duke vs. #5 St. John.  The `0` bit represents Duke advancing.
- ...and so on...


### Fourth Round Games ("Elite 8")

The bits `bracket[56..59]` represent the final games in each region.

- `bracket[56] == 0` represents the final East region game. Based on `[48]` and `[49]`, this bracket represents the game of #1 Duke vs. #7 UCLA. The `0` bit represents Duke advancing.
- ...and so on...


### Fifth Round Games ("Final Four")

The four teams playing in the "Final Four" come from the winners of the four "Elite 8" games in the four regions. The two games are `bracket[60]` (East vs. South) and `bracket[61]` (West vs. Midwest).

- `bracket[60] == 1` represents the East vs. South game. Based on `[56]` and `[57]`, this bracket represents the game of #1 Duke vs. #1 Florida. The `1` bit represents Florida advancing.
- `bracket[61] == 0` represents the West vs. Midwest game. Based on `[58]` and `[59]`, this bracket represents the game of #1 Arizona vs. #1 Michigan. The `0` bit represents Arizona advancing.

### Championship Game

The `bracket[62]` bit represents the championship game. From all the previous matches, we find that this bracket predicts it to be #1 Florida vs. #1 Arizona. With `bracket[62] == 1`, this bracket predicts #1 Arizona winning the entire tournament.
