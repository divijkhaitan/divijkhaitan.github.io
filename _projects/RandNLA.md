---
layout: page
title: RandNLA
description: A library for randomised algorithms for numerical linear algebra, written in Rust
img:
importance: 2
category: class
giscus_comments: false
---

Numerical Linear Algebra drives modern computation, especially since the deep learning revolution. There has been a push to design hardware for the sake of speeding up these computations. On the algorithmic side of things, there has been a small community of researchers that has shown that randomisation can offer massive speedups at the cost of little accuracy, with a recent paper outlining the creation of RandLAPACK and RandBLAS, randomised counterparts to the popular BLAS and LAPACK libraries. This project is an attempt to bring these developments to the Rust ecosytem.
