---
layout: page
title: Applications of SVD
description: Implementing applications of SVD
img:
importance: 2
category: class
giscus_comments: false
---

## Singular Value Decomposition: Introduction

Singular value decomposition is a matrix factorisation method, that expressed an arbitrary matrix $$A_{m \times n}$$ as a product of 3 matrices, a nonnegative diagonal matrix $$\Sigma_{m \times n}$$ and two orthogonal matrices $$U_{m \times m}$$ and $$V^T_{n \times n}$$, which can be written as

$$A = U\Sigma V^T$$

The entries of the diagonal matrix $$\Sigma$$ are called the **singular values** of A, denoted as $$\sigma_i(A)$$. For a symmetric matrix, $$\vert\lambda_i(A)\vert=\sigma_i(A)$$, where $$\lambda_i$$ is the $$i$$th largest eigenvalue of $$A$$.

In the complex domain, $$\Sigma$$ is still nonnegative and real, $$U$$ is unitary and $$V^*$$ is the conjugate transpose of a unitary matrix $$V$$.

The number of nonzero entries in $$\Sigma$$ equals the rank of $$A$$. For a matrix $$A$$ with rank $$r$$, $$\Sigma$$ has $$r$$ nonzero diagonal entries, with the rest being zeros. It would look something like this

$$
A = U
\begin{bmatrix}
    \sigma_1 & \dots & 0 & \dots & 0 \\
    \vdots & \ddots & \dots & \ddots & \vdots \\
    0 & \dots & \sigma_r & \dots & 0 \\
    \vdots & \ddots & \dots & \ddots & \vdots \\
    0 & \dots & 0 & \dots & 0
\end{bmatrix}
V^T
$$

The fact that the rank equals the number of diagonal entries can be used to find bases for important subspaces related to the matrix $$A$$. The first r columns of $$U$$ form a basis for the column space of $$A$$, and the last $$n - r$$ columns of $$V$$ are a basis for the null space of $$A$$. Similarly, the first r columns of $$V$$ are a basis for the column space of $$A^T$$ which is also the row space of $$A$$, and the last $$m -r$$ columns of $$U$$ form a basis for the null space of $$A^T$$, which is also the orthogonal complement of the row space $$\mathbb{R}(A)^\perp$$.

## Application 1: LSI

Latent Semantic Indexing is a technique for information retrival that can be used to search for documents that are similar to a querying phrase. It is based on the principal that there exists a low dimension substructure to the space of documents.

The first step is creating what is called a document-term matrix, where each row represents a document and each column denotes a term. There are several possible ways to fill up each column, including:

- Binary: $$a_{ij}$$ = $$\mathbb{I}\{t_i \in d_j\}$$
- Frequency: $$a_{ij}$$ = $$\#(t_i \in d_j)$$
- Inverse Frequency $$a_{ij}$$ = $$\frac{c}{\#(t_i \in d_j)}$$

Once the document term matrix is created, the next step is to compute a singular value decomposition. Now, assuming a high signal to noise ratio, the smaller singular values can be interpreted as noise and subsequently discarded. A rule of thumb for this I used was when $$\frac{\sigma{_i}}{\sigma_{i+1}} > 10^c$$ for some $$c >= 3$$. This should provide a significant rank reduction while preserving the structure of the document space.

Queries can now be computed as follows. The SVD equation is $$A = U\Sigma'V^T$$, where $$U$$ is in the term space. $$V$$ is in the document space and $$\Sigma'$$ is the low rank approximation of $$\Sigma$$. Each query vector is a member of the document space, so to compute the best matches we can rearrange the equation to $$V = A'U(\Sigma')\^{-1}$$. To obtain the most similar documents, we just multiply the column vector of the query by $$U(\Sigma')\^{-1}$$.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/sv.png" title="lsi_svd" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/qr.png" title="qr_svd" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

This isn't simply a property of matrix decomposition, but one unique to SVD. Attached is a side by side comparison of the first two singular vectors and the analogous projection from the QR factorisation. While the SVD plots have some differentiation, and even a few clusters with similar books, the QR factorisation maps most documents to the same points and isn't able to give any useful information.

#### Polysemy and Cosine Similarity

Polysemy is the existence of multiple meanings for a word or phrase. If I have a dataset of textbooks from Mathematics and Computer Science, the word 'function' means a very different thing in the world of algorithm design compared to what it means in complex analysis. A natural concern is that queries which such words might lead to spurious matches. But that is where the dimensionality reduction from SVD is so useful. The latent space consists of 'metawords', so the co-ocurrence of 'function' with other terms from both fields would be captured by the metaword it corresponds to. This also extends to the inverse case, where words may be used interchangably, and different documents use one but not the other.

Additionaly, when trying to retrieve information one might want to see which terms or documents are similar to one another. This can be done by analysing the reconstructed low-rank approximation, specifically $$X = A'(A')^T$$ or $$(A')^TA'$$ respectively. $$X_{ij}$$ will denote the similarity of the $$i^{th}$$ term/document to the $$j^{th}$$.

## Application 2: Eigenfaces

Eigenfaces was discovered in 1987 by Sirovich and Kirby. This is built on a similar idea of the space of all human faces having a low dimensional substructure. This algorithm performs a principal component analysis of faces, and projects every subsequent face onto the principal components, and differentiates faces based on their distance from the clusters.

To begin with, I collected and cleaned a private dataset of around 200 images taken from about 15-20 of my friends. I ensured that the lighting and background were normalised across all of these images, else the directions of largest variance would be the backgrounds which would hamper the algorithm. For similar reasons, I ensured that the faces were completely visible in each image.

The input images were flattened and organised into a matrix with each image being one row. I then computed the principal components for the matrix. One challenge I encountered here was that I had a wide matrix (many more features than examples). Computing a PCA using $$A^TA$$ would have been quite expensive. The faster method is to compute $$AA^T$$ and decompose it, which gives you one set of singular vectors for $$A$$. The others can now be obtained by mutliplying this matrix of singular vectors with A.

Having computed the principal components, the next step is projecting the training set onto them. A representative image for each class is computed by taking the mean of all the projections for that class. 2 parameters are tuned, one being for the threshold distance for an unknown face, and the second being a threshold for an image not being a face.

The accuracy of the algorithm plateaus at around 6 principal components, suggesting that even just six components can cover a space of images from 15 different people extremely well.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/eigenfaces_accuracy.png" title="acc_plot" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
