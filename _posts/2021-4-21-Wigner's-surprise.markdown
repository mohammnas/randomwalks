---
layout: post
title:  "Wigner's surprise"
toc: true
---


<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {
    inlineMath: [['$','$'], ['\\(','\\)']],
    processEscapes: true
  }
});
</script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

<link href="https://fonts.googleapis.com/css2?family=Amiri&display=swap" rel="stylesheet">



<div>
    <img src="https://raw.githubusercontent.com/mohammnas/randomwalks/master/Images/Wigner/birds.JPG" width="400" align="right"/>
</div>

**Contents**
- TOC
{:toc}



Now that I'm headed to grad school, it would be apt to spend some of my time learning about things related to my area of expertise. Therefore, I've been doing some light reading on random matrix theory (RMT), which made a strong mark on ecology back in the 70's with Robert May's seminal paper showing that a random community would become unstable as it diversified past a certain threshold [1]. However, this post will not be about ecology, but rather about a very interesting mathematical result that originated in particle physics.

## Introduction
Our story begins when a young-ish Eugene Wigner was thinking about the statistical properties of energy levels of large nuclei [2]. Basically, neutrons scattering from say, a uranium atom, would produce an energy diagram with lots of peaks. An interesting feature of these peaks, however, is that the probability they are near each other is very low. In other words, they exhibit a sort of *repulsion*.

In order to model the energy levels constrained by this interesting feature, Wigner posited that we should use statistical methods rather than deterministic ones, and represent these energy levels in a matrix where the entries are drawn from a standard Gaussian distribution. Likewise, he realized that we could inspect the  distribution of eigenvalues as a joint random variable which reflects the entries of the matrix in less dimensions. However, to enforce the eigenvalues being real-valued, he decided to symmetrize the matrices. And thus the Gaussian Orthogonal Ensemble (GOE) of matrices was born!

## A derivation

Now, how do we derive a distribution that exhibits these features? Well, apparently Wigner guessed it correctly in front of a crowd [3], but for mere mortals like most of us we need to begin the derivation by asking a fundamental question: for a $2 \times 2$ matrix in the GOE, what is the distribution of the distance between the eigenvalues?

Recall that the GOE is the collection of symmetric matrices with standard Gaussian entries. As a slight modification (to simplify calculations as shown in [3]), let's consider matrices in $\mathbb{R}^{2\times 2}$ such that the diagonal entries are distributed as $N(0,1)$ and the off-diagonals as $N(0,\frac{1}{2})$. In other words:

$$\begin{bmatrix} x_1 & x_3 \\ x_3 & x _2 \end{bmatrix} , \; x_1,x_2 \sim N(0,1), x_3 \sim N(0,\frac{1}{2})$$

Now, the eigenvalues would need to satisfy:

$$\lambda_{1,2} = \frac{\left( x_1+x_2 \pm \sqrt{(x_1-x_2)^2+4x_3^2}\right)}{2}$$

The spacing between eigenvalues, $s = abs(\lambda_1 - \lambda_2)$ is then:

$$s =\sqrt{(x_1-x_2)^2+4x_3^2}$$

Now, if we would like to find the probability density function of the spacings $s$, we require two conditions: 1) that $x_1$,$x_2$, and $x_3$ follow their respective distributions, and 2) that $s =\sqrt{(x_1-x_2)^2+4x_3^2}$. We would like to sum up all values of $x_1$,$x_2$, and $x_3$ that satisfy these conditions, which is expressed in the following pdf:

$$p(s) = \int_{-\infty}^{\infty} \frac{e^{-\frac{1}{2}x_1^2}}{\sqrt{2 \pi}} \frac{e^{-\frac{1}{2}x_2^2}}{\sqrt{2 \pi}} \frac{e^{-\frac{1}{2}x_3^2}}{\sqrt{\pi}} \delta (s - \sqrt{(x_1-x_2)^2+4x_3^2}) \; dx_1 dx_2 dx_3 $$

