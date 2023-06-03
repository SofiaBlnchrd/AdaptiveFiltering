
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

define the filter as a function with the following parameters D=desired signal, x=input signal, N=signal size, p =filter order
> def filterMANIFOLD( Deseada,x, N, p,mu=0.08,rho=100.7,maifoldx='esfera',mostrar=False,retErrorSig=False):

 #define los datos
 AECG=Deseada                # d
 x=x.reshape(x.shape[0],1);  # Xref
 r_hat=np.ones((p,1))*0.2
 g_hat=np.ones((p,1))*0.2
 p_hat=0
 #------------------------------------------
 error=np.zeros(N);salida=np.zeros(N);h1=np.ones((N,p)); 
 #parametros
 
 if maifoldx=='esfera':
  SPHERE2 = Hypersphere(dim=p-1)
 else: 
  SPHERE2 = Hyperboloid(dim=p-1, coords_type="extrinsic")


 points_in_manifold = SPHERE2.random_point(n_samples=1)
 h=points_in_manifold
 h=h.reshape(p,1)

 METRIC = SPHERE2.metric
 manifold=SPHERE2
 #mu=0.001;  delta=0.001; gamma=0.0001
 index=0
 for t in np.arange(N-p-1)+p:
  index=index+1
  #vector de entrada
  entrada= x[t:t-p:-1]
  #entrada= x[t-p+1:t+1]
  #error
  rr=h.T.dot(entrada)
  salida[t]=rr[0,0]
  error[t]=AECG[t]-salida[t]

  #calcular el line search
  # r_hat=r_hat*0.001+x[t-1]*x[t]
  # g_hat=g_hat-r_hat*salida[t]
  # #print('shapes:',r_hat.shape,x[t-p+1:t+1].shape)
  # p_hat=x[t-p+1:t+1].reshape(5).T*AECG[t-p+1:t+1].reshape(5)*0.001
  # #print('shapes:',r_hat.shape)
  # #print('shapes:',AECG[t-p+1:t+1].shape,g_hat.shape,p_hat.shape)
  # alfa=p_hat.T@AECG[t-p+1:t+1].reshape(5,1)
  # alfa=alfa-g_hat.T@h
  # #print('shapes:',alfa.shape)
  # #print('shapes:',p_hat.shape,h.shape)
  # alfa=alfa/(AECG[t-p+1:t+1].T@g_hat+1)
  # alfa=alfa.squeeze()
  # if alfa>0.1 or alfa<0:
  #   alfa=0.001
  #print(alfa)
  
  #h1[t]=h.T.copy()
  #METODO normalized LMS with a variable regularization factor 
  euclidean_grad =mu*entrada.reshape(p,1)*error[t]  #EL GRADIENTE
  #rho=100.7
  #print('error',error[t],' entrada',entrada,' mu',mu )
  #print('euclidean_grad',euclidean_grad)
  euclidean_grad=euclidean_grad.squeeze()
  #print('euclidean_grad',euclidean_grad)
  tangent_vec =manifold.to_tangent( vector=euclidean_grad, base_point=h.squeeze()) #CALCULA EL VECTOR EN EL PLANO TANGENTE
  h = manifold.metric.exp(base_point=h.squeeze(), tangent_vec=rho*tangent_vec)
  #h= h*0.999
  #h1[t]=h.T
  h=h.reshape((p,1))
  #h=h.reshape((p,1))
 # show results
 if mostrar:
  grafica1(error,AECG,N,salida)
 return error

define a test suite using the Mackey-Glass series and LMS from padasip library

