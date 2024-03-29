---
layout: post
title: "From Maximum Likelihood to minimal Cross-Entropy"
date: 2021-05-23 22:00:00 +0100
categories: ["mathematics", "machine learning"]
---
Although the equivalence of [Maximum Likelihood Estimation](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation) (MLE, widely used in statistics) and [Cross-entropy](https://en.wikipedia.org/wiki/Cross_entropy) minimization (used in deep learning) is loosely sketched in many "gentle introductions" to deep learning on internet, I was struggling to find some, maybe less gentle but clear and rigorous derivation of this equivalence. This blog post should fill this gap.

## Classical MLE: Fitting distribution to data
Let us assume we observed $$n$$ independent identically distributed (IID) data samples $$x_1, x_2,...,x_n$$ from an unknown probability distribution $$p(x)$$. Let us approximate this unknown distribution $$p(x)$$ with an distribution $$q_{\theta}(x)$$ parametrized by parameter vector $$\theta$$. We search for such parameters $$\theta$$ that maximize the likelihood of observing the data that we actually observed:

$$
\begin{align}
\hat{\theta} &= \arg\max_{\theta} \prod_{i=1}^{n} q_{\theta}(x_i) \tag{1}\label{eq:likelihood} \\
&= \arg\max_{\theta} \log (\prod_{i=1}^{n} q_{\theta}(x_i)) \\
&= \arg\max_{\theta} \sum_{i=1}^{n}\log(q_{\theta}(x_i)) \tag{2}\label{eq:log-likelihood} \\
&= \arg\min_{\theta} \frac{1}{n}\sum_{i=1}^{n}\left(-\log(q_{\theta}(x_i))\right) \\
&\xrightarrow{n \rightarrow \infty} \arg\min_{\theta} \mathbb{E}_{p(x)}\left[-\log(q_{\theta}(x_i)\right] \tag{3}\label{eq:cross-entropy}
\end{align} 
$$

Here
- $$ L(\theta\vert x_1,x_2,...x_n) = \prod_{i=1}^{n} q_{\theta}(x_i)$$ used in \eqref{eq:likelihood} is the *likelihood function*

- $$\sum_{i=1}^{n}\log(q_{\theta}(x_i))$$ used in \eqref{eq:log-likelihood} is the *log-likelihood function*

- $$\mathbb{E}_{p(x)}(-\log(q_{\theta}(x_i))$$ in \eqref{eq:cross-entropy} is the definition of cross-entropy between distributions $$p(x)$$ and $$q_{\theta}(x)$$. This limiting value is a consequence of the [Law of large numbers](https://en.wikipedia.org/wiki/Law_of_large_numbers) for $$n\rightarrow\infty$$.


## Conditional MLE: Fitting a regression model
In this case we observed sample pairs $$(x_1, y_1), (x_2, y_2),...,(x_n, y_n)$$ from unknown probability distribution $$p(x,y)$$. We are interested on how the random variable $$y$$ depends on $$x$$, that means we would like to know the unknown conditional distribution $$p(y \vert x)$$. We approximate this conditional distribution with a modelled distribution $$q_{\theta}(y | x)$$. We search for parameters $$\theta$$ that maximize the likelihood of observing $$y_1, y_2,...,y_n$$, given that $$x_1, x_2,...,x_n$$ was observed:

$$
\begin{align}
\hat{\theta} &= \arg\max_{\theta}  \prod_{i=1}^{n} q_{\theta}(y_i|x_i) \tag{4}\label{eq:cond-likelihood} \\
&= \arg\max_{\theta}  \sum_{i=1}^{n} \log q_{\theta}(y_i|x_i) \tag{5}\label{eq:cond-log-likelihood}\\
&= \arg\min_{\theta} \frac{1}{n} \sum_{i=1}^{n} - \log q_{\theta}(y_i|x_i) \tag{6}\label{eq:dl-objective}\\
&= \arg\min_{\theta} \frac{1}{n} \sum_{i=1}^{n} - \log q_{\theta}(y_i|x_i) - \log p(x_i) \tag{7}\label{eq:op1}\\
&= \arg\min_{\theta} \frac{1}{n} \sum_{i=1}^{n} - \log q_{\theta}(y_i|x_i) - \log q_{\theta}(x_i) \tag{8}\label{eq:op2}\\
&= \arg\min_{\theta} \frac{1}{n} \sum_{i=1}^{n} - \log \left( q_{\theta}(y_i|x_i)q_{\theta}(x_i) \right) \\
&= \arg\min_{\theta} \frac{1}{n} \sum_{i=1}^{n} - \log  q_{\theta}(y_i, x_i) \\
 &\xrightarrow{n \rightarrow \infty} \arg\min_{\theta} \mathbb{E}_{p(x,y)}\left[ -\log q_{\theta}(y,x) \right] \tag{9}\label{eq:cross-entropy2}
 \end{align} 
$$

Explanation of this derivation:
- $$ L(\theta\vert x_i, y_i, i=1,2,...,n) = \prod_{i=1}^{n} q_{\theta}(y_i\vert x_i)$$ used in \eqref{eq:cond-likelihood} is the *conditional likelihood function*.

- $$\sum_{i=1}^{n} \log q_{\theta}(y_i\vert x_i)$$ used in \eqref{eq:cond-log-likelihood} is the *conditional log-likelihood function*.

- the expression $$\frac{1}{n} \sum_{i=1}^{n} - \log q_{\theta}(y_i\vert x_i)$$ in \eqref{eq:dl-objective} is a classical Deep Learning objective (loss) function, typically denoted as *cross-entropy*. To be more precise, it should rather be called *approximation* of something like *conditional cross-entropy*, however this term *conditional cross-entropy* is not really established.

- In \eqref{eq:op1}, we subtract logarithm of the true unknown probability (resp. density) $$p(x_i)$$. We can do this, as $$p(x_i)$$ is not dependent on $$\theta$$ and thus not change the $$\arg\min$$.

- In \eqref{eq:op2}, we artificially define the "modelled" distribution of $$x_i$$ as the true unknown distribution: $$q_{\theta}(x_i) \equiv p(x_i)$$. We can do this, as this artificial generalization of the definition of $$q_{\theta}$$ has no impact on our modelled conditional distribution $$q(y\vert x)$$.

- Finally, the term $$\mathbb{E}_{p(x,y)}\left[ -\log q_{\theta}(y,x) \right]$$ minimized in \eqref{eq:cross-entropy2} is again the classical definition of cross entropy. Again, this limiting value is a consequence of the Law of large numbers for $$n\rightarrow\infty$$.

## Special cases
### Classification model
Let $$M_{\theta}: x \rightarrow (p_1, p_2, ..., p_k), p_i\in [0,1] \text{ for }i=1,2,...k, \sum_{i=1}^k p_i = 1$$ be a classification model (e.g. neural network with softmax activation in the last layer) parametrized by a set of parameters $$\theta$$, outputing conditional discrete probability distribution of a random variable $$y$$ over the classes $$1,2,...,k$$. 

Then $$q_{\theta}(y=y_i\vert x_i)$$ can be defined as $$p_i = e_{y_i}^T M_{\theta}(x_i)$$ (where $$e_{y_i}$$ is a $$k$$-element vector with 1 at $$y_i$$-th position and 0 elsewhere) and \eqref{eq:dl-objective} can be written as 

$$
\arg\min_{\theta} \frac{1}{n} \sum_{i=1}^{n} - \log q_{\theta}(y_i|x_i) = \arg\min_{\theta} \frac{1}{n} \sum_{i=1}^{n} - \log e_{y_i}^T M_{\theta}(x_i)
$$

which is the well-known categorical cross entropy loss function.
### Regression model

Let us have a model (e.g. neural network) $$M_{\theta}(x): x \rightarrow \hat y \in \mathbb{R}^k = Y$$. This model does not output probability distribution over $$Y$$ as in the previous case, only a single prediction (vector) $$\hat y$$. Let us model the implied predicted conditional distribution on $$Y$$ as a normal distribution $$N(\hat y, \sigma^2 I) = N(M_{\theta}(x), \sigma^2 I)$$ where $$\sigma^2 I$$ is a fixed diagonal variance matrix.  

It follows that

$$
\log q_{\theta}(y\vert x) = \log f_{N(M_{\theta}(x), \sigma^2 I)}(y) = -\log\sqrt{(2 \pi)^k \sigma^{2k}} - \frac{1}{2 \sigma^2}||y-M_{\theta}(x)||_2^2
$$

and \eqref{eq:dl-objective} can be written as

$$
\arg\min_{\theta} \frac{1}{n} \sum_{i=1}^{n} - \log q_{\theta}(y_i|x_i) = \arg\min_{\theta} \frac{1}{n} \sum_{i=1}^{n} ||y_i-M_{\theta}(x_i)||_2^2
$$

which is the $$L_2$$-loss function (aka MSE) widely used in regresion models. Note that we assumed simple and fixed variance matrix $$\sigma^2 I$$. However beside predicting mean we could also predict the variance: the paper [Multi-Task Learning Using Uncertainty to Weigh Losses for Scene Geometry and Semantics](https://arxiv.org/abs/1705.07115) is doing exactly this.

