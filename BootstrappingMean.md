# Bootstrapping to Generate a Confidence Interval

Let's say we want to understand some property of a population, like its mean. To do so, we've sampled members of this population

`[2.148, 3.141, 2.668, 3.647, 6.799, 5.728, 3.346, 3.318, 2.305, 2.051]`

and calculated their mean

`3.5151`

However, with such a small sample size, we should expect that the mean of our sample will differ from the true population mean. _The smaller the sample, the bigger the variation in estimates of the population mean._

If we know the distribution of values in the overall population, we can often use an analytical formula to calculate a confidence interval. But in cases where we aren't confident in the true population distribution, we need a different way to estimate a confidence interval.

Bootstrapping is a procedure that allows us to estimate a confidence interval on an estimate from a sample without making any assumptions about the distribution. Instead, bootstrapping relies on resampling observed values. Formally, this is called "sampling with replacement".

Unfortunately, RevBayes doesn't have a built-in function to do this. But here's one that we can define on our own.

```
function Real[] sampWithReplace(Real[] data){
    samps <- [data[runifInt(1,1,data.size())[1]]]
    while (samps.size() < data.size()){
        samps.append(data[runifInt(1,1,data.size())[1]])
    }
    return samps
}
```

So, to generate a bootstrap confidence interval, we need to do this resampling many times and calculate a mean for each of these bootstrap replicates.

```
numReps <- 1000

for (i in 1:numReps){
    bootMeans[i] <- mean(sampWithReplace(data))
    if (i % 100 == 0){print(".")}
}
```

Now, let's sort these values

```
sortedMeans <- sort(bootMeans)
```

Once we've sorted the values, we can find the values that correspond to the 2.5 percentile and the 97.5 percentile. The range between these values is an estimate of the 95% confidence interval (CI) on our sample's mean.

```
CI[1] <- sortedMeans[floor(sortedMeans.size()*0.025)]
CI[2] <- sortedMeans[floor(sortedMeans.size()*0.975)]
```

In this case, the estimate of the 95% CI that I found was

`[ 2.709, 4.464 ]`
