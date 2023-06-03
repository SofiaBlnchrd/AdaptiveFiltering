
# Adaptive filter with Riemannian manifold constraint  

This is my interpretation of the paper [Adaptive filter with Riemannian manifold constraint  ](https://doi.org/10.1038/s41598-023-36127-y).
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

Here I implement the filter in python following Algorithm 2 in the paper.  
First install the geomstats library

> !pip install geomstats

Import the nessesary libraries

> from numpy.random.mtrand import gamma  
> import numpy as np  
> import matplotlib.pylab as plt  
> import geomstats.backend as gs  
> import geomstats.visualization as visualization  
> from geomstats.geometry.hypersphere import Hypersphere

define the filter as a function with the following parameters D=desired signal, x=input signal, N=signal size, p =filter order.
```
def MANIFOLD_ant( D,x, N, p,mu=0.08):  
   #initialize  
   x=x.reshape(x.shape[0],1);  # Xref  
   np.random.seed(727); #replicate results  
```   
>  error=np.zeros(N);outputF=np.zeros(N) #zeroes error and output  
>  manifold = Hypersphere(dim=p-1) #select hypersphere manifold constraint  
>  h = manifold.random_uniform(n_samples=1) #for filter coeficients select randon point on the manifold  
>  METRIC = manifold.metric  
>   
>  #begin method  
>  index=0  
>  for t in np.arange(N-p-1)+p:  
>   index=index+1  
>   #input vector  
>   inputv= x[t-p+1:t+1]  
>   #error  
>   rr=h.T.dot(inputv)  
>   outputF[t]=rr  
>   error[t]=D[t]-outputF[t]  
>   #LMS   
>   euclidean_grad =mu*inputv.reshape(p,1)*error[t]  #grad  
>   euclidean_grad=euclidean_grad.squeeze()  
>   tangent_vec =manifold.to_tangent( vector=euclidean_grad, base_point=h.squeeze()) #tangent vector  
>   h = manifold.metric.exp(base_point=h.squeeze(), tangent_vec=tangent_vec) #exponential map  
>   
>   h=h.reshape((p,1))  
>   
>  r=range(N); #range of the signal  
>  return D[r],outputF[r]  

define a test suite using the Mackey-Glass series and LMS from padasip library  

# That's all
That's all Folks..
I hope the code works for you, if there is any error in the implementation let me know at sofia.blanchard77@gmail.com  
If this page was of any help, please cite the paper of my friends:  
Mejia, J., Mederos, B., Gordillo, N., and Ortega L. Adaptive filter with Riemannian manifold constraint. Sci Rep 13, 9014 (2023). https://doi.org/10.1038/s41598-023-36127-y

