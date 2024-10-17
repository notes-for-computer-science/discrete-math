---
title: Set Cardinality, Infinity, and The Halting Problem
draft: false
tags:
  - notes
hidden:
---
 
In our journey of formalising everything that we know, it's reinspect something that we've taken for granted. Set sizes. Thus far we've sort of been eyeballing it for finitely sized sets. E.g. $A = \{1, 2, 5\}$ so we say that $\lvert A \rvert = 3$. So how do we say $\lvert A \rvert < \lvert B \rvert$? We'd probably say $\lvert A \rvert = s_A, \lvert B \rvert = s_B$ with $s_A, s_B \in \mathbb{N}$. Then if $s_A < s_B$, then we know $\lvert A \rvert < \lvert B \rvert$.

But here, there are many things that actually have been left unformalised, and also unsolved. For example:
1. Okay let's say we accept that we can compare two natural numbers for free (which actually can be formalised).
2. How do we rigorously say that the size of a set $\lvert A \rvert = n$? You might say "sure I can just list them out and count." but that's not enough for mathematicians.
3. Even more worryingly, we've only talked about finite sets. What about infinite sets? You could count until the cows come home and never be done.

So let's talk about how to formalise these things and study not just counting the sizes of finite sets, but the sizes of infinite sets, and see how infinity behaves.

> [!Question]
> **Isn't this a little too abstract?** Why do I need to know this in computer science?
> 
> I'm glad you asked! So later on when we understand diagonalisation and the different infinities better, I'm going to show you how we can formally prove that there exists problems out there that computers cannot solve.
> 
> In fact, as newcomers (or not) to programming, you might wonder: Can my compiler/runtime environment ever look at my code and tell me it will loop forever, and then I can go fix it. The answer is **nope**. To be clear, it might be able to spot certain patterns and warn you, but there isn't a perfect algorithm that has 100% accuracy on this. We'll see this at the end of the notes.


## Let's begin!
Our starting point is picking up where we let off with functions. So recall:
A function $f : A \to B$ is:
1. Injective, if and only if $\forall a_1, a_2 \in A [f(a_1) = f(a_2) \to a_1 = a_2]$
2. Surjective, if and only if $\forall b \in B, \exists a \in A [f(a) = b]$
3. Bijective, if and only $f$ is both injective and surjective.

The idea is the following: We will say that $\lvert A \rvert \leq \lvert B \rvert$ if **there exists** a function $f : A \to B$ such that $f$ is injective. The intuition is the following: If the "size" of $A$ is less than or equals to the "size" of $B$, then we should be able to associate every item of $A$ with a unique item of $B$. This "association" is basically proven by giving $f$.

And yes, we will basically say $\lvert A \rvert = \lvert B \rvert$ if and only if $\lvert A \rvert \leq \lvert B \rvert$ and $\lvert B \rvert \leq \lvert A \rvert$. That means giving an injective function $f : A \to B$ and also an injective function $g : B \to A$.

**But wait. If there is a bijection $h : A \to B$, can't we also say $\lvert A \rvert = \lvert B \rvert$?** Yes. Yes you can.

**...So you're telling me, if I have $f : A \to B$, and $g : B \to A$ both injective functions, then there exists a bijection $h : A \to B$?** Yes! This is called the [Cantor-Schroder-Bernstein theorem](https://en.wikipedia.org/wiki/Schr%C3%B6der%E2%80%93Bernstein_theorem).

> [!Theorem]
> If there exists injective functions $f : A \to B$, $g : B \to A$ between sets $A, B$. Then there exists bijective function $h : A \to B$.

>[!Pop Quiz]+ 
> Notice that the theorem statements says if injective $f, g$ exists, then bijective $h$ exists. What about the other direction? If $h : A \to B$ is bijective, can we find injective functions $f : A \to B$ and $g : B \to A$?
> > [!Answer]-
> > 
> > Yes. Assume $g : A \to B$ bijective. Then $f = g$ is an injective function from $A \to B$. Also $f^{-1}$ is an injective function from $B \to A$. Check it!



## Using functions to talk about set cardinalities

### Finite Sets
So the first thing we should do is talk about finiteness. Now we can be formal. From this point on, we will use $\mathbb{Z}_n$ as a **shorthand (this is not standard notation)** to denote the set $\{1, 2, 3, \ldots, n\} = \{x : x \in \mathbb{Z} \land x \geq 1 \land x \leq n\}$.

