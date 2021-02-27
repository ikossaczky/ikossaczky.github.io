---
layout: post
title: "Inverse sensor model"
date: 2021-02-27 22:00:00 +0100
categories: mathematics
---
These notes on inverse sensor model are from large portion extracted from [this lecture](https://www.youtube.com/watch?v=v-Rm9TUG9LA) (which I recommend to watch) of professor Cyrill Stachniss, but I included also some of my own remarks, mainly concerning the plausibility of the model assumptions.

Inverse sensor  model is a model for occupancy grid mapping based on the following equation :

$$ 
P(m=M|z_{1:t}, x_{1:t}) = \prod_i P(m_i= M_i|z_{1:t}, x_{1:t})\tag{1}\label{eq:ism}
$$

- $$m$$: map (random vector) consisting of cells $$m_i$$ (random variables), with $$m_i$$ binary-distributed. 
- $$M$$: map (vector) with elements $$M_i$$, where $$M_i \in \{0, 1\},  \forall i$$. $$1$$ means the the cell is occupied, whereas $$0$$ means that the cell is free.
- $$z_{1:t}$$ represents $$t$$ measurements performed with a sensor
- $$x_{1:t}$$ represents the positions of the sensor corresponding to each of the measurements.

### Model assumptions

1.) Each cell is either occupied or free

2.) Cell does not change its state (static world assumption, violated in case of moving objects)

3.) The probability of each cell being being occupied is independent of the states of all other cells:

  $$
  P(m=M) = \prod_i P(m_i=M_i)
  $$ 

  (violated in real world, as occupied cells are often parts of larger structures).

