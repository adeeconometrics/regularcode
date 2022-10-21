---
title: Stable Diffusion From First Principles Part 1
author: Dave Amiana
date: 2022-10-22
categories: [Programming, Computer Science, Diffusion Model, AI]
tags: [programming, Computer Science]
math: true
mermaid: true
---

## An Overview of Generative Modeling Framework

Generative models have been successfully used for computer vision tasks such as image super-resolution (Saharia _et. al._, 2021), text-to-image synthesis (Mirza & Osindero, 2014), and 3D scene rendering (Poole, Jain, Barron, & Mildenhall, 2022) to name a few. The main idea behind generative models is to develop an approximation $q_{\phi}(x)$ of the probability distribution in a dataset $p_{\theta}(x)$. That is, the objective of generative models is to train a probabilistic model whose samples $x_i \sim q_{\phi}(x)$ approximate the data distribution $p_{\theta}$.


The Bayesian framework offers a grounded theory for machine learning (Ghahramani, 2015). Thus, it is important to begin this section with an overview of the Bayesian approach to probability. Bayesian framework interprets probability as an assertion of belief predicated by prior knowledge or initial expectation of the distribution. In this framework, we define a prior that captures our belief about the distribution. Once an evidence is obtained, we update our initial distribution based on how the evidence in question changed the state of our belief about the true distribution. This is done by defining a posterior distribution $p (\theta | x \in  X)$. The Bayesian framework is used in Machine Learning to update the model $\mathcal{M}$ based on the distribution of a new sample $x$ drawn from the dataset $X$.

The prior predictive distribution is defined as a marginal probability of $x\in X$.

$$
\begin{aligned}
p(x) &= \int_{\theta \in \Theta} p(x, \theta)\ d\theta \\
	&= \int_{\theta \in \Theta} p(x|\theta) \times p(\theta)\ d\theta
\end{aligned}
$$

The posterior predictive distribution tell us the probability of the new data $x_t$ given the previous observation $x_{t-1}$.

$$
p(x_t | x_{t-1}) = \int_{\theta \in \Theta} p(x_t| \theta, x_{t-1}) \times p(\theta|x_{t-1})\ d\theta
$$

In general, sampling from the true distribution generally not possible for the following reasons: the integral has no closed form solution and (2) the integral is computationally intractable (Blei, Kucukelbir, & McAuliffe, 2018). Given the stated reasons, we are compelled to find a good approximation of the true distribution.

Early Generative models rely on Markov Chain Monte Carlo (MCMC) sampling methods during training and inference (Bond-Taylor, Leach, Long & Willcocks, 2022). MCMC methods draw samples from a posterior distribution, $x \sim q_{\phi}(x)$ where $\phi$ indexes a family of distributions. MCMC methods such as random walk tends to have long burn-in period which incurs computational overhead. For these reasons, MCMC methods does not scale well with high-dimensional data (Hinton & Sejnowski, 1983; Hinton, Osindero, & Teh, 2006).

Instead of viewing the inference problem as a random sampling problem drawn from a distribution, variational inference (VI) treats it as an optimization problem (Blei, Kucukelbir, & McAuliffe, 2018). VI define a family of sufficiently large parametric distributions $\{p_{\theta} | \theta \in \Theta\}$ and solve for the parameter $\theta$ that brings the proposal distribution $q_{\phi}$ close to $p_{\theta}$.

$$
\text{VI} := \min_{\theta} \mathcal{L}(q_{\phi}, p_{\theta})
$$

Generative models through neural networks dates back to 1980's with its aim to learn data features without supervision (Hinton & Sejnowski, 1983 as cited in Bond-Taylor, Leach, Long & Willcocks, 2022). At the time of its inception, computers were far too limited to meet the computational demands of generative modeling. It the culmination of advancements in developing high-performance hardware, fine-tuned and specialized dataset, and computational techniques for latent-space representation (Bond-Taylor, Leach, Long & Willcocks, 2022) that led to the resurgence of generative models in the scene of deep learning. Deep generative modeling (DGM) involves a deep stacks of hidden layers used to approximate high-dimensional probability distributions (Ruthotto & Haber, 2021). Training DGMs naturally lends itself to an optimization problem where we provide a notion of distance between probability distributions $\mathcal{L}(p_{\theta}, q_{\phi})$ (Grover & Ermon, 2022). VI offers a compelling approach which is scalable, numerically stable, and take advantage of GPUs (Patacchiola, 2022; Ermon, Kuleshov, & Contributors, 2022) at the cost of giving up the search for globally optimal solutions (Ermon, Kuleshov, & Contributors, 2022).