Then, we will say a set $A$ is finite if:
1. $A$ is the empty set.
2. There exists an $n \in \mathbb{Z}$ and a bijection function $f : A \to \mathbb{Z}_n$.

Intuitively, a finite set either has 0 elements, or it has $n$ elements for some $n \in \mathbb{Z}$. The function $f$ here is basically just telling you how to show it has $n$ elements by literally saying: "This is element 1... this is element 2..." and so on.

****## Infinite Sets
Okay let's move onto infinite sets because here's where things get very interesting.
So cardinality gets a bit weirder when infinite sets are a thing. For example, $\lvert \mathbb{N}\rvert = \lvert \mathbb{Z} \rvert = \lvert \mathbb{Q} \rvert$.
In fact, let $O = \{2x + 1 : x \in \mathbb{N}\}$. We can also say $\lvert O \rvert = \lvert \mathbb{N} \rvert$. That seems weird, clearly there are more natural numbers than odd numbers. And perhaps you can say that. That's why we say: **the cardinalities are the same**. Cardinalities are our way of talking about "size" between sets.

> [!Note] 
> A few things to note:
> 1. Yes, for finite sets $A, B$, if $A$ is a proper subset of $B$, then ($\lvert A \rvert \leq \lvert B \rvert$ and $\lvert A \rvert \neq \lvert B \rvert$).
> 2. A set that has a bijection to $\mathbb{N}$ is called **countably infinite**.
> 3. No, for infinite sets, it is not necessarily the case that if $A$ is a proper subset of $B$, then $\lvert A \lvert \leq \lvert B \rvert$ and also $\lvert A \rvert \neq \lvert B \rvert$. Case in point: $\lvert \mathbb{N} \setminus \{0\} \rvert = \lvert \mathbb{N} \rvert$.

## Cardinal numbers
Okay let me elaborate a little more on **cardinal numbers**. We've talked about what finite, and countably finite means, and this is a good point to start reflecting on this and ironing out a few kinks.

For example, throughout the course we've been saying things like $\lvert A \times B \rvert = \lvert A \rvert \times \lvert B \rvert$. But as of right now, the $\lvert A \rvert$ we have defined, does not output a number. We have only said what $\lvert A \rvert \leq \lvert B \rvert$ means, but nothing else! So what does $\lvert A \rvert \times \lvert B \rvert$ mean?

So we now need a new set of objects to play with. These happen to be called **cardinal numbers**. You'll want to think of $\lvert A \rvert$ as something that takes the set $A$, and it gives you a cardinal number. If $A$ is finite, it will give you $n \in \mathbb{N}$ such that there exists a bijection from $A$ to $\{1, 2, \ldots, n\}$.

What about infinite sets? What is $\lvert \mathbb{N} \rvert$? Well that happens to give you $\aleph_0$. This is **first trans-finite cardinal number**. Some people will call this the "smallest infinite cardinal number". I.e. any smaller and you're back in finite land. Bear in mind, cardinal numbers are also sets. Yes including stuff like 0, 1, 2.

> [!An aside: Cardinals as sets]-
> If you want to stick to only fundamentals, it's possible to construct cardinals (and also numbers) out of just a few axioms (think of these as unproven but philosophically accepted truths). I will state them very informally here. If you want to know more, you can take MA3205 or learn set theory formally.
>    1. The empty set exists.
>    2. You can take two sets and union them.
>    3. You can take two sets and pair them.
>    4. There is an infinite set. (We don't know if it's countable or uncountable)
>    5. Two sets are the same if and only if they have the same elements.
>    6. We can specify sets directly. (this is a **vast** oversimplification but anyway..)
>    
>    Just take my word for it, you can use these to show that no set is an element of itself. Now, we can create the natural numbers. Define $c$ to be a function that takes a set $x$ and outputs $x \cup \{x\}$.  Then let's think about the following set:
>    
>    1. $\varnothing \in S$.
>    2. $s \in S \to c(s) \in S$
>     
>    Let's take a look at a few values. $\varnothing$ is in $S$. 
>    Also, $c(\varnothing) = \{\varnothing\}$ is in $S$. 
>    If we apply $c$ again, $\big\{ \varnothing, \{\varnothing\} \big\}$ is in $S$. 
>    If we apply $c$ yet again, $\Big\{ \big\{ \varnothing, \{\varnothing\} \big\}, \varnothing, \{\varnothing\} \Big\}$ is in $S$.
>    
>    Do you see the pattern? How many elements are there in $\varnothing$? 
>    How many elements are there in $c(\varnothing)$? 
>    How many elements are there in $c(c(\varnothing))$? 
>    How about $c(c(c(\varnothing)))$?
>    
>    If we treat $\varnothing$ as $0$. 
>    What should we treat $c(\varnothing)$ as? 
>    What about $c(c(\varnothing))$? 
>    What about $c(c(c(\varnothing)))$?
>    
>    Since $S$ contains all these sets, what should we treat $S$ as?



