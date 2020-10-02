# Maximum Likelihood Inference of the Mean

We are interested in learning about the trait values in a population (of salamanders!). We begin by making the assumption that the trait values in our population are normally distributed. We then sample some trait values from the population and try to infer the mean of the overall population based on the sample.

First, we need to define how big our sample size will be.

`sampleSize <- 10`

Next, we need to sample this many values from the population. In our case, we will know the true mean (and standard deviation) for our population, because we get to pick them! In RevBayes, functions that draw values from distributions all begin with __r__, because these are functions that draw random values. In our case, we want to draw values from a normal distribution, so we use `rnorm()`.

```
trueMean <- 10                                     # Define mean trait value in the population
trueStandDev <- 1                                  # Define the standard deviation of trait values in the population
data <- rnorm(sampleSize,trueMean,trueStandDev)    # Draw our sample from the population
data                                               # Print our sample to the screen for inspection
```

Now that we have some data, we need to be able to calculate the likelihood, P(_D_|_H_). In this case, our hypothesis (_H_) is a Normal distribution with a given mean and standard deviation. _D_ represents the data, the values sampled from our "true" Normal distribution. To do this, we'll create our own function.

```
# This function loops over values in our dataset and returns the log-likelihood of the data.
# clear(calcLike) # Use this to remove previous function definition if defining again.
function calcLike (Real[] dat, Real mean, Real stdev){
    logLike <- 0.0
    for (d in dat){
        logLike += dnorm(d,mean,stdev)
    }
    return logLike
}
```

We also need to give these variables starting values, to define a starting place for our exploration of parameter space. In this case, we'll draw a random value for the mean, but assume we already know the standard deviation.

```
m <- rUniform(1,0,50)  # Draw 1 value from a Uniform(0,50) distribution
sd <- 1
```

Let's print this value, to see where we'll be starting.

```
m[1]  # Even though m has just one value, it's stored in a vector so we need to extract it
```

Now let's calculate the likelihood for the starting value of our mean.

```
likes <- [calcLike(data,m[1],1)]
likes[1]
```

We can explore parameter space using a hill climbing algorithm to find the maximum likelihood estimate (MLE) of the mean.

```
# A hill-climbing algorithm to find the maximum likelihood estimate of the mean

function findML(Real[] dat, Real mean, Real stepSize){
    like = calcLike(dat,mean,1)
    while (calcLike(dat,mean+stepSize,1) > like){
        mean = mean + stepSize
        like = calcLike(dat,mean,1)
    }
    while (calcLike(dat,mean-stepSize,1) > like){
        mean = mean - stepSize
        like = calcLike(dat,mean,1)
    }
    if(stepSize > 0.0001){
        mean = findML(dat,mean,stepSize/2.0)
    }
    return mean
}
```

Now that we've defined our hill-climbing algorithm, let's estimate our mean.

`findML(data,m[1],1)`

How close is this to our true mean?
