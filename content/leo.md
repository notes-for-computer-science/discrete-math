---
title: Leo Parsing
hidden: true
---

In this article, we'll explain Leo parsing. As I currently do not have an article written for Earley parsing, I will provide a very short description of it here! However, if you're already familiar with chart parsing and Earley parsing, you may proceed to the Leo parsing section.

### Chart Parsing Overview
Chart parsing refers to a family of parsing algorithms that use a dynamic programming approach to efficiently parse sentences based on a given grammar. A "chart" is a table where each cell represents spans (substrings) of the input, and parsing decisions are stored in this table to avoid redundant computations. Each chart entry contains partial parsing results, known as **arcs**.

Here's a very short example of *ad-hoc chart parsing*, where I fill in arcs arbitrarily. Consider the grammar (with start nonterminal $S$):

- $S \to E$
- $E \to \text{num}$
- $E \to E + E$

We will parse the input "3 + 4" (three tokens: $\text{num}, +, \text{num}$).

We first number the positions between the input tokens, and they will denote where each arc start and end:

```
Positions: 0   1   2   3
Tokens:      3   +   4
```

For a start, we know that the numbers 3 and 4 each constitute a $\text{num}$ token, which are both valid $E$ productions! We can add this information to the chart:

- $[E \to \text{num} \bullet, 0, 1]$ (In English: Found an $E$ between positions 0 and 1)
- $[E \to \text{num} \bullet, 2, 3]$ (In English: Found an $E$ between positions 2 and 3)

This is an example of a **passive arc**, which are arcs that store information about *fully parsed nonterminals*. If we want to store information about a *partially recognized rule*, we can instead place the $\bullet$ somewhere in the middle, or even at the beginning. For example, we can do this:

- $[E \to E \bullet + E, 0, 1]$ (In English: While trying to parse $E \to E + E$, I found $E$ between positions 0 and 1, and have yet to parse ${} + E$.)

Arcs that store this kind of "partial" information are called **active arcs**. Intuitively, they are active because they still have work to do -- this arc still needs a $+$ and an $E$!

Now, if we look at our input, we can see that between positions 1 and 2, there is a `+` in the input, matching what this rule needs. This means we can infer the following arc (which happens to also be active):

- $[E \to E + \bullet E, 0, 2]$ (In English: While trying to parse $E \to E + E$, I found $E + {}$ between positions 0 and 2, and have yet to parse $E$.)

Observe that we moved the position of the $\bullet$ and increased the span of the arc.

Now, we can do something interesting known as **the fundamental rule**. The fundamental rule states that an active arc can be combined with a passive arc, if an active arc is expecting nonterminal $A$ at position $j$, and the passive arc has completed the same nonterminal $A$ at position $j$. If we look back at our chart, we see two arcs that fit the bill:

- $[E \to \text{num} \bullet, 0, 1]$ (In English: Found an $E$ between positions 0 and 1)
- $[E \to \text{num} \bullet, 2, 3]$ (In English: **Found** an $E$ between **positions 2** and 3)
- $[E \to E \bullet + E, 0, 1]$ (In English: While trying to parse $E \to E + E$, I found $E$ between positions 0 and 1, and have yet to parse ${} + E$.)
- $[E \to E + \bullet E, 0, 2]$ (In English: While trying to parse $E \to E + E$, I found $E + {}$ between positions 0 **and 2**, and **have yet to parse $E$**.)

The second arc we found completed an $E$ starting from position 2, and the fourth arc is expecting an $E$ (because its $\bullet$ immediately precedes an $E$) at position 2 as well. Combining them, we get the arc:

- $[E \to E + E \bullet, 0, 3]$ (In English: Found an $E$ between positions 0 and 3)

Since $S \to E$, we can finally make the inference:

- $[S \to E \bullet, 0, 3]$ (In English: Found an $S$ between positions 0 and 3)

which completes our parse.

