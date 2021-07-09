---
layout: post
title:  "All the games and the referee is implemented"
date:   2021-07-09 12:00:00 +0100
---

Both public games are implemented in OpenSpiel and you can run them with just
calling `load_game("gin_rummy")` or `load_game("rbc")`. 
We made an example implementation of the random bots in Python and C++, and 
provide an implementation of the referee for the tournament. 
See the [competition
repository](https://github.com/higcompetition/tournament/tree/higc/open_spiel/higc)
for more details.