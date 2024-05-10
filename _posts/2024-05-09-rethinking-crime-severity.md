---
layout: post
title: rethinking crime seveirty
date: May 06, 2024
---

2009 was a big year for folks at Statistics Canada. It marks the roll out of a metric called the Police-Reported Crime Severity Index (PRCSI) that has had a lasting impact on how we quantify crime. Before the PRCSI, crime was counted more simply as an unweighted frequency or proportion. With the PRCSI, different crimes get different weights indicative of their relative severity.

Where do the weights come from? As the Stats Canites (Canners? Cannians?) that came up with the PRCSI explain in a [2009 working paper](https://publications.gc.ca/site/eng/9.840898/publication.html):

> While the crime counts were fairly straightforward to calculate, the creation of the weights was less so. To begin with, there were questions as to what exactly should go into the calculation of the weights. Should only incarceration data be used? There was debate regarding whether other information should also be incorporated, such as conditional sentences, fines and probation. Two of the main goals of the index are for it to be objective and easily understandable. To include the other sentencing information, models would need to be built to equate levels of incarceration with fines, probation and conditional sentences. Because these other measures vary across jurisdictions and offence types, many models would be required which would result  in violating these two primary objectives. In consultation with the working group, it was determined that using only the incarceration data would provide the best objective measure of relative severity. In the final model, the weights are calculated by multiplying the incarceration rate for an offence by the average sentence length for the same offence for those people who were sent to prison. Essentially, the weights represent, on average, how long a person would be sentenced to prison given that they were found guilty (convicted) of an offence. It is worth noting that to calculate the weights, the most recent five years of courts sentencing data available were used. This provides stability in the weights, especially when dealing with relatively rare crimes, which can have few, if any, convictions in a given year. Details of how often the weights are updated are found in Section 2.6.

Let's summarize this using formal math. The formula for the PRCSI is:

$$
\[
\text{PRCSI} = \left( \frac{\sum_{i} q_{i,t} \cdot w_{i,t}}{\text{pop}_t} \right) \div \left( \frac{\sum_{i} q_{i,b} \cdot w_{i,b}}{\text{pop}_b} \right) \times 100
\]
$$

Where,
- \( q_{i,t} \) represents the number of offences of type \( i \) in the current time period \( t \).
- \( w_{i,t} \) represents the weight assigned to offence type \( i \) in time period \( t \).
- \( \text{pop}_t \) represents the population at time period \( t \).
- \( q_{i,b} \) and \( w_{i,b} \) represent the number of offences and the weights in the base year \( b \).
- \( \text{pop}_b \) is the population in the base year \( b \).

The *weights* in the formula are calculated by multiplying the incarceration rate by the average sentence length for each type of crime (\( w_{i} \)):

\[
w_{i} = \text{Incarceration Rate}_{i} \times \text{Average Sentence Length}_{i}
\]

For example, if the incarceration rate for homicide is 90% and the average sentence for homicide is 10 years (3650 days), the weight calculation would be
  \[
  0.90 \times 3650 = 3285
  \]
For fraud under $5000, if the incarceration rate is 10% and the average sentence is 1 year (365 days) -- these are completely made up data -- we'd calculate the weight as
  \[
  0.10 \times 365 = 36.5
  \]
  
## So what if we changed *w*?

Babyak and the other authors of the PRCSI faced a series of critical choices about what weights to assign to different crimes. They settled on using sentencing data, which has its merits, but imagine the alternatives! And how these might impact our thinking in turn.

For instance, what if we weighted crimes based on public perception? My guess is that crimes that are more feared or sensationalized—regardless of their actual frequency or maximum possible legal sentence—could be scored as more severe. Or what if we weighted crime counts by economic impact (super hard to get this data, *I know*, let's pretend)? Would this elevate in severity instead extremely damaging - but rarely prosecuted - crimes like market manipulation, collusion, or cybercrime? What effect would this have on how we measure and understand the year-to-year severity of crime?

**THE POINT IS** *how* we measure crime matters. It can profoundly influence how we perceive safety and the strategic direction of policing and policy. Metrics aren't neutral numerical representations; they are powerful tools that help construct our social reality. Methods, and their politics, are performative. They help produce the realities they describe.

