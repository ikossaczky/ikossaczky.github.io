---
layout: post
title: "From Likelihood to Bayesian Parameter Estimation"
date: 2024-02-13 20:00:00 +0100
categories: mathematics
---

*Bayesian parameter estimation traditionally arises from applying Bayes rule to estimate the distribution of unobservable  data distribution parameters, conditioned by the observed data. In this post, we show that this technique, as well as the need for a prior distribution, also emerges simply from trying to transform the parameter likelihood into a probability density function.*

Let us consider a probabilistic distribution of random variable $$y$$ expressed as probability density function (PDF) $$p$$ parametrized by some parameters $$\theta$$:

$$ p(y \vert \theta) \tag{1}\label{eq:pdf}$$

Clearly, for a fixed $$\theta$$, we get a distribution of the data $$y$$. On the other hand, given a measurement of $$y$$, can we see function \eqref{eq:pdf} as a distribution of $$\theta$$ ?

The answer is no, as we can not guarantee, that for a fixed $$y$$ the function $$p(y \vert \theta)$$ integrates over $$\theta$$ to $$1$$. Therefore, in case of fixed $$y$$, function $$ \theta \rightarrow p(y \vert \theta)$$ is not a probability density function (PDF), but a **likelihood function**.

This is also valuable, as we can now compare the goodness of fit of different parametrizations $$\theta$$ by comparing their  values of the likelihood function. But, what if we are interested in having a probabilistic distribution of $$\theta$$, given a fixed $$y$$,  which we can conveniently denote as $$p(\theta \vert y)$$? The simplest solution, how to get a proper PDF from a likelihood function which does not integrate over $$\theta$$ to $$1$$, is to normalize it: 

$$ p(\theta \vert y) = \frac{p(y \vert \theta)}{\int p(y \vert \theta) d \theta} \tag{2}\label{eq:cond_pdf1}$$

Now, as the rescaled likelihood $$p(\theta \vert y)$$ defined in \eqref{eq:cond_pdf1} has positive values and integrates to $$1$$, we can see it as a probability density function. But there is still a problem: what if the integral in the denominator is equal to infinity? Then the whole expression \eqref{eq:cond_pdf1} does not make any sense. In this case, it is not possible to rescale the likelihood function to acquire a proper probability density function. Therefore, to solve the problem of infinite integral, we need at first to attenuate the likelihood function $$ \theta \rightarrow p(y \vert \theta)$$ somehow, to make its integral finite. We can express this attenuation of $$p(y \vert \theta)$$ as a multiplication with some function $$p(\theta)$$:

$$p(y \vert \theta) p(\theta) \tag{3}\label{eq:attenuated}$$

For now, let us state that the function $$p(\theta)$$ should be positive, and chosen in such manner, that \eqref{eq:attenuated} does not integrate to infinity. Without loss of generality, we can choose $$p(\theta)$$ that satisfies the properties of a probability density function. Substituting the attenuated likelihood \eqref{eq:attenuated}, instead of the original one into \eqref{eq:cond_pdf1}, we arrive at the following expression for the PDF $$p(\theta \vert y)$$:

$$ p(\theta \vert y) = \frac{p(y \vert \theta) p(\theta)}{\int p(y \vert \theta) p(\theta) d \theta} \tag{4}\label{eq:cond_pdf2}$$

Look at the equation \eqref{eq:cond_pdf2}: it is the standard equation for Bayesian parameter estimation, and we got it just by trying to get reasonable PDF $$p(\theta \vert y)$$ from a likelihood function of a probabilistic model $$p(y \vert \theta)$$ and a known measurement of $$y$$. Let's recapitulate the Bayesian interpretation of the expressions composing equation \eqref{eq:cond_pdf2}:

- $$p(y \vert \theta)$$ is a probability of $$y$$, for a given $$\theta$$. As a function of $$\theta$$, it is likelihood of $$\theta$$, given some measured $$y$$.

- $$p(\theta)$$, which we used only out of necessity, to attenuate possibly infinite integral of likelihood function, is in Bayesian setting interpreted as our **prior distribution** of (a.k.a. prior belief about)  $$\theta$$. 

- $${\int p(y \vert \theta) p(\theta) d \theta} = p(y)$$ is the probability of $$y$$, taking into account all possible values of $$\theta$$.

- $$p(\theta \vert y)$$ is under Bayesian interpretation called **posterior distribution** of $$\theta$$. In contrast to the prior distribution, it takes into account also the measurement of $$y$$.


## Notes on the need for prior distribution 

