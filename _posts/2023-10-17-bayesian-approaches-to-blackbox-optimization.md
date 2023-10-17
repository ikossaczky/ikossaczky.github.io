---
layout: post
title: "Bayesian approaches to blackbox optimization"
date: 2023-10-17 18:00:00 +0100
categories: mathematics
---
Typical example of a black box function optimization problem is hyperparameter optimization. We have an ML model, and we want to find hyperparameters represented as a vector $$x$$, leading to best possible performance:

$$ y = f(x) $$

Here, $$y$$ is the performance and $$f$$ is the blackbox function from model hyperparameters to performance.

The simplest way to do this, is to sample the hyperparameter vectors on a grid or randomly from some reasonable distribution and compute the performance for each of them. However, this is typically expensive, as computing performance involves both retraining and reevaluating of the ML model.

Therefore, an obvious idea is to use some sampled points to create an approximation of the blackbox function (called surrogate model in literature), and optimize this approximation. One of the simplest ideas, how to create such an approximation, would be for example to fit a polynomial to the sampled points. However, this approach has several problems:
- First order approximation has optimum in one of the sampled points, so the overall method is equivalent to random search.
- The number of points we need for fitting second order (quadratic) polynomial is quadratic with the number of hyperparameters, which can be easily too much. Moreover, such approximation has only one single non-boundary extrema, which might not even be the one we are looking for (minimum instead of maximum), and given the typically multimodal nature of the blackbox function, this is probably not a good approximation anyway.
- higher order polynomials are even more expensive (while still not being necesarilly great approximations of the blackbox function)

Given these problems with standard numerical approximations, it becomes clear: we often need to deal with insufficient number of sampled points. And this insufficient number of points means uncertainty. We need to take this uncertainty into account in our approximation of the blackbox function, as well as during taking the decision, where to sample the next point for evaluation. This is the reason for using probabilistic Bayesian models.

In this post, I'll not give detailed derivation of these models. There are other great blogs and papers for this,  check the links below. I will rather concentrate on the intuition and motivation behind two widely used Bayesian approaches: Sequential model-based optimization (SMBO) and Tree-structured Parzen estimator (TPE).