So let's revisit what I said about bijections. $A$ is countably infinite if and only if it has a bijection $f : A \to \mathbb{N}$. Since $\mathbb{N}$ has cardinality $\aleph_0$, this means there exists $g : \mathbb{N} \to \aleph_0$ which is also a bijection. **Oh look**, this means we have a bijection $h : A \to \aleph_0$ (if and only if, in fact).

So to be more precise, $A$ is countably infinite if $A$ has a bijection to $\aleph_0$.

To be clear, mathematicians have taken great care to also define things like "multiplication" and "addition" of cardinals so that we can actually formally prove things like $\lvert A \times B \rvert = \lvert A \rvert \times \lvert B \rvert$, and $\lvert A \cup B\rvert \leq \lvert A \rvert + \lvert B \rvert$. But we won't go into that here. I just need you to appreciate that even fundamental things like this can be proven. Isn't that crazy?

## Within Countable Infinity
There are a few useful facts (provable theorems, in fact) that you should know. Let's reiterate some that we had mentioned before:

1. $\lvert \mathbb{N} \rvert = \lvert \mathbb{Z} \rvert = \lvert \mathbb{Q} \rvert = \aleph_0$
2. If $A, B$ are countably infinite sets, then $\lvert A \times B \rvert = \lvert A \cup B \rvert = \aleph_0$.
3. If $A, B$ are countable sets. Then $A \times B$ is countable. Also, $A \cup B$ is countable.
4. If we have a **countably infinite set $\mathcal{C}$ of countably infinite sets**. Then:
	1. We have a bijective function $f : \mathbb{N} \to \mathcal{C}$. (Why?) 
	2. Think of $f(i)$ as outputting the $i^{th}$ set from $\mathcal{C}$. 
	   Then the infinitely long union: $\bigcup_{i \in \mathbb{N}} f(i)$ is also countably infinite.

Remember the whole idea on how to do this is to give bijective functions.
Lastly, since a countably infinite set $A$ has a bijection to $\aleph_0$, and $\mathbb{N}$ has a bijection to $\aleph_0$, we know there is a bijection $f$ from $\mathbb{N}$ to $A$. This means, we can write out the elements of $A$ in an infinitely long sequence like $a_0a_1a_2,\ldots$ In fact, $a_i = f(i)$. Since $f(i) \in \mathbb{A}$, and $f$ is bijective, it will list out all the elements of $A$ ($f$ is surjetive), and only once per element of $A$ ($f$ injective).

## Moving out of Countably Infinite

Anyway, we've listed a bunch of finite cardinal numbers, and the smallest infinite cardinal number. And I've shown you some properties we have about those numbers.

What's next? Well, how do we define an even larger cardinal? Remember they're sets.
So, we say the following $\aleph_1$ is the smallest cardinal that is "larger than" $\aleph_0$. This is a two-part statement. So more formally, we say:

1. There exists an injection from $\aleph_0$ to $\aleph_1$.
2. There does not exist a bijection from $\aleph_0$ to $\aleph_1$.
3. Let $\kappa$ be a cardinal number. If there exists an injection from $\aleph_0$ to $\aleph_1$, and there does not exist a bijection from  $\aleph_0$ to $\aleph_1$, then there exists an injection from $\aleph_1$ to $\kappa$.
   
What's the idea here? It's the following: The first two lines basically say that $\aleph_1$ is "strictly larger" than  $\aleph_0$.

 The third line says that if we find a cardinal number $\kappa$ that is also "strictly larger" than  $\aleph_0$, then  $\aleph_1$ is "smaller than or same size" as $\kappa$. That basically establishes  $\aleph_1$ to be the smallest cardinal larger than  $\aleph_0$.