The necessity of prior distribution is often seen as a weak point of Bayesian approach. How should we formulate our prior beliefs (if we even have any), as a distribution? There are obviously many ways to do that: conjugate, informative, non-informative, and Jeffreys priors are just some of them, all having their advantages and disadvantages. In this post we have shown, that the necessity of choosing a prior arises also without the goal to formulate some beliefs about the distribution of $$\theta$$. The prior can be seen as a necessary attenuation factor needed to make rescaling of the likelihood function to PDF possible.

However, what if the integral $$\int p(y \vert \theta) d \theta$$ is finite? In that case, we do not need any attenuation (resp. prior), and we can use \eqref{eq:cond_pdf1} to rescale the likelihood into PDF directly. How does this scenario fits the Bayesian view?
In Bayesian view, this scenario is equivalent to probability distribution with a constant probability density over the whole definition domain of $$\theta$$. This can be be interpreted as considering every $$\theta$$ as equally likely, or having no  prior belief about the distribution of $$\theta$$ at all. Indeed, if we substitute a constant prior density

$$p(\theta) \propto c \tag{5}\label{eq:const_prior}$$
 
into \eqref{eq:cond_pdf2}, we get the equation \eqref{eq:cond_pdf1}. The problem with constant prior \eqref{eq:const_prior} is, that for $$\theta$$ defined on an infinite domain, there is no proper probability density satisfying \eqref{eq:const_prior}. However, by pretending there is such a density and substituting "$$c$$" into \eqref{eq:cond_pdf2}, the constant $$c$$ still cancels out and we arrive at the formula \eqref{eq:cond_pdf1}. This "pretended probability density" is called **improper prior**.

Therefore, in any case of finite integral of likelihood, we can use \eqref{eq:cond_pdf1} as a formula corresponding to a constant prior (for $$\theta$$ on a finite domain) or to an improper prior (for $$\theta$$ on an infinite domain).

Of course, if the integral of likelihood is infinite, a constant improper prior will not work for scaling the likelihood into PDF. A posterior "distribution" (which does not satisfy properties of a probability distribution) proportional to numerator of \eqref{eq:cond_pdf1} or \eqref{eq:cond_pdf2} which cannot be normalized because of the infinite denominator, is called **improper posterior**. In this case, the question arises, if any proper prior probability density $$p(\theta)$$ can be used as attenuation factor to make the integral in denominator finite and deliver a proper posterior distribution.

As it turns out, not always:
- Follow this Stack Overflow [answer](https://stats.stackexchange.com/a/211559/269540) on the result $$\text{proper prior} \Rightarrow \text{proper posterior almost everywhere}$$, which means, that the set of possible measurements for $$y$$, which would lead to an improper posterior in case of proper prior, has Lebesgue measure $$0$$.

- See this Stack Overflow [answer](https://stats.stackexchange.com/a/89150/269540) for an example of proper prior leading to an improper posterior. In a more human language, the core of the argument made in the answer post can be rephrased by the following example: let us assume a normally distributed $$y \sim N(0, \theta)$$, with known mean $$0$$ and unknown variance $$\theta$$. Now, we perform one measurement, and by chance we get measured value $$y_0 = 0$$. So, how probable is this measurement under different values of $$\theta$$? Of course, the smaller is the variance, the more probable it is to get a measurement, which is very close to the mean. As the variance $$\theta$$ approaches 0, the PDF of $$N(0, \theta)$$ evaluated in $$0$$, which is in this case the likelihood, approaches $$\infty$$ leading to $$\int p(y \vert \theta)d \theta = \infty$$. Now, if the proper prior for $$\theta$$ does not attenuate the likelihood close to $$0$$ strong enough, also the integral of the attenuated likelihood (likelihood times prior) $$\int p(y \vert \theta) p(\theta) d \theta$$ will be infinite.


## Multiple measurements case

Till now, we considered a case of inferring distribution of $$\theta$$ given single measurement of $$y$$. What about a case of several measurements of $$y_i$$ of the random variable $$y$$?

At first let us note, that all our previous derivations are not limited to one-dimensional random variable $$y$$ and one dimensional $$\theta$$. Both $$y$$ and $$\theta$$ can be multi-dimensional.

Now, having a set of $$N$$ measurement $$y_0, y_1, \dots y_N$$, of a $$D$$-dimensional random variable $$y$$, we can concatenate these measurements into a single $$N D$$-dimensional random vector $$Y$$, having components $$Y_i = y_i$$. Under the assumption, that these measurements are independent and identically distributed, this random vector has a probability density function 

$$ p(Y \vert \theta) = \prod_{i=1}^N p(Y_i \vert \theta) \tag{6}\label{eq:pdf_nd}$$.

Moreover, we can consider our dataset of measurements $$y_0, y_1, \dots y_N$$ as a single measurement of the random vector $$Y$$ with probability density defined by \eqref{eq:pdf_nd}, and all derivations and conclusions from the previous sections still hold.

