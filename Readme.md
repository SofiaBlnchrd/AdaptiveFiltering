
# Intuition

The paper introduces a novel approach to constrain the filter coefficients by placing them on an arbitrary manifold. To better understand this concept, imagine representing the filter coefficients as points on a sphere in the provided image. In this specific case, the filter consists of three coefficients, and each point on the sphere corresponds to a particular combination of these coefficients.

![pointM](https://github.com/SofiaBlnchrd/AdaptiveFiltering/assets/135394093/fc472fc2-0cdf-48d0-9e22-5ccf56672013)

By employing this manifold constraint, the authors argue that they are offering a more generalized framework compared to other types of constraints commonly found in the literature. Traditionally, constraints such as box constraints or linear constraints have been used to restrict the filter coefficients. However, the proposed method expands upon these conventional approaches by allowing the filter coefficients to reside on a manifold, providing greater flexibility and potentially capturing more complex relationships between the coefficients.

In essence, by adopting this manifold constraint, the paper offers a broader perspective on constraining filter coefficients, which could potentially lead to improved adaptive filtering techniques with enhanced performance and adaptability to various applications.

The proposed algorithm aims to find a point on the sphere that minimizes the Least Mean Square (LMS) criterion. To ensure that the algorithm remains within the surface of the manifold, the exponential map is utilized at each step of the optimization process. This technique allows the algorithm to navigate along the manifold's surface, as depicted in the accompanying figure.


![pointMgeo](https://github.com/SofiaBlnchrd/AdaptiveFiltering/assets/135394093/416e8d73-ebf1-4859-93b7-f1113d80dc18)

By employing the exponential map, the algorithm effectively projects the optimization steps onto the manifold, guaranteeing that the resulting points lie within the desired spherical structure. This approach ensures that the algorithm operates in a manner that is consistent with the constraints imposed by the manifold, enabling it to explore the relevant space of filter coefficients effectively.

The figure provides a visual representation of how the algorithm progresses on the manifold surface, demonstrating how the exponential map assists in maintaining the search within the manifold's boundaries. By leveraging this methodology, the proposed algorithm offers a robust and efficient solution for optimizing filter coefficients within the specified manifold structure.

# Python implementation

Install the geomstats library

> !pip install geomstats

Initialize filter and import library

> from numpy.random.mtrand import gamma
> 
> import numpy as np
> 
> import matplotlib.pylab as plt
> 
> import geomstats.backend as gs  
> import geomstats.visualization as visualization  
> from geomstats.geometry.hypersphere import Hypersphere
> 
> 
> 
> from geomstats.geometry.hypersphere import Hypersphere
> from geomstats.geometry.hyperbolic import Hyperbolic
> from geomstats.geometry.hyperboloid import Hyperboloid