Where $\delta$ is the Dirac delta function. Recall that this function is just extremely peaked at $x=0$ and zero elsewhere, but it also satisfies $\int_{-\infty}^{\infty} \delta(x) dx = 1$, so it really behaves like a distribution (which is great for our purposes!).

For the rest of the derivation, I'd rather not regurgitate the calculation from chapter 2 of [3] (which is freely available online). You simply switch to polar coordinates (along with computing the Jacobian and using $sin^2(x)+cos^2(x)=1$) and obtain the following value for $p(s)$:

$$p(s) = \frac{s}{2} e^{\frac{-s^2}{4}}$$

But this is not what Wigner guessed in his moment of glory. Rather, we would like to rescale this density to a new PDF $\bar{p}(s)$ such that $\mathbb{E}[\bar{p}(s)] = \int_{-\infty}^{\infty}s \cdot \bar{p}(s) ds = 1$. To this end, we will define the PDF as $\bar{p}(s) = \langle s \rangle p(\langle s \rangle s)$, where $\langle s \rangle = \int_{0}^{\infty} s p(s) ds$, which is the mean spacing (note that bounds are positive since we defined the spacings to be positive). Thus we can compute $\bar{p}(s)$ by simply computing $\langle s \rangle$. Letting $u=\frac{s}{2}$ and integrating by parts:

$$\langle s \rangle = \int_{0}^{\infty} \frac{s^2}{2} e^{\frac{-s^2}{4}} ds = \int_{0}^{\infty} 4u^2 e^{-u^2} du = 4 \left( -\frac{1}{2} u e^{-u^2} + \int_0^{\infty} \frac{1}{2} e^{-u^2} du \right) = \left( s e^{-\frac{s^2}{4}}  + \sqrt{\pi} erf(\frac{s}{2}) \right) \Big|_0^\infty = \sqrt{\pi}$$

Thus we can conclude that $\bar{p}(s) = \frac{\pi s}{2} e^{\frac{- \pi s^2}{4}}$. This little equation is known as [Wigner's surmise](https://en.wikipedia.org/wiki/Wigner_surmise), named after the way Wigner famously guessed it in front of a crowd.

Now, how does this PDF display the "repulsion" that Wigner desired? Hopefully it becomes clear after a simulation below.

## A simulation in Mathematica
Here's a little simulation in Mathematica that plots the distribution of distances between the matrices we considered. It does the following:

1) Samples 10,000 matrices with Gaussian entries as specified before \\
2) Symmetrizes them (so they are from the GOE) and finds their eigenvalues \\
3) Finds the pairwise differences between the eigenvalues and normalizes the result \\
4) Plots the histogram against the pdf for Wigner's surmise

You can clearly see that the distance between the eigenvalues is distributed exactly as Wigner "surmised".

<div>
    <img src="https://raw.githubusercontent.com/mohammnas/randomwalks/master/Images/Wigner/Code.JPG" width="1000" align="left"/>
</div>

<div>
    <img src="https://raw.githubusercontent.com/mohammnas/randomwalks/master/Images/Wigner/Hist.JPG" width="350" />
</div>

Interestingly, there seems to be a very low probability of two eigenvalues being close to each other, almost as if they are feeling the other's presence! The shape of this distribution was enough for Wigner to decide that the energy levels of neutrons were repelled in the same way. Neat!

I hope somebody learned something from this post. Next time, I hope to write about phase transitions in ecological systems. Until then!

--Nasser

## Sources
[1] May, R. Will a Large Complex System be Stable?. Nature 238, 413–414 (1972). https://doi.org/10.1038/238413a0

[2] Deift, Percy. "Universality for mathematical and physical systems." arXiv preprint math-ph/0603038 (2006).

[3] Livan, Giacomo, Marcel Novaes, and Pierpaolo Vivo. Introduction to random matrices: theory and practice. Vol. 26. Berlin: Springer, 2018.