While this was a neat demonstration, it really was **not rigorous** at all. Earley parsing makes this reasoning procedure rigorous in 2 senses:

1) Earley parsing is **correct**: All arcs that Earley parsing adds to the chart do actually occur in the input. So if it adds the item $[A \to B C \bullet D, i, j]$ to the chart, there really is a $B C$ in the input, between $i$ and $j$.
2) Earley parsing is **complete** (for predictions): While Earley parsing does not add *every* possible arc, it does guarantee that if the start nonterminal $S$ can be recognized between positions 0 to $j$, then the arc $[S \to A \bullet, 0, j]$ will be added eventually.

Let's now move to the rigorous part of Earley parsing.

### Earley Parsing Overview
Earley parsing is a chart parsing algorithm that builds a parse by tracking partial parsing results, called **Earley items**. These items are stored in **sets**, one set per input position (indexed by $j$). Each Earley item describes a state in the parsing process for a particular grammar rule.

An Earley item is denoted as $[A \to \alpha \bullet \beta, i, j]$, where:
- $A \to \alpha \beta$ is a grammar rule,
- $\alpha$ is the part of the rule that has been recognized (before the dot),
- $\beta$ is what remains to be recognized (after the dot),
- $i$ is the start index where this item originated (i.e., set $S_i$),
- $j$ is the current position (i.e., this item is stored in set $S_j$).

The Earley algorithm consists of three main operations (or inference rules):

1. **Prediction**:
   - If $[A \to \alpha \bullet B\beta, i, j]$ is in set $S_j$ and $B$ is a non-terminal, then for each rule $B \to \gamma$ in the grammar, add $[B \to \bullet \gamma, j, j]$ to set $S_j$.

   **Inference rule**:
   $$
   \frac{[A \to \alpha \bullet B \beta, i, j] \quad B \to \gamma}{[B \to \bullet \gamma, j, j]}
   $$
   
   **Why this is correct**:
   It's always valid to say "I found a nothing between positions $j$ and $j$"! So it's always ok to predict any nonterminal at any position. The key idea here is that we only predict nonterminals when we know they will be needed -- in this case, we know $A \to \alpha \bullet B \beta$ "needs" a $B$ at position $j$, and it cannot make progress without it. So we add this "empty" arc to kickstart the process of parsing a $B$.
   
2. **Scanning**:
   - If $[A \to \alpha \bullet a \beta, i, j]$ is in set $S_j$ and the next symbol $a$ matches the input symbol at position $j$, add $[A \to \alpha a \bullet \beta, i, j+1]$ to set $S_{j+1}$.

   **Inference rule**:
   $$
   \frac{[A \to \alpha \bullet a \beta, i, j] \quad \text{input symbol at $j$ is } a}{[A \to \alpha a \bullet \beta, i, j+1]}
   $$
   
   **Why this is correct**: This is correct simply by how we defined these arcs. Since there is an $\alpha$ between $i$ and $j$, there's an $\alpha a$ between $i$ and $j+1$ so long as we know the input symbol after $j$ is indeed an $a$.
   
3. **Completion**:
   - If $[B \to \gamma \bullet, j, k]$ is in set $S_k$ and $[A \to \alpha \bullet B \beta, i, j]$ is in set $S_j$, add $[A \to \alpha B \bullet \beta, i, k]$ to set $S_k$.

   **Inference rule**:
   $$
   \frac{[B \to \gamma \bullet, j, k] \quad [A \to \alpha \bullet B \beta, i, j]}{[A \to \alpha B \bullet \beta, i, k]}
   $$
   
   **Why this is correct**: This is our fundamental rule from earlier.

4. One final note: We need to "kickstart" the parsing process by including an empty arc for our start nonterminal $S$ at position $0$.

   **Initialization**:
   - If $S \to \alpha$ is a rule in the grammar for our start nonterminal, add $[S \to \bullet \alpha, 0, 0]$ to set $S_0$.

   **Inference rule**:
   $$
   \frac{S \to \alpha}{[S \to \bullet \alpha, 0, 0]}
   $$

