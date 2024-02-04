---
layout: post
title: "Non-Bayesian motivation for Bayesian parameter estimation"
date: 2024-01-29 18:00:00 +0100
categories: mathematics
---

Let us think about a probabilistic distribution of ranodom variable $$y$$ expressed as probability density function (PDF) $$p$$ parametrized by some parameters $$\theta$$:

$$ p(y \vert \theta) \tag{1}\label{eq:pdf}$$

Now clearly, for a fixed $$\theta$$, we get a distribution of the data $$y$$. On the other hand, given a measurement of $$y$$, can we see function \eqref{eq:pdf} as a distribution of $$\theta$$ ?

The answer is no, as nobody guarantees that for fixed $$y$$ the function $$p(y \vert \theta)$$ integrates over $$\theta$$ to $$1$$. Therefore, in case of fixed $$y$$, function $$ \theta \rightarrow p(y \vert \theta)$$ is not a probability density function, but a **likelyhood function**.

This is also valuable, as we can now compare the goodness of fit of different parametrizations $$\theta$$ by comparing these likelyhood functions. But what if we really want have a probabilistic distribution of $$\theta$$ given a fixed $$y$$,  which we can conveniently denote as $$p(\theta \vert y)$$? The simplest solution, how to get a proper PDF from the likelihood function which does not integrate over $$\theta$$ to $$1$$, is to normalize it: 

$$ p(\theta \vert y) = \frac{p(y \vert \theta)}{\int p(y \vert \theta) d \theta} \tag{2}\label{eq:cond_pdf1}$$

Now, as the rescaled likelyhood $$p(\theta \vert y)$$ defined in \eqref{eq:cond_pdf1} has positive values and integrates to $$1$$, we can see it as a probability density function. But there is still a problem: what if the integral in the denominator is equall to infinity? Then the whole expression \eqref{eq:cond_pdf1} does not make any sense. In this case, it is not possible to rescale the likelyhood function to acquire a proper probability density function. Therefore, to solve the problem of infinite integral we need at first to attenuate the function $$ \theta \rightarrow p(y \vert \theta)$$ somehow, to make the integral finite. We can express this attenuation of $$p(y \vert \theta)$$ as multiplication  with some function $$p(\theta)$$:

$$p(y \vert \theta) p(\theta) \tag{3}\label{eq:attenuated}$$

For now, let us state that the function p($$\theta$$) should be positive, an chosen in such manner that \eqref{eq:attenuated} does not integrate to infinity. Without loss of generality, we can choose $$p(\theta)$$ that satisfies the properties of a probability density function. Using the attenuated likelihood \eqref{eq:attenuated}, instead of the original one in \eqref{eq:cond_pdf1}, we arrive at the following expression for the PDF $$p(\theta \vert y)$$:

$$ p(\theta \vert y) = \frac{p(y \vert \theta) p(\theta)}{\int p(y \vert \theta) p(\theta) d \theta} \tag{4}\label{eq:cond_pdf2}$$

Look at equation \eqref{eq:cond_pdf2}: it is the standard equation for Bayesian parameter estimation, and we got it just by trying to get reasonable PDF $$p(\theta \vert y)$$ from a probabilistic model $$p(y \vert \theta)$$ and a known measurement of$$y$$. Let us now recapitulate the parts of this equation:

- $$p(y \vert \theta)$$ is a probability of $$y$$ for given $$\theta$$. As a function of $$\theta$$, it is likelyhood of $$\theta$$, given some measured $$y$$.
- $$p(\theta)$$ which we used only out of necessity to attenuate possibly infinite integral of likelyhood function, is in Bayesian setting interpreted as our **prior distribution** (a.k.a. prior belief) of $$\theta$$. 
- $${\int p(y \vert \theta) p(\theta) d \theta} = p(y)$$ is probability of $$y$$, taking into account all possible values of $$\theta$$.
- $$p(\theta \vert y)$$ is under Bayesian interpretation called **posterior distribution** of $$\theta$$. In contrast to the prior distribution, it takes into account also the measurement for $$y$$.


### Notes on the prior distribution of $$\theta$$ as attenuation factor

The necessity of prior distribution is often seen as a week point of Bayesian approach. How should we formulate our prior beliefs (if we even have any), as a distribution? There are obviously many ways to do that: conjugate, informative, non-informative, and Jeffreys priors are just some of them, all having their advantages and disadvantages. However, in this post we have shown that the necessity of choosing a prior arises also without the goal to formulate some beliefs about the distribution of $$\theta$$. It can be seen as a necessary attenuation factor needed to make rescaling of the likelyhood function to PDF possible.

