# Drawing Values From Distributions in RevBayes

To draw a random number from a particular binomial distribution, we use this syntax like this

`rbinom(1,0.5,10)`

In my case, this call returned `4` but remember that the outcome is stochastic so you are likely to see a different value.

The first argument (`1`) tells RevBayes how many independent draws we want from this distribution, the second argument (`0.5`) specifies the probability of success, and the third argument (`10`) gives the number of trials. Remember that a _single_ draw from a binomial involves multiple trials (10, in this case).

In RevBayes, you can (sometimes) get help for a function by typing

`? rbinom`

The output from `rbinom(1,0.5,10)` should look something like this

`[4]`

The brackets indicate that this is a vector, although it only contains a single value. Nonetheless, if we want to do math with that value, we have to extract it from the vector

```
binomVals <- rbinom(1,0.5,10)
binomVals[1]
```