### Earley Parsing Example:
Consider the grammar:

- $S \to E$
- $E \to \text{num}$
- $E \to E + E$

We will parse the input "3 + 4" (three tokens: $\text{num}, +, \text{num}$) using Earley parsing.

#### Step-by-Step:

**Set $S_0$** (Start of parse):
- $[S \to \bullet E, 0, 0]$ (Initialization)
- $[E \to \bullet \text{num}, 0, 0]$ (Prediction caused by $[S \to \bullet E, 0, 0]$)
- $[E \to \bullet E + E, 0, 0]$ (Prediction caused by $[S \to \bullet E, 0, 0]$)
- Observe that the latest arc we added also predicts $E$! However, we would not get any new inferences, so we can stop here.

**Set $S_1$** (After scanning "3"):
- $[E \to \text{num} \bullet, 0, 1]$ (Scanning caused by $[E \to \bullet \text{num}, 0, 0]$)
- $[S \to E \bullet, 0, 1]$ (Completion caused by active arc $[S \to \bullet E, 0, 0]$ and passive arc $[E \to \text{num} \bullet, 0, 1]$)
- $[E \to E \bullet + E, 0, 1]$ (Completion caused by active arc $[E \to \bullet E + E, 0, 0]$ and passive arc $[E \to \text{num} \bullet, 0, 1]$)

**Set $S_2$** (After scanning "+"):
- $[E \to E + \bullet E, 0, 2]$ (Scanning caused by $[E \to E \bullet + E, 0, 1]$)
- $[E \to \bullet \text{num}, 2, 2]$ (Prediction caused by $[E \to E + \bullet E, 0, 2]$)
- $[E \to \bullet E + E, 2, 2]$ (Prediction caused by $[E \to E + \bullet E, 0, 2]$)

**Set $S_3$** (After scanning "4"):
- $[E \to \text{num} \bullet, 2, 3]$ (Scanning caused by $[E \to \bullet \text{num}, 2, 2]$)
- $[E \to E + E \bullet, 0, 3]$ (Completion caused by active arc $[E \to E + \bullet E, 0, 2]$ and passive arc $[E \to \text{num} \bullet, 2, 3]$)
- $[E \to E \bullet + E, 2, 3]$ (Completion caused by active arc $[E \to \bullet E + E, 0, 0]$ and passive arc $[E \to \text{num} \bullet, 2, 3]$)
- $[S \to E \bullet, 0, 3]$ (Completion caused by active arc $[S \to \bullet E, 0, 0]$ and passive arc $[E \to E + E \bullet, 0, 3]$)

### Right Recursion Example:
Unfortunately, one downside of Earley parsing is that it does not do well with right recursion, even for "simple" grammars which LR parsers can deal with easily!

Consider the grammar:

- $S \to A$
- $A \to a$
- $A \to a A$

As we'll see, the right recursive rule triggers *predictions* at every starting position. Then because our completion rule always applies the fundamental rule at every possible location, we will get more and more completions at every step.

We will parse the input "aaa" (three tokens: $a, a, a$) using Earley parsing.

#### Step-by-Step:

**Set $S_0$** (Start of parse):
- $[S \to \bullet A, 0, 0]$ (Initialization)
- $[A \to \bullet a, 0, 0]$ (Prediction caused by $[S \to \bullet A, 0, 0]$)
- $[A \to \bullet a A, 0, 0]$ (Prediction caused by $[S \to \bullet A, 0, 0]$)

