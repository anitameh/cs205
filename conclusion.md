## Reflections

This project was an excellent learning experience, and allowed us to grow our skills with MPI, CUDA, installation of packages and unsupervised learning. Here, we reflect on avenues of improvement, challenges and things we enjoyed the most.

### Potential next steps

The purpose of this project was to speed up an approximate SVD, employing parallelization and smart memory allocation techniques. Ironically, it was the construction of the similarity matrix (despite using MPI) that took the most amount of time. This was partly because constructing elements in the matrix required reading contents of the HDF5 files into memory, then performing computations on these (e.g. computing the mean, the l2 norm, etc). The memory limitations on a standard GPU (approximately 6 GB), made this a slow process.

A good next step would thus be to figure out a way to reduce this computation time.

### What was interesting... and what was frustrating

Working with the music data and learning about spectral clustering was fun (after all, we were attempting to make cool mixes!) but also very frustrating: interpreting our results, for example, proved difficult. We were working with discrete features but had to interpret them in a continuous fashion. Also interesting was installing PyCUDA and MPI4Py on the Odyssey cluster, though this was frustrating at times as well. Learning the algorithm for approximate SVD’s was very cool. Finally, implementing the parallel approximate SVD was very empowering: we now have the power to operate on large matrices in a fashion that is useful for a vast range of research and industry-related projects.

## References

[1] S. Friedland, A. Niknejad, M. Kaveh, and H. Zare, \Fast monte-carlo low rank approximations for matrices,” Proc. IEEE SoSE , pp. 218{223, 2006.

[2] Thierry Bertin-Mahieux, Daniel P.W. Ellis, Brian Whitman, and Paul Lamere. The Million Song Dataset. In Proceedings of the 12th International Society for Music Information Retrieval Conference (ISMIR 2011), 2011.

[3] Million Song Dataset, official website by Thierry Bertin-Mahieux, available at: http://labrosa.ee.columbia.edu/millionsong/.
