## Using Parallel SVD to Determine a Good Music Mix

### Introduction

In 2009, Apple released iTunes 9.x and with it, launched the Genius Mixer, a “recommender” that chose songs from individuals’ playlist based on what sounded “good together.” Simultaneously, the music industry saw an explosion in self-made electronica/pop DJs, a precipitous increase in the popularity of artists like Krewella, Avicii and David Guetta, and an increase in the number of music-mixing technologies including the well-known Djay app for iOS by (aptly-named) Algoriddm.

So how exactly does such software determine which songs sound good together? Little is known about the role that big data can play in fine-tuning these algorithms. In this project, we explore the publicly-available Million Song Dataset in an attempt to answer this question.

We approach the question at hand by implementing a clustering algorithm for songs. The archetypal clustering method is K-means. The solution of cluster-membership indicators in K-means, however, are the principal components from Principal Component Analysis (PCA). Note that the principal components used in PCA are in fact the eigenvectors of a Singular-Value Decomposition (SVD).

### Theory & Methodology

First note the following: 

Applying SVD to a matrix A gives the following: [1]

Following [1], we can compute an approximation to A with a dot-product approximation B that is computationally less intensive (O(kn^2) as opposed to a typical SVD, which is O(n^3)): [2]

To compute B, one’s first inclination would be to have all GPUs access A i.e. utilize shared memory (see Figure 1(a)). However, this is intractable for a sufficiently large Instead, we distribute portions of A to each GPU, compute partial dot-products, then perform a reduction on the CPU to get a final approximation (Figure 1(b)). We implement this in Python, utilizing MPI4Py (distribution), PyCUDA and CUDABlas (dot-product computation) on several K-20 GPUs.

Next, we compute a similarity “score” between every pair of songs, where similarity is defined as the 2-norm of numeric vector of attributes per song, rescaled to [0,1]. Thus, A is a symmetric nxn matrix, where n is equal to the total number of songs.

Using a subset of the eigenvectors calculated from our approximate SVD, we can now cluster songs. The resulting graph clusters indicate how similar songs are to each other. By specifying the number of eigenvectors, we can present a path from one song to the next – a “mix” of music based on similarity.