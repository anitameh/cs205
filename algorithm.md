### Algorithm [1]

Analyzing big data may require finding an approximate representation for the system due to computational limitations. A popular method for approximating large systems is by first factorizing the system with the Singular Value Decomposition (SVD). However, computing the SVD is typically done in a two-step process by first reducing to a bi-diagonal matrix and then computing the SVD of this bi-diagonal matrix using an iterative method.

However, a recent paper by Friedland et al [1] utilizes an iterative Monte Carlo sampling approach  to compute the k-rank approximation to A in O(kmn) . The approach presented in the paper is actually quite simple. It relies on two key steps: (1) randomly sampling columns from your large matrix “A” and computing a set of orthonormal vectors from those columns and (2) applying those columns to the matrix “A”.  This process is then iterated until some convergence criteria is reached.  Here the term “apply” simply means to multiply they orthonormal columns by the left and right side of the matrix “A”.  This is actually quite similar to the divide and conquer approach in your typical SVD computation where you compute left and right QR factorizations and then apply them to your matrix to reduce down to bi-diagonal structure.

The second step in this process is the main bottleneck because it requires computing “k” matrix-vector products – each with O(mn) complexity.  A main focus of our project was to parallelize this step.  The simplest way to do this would be to compute one matrix-vector product on each processor, however this requires your large matrix “A” being known to every processor This is shown visually below:

[Fig 1]

However, a better approach would be to distribute your large matrix “A” across processors and simply pass the vectors around because they are much smaller.  This is shown visually below:

[Fig 2]

This large matrix “A” could also be distributed across multiple GPUs to attain even greater speedups.   We hypothesize that by using distributed memory i.e. splitting up and distributing pieces of A to each GPU (along with pieces of the orthonormal columns) we will see speed-ups. We then compute the dot product on each GPU, and aggregate the results by performing a reduction on the CPU (as shown above).

### Algorithm [2]

For this portion of the project, we use the parallel SVD approximation to determine a “good” mix of music. Importantly, note that the eigenvectors from an SVD of a covariance matrix are used in spectral clustering. This is used to determine the clusters in which the data points (i.e. songs) are grouped. We construct a covariance matrix that captures the similarities between each song. This similarity metric is equal to the 2-norm between nine attributes of pairs of songs i, j:

[1]

where features we chose to use include:

Danceability
Artist Hotttnesss
Segments timbre
Beats confidence
Loudness
Mode Confidence
Key Confidence
Tempo
Start of fade out
A visualization of the similarity matrix for a small subset of songs is shown here:

[Fig 1]

Figure 1. A similarity matrix of a subset of songs.

After applying the approximate SVD, we get a set of eigenvectors. For this sample clustering, we use just one eigenvector (i.e. the one corresponding to the largest eigenvalue) to cluster the songs into two groups. Note the trend that we observe: as songs change in timbre, they become less similar to each other (as captured by our measurement of “similarity”). Now, to generate a mix, an individual can specify to the DJ a start song, an end song, and how many minutes they want the mix to play. Using the clustering, we can move from one song to the next across nodes based on similarity.

[Fig 2]
[Fig 3]