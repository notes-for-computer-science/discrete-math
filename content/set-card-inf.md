---
title: Set Cardinality, Infinity, and The Halting Problem
draft: false
tags:
  - notes
hidden:
---
 
In our journey of formalising everything that we know, let's reinspect something that we've taken for granted: Set sizes. Thus far we've sort of been eyeballing it for finitely sized sets. E.g. $A = \{1, 2, 5\}$ so we say that $\lvert A \rvert = 3$. So how do we say $\lvert A \rvert < \lvert B \rvert$? We'd probably say $\lvert A \rvert = s_A, \lvert B \rvert = s_B$ with $s_A, s_B \in \mathbb{N}$. Then if $s_A \leq s_B$, then we know $\lvert A \rvert \leq \lvert B \rvert$.

But here, there are many things that actually have been left unformalised, and also unsolved. For example:
1. Okay let's say we accept that we can compare two natural numbers for free (which actually can be formalised, but story for another day).
2. How do we rigorously say that the size of a set $\lvert A \rvert = n$? You might say "I can just list them out and count." but sometimes that's not enough for mathematicians.
3. Even more worryingly, we've only talked about finite sets. What about infinite sets? You could count until the cows come home and never be done. 
4. Is there only one infinity? We all know that $\mathbb{N}, \mathbb{Z}, \mathbb{Q}, \mathbb{R}$ all behave a little different. $\mathbb{N}$ seems to only be infinite in one way, $\mathbb{Z}$ seems to be infinite in two ways, $\mathbb{Q}$ seems to be so infinite, you can take two items and find infinitely many items between them (where "between" is based on the $<$ relation). $\mathbb{R}$ seems to be so infinite it even contains infinitely many more items that $\mathbb{Q}$ does not. But are their "sizes" different? 

So let's talk about how to formalise these things and study not just counting the sizes of finite sets, but the sizes of infinite sets, and see how infinity behaves.

> [!Question]
> **Isn't this a little too abstract?** Why do I need to know this in computer science?
> 
> I'm glad you asked! So later on when we understand diagonalisation and the different infinities better, I'm going to show you how we can formally prove that there exists problems out there that computers cannot solve.
> 
> In fact, as newcomers (or not) to programming, you might wonder: Can my compiler/runtime environment ever look at my code and tell me it will loop forever, and then I can go fix it. The answer is **nope**. To be clear, it might be able to spot certain patterns and warn you, but there isn't a perfect algorithm that has 100% accuracy on this. We'll see this at the end of the notes.

>[!Disclaimer]
> The following notes are not meant to be a formal foray into set theory and the cardinals. So we will be glossing over a lot of details (for convenience) and taking a lot of very hardcore theorems for granted.
> So to be clear we're restricting ourselves to certain statements we want to make. And the focus of these notes is to just give intuition on higher level ideas about stuff between sets.

## Let's begin!
Our starting point is picking up where we let off with functions. So recall:
A function $f : A \to B$ is:
1. Injective, if and only if, $\forall a_1, a_2 \in A [f(a_1) = f(a_2) \to a_1 = a_2]$
2. Surjective, if and only if, $\forall b \in B, \exists a \in A [f(a) = b]$
3. Bijective, if and only if, $f$ is both injective and surjective.

The idea is the following: We will say that $\lvert A \rvert \leq \lvert B \rvert$ if **there exists** a function $f : A \to B$ such that $f$ is injective. The intuition is the following: If the "size" of $A$ is less than or equals to the "size" of $B$, then we should be able to associate every item of $A$ with a unique item of $B$. This "association" is basically proven by giving $f$.

Furthermore, if $\lvert A \rvert \leq \lvert B \rvert$, and $\lvert B \rvert \leq |A|$ then we will say $\lvert A \rvert = \lvert B \rvert$.

>[!Aside]
>Notice here I am saying that the $\lvert A \rvert$ here cannot actually yet mean a number. We must take two sets $A, B$ and then surround them by $\vert$, and then this thing can sandwich a $\leq$ symbol if and only if there exists an injection $f : A \to B$.
>
>So at least in the current formalisation: We can only compare set sizes, and we can only argue set cardinalities relatively between $A$ and $B$. We cannot say $\lvert A \rvert = n$ for some $n \in \mathbb{N}$.

> **But wait. If there is a bijection $h : A \to B$, can't we also say $\lvert A \rvert = \lvert B \rvert$?** 

Yes. Yes you can.

> **...So you're telling me, if I have $f : A \to B$, and $g : B \to A$ both injective functions, then there exists a bijection $h : A \to B$?**

