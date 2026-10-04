---
title: 'Intro to Tabular Foundation Models'
date: 2026-10-02
permalink: /posts/2026/10/intro-to-tfms/
---

This is the first in a series of (upcoming) blog posts relating to tabular foundation models.

With the recent release of [NVIDIA Kumo Tabular](https://huggingface.co/blog/nvidia/kumo-tabular), I have decided to spend some time taking a closer look into the field of tabular and relational foundation models. There are many parallels between the field of graphs and relational databases (each row can be represented as a node with primary-foreign key relations representing edges), and tabular foundation models have been shown to perform competitively with dedicated graph architectures on certain benchmarks. In this blog, I will outline the motivation and background necessary to later understand the current frontier of tabular machine learning.

# Background

In the early 2020s, as deep learning was seeing incredible success in the fields of natural language and image processing, researchers wondered whether the same principles could be applied to the domain of tabular data. Early works adapted a wide range of traditional neural network architectures, but the field itself was fragmented and reported results were not generally comparable. 

Gorishniy et al. (2021) explored the use of the popular transformer architecture on tabular data, representing each row as a distinct identity (a sequence where each feature is a token) and performing self-attention across the feature dimension. The empirical experiments showed good performance compared to other deep-learning models, but importantly, the model did not universally outperform tree-based methods.

Grinsztajn et al. (2022) analyzed the difference in inductive biases between tree-based models (specifically gradient boosted decision trees) and neural networks and how they contributed to the relative success of the former. They found that tree methods are more robust to irregular target functions (due to a neural network's bias towards smoothness), can ignore uninformative features, and work in the natural basis of the data. On the other hand, neural networks are much more expressive, but they lack the regularization necessary to perform well.

These papers made it increasingly clear that tabular problems could not simply be solved by throwing the exact same architectures and techniques from language and images at this new problem.

# Prior-Data Fitted Networks

Müller et al. (2022) introduces the Prior-data fitted network (PFN), which is the basis for modern tabular foundation models. Given a dataset $$D=\{(x_i, y_i)\}_{i=1}^n$$ and a query $$x$$, we are interested in finding the $$\textit{posterior predictive distribution}$$:

$$
\begin{equation*}
p(y \mid x, D) = \int_t p(y \mid x, t) p(t \mid D).
\end{equation*}
$$

- $$t$$ is a latent variable representing the task, or a true rule that generated the data
- $$p(t)$$ is the prior that gives you a probability distribution of what tasks are likely to be the true task
- $$p(t \mid D)$$ is the posterior, representing which tasks are still plausible given the data we have observed
- $$p(y \mid x, t)$$ is the distribution of outputs $$y$$ given the input and the true task

This gives us the distribution over output label $$y$$ given an input $$x$$ and all the data points seen before. By Bayes' rule, $$p(t\midD) \propto p(D\midt)p(t)$$.

Suppose we have a parameterized model $$q_\theta$$ that accepts a dataset $$D$$ and query $$x$$ and predicts distributions of output $$y$$. We can define the $$\textit{Prior-Data Negative Log-Likelihood}$$:

$$
\begin{equation*}
\ell_\theta = \mathbb{E}_{D \bigcup \{x,y\} \sim p(D)} [- \log q_\theta (y \mid x, D)].
\end{equation*}
$$

$$\ell_\theta$$ is the expectation of the cross-entropy (and thus minimizing it is also equivalent to minimizing the KL-Divergence) between the true PPD and its approximation $$q_\theta$$.

Proof:

$$
\begin{align*}
\ell_\theta &= - \int_{D,x,y} p(x, y, D) \log q_{\theta} (y \mid x, D) \\
&= - \int_{D, x} p(x, D) \int_y p(y \mid x, D) \log q_\theta (y \mid x, D) \\
&= \int_{D,x} p(x,D) H(p(\cdot \mid x, D), q_\theta (\cdot \mid x, D)) \\
&= \mathbb{E}_{x, D \sim p(D)} [H(p(\cdot \mid x, D), q_\theta (\cdot \mid x, D))],
\end{align*}
$$
where $$H$$ represent cross-entropy.

This allows them to use a transformer model to directly approximate the PPD, in contrast to previous methods that approximated the posterior. The method shows clear results in the task of performing Bayesian inference in the case of Gaussian Processes (GPs).

They apply this to tabular datasets by building a classification prior on Bayesian Neural Networks (BNNs). By placing a prior over model architectures, they avoid the resource-intensive hyperparameter tuning and architecture search necessary to find a performant model. This allows for a fully Bayesian approach over both the weights and the architecture of the model. Their algorithm takes the following steps to train:

1. Sample a model architecture $$A \sim p(A)$$
2. Sample model weights for the given architecture: $$W_{i,j} \sim p_w(\cdot)$$
3. Sample i.i.d. features $$x_{i,f} \sim \mathcal{N}(0,1)$$ for each data point $$i$$ and each feature $$f$$.
4. Propagate each $$x_i$$ forward through $$A_W$$ yielding $$\{(x_i, A_W(x_i))\}_{i=1}^N$$.

Outputs are converted to class labels by thresholding.

In this case, the task $$t = (A,W)$$. This is the rule that generates the data. Since the PPD integrates over the task, it means that we are able to integrate over both the candidate architectures and weights. By approximating this intractable function with a PFN, it gives us a method for efficiently evaluating the approximate prediction that we would get from averaging across all plausible architectures. One important weakness to notice about this model is that it maintains the biases of the prior. In this case, all the features are drawn from a standard Normal distribution, and labels are smooth functions of the features. In reality, this is a strong assumption to make and it is also why future architectures take different approaches.

This paper was a strong proof-of-concept for a neural network approach to tabular data that could begin to compete with tree-based models, although its experiments were limited to small tabular datasets, and likely would not scale.



## References
[1] Grinsztajn, Léo, Edouard Oyallon, and Gaël Varoquaux. “Why Do Tree-Based Models Still Outperform Deep Learning on Tabular Data?” arXiv:2207.08815. Preprint, arXiv, July 18, 2022. https://doi.org/10.48550/arXiv.2207.08815.

[2] Gorishniy, Yury, Ivan Rubachev, Valentin Khrulkov, and Artem Babenko. “Revisiting Deep Learning Models for Tabular Data.” arXiv:2106.11959. Preprint, arXiv, October 26, 2023. https://doi.org/10.48550/arXiv.2106.11959.

[3] Müller, Samuel, Noah Hollmann, Sebastian Pineda Arango, Josif Grabocka, and Frank Hutter. “Transformers Can Do Bayesian Inference.” arXiv:2112.10510. Preprint, arXiv, August 13, 2024. https://doi.org/10.48550/arXiv.2112.10510.