4.) **Most important & most problematic assumption**: the equation \eqref{eq:ism} itself: *Given a set of measurements, the state of cell $$i$$ is still independent of the states of all other cells.* This assumption is even stronger than the assumption 3: if we think about two random variables $$m_1$$, $$m_2$$ representing two coin tosses ($$m_i=1$$ if the coin toss ends up with head and $$m_i=0$$ if not), these are clearly independent random variables, and therefore fulfilling assumption 3. However, given a measurement $$z$$ telling us, how many tosses resulted in *head*, these two random variables are not [conditionally  independent](https://en.wikipedia.org/wiki/Conditional_independence) anymore:

$$ P(m_1 =1, m_2=1 |z = 1) = 0 \neq 0.25 = P(m_1 =1|z = 1) P(m_2=1 |z = 1).$$

In the context of occupancy grids, the example above translates into the following situation: assume we have only a single measurement, which very likely indicates exactly one occupied cell. Therefore, if we know that cell $$i$$ is occupied, the probability of cell $$j$$ being also occupied is close to $$0$$. However, if cell $$i$$ is not occupied, then cell $$j$$ is occupied with some positive probability. This means, the random variable $$m_j$$ expressing if cell $$j$$ is being occupied is not conditionally independent of the variable $$m_i$$ given this single measurement $$z$$.

This assumption is more flawed then the previous assumptions: in a gridded world, with randomly sampled occupancy of cells and without moving objects, assumptions 1.-3. would be fulfilled. However, it is difficult to imagine a world and a measurement setup that would fulfill the assumption 4.

### Algorithm derivation
In the following derivation we use the following simplified notation:
- $$P(m_i) \equiv P(m_i=1)$$: probability that the cell $$i$$ is occupied
- $$P(\neg m_i) \equiv P(m_i=0) = 1 - P(m_i)$$: probability that the cell $$i$$ is free

Based on equation \eqref{eq:ism} resulting from assumption 4, we can examine probability of each cell being occupied separately:

$$
\begin{align}
P(m_i|z_{1:t}, x_{1:t}) &= \frac{P(z_t| m_i, z_{1:t-1}, x_{1:t}) P(m_i|z_{1:t-1}, x_{1:t})}{P(z_t|z_{1:t-1}, x_{1:t})} \\ \\
&= \frac{P(z_t| m_i, x_{t}) P(m_i|z_{1:t-1}, x_{1:t-1})}{P(z_t|z_{1:t-1}, x_{1:t})} \tag{2}\label{eq:der1}
\end{align}
$$

where we first used Bayes rule and then Markov assumption, combined with the fact that $$P(m_i \vert z_{1:t-1}, x_{1:t})=P(m_i\vert z_{1:t-1}, x_{1:t-1})$$, i.e. probability of the cell being occupied is independent of the position of the sensor, if no measurement for this sensor position is provided.  
Using Bayes rule to expand  $$P(z_t \vert m_i, x_{t})$$,

$$
P(z_t| m_i, x_{t}) = \frac{P(m_i|z_t, x_t)P(z_t|x_{t})}{P(m_i|x_t)}
$$

we can write \eqref{eq:der1} as 

$$
\begin{align}
&\frac{P(m_i|z_t, x_t) P(z_t|x_{t}) P(m_i|z_{1:t-1}, x_{1:t-1})}{P(m_i|x_t) P(z_t|z_{1:t-1}, x_{1:t})}\\ \\
=&\frac{P(m_i|z_t, x_t) P(z_t|x_{t}) P(m_i|z_{1:t-1}, x_{1:t-1})}{P(m_i) P(z_t|z_{1:t-1}, x_{1:t})} \tag{3}\label{eq:prob_occup}
\end{align}
$$

where we used the fact that $$P(m_i \vert x_t) = P(m_i)$$  (again, probability of the cell being occupied is independent of the position of the sensor, if no measurement is provided).

Performing the same steps for the conditional probability that the cell is free, we end up with:

$$
P(\neg m_i|z_{1:t}, x_{1:t}) = \frac{P(\neg m_i|z_t, x_t) P(z_t|x_{t}) P(\neg m_i|z_{1:t-1}, x_{1:t-1})}{P(\neg m_i) P(z_t|z_{1:t-1}, x_{1:t})} \tag{4}\label{eq:prob_free}
$$

Now by dividing \eqref{eq:prob_occup} by \eqref{eq:prob_free} we get the formula for [odds](https://en.wikipedia.org/wiki/Odds) that the cell is occupied conditioned by the measurements $$z_{1:t}$$, taken at sensor positions $$x_{1:t}$$:

$$
\begin{align}
odds(m_i|z_{1:t}, x_{1:t}) &= \frac{P(m_i|z_{1:t}, x_{1:t})}{1-P(m_i|z_{1:t}, x_{1:t})} = \frac{P(m_i|z_{1:t}, x_{1:t})}{P(\neg m_i|z_{1:t}, x_{1:t}) } \\ \\
&= \frac{P(m_i|z_t, x_t) P(m_i|z_{1:t-1}, x_{1:t-1}) P(\neg m_i)}{P(\neg m_i|z_t, x_t) P(\neg m_i|z_{1:t-1}, x_{1:t-1}) P(m_i)}\\ \\
&= \frac{odds(m_i|z_t, x_t) \cdot odds(m_i|z_{1:t-1}, x_{1:t-1})}{odds(m_i)} \tag{5}\label{eq:odds}
\end{align}
$$

- Here the term $$odds(m_i \vert z_t, x_t)$$ can be modeled by modeling $$P(m_i \vert z_t, x_t)$$, the probability of the cell $$m_i$$ being occupied, given a single measurement $$z_i$$ (containing for example position of the measured signal, that should be correlated with the position of the occupied cell) for the sensor positioned at $$x_i$$. 
- The term $$odds(m_i \vert z_{1:t-1}, x_{1:t-1})$$ is recurrent term and is known from the previous iteration of the algorithm.
- The term $$odds(m_i)$$ denotes prior odds of the cell being occupied (without any measurements taken). 

The formula \eqref{eq:odds} can already be used to compute the odds conditioned by all measurements in recurrent manner. However, in order to get more convenient and numerically more stable algorithm, the equation can be converted into log-odds (aka [logit](https://en.wikipedia.org/wiki/Logit)) representation by applying logarithm to both left and right hand side of the equation:

$$
\begin{align}
\log odds(m_i|z_{1:t}, x_{1:t}) &= \log odds(m_i|z_t, x_t) + \log odds(m_i|z_{1:t-1}, x_{1:t-1}) - \log odds(m_i) \\ \\
&= \sum_{\tau=1}^t\log odds(m_i|z_\tau, x_\tau) - t \cdot \log odds(m_i).
\end{align}
$$






