---
layout: posts
title: Monte Hall problem with Baysian inference
usemathjax: true
published: true
---

One simple way to think about the Monte Hall problem is to use Baye's rule.
For the sake of completeness, here is the problem statement:

_Suppose you're on a game show, and you're given the choice of three doors: Behind one door is a car; behind the others, goats. You pick a door, say No. 1, and the host, who knows what's behind the doors, opens another door, say No. 3, which has a goat. He then says to you, "Do you want to pick door No. 2?" Is it to your advantage to switch your choice?_

Let $$X_2$$ be the event that the car is behind door 2, $$Y_1$$ be the event that you pick door 1, and $$H_3$$ be the event that the host opens door 3.
The mental gymnastics is now the compare $$P(X_2)$$ with $$P(X_2|Y_1,H_3)$$.
Well, we can use Baye's rule to compute this:

$$
\begin{align}
P(X_2\mid Y_1,H_3) = \frac{P(H_3\mid X_2,Y_1)P(X_2\mid Y_1)}{P(H_3\mid Y_1)}
\end{align}
$$

Now $$P(X_2\mid Y_1) = P(X_2) = \frac{1}{3}$$, since the car is equally likely to be behind any of the doors and is independent of your choice.
Second, $$P(H_3 \mid X_2, Y_1) = 1$$ because if the car is behind door 2 and door 1 is chosen by you, the host must open door 3.
Finally, $$P(H_3 \mid Y_1) = \frac{1}{2}$$ because the host can open either door 2 or door 3 with equal probability if the knowledge of the car's location is not taken into account.
So it is clear that 

$$
P(X_2\mid Y_1,H_3) = \frac{1\cdot 1/3}{1/2} = \frac{2}{3},
$$

which implies that switching is the better option.