## Sequential model-based optimization
Resources for technical details + derivations:
- [Wikipedia: Gaussian process)](https://en.wikipedia.org/wiki/gaussian_process) for definition and properties of Gaussian processes
- [krasserm.github.io: Blog on Gaussian processes](https://krasserm.github.io/2018/03/19/gaussian-processes/): Gaussian processes intro + how to fit these to measured data
- [krasserm.github.io: Blog on Bayesian optimization](https://krasserm.github.io/2018/03/21/bayesian-optimization/): Bayesian optimization using Gaussian processes
- [ekamperi.github.io: Blog on acquisition functions](https://ekamperi.github.io/machine%20learning/2021/06/11/acquisition-functions.html): Different acquisition functions + EI derivation for Gaussian process based Bayesian optimization

As the function $$y=f(x), x \in X$$ is uknown, let us model it by an stochastic process, in this case Gaussian process. Although stochastic processes are typically defined on a time domain (which is mirrored in the term "process"), this is not a hard rule. In our case, the stochastic process is defined for example on a hyperparameter space $$X$$. Rather than a process, it can be understood as a probabilitic distribution over functions defined on $$X$$, a generalization of random vectors, which are probabilistic distributions over vectors. In our case, Gaussian process is then generalization of multidimensional Gaussian distribution to the space of functions. 

Now, we have a set measurements $$y_i$$ of the blackbox function values in some sampled $$x_i$$ points $$\mathcal D = \{(x_i, y_i) \vert i=1...N\}$$. Based on these measurements, and our prior beliefs about the unknown blackbox function, formulated by the concrete parameters of the Gaussian process, we can derive posterior belief about (which actually means *posterior distribution of*) the unknown blackbox function, using Bayes rule, and the rules for conditional Gaussian distributions:

$$ p(y\vert x, \mathcal D) = \frac{p(\mathcal D, y \vert x)}{p(\mathcal D)} \tag{1}\label{eq:smbo_bayes}$$

- $$p(y\vert x, \mathcal D)$$ is the posterior distribution of y given that we are measuring in point $$x$$, and  the previous measurements $$\mathcal D$$ are taken into account.
- $$p(\mathcal D, y \vert x)$$ is the joint probability of measurements $$\mathcal D$$ and of measuring $$y$$, given the point where this last measurement is done is $$x$$. It's just a different way of writing $$p((x_1, y_1), (x_2, y_2), \dots (x_N, y_N), (x, y))$$.
- Similarly, $$p(\mathcal D) = p((x_1, y_1), (x_2, y_2), \dots (x_N, y_N))$$

After evaluating this formula, we have the model of our posterior, measurement-aware belief about the unknown blackbox function. But where to pick do the next measurement? We can greedily optimize for best mean $$y$$ by searching $$\arg\max_x \mathbb E(y \vert x, \mathcal D) = \int_{\mathbb R} y p(y \vert x, \mathcal D) dy$$, or take into account also variance, or maximize for example the *probability of improvement* (PI), or the *expected improvement* (EI). These different strategies are represented by so-called acquisition functions, so that we end up maximizing an acquisition functions $$u$$:

$$x^{N+1} = \arg\max_x u(x, \mathcal D) \tag{2}\label{eq:acquisition_function}$$

The choice of acquisition functions depends on our preference between *"lower but more certain"* and *possibly higher, but less certain"* improvement. This concept is similar to the concept of risk-aversion and utility functions in finance. Considering the fact, that in this case our budget is represented by number of black box evaluation we can do, which depends mainly on our resources, these two concepts are even more similar. However, in this context, we speak rather about balancing the *exploration* (testing some part of hyperparameter space, we are very uncertian about, typicaly because of not having many similar samples) and *exploitation* (achieving good performance by testing at a promising point). One of the most widely used acquistion function is the expected improvement (EI):

$$ u(x) = \int_{-\infty}^{y^*} (y-y^*) p(y \vert x, \mathcal D) dy \tag{3}\label{eq:ei}$$

Here, $$y^*$$ is some reference performance over which we want to improve. It can be the best performance across the samples so far, or also some higher or lower value leading to a different exploration-exploitation tradeoff. For Gaussian process as prior, the posterior \eqref{eq:smbo_bayes} can be expressed analytically, and so can be the expected improvement \eqref{eq:ei}. After finding the point maximizing the acquisition function $$x^{N+1}$$, we can evaluate the blackbox function at this point $$y^{N+1} = f(x^{N+1}) $$, add the new measurement $$(x^{N+1}, y^{N+1})$$ to $$\mathcal D$$ and repeat the process.

## Tree-structured Parzen estimator
Resources for technical details + derivations:
- [Algorithms for Hyper-Parameter Optimization](https://proceedings.neurips.cc/paper_files/paper/2011/file/86e8f7ab32cfd12577bc2619bc635690-Paper.pdf): Original paper with very minimalistic explanation
- [Tree-Structured Parzen Estimator: Understanding Its Algorithm Components and Their Roles for Better Empirical Performance](https://arxiv.org/abs/2304.11127): more detailed explanation + ablation studies
- [AIxplained: Automated Machine Learning - Tree Parzen Estimator (TPE)](https://www.youtube.com/watch?v=bcy6A57jAwI): good youtube video explanation
- [Wikipedia: Kernel density estimation](https://en.wikipedia.org/wiki/Kernel_density_estimation): Kernel density estimation method for estimating probability distribution based on a sampled set of points.

Let us assume, we have a distribution for sampling the blackbox function inputs $$x \in X$$ (e.g. hyperparameters). Using this distribution, we again sample a set of measurements $$\mathcal D = \{(x_i, y_i) \vert i=1...N\}$$. 

We are at first interested in fitting $$p(x \vert y)$$, which can be in the context of hyperparameter optimization interpreted as *"the probability that the used hyperparameters are x, if the performance is y, given our sampling distribution"*. Note, that the dependence on the sampling distribution is important. For different sampling strategies, we would end up with different $$p(x \vert y)$$.

Depending on the distribution assumptions, which we pose on unknown distribution family, there are different ways of fitting $$p(x \vert y)$$. The authors of the original TPE paper chose the following form of approximating $$p(x \vert y)$$:

$$
p(x \vert y) =
\begin{cases}
  l(x)& \text{if } x \leq y^\gamma \\
  g(x) & \text{if } x > y^\gamma
\end{cases}\tag{4}\label{eq:tpe_pxy}
$$

where $$y^\gamma$$ is the $$\gamma$$th quantile from the samples $$\mathcal D$$ ordered by $$y$$. This can be understood as fitting distribution over $$(x,y_q)$$ where $$y_q$$ corresponds to $$y$$ quantized into two bins sepparated by $$y^\gamma$$. 

For fitting $$l(x)$$ and $$g(x)$$, the kernel density estimation (KDE, also known asParzen–Rosenblatt window method) is used. This method works on similar principle to RBF kernel in support vector machines, or to solving of heat equation with peaky initial condition.

Now, we choose the next sampling point, following the same objective as in the SMBO case, i.e. the point $$x$$ that maximizes the expected improvement (EI), where we choose $$y^* = y^\gamma$$. To express  $$p(y \vert x)$$ needed in the formula for EI \eqref{eq:ei}, we use Bayes rule:

$$
p(y \vert x) = \frac{p(x \vert y) p(y)}{\int_{\mathbb R} p(x \vert y) p(y) dy} \tag{5}\label{eq:tpe_bayes}
$$

After substituting \eqref{eq:tpe_bayes} into \eqref{eq:ei}, despite the fact that $$p(y)$$ is stays unknown, formulation \eqref{eq:tpe_pxy}, and the special choice of $$y^* = y^\gamma$$ makes it possible to express EI as

$$
u(x) = \frac{c}{\gamma + \frac{g(x)}{l(x)}(1-\gamma)} \tag{6}\label{eq:tpe_ei}
$$

where $$c$$ is a constant, dependet on $$\gamma$$, and the distribution $$p(y)$$, but independent of $$x$$. See the links above for details of the derivation. This means, in order to maximize expected improvement of the new measurement, we can simply choose to perform new measurement at the point 

$$x^{N+1} = \arg\max_x \frac{l(x)}{g(x)}$$

After obtaining $$y^*$$, we can add the measurement $$(x^{N+1}, y^{N+1})$$ to the set of measurements $$\mathcal D$$, and repeat the process.

### Note on the tree-structure of the hyperparameter space
Finally, let us add a note about the term "tree-structured" in the name of this method. For complex hyperparameter search-spaces, not all parameter combinations are valid. For example, if a neural network has only one hidden layer, then number of neurons in the second hidden layer is not a valid hyperparameter. Therefore, the hyperparameter space can be represented in a form of a tree. This is  taken into account in the KDE fitting of the functions $$g(x)$$ and $$l(x)$$. For the details, check: 
- in the [original paper](https://proceedings.neurips.cc/paper_files/paper/2011/file/86e8f7ab32cfd12577bc2619bc635690-Paper.pdf), the part "4.2 Details of the Parzen Estimator" (very rough explanation only)
- and the last paragraph of part "3.1 Optimizing EI in the GP" for tree-structure usage in SMBO algorithm, 
- in the more detailed [paper](https://arxiv.org/abs/2304.11127), for better explanation see part "3.3.3 Univariate Kernel vs Multivariate Kernel".

