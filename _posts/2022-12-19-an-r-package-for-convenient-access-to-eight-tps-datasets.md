---
layout: post
title: eight new tps datasets
date: December 19, 2022
---

As part of its broader [Race and Identity-Based Data Collection (RBDC) Strategy](https://data.torontopolice.on.ca/pages/race-based-data), the Toronto Police has published eight open data sets that it plans to update periodically. To make accessing these eight data sets as convenient as possible, I put together a little R package that grabs the data directly from the TPS's client-side API, cleans up the column names, and imports it into R in tidy (tibble) format. You can install `library(tps.rbdc)` from my GitHub [here](https://github.com/alexlusco/tps.rbdc), where I've also provided some details on how to use it.