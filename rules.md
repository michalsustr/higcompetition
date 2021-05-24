---
layout: page
title: Rules
permalink: /rules/
---

A high-level overview:

- Simultaneous evaluation of 3 games.
- Statistically significant evaluations.
- Open-source implementations in C++, bindings to other languages (Python) in [OpenSpiel](https://github.com/deepmind/open_spiel).
- Submit final bots for evaluation by the end of the year.

## Rules

- There are [3 games](/games) that are used for evaluation: Gin Rummy, Reconnaissance
  Blind Chess and a Secret Game.
    <div style="text-align:center; margin: 20px">
        <img src="/assets/gin_rummy.png">
        <img src="/assets/rbc.png">
        <img src="/assets/secret.png">
    </div>
- The competition uses a [round-robin tournament](https://en.wikipedia.org/wiki/Round-robin_tournament) (everyone plays against everyone) of 5000 matches for each pair of players, for each of the three games. The players will play the games from each position (i.e. as player 1 and player 2, for 2500 matches in each position).
- Players communicate with a referee via standard input/output. They do not communicate directly with the opponent.
- The games in the competition have imperfect-information, so the players receive only observations of the current game state from the referee. The referee gives players observations both when it’s their turn to act, but also when it's the opponent's turn (or chance’s). They can use these additional observations to ponder (i.e. think while waiting for the opponent).
    - When it's the player's turn to act, it also receives a list of legal actions it can play. The player submits a single legal action `open_spiel::Action` (a game-specific integer) to the referee. If the submitted action is illegal (not in the list), a random action is played instead. The player does not learn about its mistake, but the fact that the action was selected randomly is noted in the match log.
    - When it’s not the player’s turn to act, but the player submits an action anyway, nothing happens to the game state, but this fact is noted in the match log.
- Time limits:
    - There is a strict limit of **5 seconds per move** when it’s the player’s turn. There is no time limit to how long the whole match can take, the limit is only per move.
    - The pondering time can be however arbitrary, as the opponent may submit its actions immediately, and the bot implementation should be able to handle that. Chance transitions have a fixed duration of **0.2 seconds**, so for example the initial dealing of 20 cards in Gin Rummy takes 4 seconds.
- Going over time limit / submitting illegal actions: If the bot submits 3 illegal actions, or 3 actions during pondering-only phase, or goes over the time limit within a match, it is shut down and random actions are picked instead. This fact is noted in the match log.
- If the bot goes over the time limit in more than 1% of the matches in any of the games it is **disqualified**.
- One team, one program. The same program can only register once, no hyper-parameter sweeps are allowed.
- The same program should run for each game, but can use different supplementary data (precomputed lookup tables, neural networks etc.).
- The supplementary data is mounted from external file system storage for read-write access.
- The bot is completely restarted in each match by the evaluation system.
- The matches against a specific opponent run sequentially.
- No activity other than game computation is allowed.
- There is no connection to the internet, and the only file storage is through the space-limited supplementary data.

## The secret game

To drive algorithm development towards generality, we use a third game, which is
secret. It is chosen by a committee member that does not participate in the
competition. The game will be published, but it will be obfuscated to make it
difficult for participants to exploit domain-specific algorithm adaptations. The
game will however use the standard [OpenSpiel API](https://github.com/deepmind/open_spiel/blob/master/open_spiel/spiel.h), just as the other games.

The game will be announced after the final submissions (program + 2
game-specific supplementary datums) are received for the two public games. The
participants will then have 7 days to prepare the supplementary data for the
third game (for example to train a neural network). The program must not change
for the secret game! We will use the program ([singularity image](https://sylabs.io/guides/3.7/user-guide/)) that is
submitted before the publishing of the secret game.

## Qualification for the competition

Before the bots are allowed to compete they must first qualify by beating the
random player within 100 matches (50 matches as player 1 and 50 matches as
player 2) in both public games. The average score must be greater than zero.

## Technical details

- Bots will run on a single dedicated CPU core with 16GB RAM available. Each bot gets the same hardware specification to ensure computational fairness.
- 128 GB of space is allowed per each game for the supplementary data.
- Games and bots are implemented in [OpenSpiel](https://github.com/deepmind/open_spiel).
- The observations supplied by the referee are standard [OpenSpiel](https://github.com/deepmind/open_spiel) tensor observations, i.e. what you would get for the current state and game with the Observer API:

    Python
    ```python
    observation = make_observation(game)
    observation.set_from(state, player)
    observation.tensor
    ```

    C++
    ```cpp
    Observation obs(*game, game->MakeObserver(kDefaultObsType, {}));
    obs.SetFrom(state, player);
    obs.Tensor();
    ```

- Participants submit a [Singularity image](https://sylabs.io/guides/3.7/user-guide/). We provide a base image with dynamically mounted [OpenSpiel](https://github.com/deepmind/open_spiel) library that participants must use as base image, but feel free to add any packages that your algorithm requires on top of the base image. Maximum size of the entire image is 5GB and it should not contain game-specific data -- you can use supplementary data for that.  The [OpenSpiel](https://github.com/deepmind/open_spiel) dynamic library is mounted from an external file, in order to easily swap the implementation with the secret game, without changing the base image.
- Communication with the referee is done via STDIO (standard input / output).
    - First, the bot receives a string with the name of the game that it should play.
    - Then on the next line it receives the index of the player it should play as (either 0 or 1).
    - The bot then has 5 seconds to prepare for the match.
    - Then the bot should enter a while loop, waiting for a message from the referee. The message consists of
      - The current observation, a one-line tensor compatible with the Observer API, encoded in base64.
      - If it is bot’s turn, a list of legal actions (space-separated integers, i.e. list of `open_spiel::Action`).
    - Once the referee prints the observation and it is the bot’s turn, the bot has 5 seconds to make a move by printing the action number. If it is not its turn, it can use the observation to ponder.
    - The distinction between acting / pondering phase can be determined whether legal actions are supplied or not (see code below).
    - At the end the bot receives a string “end of game” followed by the final score the bot received. The bot should quit the while loop and finish its process. If not, the process is shut down after a timeout. This does not count towards the 1% no-timeouts-policy, but we prefer if your program behaves “nicely”.

## Evaluation
The bots will receive an ordered ranking for each game, based on the instant run-off voting (IRV). The smaller the rank, the better. The final evaluation is then lexicographic ordering of sorted ranks across the three games (i.e. comparison of the worst result across the games).

All the results from the competition will be published -- the summary statistics as well as the match history trajectories.

### More detailed explanation with an example.

In IRV, each bot casts a preference voting “ballot” that is generated based on the ordered pairwise outcomes, placing the hardest opponents first. The choice with the least first-place votes is then eliminated from the election, and any votes for that candidate are redistributed to the voters’ next choice. This continues until a choice has a majority (over 50%).

To determine the second and next places, the process is restarted with the selected choices removed.

Example with 2 games G1 and G2, and 3 bots A,B,C:

```
Game 1 pairwise average outcomes (note the  matrix is skew-symmetric):

G1   A    B    C
A    x    2    3
B   -2    x   -1
C   -3    1    x

Rank 1 iteration:

G1 ballots  Bot A   Bot B   Bot C
1st place       B       A       A   --> Majority winner is A
2nd place       C       C       B

Rank 2 iteration (A is removed):

G1 ballots  Bot A    Bot B   Bot C
1st place       B        C       B   --> Majority winner is B
2nd place       C

Remaining player is C, therefore ranking for Game 1 is:
  1. A
  2. B
  3. C

Game 2 pairwise average outcomes:

G2   A    B    C
A    x   -1    2
B    1    x    5
C   -2   -5    x

Rank 1 iteration:

G2 ballots  Bot A   Bot B    Bot C
1st place       B       A        B   --> Majority winner is B
2nd place       C       C        A

Rank 2 iteration (B is removed):

G2 ballots  Bot A   Bot B    Bot C
1st place       C       A        A   --> Majority winner is A
2nd place               C

Ranking for Game 2 is:
  1. B
  2. A
  3. C

Lexicographic ordering of sorted ranks across games is:

A: 2 1
B: 2 1
C: 3 3
```

Finally, A and B share first place, and C has third place.

## Example code for random agent

```python
import pyspiel
import base64
import numpy as np
from open_spiel.python.observation import make_observation

game_name = input()
play_as = int(input())

# Now there is 5 secs warm-up time that could be used for loading relevant supplementary data.

game = pyspiel.load_game(game_name)
# Will be set later based on the referee message.
observation = make_observation(game)

while (True):
   message = input()
   if message.startswith("end of game"): break

   encoded_obs, *legal_actions = message.split(" ")
   decoded_obs = np.frombuffer(base64.b64decode(encoded_obs),
   np.float32)
   np.copyto(observation.tensor, decoded_obs)
   should_act = len(legal_actions) > 0

   if should_act:  # Time limit 5 secs.
       print(np.random.choice(legal_actions))
   else:
       # Pondering phase. This bot does not ponder.
       pass

score = float(message.split(" ")[-1])
```

## Future updates

The organizers reserve the right to update the rules if deemed necessary. Such updates will be based on a committee vote. The participants will be notified of the change by e-mail as well on the website.
