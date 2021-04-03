---
layout: page
title: Games
permalink: /games/
---

Games chosen for the 2021 competition are following:

- [Reconnaisance Blind Chess](#reconnaisance-blind-chess)
- [Gin Rummy](#gin-rummy)
- [_Secret game_](#secret-game)


## Reconnaisance Blind Chess

[Reconnaissance Blind Chess](https://rbc.jhuapl.edu/gameRules) (RBC) ([play
online](https://rbc.jhuapl.edu/challenge)) is like Chess except a player cannot
directly observe location of its opponent's pieces. The players can however use
a sensing action, a kind of "radar", that reveals all the pieces under a probed
square region of size 3x3. The game has been used in the [RBC
competition](https://rbc.jhuapl.edu/). It is a challenging game, as there is no
common knowledge about all piece positions (apart from the starting board). The
approximate total number of game states that can be considered in a game of RBC
is approximately 10^93 times that of a game of Chess, because each state
requires keeping track of not only what the game board actually looks like, but
the information that each player has acquired [1]. You can find [more detailed
analysis of the game](https://arxiv.org/abs/1811.03119).

While there exist strong Chess bots, such as
[Stockfish](https://github.com/official-stockfish/), and they have been adopted
to work with RBC by identification of the current chessboard, this
domain-specific adaptation will not work for the following card game.

## Gin Rummy

[Gin Rummy](https://en.wikipedia.org/wiki/Gin_rummy) ([play
online](https://cardgames.io/ginrummy/)) is a classic game, one of the most
widely-played two-player card games of the 20th century, and large sums are
wagered on it by expert players. Like other well studied games (Backgammon,
Chess, Go, Poker, etc...), this makes it a domain where human performance
represents an especially strong benchmark. It's noteworthy that the game is
played by some of the most successful hedge fund managers, including [Warren
Buffett and Howard
Marks](https://www.oaktreecapital.com/docs/default-source/memos/you-bet.pdf).

Gin Rummy is a large game with at least 10^80 information states and a large
number of states per info-state (there are over 1 billion possible opponent
hands off the deal versus 1,225 in heads up Texas hold 'em). HUNL has ~10^160
info-states, but can be abstracted to a much smaller game through bet bucketing
(action abstraction). This technique, along with several others used in Poker
research, is not applicable in a straightforward way to Gin.

It is also a difficult game for an algorithm to learn from scratch. Audrunas
trained [ARMAC](https://arxiv.org/abs/2008.12234) on Gin for a week and it
failed to learn a good strategy. ARMAC produced promising results in Poker and
Montezuma’s Revenge, so that offers some empirical evidence that Gin is
difficult and represents a distinct challenge. [2]

## _Secret game_

To drive algorithm development towards generality, we will use a third game,
which is secret. It is chosen by a committee member that does not participate in
the competition. The game will be announced after the final submissions are
posted. The game will use standard OpenSpiel API, just as the previous games.


## References

[1] [Mastering Reconnaissance Blind Chess with Reinforcement
Learning](https://smartech.gatech.edu/bitstream/handle/1853/63890/SAVELYEV-UNDERGRADUATERESEARCHOPTIONTHESIS-2020.pdf)

[2] Conversation with John Schultz.