---
layout: post
title:  "Data Science Reference Hub"
categories: ["mathematics", "machine learning"]
---

Central place for keeping links to interesting websites, blogs, tutorials and papers related to data science, machine learning, mathematics and programming.
- [Websites](#websites): Websites having lot of different interesting content.
- [Posts](#posts): Interesting posts, blogs, videos, tutorials or papers on a single topic.
- [Books](#books): Books I have read, which I would also recommend to others.
- [Papers](#papers): Some of the papers I have read (or at least partly went through) recently.

## Websites
List of websites having lot of different interesting content.

[**distill.pub**](https://distill.pub/): Machine learning research journal, focused on very clear explanations using visualisations and interactive examples. Unfortunately, currently discontinued.

[**ekamperi.github.io**](https://ekamperi.github.io/): Good data science blog of Stathis Kamperis on various topics: Bayesian optimizations, trees, autoencoders.

[**Interpretable Machine Learning book**](https://christophm.github.io/interpretable-ml-book/) by Christoph Molnar, online version. To be read.

[**jalammar.github.io**](https://jalammar.github.io/): Website of Jay Alammar. Very good visual explanations of neural network structures and concepts behind LLMs and vision models.

[**krasserm.github.io**](https://krasserm.github.io/): Great blogs on various ML topics by Martin Krasser, especially good explanations about Bayesian
approaches.

[**statproofbook.github.io**](https://statproofbook.github.io/): The Book of Statistical Proofs. Great collection of statistical definitions and proofs.

[**alpopkes.com**](https://alpopkes.com/posts/machine_learning/variational_inference/):  Blog of Anna-Lena Popkes with interesting posts on machine learning, statistics and python.

## Posts
List of interesting posts, blogs, videos, tutorials or papers on a single topic.

[**Conditional normal distribution derivation**](https://statproofbook.github.io/P/mvn-cond.html). Multidimensional case.

[**Generalized Linear Models, lecture notes**](https://www.stat.cmu.edu/~ryantibs/advmethods/notes/glm.pdf) by Ryan Tibshirani, very understandable explanation

[**github.com/karpathy/micrograd**](https://github.com/karpathy/micrograd): Great minimalistic neural network framework by Andrej Karpathy. Perfect for understanding how the neural network training exactly works.

[**Illustrated transformer**](https://jalammar.github.io/illustrated-transformer/) by Jay Allamar, for me the best intro into some concepts behind the current LLMs. See also later posts on the changes done in GPT architectures.

[**Iteratively Reweighted Least Squares video**](https://www.youtube.com/watch?v=hbWVVCc8x3A), derivation of iteratively reweighted least squares which is basically newton method for finding solution to GLM. Very hard to find this derivation on internet.

[**State of GPT**](https://www.youtube.com/watch?v=bZQun8Y4L2A&t=1510s) video by Andrej Karpathy. Great introduction into components of ChatGPT related  architectures, with high level explanation of transformer, supervised finetuning, reward modeling, RLHF, chains and agents

[**Wikipedia: Matrix Calculus**](https://en.wikipedia.org/wiki/Matrix_calculus): Good reference point for nontrivial multidimensional calculus rules.

**Jeffreys prior**: [**stack overflow answer**](https://math.stackexchange.com/a/1439037/1281914) and [**lecture notes**](https://www2.stat.duke.edu/courses/Fall11/sta114/jeffreys.pdf) explaining the ideas behind this method for constructing prior distribution.

## Books
List of books I have read, which I would also recommend to others. I include link to the edition I have read (in chronological order), but often newer and hence better edition already exists.

[**Hands-On Machine Learning with Scikit-Learn and TensorFlow**](https://www.oreilly.com/library/view/hands-on-machine-learning/9781491962282/) *(Aurélien Geron)*: Very good starter book on machine learning both "classical" and deep learning. Great balance between theory, intuition and code.

[**The Linux Command Line**](https://www.linuxcommand.org/tlcl.php) *(William Shotts)*: Great book for finally understanding bash, resp. learning it from scratch. Also freely available in [electronic from](https://www.linuxcommand.org/tlcl.php).

[**Fluent Python**](https://www.oreilly.com/library/view/fluent-python/9781491946237/) *(Luciano Ramalho)*: Advanced python concepts, well explained. Quite long book.

[**SQL Pocket Guide**](https://www.oreilly.com/library/view/sql-pocket-guide/9781492090397/) *(Alice Zhao)*: Short and concise guide to SQL. Great to learn SQL fast.

[**Spark: The Definitive Guide**](https://www.oreilly.com/library/view/spark-the-definitive/9781491912201/) *(Bill Chambers, Matei Zaharia)*: All about Spark including advanced topics like Spark machine learning, structured streaming, RDDs and mechanisms behind the spark engine. Code examples in both Python and Scala.

[**Interpreting Machine Learning Models With SHAP**](https://christophmolnar.com/books/shap/) *(Christoph Molnar)*: Excelent book about SHAP technique for explaining/interpreting ML models, based on the Shapley values. Includes theory, explanations and practical code examples.

[**Modeling Mindsets**](https://christophmolnar.com/books/modeling-mindsets/) *(Christoph Molnar)*: Nice short book about modeling mindsets behind different approaches to statistics and machine learning. Covers main ideas motivating frequentionist/bayesian statistics, likelihood method, causal inference, supervised, unsupervised, deep and reinforcement learning.

## Papers
Some of the papers I have read recently.

[**Axiomatic arguments for decomposing goodness of fit according to Shapley and Owen values**](https://projecteuclid.org/journals/electronic-journal-of-statistics/volume-6/issue-none/Axiomatic-arguments-for-decomposing-goodness-of-fit-according-to-Shapley/10.1214/12-EJS710.pdf) *(F. Huettner, M. Sunder - 2012)*: Some explanations about Shapley, but more importantly, Owen values.

[**Axiomatic Attribution for Deep Networks**](https://arxiv.org/abs/1703.01365) *(Mukund Sundararajan, Ankur Taly, Qiqi Yan - 2017)*: Definition of Integrated Gradients DNN interpretability technique, based on well-defined axioms

[**Improving performance of deep learning models with axiomatic attribution priors and expected gradients**](https://arxiv.org/abs/1906.10670) *(Gabriel Erion, Joseph D. Janizek, Pascal Sturmfels, Scott Lundberg, Su-In Lee - 2019)*: Definition of Expected Gradients DNN interpretability technique (closely related to integrated gradients, and somehow also to SHAP) and proposal of incorporating attribution method in form of attribution priors as regularization in training.

[**An introduction to causal inference**](https://psyarxiv.com/b3fkw/download?format=pdf) *(Fabian Dablander - 2020)*: Concise introduction into the main concepts of causal inference.