One way to measure the distance between statistical objects is to measure the Kullback-Leibler (KL) Divergence $\mathcal{D}_{KL} (p_{\theta}, q_{\phi})$.

$$
\begin{aligned}
\mathcal{D}_{KL}(p_{\theta}||q_{\phi}) &= \int_{-\infty}^{\infty} p(x) \log \left( \frac{p(x)}{q(x)}\right)\ dx
\end{aligned}
$$

KL-divergence is interpreted as a statistic that distinguish between distribution $p_{\theta}$ and $q_{\phi}$ based from a drawn sample $x \in X$. We can now define VI as an optimization problem that seek to minimize the KL-divergence between distributions as follows:

$$
\text{VI} := \min \mathcal{D}_{KL}\left(p_{\theta \in Data}(x) || q_{\phi}(x)\right)
$$

Since explicitly parameterized distributions are limited, we can consider implicitly parameterized distributions and use a neural network $f_{\theta}$ to account for a family of complicated functions.

1. Define a naive prior distribution $p$ over a latent random variable $z\in Z$;
2. Define a function approximator that $f_{\theta}(z)$ that approximates the distribution $p(z)$ via parameter $\theta$;
3. Define a function that converts latent function approximation into a simpler distribution over the observable random space $F: f_{\theta}(z) \to q_{\phi}(x)$.
   Using this approach we obtain a flexible generative model that is easy to sample.

We can use KL-divergence as our loss function. But KL-divergence still arrives at an intractable state as shown below:

$$
\begin{aligned}
\mathcal{D}_{KL} \big( q(\mathbf{z}) || p(\mathbf{z} | \mathbf{x}) \big)
&= \int q(\mathbf{z}) \log \frac{q(\mathbf{z})}{p(\mathbf{z} | \mathbf{x})} d\mathbf{z}\\
&= \int q(\mathbf{z}) \big( \log q(\mathbf{z}) - \log p(\mathbf{z} | \mathbf{x}) \big) d\mathbf{z}\\
&= \int q(\mathbf{z}) \log q(\mathbf{z}) - q(\mathbf{z}) \log p(\mathbf{z} | \mathbf{x}) d\mathbf{z}\\
&= \int q(\mathbf{z}) \log q(\mathbf{z}) d\mathbf{z} - \int q(\mathbf{z}) \log p(\mathbf{z} | \mathbf{x}) d\mathbf{z}\\
\end{aligned}
$$

Applying the definition of expectation over integrals $\mathbb{E}[X] = \int_{x\in X} x \cdot f(x)\ dx$:

$$
\begin{aligned}
\mathcal{D}_{KL} \big( q(\mathbf{z}) || p(\mathbf{z} | \mathbf{x}) \big) &= \mathbb{E}_{q} \big[ \log q(\mathbf{z}) \big] - \mathbb{E}_{q} \big[ \log p(\mathbf{z} | \mathbf{x}) \big] &\\
&= \mathbb{E}_{q} \big[ \log q(\mathbf{z}) \big] - \mathbb{E}_{q} \bigg[ \log \frac{p(\mathbf{x}, \mathbf{z}) }{p(\mathbf{x})} \bigg]\\
&= \mathbb{E}_{q} \big[ \log q(\mathbf{z}) \big] - \mathbb{E}_{q} \big[ \log p(\mathbf{x}, \mathbf{z}) - \log p(\mathbf{x}) \big]\\
&= \mathbb{E}_{q} \big[ \log q(\mathbf{z}) - \log p(\mathbf{x}, \mathbf{z}) \big] + \mathbb{E}_{q} \big[ \log p(\mathbf{x}) \big]\\
&= \mathbb{E}_{q} \big[ \log q(\mathbf{z}) - \log p(\mathbf{x}, \mathbf{z}) \big] + \underbrace{\log p(\mathbf{x})}_{\text{intractable}}\\
\end{aligned}
$$

Training a neural network involves a loss function $\mathcal{L}$ which measures the direction we adjust our gradients. To remedy this, we derive the Evidence Lower Bound (ELBO) from $\mathcal{D}_{KL}$. The key insight behind ELBO is to group the intractable part of the derived equation and frame the problem as a maximization problem:

