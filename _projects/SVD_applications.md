---
layout: page
title: Applications of Singular Value Decomposition
description: Implementing applications of SVD
img: 
importance: 2
category: work
giscus_comments: false
---

## Singular Value Decomposition: Introduction

Singular value decomposition is a matrix factorisation method, that expressed an arbitrary matrix $$A_{m \times n}$$ as a product of 3 matrices, a nonnegative diagonal matrix $$\Sigma_{m \times n}$$ and two orthogonal matrices $$U_{m \times m}$$ and $$V^T_{n \times n}$$, which can be written as

$$A = U\Sigma V^T$$

The entries of the diagonal matrix $$\Sigma$$ are called the **singular values** of A, denoted as $$\sigma_i(A)$$. For a symmetric matrix, $$\vert\lambda_i(A)\vert=\sigma_i(A)$$, where $$\lambda_i$$ is the $$i$$th largest eigenvalue of $$A$$.

In the complex domain, $$\Sigma$$ is still nonnegative and real, $$U$$ is unitary and $$V^*$$ is the conjugate transpose of a unitary matrix $$V$$.

The number of nonzero entries in $$\Sigma$$ equals the rank of $$A$$. For a matrix $$A$$ with rank $$r$$, $$\Sigma$$ has $$r$$ nonzero diagonal entries, with the rest being zeros. It would look something like this 

$$A = U 
\begin{bmatrix}
    \sigma_1 & \dots & 0 & \dots & 0 \\
    \vdots & \ddots & \dots & \ddots & \vdots \\
    0 & \dots & \sigma_r & \dots & 0 \\
    \vdots & \ddots & \dots & \ddots & \vdots \\
    0 & \dots & 0 & \dots & 0
\end{bmatrix} 
V^T$$

The fact that the rank equals the number of diagonal entries can be used to find bases for important subspaces related to the matrix $$A$$. 

## Application 1: LSI

## Application 2: Eigenfaces

