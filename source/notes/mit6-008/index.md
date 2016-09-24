---
title: MIT 6.008
date: 2016-09-24 15:27:47
mathjax: true
---

## Section 1: Probability and Inference

Probability -- fraction of times.

### Two Ingredients to Modeling Uncertainty

1. The set of all possible outcomes for the experiment: this set is called the *sample space* and is usually denoted by the Greek letter Omega $\Omega$.
2. The probability of each outcome: for each possible outcome, assign a probability that is at least 0 and at most 1.

### Probability Spaces

$$\sum_{\omega \in \Omega}\mathbb{P}(outcome\ \omega) = 1$$


### Random Variables

Definition of a "finite random variable": Given a finite probability space $(\Omega, \mathbb{P})$, a *finite random variable* $X$ is a mapping from the sample space $\Omega$ to a set of values $\mathcal{X}$ that random variable $X$ can take on.

### Conditioning for Random Variables

Consider two random variables $X$ and $Y$ (that take on values in the sets $\mathcal{X}$ and $\mathcal{Y}$ respectively) with joint probability table $p_{X,Y}$ (from which by marginalization we can readily compute the marginal probability table $p_Y$). For any $x \in \mathcal{X}$ and $y \in \mathcal{Y}$ such that $p_Y(y)>0$, the *conditional probability* of event $X=x$ given event $Y=y$ has happened is

$$ p\_{X|Y}(x|y) \triangleq \frac{p_{X,Y}(x,y)}{p_Y{y}}$$