Yes! This is called the [Cantor-Schroder-Bernstein theorem](https://en.wikipedia.org/wiki/Schr%C3%B6der%E2%80%93Bernstein_theorem).

> [!Theorem]
> (Cantor-Schoder-Bernstein) If there exists injective functions $f : A \to B$, $g : B \to A$ between sets $A, B$. Then there exists bijective function $h : A \to B$.

>[!Quiz]+ 
> Notice that the theorem statements says if injective $f, g$ exists, then bijective $h$ exists. What about the other direction? If $h : A \to B$ is bijective, can we find injective functions $f : A \to B$ and $g : B \to A$?
> > [!Answer]-
> > 
> > Yes. Assume $g : A \to B$ bijective. Then $f = g$ is an injective function from $A \to B$. Also $f^{-1}$ is an injective function from $B \to A$. Check it!

## Using functions to talk about set cardinalities

### Finite Sets
So the first thing we should do is talk about finiteness. Now we can be (slightly more) formal. From this point on, we will use $\mathbb{Z}_n$ as a **shorthand (this is not standard notation)** to denote the set $\{1, 2, 3, \ldots, n\} = \{x : x \in \mathbb{Z} \land x \geq 1 \land x \leq n\}$. Also, $\mathbb{Z}_0$ is defined to be $\varnothing$. [^assumption-nat]

Then, we will say a set $A$ is finite if:

There exists an $n \in \mathbb{N}$ and a bijection function $f : A \to \mathbb{Z}_n$.

Intuitively, a finite set either has 0 elements, or it has $n$ elements for some $n \in \mathbb{Z}^+$. The function $f$ here is basically just telling you how to show it has $n$ elements by literally saying: "This is element 1... this is element 2..." and so on.
## Infinite Sets

### Countably Infinite Sets
Okay let's move onto infinite sets because here's where things get very interesting. And we'll talk about the "smallest" infinity: Countable infinity.
So cardinality gets a bit weirder when infinite sets are a thing. For example, $\lvert \mathbb{N}\rvert = \lvert \mathbb{Z} \rvert = \lvert \mathbb{Q} \rvert$.
In fact, let $O = \{2x + 1 : x \in \mathbb{N}\}$. We can also say $\lvert O \rvert = \lvert \mathbb{N} \rvert$. That seems weird, clearly there are more natural numbers than odd numbers. And perhaps you can say that. That's why we say: the cardinalities are the same. Cardinalities are our way of talking about "size" between sets.

> [!Technique]
> Let's begin with the fundamental techinque of proving statements like:
> $$\lvert A \rvert = \lvert B \rvert$$
> 
> To prove $|A| = |B|$, in CS1231S, you need to give a bijection $f : A \to B$.
> Outside of CS1231S, there are other methods:
> 1. Give an injective function $f : A \to B$, and an injective function $g : A \to B$; or
> 2. Give an injective function $f : A \to B$, and a surjective function $g : A \to B$. 
> 
>
> Two things to note about the additional methods (in fact, in CS1231S, they've mentioned this holds for finite sets):
> When there exists a surjection $g : A \to B$, you're proving $\lvert A \rvert \geq \lvert B \rvert$. Does this mean you're proving that there exists an injection $h$ from $B \to A$? Yes!
>
> Just because there is an injection $f : A \to B$, and surjection $g : A \to B$ does not mean $f$ is a surjection, and it does not mean $g$ is a surjection. What it means is that if you prove $f, g$ exist, then there exists a bijection $h: A \to B$.
> 
> To summarise, the following are equivalent:
> 1. $A \leq B$
> 2. $\exists f : A \to B$ such that $f$ is injective.
> 3. $\exists f : B \to A$ such that $f$ is surjective.
>
> Also, these follow are equivalent:
> 1. $A = B$
> 2. $A \leq B \land B \leq A$
> 3. $\exists f : A \to B$ such that $f$ is bijective.
> 4. $\exists f_1 : A \to B$ and $f_2 : A \to B$, such that $f_1$ is injective, and $f_2$ is surjective.
> 5. $\exists f_1 : A \to B$ and $f_2 : B \to A$, such that $f_1, f_2$ are both injective.
> 
> Lastly, these are equivalent:
> 1. Exists an injective function $f : A \to B$
> 2. Exists a surjective function $B : B \to A$

> [!Technique]
> Another technique given in CS1231S that was not made entirely explicit is the following:
> To show $\lvert A \rvert \leq \lvert\mathbb{N}\rvert$, one can give a surjective function $g : \mathbb{N} \to A$.
> 
> They've stated in in the form of sequences, but $g$ is basically giving you your sequence here.
> 
> Why is this simpler? Because now instead of giving a bijection, you only have to give a surjection.


For the remainder of this subsection, let's demonstrate some of these to show some of the equalities.

And for fun, we'll prove some of the equivalences. The proofs will be quite long but they're collapsible so hopefully the page isn't too large.


> [!Theorem]
> $\lvert \mathbb{N} \rvert = \lvert \mathbb{Z} \rvert$

> [!Proof]-
> So we'll do this by giving the bijective function itself.
> Consider $f : \mathbb{N} \to \mathbb{Z}$, where $f : x \mapsto (-1)^x (\frac{n}{2} + \frac{1}{4}) - \frac{1}{4}$
> We now need to show that $f$ is bijective. We are going to employ the help of this lemma:
> > [!Lemma] **Claim: $f(n)$ is negative if and only if $n$ is odd.**
> > 1. Let $n \in \mathbb{N}$ be odd. I.e. $\exists t \in\mathbb{N} = 2\cdot t + 1$.
> > 2. Then $(-1)^{n} = (-1)^{2t+1} = (-1)^{2t}(-1) = (-1)$
> > 3. Then $(-1)^{n}\left(\frac{n}{2} + \frac{1}{4}\right) - \frac{1}{4} = (-1)\left(\frac{2t+1}{2} + \frac{1}{4}\right) - \frac{1}{4}$
> > 4. $(-1)\left(\frac{2t+1}{2} + \frac{1}{4}\right) - \frac{1}{4} = (-1)(t + \frac{3}{4}) - \frac{1}{4}$
> > 5. $(-1)(t + \frac{3}{4}) - \frac{1}{4} = - t - 1$.
> > 6. Since $t \in \mathbb{N}, f(n) < 0$.
> > 7. Let $n \in \mathbb{N}$ be even. I.e. $\exists t \in\mathbb{N} = 2\cdot t$.
> > 8. $(-1)^{2t} = 1$.
> > 9. $(-1)^{n}\left(\frac{n}{2} + \frac{1}{4}\right) - \frac{1}{4} = \left(t  + \frac{1}{4}\right) - \frac{1}{4}$
> > 10. $\left(t  + \frac{1}{4}\right) - \frac{1}{4} = t$
> > 11. Since $t \in \mathbb{N}$, $f(n) = t \geq 0$.
> > 12. By lines 6. and 10. $f(n)$ is negative if and only if $n$ is odd.
>
> > [!Proof]
> > **Claim: The given $f$ is surjective.**
> >
> > Remark: The tricky part about this one is that when we take an arbitrary $y$ as output of $f$, we don't yet know how to argue whether $n$ is even or odd. So we begin with this first:
> > 
> > Now we can try to prove surjectivity.
> > 1. Let $y \in \mathbb{Z}$ be arbitrarily chosen.
> > 2. Either $y \geq 0$ or $y < 0$.
> > 3. Case 1: $y \geq 0$
> > 	1. Then consider $n = 2y$.
> > 	2. Since $f(n) = (-1)^{n}\left(\frac{n}{2} + \frac{1}{4}\right) - \frac{1}{4}$
> > 	3. $(-1)^{n}\left(\frac{n}{2} + \frac{1}{4}\right) - \frac{1}{4} = \left(y + \frac{1}{4}\right) - \frac{1}{4}$
> > 	4. $\left(y + \frac{1}{4}\right) - \frac{1}{4} = y$
> > 	6. Also since $2y = n$, $n \in \mathbb{N}$, by closure of $\times$ on $\mathbb{N}$.
> > 	7. Therefore $\exists n \in \mathbb{N} [f(n) = y]$
> > 4. Case 2: $y < 0$
> > 	1. Then consider $n=-2y-1$.
> > 	2. $f(n) = (-1)^{n}\left(\frac{n}{2} + \frac{1}{4}\right) - \frac{1}{4}$
> > 	3. $(-1)^{n}\left(\frac{n}{2} + \frac{1}{4}\right) - \frac{1}{4} = (-1)\left(\frac{-2y-1}{2} + \frac{1}{4}\right) - \frac{1}{4}$
> > 	4. $(-1)\left(\frac{-2y-1}{2} + \frac{1}{4}\right) - \frac{1}{4}=(-1)(-y-\frac{1}{4}) - frac{1}{4}$
> > 	5. $(-1)(-y-\frac{1}{4}) - frac{1}{4}=y$
> > 	6. Also since $y < 0$, $2y < 1$, and $-2y \geq 1$, and $-2y - 1\geq 0$.
> > 	7. So $n \geq 0$.
> > 	8. Also by closure of multiplication and subtraction/addition, $n \in \mathbb{Z}$.
> > 	9. Therefore $n \in \mathbb{N}$.
> > 	10. Therefore $\exists n \in \mathbb{N} [f(n) = y]$
> > 5. In all cases it is shown that $\exists n \in \mathbb{N} [f(n) = y]$.
> > 6. By universal generalisation, $\forall y \in \mathbb{Z}, \exists n \in \mathbb{N} [f(n) = y]$.
> 
> Now we move to prove $f$ is injective.
> 
> >[!Proof]
> > **Claim: The given $f$ is injective.**
> > 
> > 1. Let $x_1, x_2 \in \mathbb{N}$ arbitrarily chosen. Further assume $f(x_1) = f(x_2)$.
> > 2. Now, by the Lemma, either both $x_1, x_2$ are even, or $x_1, $x_2$ are odd.
> > 3. Case 1: Both $x_1, x_2$ are even.
> > 	1. Now $(-1)^{x_1} = 1 = (-1)^{x_2}$.
> > 	2. By the assumption, $(-1)^{x_1}\left( \frac{x_1}{2} + \frac{1}{4} \right) - \frac{1}{4} = (-1)^{x_2}\left( \frac{x_2}{2} + \frac{1}{4} \right) - \frac{1}{4}$
> > 	3. Then by line 3.1 and basic algebra: $x_1 = x_2$.
> > 4. Case 2: Both $x_1, x_2$ are odd.
> > 	5. Now $(-1)^{x_1} = -1 = (-1)^{x_2}$.
> > 	6. By the assumption, $(-1)^{x_1}\left( \frac{x_1}{2} + \frac{1}{4} \right) - \frac{1}{4} = (-1)^{x_2}\left( \frac{x_2}{2} + \frac{1}{4} \right) - \frac{1}{4}$
> > 	7. Then by line 3.1 and basic algebra: $x_1 = x_2$.
> > 5. In all cases it is shown that $x_1 = x_2$.
> > 6. By universal generalisation, $\forall x_1, x_2 \in \mathbb{N}[f(x_1) = f(x_2) \to x_1 = x_2]$.

> [!Theorem]
> $\lvert \mathbb{N} \rvert = \lvert \mathbb{Z}^+ \rvert$

> [!Proof\-Idea]-
> Consider function $f : \mathbb{N} \to \mathbb{Z}^+$ where $f : n \mapsto n + 1$.
> This should be doable for the reader at this point, to show that this function is indeed a bijection.


> [!Theorem]
> $\lvert \mathbb{N} \times \mathbb{N} \rvert = \lvert \mathbb{N} \rvert$

This is one of the more useful statements. We will use what is called [Cantor's Pairing Function](https://en.wikipedia.org/wiki/Pairing_function) Here's a proof:

> [!Proof]-
> 
> Here's a picture that sort of illustrates the what the below function is doing:
> 
> ![[diagonal-bijection.png]]
> 
> Let's think about it this way, we're making triangles. How do I make a $n$-tall, and $n$-wide triangle? What's the size? It's actually the $n^{th}$ triangle number $(n)(n+1)/2$. Try it!
> 
> One more thing, given $(i, j)$, we can actually see that it falls on the $(i + j)^{th}$ diagonal. We are going to refer to the $d = i + j$. So the $d^{th}$ diagonal contains all pairs $(i, j)$ such that $i + j = d$.
> 
> ![[diagonal-bijection-change-coord 1.png|350]]
> 
> Combine the two ideas we had, and you can see that basically on the $d^{th}$ diagonal starts with the $d^{th}$ triangle number. Furthermore, the $d^{th}$ diagonal actually has $d+1$ elements on its diagonal. So what numbers are on the $d^{th}$ diagonal?
> 
> Any number of the form $d(d+1)/2 + t$, for any $0 \leq t \leq d$ (there are in total $d+1$ such numbers).  
> 
> Okay we are in place to begin the proof. Here's the intuition. Given $(i, j)$, we map it to the $d^{th}$ diagonal (where $i + j = d$, and its offset into the diagonal is $j$. 
> Consider function $f : (i, j) \mapsto \frac{(i + j)(i + j + 1)}{2} + j$.
> 
> We first show that $f$ is surjective. The idea is the following: We first find the largest triangle number that is at most $n$, then use that to figure out which diagonal $n$ sits on, then figure out what $n$'s offset is on that diagonal.
> 1. Let $n \in \mathbb{N}$, arbitrarily chosen.
> 2. Let $t$ be the largest natural number such that $t(t+1)/2 \leq n$.
> 3. We first claim that $n \leq t(t+1)/2 + t$.
> 	1. Suppose not. Then $n > t(t+1)/2 + (t + 1) - 1 = \frac{t(t+1) + 2(t+1)}{2} - 1 = \frac{(t+1)(t+2)}{2} - 1$.
> 	2. Then we obtain a contradiction because now $t+1 > t$, and $n \geq \frac{(t+1)(t+2)}{2}$.
> 4.  Now consider $j = n - t(t+1)/2$. Since $n \leq t(t+1)/2 + t$, $j \leq t$.
> 5.  Also since $n \geq t(t+1)/2$, $j = n - t(t+1)/2 \geq 0$.
> 6.  Now let $i = t - j$. Since $j \leq t$, $i \geq 0$.
> 7.  Now both $i, j \in \mathbb{N}$, since $t \in \mathbb{N}$.
> 8.  Now 
> $$\begin{align}
> f(i, j) &= \frac{(i+j+1)(i+j)}{2} + j\\
> 		&= \frac{(t+1)(t)}{2} + n - \frac{t(t+1)}{2}\\
> 		&= n
> \end{align}$$
> 9. Therefore, $\forall n \in \mathbb{N}, \exists i, j \in \mathbb{N} [f(i, j) = n]$.
>  Remark: To be clear, in case the argument looks circular, the important idea is the following: when we are given $n$, we need to figure out which diagonal it's on. To do this, we needed to find the largest triangle number that is less than or equals to $n$. Let that be the $t^{th}$ triangle number. Then we know $n$ was due to some $i, j$ such that $i + j$ is $t$. Then, to figure out $j$, we know $n$ is exactly $j$ away from $t(t+1)/2$. So $j = n - t(t+1)/2$. We then had to check that $i$ and $j$ were natural numbers. I.e. $\geq 0$, and I skipped a few steps on closure. E.g. $t(t+1)/2$ is a natural number. But that's the idea.
>  
>  Next, we show that $f$ is injective. To do this, we again use the fact that we can compare two outputs using which diagonal they are sitting on. If it's different, they we know they're already different. If they're the same, then their offsets into the diagonal must be different.
>  To do this, we will actually prove the contrapositive statement. 
>  
>  1. Let $(i_1, j_1), (i_2, j_2) \in \mathbb{N} \times \mathbb{N}$. Further assume that $(i_1, j_1) \neq (i_2, j_2)$.
>  2. Either $i_1 + j_1 = i_2 + j_2$, or $i_1 + j_1 \neq i_2 + j_2$.
>  3. Case 1: $i_1 + j_1 = i_2 + j_2$
> 	 1. Letting $i_2 + j_2 = d = i_1 + j_1$:
> 	 2. Assume that $j_1 = j_2$.
> 		 1. Then $i_2 = d - j_2 = d - j_1 = i_1$.
> 		 2. Then $j_1 = j_2$ and $i_1 = i_2$. Contradiction against line 1.
> 	 3. Therefore $j_1 \neq j_2$
> 	 4. $$\begin{align}
> 		f(i_1, j_1) &= (d)(d+1)/2 + j_1\\
> 					&\neq (d)(d+1)/2 + j_2\\
> 					&= f(i_2, j_2)
> 		\end{align}$$
>  4. Therefore, $\forall (i_1, j_1), (i_2, j_2) \in \mathbb{N}\times \mathbb{N}[(i_1, j_1) \neq (i_2, j_2) \to f(i_1, j_1) \neq f(i_2, j_2) ]$
>  5. $f$ is injective (Contrapositivity of line 4).

## Within Countable Infinity

It turns out we have a name for the "size" or cardinality of $\mathbb{N}$. We call it $\aleph_0$ (pronounced "aleph null"). This is the **first** transfinite cardinal number. Think of cardinal numbers as "numbers" that represent the size of sets. So we write $\lvert \mathbb{N} \rvert = \aleph_0$.

>[!Definition]
>We will say a set $A$ is **countably infinite** if $A$ has a bijection to $\mathbb{N}$.
>
>Furthermore, we will say a set $A$ is **countable** if either:
> 1. $A$ is finite; Or
> 2. $A$ is countably infinite.
> 
> It's important to note the difference between **countable** and **countably infinite**.
>    

That said, there is a different formulation that I've previously mentioned. Basically:

>[!Remark]
> A set $A$ is **countable** if there exists a surjective function $f : \mathbb{N} \to A$.
> 
> The CS1231S treatment is via sequences. But such a function $f$ that is surjective basically provides a sequence that satisfies [Lemma 9.2, Slide 34 of the CS1231S notes](https://www.comp.nus.edu.sg/~cs1231s/lect/24s1/Lecture09_Cardinality_full.pdf). 

There are a few useful facts (provable theorems, in fact) that you should know. Let's list some out now:

1. $\lvert \mathbb{N} \rvert = \lvert \mathbb{Z} \rvert = \lvert \mathbb{Q} \rvert = \aleph_0$
2. Let $A \subseteq B$ where $B$ is countably infinite. Then $A$ is countable.
3. If $A, B$ are countably infinite sets, then $\lvert A \times B \rvert = \lvert A \cup B \rvert = \aleph_0$.
4. If $A, B$ are countable sets. Then $A \times B$ is countable. Also, $A \cup B$ is countable.
5. Let $A_1, A_2, \ldots = (A_n)_{n \in \mathbb{Z}^+}$ be a sequence of countable sets. Define $\bigcup_{i = 1}^{\infty} A_i = \mathcal{A}$. Then $\mathcal{A}$ is countable.

>[!Theorem]+
> The idea is to use the fact that we can make a surjection from $\mathbb{N}$ to $A$.
> 1. Let $B$ be countably infinite, and $A$ be a subset of $B$. If $A$ is empty, we are done, since $A$ is finite, therefore countable.
> 2. Therefore we now assume $A$ is non-empty.
> 3. Since we know $A$ is non-empty, $\exists z \in A$.
> 4. By line 1, we know that there exists a bijection $f : \mathbb{N} \to B$.
> 5.  Now create function $g : \mathbb{N} \to A$, where:
> $$
> g : n \mapsto \begin{cases}
> z &, \text{ if }f(n) \in B \setminus A\\
> f(n)&, \text{ otherwise}
> \end{cases}
> $$
> 6. Note that $g$ only maps to elements in $A$, since $f$ maps to $B \setminus A$ or $A$, and when it does, $g$ maps to $z \in A$. 
> 7. Now let $a \in A$, arbitrarily chosen.
> 8. Since $f$ was surjective on $B$, and $a \in B$ (because $A \subseteq B$), $\exists i \in \mathbb{N}$ such that $f(i) = a$.
> 9. Since $f(i) \notin B \setminus A$, $g(i) = f(i) = a$.
> 10. Therefore $\forall a \in A, \exists i \in \mathbb{N}[g(i) = a]$.
> 11. Therefore $g : \mathbb{N} \to A$ is a surjection.
>  12. Thus, $A$ is countable. 

>[!Theorem]+
> If $A, B$ are countably infinite sets, then $\lvert A \times B \rvert = \lvert A \cup B \rvert = \aleph_0$
> 
> Let $A, B$ be countably infinite. Then there exists bijections $f: \mathbb{N} \to A$, and $g : \mathbb{N} \to B$ such that $f, g$ bijective.
> 
> For $\lvert A \times B\rvert$, it suffices to show that $\lvert A \times B\rvert = \lvert \mathbb{N} \times \mathbb{N}\rvert$, since we know the latter has a bijection to $\lvert \mathbb{N}\rvert$.
> 
> Consider $h : \mathbb{N} \times \mathbb{N} \to A \times B$, where $h(i, j) \mapsto (f(i), g(j))$. We know that since $f(i) \in A, g(j) \in B$, $h(i, j) \in A \times B$.
> 
> 1. Let $(a, b) \in A \times B$, arbitrarily chosen.
> 2. Now since $f$ is surjective, exists $i \in \mathbb{N}$ such that $f(i) = a$.
> 3. Now since $g$ is surjective, exists $j \in \mathbb{N}$ such that $f(j) = a$.
> 4. Therefore $h(i, j) = (f(i), g(j)) = (a, b)$.
> 5. By universal generalisation $\forall (a, b) \in A \times B \exists i, j \in \mathbb{N} [h(i, j) = (a, b)]$.
>    
> For $\lvert A \cup B\rvert$, the idea is that we want to list out the elements in an alternating fashion. One element from $A$, then one from $B$, then one from $A$, then one from $B$, then one from...
> The tricky thing here is that they might not be disjoint. So a little annoyingly, we want to actually do this thing where we make 3 sets: $A \cap B$, $A \setminus B$, and $B \setminus A$.
> 
> Again, since $A, B$ are both countably infinite, we know that  there exists bijections $f: \mathbb{N} \to A$, and $g : \mathbb{N} \to B$ such that $f, g$ bijective.
> Consider $h : \mathbb{N} \to A \cup B$, $h : n \mapsto \begin{cases} f(i), \exists i \in \mathbb{N} [n = 2i] \\ g(i), \exists i \in \mathbb{N} [n = 2i + 1] \end{cases}$
> 
> We need to show $h$ is a function to begin with. After all, what happens if a single $n$ lands in both cases? What about if it's in a single case but there are $i \neq i'$ such that $n$ somehow is related to both $i$? We know it won't happen, but we should formally prove it.
> 
> **Claim 1: $h$ is well-defined**.
> 1. Let $n \in \mathbb{N}$, then $n$ either even or odd.
> 2. Case 1: $n$ is even.
> 	1. Then $\exists i \in \mathbb{N} [n = 2i]$.
> 	2. Assume for a contradiction that $\exists i' \in \mathbb{N} [i' \neq i \land n = 2i']$.
> 	2. Then $2i' = n = 2i$.
> 	3. Thus $i' = i$. But that contradicts our assumption on $i'$.
> 	4. Therefore $i$ is unique. 
> 	5. Since $f$ is a well-defined function, there exists a unique $a \in A$ such that $f(i) = a$.
> 	6. Since $i$ is unique, there exists a unique value $a \in A$ such that $h(n) = f(i) = a$.
> 	7. Therefore $h$ is well-defined.
> 3. Case 2: $n$ is odd.
> 	1. Then $\exists i \in \mathbb{N} [n = 2i+1]$.
> 	2. Assume for a contradiction that $\exists i' \in \mathbb{N} [i' \neq i \land n = 2i' + 1]$.
> 	3. Then $2i' + 1 = n = 2i + 1$.
> 	4. Thus $i' = i$. But that contradicts our assumption on $i'$.
> 	5. Therefore $i$ is unique. 
> 	6. Since $g$ is a well-defined function, there exists a unique $b \in B$ such that $g(i) = b$.
> 	7. Since $i$ is unique, there exists a unique value $b \in B$ such that $h(n) = g(i) = b$.
> 	8. Therefore $h$ is well-defined.
> 4. In both cases, it is shown that $h$ is well defined.
>    
> Okay, now that we've done that, we next move to show that $h$ is bijective.
> 
> 1. Let $x \in A \cup B$ be arbitrarily chosen.
> 2. Now either $x \in A$ or $x \in B$.
> 3. Case 1: $x \in A$
> 	1. Then by surjectivity of $f : \mathbb{N} \to A$, we know that $\exists i \in \mathbb{N}$ such that $f(i) = x$.
> 	2. Now consider input $n = 2i$.
> 	3. Since $h(n) = h(2i) = f(i) = x$.
> 4. Case 2: $x \in B$
> 	1. Then by surjectivity of $g : \mathbb{N} \to B$, we know that $\exists i \in \mathbb{N}$ such that $f(i) = x$.
> 	2. Now consider input $n = 2i + 1$.
> 	3. Since $h(n) = h(2i + 1) = f(i) = x$.
> 5. In all cases, it is shown that $\exists i \in \mathbb{N} [h(i) = x]$.
> 6. By universal generalisation $\forall x \in (A \cup B), \exists i \in \mathbb{N} [h(i) = x]$.
>    
>    


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

![[Images/Cantor.light.png|Center]]


Where all of the $d(j)_{g(i)}$ happens to be a number between 0 and 9. So in particular:

$$
g(i) = \sum_{j \in \mathbb{Z}^+} 10^{-j} d(j)_{g(i)}
$$

So what happens if we took every number on the diagonal and collected them. Then did a bunch of changing? Like so:

![[Images/Cantor-changed.light.png|Center]]

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

Now, what is the cardinality of $\mathcal{Q}$? I'm actually going to show you that $\lvert [0, 1] \rvert \leq \lvert \mathcal{Q} \rvert$. How do we do this? Here's the trick:  We will construct an injection $c : [0, 1] \to \mathcal{Q}$. And we know this interval itself [[#Cantor's Diagonalisation Argument|has cardinality strictly larger than any countably infinite set]]. Thanks Dr. Georg Cantor.

The overall idea is the following:
1. Take $x \in [0, 1]$.
2. Write $x$ out in binary expansion as $\sum_{i \in \mathbb{Z}^+} x_i \cdot 2^{-i}$, where $x_j \in \{0, 1\}$, for all $i \in \mathbb{Z}^+$.
3. Then when $x_i$ is $1$, we map it to $true$, and $x_i$ is $0$, we map it to $false$.
4. Then this sequence of $true$ and $false$ defines a problem $Q$.
5. The last thing to do is to map the sequence back into $\mathbb{I}$.

![[Images/problem-to-decimal.light.png]]

Pseudo-formally: We want to create a mapping $c : [0, 1] \to Q$ where $c$ is injective.
1. Since $\mathbb{I}$ is a countably infinite set, there exists a bijective function $f : \mathbb{Z}^+ \to \mathbb{I}$.
2. Let $x \in [0, 1]$. Then $x$ can be written as $\sum_{i \in \mathbb{Z}^+} x_i \cdot 2^{-i}$, where $x_i \in \{0, 1\}$, for all $i \in \mathbb{Z}^+$.
	1. Alternatively, one can say there is a corresponding function $b : \mathbb{Z}^+ \to \{0, 1\}$ for which $x = \sum_{i = 1}^{\infty} b(i) \cdot 2^{-i}$. 
3. Create a mapping $d : \mathbb{Z}^+ \to \{true, false\}$, where:
	1. $d(i) = true$ if $x_i = 1$;
	2. $d(i) = false$ if $x_i = 0$
	3. Note: $d$ is a bijection.
4. Since $\lvert \mathbb{Z}^+ \rvert = |\mathbb{Z}| = |\mathbb{I}|$, exists a bijective function $f: \mathbb{I} \to \mathbb{Z}^+$.
5. We now move to create a problem $Q$. This means $Q$ has to be a function from $\mathbb{I} \to \{true, false\}$.
	1. On input $I \in \mathbb{I}$, note that since $f$ is a function, $f$ maps $I$ to a unique value $n \in \mathbb{Z}^+$. 
	2. Since $f : \mathbb{I} \to \mathbb{Z}^+$, $b : \mathbb{Z}^+ \to \{0, 1\}$, $d : \{0, 1\} \to \{true, false\}$ are functions, then:
	3. $(d \circ b \circ f) : \mathbb{I} \to \{true, false\}$ is a well-defined function.
	4. Define $Q = (d \circ b \circ f)$.
	   Note, the choice of $b$ depends on $x$. The choice of $f$ and $d$ are fixed beforehand. Furthermore, $f$ and $d$ are bijections.
6. Then $Q$ is a function $\mathbb{I} \to \{true, false\}$.
7. Thus, for an arbitrary $x \in [0, 1]$, we have constructed a function $Q_x$. Thus by universal generalisation, $\forall x\in [0, 1]$, $\exists Q [Q : \mathbb{I} \to \{true, false\}]$. In other words, on any input $x$, there exists an output $Q$.
8. Furthermore, let $x_1, x_2 \in [0, 1]$ be arbitrarily chosen such that $Q_1$ was created (using lines 1-5) using $x_1$, and $Q_2$ was created using $x_2$. And further assume $Q_1 \neq Q_2$.
9. Then, there exists $I \in \mathbb{I}$ such that $Q_1(I) \neq Q_2(I)$. (They differ when given input $I$.)
10.  Since $Q_1(I) = d(b_1(f(I)))$ and $Q_2(I) = d(b_2(f(I)))$, and $d$ is a bijection:
11. $Q_1(I) \neq Q_2(I)$ imply that $d(b_1(f(I))) \neq d(b_2(f(I)))$.
12. And because $d$ is a bijection, $b_1(f(I)) \neq b_2(f(I))$.
13. Therefore $x_1$ and $x_2$ differ in their binary representation at location $f(I) \in \mathbb{Z}^+$.
14. Therefore $Q_1 \neq Q_2 \to x_1 \neq x_2$.
15. By contrapositivity: $x_1 = x_2 \to Q_1 = Q_2$.
16. Therefore $\forall x_1, x_2 [x_1 = x_2 \to Q_1 = Q_2]$, where $Q_1$ is created from $x_1$ and $Q_2$ is created from $x_2$
17. Therefore define $c : [0, 1] \to \mathcal{Q}$ to be a mapping where on input $x$, it outputs $Q : \mathbb{I} \to \{true, false\}$ by using lines 5.1 through 5.4.
18. By lines 7, and 15, $c$ is a well-defined function since on every input, it has an output. Furthermore, on inputs $x_1 = x_2$, $c(x_1) = c(x_2)$.

We're not done! We need to argue that $c$ is injective.

Pseudo-proof:
1. Let $x_1, x_2 \in [0, 1]$. Assume that $c(x_1) = c(x_2)$.
2. Let $c(x_1) = Q_1$, let $c(x_2) = Q_2$.
3. Since $Q_1 = Q_2$, $\forall I \in\mathbb{I}[Q_1(I) = Q_2(I)]$.
4. Now, $\forall I \in\mathbb{I}[d(b_1(f(I))) = d(b_2(f(I)))]$.
5. Since $d$ bijective, this means: $\forall I \in\mathbb{I}[b_1(f(I)) = b_2(f(I))]$.
6. Assume for the sake of contradiction that $x_1 \neq x_2$. Then $\exists n \in \mathbb{Z}^+$ for which $b_1(n) \neq b_2(n)$.
	1. Since $f$ is surjective, exists $I_n \in \mathbb{I}$ for which $f(I_n) = n$. Now: $b_1(f(I_n)) = b_1(n) \neq b_2(n) = b_2(f(I_n))$.
	2. Contradiction, since $\forall I \in\mathbb{I}[b_1(f(I)) = b_2(f(I))]$.
7. Therefore, $x_1 \neq x_2$. \[Negation of assumption on line 6.]

This means that $c$ is an injection from $[0, 1] \to \mathcal{Q}$. So now we say "the cardinality of $[0, 1]$ is less than or equals to the cardinality of $\mathcal{Q}$, the set of all computer problems".

Ok what about the cardinality of the set of all computer problems? What is $\lvert \mathbb{P} \rvert$? I'm going to appeal to your intuition here. Computer programs have to be finite in length. Regardless of whether you're writing Source, or Python, or Javascript. The program description must always be finite in length. And for the sake of simplicity, let's say the program code can only take on 256 possible characters. [^1] So a code is a finite sequence of $\mathbb{Z}_{256}$, effectively. As a shorthand, $(\mathbb{Z}_{256})^t$ is basically a $t$ length sequence where each element in the sequence is one of $256$ possible values. Now $\mathbb{P} = \bigcup_{t \in \mathbb{Z}^+} (\mathbb{Z}_{256})^t$. By the way, $\bigcup_{t \in \mathbb{Z}^+} (\mathbb{Z}_{256})^t$ is **countably infinite**. So, $\mathbb{P}$ is countably infinite.

So that is to say:
$$
\lvert \mathbb{P} \rvert = \aleph_0 \lneq \aleph_1 \leq \big\lvert [0, 1] \big\rvert \leq \lvert \mathcal{Q} \rvert
$$

So what does this show? This shows there are no possible bijections between the set of computer programs $\mathbb{P}$, and the set of computer problems $\mathcal{Q}$. What does no bijection mean? We know that since $\lvert \mathbb{P} \rvert \lneq \lvert \mathcal{Q} \rvert$, there is an injection from $\mathbb{P}$ to $\mathcal{Q}$ but no surjection (or else a bijection would exist). Intuitively, this means there are **way more problems than there are solutions**. 

You might think: That's it? We only know they exist? Can we at least solve everything that we care about? Well...
## The Halting Problem

Consider the following problem:

> [!Example]
> Let $P$ be a computer program, and $\langle P' \rangle$ denote the code of computer program $P'$. For simplicity, we will think of computer programs as functions that take any string as input, and outputs $\{true, false\}$, **if it terminates**.
> 
> The **halting problem**, when set of inputs $\mathbb{I}$ is the set of all computer program code and input pairs $\langle P \rangle$. I.e. $\mathbb{I} = \{ (\langle P \rangle, I) : P\text{ is a computer program}, I\text{ is input for the program} \}$. **On input $(\langle P \rangle, I)$**, decide if $P(I)$ terminates. 

Now you might think. Oh this is easy. Just take $\langle P \rangle$, run the program $P(I)$, then when it outputs something, just say $true$, because it halted. But what if it doesn't halt? How would we know?

The answer is that we actually can't solve the **halting problem** in general.

> [!Theorem]
> The halting problem is **undecidable/uncomputable**.

The idea is really similar to [[#Cantor's Diagonalisation Argument]], we will assume for the sake of contradiction there is a computer program $D$ that solves this problem, we will make use of the fact that computer programs have a bijection to $\mathbb{N}$ and therefore **it is a fact** that **every program** can be enumerated in a sequence. And then assuming the existence of $D$ then we will construct a computer program $\hat{D}$ that is not enumerated in this sequence. This gives us a contraction, leading us to conclude that $D$ must not be able to exist.

Again, we will just use this very useful lemma:

> [!Lemma]
> The set of all computer programs $P$ has a bijection to $\mathbb{N}$.

Intuitively, this is true, because every program code $\langle P \rangle$ can be written as a finite sequence of characters from a finite set of characters (like ASCII).

Proof by contradiction
1. Assume that exists a program $D$ that solves the **halting problem**. 
2. This means it always terminates, and always outputs a correct answer when given input $\langle P \rangle$ and $I$, it says $true$ if 
3. Create a new program $\hat{D}$ in the following way:
	1. On input $(\langle P \rangle, I)$, output $true$ if $D(\langle P\rangle)$ outputs $false$.
	2. Otherwise, output $false$.
4. ****

---
[^1]: Yes I know unicode is a thing. I'm too lazy to mention how many codepoints there are etc.

[^assumption-nat]: In this part we're basically assume $\mathbb{N}$ exists already. There are axioms that we can use where $\mathbb{N}$ does not exist, and we can construct it.