Okay. So here's where it gets interesting. You all probably already had an intuitive sense about this but let's think about the following:

> Does of real numbers $\mathbb{R}$ have cardinality $\aleph_1$? 

We will revisit this in a bit. But first of all let us establish something a little more basic: "The set of reals is **strictly larger** than the set of naturals".

## Cantor's Diagonalisation Argument

To show this, we need to show that there is an injection from $\mathbb{N}$ to $\mathbb{R}$, and there is no bijection from $\mathbb{N}$ to $\mathbb{R}$. Okay! (We'll actually use $\mathbb{Z}^+$, but we know that $\lvert \mathbb{Z}^+\rvert = \lvert \mathbb{N} \rvert$.)

>[!Aside]
> I don't know if they told you this but you're about to see **diagonalisation** in a bit. And this is one of the most powerful techniques in math and CS combined. Some of the biggest and most famous theorems about logic, the naturals vs the reals, about the nature of computation, about computational complexity theory, all use **diagonalisation**. 

Let $I$ be the set $\{ x \in \mathbb{R} : 0 < x \land x < 1\}$.

We will show the two following claims:

> Claim 1: There exists an injective function $f : \mathbb{Z}^+ \to I$. Therefore $\lvert \mathbb{Z}^+ \rvert \leq \lvert I \rvert$
> Claim 2: There exists no bijection function $g : \mathbb{N} \to I$. Therefore $\lvert \mathbb{Z}^+ \rvert \neq \lvert I \rvert$
> Claim 3: There exists an injective function $h : I \to \mathbb{R}$. Therefore $\lvert I \rvert \leq \lvert \mathbb{R} \rvert$

Using all 3 claims, we get that:

$$
\lvert\mathbb{Z}^+\rvert \lneq \lvert I \rvert \leq \lvert \mathbb{R} \rvert
$$
(We actually haven't shown $\leq$ on cardinals are transitive. Perhaps that's a good exercise. We need to show it's transitive to show that claims 1-3 imply $\lvert \mathbb{Z}^+ \rvert \lneq \lvert \mathbb{R} \rvert$.)

Claims 1 and 3 are actually somewhat straightforward. We'll give the functions here, and it'll be a mini-exercise to prove they are injective, and that their co-domains are correct.

$$
\begin{align}
&f : x \mapsto \frac{1}{x+1}\\
&h : x \mapsto x
\end{align}
$$

We need the fact that every $x \in I$ has a "decimal expansion".

> Lemma: For all $x \in \mathbb{R}$ such that $0 \leq x \land x < 1$, there exists a function $d : \mathbb{Z}^+ \to \{0, 1, 2, 3, 4, 5, 6, 7, 8, 9\}$ such that, letting $x_i = d(i)$, then $x = x_1x_2x_3\ldots$ Or equivalently: 
  $$
	x = \sum_{i \in \mathbb{Z}^+} d(i) \times 10^{-i}
 $$
 > For $x \in I$, let $d_x$ be the function for $x$.


To prove the above lemma is pretty annoying unless you have a background in [Real Analysis](https://en.wikipedia.org/wiki/Real_analysis). So we'll just take this as fact. Here's the strategy:

1. We want to assumes that a bijection $g$ exists. Remember this means $g$ will list out all elements in $I$ somehow (surjection, injection).
2. We will use $g$ to create another element $y$ so that $y \in I$, but $y \notin ran(g)$.
3. That's a contradiction because $g$ was supposed to be bijective, and therefore surjective. But line 3 shows that $g$ is not surjective.

Remember, this means that $g$ on a positive integer gives us a value $x$ from set $I$.
Furthermore, any value $x$ in $I$ can be written in decimal representation using function $d_x$.
In particular, the $d_x(j)$ gives us the $j^{th}$ decimal place of $x$.

**Proof by contradiction.**

1. Assume for a contradiction that there exists a bijection $g : \mathbb{Z}^+ \to I$.
2. For all $i \in \mathbb{Z}^+$, $g(i) \in I$, therefore $d_{g(i)}$ exists.
3. Now define a new function $d_y : \mathbb{Z}^+ \to \{0, 1, 2, 3, 4, 5, 6, 7, 8, 9\}$ in the following way:
   
	 $d_y(i) = \begin{cases} 1 & \text{ if } d_{g(i)}(i) \neq 1 \\ 2 & \text{ if } d_{g(i)}(i) = 1 \end{cases}$
	 
	 Thus, let $y = \sum_{i \in \mathbb{Z}^+} d_y(i) \times 10^{-i}$.
4. Now notice, by definition of $d_y$:
	1. Let $i \in \mathbb{Z}^+$, arbitrarily chosen.
	2. $d_y(i) \neq d_{g(i)}(i)$.
	3. Since $y$ and $g(i)$ differ on the $i^{th}$ decimal place, $y \neq g(i)$.
	4. Therefore, by universal generalisation $\forall i \in \mathbb{Z}^+[y \neq g(i)]$.
5. By line $4$, this means $y$ is not in the range of $g(i)$.
6. But note that $y < 1$ and $y > 0$. Since $y$ only has decimal places that are either $1$ or $2$.
7. Therefore $y \in I$. But this means that $g$ is not a surjection on $I$.
8. Line 7 contradicts line 1. Therefore there does not exist bijections from $\mathbb{Z}^+ \to I$.

This proof is basically an informal version of [Cantor's Diagonalisation Argument](https://en.wikipedia.org/wiki/Cantor%27s_diagonal_argument).

I'll drawn some diagrams that might help explain the idea:

#### Diagrammatically:
The idea is that any number between 0 and 1 and can be written out in the following way:

![[Excalidraw/Cantor.light.png|Center]]


Where all of the $d(j)_{g(i)}$ happens to be a number between 0 and 9. So in particular:

$$
g(i) = \sum_{j \in \mathbb{Z}^+} 10^{-j} d(j)_{g(i)}
$$

So what happens if we took every number on the diagonal and collected them. Then did a bunch of changing? Like so:

![[Excalidraw/Cantor-changed.light.png|Center]]

As long as we make all the $y_i$ values a value between 1 and 8 (inclusive) then 
$$
y = \sum_{i \in \mathbb{Z}^+} y_i \times 10^{-i}
$$
is a value greater than $0$, and strictly less 1. The bigger question is whether $y$ can be an output of $g$. Well we claim the answer is no. Why? Look at the $i^{th}$ output of $g$. That happens to be $g(i)$. Then look at the $i^{th}$ decimal place of $g(i)$, that happens to be $d(i)_{g(i)} = g(i)_i$. Now we made the $i^{th}$ decimal place of $y$ (which is $y_i$) different from $d(i)_{g(i)} = g(i)_i$ . So that means $y$ cannot be equals to $g(i)$ (since they differ on the $i^{th}$ decimal point). So the above formal proof is basically just carrying that idea out.

## So have we created the next cardinality after $\aleph_0$?
Well.. that's complicated. We know that the reals indeed has a cardinality larger than the naturals. It turns out we **actually cannot prove whether or not** $\lvert \mathbb{R} \rvert = \aleph_1$. Like not that humans are not smart enough. No, more like the proof system we all use is unable to. At least not with the current axioms we like to take for granted.

The next cardinality after $\aleph_0$ happens to be $\aleph_1$,  but we cannot prove whether $\mathbb{R}$ has cardinality $\aleph_1$ or not. 

# How is this relevant to computer science?
So this proof idea actually extends to computer programs and program analysis. (Also further beyond that). I have two theorems to offer you, both saying roughly the same thing. Also I will only make informal statements because to formalise these concepts, you'll need a full semester so the notions here are only pseudo-formal but should convey the key idea nonetheless.

> Claim 1: There exist unsolve-able problems by computer programs.
> 
> Claim 2: Let $\mathcal{D} : \mathbb{P} \times \mathbb{I} \to \{true, false\}$ be the following problem: Let $\mathbb{P}$ be the set of computer programs, let $\mathbb{I}$ be the set of program inputs. Let $P \in \mathbb{P}$ be a computer program, let $I \in \mathbb{I}$ be a program input. Then $\mathcal{D} : (P, I) \mapsto true$, if program $P$ on input $I$ will terminate. Otherwise, $\mathcal{D}$ outputs $false$.

Let's begin with the first one. We first need a simple formalisation of a computer program. To simplify things, let's consider problems as functions $Q : \mathbb{I} \to \{yes, no\}$. So $Q$ is a problem, because it takes a program input, and $Q$ specifies for each input whether you need to say yes, or no. Here's an example:

>[!Example]
> **Is Sorted Problem:** Let $\mathbb{I}$ be the set of all lists of numbers. Then $Q : \ell \mapsto true$ if and only if the list $\ell$ is sorted. $Q$ outputs $false$ otherwise.

Bear in mind, $Q$ is not a program, it's a problem. That means to solve the sorted problem, you need to give a computer program $P$, maybe in Source, or in Javascript or Python, such that for all inputs $\ell \in \mathbb{I}$, $P(\ell) = Q(\ell)$. Why are they different? Because you can write many programs that solve the same problem. After all, I'm sure you've seen at least your classmates write different code to solve the same problem. Here's another example:

>[!Example] 
> **Is Palindromic Problem:** Let $\mathbb{I}$ be the set of all strings. Then $Q : s \mapsto true$ if and only if $s$ is a palindromic string.

Okay, so here's a few observations we need to make in our formalisation. We think of our input as finite length objects. Our input lists or strings should be **finite in length**. And furthermore, there are **countably infinitely many** of such inputs. This means that our $\mathbb{I}$ is basically a countably infinite set.

I already hear the 1% of you protesting: "But what about infinite lists as our input or uncountably infinitely sized input sets $\mathbb{I}$?". Well do you want your program to take forever to just read the inputs? Also uncountably large $\mathbb{I}$ is technically not an issue, but let's table that for another day.

Okay, now you know what a problem $Q : \mathbb{I} \to \{true, false\}$ is. I'll tell you up front, if $\mathbb{I}$ is countably infinite, then $Q$ as a set is also countably infinite. I won't prove this here because it's beside the point.

Let $\mathcal{Q}$ be the set of all problems $\{ Q \subseteq (\mathbb{I} \times \{true, false\}) : Q\text{ is a function } \mathbb{I} \to \{true, false\} \}$. Yes we can formalise set using first order logic, but I'll skip that, we're doing big picture right now.

Now, what is the cardinality of $\mathcal{Q}$? I'm actually going to show you that $\lvert \mathcal{Q}\rvert = \lvert \mathbb{R} \rvert$. How do we do this? Here's the trick: We will construct a bijection between $\mathcal{Q}$ and the real interval $[0, 1]$. And we know this interval itself [[#Cantor's Diagonalisation Argument|has cardinality strictly larger than any countably infinite set]]. Thanks Dr. Georg Cantor.

To construct this bijection would mean to take a $Q$ in $\mathbb{Q}$, and uniquely map it to a real number in $[0, 1]$. We take $Q : \mathbb{I} \to \{true, false\}$, and first make $Q' : \mathbb{I} \to \{0, 1\}$. Then we make a function $c : \mathbb{N} \to \{0, 1\}$. $Q'$ is basically $Q$ but remapping $true$ to $1$, and $false$ to $0$. Then $c$ is basically making use o the fact that $\mathbb{I}$ is countably infinite, so we can replace the domain of $Q'$ with $\mathbb{N}$ using a bijection. Then what is $c$? $c$ basically lists out an infinite sequence of 0's and 1's. What happens if we interpret this sequence as a fractional number in binary? Then we just get all of the numbers in the interval $[0, 1]$! So each $Q \in \mathcal{Q}$ now maps to $x_Q \in [0, 1]$. 

1. Let $Q \in \mathcal{Q}$. Then $Q : \mathbb{I} \to \{true, false\}$.
2. Since $\lvert \mathbb{I}\rvert = \lvert \mathbb{N} \rvert$, exists bijective $f : \mathbb{N} \to \mathbb{I}$.
3. Consider function $c: \mathbb{N} \to \{0, 1\}$:
	1. $c : i \mapsto \begin{cases} 1, \text{ if } Q(f(i)) = true \\ 0, \text{ if } Q(f(i)) = false\end{cases}$
	2. Note: $f$ bijects $\mathbb{N}$ to $\mathbb{I}$, and $Q$ maps the output of $f$ to either $true$ or $false$.
			Then $true$ is mapped to $1$ and $false$ is mapped to 0.
		
