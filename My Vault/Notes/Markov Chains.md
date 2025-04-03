
# Markov Chains


DATE:  03-04-25


Tags:  

# References:




# Content:

Markov Chains (or Markov Models) are mathematical systems used to model processes that transition from one **state** to another over time, based on certain **probabilistic rules**. The defining characteristic, known as the **Markov Property**, is that the probability of transitioning to the next state depends _only_ on the _current_ state and not on the sequence of states that preceded it.

Think of it like this: "Where you go next only depends on where you are now, not how you got here." This property is often called "memorylessness."

**Key Components of a Markov Chain:**

1. **States (State Space):** These are the distinct conditions or situations the system can be in at any given time. The set of all possible states is called the state space. States can be finite (e.g., {Sunny, Rainy, Cloudy}) or countably infinite (e.g., {0, 1, 2, 3,...}).
    
    - _Example:_ For a simple weather model, the states could be `Sunny`, `Cloudy`, `Rainy`.
2. **Transitions:** These are the movements between states. A transition occurs from a current state to the next state (which could be the same state).
    
3. **Transition Probabilities:** Each possible transition has an associated probability. The probability of moving from state `i` to state `j` in one time step is denoted as P<sub>ij</sub>.
    
    - _Condition:_ For any given state `i`, the sum of probabilities of transitioning from state `i` to _all_ possible next states (including `i` itself) must equal 1. (Σ<sub>j</sub> P<sub>ij</sub> = 1 for all i).
    - _Example:_
        - P(Rainy | Sunny) = 0.1 (Probability it's Rainy tomorrow, given it's Sunny today is 10%)
        - P(Sunny | Sunny) = 0.8 (Probability it stays Sunny)
        - P(Cloudy | Sunny) = 0.1 (Probability it gets Cloudy)
        - (Note: 0.1 + 0.8 + 0.1 = 1)
4. **Initial State Distribution:** This is a probability distribution describing the starting state of the system at time t=0. It tells us the probability of being in each state initially.
    
    - _Example:_ At the start (t=0), P(State=Sunny) = 0.7, P(State=Cloudy) = 0.2, P(State=Rainy) = 0.1.
5. **Time Steps (for Discrete-Time Markov Chains):** The process evolves over discrete time steps (e.g., seconds, days, rounds of a game).
    

**The Markov Property (Memorylessness)**

This is the cornerstone. Formally, it states: P(X<sub>n+1</sub> = j | X<sub>n</sub> = i, X<sub>n-1</sub> = k, ..., X<sub>0</sub> = l) = P(X<sub>n+1</sub> = j | X<sub>n</sub> = i)

In simpler terms: The probability of being in state `j` at the next time step (n+1), given the entire history of states up to the current time step `n` (where the current state is `i`), is exactly the same as the probability of being in state `j` at time `n+1` _only_ knowing that the current state at time `n` is `i`. The past history (X<sub>n-1</sub>, ..., X<sub>0</sub>) provides no additional information about the next step beyond what the current state tells us.

**Mathematical Representation: The Transition Matrix**

For a system with a finite number of states (say, N states), the transition probabilities can be neatly organized into an N x N matrix called the **Transition Matrix (P)**.

- The entry in the i-th row and j-th column (P<sub>ij</sub>) is the probability of transitioning _from_ state `i` _to_ state `j` in one step.
- Each row of the matrix must sum to 1.

_Example Weather Transition Matrix:_ Let States be: 1=Sunny, 2=Cloudy, 3=Rainy

```
      To: Sunny Cloudy Rainy
From: Sunny [ 0.8   0.1    0.1 ]
      Cloudy[ 0.4   0.4    0.2 ]
      Rainy [ 0.2   0.5    0.3 ]
```

This matrix tells us:

- If it's Sunny (Row 1), the probability of it being Sunny next is 0.8, Cloudy is 0.1, Rainy is 0.1.
- If it's Cloudy (Row 2), the probability of it being Sunny next is 0.4, Cloudy is 0.4, Rainy is 0.2.
- If it's Rainy (Row 3), the probability of it being Sunny next is 0.2, Cloudy is 0.5, Rainy is 0.3.

**Types of Markov Models:**

1. **Discrete-Time Markov Chains (DTMC):** Time progresses in discrete steps. These are the most common type introduced first. The weather example above is a DTMC.
2. **Continuous-Time Markov Chains (CTMC):** Transitions can happen at any point in continuous time. The time spent in a state before transitioning follows an exponential distribution. These are used for modeling things like queuing systems or chemical reactions.
3. **Hidden Markov Models (HMMs):** These are more complex. In an HMM, the underlying Markov process (the sequence of states) is not directly observable (it's "hidden"). Instead, we observe **outputs** or **emissions** that are probabilistically dependent on the hidden state.
    - _Example:_ In speech recognition, the hidden states might be phonemes (basic sound units), and the observations are the actual audio signals. We infer the most likely sequence of phonemes (hidden states) from the observed audio.

**Properties and Concepts:**

- **Reachability:** Can state `j` be reached from state `i`?
- **Communicating States:** States `i` and `j` communicate if `i` is reachable from `j` AND `j` is reachable from `i`.
- **Irreducibility:** A Markov chain is irreducible if all states communicate with each other (you can eventually get from any state to any other state).
- **Periodicity:** A state `i` has period `d` if any return to state `i` must occur in a multiple of `d` time steps. If `d=1`, the state is aperiodic. An irreducible chain where all states are aperiodic is called an **ergodic** chain.
- **Recurrence and Transience:**
    - A state is **recurrent** if, starting from that state, the probability of eventually returning to it is 1.
    - A state is **transient** if, starting from that state, there's a non-zero probability of never returning to it.
- **Stationary Distribution (π):** For many Markov chains (specifically, irreducible and aperiodic ones), there exists a unique probability distribution `π` over the states such that if the system starts in this distribution, it remains in this distribution for all future time steps. Mathematically, `πP = π`. This distribution represents the long-term proportion of time the system spends in each state.

**Applications of Markov Chains/Models:**

Their relative simplicity and power make them useful in many fields:

- **Finance:** Modeling stock price movements (though often simplified), credit ratings transitions.
- **Biology:** Modeling population dynamics, DNA sequence analysis (HMMs are common here).
- **Natural Language Processing (NLP):** Text generation (predicting the next word based on the current word), part-of-speech tagging (HMMs).
- **Speech Recognition:** HMMs are fundamental for modeling sequences of sounds.
- **Web Page Ranking:** Google's original PageRank algorithm was based on a Markov chain modeling a user randomly clicking links. The stationary distribution gave the "importance" of pages.
- **Queueing Theory:** Modeling customer arrivals and service times (CTMCs).
- **Genetics:** Modeling gene evolution.
- **Games:** Analyzing board games or simple chance-based games.
- **Meteorology:** Simple weather prediction models.

**Advantages:**

- Relatively simple concept to grasp.
- Mathematically tractable, especially with matrix algebra.
- Widely applicable to diverse problems.
- Foundation for more complex models like HMMs.

**Limitations:**

- **The Markov Property:** The biggest limitation. Real-world processes often _do_ have memory (e.g., weather patterns might depend on the last few days, not just yesterday). The memoryless assumption is often an approximation.
- **State Space Size:** The number of states can become very large, making analysis computationally expensive.
- **Static Probabilities:** Basic Markov chains assume transition probabilities don't change over time, which might not hold true in reality.



