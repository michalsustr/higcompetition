---
layout: post
title:  "Competition evaluation"
date:   2021-12-06 12:00:00 +0100
---

We received eight registrations for the competition, however no one decided to 
submit their final bot before deadline. 
Therefore we decided to close the competition for this year.
[We would like to ask you for 
feedback](https://docs.google.com/forms/d/e/1FAIpQLSdEOvI_yIbgjNz2DRGvOcfmk83EIQUQA5xaJCIh5mHUbgC2pg/viewform), 
so that we learn how to improve the competition or whether
we should run it the next year. 

As organizers, we prepared a (weak) baseline to play in the tournament, 
we show its results here. 
We trained a DQN agent against a random player based on the 
[OpenSpiel implementation of DQN in libtorch](https://github.com/deepmind/open_spiel/tree/master/open_spiel/algorithms/dqn_torch). 
Every 1000 iterations 1000 games have been played between the DQN agent playing 
either as Player 0 or 1, and the opponent random player to evaluate the performance: 

<div style="margin:20px">
<img src="/assets/dqn_against_random.png">
</div>

As the above plot shows, the DQN learned how to beat the random opponent quickly
in RBC. In Gin Rummy, it slightly improves over time, but still keeps losing to
random player after 10^6 iterations.