$$
\begin{aligned}
\mathcal{D}_{KL}(q(\textbf{z}) || p(\textbf{x} | \textbf{z}))
&= \mathbb{E}_{q} \big[ \log q(\mathbf{z}) - \log p(\mathbf{x}, \mathbf{z}) \big] + \log p(\mathbf{x}) \\
\mathbb{E}_{q} \big[ \log q(\mathbf{z}) - \log p(\mathbf{x}, \mathbf{z}) \big] &= \log p(\mathbf{x})-\mathcal{D}_{KL}(q(\textbf{z}) || p(\textbf{x} | \textbf{z})) \\
\text{ELBO}(q) &= \mathbb{E}_{q} \big[ \log q(\mathbf{z}) - \log p(\mathbf{x}, \mathbf{z}) \big]\\
& = \mathbb{E}_{q} \bigg[ \log \frac{p(\mathbf{x}, \mathbf{z})}{q(\mathbf{z})} \bigg].
\end{aligned}
$$

In this frame, by maximizing ELBO, we maximize the evidence $p(\mathbf{x})$ and minimize the KL-divergence.

Now that we define a probabilistic framework for generative modeling, we move our discussion to a specific type of generative model called Diffusion model.

## Diffusion-based Generative Models

Diffusion-based generative models are inspired by non-equilibrium thermodynamics (Ho, Jain, & Abbeel, 2020). This approach introduce a diffusion process of adding random noise to an image via a forward process and training a neural network to denoise the image through a reverse process.

The diffusion process is a Markov Chain summarized as:

$$
\begin{aligned}
	q(x_t | x_{t-1}) &= \mathcal{N_{\theta}}\left(x_t\right) \\
	q(x_{1:T}|x_0) &= \prod_{t=1}^T q(x_t | x_{t-1})

\end{aligned}
$$

The forward process adds noise from the noise kernel $\mathcal{N}$. Gaussian Noise is favored for its simplicity and additive property. The reverse process then attempt to predict the added noise and recover the original image. Iteratively adding Gaussian Noise at $x_t$ as $t \to \infty$ is equivalent to an isotropic Gaussian Distribution.

### Denoising Diffusion Probabilistic Model (DDPM)

Ho, Jain, and Abbel (2020) introduced a promising approach for generative modeling called DDPM. As mentioned, the model mainly revolves around forward diffusion process and reverse diffusion process.

During training, the forward diffusion process takes the image $x_{t:T}$ to a standard Gaussian $\mathcal{N}(0, \text{I})$. At each step of the Markov Process, add a Gaussian noise: $\mathcal{N}(x_t; \mu_t = \sqrt{1-\beta_t}x_{t-1}, \sigma^2_t = \beta_t \textbf{I})$ where $\beta$ is a step size defined in $\{\beta_t \in (0.1)\}^T_{t=1}$. This process will produce a latent variable $x_t$ with distribution $q(x_t|x_{t-1})$. This process is summarized as:

$$
\begin{aligned}
	q(x_t | x_{t-1}) &= \mathcal{N}\left(x_t; \mu_t = \sqrt{1-\beta_t}x_{t-1}, \sigma^2_t = \beta_t \textbf{I} \right) \\
	q(x_{1:T}|x_0) &= \prod_{t=1}^T q(x_t | x_{t-1})

\end{aligned}
$$

At a sufficiently large step size, the image is assumed approximate a Gaussian Kernel $x_T \sim \mathcal{N}(x_t; \textbf{0,I})$.

The reverse diffusion process attempts to recover the image from information loss introduced by the noise kernel. The reverse process is also a Markov Process summarized as a set of parametric equations:

$$
\begin{aligned}
	p(x_T) &= \mathcal{N}(x_T; \textbf{0,I}) \\
	p_{\theta}(x_{t-1} | x_t) &= \mathcal{N}(x_{t-1}; \mu_{\theta}(x_t, t), \sigma^2_t\textbf{I}).
\end{aligned}
$$

In the reverse process, we recognize that $x_{x\to \infty} \approx \mathcal{N}(\mathbf{0}, \mathbf{I})$. For training our model to predict the added noise at timestep $t$, we parameterize $\mu$ and turn it into a neural network that predicts the noise.

Training DDPM usually involve a reweighted variant of ELBO on $p(x)$.