**Set $S_1$** (After scanning "a"):
- $[A \to a \bullet, 0, 1]$ (Scanning caused by $[A \to \bullet a, 0, 0]$)
- $[A \to a \bullet A, 0, 1]$ (Scanning caused by $[A \to \bullet a A, 0, 0]$)
- Only 1 completion here
- $[S \to A \bullet, 0, 1]$ (Completion caused by active arc $[S \to \bullet A, 0, 0]$ and passive arc $[A \to a \bullet, 0, 1]$)
- Here comes our first additional set of predictions of $A$!
- $[A \to \bullet a, 1, 1]$ (Prediction caused by $[A \to a \bullet A, 0, 1]$)
- $[A \to \bullet a A, 1, 1]$ (Prediction caused by $[A \to a \bullet A, 0, 1]$)

**Set $S_2$** (After scanning "a"):
- $[A \to a \bullet, 1, 2]$ (Scanning caused by $[A \to \bullet a, 1, 1]$)
- $[A \to a \bullet A, 1, 2]$ (Scanning caused by $[A \to \bullet a A, 1, 1]$)
- Now 2 completions...
- $[A \to a A \bullet, 0, 2]$ (Completion caused by active arc $[A \to a \bullet A, 0, 1]$ and passive arc $[A \to a \bullet, 1, 2]$)
- $[S \to A \bullet, 0, 2]$ (Completion caused by active arc $[S \to \bullet A, 0, 0]$ and passive arc $[A \to a A \bullet, 0, 2]$)
- Another set of predictions of $A$
- $[A \to \bullet a, 2, 2]$ (Prediction caused by $[A \to a \bullet A, 1, 2]$)
- $[A \to \bullet a A, 2, 2]$ (Prediction caused by $[A \to a \bullet A, 1, 2]$)

**Set $S_3$** (After scanning "a"):
- $[A \to a \bullet, 2, 3]$ (Scanning caused by $[A \to \bullet a, 2, 2]$)
- $[A \to a \bullet A, 2, 3]$ (Scanning caused by $[A \to \bullet a A, 2, 2]$)
- Now 3 completions...
- $[A \to a A \bullet, 1, 3]$ (Completion caused by active arc $[A \to a \bullet A, 1, 2]$ and passive arc $[A \to a \bullet, 2, 3]$)
- $[A \to a A \bullet, 0, 3]$ (Completion caused by active arc $[A \to a \bullet A, 0, 1]$ and passive arc $[A \to a A \bullet, 1, 3]$)
- $[S \to A \bullet, 0, 3]$ (Completion caused by active arc $[S \to \bullet A, 0, 0]$ and passive arc $[A \to a A \bullet, 0, 3]$)
- Yet another set of predictions of $A$
- $[A \to \bullet a, 3, 3]$ (Prediction caused by $[A \to a \bullet A, 2, 3]$)
- $[A \to \bullet a A, 3, 3]$ (Prediction caused by $[A \to a \bullet A, 2, 3]$)

This demonstrates how the number of Earley items grows quadratically for right-recursive grammars. Leo parsing optimizes this scenario by eliminating redundant steps.

### Leo Parsing Overview
Leo parsing optimizes Earley parsing for right-recursive grammars by skipping unnecessary intermediate completions. It introduces **transitive items** (Leo items) to directly advance parsing when a completion can only lead to one result. These transitive items allow the parser to bypass redundant steps and reduce the time complexity for right recursion from $O(n^2)$ to $O(n)$.

Take a look at our previous example, specifically these completions in the latest step.

- $[A \to a A \bullet, 1, 3]$ (Completion caused by active arc $[A \to a \bullet A, 1, 2]$ and passive arc $[A \to a \bullet, 2, 3]$)
- $[A \to a A \bullet, 0, 3]$ (Completion caused by active arc $[A \to a \bullet A, 0, 1]$ and passive arc $[A \to a A \bullet, 1, 3]$)
- $[S \to A \bullet, 0, 3]$ (Completion caused by active arc $[S \to \bullet A, 0, 0]$ and passive arc $[A \to a A \bullet, 0, 3]$)

