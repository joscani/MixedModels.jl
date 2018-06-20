
# Normalized Gauss-Hermite Quadrature

[*Gaussian Quadrature rules*](https://en.wikipedia.org/wiki/Gaussian_quadrature) provide sets of `x` values, called *abscissae*, and weights, `w`, to approximate an integral with respect to a *weight function*, $g(x)$.  For a `k`th order rule the approximation is
$$
\int f(x)g(x)\,dx \approx \sum_{i=1}^k w_i f(x_i)
$$

For the *Gauss-Hermite* rule the weight function is
$$
g(x) = e^{-x^2}
$$
and the domain of integration is $(-\infty, \infty)$.  A slight variation of this is the *normalized Gauss-Hermite* rule for which the weight function is the standard normal density
$$
g(z) = \phi(z) = \frac{e^{-z^2/2}}{\sqrt{2\pi}}
$$

That is
$$
\mathbb{E}[f]=\int_{-\infty}^{\infty} f(z) \phi(z)\,dz\approx\sum_{i=1}^k f(z_i)\.w_i .
$$

Naturally, there is a caveat.  A `k`th order rule is exact when `f` is a `k-1` order polynomial.  To obtain a reasonable approximation the function, `f`, should be close to a low order polynomial over the range of interest. 

## Evaluating the weights and abscissae

In the [*Golub-Welsch algorithm*](https://en.wikipedia.org/wiki/Gaussian_quadrature#The_Golub-Welsch_algorithm) the abscissae are deteremined as the eigenvalues of a symmetric tri-diagonal matrix and the weights as the squares of the first row of the matrix of eigenvectors.

````julia
using Compat, DataFrames, DataFramesMeta, MixedModels, RData 
using Compat.LinearAlgebra

function gausshermitenorm(k)
    ev = eigfact(SymTridiagonal(zeros(k), sqrt.(1:k-1)))
    ev.values, abs2.(ev.vectors[1,:])
end
````


````
gausshermitenorm (generic function with 1 method)
````



````julia
gausshermitenorm(3)
````


````
([-1.73205, 1.11022e-15, 1.73205], [0.166667, 0.666667, 0.166667])
````



````julia
gausshermitenorm(9)
````


````
([-4.51275, -3.20543, -2.07685, -1.02326, 0.0, 1.02326, 2.07685, 3.20543, 4
.51275], [2.23458e-5, 0.00278914, 0.0499164, 0.244098, 0.406349, 0.244098, 
0.0499164, 0.00278914, 2.23458e-5])
````





The definition of `MixedModels.GHnorm` is similar to this with some extra provisions for ensuring symmetry of the abscissae and the weights and for caching values once they have been calculated.

````julia
GHnorm(3)
````


````
MixedModels.GaussHermiteNormalized{3}([-1.73205, 0.0, 1.73205], [0.166667, 
0.666667, 0.166667])
````





By the properties of the normal distribution, when $\mathcal{X}\sim\mathscr{N}(\mu, \sigma^2)$
$$
\mathbb{E}[g(x)] \approx \sum_{i=1}^k g(\mu + \sigma z_i)\,w_i
$$

For example, $\mathbb{E}[\mathcal{X}^2]$ where $\mathcal{X}\sim\mathcal{N}(2, 3^2)$ is

````julia
μ = 2
σ = 3
ghn3 = GHnorm(3)
sum(@. ghn3.wt * abs2(μ + σ * ghn3.z))
````



13.0



(In general a dot, '`.`', after the function name in a function call, as in `abs2.(...)`, or before an operator creates a [*fused vectorized*](https://docs.julialang.org/en/stable/manual/performance-tips/#More-dots:-Fuse-vectorized-operations-1) evaluation in Julia.  The macro `@.` has the effect of vectorizing all operations in the subsequent expression.)