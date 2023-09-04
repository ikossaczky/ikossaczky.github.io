---
layout: post
title: "Bayesian approaches to blackbox optimization"
date: 2023-09-01 22:00:00 +0100
categories: mathematics
---
Typical example of a black box function optimization problem is hyperparameter optimizations. We have an ML model and we want to find hyperparameters represented as a vector $$x$$ leading to best possible performance:

$$ y = f(x) $$

Here, $$y$$ is the performance and $$f$$ is the blackbox function from model hyperparameters to performance.

The simplest way to do this is to sample the hyperparameter vectors on a grid or randomly from some reasonable distribution and and compute the performance for each of them. However, this is typically expensive, as computing performance involves both retraining and reevaluating the ML model.

Therefore, an obvious idea is to use some sampled points to create an approximation of the blackbox function (called surrogate model in literature), and optimize this approximation. One of the simplest ideas how to create such an approximation would be for example to fit a polynomial to the sampled points. This has however some problems:
- First order approximation has optimum in one of the sampled points, so the overall method is equivalent to random search.
- The number of points we need for fitting second order (quadratic) polynomial is quadratic with the number of hyperparameters, which can be easily too much. Moreover, such approximation has only one single non-boundary extrema, which might not even be the one we are looking for (min instead of max), and given the typically multimodal nature of the blackbox function, this is probably not a good approximation anyway
- higher order polynomials are even more expensive (while still not being necesarilly great approximations of the blackbox function)

Given this problems with standard numerical approximations it becomes clear: we often need to deal with insufficient number of sampled points. And this insufficient number of points means uncertainty. Which we need to take into account in our models, as well as during deciding where to sample next point for evaluation. So this is the reason for using probabilistic bayesian models.

In this posts I'll not give detailed derivation of these models. There are other great blogs and papers doing this,  check the links below. I will rather concentrate on the intuition and motivation behind two widely used Bayesian approaches: Sequential model-based optimization (SMBO) and Tree-structured Parzen estimator (TPE).

## Sequential model-based optimization
Resources for technical details + derivations:
- [Wikipedia: Gaussian process)](https://en.wikipedia.org/wiki/Gaussian_process) for definition and properties of Gaussian processes
- [krasserm.github.io: Blog on Gaussian processes](https://krasserm.github.io/2018/03/19/gaussian-processes/): Gaussian processes intro + how to fit these to measured data
- [krasserm.github.io: Blog on Bayesian optimization](https://krasserm.github.io/2018/03/21/bayesian-optimization/): Bayesian optimization using gaussian processes
- [ekamperi.github.io: Blog on acquisition functions](https://ekamperi.github.io/machine%20learning/2021/06/11/acquisition-functions.html): Different acquisition functions + EI derivation for gaussian process based Bayesian optimization

As the function $$y=f(x), x \in X$$ is uknown, let therefore let us model it by an stochastic process, in this case gaussian process. Although stochastic processes are typically defined on a time domain (which is mirrored in the term "process"), this is not a hard rule. In our case, the stochastic process is defined for example on a hyperparameter space $$X$$. Rather than a process, it can be understood as a probabilitic distribution over functions defined on $$X$$, a generalization of random vectors which are probabilistic distributions over vectors. In our case, Gaussian process is then generalization of multidimensional gaussian distribution to the space of functions. 

Now, we have a set measurements $$y_i$$ of the blackbox function values in some sampled $$x_i$$ points $$\mathcal D = \{(x_i, y_i) \vert i=1...N\}$$. Based on these measurements, and our prior beliefs about the unknown blackbox function formulated by the concrete parameters of the gaussian process, we can derive posterior belief about (which actually means *posterior distribution of*) the unknown blackbox function using bayes rule and the rules for conditional gaussian distributions:

$$ p(y\vert x, \mathcal D) = \frac{p(\mathcal D, y \vert x)}{p(\mathcal D)} $$

- $$p(y\vert x, \mathcal D)$$ is the posterior distribution of y given that we are measuring ing point $$x$$ and that the measurements $$\mathcal D$$ were done.
- $$p(\mathcal D, y \vert x)$$ is the joint probability of measurements $$\mathcal D$$ and of measuring $$y$$. given the point where this last measurement is done is $$x$$. It's just a different way of writing $$p((x_1, y_1), (x_2, y_2), \dots (x_N, y_N), (x, y))$$.
- Similarly, $$p(\mathcal D) = p((x_1, y_1), (x_2, y_2), \dots (x_N, y_N))$$

After evaluating this formula, we have the model of our posterior, measurement-aware belief about the unknown blackbox function. But where to pick do the next measurement? We can greedily optimize for best mean $$y$$ by searching $$\arg\max_x \mathbb E(y \vert x, \mathcal D) = \int y p(y \vert x, \mathcal D) dy$$, or take into account also variance, or maximize for example the *probability of improvement* (PI), or the *expected improvement* (EI). These different strategies are represented as so called acquisition functions, so that end up maximizing an acquisition functions $$u$$:

$$ x = \arg\max_x u(x, \mathcal D)$$

The choice of acquisition functions depends on our preference between *"lower but more certain"* and *possibly higher, but less certain"* improvement. This concept is similar to the concept of risk-aversion and utility functions in finance. (Considered the fact that here our budget is represented by number of black box evaluation we can do, which depends mainly on our resources, these two problems are even more similar). However in this context, we speak rather about balancing the *exploration* (testing some part of hyperparameter space we are very uncertian about, typicaly because of not having many similar samples) and *exploitation* (achieving good performance by testing a promising point). One of the most widely used acquistion function is the expected improvement (EI):

$$ u(x) = \int_{-\infty}^{y^*} (y-y^*) p(y \vert x, \mathcal D) dy$$

Here, $$ y^* $$ is some reference improvement over which we want to improve. It can be the best performance across the samples so far, or also some higher or lower value leading to a different exploration-exploitation tradeoff.

## Tree-structured Parzen estimator
Resources for technical details + derivations:
- [Algorithms for Hyper-Parameter Optimization](https://proceedings.neurips.cc/paper_files/paper/2011/file/86e8f7ab32cfd12577bc2619bc635690-Paper.pdf): Original paper, but probably with very minimalistic explanation
- [Tree-Structured Parzen Estimator: Understanding Its Algorithm Components and Their Roles for Better Empirical Performance](https://arxiv.org/abs/2304.11127): more detailed explanation + ablation studies
- [AIxplained: Automated Machine Learning - Tree Parzen Estimator (TPE)](https://www.youtube.com/watch?v=bcy6A57jAwI): good youtube video explanation