Now, let's be philosophical for a moment. *Why* do we care about performing completions? Well, obviously it's so that we can use the fundamental rule to advance existing active arcs closer to completion! The more we do this, the more completed arcs we have, and the more likely it is that we can eventually advance the arc for our start nonterminal.

Is that the case in our example? The answer is unfortunately yes. If we look closely, we'll see that the first completion uses our recently scanned arc $[A \to a \bullet, 2, 3]$ to produce a new passive arc, $[A \to a A \bullet, 1, 3]$. This new arc is immediately "useful" as it is used to produce another passive arc $[A \to a A \bullet, 0, 3]$. This in turn produces our final passive arc $[S \to A \bullet, 0, 3]$.

However, from another perspective, we really only care about completions that eventually advance the start nonterminal! So any other completions are a *means to an end*. If we can find a way to omit them, we should.

In fact, we can make a stronger claim that passive arcs are useless once they've been processed! Since Earley parsing only add arcs from left to right, all active arcs that end at position $j$ have already been added by the time we finish scanning the $j$th input symbol. Then, because the fundamental rule only applies between an active arc on the left and a passive arc on the right, when a passive arc is done adding all completions, we know there will be no more active arcs that can be combined with that passive arc.

Observe that while all 3 completions were useful, each completion was really only useful for producing exactly one other completion. So if we know that a completion of one active arc leads to exactly one other completion, we could store an item (a Leo item) which states that the completion of a particular nonterminal should instead directly complete some other completion.

In our case, we can make these claims:

- Completing $A$ at position $2$ would lead to a new passive arc $[A \to a A \bullet, 1, j]$, which results in the creation of exactly one new arc $[A \to a A \bullet, 0, j]$
- Similarly, completing $A$ at position $1$ would lead to a new passive arc $[A \to a A \bullet, 0, j]$, which results in the creation of exactly one new arc $[S \to A \bullet, 0, j]$

In fact, these two claims compose! So we can claim:

- Completing $A$ at position $2$ would lead to a new passive arc $[A \to a A \bullet, 1, j]$, which results in the creation of exactly one new arc $[A \to a A \bullet, 0, j]$, which results in the creation of exactly one new arc $[S \to A \bullet, 0, j]$
- Here's the timeline:
  1) $A$ is completed from positions $2$ to $j$. Look for active arcs to combine with.
  2) Find one active arc, use the fundamental rule to create $[A \to a A \bullet, 1, j]$
  3) The $A$ from step 1 becomes useless since there are no more active arcs at position $2$. NOW USELESS: $A$ from position $2$ to $j$
  4) Look for active arcs to combine with the newly created passive arc from step 2
  5) Find one active arc, use the fundamental rule to create $[A \to a A \bullet, 0, j]$
  6) The passive arc from step 2 becomes useless as there are no more active arcs. NOW USELESS: $[A \to a A \bullet, 1, j]$
  7) Look for active arcs to combine with the newly created passive arc from step 5
  8) Find one active arc, use the fundamental rule to create $[S \to A \bullet, 0, j]$
  9) The arc from step 5 becomes useless as there are no more active arcs. NOW USELESS: $[A \to a A \bullet, 0, j]$
- In short, completing $A$ at position $2$ would lead to **the creation of some intermediate arcs that immediately become useless**, eventually resulting in the creation of exactly one new arc $[S \to A \bullet, 0, j]$

We indicate that information with the following Leo item, which we store in set 2:

- $\{S \to A \bullet, A, 0, 2\}$ (In English: The completion of $A$ between positions $2$ and $j$ would result in the creation of useless arcs plus $[S \to A \bullet, 0, j]$)

More generally, for nonterminals $A$ and $B$ with production rule $A \to \alpha$,

- $\{A \to \alpha \bullet \beta, B, i, j\}$ (In English: The completion of $A$ between positions $j$ and $k$ would result in the creation of useless arcs plus $[A \to \alpha \bullet \beta, i, k]$)

