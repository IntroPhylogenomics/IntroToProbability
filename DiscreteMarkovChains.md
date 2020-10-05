# Discrete Markov Chain Simulation

In this exercise, we are going to explore some properties of discrete-time Markov chains. To do this, we'll define a custom state space and transition matrix, which contains the pairwise probabilities of transitioning between states. By default, our state space consists of two types of days - `Rainy` and `Sunny`. In the Rev language, we encode our states in a one-dimensional vector that we are calling `stateSpace`. We encode our transition matrix as a two-dimensional vector - a vector of vectors - called `transitionMatrix`. We also create a vector to hold the states of our chain, `myChain`, and provide one initial state (in this case, `Rainy`).

```
# Clear our workspace to start
clear()

# Define the states for our chain
stateSpace = ["Rainy","Sunny"]

# Define our transition matrix
transitionMatrix = [[0.8,0.2],[0.2,0.8]]

# Initialize our chain
myChain = ["Rainy"]
```

Now that we've initialized our chain, we need a way to draw a new state for each generation. Since this is something we'll want to do a lot, we'll create a new function called `addState`. Any user can define their own functions in RevBayes using this syntax:

`function <returnType> <functionName> ( <ArgumentType> <ArgumentName>, ... ) { <CODE> }`.

```
function addState(String[] chain, String[] states, Probability[][] tMat){

    numStates = states.size()
    
    for (s in 1:numStates){
        if (chain[chain.size()] == states[s]){
            probs = tMat[s]
        }
    }
    
    ranNum = runif(1,0,1)[1]
    
    probSum = 0.0
    
    i = 1
    while(probSum < ranNum){
        probSum += probs[i]
        if (ranNum < probSum){
            return append(chain,states[i])
        }
        i += 1
    }     
}
```

Now that our chain is set up and we have a function to draw a new state for each generation, we can use a for loop to run the chain for as long as we want.

```
# Define the number of generations (iterations) in our chain
numGens = 30

# Run the chain for numGens generations
for (g in 1:numGens){
    myChain = addState(myChain,stateSpace,transitionMatrix)
}

# Tally the number of times each state occurs in the chain
rainCount = 0
sunCount = 0
for (i in 1:myChain.size()){
    if (myChain[i] == "Rainy"){
        rainCount += 1
    } else {
        sunCount += 1
    }
}

# Calculate the frequencies of states visited by the chain
print(rainCount/myChain.size())
print(sunCount/myChain.size())
```

Note that each time you run the code in the cell above, it will add numGens generations to the chain. As you run the chain longer, where do the frequencies settle? What are the stationary probabilities?

Get a sense for the dynamics of the chain by examining its states.

```
# Show all states in the entire chain
print(myChain)
```

Now, try changing the probabilities in the transition matrix. First, keep the matrix symmetric (the diagonal elements should remain identical, as should the non-diagonal elements). What happens to the patterns you see in the chain?

Now, make the matrix non-symmetrical. What happens to the stationary frequencies? What's the relationship between the transition matrix and the stationary frequencies?