However, what if the integral $$\int p(y \vert \theta) d \theta$$ is finite? In that case, we do not need any attenuation (resp. prior), and we can use \eqref{eq:cond_pdf1} to rescale likelihood into PDF directly. How does this scenario fits the Bayesian view?
In Bayesian view, this scenario is equivalent to probability distribution which has a constant probability density over the whole definition domain of $$\theta$$. It means, we consider every $$\theta$$ as equally likely, or we do not have any prior belief about the distribution of $$\theta$$ at all. Indeed, if we substitute a constant prior density

$$p(\theta) \propto c \tag{5}\label{eq:const_prior}$$
 
into \eqref{eq:cond_pdf2}, we arrive at the same result as in \eqref{eq:cond_pdf1}. The problem with constant prior as defined in \eqref{eq:const_prior} is that for $$\theta$$ defined on infinite domain, there is no proper probability density satisfying \eqref{eq:const_prior}. However, by pretending there is such and substituting it into \eqref{eq:cond_pdf2}, the constant $$c$$ still cancels out and we arrive at the formula \eqref{eq:cond_pdf1}. This "pretended probability density" is called **improper prior**.

Therefore, in any case, we can use \eqref{eq:cond_pdf1} as a formula corresponding to a constant prior (for $$\theta$$ on a finite domain) or improper prior (for $$\theta$$ on an infinite domain).

Of course if the integral in the denominator of \eqref{eq:cond_pdf2} is infinite, using the improper prior is not possible. A posterior "distribution" (which does not satisfy properties of a probability distribution) proportional to numerator of \eqref{eq:cond_pdf1} or \eqref{eq:cond_pdf2} and cannot be normalized because of the infinite denominator, is called **improper posterior**. In this case, the question arises, if any proper prior probability density $$p(\theta)$$ can be used as attenuation factor to make the integral finite.

As it turns out, not always:
- follow this Stack Overflow [answer](https://stats.stackexchange.com/a/211559/269540) on the result $$\text{proper prior} \Rightarrow \text{proper posterior almost everywhere}$$, which means that the set of possible measurements for $$y$$, which would lead to an improper posterior in case of proper prior has Lebsgue measure $$0$$.
- follow this Stack Overflow [answer](https://stats.stackexchange.com/a/89150/269540) for an example of proper prior leading to improper posterior. In human language, the core of the argument made in the answer post can be rephrased by the following example: let us assume a normally distributed $$y$$, with known mean $$0$$, but unknown variance $$\theta$$: $$y ~ N(0, \theta)$$. Now, we perform one measurement, and by chance we get measured value $$y_0 = 0$$. So, How probable is this measurement under different values of $$\theta$$? Of course, the smaller the variace, the more probable it was to get a meaurement, which is exactly at the mean. As the variance $$\theta$$ approaches 0, the PDF of $$N(0, \theta)$$ evaluated in $$0$$, which is in this case the likelihood, approaches $$\infty$$ leading to $$\int p(y \vert \theta) p(\theta) d \theta = \infty$$. Now, if the proper prior for $$\theta$$ does not attenuate the likelihood close to $$0$$ strong enough, also the integral of the attenuated likelihood (likelihood times prior) will be infinite.

### Case of multiple measurements

Till now we considered a case of infering distribution of $$\theta$$ given single measurement of $$y$$. What about a case of several measurements of $$y$$?

At first let us note, that all our previous derivations are not limited to one-dimensional $$y$$ and one dimensional $$\theta$$. Both $$y$$ and $$\theta$$ can be multi-dimensional.

Now, having a set of $$N$$ measurement $$y_0, y_1, \dots y_N$$, of a $$D$$-dimensional random variable $$y$$, we can concatenate these measurements into a single $$N D$$-dimensional random vector $$Y$$, having components $$Y_i = y_i$$. Under the assumption that these measurements are not only identically distributed, but also independent, this random vector has a probability density function 

$$ p(Y \vert \theta) = \prod_{i=1}^N p(Y_i \vert \theta)$$.

Moreover, we can consider our set of measurements $$y_0, y_1, \dots y_N$$ as a single measurement of random vector $$Y$$, and all the derivations in the previous section are still applicable.