We now formalize the rules for Leo parsing.

**Inference Rules for Leo Parsing**

1. **Leo Item Creation**:
   If $[A \to \alpha \bullet B \beta, i, j]$ is the only Earley item in set $S_j$ predicting $B$, add the Leo item $\{A \to \alpha B \bullet \beta, B, i, j\}$ to set $S_j$.

   **Inference rule**:
   $$
   \frac{[A \to \alpha \bullet B \beta, i, j] \text{ is the only item predicting } B}{\{A \to \alpha B \bullet \beta, B, i, j\}}
   $$

   **Why this is correct**: If only one item predicts a particular nonterminal $B$, then the completion of $B$ from $j$ to $k$ would result in no useless arcs (thus we vacuously have that all created arcs are useless), eventually resulting in the creation of exactly one new arc (because it's the only active arc expecting $B$!), $[A \to \alpha B \bullet \beta, i, k]$, by the fundamental rule.

2. **Skipping Useless Completions**:
   If $\{A \to \alpha \bullet B \beta, B, i, j\}$ is a Leo item in set $S_j$ and $[B \to \gamma \bullet, j, k]$ is an Earley item in set $S_k$, immediately add $[A \to \alpha B \bullet \beta, i, k]$ to set $S_k$, skipping intermediate completions.

   **Inference rule**: (Perform the completion for the Leo item)
   $$
   \frac{\{A \to \alpha \bullet B \beta, B, i, j\} \quad [B \to \gamma \bullet, j, k]}{[A \to \alpha B \bullet \beta, i, k]}
   $$

   **Modified inference rule for Completion**: (Do NOT perform any completions for Earley items in this set, if a Leo item exists)
   $$
   \frac{[B \to \gamma \bullet, j, k] \quad [A \to \alpha \bullet B \beta, i, j] \quad \neg \{\dots, B, i, j\}}{[A \to \alpha B \bullet \beta, i, k]}
   $$

   **Why this is correct**: By how we defined a Leo item, it indicates that only the Leo item's completion is worth doing, and all other completions would result in exactly one completion before becoming useless forever.

3. **Transitivite Closure of Leo Items**:
   If $\{A \to \alpha \bullet B \beta, B, i, j\}$ is a Leo item in set $S_j$ and $\{B \to \gamma \bullet, C, j, k\}$ is another Leo item, then we add the Leo item $\{A \to \alpha B \bullet \beta, C, i, k\}$ for nonterminal $C$ to set $k$, **replacing** the existing Leo item $\{B \to \gamma \bullet, C, j, k\}$ for the same nonterminal $C$ at set $k$.

   **Inference rule**:
   $$
   \frac{\{A \to \alpha \bullet B \beta, B, i, j\} \quad \{B \to \gamma \bullet, C, j, k\}}{\{A \to \alpha B \bullet \beta, C, i, k\}}
   $$

   **Why this is correct**: The first Leo item says that completing $B$ starting at position $j$ should only result in the completion of $A \to \alpha \bullet B \beta$, so $B$ is "otherwise useless". The second Leo item then says that completing $C$ at position $k$ results in the completion of $B$ at position $j$, and also that this completion of $B$ is the *only* one that results from $C$. So all completions caused by $C$ that eventually lead to $A$ are useless -- the only possibly non-useless one was $B$, but the first item tells us that $B$ is only ever used for $A$. So we deduce that a completing $C$ at position $k$ is only useful for completing $A$.

   **One note**: When we infer the new Leo item, it should *replace* the existing item $\{B \to \gamma \bullet, C, j, k\}$. By our reasoning above, we know that $B$ is a useless arc that will only result in the completion of $A$, so we do not need to keep the $B$ Leo item around.

### Leo Parsing Example:
Consider the grammar:

- $S \to A$
- $A \to a$
- $A \to a A$

We will parse the input "aaa" using Leo parsing.

#### Step-by-Step:

TODO