$$
\mathcal{L}_{DM} := \mathbb{E}_{x,\epsilon \sim \mathcal{N}(0,1),t} \big[ ||\epsilon - \epsilon_{\theta}(x,t) ||^2_2 \big]
$$

In this process, we sample $x$ and a noise $\epsilon$ from the standard Gaussian kernel as well as the timestep $t$. Then we minimize the Mean Square Error (MSE) loss between the known noise $\epsilon$ and the predicted noise given by the model $\epsilon_{\theta}$.

### Latent Diffusion Model (LDM)

Rombach, Blattmann, Lorenz, Esser, & Ommer (2022) introduced LDM. Their model, Stable Diffusion (SD), improved the computational cost of running diffusion models on high-dimensional dataset such as image by obtaining a latent space representation of the sample in question.

For training LDM, we sample from the latent space $z \in Z$.

$$
\mathcal{L}_{LDM} := \mathbb{E}_{\varepsilon(x), \epsilon \sim \mathcal{N}(0,1),t} \big[ ||\epsilon - \epsilon_{\theta}(z_t,t) ||^2_2 \big]
$$

The model $\varepsilon(x)$ maps to a latent space with significantly dimensionality. This approach enable SD to run more efficiently over Google's Imagen (Saharia _et al._, 2022) and OpenAI's Dall-E 2 (Ramesh, Dhariwal, Nichol, Chu, & Chen, 2022).

---

## References

1. Saharia, C., Ho, J., Chan, W., Salimans, T., Fleet, D. J., & Norouzi, M. (2021). Image Super-Resolution via Iterative Refinement. _arXiv_. https://doi.org/10.48550/arXiv.2104.07636
2. Mirza, M., & Osindero, S. (2014). Conditional Generative Adversarial Nets. _arXiv_. https://doi.org/10.48550/arXiv.1411.1784
3. Poole, B., Jain, A., Barron, J. T., & Mildenhall, B. (2022). DreamFusion: Text-to-3D using 2D Diffusion. _arXiv_. https://doi.org/10.48550/arXiv.2209.14988
4. Ghahramani, Z. (2015). Probabilistic machine learning and artificial intelligence. Nature, 521(7553), 452–459. doi:10.1038/nature14541
5. Hinton, G.E., & Sejnowski, T.J. (1983). Optimal Perceptual Inference.
6. Hinton, G. E., Osindero, S., & Teh, Y. W. (2006). A fast learning algorithm for deep belief nets. *Neural computation*, *18*(7), 1527-1554.
7. Bond-Taylor, S., Leach, A., Long, Y., & Willcocks, C. G. (2021). Deep generative modelling: A comparative review of VAEs, GANs, normalizing flows, energy-based and autoregressive models. arXiv preprint arXiv:2103.04922.
8. Blei, D. M., Kucukelbir, A., & McAuliffe, J. D. (2017). Variational inference: A review for statisticians. *Journal of the American statistical Association*, *112*(518), 859-877.
9. Grover, A. & Ermon, S. (2022). Notes on Deep Generative Models. Stanford. https://github.com/deepgenerativemodels/notes.
10. Ruthotto, L., & Haber, E. (2021). *An introduction to deep generative modeling. GAMM-Mitteilungen, 44(2).* doi:10.1002/gamm.202100008.
11. M. Patacchiola (2021). Evidence, KL-divergence, and ELBO. https://mpatacchiola.github.io/blog/2021/01/25/intro-variational-inference.html
12. Ermon, S., Kuleshov, V. & Contributors (2022). Variational Inference. Stanford. https://ermongroup.github.io/cs228-notes/
13. Ho, J., Jain, A., & Abbeel, P. (2020). Denoising diffusion probabilistic models. *Advances in Neural Information Processing Systems*, *33*, 6840-6851.
14. Rombach, R., Blattmann, A., Lorenz, D., Esser, P., & Ommer, B. (2022). High-resolution image synthesis with latent diffusion models. In *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition* (pp. 10684-10695).
15. Saharia, C., Chan, W., Saxena, S., Li, L., Whang, J., Denton, E., ... & Norouzi, M. (2022). Photorealistic Text-to-Image Diffusion Models with Deep Language Understanding. *arXiv preprint arXiv:2205.11487*.
16. Ramesh, A., Dhariwal, P., Nichol, A., Chu, C., & Chen, M. (2022). Hierarchical text-conditional image generation with clip latents. *arXiv preprint arXiv:2204.06125*.
