---
layout: page
title: Docs
permalink: /docs/
---

You can find heavily commented code of a random bot and referee implementation
at the

<div style="text-align: center; font-size: 1.5em;margin:20px auto;"> 
<a href="https://github.com/deepmind/open_spiel/tree/master/open_spiel/higc">Competition
directory within the OpenSpiel framework.</a> </div>

The tournament code is available as of version 1.0.1 of OpenSpiel.

If you encounter any problems, [let us know and open an
issue](https://github.com/deepmind/open_spiel/issues). You can tag
`@michalsustr` for fast response. We're happy to help and try to make everything
as smooth as possible. You can also write to us at the <a
href="https://discord.gg/6Q3UurHxMh">Discord server</a>.

# Submission

For submission, please use the [template Singularity definition
file](/assets/higc/container.def) from which you can base your bot submission. In
most cases, this should be just copying your bot files and making sure that all
the software dependencies are installed correctly. The template contains
instructions on how to set it up.

Note that the secret game will be published in an obfuscated way via compiled
binary dynamic libraries. We publish [references
libraries](/assets/higc/higc_test_dyn_libs.zip) for testing. They do not contain
the secret game, but a special testing game `higc_test` (copy of
`cliff_walking`) that is not in the standard game suite.

Make sure that your bot works correctly using these two
reference files: the secret game will be provided through an updated version.
You can test it by running the tournament on `higc_test`

Note that after the submission deadline you cannot change your code, you can
only provide new supplementary data for the secret game.

Finally, before the [submission deadline](/schedule), please send us a download
link for the singularity container and supplementary data for Gin Rummy and RBC
via e-mail to `organizers (you know what) higcompetition.info`. Ater the second
deadline, you can similarly send us a link for secret game supplementary data.
