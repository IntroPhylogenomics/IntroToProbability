# Using Distributions in RevBayes

In general, probability distributions can be used in two ways, prediction and inference.

## Prediction

In the case of prediction, we know the parameter values that define our probability distribution, and we are interested in what kinds of values we can then expect to see. So, one of the first things we might want to do is draw random values. Thankfully, RevBayes has built-in functions to do this for many standard probability distributions. In this case, we'll demonstrate with a binomial distribution, but the syntax will be very similar for other distributions.

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

## Inference

Instead of having parameter values in hand and wishing to predict possible values that might be sampled from a distribution, we may instead want to learn something about the distribution based on observed data. In this case, instead of drawing values from a distribution with known parameters, we might like to know the probability of the values we've already observed as we change the possible parameters of the distribution.

For example, imagine that we've collected these data about the number of successes from a binomial with 10 trials.

`obs <- [9,7,8,7,7]`

First, we might like to know the probability of each of these values if the probability of success was 0.5. To do this, we can use the `dbinom()` function. This function will return the log probability (mass) associated with each value for a given probability of success.

For instance, `dbinom(9,0.5,10)` returns `-4.628887`. This is the natural log of the probability (0.009765625) of 9 successes in 10 trials if the probability of success is 0.5. To get the raw probability, we can use `exp()` to "unlog" the output of `dbinom()`. `exp(dbinom(9,0.5,10))` will return `0.009765625`. Therefore, 9 successes seems improbable if the probability of success is really 0.5.

If we want to see the total (joint) probability across all 5 observed values, we can do this

```
logProbs <- 0
for (o in obs){
  logProbs = logProbs + dbinom(o,0.5,10)
}
exp(logProbs)
```

The result should be `6.906475e-07`.

Since these observations seem unlikely if _p_=0.5, let's try a larger _p_ and compare the probabilities associated with our observations.

```
logProbs <- 0
for (o in obs){
  logProbs = logProbs + dbinom(o,0.7,10)
}
exp(logProbs)
```

The result in this case should be `0.0005369537`. This also seems like a small number, but remember two things. First, it's a LOT bigger than the probability associated with _p_=0.5. Second, this is a joint probability across 5 observations. Since each observation has a probability of less than 1, the more observations we add, the smaller the joint probability is guaranteed to be, even if the value of _p_ is the one used to simulate the data. In this case, I used _p_=0.7 to simulate the data.
