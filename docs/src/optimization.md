# Details of the parameter estimation

## The probability model

Maximum likelihood estimates are based on the probability model for the observed responses.
In the probability model the distribution of the responses is expressed as a function of one or more *parameters*.

For a continuous distribution the probability density is a function of the responses, given the parameters.
The *likelihood* function is the same expression as the probability density but regarding the observed values as fixed and the parameters as varying.

In general a mixed-effects model incorporates two random variables: $\mathcal{B}$, the $q$-dimensional vector of random effects, and $\mathcal{Y}$, the $n$-dimensional response vector.
The value, $\bf y$, of $\mathcal{Y}$ is observed; the value, $\bf b$, of $\mathcal{B}$ is not.

## Linear Mixed-Effects Models

In a linear mixed model the unconditional distribution of $\mathcal{B}$ and the conditional distribution, $(\mathcal{Y} | \mathcal{B}=\bf{b})$, are both multivariate Gaussian distributions,
\begin{equation}
\begin{aligned}
  (\mathcal{Y} | \mathcal{B}=\bf{b}) &\sim\mathcal{N}(\bf{ X\beta + Z b},\sigma^2\bf{I})\\\\
  \mathcal{B}&\sim\mathcal{N}(\bf{0},\Sigma_\theta) .
\end{aligned}
\end{equation}

The *conditional mean* of $\mathcal Y$, given $\mathcal B=\bf b$, is the *linear predictor*, $\bf X\bf\beta+\bf Z\bf b$, which depends on the $p$-dimensional *fixed-effects parameter*, $\bf \beta$, and on $\bf b$.
The *model matrices*, $\bf X$ and $\bf Z$, of dimension $n\times p$ and $n\times q$, respectively, are determined from the formula for the model and the values of covariates. 
Although the matrix $\bf Z$ can be large (i.e. both $n$ and $q$ can be large), it is sparse (i.e. most of the elements in the matrix are zero).

The *relative covariance factor*, $\Lambda_\theta$, is a $q\times q$ lower-triangular matrix, depending on the *variance-component parameter*, $\bf\theta$, and generating the symmetric $q\times q$ variance-covariance matrix, $\Sigma_\theta$, as
\begin{equation}
  \Sigma_\theta=\sigma^2\Lambda_\theta\Lambda_\theta'
\end{equation}

The *spherical random effects*, $\mathcal{U}\sim\mathcal{N}({\bf 0},\sigma^2{\bf I}_q)$, determine $\mathcal B$ according to
\begin{equation}
  \mathcal{B}=\Lambda_\theta\mathcal{U}.
\end{equation}

The *penalized residual sum of squares* (PRSS),
\begin{equation}
  r^2(\theta,\beta,{\bf u})=\|{\bf y} -{\bf X}\beta -{\bf Z}\Lambda_\theta{\bf u}\|^2+\|{\bf u}\|^2,
\end{equation}
is the sum of the residual sum of squares, measuring fidelity of the model to the data, and a penalty on the size of $\bf u$, measuring the complexity of the model.
Minimizing $r^2$ with respect to $\bf u$,
\begin{equation}
  r^2_{\beta,\theta} =\min_{\bf u}\left(\|{\bf y} -{\bf X}{\beta} -{\bf Z}\Lambda_\theta{\bf u}\|^2+\|{\bf u}\|^2\right)
\end{equation}
is a direct (i.e. non-iterative) computation.
The particular method used to solve this generates a *blocked Choleksy factor*, ${\bf L}_\theta$, which is an lower triangular $q\times q$ matrix satisfying
\begin{equation}
  {\bf L}_\theta{\bf L}_\theta'=\Lambda_\theta'{\bf Z}'{\bf Z}\Lambda_\theta+{\bf I}_q .
\end{equation}
where ${\bf I}_q$ is the $q\times q$ *identity matrix*.

Negative twice the log-likelihood of the parameters, given the data, $\bf y$, is
\begin{equation}
d({\bf\theta},{\bf\beta},\sigma|{\bf y})
=n\log(2\pi\sigma^2)+\log(|{\bf L}_\theta|^2)+\frac{r^2_{\beta,\theta}}{\sigma^2}.
\end{equation}
where $|{\bf L}_\theta|$ denotes the *determinant* of ${\bf L}_\theta$.
Because ${\bf L}_\theta$ is triangular, its determinant is the product of its diagonal elements.

Because the conditional mean, $\bf\mu_{\mathcal Y|\mathcal B=\bf b}=\bf
X\bf\beta+\bf Z\Lambda_\theta\bf u$, is a linear function of both $\bf\beta$ and $\bf u$, minimization of the PRSS with respect to both $\bf\beta$ and $\bf u$ to produce
\begin{equation}
r^2_\theta =\min_{{\bf\beta},{\bf u}}\left(\|{\bf y} -{\bf X}{\bf\beta} -{\bf Z}\Lambda_\theta{\bf u}\|^2+\|{\bf u}\|^2\right)
\end{equation}
is also a direct calculation.
The values of $\bf u$ and $\bf\beta$ that provide this minimum are called, respectively, the *conditional mode*, $\tilde{\bf u}_\theta$, of the spherical random effects and the conditional estimate, $\widehat{\bf\beta}_\theta$, of the fixed effects.
At the conditional estimate of the fixed effects the objective is
\begin{equation}
d({\bf\theta},\widehat{\beta}_\theta,\sigma|{\bf y})
=n\log(2\pi\sigma^2)+\log(|{\bf L}_\theta|^2)+\frac{r^2_\theta}{\sigma^2}.
\end{equation}

Minimizing this expression with respect to $\sigma^2$ produces the conditional estimate
\begin{equation}
\widehat{\sigma^2}_\theta=\frac{r^2_\theta}{n}
\end{equation}
which provides the *profiled log-likelihood* on the deviance scale as
\begin{equation}
\tilde{d}(\theta|{\bf y})=d(\theta,\widehat{\beta}_\theta,\widehat{\sigma}_\theta|{\bf y})
=\log(|{\bf L}_\theta|^2)+n\left[1+\log\left(\frac{2\pi r^2_\theta}{n}\right)\right],
\end{equation}
a function of $\bf\theta$ alone.

The MLE of $\bf\theta$, written $\widehat{\bf\theta}$, is the value that minimizes this profiled objective.
We determine this value by numerical optimization.
In the process of evaluating $\tilde{d}(\widehat{\theta}|{\bf y})$ we determine $\widehat{\beta}=\widehat{\beta}_{\widehat\theta}$, $\tilde{\bf u}_{\widehat{\theta}}$ and $r^2_{\widehat{\theta}}$, from which we can evaluate $\widehat{\sigma}=\sqrt{r^2_{\widehat{\theta}}/n}$.

The elements of the conditional mode of $\mathcal B$, evaluated at the parameter estimates,
\begin{equation}
\tilde{\bf b}_{\widehat{\theta}}=
\Lambda_{\widehat{\theta}}\tilde{\bf u}_{\widehat{\theta}}
\end{equation}
are sometimes called the *best linear unbiased predictors* or BLUPs of the random effects.
Although BLUPs an appealing acronym, I don’t find the term particularly instructive (what is a “linear unbiased predictor” and in what sense are these the “best”?) and prefer the term “conditional modes”, because these are the values of $\bf b$ that maximize the density of the conditional distribution $\mathcal{B} | \mathcal{Y} = {\bf y}$.
For a linear mixed model, where all the conditional and unconditional distributions are Gaussian, these values are also the *conditional means*.

## Internal structure of $\Lambda_\theta$ and $\bf Z$

In the types of `LinearMixedModel` available through the `MixedModels` package, groups of random effects and the corresponding columns of the model matrix, $\bf Z$, are associated with *random-effects terms* in the model formula.

For the simple example

````julia
julia> fm1 = fit(LinearMixedModel, @formula(Y ~ 1 + (1|G)), dat[:Dyestuff])
Linear mixed model fit by maximum likelihood
 Formula: Y ~ 1 + (1 | G)
   logLik   -2 logLik     AIC        BIC    
 -163.66353  327.32706  333.32706  337.53065

Variance components:
              Column    Variance  Std.Dev. 
 G        (Intercept)  1388.3332 37.260344
 Residual              2451.2500 49.510100
 Number of obs: 30; levels of grouping factors: 6

  Fixed-effects parameters:
             Estimate Std.Error z value P(>|z|)
(Intercept)    1527.5   17.6946  86.326  <1e-99


````




the only random effects term in the formula is `(1|G)`, a simple, scalar random-effects term.
````julia
julia> t1 = fm1.trms[1]
MixedModels.ScalarFactorReTerm{Float64,String,UInt8}(CategoricalArrays.CategoricalString{UInt8}["A", "A", "A", "A", "A", "B", "B", "B", "B", "B"  …  "E", "E", "E", "E", "E", "F", "F", "F", "F", "F"], [1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0  …  1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0], [1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0  …  1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0], :G, String["(Intercept)"], 0.7525806571450242)

````




```@docs
ScalarFactorReTerm
```

This `ScalarFactorReTerm` contributes a block of columns to the model matrix $\bf Z$ and a diagonal block to $\Lambda_\theta$.
````julia
julia> getθ(t1)
1-element Array{Float64,1}:
 0.752581

julia> getΛ(t1)
0.7525806571450242

julia> convert(Array{Int}, full(t1)) # matrix is floating point but all integers
30×6 Array{Int64,2}:
 1  0  0  0  0  0
 1  0  0  0  0  0
 1  0  0  0  0  0
 1  0  0  0  0  0
 1  0  0  0  0  0
 0  1  0  0  0  0
 0  1  0  0  0  0
 0  1  0  0  0  0
 0  1  0  0  0  0
 0  1  0  0  0  0
 ⋮              ⋮
 0  0  0  0  1  0
 0  0  0  0  1  0
 0  0  0  0  1  0
 0  0  0  0  1  0
 0  0  0  0  0  1
 0  0  0  0  0  1
 0  0  0  0  0  1
 0  0  0  0  0  1
 0  0  0  0  0  1

````





Because there is only one random-effects term in the model, the matrix $\bf Z$ is the indicators matrix shown as the result of `full(t1)`, but stored in a special sparse format.
Furthermore, there is only one block in $\Lambda_\theta$.
For a `ScalarFactorReTerm` this block is a multiple of the identity, in this case $0.75258\cdot{\bf I}_6$.


For a vector-valued random-effects term, as in
````julia
julia> fm2 = fit(LinearMixedModel, @formula(Y ~ 1 + U + (1+U|G)), dat[:sleepstudy])
Linear mixed model fit by maximum likelihood
 Formula: Y ~ 1 + U + ((1 + U) | G)
   logLik   -2 logLik     AIC        BIC    
 -875.96967 1751.93934 1763.93934 1783.09709

Variance components:
              Column    Variance  Std.Dev.   Corr.
 G        (Intercept)  565.51067 23.780468
          U             32.68212  5.716828  0.08
 Residual              654.94145 25.591824
 Number of obs: 180; levels of grouping factors: 18

  Fixed-effects parameters:
             Estimate Std.Error z value P(>|z|)
(Intercept)   251.405   6.63226 37.9064  <1e-99
U             10.4673   1.50224 6.96781  <1e-11


julia> t21 = fm2.trms[1]
MixedModels.VectorFactorReTerm{Float64,String,UInt8,2}(CategoricalArrays.CategoricalString{UInt8}["308", "308", "308", "308", "308", "308", "308", "308", "308", "308"  …  "372", "372", "372", "372", "372", "372", "372", "372", "372", "372"], [1.0 1.0 … 1.0 1.0; 0.0 1.0 … 8.0 9.0], [1.0 1.0 … 1.0 1.0; 0.0 1.0 … 8.0 9.0], StaticArrays.SArray{Tuple{2},Float64,1,2}[[1.0, 0.0], [1.0, 1.0], [1.0, 2.0], [1.0, 3.0], [1.0, 4.0], [1.0, 5.0], [1.0, 6.0], [1.0, 7.0], [1.0, 8.0], [1.0, 9.0]  …  [1.0, 0.0], [1.0, 1.0], [1.0, 2.0], [1.0, 3.0], [1.0, 4.0], [1.0, 5.0], [1.0, 6.0], [1.0, 7.0], [1.0, 8.0], [1.0, 9.0]], :G, String["(Intercept)", "U"], [2], [0.929221 0.0; 0.0181684 0.222645], [1, 2, 4])

````




the random-effects term `(1+U|G)` generates a
```@docs
VectorFactorReTerm
```
The model matrix $\bf Z$ for this model is
````julia
julia> convert(Array{Int}, full(t21))
180×36 Array{Int64,2}:
 1  0  0  0  0  0  0  0  0  0  0  0  0  …  0  0  0  0  0  0  0  0  0  0  0  0
 1  1  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  0  0
 1  2  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  0  0
 1  3  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  0  0
 1  4  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  0  0
 1  5  0  0  0  0  0  0  0  0  0  0  0  …  0  0  0  0  0  0  0  0  0  0  0  0
 1  6  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  0  0
 1  7  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  0  0
 1  8  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  0  0
 1  9  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  0  0
 ⋮              ⋮              ⋮        ⋱     ⋮              ⋮              ⋮
 0  0  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  1  1
 0  0  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  1  2
 0  0  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  1  3
 0  0  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  1  4
 0  0  0  0  0  0  0  0  0  0  0  0  0  …  0  0  0  0  0  0  0  0  0  0  1  5
 0  0  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  1  6
 0  0  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  1  7
 0  0  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  1  8
 0  0  0  0  0  0  0  0  0  0  0  0  0     0  0  0  0  0  0  0  0  0  0  1  9

````




and $\Lambda_\theta$ is a $36\times36$ block diagonal matrix with $18$ diagonal blocks, all of the form
````julia
julia> getΛ(t21)
2×2 LowerTriangular{Float64,Array{Float64,2}}:
 0.929221    ⋅      
 0.0181684  0.222645

````




The $\theta$ vector is
````julia
julia> getθ(t21)
3-element Array{Float64,1}:
 0.929221 
 0.0181684
 0.222645 

````





Random-effects terms in the model formula that have the same grouping factor are amagamated into a single `VectorFactorReTerm` object.
````julia
julia> fm3 = fit(LinearMixedModel, @formula(Y ~ 1 + U + (1|G) + (0+U|G)), dat[:sleepstudy])
Linear mixed model fit by maximum likelihood
 Formula: Y ~ 1 + U + (1 | G) + ((0 + U) | G)
   logLik   -2 logLik     AIC        BIC    
 -876.00163 1752.00326 1762.00326 1777.96804

Variance components:
              Column    Variance  Std.Dev.   Corr.
 G        (Intercept)  584.258973 24.17145
          U             33.632805  5.79938  0.00
 Residual              653.115782 25.55613
 Number of obs: 180; levels of grouping factors: 18

  Fixed-effects parameters:
             Estimate Std.Error z value P(>|z|)
(Intercept)   251.405   6.70771   37.48  <1e-99
U             10.4673   1.51931 6.88951  <1e-11


julia> t31 = fm3.trms[1]
MixedModels.VectorFactorReTerm{Float64,String,UInt8,2}(CategoricalArrays.CategoricalString{UInt8}["308", "308", "308", "308", "308", "308", "308", "308", "308", "308"  …  "372", "372", "372", "372", "372", "372", "372", "372", "372", "372"], [1.0 1.0 … 1.0 1.0; 0.0 1.0 … 8.0 9.0], [1.0 1.0 … 1.0 1.0; 0.0 1.0 … 8.0 9.0], StaticArrays.SArray{Tuple{2},Float64,1,2}[[1.0, 0.0], [1.0, 1.0], [1.0, 2.0], [1.0, 3.0], [1.0, 4.0], [1.0, 5.0], [1.0, 6.0], [1.0, 7.0], [1.0, 8.0], [1.0, 9.0]  …  [1.0, 0.0], [1.0, 1.0], [1.0, 2.0], [1.0, 3.0], [1.0, 4.0], [1.0, 5.0], [1.0, 6.0], [1.0, 7.0], [1.0, 8.0], [1.0, 9.0]], :G, String["(Intercept)", "U"], [1, 1], [0.945818 0.0; 0.0 0.226927], [1, 4])

````




For this model the matrix $\bf Z$ is the same as that of model `fm2` but the diagonal blocks of $\Lambda_\theta$ are themselves diagonal.
````julia
julia> getΛ(t31)
2×2 LowerTriangular{Float64,Array{Float64,2}}:
 0.945818   ⋅      
 0.0       0.226927

julia> getθ(t31)
2-element Array{Float64,1}:
 0.945818
 0.226927

````




Random-effects terms with distinct grouping factors generate distinct elements of the `trms` member of the `LinearMixedModel` object.
Multiple `AbstractFactorReTerm` (i.e. either a `ScalarFactorReTerm` or a `VectorFactorReTerm`) objects are sorted by decreasing numbers of random effects.
````julia
julia> fm4 = fit(LinearMixedModel, @formula(Y ~ 1 + (1|H) + (1|G)), dat[:Penicillin])
Linear mixed model fit by maximum likelihood
 Formula: Y ~ 1 + (1 | H) + (1 | G)
   logLik   -2 logLik     AIC        BIC    
 -166.09417  332.18835  340.18835  352.06760

Variance components:
              Column    Variance  Std.Dev. 
 G        (Intercept)  0.7149795 0.8455646
 H        (Intercept)  3.1351924 1.7706474
 Residual              0.3024264 0.5499331
 Number of obs: 144; levels of grouping factors: 24, 6

  Fixed-effects parameters:
             Estimate Std.Error z value P(>|z|)
(Intercept)   22.9722  0.744596 30.8519  <1e-99


julia> t41 = fm4.trms[1]
MixedModels.ScalarFactorReTerm{Float64,String,UInt8}(CategoricalArrays.CategoricalString{UInt8}["a", "a", "a", "a", "a", "a", "b", "b", "b", "b"  …  "w", "w", "w", "w", "x", "x", "x", "x", "x", "x"], [1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0  …  1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0], [1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0  …  1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0], :G, String["(Intercept)"], 1.5375772637253458)

julia> t42 = fm4.trms[2]
MixedModels.ScalarFactorReTerm{Float64,String,UInt8}(CategoricalArrays.CategoricalString{UInt8}["A", "B", "C", "D", "E", "F", "A", "B", "C", "D"  …  "C", "D", "E", "F", "A", "B", "C", "D", "E", "F"], [1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0  …  1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0], [1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0  …  1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0], :H, String["(Intercept)"], 3.219750701314265)

````




Note that the first `ScalarFactorReTerm` in `fm4.trms` corresponds to grouping factor `G` even though the term `(1|G)` occurs in the formula after `(1|H)`.

### Progress of the optimization

An optional `Bool` argument of `true` in the call to `fit!` of a `LinearMixedModel` causes printing of the objective and the $\theta$ parameter at each evaluation during the optimization.
````julia
julia> fit!(LinearMixedModel(@formula(Y ~ 1 + (1|G)), dat[:Dyestuff]), true);
f_1: 327.76702 [1.0]
f_2: 331.03619 [1.75]
f_3: 330.64583 [0.25]
f_4: 327.69511 [0.97619]
f_5: 327.56631 [0.928569]
f_6: 327.3826 [0.833327]
f_7: 327.35315 [0.807188]
f_8: 327.34663 [0.799688]
f_9: 327.341 [0.792188]
f_10: 327.33253 [0.777188]
f_11: 327.32733 [0.747188]
f_12: 327.32862 [0.739688]
f_13: 327.32706 [0.752777]
f_14: 327.32707 [0.753527]
f_15: 327.32706 [0.752584]
f_16: 327.32706 [0.752509]
f_17: 327.32706 [0.752591]
f_18: 327.32706 [0.752581]

julia> fit!(LinearMixedModel(@formula(Y ~ 1 + U + (1+U|G)), dat[:sleepstudy]), true);
f_1: 1784.6423 [1.0, 0.0, 1.0]
f_2: 1790.12564 [1.75, 0.0, 1.0]
f_3: 1798.99962 [1.0, 1.0, 1.0]
f_4: 1803.8532 [1.0, 0.0, 1.75]
f_5: 1800.61398 [0.25, 0.0, 1.0]
f_6: 1798.60463 [1.0, -1.0, 1.0]
f_7: 1752.26074 [1.0, 0.0, 0.25]
f_8: 1797.58769 [1.18326, -0.00866189, 0.0]
f_9: 1754.95411 [1.075, 0.0, 0.325]
f_10: 1753.69568 [0.816632, 0.0111673, 0.288238]
f_11: 1754.817 [1.0, -0.0707107, 0.196967]
f_12: 1753.10673 [0.943683, 0.0638354, 0.262696]
f_13: 1752.93938 [0.980142, -0.0266568, 0.274743]
f_14: 1752.25688 [0.984343, -0.0132347, 0.247191]
f_15: 1752.05745 [0.97314, 0.00253785, 0.23791]
f_16: 1752.02239 [0.954526, 0.00386421, 0.235892]
f_17: 1752.02273 [0.935929, 0.0013318, 0.234445]
f_18: 1751.97169 [0.954965, 0.00790664, 0.229046]
f_19: 1751.9526 [0.953313, 0.0166274, 0.225768]
f_20: 1751.94852 [0.946929, 0.0130761, 0.222871]
f_21: 1751.98718 [0.933418, 0.00613767, 0.218951]
f_22: 1751.98321 [0.951544, 0.005789, 0.220618]
f_23: 1751.95197 [0.952809, 0.0190332, 0.224178]
f_24: 1751.94628 [0.946322, 0.0153739, 0.225088]
f_25: 1751.9467 [0.947124, 0.0148894, 0.224892]
f_26: 1751.94757 [0.946497, 0.0154643, 0.225814]
f_27: 1751.94531 [0.946086, 0.0157934, 0.224449]
f_28: 1751.94418 [0.945304, 0.0166902, 0.223361]
f_29: 1751.94353 [0.944072, 0.0172106, 0.222716]
f_30: 1751.94244 [0.941271, 0.0163099, 0.222523]
f_31: 1751.94217 [0.939, 0.015899, 0.222132]
f_32: 1751.94237 [0.938979, 0.016548, 0.221562]
f_33: 1751.94228 [0.938863, 0.0152466, 0.222683]
f_34: 1751.9422 [0.938269, 0.015733, 0.222024]
f_35: 1751.94131 [0.938839, 0.0166373, 0.222611]
f_36: 1751.94093 [0.938397, 0.0173965, 0.222817]
f_37: 1751.94057 [0.937006, 0.0180445, 0.222534]
f_38: 1751.94018 [0.934109, 0.0187354, 0.22195]
f_39: 1751.94008 [0.932642, 0.0189242, 0.221726]
f_40: 1751.94027 [0.931357, 0.0190082, 0.221309]
f_41: 1751.9415 [0.932821, 0.0206454, 0.221367]
f_42: 1751.93949 [0.931867, 0.0179574, 0.222564]
f_43: 1751.93939 [0.929167, 0.0177824, 0.222534]
f_44: 1751.9394 [0.929659, 0.0177721, 0.222508]
f_45: 1751.93943 [0.929193, 0.0187806, 0.22257]
f_46: 1751.93935 [0.928986, 0.0182366, 0.222484]
f_47: 1751.93949 [0.928697, 0.0182937, 0.223175]
f_48: 1751.93936 [0.928243, 0.0182695, 0.222584]
f_49: 1751.93934 [0.929113, 0.0181791, 0.222624]
f_50: 1751.93934 [0.929191, 0.0181658, 0.222643]
f_51: 1751.93935 [0.929254, 0.0182093, 0.222621]
f_52: 1751.93935 [0.929189, 0.0181298, 0.222573]
f_53: 1751.93934 [0.929254, 0.0181676, 0.22265]
f_54: 1751.93934 [0.929215, 0.0181717, 0.222647]
f_55: 1751.93934 [0.929208, 0.0181715, 0.222646]
f_56: 1751.93934 [0.929209, 0.018173, 0.222652]
f_57: 1751.93934 [0.929221, 0.0181684, 0.222645]

````





A shorter summary of the optimization process is always available as an
```@docs
OptSummary
```
object, which is the `optsum` member of the `LinearMixedModel`.
````julia
julia> fm2.optsum
Initial parameter vector: [1.0, 0.0, 1.0]
Initial objective value:  1784.6422961924507

Optimizer (from NLopt):   LN_BOBYQA
Lower bounds:             [0.0, -Inf, 0.0]
ftol_rel:                 1.0e-12
ftol_abs:                 1.0e-8
xtol_rel:                 0.0
xtol_abs:                 [1.0e-10, 1.0e-10, 1.0e-10]
initial_step:             [0.75, 1.0, 0.75]
maxfeval:                 -1

Function evaluations:     57
Final parameter vector:   [0.929221, 0.0181684, 0.222645]
Final objective value:    1751.9393444646757
Return code:              FTOL_REACHED


````





### Modifying the optimization process

The `OptSummary` object contains both input and output fields for the optimizer.
To modify the optimization process the input fields can be changed after constructing the model but before fitting it.

Suppose, for example, that the user wishes to try a [Nelder-Mead](https://en.wikipedia.org/wiki/Nelder%E2%80%93Mead_method) optimization method instead of the default [`BOBYQA`](https://en.wikipedia.org/wiki/BOBYQA) (Bounded Optimization BY Quadratic Approximation) method.
````julia
julia> fm2 = LinearMixedModel(@formula(Y ~ 1 + U + (1+U|G)), dat[:sleepstudy]);

julia> fm2.optsum.optimizer = :LN_NELDERMEAD;

julia> fit!(fm2)
Linear mixed model fit by maximum likelihood
 Formula: Y ~ 1 + U + ((1 + U) | G)
   logLik   -2 logLik     AIC        BIC    
 -875.96967 1751.93934 1763.93934 1783.09709

Variance components:
              Column    Variance   Std.Dev.   Corr.
 G        (Intercept)  565.528831 23.780850
          U             32.681047  5.716734  0.08
 Residual              654.941678 25.591828
 Number of obs: 180; levels of grouping factors: 18

  Fixed-effects parameters:
             Estimate Std.Error z value P(>|z|)
(Intercept)   251.405   6.63233  37.906  <1e-99
U             10.4673   1.50222  6.9679  <1e-11


julia> fm2.optsum
Initial parameter vector: [1.0, 0.0, 1.0]
Initial objective value:  1784.6422961924507

Optimizer (from NLopt):   LN_NELDERMEAD
Lower bounds:             [0.0, -Inf, 0.0]
ftol_rel:                 1.0e-12
ftol_abs:                 1.0e-8
xtol_rel:                 0.0
xtol_abs:                 [1.0e-10, 1.0e-10, 1.0e-10]
initial_step:             [0.75, 1.0, 0.75]
maxfeval:                 -1

Function evaluations:     140
Final parameter vector:   [0.929236, 0.0181688, 0.222641]
Final objective value:    1751.9393444749974
Return code:              FTOL_REACHED


````





The parameter estimates are quite similar to those using `:LN_BOBYQA` but at the expense of 140 functions evaluations for `:LN_NELDERMEAD` versus 57 for `:LN_BOBYQA`.

See the documentation for the [`NLopt`](https://github.com/JuliaOpt/NLopt.jl) package for details about the various settings.

### Convergence to singular covariance matrices

To ensure identifiability of $\Sigma_\theta=\sigma^2\Lambda_\theta \Lambda_\theta$, the elements of $\theta$ corresponding to diagonal elements of $\Lambda_\theta$ are constrained to be non-negative.
For example, in a trivial case of a single, simple, scalar, random-effects term as in `fm1`, the one-dimensional $\theta$ vector is the ratio of the standard deviation of the random effects to the standard deviation of the response.
It happens that $-\theta$ produces the same log-likelihood but, by convention, we define the standard deviation to be the positive square root of the variance.
Requiring the diagonal elements of $\Lambda_\theta$ to be non-negative is a generalization of using this positive square root.

If the optimization converges on the boundary of the feasible region, that is if one or more of the diagonal elements of $\Lambda_\theta$ is zero at convergence, the covariance matrix $\Sigma_\theta$ will be *singular*.
This means that there will be linear combinations of random effects that are constant.
Usually convergence to a singular covariance matrix is a sign of an over-specified model.

## Generalized Linear Mixed-Effects Models

In a [*generalized linear model*](https://en.wikipedia.org/wiki/Generalized_linear_model) the responses are modelled as coming from a particular distribution, such as `Bernoulli` for binary responses or `Poisson` for responses that represent counts.
The scalar distributions of individual responses differ only in their means, which are determined by a *linear predictor* expression $\eta=\bf X\beta$, where, as before, $\bf X$ is a model matrix derived from the values of covariates and $\beta$ is a vector of coefficients.

The unconstrained components of $\eta$ are mapped to the, possiby constrained, components of the mean response, $\mu$, via a scalar function, $g^{-1}$, applied to each component of $\eta$.
For historical reasons, the inverse of this function, taking components of $\mu$ to the corresponding component of $\eta$ is called the *link function* and more frequently used map from $\eta$ to $\mu$ is the *inverse link*.

A *generalized linear mixed-effects model* (GLMM) is defined, for the purposes of this package, by
\begin{equation}
\begin{aligned}
  (\mathcal{Y} | \mathcal{B}=\bf{b}) &\sim\mathcal{D}(\bf{g^{-1}(X\beta + Z b)},\phi)\\\\
  \mathcal{B}&\sim\mathcal{N}(\bf{0},\Sigma_\theta) .
\end{aligned}
\end{equation}
where $\mathcal{D}$ indicates the distribution family parameterized by the mean and, when needed, a common scale parameter, $\phi$.
(There is no scale parameter for `Bernoulli` or for `Poisson`.
Specifying the mean completely determines the distribution.)
```@docs
Bernoulli
Poisson
```


The `glmm` function generates, but does not fit, a `GeneralizedLinearMixedModel` object.

````julia
julia> mdl = GeneralizedLinearMixedModel(@formula(r2 ~ 1 + a + g + b + s + (1|id) + (1|item)),
           dat[:VerbAgg], Bernoulli());

julia> typeof(mdl)
MixedModels.GeneralizedLinearMixedModel{Float64}

````





A separate call to `fit!` is required to fit the model.
This involves optimizing an objective function, the Laplace approximation to the deviance, with respect to the parameters, which are $\beta$, the fixed-effects coefficients, and $\theta$, the covariance parameters.
The starting estimate for $\beta$ is determined by fitting a GLM to the fixed-effects part of the formula

````julia
julia> mdl.β
6-element Array{Float64,1}:
  0.0399404
 -0.776656 
 -0.794186 
  0.231317 
 -1.53919  
  0.206053 

````





and the starting estimate for $\theta$, which is a vector of the two standard deviations of the random effects, is chosen to be

````julia
julia> mdl.θ
2-element Array{Float64,1}:
 1.0
 1.0

````





The Laplace approximation to the deviance requires determining the conditional modes of the random effects.
These are the values that maximize the conditional density of the random effects, given the model parameters and the data.
This is done using Penalized Iteratively Reweighted Least Squares (PIRLS).
In most cases PIRLS is fast and stable.
It is simply a penalized version of the IRLS algorithm used in fitting GLMs.

The distinction between the "fast" and "slow" algorithms in the `MixedModels` package (`nAGQ=0` or `nAGQ=1` in `lme4`) is whether the fixed-effects parameters, $\beta$, are optimized in PIRLS or in the nonlinear optimizer.
In a call to the `pirls!` function the first argument is a `GeneralizedLinearMixedModel`, which is modified during the function call.
(By convention, the names of such *mutating functions* end in `!` as a warning to the user that they can modify an argument, usually the first argument.)
The second and third arguments are optional logical values indicating if $\beta$ is to be varied and if verbose output is to be printed.

````julia
julia> pirls!(mdl, true, true)
varyβ = true
obj₀ = 10210.853438905406
β = [0.0399404, -0.776656, -0.794186, 0.231317, -1.53919, 0.206053]
iter = 1
obj = 8301.483049027265
iter = 2
obj = 8205.604285133919
iter = 3
obj = 8201.89659746689
iter = 4
obj = 8201.848598910707
iter = 5
obj = 8201.848559060705
Generalized Linear Mixed Model fit by maximum likelihood
  Formula: r2 ~ 1 + a + g + b + s + (1 | id) + (1 | item)
  Distribution: Distributions.Bernoulli{Float64}
  Link: GLM.LogitLink()

  Deviance: 8201.8486

Variance components:
          Column   Variance Std.Dev. 
 id   (Intercept)         1        1
 item (Intercept)         1        1

 Number of obs: 7584; levels of grouping factors: 316, 24

Fixed-effects parameters:
              Estimate Std.Error  z value P(>|z|)
(Intercept)   0.218535  0.491968 0.444206  0.6569
a            0.0514385 0.0130432  3.94371   <1e-4
g: M          0.290225  0.148818   1.9502  0.0512
b: scold     -0.979124  0.504402 -1.94116  0.0522
b: shout      -1.95402  0.505235 -3.86754  0.0001
s: self      -0.979493  0.412168 -2.37644  0.0175


````



````julia
julia> deviance(mdl)
8201.848559060705

````



````julia
julia> mdl.β
6-element Array{Float64,1}:
  0.0514385
 -0.979493 
 -0.979124 
  0.290225 
 -1.95402  
  0.218535 

````



````julia
julia> mdl.θ # current values of the standard deviations of the random effects
2-element Array{Float64,1}:
 1.0
 1.0

````





If the optimization with respect to $\beta$ is performed within PIRLS then the nonlinear optimization of the Laplace approximation to the deviance requires optimization with respect to $\theta$ only.
This is the "fast" algorithm.
Given a value of $\theta$, PIRLS is used to determine the conditional estimate of $\beta$ and the conditional mode of the random effects, **b**.

````julia
julia> mdl.b # conditional modes of b
2-element Array{Array{Float64,2},1}:
 [-0.600772 -1.93227 … -0.144554 -0.575224]
 [-0.186364 0.180552 … 0.282092 -0.221974] 

````



````julia
julia> fit!(mdl, fast=true, verbose=true);
f_1: 8201.84856 [1.0, 1.0]
f_2: 8190.11782 [1.75, 1.0]
f_3: 8224.45098 [1.0, 1.75]
f_4: 9026.00391 [0.25, 1.0]
f_5: 8205.79378 [1.0, 0.25]
f_6: 8157.04103 [1.38583, 0.736457]
f_7: 8367.72422 [1.33715, 0.0]
f_8: 8170.28883 [1.41365, 1.11042]
f_9: 8158.82932 [1.27225, 0.762811]
f_10: 8161.93341 [1.40936, 0.868084]
f_11: 8156.30098 [1.32694, 0.721015]
f_12: 8156.11668 [1.32365, 0.714275]
f_13: 8156.00207 [1.31847, 0.708856]
f_14: 8155.75359 [1.32072, 0.701702]
f_15: 8155.27522 [1.32636, 0.687802]
f_16: 8154.41 [1.33859, 0.660408]
f_17: 8153.39496 [1.37582, 0.613358]
f_18: 8152.74094 [1.39515, 0.563096]
f_19: 8151.76473 [1.36763, 0.509124]
f_20: 8152.80894 [1.26776, 0.475123]
f_21: 8152.86642 [1.4148, 0.471099]
f_22: 8151.76959 [1.32589, 0.527523]
f_23: 8151.73776 [1.36681, 0.498606]
f_24: 8151.58516 [1.33974, 0.493492]
f_25: 8151.60206 [1.33758, 0.486311]
f_26: 8151.6005 [1.34692, 0.491348]
f_27: 8151.58338 [1.33958, 0.497337]
f_28: 8151.58359 [1.33927, 0.49802]
f_29: 8151.58335 [1.33972, 0.496955]
f_30: 8151.58351 [1.34041, 0.497255]
f_31: 8151.5834 [1.33957, 0.49622]
f_32: 8151.58348 [1.34031, 0.496495]
f_33: 8151.58334 [1.33956, 0.496833]
f_34: 8151.58334 [1.33953, 0.496902]
f_35: 8151.58334 [1.33963, 0.496867]
f_36: 8151.58334 [1.33963, 0.496803]
f_37: 8151.58334 [1.33956, 0.496833]

````





The optimization process is summarized by

````julia
julia> mdl.LMM.optsum
Initial parameter vector: [1.0, 1.0]
Initial objective value:  8201.848559060627

Optimizer (from NLopt):   LN_BOBYQA
Lower bounds:             [0.0, 0.0]
ftol_rel:                 1.0e-12
ftol_abs:                 1.0e-8
xtol_rel:                 0.0
xtol_abs:                 [1.0e-10, 1.0e-10]
initial_step:             [0.75, 0.75]
maxfeval:                 -1

Function evaluations:     37
Final parameter vector:   [1.33956, 0.496833]
Final objective value:    8151.583340132134
Return code:              FTOL_REACHED


````





As one would hope, given the name of the option, this fit is comparatively fast.
````julia
julia> @time(fit!(GeneralizedLinearMixedModel(@formula(r2 ~ 1 + a + g + b + s + (1 | id) + (1 | item)), 
        dat[:VerbAgg], Bernoulli()), fast=true))
  0.263251 seconds (44.23 k allocations: 8.835 MiB, 3.48% gc time)
Generalized Linear Mixed Model fit by maximum likelihood
  Formula: r2 ~ 1 + a + g + b + s + (1 | id) + (1 | item)
  Distribution: Distributions.Bernoulli{Float64}
  Link: GLM.LogitLink()

  Deviance: 8151.5833

Variance components:
          Column    Variance   Std.Dev. 
 id   (Intercept)  1.79443144 1.3395639
 item (Intercept)  0.24684282 0.4968328

 Number of obs: 7584; levels of grouping factors: 316, 24

Fixed-effects parameters:
              Estimate Std.Error  z value P(>|z|)
(Intercept)   0.208273  0.405425 0.513715  0.6075
a            0.0543791 0.0167533  3.24587  0.0012
g: M          0.304089  0.191223  1.59023  0.1118
b: scold       -1.0165  0.257531 -3.94708   <1e-4
b: shout       -2.0218  0.259235 -7.79912  <1e-14
s: self       -1.01344  0.210888 -4.80559   <1e-5


````





The alternative algorithm is to use PIRLS to find the conditional mode of the random effects, given $\beta$ and $\theta$ and then use the general nonlinear optimizer to fit with respect to both $\beta$ and $\theta$.
Because it is slower to incorporate the $\beta$ parameters in the general nonlinear optimization, the fast fit is performed first and used to determine starting estimates for the more general optimization.

````julia
julia> @time mdl1 = fit!(GeneralizedLinearMixedModel(@formula(r2 ~ 1+a+g+b+s+(1|id)+(1|item)), 
        dat[:VerbAgg], Bernoulli()), verbose = true)
f_1: 8201.84856 [1.0, 1.0]
f_2: 8190.11782 [1.75, 1.0]
f_3: 8224.45098 [1.0, 1.75]
f_4: 9026.00391 [0.25, 1.0]
f_5: 8205.79378 [1.0, 0.25]
f_6: 8157.04103 [1.38583, 0.736457]
f_7: 8367.72422 [1.33715, 0.0]
f_8: 8170.28883 [1.41365, 1.11042]
f_9: 8158.82932 [1.27225, 0.762811]
f_10: 8161.93341 [1.40936, 0.868084]
f_11: 8156.30098 [1.32694, 0.721015]
f_12: 8156.11668 [1.32365, 0.714275]
f_13: 8156.00207 [1.31847, 0.708856]
f_14: 8155.75359 [1.32072, 0.701702]
f_15: 8155.27522 [1.32636, 0.687802]
f_16: 8154.41 [1.33859, 0.660408]
f_17: 8153.39496 [1.37582, 0.613358]
f_18: 8152.74094 [1.39515, 0.563096]
f_19: 8151.76473 [1.36763, 0.509124]
f_20: 8152.80894 [1.26776, 0.475123]
f_21: 8152.86642 [1.4148, 0.471099]
f_22: 8151.76959 [1.32589, 0.527523]
f_23: 8151.73776 [1.36681, 0.498606]
f_24: 8151.58516 [1.33974, 0.493492]
f_25: 8151.60206 [1.33758, 0.486311]
f_26: 8151.6005 [1.34692, 0.491348]
f_27: 8151.58338 [1.33958, 0.497337]
f_28: 8151.58359 [1.33927, 0.49802]
f_29: 8151.58335 [1.33972, 0.496955]
f_30: 8151.58351 [1.34041, 0.497255]
f_31: 8151.5834 [1.33957, 0.49622]
f_32: 8151.58348 [1.34031, 0.496495]
f_33: 8151.58334 [1.33956, 0.496833]
f_34: 8151.58334 [1.33953, 0.496902]
f_35: 8151.58334 [1.33963, 0.496867]
f_36: 8151.58334 [1.33963, 0.496803]
f_37: 8151.58334 [1.33956, 0.496833]
f_1: 8151.58334 [0.0543791, -1.01344, -1.0165, 0.304089, -2.0218, 0.208273, 1.33956, 0.496833]
f_2: 8648.50534 [0.189521, -1.01344, -1.0165, 0.304089, -2.0218, 0.208273, 1.33956, 0.496833]
f_3: 8151.59709 [0.0543791, -1.00786, -1.0165, 0.304089, -2.0218, 0.208273, 1.33956, 0.496833]
f_4: 8151.71186 [0.0543791, -1.01344, -0.952756, 0.304089, -2.0218, 0.208273, 1.33956, 0.496833]
f_5: 8151.75117 [0.0543791, -1.01344, -1.0165, 0.389933, -2.0218, 0.208273, 1.33956, 0.496833]
f_6: 8152.01341 [0.0543791, -1.01344, -1.0165, 0.304089, -1.93539, 0.208273, 1.33956, 0.496833]
f_7: 8151.93507 [0.0543791, -1.01344, -1.0165, 0.304089, -2.0218, 0.278569, 1.33956, 0.496833]
f_8: 8152.13673 [0.0543791, -1.01344, -1.0165, 0.304089, -2.0218, 0.208273, 1.38956, 0.496833]
f_9: 8151.94237 [0.0543791, -1.01344, -1.0165, 0.304089, -2.0218, 0.208273, 1.33956, 0.546833]
f_10: 8650.53785 [-0.0807627, -1.01344, -1.0165, 0.304089, -2.0218, 0.208273, 1.33956, 0.496833]
f_11: 8151.57191 [0.0543791, -1.01903, -1.0165, 0.304089, -2.0218, 0.208273, 1.33956, 0.496833]
f_12: 8151.67196 [0.0543791, -1.01344, -1.08024, 0.304089, -2.0218, 0.208273, 1.33956, 0.496833]
f_13: 8151.8632 [0.0543791, -1.01344, -1.0165, 0.218245, -2.0218, 0.208273, 1.33956, 0.496833]
f_14: 8151.54498 [0.0543791, -1.01344, -1.0165, 0.304089, -2.10821, 0.208273, 1.33956, 0.496833]
f_15: 8151.81772 [0.0543791, -1.01344, -1.0165, 0.304089, -2.0218, 0.137977, 1.33956, 0.496833]
f_16: 8152.10285 [0.0543791, -1.01344, -1.0165, 0.304089, -2.0218, 0.208273, 1.28956, 0.496833]
f_17: 8152.03137 [0.0543791, -1.01344, -1.0165, 0.304089, -2.0218, 0.208273, 1.33956, 0.446833]
f_18: 8151.50037 [0.0545782, -1.01504, -1.0264, 0.313121, -2.07246, 0.203182, 1.33926, 0.500674]
f_19: 8151.49983 [0.054508, -1.02, -1.0132, 0.312539, -2.0735, 0.204669, 1.33929, 0.497885]
f_20: 8151.50032 [0.054517, -1.02018, -1.02354, 0.314584, -2.07166, 0.201517, 1.33882, 0.499721]
f_21: 8151.50444 [0.0545195, -1.02059, -1.0129, 0.313297, -2.0859, 0.204346, 1.33927, 0.49798]
f_22: 8151.49937 [0.0545436, -1.02093, -1.01601, 0.313368, -2.07208, 0.202343, 1.33887, 0.499223]
f_23: 8151.50045 [0.0545177, -1.01994, -1.01714, 0.313572, -2.07284, 0.202423, 1.33883, 0.498984]
f_24: 8151.50381 [0.0545532, -1.02136, -1.01598, 0.308902, -2.07501, 0.201997, 1.33883, 0.499362]
f_25: 8151.49031 [0.0545123, -1.02098, -1.01699, 0.31816, -2.07144, 0.207295, 1.34, 0.49761]
f_26: 8151.48235 [0.0545189, -1.02186, -1.02069, 0.324514, -2.07139, 0.212551, 1.34059, 0.49643]
f_27: 8151.48046 [0.0545088, -1.02404, -1.02192, 0.329026, -2.07094, 0.216693, 1.34136, 0.495824]
f_28: 8151.49589 [0.0544553, -1.02569, -1.0114, 0.319052, -2.07151, 0.229, 1.34085, 0.493857]
f_29: 8151.48049 [0.0544842, -1.02401, -1.03039, 0.325883, -2.07585, 0.219114, 1.3435, 0.489754]
f_30: 8151.47 [0.0545297, -1.02415, -1.0335, 0.323453, -2.07385, 0.21891, 1.34163, 0.494977]
f_31: 8151.4656 [0.0545298, -1.02607, -1.04547, 0.323077, -2.07396, 0.223992, 1.3416, 0.496608]
f_32: 8151.46346 [0.054515, -1.02725, -1.04366, 0.323062, -2.07649, 0.224199, 1.3424, 0.4963]
f_33: 8151.46064 [0.0545172, -1.0296, -1.0498, 0.324585, -2.07866, 0.22663, 1.34282, 0.495188]
f_34: 8151.45792 [0.0545172, -1.03425, -1.0543, 0.328765, -2.07886, 0.231375, 1.34301, 0.494065]
f_35: 8151.46191 [0.0545405, -1.03411, -1.06293, 0.331515, -2.08506, 0.234, 1.34498, 0.494754]
f_36: 8151.45368 [0.0544765, -1.03428, -1.05227, 0.331443, -2.08572, 0.230015, 1.34124, 0.493651]
f_37: 8151.45047 [0.0545181, -1.03465, -1.05513, 0.331025, -2.08911, 0.230889, 1.3401, 0.493283]
f_38: 8151.45792 [0.0545193, -1.03499, -1.0651, 0.330483, -2.08697, 0.233031, 1.33652, 0.491913]
f_39: 8151.45431 [0.0546115, -1.03466, -1.05458, 0.331301, -2.08906, 0.23223, 1.34151, 0.488605]
f_40: 8151.45292 [0.0544794, -1.03469, -1.05531, 0.323467, -2.09262, 0.229783, 1.34089, 0.492875]
f_41: 8151.87963 [0.0512051, -1.03517, -1.05608, 0.331686, -2.09076, 0.231444, 1.34012, 0.493196]
f_42: 8151.43234 [0.0551766, -1.03462, -1.05497, 0.329654, -2.0968, 0.232664, 1.33893, 0.494489]
f_43: 8151.43565 [0.0552215, -1.0345, -1.05509, 0.329481, -2.11109, 0.240377, 1.33829, 0.494463]
f_44: 8155.30807 [0.0432737, -1.03448, -1.05501, 0.328253, -2.0956, 0.234593, 1.338, 0.494402]
f_45: 8151.43826 [0.0552043, -1.03449, -1.05376, 0.326916, -2.09543, 0.23813, 1.33689, 0.49494]
f_46: 8151.45587 [0.0559145, -1.03484, -1.05396, 0.329706, -2.09598, 0.235786, 1.34204, 0.496884]
f_47: 8151.43165 [0.0551981, -1.03433, -1.05689, 0.3307, -2.10156, 0.229931, 1.33696, 0.494296]
f_48: 8151.43112 [0.0552183, -1.03484, -1.05788, 0.331358, -2.10354, 0.230467, 1.33689, 0.49547]
f_49: 8151.43231 [0.0552073, -1.0349, -1.05349, 0.330751, -2.1076, 0.233586, 1.33741, 0.494069]
f_50: 8151.45399 [0.0544731, -1.03496, -1.06045, 0.326005, -2.10231, 0.232486, 1.33761, 0.492848]
f_51: 8151.43594 [0.0552048, -1.03468, -1.05685, 0.335718, -2.10875, 0.23221, 1.33744, 0.497679]
f_52: 8151.43091 [0.0552398, -1.03539, -1.05894, 0.332055, -2.10342, 0.231034, 1.33682, 0.495435]
f_53: 8151.43914 [0.0552268, -1.03543, -1.0567, 0.332696, -2.10198, 0.229357, 1.33354, 0.498386]
f_54: 8151.4329 [0.0552282, -1.03536, -1.05944, 0.335579, -2.10401, 0.229742, 1.33769, 0.495134]
f_55: 8151.43083 [0.0552882, -1.03548, -1.05867, 0.332092, -2.10469, 0.230828, 1.33626, 0.495482]
f_56: 8151.43055 [0.0552365, -1.03549, -1.05928, 0.331588, -2.10584, 0.231298, 1.33686, 0.49603]
f_57: 8151.43198 [0.0552411, -1.03552, -1.05942, 0.332221, -2.10659, 0.232199, 1.33602, 0.49578]
f_58: 8151.42966 [0.0557064, -1.03553, -1.05945, 0.331719, -2.10625, 0.231169, 1.33682, 0.496094]
f_59: 8151.42851 [0.0554528, -1.03557, -1.05986, 0.331697, -2.10594, 0.231074, 1.33687, 0.496066]
f_60: 8151.42839 [0.0555138, -1.0356, -1.05953, 0.33188, -2.10607, 0.2312, 1.33715, 0.496169]
f_61: 8151.46056 [0.0544336, -1.0356, -1.05938, 0.331753, -2.1064, 0.230981, 1.33709, 0.496243]
f_62: 8151.4277 [0.0555266, -1.0356, -1.05998, 0.33156, -2.10635, 0.230942, 1.3373, 0.496239]
f_63: 8151.42648 [0.0554859, -1.0356, -1.06048, 0.330806, -2.10719, 0.230841, 1.33784, 0.495883]
f_64: 8151.42472 [0.0555437, -1.03561, -1.06169, 0.329449, -2.10909, 0.231155, 1.33892, 0.495714]
f_65: 8151.42438 [0.0555745, -1.03563, -1.06189, 0.329677, -2.11233, 0.230282, 1.33909, 0.495661]
f_66: 8151.42423 [0.0555857, -1.03575, -1.06329, 0.329038, -2.11139, 0.231788, 1.33938, 0.495889]
f_67: 8151.42468 [0.0555866, -1.03565, -1.06386, 0.328694, -2.11339, 0.231923, 1.34035, 0.49668]
f_68: 8151.52036 [0.0575381, -1.03578, -1.06311, 0.329412, -2.11122, 0.231281, 1.33981, 0.496044]
f_69: 8151.42417 [0.0556593, -1.03579, -1.06285, 0.329495, -2.11129, 0.231168, 1.34004, 0.496034]
f_70: 8151.42392 [0.0556741, -1.03583, -1.06311, 0.329496, -2.11129, 0.230856, 1.3401, 0.496087]
f_71: 8151.42344 [0.0556728, -1.03584, -1.06307, 0.329007, -2.11147, 0.23055, 1.33986, 0.496316]
f_72: 8151.42329 [0.0556642, -1.03584, -1.06336, 0.329126, -2.11155, 0.230107, 1.33959, 0.496462]
f_73: 8151.4226 [0.0556962, -1.03584, -1.06341, 0.328534, -2.11161, 0.229798, 1.3396, 0.496186]
f_74: 8151.42214 [0.0556788, -1.03586, -1.06337, 0.32789, -2.11175, 0.229521, 1.33956, 0.495996]
f_75: 8151.42127 [0.0557001, -1.03589, -1.06384, 0.326706, -2.11175, 0.229022, 1.33996, 0.495754]
f_76: 8151.42134 [0.0557081, -1.03591, -1.06517, 0.325026, -2.1119, 0.229019, 1.34133, 0.495869]
f_77: 8151.42073 [0.0558933, -1.03594, -1.06329, 0.326988, -2.1118, 0.228083, 1.34016, 0.495953]
f_78: 8151.42066 [0.0557229, -1.03602, -1.06298, 0.326817, -2.11189, 0.227221, 1.34015, 0.495874]
f_79: 8151.42065 [0.0557173, -1.03606, -1.06324, 0.326733, -2.11236, 0.226971, 1.3401, 0.495844]
f_80: 8151.42043 [0.0557694, -1.03603, -1.06345, 0.326995, -2.11267, 0.226739, 1.34033, 0.495984]
f_81: 8151.42058 [0.0557613, -1.03603, -1.06325, 0.326651, -2.11314, 0.227038, 1.34038, 0.496236]
f_82: 8151.42705 [0.0564142, -1.03603, -1.06342, 0.327487, -2.11295, 0.226829, 1.34005, 0.495978]
f_83: 8151.42031 [0.0558631, -1.03603, -1.06336, 0.327669, -2.11298, 0.226966, 1.34017, 0.495925]
f_84: 8151.42008 [0.0558859, -1.03607, -1.0637, 0.327563, -2.11316, 0.226736, 1.34026, 0.495989]
f_85: 8151.42002 [0.0558764, -1.03607, -1.06381, 0.327599, -2.11298, 0.226551, 1.33994, 0.49632]
f_86: 8151.42342 [0.0555816, -1.03608, -1.06348, 0.327662, -2.1135, 0.22619, 1.33985, 0.496281]
f_87: 8151.41968 [0.0559332, -1.03608, -1.06386, 0.327037, -2.11334, 0.226742, 1.3398, 0.496076]
f_88: 8151.4193 [0.0559456, -1.0361, -1.06313, 0.325843, -2.11297, 0.226822, 1.34009, 0.496043]
f_89: 8151.41798 [0.056063, -1.03619, -1.06228, 0.324284, -2.11197, 0.225275, 1.34006, 0.495278]
f_90: 8151.41955 [0.0560946, -1.03629, -1.06073, 0.325271, -2.11128, 0.225441, 1.34072, 0.494381]
f_91: 8151.41829 [0.0560013, -1.03613, -1.06187, 0.324904, -2.11124, 0.225576, 1.33958, 0.495508]
f_92: 8151.41725 [0.0560237, -1.03622, -1.06226, 0.324119, -2.11132, 0.224283, 1.34011, 0.494755]
f_93: 8151.4173 [0.0560095, -1.03628, -1.06132, 0.323768, -2.1114, 0.224189, 1.34015, 0.494377]
f_94: 8151.42557 [0.0554622, -1.0362, -1.0622, 0.32363, -2.11109, 0.224386, 1.34034, 0.494731]
f_95: 8151.41725 [0.0560035, -1.03625, -1.06222, 0.324521, -2.11115, 0.224055, 1.34022, 0.494983]
f_96: 8151.41741 [0.0561186, -1.03628, -1.06234, 0.32422, -2.11065, 0.224209, 1.33995, 0.495029]
f_97: 8151.41726 [0.0560459, -1.03628, -1.06243, 0.324698, -2.11129, 0.22406, 1.34006, 0.494657]
f_98: 8151.41692 [0.0560307, -1.03629, -1.06224, 0.324193, -2.11108, 0.223659, 1.34029, 0.49494]
f_99: 8151.41676 [0.0560885, -1.03632, -1.06214, 0.323753, -2.11096, 0.223363, 1.34046, 0.494736]
f_100: 8151.41636 [0.0560858, -1.03633, -1.06196, 0.323665, -2.11103, 0.222935, 1.34014, 0.494851]
f_101: 8151.41587 [0.0561461, -1.03642, -1.06179, 0.323335, -2.11141, 0.222202, 1.33993, 0.494659]
f_102: 8151.41615 [0.056164, -1.03655, -1.06077, 0.323981, -2.1098, 0.221461, 1.34075, 0.49452]
f_103: 8151.48685 [0.0545185, -1.03641, -1.06158, 0.322679, -2.11144, 0.22187, 1.34031, 0.495063]
f_104: 8151.41544 [0.0561525, -1.03642, -1.0615, 0.322023, -2.1117, 0.221586, 1.34031, 0.494626]
f_105: 8151.41491 [0.0562262, -1.03648, -1.06144, 0.321169, -2.11111, 0.220881, 1.34004, 0.494419]
f_106: 8151.41437 [0.0562815, -1.03654, -1.0603, 0.319425, -2.11078, 0.219262, 1.33991, 0.493741]
f_107: 8151.4149 [0.0563288, -1.0367, -1.05885, 0.318641, -2.11044, 0.219879, 1.33992, 0.49421]
f_108: 8151.41486 [0.0560723, -1.03654, -1.05998, 0.32015, -2.11031, 0.218575, 1.33954, 0.494285]
f_109: 8151.41391 [0.0562802, -1.03656, -1.06011, 0.319748, -2.10972, 0.21824, 1.33995, 0.493773]
f_110: 8151.41378 [0.056297, -1.0366, -1.05933, 0.319327, -2.10964, 0.217451, 1.33972, 0.493485]
f_111: 8151.41399 [0.0562331, -1.03655, -1.05881, 0.318412, -2.1086, 0.217672, 1.33966, 0.493544]
f_112: 8151.42906 [0.0570588, -1.03661, -1.05962, 0.318999, -2.10956, 0.217435, 1.33948, 0.493507]
f_113: 8151.41361 [0.0562857, -1.03662, -1.05965, 0.318908, -2.10963, 0.217353, 1.33942, 0.493537]
f_114: 8151.41363 [0.0563045, -1.0366, -1.05978, 0.318282, -2.10973, 0.217122, 1.33928, 0.493356]
f_115: 8151.41426 [0.0561663, -1.03664, -1.05983, 0.318624, -2.10948, 0.217447, 1.33977, 0.493446]
f_116: 8151.4138 [0.056308, -1.03664, -1.05942, 0.318636, -2.10937, 0.217414, 1.33923, 0.493239]
f_117: 8151.41354 [0.0562762, -1.0366, -1.05983, 0.318967, -2.10945, 0.216797, 1.33931, 0.493389]
f_118: 8151.4131 [0.0563303, -1.03662, -1.05998, 0.318764, -2.10924, 0.216421, 1.33946, 0.49368]
f_119: 8151.41302 [0.0562673, -1.03665, -1.06058, 0.318238, -2.10895, 0.215753, 1.33978, 0.494137]
f_120: 8151.41229 [0.0563871, -1.03672, -1.05997, 0.318098, -2.1089, 0.214878, 1.33953, 0.494105]
f_121: 8151.4117 [0.0564758, -1.03684, -1.05871, 0.318796, -2.10933, 0.213101, 1.33941, 0.494194]
f_122: 8151.41153 [0.0564932, -1.03689, -1.0583, 0.319119, -2.10813, 0.211011, 1.33896, 0.493345]
f_123: 8151.41133 [0.0565833, -1.03699, -1.05719, 0.317024, -2.10771, 0.209756, 1.33863, 0.49342]
f_124: 8151.4104 [0.0566871, -1.03709, -1.05881, 0.317071, -2.10853, 0.208503, 1.33932, 0.493672]
f_125: 8151.40958 [0.0567574, -1.03723, -1.05924, 0.318809, -2.10918, 0.207438, 1.33968, 0.494355]
f_126: 8151.40907 [0.0568832, -1.03754, -1.06002, 0.321422, -2.11169, 0.204906, 1.34026, 0.494674]
f_127: 8151.40817 [0.0571325, -1.03781, -1.06046, 0.322034, -2.11042, 0.200733, 1.34057, 0.495421]
f_128: 8151.40772 [0.0572732, -1.03803, -1.0589, 0.322074, -2.11004, 0.197259, 1.34038, 0.494812]
f_129: 8151.40793 [0.057342, -1.03813, -1.05892, 0.322816, -2.11044, 0.196315, 1.34063, 0.495083]
f_130: 8151.62671 [0.0600344, -1.03802, -1.05884, 0.321296, -2.11045, 0.197076, 1.34091, 0.494785]
f_131: 8151.40736 [0.0571884, -1.03806, -1.05916, 0.322769, -2.10964, 0.197477, 1.33925, 0.494979]
f_132: 8151.41171 [0.0575427, -1.03807, -1.05904, 0.323141, -2.10912, 0.197701, 1.3394, 0.494819]
f_133: 8151.4074 [0.0571629, -1.03802, -1.05915, 0.322866, -2.10994, 0.197018, 1.33934, 0.494985]
f_134: 8151.40731 [0.0571779, -1.03806, -1.05966, 0.32269, -2.10958, 0.197363, 1.33928, 0.494695]
f_135: 8151.40725 [0.0572115, -1.03807, -1.05966, 0.322025, -2.11001, 0.197252, 1.33939, 0.494828]
f_136: 8151.40751 [0.0570767, -1.03806, -1.05977, 0.321831, -2.10936, 0.197013, 1.33943, 0.495063]
f_137: 8151.40711 [0.0572025, -1.03807, -1.05961, 0.321806, -2.10966, 0.196888, 1.33959, 0.495109]
f_138: 8151.40698 [0.0572335, -1.03811, -1.05921, 0.320853, -2.10935, 0.196065, 1.3399, 0.495222]
f_139: 8151.40699 [0.0572984, -1.03817, -1.05807, 0.318895, -2.10877, 0.194378, 1.3401, 0.495165]
f_140: 8151.41169 [0.0576263, -1.0382, -1.0591, 0.320742, -2.10901, 0.196797, 1.33998, 0.49536]
f_141: 8151.4069 [0.05727, -1.03814, -1.05895, 0.320282, -2.10912, 0.194842, 1.33994, 0.495215]
f_142: 8151.40674 [0.0573619, -1.03827, -1.05885, 0.320452, -2.10896, 0.192601, 1.34006, 0.49527]
f_143: 8151.4066 [0.0575386, -1.03856, -1.0599, 0.322492, -2.10917, 0.188817, 1.33981, 0.495251]
f_144: 8151.40665 [0.0575904, -1.03859, -1.05903, 0.320773, -2.10877, 0.187293, 1.34019, 0.495207]
f_145: 8151.40765 [0.0574015, -1.03853, -1.06164, 0.321666, -2.10845, 0.188601, 1.34024, 0.49584]
f_146: 8151.40661 [0.057589, -1.03862, -1.05975, 0.321385, -2.10876, 0.187637, 1.34024, 0.495762]
f_147: 8151.40674 [0.0575377, -1.03855, -1.06006, 0.32191, -2.10972, 0.188662, 1.33915, 0.495433]
f_148: 8151.40658 [0.0575448, -1.03856, -1.05979, 0.322005, -2.10866, 0.188059, 1.33931, 0.49538]
f_149: 8151.40752 [0.0573726, -1.03859, -1.05969, 0.321926, -2.10835, 0.188432, 1.33917, 0.495451]
f_150: 8151.40655 [0.057563, -1.03858, -1.05989, 0.32173, -2.10902, 0.188179, 1.33964, 0.495431]
f_151: 8151.40661 [0.0575638, -1.0386, -1.06001, 0.32248, -2.1092, 0.188177, 1.33959, 0.495551]
f_152: 8151.45356 [0.0589027, -1.03859, -1.05989, 0.321721, -2.10895, 0.188237, 1.33962, 0.49543]
f_153: 8151.40643 [0.0575692, -1.03858, -1.05979, 0.321417, -2.10853, 0.188614, 1.33946, 0.495415]
f_154: 8151.40657 [0.0575383, -1.03854, -1.05904, 0.321323, -2.10864, 0.189095, 1.33891, 0.495092]
f_155: 8151.40645 [0.0575676, -1.03855, -1.05974, 0.321525, -2.10857, 0.189182, 1.33946, 0.495378]
f_156: 8151.40646 [0.0575754, -1.03858, -1.06022, 0.321916, -2.10855, 0.188667, 1.33926, 0.495335]
f_157: 8151.40711 [0.0577117, -1.03859, -1.05957, 0.321193, -2.10863, 0.189045, 1.33975, 0.495503]
f_158: 8151.40643 [0.0575682, -1.03856, -1.05971, 0.320803, -2.10822, 0.188379, 1.33941, 0.495574]
f_159: 8151.40641 [0.057573, -1.03857, -1.05974, 0.321202, -2.10832, 0.188385, 1.33948, 0.495472]
f_160: 8151.40636 [0.0575827, -1.0386, -1.05946, 0.320855, -2.10786, 0.188113, 1.33954, 0.495533]
f_161: 8151.40636 [0.0576125, -1.03864, -1.05914, 0.320138, -2.10746, 0.187101, 1.33963, 0.495392]
f_162: 8151.40649 [0.0576417, -1.0386, -1.05941, 0.320337, -2.10836, 0.188109, 1.3394, 0.495496]
f_163: 8151.40642 [0.0575837, -1.03859, -1.05906, 0.320529, -2.10802, 0.188013, 1.3398, 0.495696]
f_164: 8151.40636 [0.0576008, -1.0386, -1.05924, 0.320774, -2.1078, 0.188064, 1.33947, 0.495502]
f_165: 8151.40636 [0.0575887, -1.03861, -1.05932, 0.320704, -2.10787, 0.187979, 1.33961, 0.495384]
f_166: 8151.40694 [0.0574412, -1.03861, -1.05926, 0.32059, -2.10775, 0.18795, 1.3396, 0.495379]
f_167: 8151.40636 [0.0575887, -1.03861, -1.05931, 0.32064, -2.10785, 0.187928, 1.33956, 0.495485]
f_168: 8151.40635 [0.0575887, -1.03861, -1.0593, 0.320673, -2.10783, 0.187995, 1.33958, 0.495367]
f_169: 8151.40634 [0.0575852, -1.03861, -1.05929, 0.320654, -2.10776, 0.188017, 1.33958, 0.49537]
f_170: 8151.40632 [0.0575869, -1.03861, -1.05927, 0.3206, -2.10763, 0.188074, 1.33959, 0.495387]
f_171: 8151.4063 [0.0575838, -1.03862, -1.05924, 0.320448, -2.10736, 0.188128, 1.33962, 0.495444]
f_172: 8151.40629 [0.0575869, -1.03862, -1.05911, 0.319978, -2.10698, 0.18803, 1.33971, 0.495549]
f_173: 8151.40632 [0.0576007, -1.03864, -1.05891, 0.31986, -2.1072, 0.187765, 1.33979, 0.495373]
f_174: 8151.40742 [0.0573775, -1.03863, -1.05923, 0.319999, -2.10698, 0.188027, 1.33983, 0.495579]
f_175: 8151.40629 [0.0575822, -1.03863, -1.05923, 0.320093, -2.10692, 0.18805, 1.33984, 0.495594]
f_176: 8151.40661 [0.0574673, -1.03863, -1.05914, 0.320151, -2.10688, 0.188047, 1.33987, 0.495624]
f_177: 8151.40628 [0.0575768, -1.03863, -1.05919, 0.320162, -2.10692, 0.188086, 1.33979, 0.495656]
f_178: 8151.40628 [0.0575741, -1.03862, -1.05915, 0.320249, -2.10687, 0.188128, 1.33978, 0.49564]
f_179: 8151.40628 [0.0575692, -1.03862, -1.05908, 0.320304, -2.10691, 0.188177, 1.33979, 0.495657]
f_180: 8151.40627 [0.0575692, -1.03863, -1.05914, 0.320215, -2.10681, 0.188096, 1.33978, 0.495644]
f_181: 8151.40627 [0.0575733, -1.03863, -1.05914, 0.320266, -2.10677, 0.188059, 1.33976, 0.495629]
f_182: 8151.40625 [0.057571, -1.03863, -1.05915, 0.320351, -2.10667, 0.188035, 1.33974, 0.495589]
f_183: 8151.40622 [0.0575741, -1.03864, -1.05915, 0.320467, -2.10652, 0.188084, 1.33968, 0.495492]
f_184: 8151.40619 [0.0575695, -1.03865, -1.05929, 0.320765, -2.10617, 0.187931, 1.33944, 0.495443]
f_185: 8151.40619 [0.0575695, -1.03867, -1.05942, 0.320759, -2.10594, 0.188178, 1.33918, 0.49534]
f_186: 8151.40621 [0.0575797, -1.03867, -1.0594, 0.320767, -2.10577, 0.188251, 1.33919, 0.495504]
f_187: 8151.40618 [0.0575708, -1.03867, -1.05926, 0.321004, -2.10591, 0.188136, 1.33923, 0.495348]
f_188: 8151.40617 [0.0575732, -1.03868, -1.05934, 0.32104, -2.10582, 0.187935, 1.33934, 0.495326]
f_189: 8151.40616 [0.0575754, -1.03869, -1.0592, 0.321049, -2.10585, 0.187797, 1.33941, 0.495358]
f_190: 8151.40616 [0.0575808, -1.03868, -1.0592, 0.321171, -2.10577, 0.187642, 1.33954, 0.495311]
f_191: 8151.40757 [0.0573402, -1.03869, -1.05921, 0.321017, -2.10586, 0.187746, 1.33939, 0.49537]
f_192: 8151.40616 [0.0575679, -1.03869, -1.05927, 0.321096, -2.10595, 0.187799, 1.33944, 0.495396]
f_193: 8151.40616 [0.0575684, -1.03869, -1.05928, 0.321103, -2.10602, 0.187795, 1.33946, 0.495405]
f_194: 8151.40616 [0.0575714, -1.03869, -1.05928, 0.321039, -2.1059, 0.187784, 1.33946, 0.495403]
f_195: 8151.40616 [0.0575705, -1.03869, -1.05927, 0.321052, -2.10586, 0.187739, 1.33944, 0.495396]
f_196: 8151.40615 [0.0575739, -1.0387, -1.05925, 0.321051, -2.10583, 0.187764, 1.33947, 0.49537]
f_197: 8151.40614 [0.0575685, -1.0387, -1.05922, 0.321075, -2.10571, 0.187801, 1.33952, 0.495354]
f_198: 8151.40614 [0.0575687, -1.0387, -1.05921, 0.321136, -2.10543, 0.187832, 1.33962, 0.495364]
f_199: 8151.40614 [0.057573, -1.0387, -1.0592, 0.321038, -2.10535, 0.187764, 1.33959, 0.495341]
f_200: 8151.40613 [0.0575662, -1.03871, -1.05922, 0.321029, -2.10536, 0.18779, 1.33962, 0.495267]
f_201: 8151.40613 [0.057571, -1.03871, -1.05921, 0.321097, -2.10532, 0.187685, 1.33961, 0.495272]
f_202: 8151.40613 [0.0575647, -1.03871, -1.05922, 0.321058, -2.1053, 0.187813, 1.33962, 0.495263]
f_203: 8151.40612 [0.0575646, -1.03871, -1.05923, 0.321043, -2.10524, 0.187852, 1.3396, 0.49527]
f_204: 8151.40612 [0.0575608, -1.03871, -1.05919, 0.321065, -2.1052, 0.187886, 1.3396, 0.495258]
f_205: 8151.40612 [0.0575605, -1.03871, -1.05921, 0.32106, -2.10514, 0.187913, 1.33962, 0.495262]
f_206: 8151.40623 [0.0576134, -1.03871, -1.05919, 0.321113, -2.1052, 0.187902, 1.3396, 0.495288]
f_207: 8151.40612 [0.0575537, -1.0387, -1.05921, 0.321067, -2.10518, 0.187867, 1.33958, 0.495229]
f_208: 8151.40612 [0.0575671, -1.03871, -1.05919, 0.321102, -2.10514, 0.187881, 1.33959, 0.495247]
f_209: 8151.40612 [0.0575531, -1.03871, -1.05917, 0.321001, -2.10516, 0.187864, 1.33961, 0.495254]
f_210: 8151.40611 [0.05755, -1.03871, -1.05915, 0.32102, -2.10512, 0.187877, 1.3396, 0.495285]
f_211: 8151.40611 [0.0575514, -1.03871, -1.05915, 0.320987, -2.10507, 0.187872, 1.33959, 0.495281]
f_212: 8151.40611 [0.0575494, -1.03872, -1.05913, 0.320997, -2.10502, 0.187882, 1.33959, 0.495288]
f_213: 8151.4061 [0.0575524, -1.03873, -1.05914, 0.320982, -2.10491, 0.187861, 1.33957, 0.495278]
f_214: 8151.40609 [0.0575481, -1.03873, -1.05912, 0.320984, -2.10482, 0.187888, 1.33955, 0.495308]
f_215: 8151.40609 [0.0575444, -1.03875, -1.05917, 0.320865, -2.10456, 0.187902, 1.33959, 0.495298]
f_216: 8151.40608 [0.0575342, -1.03876, -1.05903, 0.321038, -2.1045, 0.187935, 1.3395, 0.495251]
f_217: 8151.40607 [0.0575464, -1.03878, -1.0591, 0.320965, -2.10446, 0.187953, 1.33947, 0.49525]
f_218: 8151.40607 [0.057538, -1.03878, -1.05906, 0.320971, -2.10413, 0.188014, 1.33949, 0.495242]
f_219: 8151.40608 [0.0575572, -1.03878, -1.05915, 0.321034, -2.10442, 0.187859, 1.33944, 0.49528]
f_220: 8151.40606 [0.0575534, -1.03878, -1.05901, 0.320914, -2.10447, 0.187898, 1.3395, 0.495254]
f_221: 8151.40637 [0.0574342, -1.03878, -1.05902, 0.320917, -2.10445, 0.187884, 1.33952, 0.49525]
f_222: 8151.40606 [0.0575421, -1.03878, -1.05902, 0.320961, -2.10446, 0.187888, 1.33954, 0.495237]
f_223: 8151.40606 [0.0575395, -1.03878, -1.059, 0.320957, -2.10443, 0.187846, 1.33951, 0.495217]
f_224: 8151.40606 [0.0575504, -1.03878, -1.05901, 0.320959, -2.1044, 0.187894, 1.33954, 0.495231]
f_225: 8151.40606 [0.0575399, -1.03878, -1.05899, 0.320993, -2.10446, 0.187881, 1.33953, 0.495273]
f_226: 8151.40606 [0.0575432, -1.03879, -1.05899, 0.320941, -2.10444, 0.187836, 1.33953, 0.495276]
f_227: 8151.40606 [0.0575209, -1.03879, -1.05901, 0.320971, -2.10446, 0.187914, 1.3395, 0.49527]
f_228: 8151.40605 [0.0575428, -1.03879, -1.05898, 0.320981, -2.10448, 0.187898, 1.3395, 0.495252]
f_229: 8151.40605 [0.0575411, -1.03879, -1.05901, 0.320991, -2.10447, 0.18792, 1.33948, 0.495258]
f_230: 8151.40605 [0.0575418, -1.03879, -1.05897, 0.320949, -2.10444, 0.187947, 1.33948, 0.495275]
f_231: 8151.40605 [0.0575404, -1.0388, -1.05896, 0.320901, -2.10441, 0.187952, 1.33947, 0.495286]
f_232: 8151.40604 [0.0575332, -1.0388, -1.05893, 0.320885, -2.10431, 0.187986, 1.33946, 0.495314]
f_233: 8151.40603 [0.0575351, -1.03882, -1.05884, 0.320773, -2.10412, 0.187986, 1.33944, 0.495319]
f_234: 8151.40602 [0.0575322, -1.03883, -1.05878, 0.320796, -2.10403, 0.18815, 1.33941, 0.495287]
f_235: 8151.406 [0.0575229, -1.03886, -1.05854, 0.320718, -2.10375, 0.188408, 1.33934, 0.495254]
f_236: 8151.40599 [0.0575106, -1.03893, -1.05828, 0.320637, -2.10305, 0.188364, 1.33925, 0.49505]
f_237: 8151.40601 [0.0575272, -1.03899, -1.05838, 0.320265, -2.10267, 0.187835, 1.33917, 0.495406]
f_238: 8151.40602 [0.0575058, -1.03892, -1.05841, 0.320669, -2.10271, 0.188455, 1.33927, 0.49513]
f_239: 8151.40598 [0.0575103, -1.03895, -1.05827, 0.320331, -2.10313, 0.188673, 1.33924, 0.495281]
f_240: 8151.40599 [0.0574946, -1.03894, -1.05825, 0.320176, -2.10308, 0.189172, 1.33925, 0.495418]
f_241: 8151.40633 [0.0575992, -1.03895, -1.05831, 0.320566, -2.10321, 0.188712, 1.33912, 0.495317]
f_242: 8151.40597 [0.0575054, -1.03894, -1.05818, 0.320207, -2.10307, 0.188631, 1.3394, 0.495281]
f_243: 8151.40599 [0.0575039, -1.03894, -1.05814, 0.319971, -2.10289, 0.188645, 1.33931, 0.495254]
f_244: 8151.4072 [0.0577, -1.03895, -1.05823, 0.320198, -2.10306, 0.188654, 1.33943, 0.495248]
f_245: 8151.40596 [0.0574878, -1.03894, -1.05811, 0.320248, -2.10305, 0.188602, 1.33935, 0.495333]
f_246: 8151.40598 [0.0574546, -1.03895, -1.05808, 0.320279, -2.10303, 0.18863, 1.33935, 0.495314]
f_247: 8151.40596 [0.0574846, -1.03894, -1.05814, 0.320218, -2.10303, 0.188615, 1.33934, 0.495297]
f_248: 8151.40596 [0.0574865, -1.03895, -1.05808, 0.32023, -2.10301, 0.188621, 1.33935, 0.495336]
f_249: 8151.40595 [0.0574828, -1.03895, -1.05811, 0.320222, -2.10305, 0.188656, 1.33937, 0.49535]
f_250: 8151.40595 [0.0574845, -1.03895, -1.05814, 0.320218, -2.10303, 0.188652, 1.3394, 0.495359]
f_251: 8151.40601 [0.0574381, -1.03895, -1.05812, 0.320257, -2.10307, 0.18864, 1.33936, 0.495342]
f_252: 8151.40595 [0.0574866, -1.03895, -1.05811, 0.320186, -2.10311, 0.18865, 1.33939, 0.495342]
f_253: 8151.40594 [0.0574894, -1.03896, -1.0581, 0.320136, -2.10322, 0.188657, 1.3394, 0.495333]
f_254: 8151.40592 [0.0574977, -1.03898, -1.05814, 0.32014, -2.10338, 0.188707, 1.33936, 0.495306]
f_255: 8151.4059 [0.0575052, -1.03899, -1.05824, 0.320086, -2.1036, 0.188669, 1.33943, 0.495229]
f_256: 8151.4059 [0.0575097, -1.039, -1.05833, 0.320207, -2.10371, 0.188749, 1.33931, 0.495244]
f_257: 8151.4059 [0.0575182, -1.03901, -1.05835, 0.320232, -2.10378, 0.188745, 1.33939, 0.495097]
f_258: 8151.40589 [0.0575274, -1.03901, -1.05837, 0.32026, -2.10408, 0.188678, 1.33937, 0.495155]
f_259: 8151.40589 [0.0575478, -1.03902, -1.05854, 0.320675, -2.10448, 0.188468, 1.33932, 0.495085]
f_260: 8151.40587 [0.0575278, -1.03903, -1.05835, 0.320214, -2.10412, 0.188705, 1.33937, 0.495146]
f_261: 8151.40588 [0.0575311, -1.03903, -1.05831, 0.319953, -2.10423, 0.188831, 1.33937, 0.495102]
f_262: 8151.40627 [0.0573776, -1.03903, -1.05831, 0.320246, -2.10416, 0.188728, 1.33942, 0.495194]
f_263: 8151.40585 [0.0575057, -1.03903, -1.05831, 0.320285, -2.10415, 0.188779, 1.33942, 0.495185]
f_264: 8151.40585 [0.0574974, -1.03903, -1.05821, 0.320299, -2.1042, 0.188733, 1.3394, 0.495181]
f_265: 8151.40588 [0.0575388, -1.03903, -1.0582, 0.320292, -2.10422, 0.188703, 1.33943, 0.495173]
f_266: 8151.40584 [0.0575023, -1.03903, -1.05821, 0.320315, -2.10425, 0.188744, 1.33939, 0.495206]
f_267: 8151.40584 [0.057501, -1.03903, -1.05821, 0.32033, -2.10429, 0.188749, 1.33938, 0.495182]
f_268: 8151.40584 [0.0575041, -1.03904, -1.05821, 0.32032, -2.10434, 0.188747, 1.33938, 0.4952]
f_269: 8151.40583 [0.0575036, -1.03904, -1.05822, 0.32037, -2.10441, 0.188756, 1.33938, 0.4952]
f_270: 8151.40583 [0.0575069, -1.03904, -1.05822, 0.320436, -2.10453, 0.188761, 1.33935, 0.495179]
f_271: 8151.40582 [0.0575065, -1.03905, -1.0582, 0.320482, -2.10457, 0.188738, 1.33939, 0.495186]
f_272: 8151.40581 [0.0575116, -1.03907, -1.05813, 0.320482, -2.10473, 0.188675, 1.33941, 0.495208]
f_273: 8151.4058 [0.0575236, -1.03911, -1.05823, 0.320371, -2.10511, 0.188573, 1.33943, 0.495206]
f_274: 8151.40578 [0.057523, -1.03914, -1.05821, 0.320017, -2.10502, 0.188665, 1.33942, 0.495122]
f_275: 8151.4058 [0.0575306, -1.03915, -1.05845, 0.319503, -2.10523, 0.188706, 1.33945, 0.49523]
f_276: 8151.4066 [0.0576863, -1.03914, -1.05828, 0.320169, -2.10503, 0.188855, 1.33945, 0.495206]
f_277: 8151.40579 [0.0575193, -1.03914, -1.0582, 0.320056, -2.10513, 0.188746, 1.33954, 0.494988]
f_278: 8151.40693 [0.0577267, -1.03914, -1.05825, 0.320045, -2.10498, 0.188619, 1.33941, 0.495085]
f_279: 8151.40577 [0.0575198, -1.03915, -1.05813, 0.320047, -2.10506, 0.188693, 1.33948, 0.495132]
f_280: 8151.40578 [0.0575128, -1.03915, -1.05806, 0.320063, -2.10509, 0.188702, 1.33943, 0.495083]
f_281: 8151.40589 [0.0574527, -1.03915, -1.05815, 0.320094, -2.10505, 0.188685, 1.33948, 0.495129]
f_282: 8151.40577 [0.057517, -1.03915, -1.05813, 0.32009, -2.10503, 0.18867, 1.33949, 0.495122]
f_283: 8151.40577 [0.0575204, -1.03915, -1.05815, 0.320125, -2.10508, 0.188655, 1.33948, 0.495122]
f_284: 8151.40576 [0.0575181, -1.03916, -1.05816, 0.320174, -2.10506, 0.188667, 1.33948, 0.495112]
f_285: 8151.40576 [0.0575182, -1.03917, -1.05819, 0.320226, -2.10505, 0.188701, 1.33947, 0.495072]
f_286: 8151.40575 [0.0575201, -1.03918, -1.05825, 0.320257, -2.10517, 0.188676, 1.33947, 0.494996]
f_287: 8151.40574 [0.0575252, -1.03919, -1.05827, 0.320524, -2.1053, 0.188566, 1.33945, 0.495035]
f_288: 8151.40574 [0.0575337, -1.03919, -1.05828, 0.32066, -2.10536, 0.188353, 1.33946, 0.495116]
f_289: 8151.40575 [0.0575332, -1.03919, -1.05827, 0.320956, -2.10552, 0.188291, 1.33945, 0.495112]
f_290: 8151.40579 [0.0574983, -1.03919, -1.05826, 0.320601, -2.10549, 0.188344, 1.3395, 0.49513]
f_291: 8151.40574 [0.0575304, -1.0392, -1.05832, 0.320711, -2.10524, 0.188378, 1.33943, 0.495123]
f_292: 8151.40573 [0.0575268, -1.03921, -1.05839, 0.320804, -2.10507, 0.188255, 1.3394, 0.495093]
f_293: 8151.40574 [0.0575354, -1.03922, -1.05852, 0.320843, -2.10524, 0.188106, 1.33938, 0.495141]
f_294: 8151.40609 [0.0576498, -1.03921, -1.05844, 0.320833, -2.10501, 0.188225, 1.33938, 0.495124]
f_295: 8151.40573 [0.0575302, -1.03921, -1.05843, 0.320872, -2.10498, 0.18825, 1.33941, 0.495151]
f_296: 8151.40573 [0.0575297, -1.03921, -1.05843, 0.320878, -2.105, 0.188243, 1.33941, 0.495152]
f_297: 8151.40573 [0.0575347, -1.03921, -1.05842, 0.320887, -2.10499, 0.188247, 1.33937, 0.49516]
f_298: 8151.40572 [0.0575296, -1.03922, -1.05843, 0.320944, -2.10499, 0.188221, 1.33941, 0.495149]
f_299: 8151.40572 [0.0575336, -1.03922, -1.05846, 0.320925, -2.10497, 0.188245, 1.3394, 0.49513]
f_300: 8151.40572 [0.0575304, -1.03922, -1.05845, 0.320885, -2.10495, 0.188223, 1.3394, 0.495129]
f_301: 8151.40571 [0.0575331, -1.03923, -1.05845, 0.320876, -2.10496, 0.188263, 1.33939, 0.495131]
f_302: 8151.40571 [0.0575195, -1.03924, -1.05844, 0.320845, -2.10498, 0.188331, 1.33939, 0.495117]
f_303: 8151.4057 [0.0575306, -1.03925, -1.05842, 0.320803, -2.10499, 0.18839, 1.3394, 0.495118]
f_304: 8151.40569 [0.0575254, -1.03926, -1.05839, 0.320634, -2.105, 0.188493, 1.33939, 0.495072]
f_305: 8151.40567 [0.0575245, -1.03927, -1.05822, 0.320582, -2.10511, 0.188621, 1.33944, 0.495109]
f_306: 8151.40567 [0.057518, -1.03927, -1.0581, 0.320423, -2.10502, 0.188807, 1.3395, 0.495126]
f_307: 8151.40568 [0.057515, -1.03927, -1.05791, 0.320269, -2.10512, 0.188855, 1.33954, 0.495098]
f_308: 8151.40566 [0.0575164, -1.03928, -1.05809, 0.320365, -2.10496, 0.188864, 1.3395, 0.495128]
f_309: 8151.40566 [0.0575132, -1.03929, -1.05813, 0.320338, -2.10504, 0.188922, 1.33945, 0.49513]
f_310: 8151.40565 [0.0575161, -1.03929, -1.05817, 0.320301, -2.105, 0.188882, 1.3395, 0.495135]
f_311: 8151.40565 [0.0575146, -1.0393, -1.05819, 0.32028, -2.10496, 0.188887, 1.33943, 0.495136]
f_312: 8151.40564 [0.05751, -1.03931, -1.05821, 0.320361, -2.10494, 0.188941, 1.33948, 0.495102]
f_313: 8151.40563 [0.0575109, -1.03932, -1.05821, 0.320401, -2.10493, 0.188981, 1.33944, 0.495101]
f_314: 8151.40563 [0.0575134, -1.03933, -1.05819, 0.320427, -2.10487, 0.189017, 1.33948, 0.494982]
f_315: 8151.40561 [0.057504, -1.03935, -1.05827, 0.320449, -2.10476, 0.189118, 1.33944, 0.495026]
f_316: 8151.40558 [0.0575108, -1.03939, -1.05837, 0.320442, -2.10476, 0.189262, 1.33939, 0.495086]
f_317: 8151.40553 [0.057519, -1.03945, -1.05901, 0.320295, -2.10521, 0.189393, 1.33946, 0.495148]
f_318: 8151.40551 [0.0575408, -1.03951, -1.05951, 0.320734, -2.10565, 0.189097, 1.33955, 0.495383]
f_319: 8151.40555 [0.0575464, -1.03954, -1.0603, 0.320019, -2.10554, 0.189165, 1.33955, 0.495287]
f_320: 8151.41105 [0.0579784, -1.03951, -1.05955, 0.320916, -2.1057, 0.189125, 1.33964, 0.49511]
f_321: 8151.40554 [0.0575446, -1.03952, -1.05936, 0.320444, -2.10567, 0.189242, 1.33938, 0.495652]
f_322: 8151.40835 [0.0572029, -1.03952, -1.05952, 0.3208, -2.10577, 0.18924, 1.33952, 0.495332]
f_323: 8151.4055 [0.0575315, -1.03952, -1.05946, 0.320746, -2.10577, 0.189303, 1.33946, 0.495386]
f_324: 8151.40549 [0.0575219, -1.03953, -1.05934, 0.320651, -2.10572, 0.18927, 1.33957, 0.495359]
f_325: 8151.40548 [0.0575198, -1.03954, -1.05929, 0.320672, -2.10556, 0.189287, 1.33943, 0.495367]
f_326: 8151.40546 [0.0575086, -1.03955, -1.05919, 0.320655, -2.10547, 0.189446, 1.33949, 0.495412]
f_327: 8151.40545 [0.0575184, -1.03956, -1.05903, 0.320504, -2.10559, 0.18942, 1.33946, 0.495375]
f_328: 8151.40544 [0.0575052, -1.03958, -1.05895, 0.320399, -2.10552, 0.189563, 1.33951, 0.49545]
f_329: 8151.40543 [0.0574966, -1.03958, -1.05881, 0.32063, -2.10553, 0.189639, 1.33946, 0.495392]
f_330: 8151.40542 [0.0574951, -1.03959, -1.05874, 0.320652, -2.10531, 0.189554, 1.33944, 0.495302]
f_331: 8151.40541 [0.0574858, -1.0396, -1.05862, 0.320799, -2.10524, 0.189653, 1.33939, 0.495303]
f_332: 8151.4054 [0.0574818, -1.03962, -1.05845, 0.320885, -2.10528, 0.189736, 1.33942, 0.49524]
f_333: 8151.40538 [0.05747, -1.03965, -1.0581, 0.320922, -2.10517, 0.189812, 1.33937, 0.495272]
f_334: 8151.40536 [0.0574516, -1.03971, -1.05749, 0.321331, -2.10524, 0.19004, 1.33936, 0.495184]
f_335: 8151.40529 [0.0574442, -1.03977, -1.05769, 0.320579, -2.1049, 0.190371, 1.33931, 0.495173]
f_336: 8151.40526 [0.0574303, -1.0399, -1.05833, 0.319197, -2.10477, 0.191228, 1.33927, 0.494999]
f_337: 8151.4051 [0.0574263, -1.04005, -1.0586, 0.32051, -2.10534, 0.191416, 1.33925, 0.495066]
f_338: 8151.40499 [0.0573845, -1.04037, -1.05848, 0.318815, -2.10505, 0.192642, 1.33908, 0.495279]
f_339: 8151.40499 [0.0573599, -1.04064, -1.05907, 0.321325, -2.10491, 0.193721, 1.33913, 0.494082]
f_340: 8151.41729 [0.0567702, -1.04039, -1.05841, 0.318421, -2.10534, 0.191675, 1.33982, 0.494969]
f_341: 8151.40486 [0.0574029, -1.04048, -1.05894, 0.319766, -2.10654, 0.193344, 1.33945, 0.495048]
f_342: 8151.40482 [0.0574063, -1.04058, -1.06049, 0.319879, -2.10598, 0.193184, 1.33932, 0.494794]
f_343: 8151.40471 [0.0573652, -1.04071, -1.05923, 0.320286, -2.10628, 0.193875, 1.33922, 0.494896]
f_344: 8151.4046 [0.0573024, -1.04103, -1.05891, 0.319194, -2.10563, 0.19547, 1.33921, 0.494788]
f_345: 8151.40446 [0.0572865, -1.04137, -1.05952, 0.320231, -2.10626, 0.196091, 1.33903, 0.494467]
f_346: 8151.40444 [0.0572809, -1.04137, -1.05952, 0.320115, -2.10629, 0.196262, 1.33912, 0.494504]
f_347: 8151.40445 [0.0572431, -1.0416, -1.05953, 0.318769, -2.10585, 0.197444, 1.33907, 0.49443]
f_348: 8151.44643 [0.0559843, -1.04134, -1.05999, 0.320686, -2.10626, 0.197631, 1.3381, 0.494493]
f_349: 8151.40477 [0.057247, -1.04143, -1.06076, 0.321127, -2.10608, 0.197191, 1.3385, 0.494147]
f_350: 8151.41786 [0.0565672, -1.04138, -1.05973, 0.320406, -2.10554, 0.196118, 1.33925, 0.495042]
f_351: 8151.40455 [0.0572709, -1.04137, -1.05949, 0.319789, -2.1075, 0.196732, 1.33907, 0.494436]
f_352: 8151.42562 [0.0581498, -1.04137, -1.05946, 0.320178, -2.1063, 0.196326, 1.33901, 0.494703]
f_353: 8151.4045 [0.0572547, -1.04137, -1.05935, 0.320182, -2.10634, 0.196544, 1.33883, 0.494624]
f_354: 8151.40448 [0.0572449, -1.04137, -1.05969, 0.319919, -2.10632, 0.196228, 1.33905, 0.494545]
f_355: 8151.40442 [0.0572786, -1.04138, -1.05948, 0.320136, -2.10651, 0.196159, 1.33921, 0.494503]
f_356: 8151.40441 [0.0572876, -1.0414, -1.0595, 0.320174, -2.1066, 0.196021, 1.33928, 0.494443]
f_357: 8151.40437 [0.0572883, -1.0414, -1.05936, 0.320111, -2.10666, 0.195908, 1.33932, 0.494575]
f_358: 8151.40435 [0.0573047, -1.0414, -1.05906, 0.319886, -2.10667, 0.195529, 1.33943, 0.494607]
f_359: 8151.4043 [0.0573152, -1.04141, -1.05914, 0.32021, -2.10685, 0.195154, 1.33952, 0.494781]
f_360: 8151.40429 [0.0573274, -1.04143, -1.05902, 0.320402, -2.10706, 0.194785, 1.33966, 0.494842]
f_361: 8151.4043 [0.0573434, -1.0414, -1.05913, 0.32021, -2.10703, 0.194471, 1.33984, 0.494924]
f_362: 8151.40427 [0.057339, -1.04145, -1.05903, 0.320443, -2.10709, 0.19485, 1.33966, 0.494839]
f_363: 8151.40426 [0.0573387, -1.04146, -1.05917, 0.3203, -2.10712, 0.194789, 1.33959, 0.49487]
f_364: 8151.40426 [0.0573331, -1.04147, -1.05923, 0.320168, -2.10721, 0.194884, 1.33966, 0.49491]
f_365: 8151.40438 [0.0572807, -1.04147, -1.05916, 0.320113, -2.10733, 0.194888, 1.33965, 0.494889]
f_366: 8151.40424 [0.0573334, -1.04148, -1.05926, 0.320208, -2.10706, 0.194865, 1.33967, 0.494917]
f_367: 8151.40423 [0.0573396, -1.04148, -1.05935, 0.320311, -2.10708, 0.194855, 1.33963, 0.49493]
f_368: 8151.40422 [0.0573426, -1.04149, -1.05938, 0.320326, -2.10704, 0.1948, 1.33956, 0.494929]
f_369: 8151.40421 [0.0573427, -1.0415, -1.05949, 0.320548, -2.10701, 0.194728, 1.33951, 0.494937]
f_370: 8151.40422 [0.0573336, -1.04151, -1.05968, 0.320945, -2.10735, 0.194725, 1.33945, 0.495106]
f_371: 8151.40749 [0.0576927, -1.0415, -1.05934, 0.320552, -2.10709, 0.194786, 1.33952, 0.494973]
f_372: 8151.40422 [0.0573406, -1.04151, -1.0595, 0.320574, -2.10713, 0.19482, 1.33948, 0.49483]
f_373: 8151.4042 [0.0573464, -1.0415, -1.05944, 0.320526, -2.10705, 0.194631, 1.33955, 0.494968]
f_374: 8151.40419 [0.0573497, -1.0415, -1.05942, 0.320499, -2.10701, 0.194644, 1.33952, 0.49505]
f_375: 8151.40418 [0.0573551, -1.04151, -1.05944, 0.32045, -2.10697, 0.194588, 1.33951, 0.495099]
f_376: 8151.40416 [0.0573568, -1.04152, -1.05954, 0.320298, -2.10691, 0.194494, 1.33954, 0.495147]
f_377: 8151.40415 [0.0573559, -1.04154, -1.05951, 0.320328, -2.10677, 0.194379, 1.33948, 0.495197]
f_378: 8151.40412 [0.0573591, -1.04157, -1.05943, 0.320428, -2.10658, 0.193994, 1.33944, 0.495282]
f_379: 8151.40411 [0.05737, -1.04156, -1.05906, 0.320992, -2.10595, 0.19323, 1.33942, 0.4954]
f_380: 8151.40411 [0.0573867, -1.04156, -1.0588, 0.320638, -2.10605, 0.193232, 1.33957, 0.495556]
f_381: 8151.40411 [0.057385, -1.04158, -1.0586, 0.32094, -2.10627, 0.192982, 1.33968, 0.495473]
f_382: 8151.40411 [0.0573949, -1.04155, -1.05875, 0.320745, -2.10606, 0.192991, 1.3396, 0.495621]
f_383: 8151.40409 [0.0573742, -1.04157, -1.05871, 0.320607, -2.10583, 0.193212, 1.33958, 0.495443]
f_384: 8151.4041 [0.0573623, -1.04158, -1.05854, 0.32073, -2.1058, 0.193266, 1.33951, 0.495485]
f_385: 8151.40416 [0.0574396, -1.04157, -1.05875, 0.320654, -2.10578, 0.193242, 1.33964, 0.495483]
f_386: 8151.4041 [0.0573618, -1.04157, -1.05875, 0.320656, -2.10578, 0.193249, 1.33964, 0.495485]
f_387: 8151.40411 [0.0573615, -1.04157, -1.05869, 0.320614, -2.10583, 0.193161, 1.33959, 0.495455]
f_388: 8151.40409 [0.0573865, -1.04157, -1.05869, 0.320591, -2.10577, 0.193219, 1.3396, 0.495465]
f_389: 8151.40408 [0.0573828, -1.04157, -1.05869, 0.32064, -2.10576, 0.193209, 1.33961, 0.495438]
f_390: 8151.40408 [0.057387, -1.04157, -1.05869, 0.320681, -2.10577, 0.193197, 1.33961, 0.495435]
f_391: 8151.40408 [0.0573838, -1.04158, -1.05868, 0.32077, -2.1058, 0.19318, 1.33961, 0.495452]
f_392: 8151.40407 [0.0573893, -1.04159, -1.05864, 0.320839, -2.1058, 0.193096, 1.33961, 0.495471]
f_393: 8151.40407 [0.05739, -1.0416, -1.05864, 0.320795, -2.10583, 0.193102, 1.3396, 0.495508]
f_394: 8151.40406 [0.0573966, -1.04162, -1.05865, 0.320753, -2.10596, 0.193078, 1.33963, 0.495534]
f_395: 8151.40406 [0.0574103, -1.04165, -1.05877, 0.320689, -2.10624, 0.193018, 1.33967, 0.49567]
f_396: 8151.4043 [0.0575061, -1.04165, -1.05868, 0.320729, -2.10628, 0.192763, 1.33968, 0.495664]
f_397: 8151.40405 [0.0573954, -1.04166, -1.05874, 0.320639, -2.10616, 0.193259, 1.3397, 0.495607]
f_398: 8151.40404 [0.0573906, -1.04167, -1.05868, 0.320504, -2.10614, 0.193427, 1.33961, 0.495555]
f_399: 8151.40404 [0.0573864, -1.04168, -1.0585, 0.320388, -2.10614, 0.193522, 1.33961, 0.495542]
f_400: 8151.40417 [0.0573102, -1.04168, -1.05848, 0.320466, -2.10605, 0.193463, 1.33958, 0.495549]
f_401: 8151.40403 [0.0573814, -1.04168, -1.05849, 0.320491, -2.10604, 0.193497, 1.33958, 0.495527]
f_402: 8151.40403 [0.0573804, -1.04169, -1.05852, 0.320456, -2.10592, 0.193438, 1.33957, 0.495533]
f_403: 8151.40402 [0.0573814, -1.0417, -1.05853, 0.32051, -2.10585, 0.193408, 1.33954, 0.495559]
f_404: 8151.40401 [0.0573817, -1.04171, -1.05854, 0.320528, -2.10583, 0.193363, 1.33954, 0.495494]
f_405: 8151.40399 [0.0573875, -1.04173, -1.05856, 0.320585, -2.10586, 0.1933, 1.33956, 0.495444]
f_406: 8151.40399 [0.0573964, -1.04177, -1.05854, 0.320549, -2.10597, 0.193325, 1.33949, 0.495595]
f_407: 8151.40394 [0.0573903, -1.0418, -1.05849, 0.320578, -2.1057, 0.193196, 1.33962, 0.495462]
f_408: 8151.4039 [0.0573923, -1.04188, -1.05817, 0.320564, -2.10564, 0.193081, 1.33955, 0.495497]
f_409: 8151.40384 [0.0573947, -1.04196, -1.05868, 0.320332, -2.10568, 0.193306, 1.3395, 0.495394]
f_410: 8151.4038 [0.0573765, -1.04203, -1.05892, 0.320601, -2.10578, 0.193817, 1.33943, 0.495274]
f_411: 8151.40377 [0.0573872, -1.0421, -1.05904, 0.319884, -2.10592, 0.193882, 1.33954, 0.495195]
f_412: 8151.40372 [0.0573899, -1.04216, -1.05916, 0.320633, -2.10541, 0.193574, 1.33934, 0.495219]
f_413: 8151.40367 [0.057403, -1.04224, -1.05892, 0.320532, -2.10576, 0.193423, 1.33938, 0.49522]
f_414: 8151.40357 [0.0574064, -1.04242, -1.05861, 0.320394, -2.10541, 0.193295, 1.33941, 0.495243]
f_415: 8151.4034 [0.0574225, -1.04276, -1.05782, 0.319764, -2.10496, 0.192966, 1.3394, 0.495275]
f_416: 8151.40314 [0.0575117, -1.04346, -1.05697, 0.319408, -2.10497, 0.191532, 1.33982, 0.49566]
f_417: 8151.40293 [0.057443, -1.04373, -1.05813, 0.319728, -2.10469, 0.193276, 1.3393, 0.494716]
f_418: 8151.40283 [0.0575444, -1.04423, -1.05628, 0.319002, -2.10417, 0.190949, 1.33989, 0.495281]
f_419: 8151.40266 [0.0574628, -1.04442, -1.05775, 0.319216, -2.10409, 0.193199, 1.3392, 0.494663]
f_420: 8151.40257 [0.0575419, -1.04499, -1.05567, 0.318547, -2.10359, 0.191402, 1.33967, 0.495078]
f_421: 8151.40246 [0.0574871, -1.04515, -1.05765, 0.31877, -2.10307, 0.192867, 1.33915, 0.494488]
f_422: 8151.40256 [0.0574732, -1.04569, -1.05745, 0.317565, -2.10314, 0.193858, 1.33901, 0.494046]
f_423: 8152.2157 [0.0628744, -1.04515, -1.05747, 0.319748, -2.10308, 0.192791, 1.33781, 0.494127]
f_424: 8151.40235 [0.0573693, -1.04537, -1.05632, 0.318856, -2.10161, 0.193395, 1.33956, 0.494771]
f_425: 8151.40241 [0.0574297, -1.04549, -1.05523, 0.319963, -2.10026, 0.190837, 1.33922, 0.494903]
f_426: 8151.40297 [0.0572803, -1.04537, -1.05597, 0.319542, -2.10186, 0.193296, 1.34, 0.493717]
f_427: 8151.40233 [0.0574366, -1.04542, -1.0566, 0.318426, -2.10324, 0.192752, 1.33986, 0.49453]
f_428: 8151.41436 [0.0567693, -1.04543, -1.05646, 0.31913, -2.10369, 0.19292, 1.33965, 0.494679]
f_429: 8151.40246 [0.0574108, -1.04542, -1.05629, 0.318053, -2.10251, 0.192798, 1.33959, 0.494344]
f_430: 8151.41522 [0.0567658, -1.04543, -1.05671, 0.318432, -2.10307, 0.192609, 1.33994, 0.494515]
f_431: 8151.40226 [0.0574406, -1.04542, -1.05656, 0.318752, -2.10336, 0.192879, 1.33965, 0.494549]
f_432: 8151.40225 [0.0574183, -1.04542, -1.05643, 0.318742, -2.10351, 0.19324, 1.33973, 0.494628]
f_433: 8151.40225 [0.0574183, -1.04543, -1.05642, 0.318735, -2.10351, 0.193242, 1.33973, 0.494624]
f_434: 8151.40224 [0.057433, -1.04544, -1.05633, 0.318815, -2.10364, 0.193108, 1.33969, 0.494636]
f_435: 8151.40221 [0.0574302, -1.04544, -1.05641, 0.319021, -2.10363, 0.193055, 1.33974, 0.494675]
f_436: 8151.40218 [0.0574391, -1.04544, -1.05649, 0.319181, -2.10372, 0.192954, 1.33968, 0.494683]
f_437: 8151.40213 [0.0574406, -1.04545, -1.05652, 0.319641, -2.10374, 0.192799, 1.33971, 0.494777]
f_438: 8151.40208 [0.0574588, -1.04546, -1.05641, 0.320464, -2.10408, 0.192384, 1.33962, 0.494891]
f_439: 8151.40211 [0.0574558, -1.04547, -1.05587, 0.320712, -2.10356, 0.191992, 1.33959, 0.495019]
f_440: 8151.40211 [0.0574646, -1.04543, -1.05636, 0.32033, -2.10404, 0.192315, 1.33963, 0.494904]
f_441: 8151.40206 [0.0574674, -1.04546, -1.05675, 0.320692, -2.10416, 0.19244, 1.3396, 0.494939]
f_442: 8151.40207 [0.0574594, -1.04546, -1.05692, 0.320799, -2.10452, 0.192574, 1.33959, 0.494781]
f_443: 8151.4034 [0.0576735, -1.04545, -1.05678, 0.320656, -2.10412, 0.192453, 1.33953, 0.494908]
f_444: 8151.40204 [0.0574627, -1.04547, -1.05681, 0.320612, -2.10426, 0.192378, 1.33968, 0.495015]
f_445: 8151.40205 [0.0574564, -1.04547, -1.05678, 0.32077, -2.10425, 0.192451, 1.33979, 0.495008]
f_446: 8151.40205 [0.0574624, -1.04546, -1.05679, 0.320686, -2.10429, 0.192342, 1.3397, 0.495038]
f_447: 8151.40204 [0.0574617, -1.04547, -1.05683, 0.320673, -2.10426, 0.192368, 1.33971, 0.49499]
f_448: 8151.40204 [0.057463, -1.04548, -1.05681, 0.320689, -2.10431, 0.192435, 1.33968, 0.495026]
f_449: 8151.40204 [0.0574569, -1.04548, -1.05685, 0.320674, -2.10433, 0.192514, 1.33969, 0.495012]
f_450: 8151.40203 [0.0574568, -1.04548, -1.05687, 0.320646, -2.10437, 0.192545, 1.33965, 0.494978]
f_451: 8151.40203 [0.0574539, -1.04549, -1.05692, 0.320647, -2.10434, 0.192604, 1.33965, 0.494972]
f_452: 8151.40202 [0.0574493, -1.0455, -1.05705, 0.320694, -2.10425, 0.192714, 1.33964, 0.494967]
f_453: 8151.40201 [0.0574365, -1.04551, -1.05726, 0.320747, -2.10413, 0.192943, 1.33952, 0.494874]
f_454: 8151.40199 [0.0574442, -1.04553, -1.05743, 0.320383, -2.10419, 0.192976, 1.33957, 0.494947]
f_455: 8151.40198 [0.0574407, -1.04559, -1.05749, 0.320034, -2.10396, 0.193078, 1.33955, 0.494914]
f_456: 8151.40205 [0.0574221, -1.04561, -1.05731, 0.319378, -2.10321, 0.193189, 1.33951, 0.49485]
f_457: 8151.40196 [0.0574417, -1.0456, -1.0577, 0.32004, -2.10431, 0.193286, 1.33958, 0.494915]
f_458: 8151.40196 [0.0574583, -1.04562, -1.05793, 0.320091, -2.10461, 0.193151, 1.33957, 0.494848]
f_459: 8151.40194 [0.0574666, -1.04563, -1.05802, 0.320116, -2.10474, 0.193145, 1.33955, 0.4949]
f_460: 8151.40193 [0.0574541, -1.04563, -1.05795, 0.320141, -2.10479, 0.193245, 1.33945, 0.494969]
f_461: 8151.40192 [0.0574545, -1.04564, -1.058, 0.320189, -2.10468, 0.193246, 1.33954, 0.495062]
f_462: 8151.40191 [0.0574702, -1.04566, -1.05806, 0.320068, -2.10481, 0.193068, 1.33952, 0.495242]
f_463: 8151.4019 [0.0574611, -1.04568, -1.05815, 0.320437, -2.10475, 0.193106, 1.33943, 0.495164]
f_464: 8151.40188 [0.0574671, -1.0457, -1.0582, 0.320574, -2.10512, 0.193095, 1.3395, 0.495252]
f_465: 8151.40188 [0.0574644, -1.04572, -1.05844, 0.320551, -2.10516, 0.193131, 1.33942, 0.49537]
f_466: 8151.40186 [0.057471, -1.04575, -1.05834, 0.320725, -2.10529, 0.193129, 1.33947, 0.495308]
f_467: 8151.40186 [0.0574885, -1.0458, -1.05814, 0.321016, -2.10559, 0.192803, 1.33952, 0.495441]
f_468: 8151.40685 [0.0579173, -1.0458, -1.05827, 0.321156, -2.10581, 0.192701, 1.33952, 0.495259]
f_469: 8151.40184 [0.0574724, -1.04583, -1.05806, 0.320846, -2.10556, 0.193088, 1.33959, 0.495422]
f_470: 8151.40182 [0.057468, -1.04585, -1.0583, 0.320645, -2.10545, 0.193237, 1.33952, 0.495441]
f_471: 8151.4018 [0.0574603, -1.04588, -1.05879, 0.320262, -2.10545, 0.193677, 1.3395, 0.495312]
f_472: 8151.40182 [0.0574446, -1.04591, -1.05916, 0.319882, -2.10504, 0.194031, 1.33935, 0.495439]
f_473: 8151.4018 [0.057459, -1.04589, -1.05892, 0.320529, -2.10568, 0.193659, 1.33955, 0.495438]
f_474: 8151.4018 [0.0574728, -1.04592, -1.05894, 0.320243, -2.10587, 0.193618, 1.33956, 0.495492]
f_475: 8151.40201 [0.0575502, -1.04589, -1.05891, 0.320542, -2.10579, 0.193686, 1.33941, 0.495434]
f_476: 8151.40179 [0.0574592, -1.04591, -1.05891, 0.320624, -2.10566, 0.1937, 1.33949, 0.495375]
f_477: 8151.40179 [0.0574589, -1.04592, -1.059, 0.320594, -2.10569, 0.193771, 1.33947, 0.495408]
f_478: 8151.40178 [0.0574629, -1.04593, -1.05911, 0.320598, -2.10574, 0.193729, 1.33945, 0.495412]
f_479: 8151.40178 [0.0574605, -1.04595, -1.0591, 0.320605, -2.10584, 0.193805, 1.33947, 0.495413]
f_480: 8151.40176 [0.0574616, -1.04598, -1.05913, 0.320652, -2.106, 0.193897, 1.33951, 0.495352]
f_481: 8151.40174 [0.0574618, -1.04605, -1.0593, 0.3208, -2.10623, 0.194034, 1.33948, 0.495305]
f_482: 8151.40171 [0.0574628, -1.04617, -1.05988, 0.320629, -2.10662, 0.194437, 1.33949, 0.495338]
f_483: 8151.40164 [0.0574713, -1.04644, -1.06017, 0.321012, -2.10727, 0.194619, 1.33941, 0.495363]
f_484: 8151.40167 [0.0574415, -1.04693, -1.06248, 0.319831, -2.10783, 0.196617, 1.33913, 0.495074]
f_485: 8151.49384 [0.0556383, -1.04644, -1.05985, 0.320238, -2.10768, 0.194736, 1.33946, 0.495725]
f_486: 8151.40202 [0.057531, -1.04661, -1.06006, 0.32298, -2.10971, 0.194044, 1.33978, 0.49586]
f_487: 8151.4019 [0.0574512, -1.04643, -1.06032, 0.320794, -2.10807, 0.194388, 1.33885, 0.495065]
f_488: 8151.40173 [0.0574829, -1.0465, -1.06042, 0.319671, -2.10829, 0.195098, 1.33948, 0.494975]
f_489: 8151.43212 [0.0585176, -1.04644, -1.05975, 0.320848, -2.10772, 0.19485, 1.33942, 0.495539]
f_490: 8151.4017 [0.0574965, -1.04645, -1.06083, 0.320822, -2.10757, 0.194306, 1.33929, 0.495341]
f_491: 8151.40204 [0.0573409, -1.04643, -1.06028, 0.32112, -2.10749, 0.194947, 1.33948, 0.495304]
f_492: 8151.40167 [0.0574675, -1.04644, -1.06018, 0.321079, -2.1073, 0.194545, 1.33923, 0.495119]
f_493: 8151.40177 [0.05754, -1.04643, -1.06014, 0.320828, -2.10717, 0.194664, 1.33945, 0.49531]
f_494: 8151.40165 [0.0574711, -1.04644, -1.06025, 0.320802, -2.10728, 0.194599, 1.33936, 0.495324]
f_495: 8151.40164 [0.0574657, -1.04645, -1.06019, 0.32101, -2.10729, 0.194643, 1.33941, 0.495364]
f_496: 8151.40163 [0.0574722, -1.04645, -1.06019, 0.320998, -2.10728, 0.194609, 1.33946, 0.495317]
f_497: 8151.40162 [0.0574656, -1.04645, -1.06018, 0.32099, -2.10716, 0.194643, 1.33948, 0.49529]
f_498: 8151.40162 [0.0574646, -1.04645, -1.06012, 0.320935, -2.10709, 0.194684, 1.33949, 0.495273]
f_499: 8151.40161 [0.05746, -1.04645, -1.06011, 0.320901, -2.10698, 0.194712, 1.33951, 0.495264]
f_500: 8151.40161 [0.0574612, -1.04645, -1.06005, 0.32084, -2.10695, 0.194722, 1.33954, 0.495247]
f_501: 8151.4016 [0.0574506, -1.04645, -1.06002, 0.320825, -2.10688, 0.194798, 1.33952, 0.495268]
f_502: 8151.4016 [0.0574554, -1.04644, -1.06003, 0.320813, -2.10679, 0.194789, 1.33956, 0.495252]
f_503: 8151.4016 [0.0574453, -1.04644, -1.05996, 0.320771, -2.10675, 0.194839, 1.33958, 0.495267]
f_504: 8151.40159 [0.0574514, -1.04644, -1.05994, 0.320745, -2.10665, 0.194819, 1.33956, 0.495225]
f_505: 8151.40159 [0.0574556, -1.04644, -1.05993, 0.32072, -2.10659, 0.194896, 1.33959, 0.495237]
f_506: 8151.40159 [0.0574461, -1.04643, -1.05989, 0.320756, -2.1065, 0.19486, 1.33959, 0.495266]
f_507: 8151.40159 [0.0574467, -1.04643, -1.05984, 0.320714, -2.10646, 0.194876, 1.33963, 0.49524]
f_508: 8151.40158 [0.0574417, -1.04643, -1.05979, 0.320671, -2.1064, 0.194913, 1.33959, 0.495257]
f_509: 8151.40158 [0.0574406, -1.04643, -1.05981, 0.320678, -2.10629, 0.194914, 1.33961, 0.495231]
f_510: 8151.40158 [0.0574405, -1.04643, -1.05974, 0.320676, -2.10625, 0.19496, 1.33963, 0.49527]
f_511: 8151.40158 [0.0574392, -1.04643, -1.05968, 0.320671, -2.10616, 0.194921, 1.33963, 0.495257]
f_512: 8151.40158 [0.057437, -1.04642, -1.05959, 0.320623, -2.10604, 0.194917, 1.33966, 0.495275]
f_513: 8151.40157 [0.0574392, -1.04644, -1.05972, 0.320658, -2.10618, 0.194946, 1.33962, 0.495254]
f_514: 8151.40157 [0.0574322, -1.04644, -1.05967, 0.320707, -2.10614, 0.194994, 1.33963, 0.495247]
f_515: 8151.40157 [0.057434, -1.04644, -1.05963, 0.320698, -2.10606, 0.194966, 1.33964, 0.495276]
f_516: 8151.40156 [0.0574296, -1.04645, -1.05961, 0.320725, -2.10597, 0.194971, 1.33964, 0.495277]
f_517: 8151.40156 [0.0574295, -1.04646, -1.05955, 0.320664, -2.10596, 0.19501, 1.33969, 0.495367]
f_518: 8151.40156 [0.0574321, -1.04647, -1.05959, 0.320747, -2.1059, 0.194932, 1.33967, 0.495415]
f_519: 8151.40155 [0.0574271, -1.04648, -1.0595, 0.320718, -2.10587, 0.194972, 1.33969, 0.495317]
f_520: 8151.40154 [0.0574244, -1.0465, -1.05941, 0.320671, -2.10578, 0.195022, 1.33969, 0.495322]
f_521: 8151.40153 [0.0574256, -1.04653, -1.05931, 0.320551, -2.10568, 0.19506, 1.33976, 0.495331]
f_522: 8151.40151 [0.057419, -1.04656, -1.05914, 0.320772, -2.10574, 0.195028, 1.33971, 0.495279]
f_523: 8151.4015 [0.0574234, -1.04659, -1.05921, 0.320676, -2.10568, 0.195103, 1.33964, 0.495327]
f_524: 8151.40148 [0.057425, -1.04665, -1.05921, 0.320395, -2.10589, 0.195085, 1.33967, 0.495243]
f_525: 8151.40146 [0.0574086, -1.04671, -1.05903, 0.320758, -2.10552, 0.195156, 1.33969, 0.495465]
f_526: 8151.40144 [0.0573923, -1.04675, -1.05892, 0.320351, -2.10515, 0.195546, 1.33966, 0.495229]
f_527: 8151.40141 [0.0573985, -1.04681, -1.05895, 0.320399, -2.10521, 0.195368, 1.33967, 0.495357]
f_528: 8151.40137 [0.0574148, -1.04694, -1.05899, 0.320619, -2.10599, 0.195293, 1.33975, 0.495326]
f_529: 8151.40132 [0.0573966, -1.04707, -1.05849, 0.320203, -2.10549, 0.195489, 1.33973, 0.495272]
f_530: 8151.40124 [0.0573899, -1.04734, -1.05841, 0.320718, -2.10553, 0.195597, 1.33988, 0.495481]
f_531: 8151.40145 [0.0574937, -1.04778, -1.06063, 0.318697, -2.10927, 0.195896, 1.33966, 0.494897]
f_532: 8151.40147 [0.0572493, -1.04734, -1.05844, 0.32108, -2.10569, 0.196964, 1.33956, 0.495303]
f_533: 8151.40122 [0.0573032, -1.04757, -1.05802, 0.319753, -2.10421, 0.196996, 1.33956, 0.495344]
f_534: 8151.40621 [0.0577308, -1.04757, -1.05774, 0.319496, -2.10449, 0.197454, 1.34045, 0.494978]
f_535: 8151.40126 [0.0573304, -1.0476, -1.05699, 0.320159, -2.10531, 0.19668, 1.33997, 0.494972]
f_536: 8151.40296 [0.0575651, -1.04757, -1.05822, 0.320404, -2.10397, 0.197205, 1.33945, 0.494941]
f_537: 8151.40123 [0.0573089, -1.04757, -1.05813, 0.32038, -2.10392, 0.196956, 1.33985, 0.494975]
f_538: 8151.40125 [0.0572891, -1.04756, -1.05783, 0.319832, -2.10383, 0.197078, 1.33955, 0.495377]
f_539: 8151.40118 [0.0573243, -1.04758, -1.05805, 0.320144, -2.10432, 0.19701, 1.33959, 0.495147]
f_540: 8151.40119 [0.0573493, -1.04757, -1.05808, 0.319962, -2.10422, 0.196657, 1.33957, 0.495014]
f_541: 8151.40518 [0.0577071, -1.04757, -1.05801, 0.320119, -2.10433, 0.197041, 1.33961, 0.49514]
f_542: 8151.40119 [0.0573323, -1.04757, -1.05821, 0.320079, -2.10424, 0.196957, 1.33962, 0.495099]
f_543: 8151.40123 [0.0573704, -1.04758, -1.05807, 0.320217, -2.10431, 0.196957, 1.33959, 0.495195]
f_544: 8151.40118 [0.0573291, -1.04758, -1.05805, 0.320185, -2.10427, 0.196918, 1.33957, 0.49514]
f_545: 8151.40118 [0.0573282, -1.04758, -1.05807, 0.32023, -2.10426, 0.196902, 1.33962, 0.495175]
f_546: 8151.40118 [0.0573306, -1.04757, -1.05808, 0.320306, -2.10431, 0.196902, 1.33963, 0.495216]
f_547: 8151.40118 [0.0573259, -1.04758, -1.0581, 0.320352, -2.10429, 0.196941, 1.33959, 0.495257]
f_548: 8151.40118 [0.0573256, -1.04758, -1.0581, 0.320286, -2.10436, 0.197007, 1.33959, 0.495287]
f_549: 8151.40117 [0.0573279, -1.04758, -1.05813, 0.320332, -2.10435, 0.196928, 1.33959, 0.495252]
f_550: 8151.40117 [0.0573309, -1.04758, -1.05814, 0.320329, -2.10442, 0.196927, 1.33958, 0.495238]
f_551: 8151.40116 [0.0573321, -1.04758, -1.05818, 0.320312, -2.10448, 0.196908, 1.33957, 0.495244]
f_552: 8151.40116 [0.0573374, -1.04759, -1.05822, 0.320306, -2.10461, 0.196877, 1.33955, 0.495245]
f_553: 8151.40115 [0.057344, -1.0476, -1.05823, 0.320317, -2.10489, 0.1968, 1.33952, 0.495243]
f_554: 8151.40114 [0.0573568, -1.04761, -1.0584, 0.320236, -2.10506, 0.196694, 1.33956, 0.495273]
f_555: 8151.40114 [0.0573615, -1.04761, -1.05832, 0.32023, -2.10532, 0.196624, 1.33948, 0.49531]
f_556: 8151.40113 [0.0573612, -1.04762, -1.0584, 0.320202, -2.10504, 0.196683, 1.33956, 0.495271]
f_557: 8151.40113 [0.0573569, -1.04762, -1.05837, 0.320246, -2.10513, 0.196714, 1.33962, 0.495244]
f_558: 8151.40113 [0.0573595, -1.04763, -1.05841, 0.320336, -2.10514, 0.196675, 1.33958, 0.49522]
f_559: 8151.40113 [0.0573562, -1.04763, -1.05845, 0.320349, -2.10521, 0.196759, 1.33957, 0.495203]
f_560: 8151.40112 [0.0573582, -1.04764, -1.05848, 0.320468, -2.10517, 0.196701, 1.33957, 0.495186]
f_561: 8151.40112 [0.0573538, -1.04765, -1.05846, 0.320567, -2.10531, 0.196823, 1.3396, 0.495092]
f_562: 8151.40164 [0.0572199, -1.04764, -1.05846, 0.320464, -2.10515, 0.196678, 1.33951, 0.495134]
f_563: 8151.40112 [0.0573564, -1.04764, -1.05855, 0.320548, -2.10524, 0.196748, 1.33958, 0.495214]
f_564: 8151.40112 [0.0573565, -1.04765, -1.05862, 0.320583, -2.10522, 0.196764, 1.33954, 0.495151]
f_565: 8151.40112 [0.0573573, -1.04764, -1.05861, 0.320562, -2.10523, 0.19675, 1.33956, 0.495222]
f_566: 8151.40112 [0.0573573, -1.04765, -1.05862, 0.320549, -2.10526, 0.196751, 1.33956, 0.495236]
f_567: 8151.40111 [0.0573612, -1.04766, -1.05867, 0.32055, -2.10531, 0.196723, 1.33957, 0.495248]
f_568: 8151.4011 [0.0573689, -1.04767, -1.05874, 0.320548, -2.10547, 0.196637, 1.33963, 0.495264]
f_569: 8151.40109 [0.0573768, -1.04771, -1.05857, 0.320618, -2.10557, 0.196466, 1.3396, 0.495401]
f_570: 8151.4011 [0.0573887, -1.04773, -1.05841, 0.320526, -2.1057, 0.196236, 1.33986, 0.495338]
f_571: 8151.40218 [0.0575705, -1.04771, -1.05857, 0.320586, -2.10568, 0.19656, 1.33968, 0.495493]
f_572: 8151.40108 [0.0573834, -1.04771, -1.05865, 0.320585, -2.10557, 0.196306, 1.33953, 0.495268]
f_573: 8151.40108 [0.0573804, -1.04773, -1.0587, 0.320492, -2.1055, 0.196453, 1.33961, 0.49532]
f_574: 8151.40107 [0.0573752, -1.04774, -1.05875, 0.32071, -2.10546, 0.196475, 1.33956, 0.495329]
f_575: 8151.40108 [0.0573757, -1.04775, -1.05877, 0.320543, -2.10549, 0.196489, 1.33943, 0.495331]
f_576: 8151.40169 [0.0572279, -1.04774, -1.05882, 0.320679, -2.10549, 0.196426, 1.33959, 0.495369]
f_577: 8151.40107 [0.057378, -1.04775, -1.05882, 0.320663, -2.1054, 0.196409, 1.33956, 0.495352]
f_578: 8151.40107 [0.0573793, -1.04775, -1.05885, 0.320612, -2.10549, 0.196441, 1.33956, 0.495387]
f_579: 8151.40107 [0.0573821, -1.04776, -1.05889, 0.32062, -2.10548, 0.196377, 1.33953, 0.495387]
f_580: 8151.40106 [0.0573874, -1.04778, -1.05898, 0.320635, -2.10552, 0.196309, 1.33951, 0.495415]
f_581: 8151.40105 [0.0574039, -1.04782, -1.05904, 0.320679, -2.10581, 0.196064, 1.3395, 0.49545]
f_582: 8151.4011 [0.0574592, -1.04783, -1.05913, 0.320206, -2.10659, 0.19529, 1.33947, 0.495654]
f_583: 8151.40185 [0.0575636, -1.04782, -1.05887, 0.320688, -2.10584, 0.196206, 1.33941, 0.495625]
f_584: 8151.40104 [0.0573995, -1.04786, -1.05893, 0.320841, -2.10557, 0.196076, 1.33945, 0.495391]
f_585: 8151.40104 [0.0573937, -1.04789, -1.05921, 0.321019, -2.10559, 0.196198, 1.33935, 0.495397]
f_586: 8151.406 [0.0569698, -1.04785, -1.0589, 0.321021, -2.10557, 0.196058, 1.33945, 0.495442]
f_587: 8151.40103 [0.0574021, -1.04787, -1.0589, 0.320639, -2.10549, 0.196008, 1.33949, 0.495401]
f_588: 8151.40101 [0.0574073, -1.04791, -1.05882, 0.320595, -2.10538, 0.195869, 1.33953, 0.495423]
f_589: 8151.40098 [0.0574136, -1.048, -1.05864, 0.320663, -2.10523, 0.195688, 1.3396, 0.49543]
f_590: 8151.40093 [0.0574281, -1.04817, -1.05878, 0.320766, -2.10507, 0.1954, 1.33943, 0.49547]
f_591: 8151.40087 [0.057447, -1.04851, -1.05801, 0.320715, -2.10388, 0.194616, 1.33976, 0.495665]
f_592: 8151.40083 [0.0574955, -1.04864, -1.05892, 0.320942, -2.10502, 0.194209, 1.33942, 0.495689]
f_593: 8151.40081 [0.0574557, -1.04873, -1.0587, 0.321245, -2.10382, 0.19459, 1.3396, 0.495586]
f_594: 8151.4008 [0.0575071, -1.04889, -1.05899, 0.32133, -2.1045, 0.193822, 1.33939, 0.495759]
f_595: 8151.40075 [0.0574902, -1.04896, -1.05845, 0.320861, -2.10375, 0.193972, 1.33957, 0.495612]
f_596: 8151.4007 [0.0575056, -1.04916, -1.05861, 0.321204, -2.10378, 0.193724, 1.3395, 0.495562]
f_597: 8151.40063 [0.0575339, -1.04956, -1.05769, 0.321444, -2.10328, 0.192802, 1.33967, 0.495434]
f_598: 8151.4006 [0.0575992, -1.04986, -1.05779, 0.32179, -2.10385, 0.191777, 1.33962, 0.495509]
f_599: 8151.4006 [0.0575701, -1.04991, -1.05769, 0.321786, -2.10304, 0.192137, 1.33963, 0.495507]
f_600: 8151.402 [0.0573496, -1.04986, -1.05776, 0.322387, -2.1038, 0.191823, 1.34007, 0.495834]
f_601: 8151.40071 [0.0575917, -1.04986, -1.05794, 0.322287, -2.10364, 0.191708, 1.34018, 0.495614]
f_602: 8151.40467 [0.057193, -1.04986, -1.05784, 0.321367, -2.10396, 0.19186, 1.33975, 0.495549]
f_603: 8151.4006 [0.0575829, -1.04986, -1.05764, 0.321394, -2.1038, 0.191747, 1.33973, 0.495676]
f_604: 8151.40061 [0.0575833, -1.04986, -1.05736, 0.321429, -2.10356, 0.191469, 1.33978, 0.495613]
f_605: 8151.4014 [0.0574137, -1.04986, -1.05774, 0.32139, -2.10368, 0.191636, 1.33969, 0.495742]
f_606: 8151.40058 [0.0575737, -1.04986, -1.05757, 0.321497, -2.10386, 0.191846, 1.33975, 0.495553]
f_607: 8151.40057 [0.0575704, -1.04987, -1.05776, 0.321878, -2.10403, 0.192009, 1.33971, 0.495428]
f_608: 8151.40057 [0.0575542, -1.04987, -1.05799, 0.321838, -2.10372, 0.192255, 1.33961, 0.495462]
f_609: 8151.40058 [0.0575411, -1.04989, -1.05822, 0.321995, -2.10351, 0.192489, 1.33952, 0.495429]
f_610: 8151.40095 [0.0576806, -1.04989, -1.05805, 0.321828, -2.1037, 0.192142, 1.3396, 0.49548]
f_611: 8151.40057 [0.0575639, -1.04988, -1.05811, 0.321707, -2.10376, 0.192153, 1.33967, 0.495452]
f_612: 8151.40057 [0.0575644, -1.04989, -1.05805, 0.321752, -2.10389, 0.192126, 1.33959, 0.495508]
f_613: 8151.40057 [0.0575612, -1.04987, -1.05811, 0.321704, -2.10377, 0.19217, 1.33967, 0.495452]
f_614: 8151.40057 [0.0575658, -1.04988, -1.05806, 0.321793, -2.1038, 0.192097, 1.33965, 0.495457]
f_615: 8151.40058 [0.0575399, -1.04988, -1.0581, 0.321691, -2.10373, 0.19216, 1.33969, 0.495421]
f_616: 8151.40056 [0.0575654, -1.04988, -1.05808, 0.321645, -2.10376, 0.192169, 1.33965, 0.495437]
f_617: 8151.40056 [0.0575628, -1.04988, -1.05806, 0.321579, -2.10375, 0.192199, 1.33965, 0.495436]
f_618: 8151.40055 [0.0575623, -1.04988, -1.05804, 0.321441, -2.10368, 0.192233, 1.33964, 0.495419]
f_619: 8151.40055 [0.0575623, -1.04989, -1.05813, 0.321234, -2.10348, 0.192226, 1.33963, 0.495376]
f_620: 8151.40055 [0.0575594, -1.04989, -1.05805, 0.321356, -2.10368, 0.192286, 1.33959, 0.4954]
f_621: 8151.40054 [0.0575587, -1.04989, -1.05806, 0.321318, -2.10373, 0.192356, 1.33957, 0.495333]
f_622: 8151.40053 [0.0575552, -1.0499, -1.05813, 0.321261, -2.1038, 0.192463, 1.33954, 0.495206]
f_623: 8151.40053 [0.0575485, -1.04991, -1.05817, 0.321141, -2.10368, 0.192642, 1.33945, 0.495132]
f_624: 8151.40104 [0.0576927, -1.0499, -1.05811, 0.321307, -2.10387, 0.19252, 1.33954, 0.49519]
f_625: 8151.40053 [0.0575581, -1.0499, -1.05811, 0.321272, -2.10389, 0.192468, 1.33952, 0.49513]
f_626: 8151.40053 [0.0575542, -1.0499, -1.05816, 0.321226, -2.10385, 0.192484, 1.33953, 0.495186]
f_627: 8151.40052 [0.0575571, -1.0499, -1.05817, 0.321205, -2.10389, 0.192531, 1.33955, 0.495198]
f_628: 8151.40052 [0.0575527, -1.0499, -1.05816, 0.321184, -2.10394, 0.192635, 1.33961, 0.495194]
f_629: 8151.40051 [0.0575459, -1.0499, -1.05821, 0.321184, -2.10391, 0.19283, 1.33969, 0.495084]
f_630: 8151.40057 [0.0575908, -1.04991, -1.0582, 0.321096, -2.10395, 0.19289, 1.33968, 0.495104]
f_631: 8151.40051 [0.0575422, -1.0499, -1.05814, 0.32111, -2.10396, 0.192888, 1.33964, 0.495099]
f_632: 8151.4005 [0.057543, -1.04991, -1.0581, 0.320891, -2.10402, 0.192948, 1.33955, 0.49505]
f_633: 8151.40049 [0.0575356, -1.04992, -1.05822, 0.32102, -2.10407, 0.193117, 1.33956, 0.495116]
f_634: 8151.40049 [0.0575262, -1.04995, -1.05843, 0.321255, -2.10407, 0.193385, 1.33948, 0.495069]
f_635: 8151.40263 [0.0578063, -1.04995, -1.05853, 0.321232, -2.10409, 0.193428, 1.33945, 0.495034]
f_636: 8151.40047 [0.0575227, -1.04995, -1.05845, 0.321083, -2.10423, 0.193512, 1.33951, 0.49518]
f_637: 8151.40048 [0.0575263, -1.04994, -1.05872, 0.320691, -2.10442, 0.19372, 1.33949, 0.495018]
f_638: 8151.40082 [0.0576348, -1.04995, -1.05839, 0.32093, -2.10407, 0.193548, 1.3395, 0.495173]
f_639: 8151.40046 [0.0575152, -1.04995, -1.0585, 0.321048, -2.10425, 0.193774, 1.33953, 0.495156]
f_640: 8151.40045 [0.0574958, -1.04996, -1.05863, 0.320963, -2.10431, 0.194306, 1.3395, 0.495183]
f_641: 8151.40045 [0.0574624, -1.04994, -1.0591, 0.320916, -2.10448, 0.195244, 1.33942, 0.495039]
f_642: 8151.40199 [0.0577389, -1.04996, -1.05861, 0.32093, -2.10459, 0.194284, 1.33947, 0.495247]
f_643: 8151.40045 [0.0574913, -1.04998, -1.05868, 0.320942, -2.10473, 0.194537, 1.33932, 0.495093]
f_644: 8151.40072 [0.0575985, -1.04997, -1.05862, 0.320875, -2.10432, 0.194321, 1.33961, 0.495121]
f_645: 8151.40044 [0.057497, -1.04997, -1.05875, 0.320831, -2.10441, 0.194411, 1.33959, 0.495212]
f_646: 8151.40043 [0.057486, -1.04999, -1.059, 0.32075, -2.10447, 0.194776, 1.33957, 0.495321]
f_647: 8151.40042 [0.0574856, -1.04998, -1.05911, 0.320674, -2.10499, 0.195035, 1.33955, 0.495197]
f_648: 8151.40043 [0.0574747, -1.04996, -1.05915, 0.320192, -2.10494, 0.195366, 1.33957, 0.495165]
f_649: 8151.4026 [0.0577583, -1.04998, -1.05896, 0.320787, -2.105, 0.195167, 1.33959, 0.495213]
f_650: 8151.40042 [0.057484, -1.04998, -1.0592, 0.320913, -2.10518, 0.195101, 1.33953, 0.495181]
f_651: 8151.40101 [0.0576313, -1.04998, -1.05922, 0.320893, -2.10518, 0.195075, 1.33953, 0.495108]
f_652: 8151.40042 [0.05748, -1.04998, -1.05919, 0.320898, -2.10517, 0.195173, 1.33956, 0.49526]
f_653: 8151.40041 [0.0574737, -1.04999, -1.05931, 0.320874, -2.10509, 0.195352, 1.33959, 0.495349]
f_654: 8151.40041 [0.0574731, -1.05, -1.05935, 0.320886, -2.10535, 0.195464, 1.33957, 0.49538]
f_655: 8151.40041 [0.0574695, -1.05003, -1.05945, 0.320965, -2.10559, 0.195649, 1.33954, 0.495431]
f_656: 8151.4004 [0.057472, -1.05006, -1.05938, 0.321128, -2.10556, 0.195554, 1.33957, 0.495483]
f_657: 8151.40041 [0.0574731, -1.05013, -1.05928, 0.321479, -2.1054, 0.195438, 1.33964, 0.495622]
f_658: 8151.40104 [0.0573069, -1.05007, -1.05933, 0.321075, -2.10534, 0.195731, 1.33949, 0.495321]
f_659: 8151.4004 [0.0574534, -1.05008, -1.05943, 0.321303, -2.10541, 0.195906, 1.33957, 0.495389]
f_660: 8151.40039 [0.0574442, -1.0501, -1.05953, 0.321083, -2.10542, 0.196178, 1.33957, 0.495444]
f_661: 8151.40039 [0.0574241, -1.05015, -1.05985, 0.320872, -2.10528, 0.196806, 1.33963, 0.495555]
f_662: 8151.40039 [0.0574326, -1.05009, -1.05964, 0.320925, -2.10542, 0.196474, 1.33954, 0.495374]
f_663: 8151.40038 [0.0574474, -1.05013, -1.05954, 0.32096, -2.10576, 0.196323, 1.33958, 0.495392]
f_664: 8151.40037 [0.0574448, -1.0502, -1.05959, 0.320953, -2.10629, 0.196552, 1.33957, 0.495321]
f_665: 8151.40036 [0.0574263, -1.05025, -1.05978, 0.320832, -2.10635, 0.197123, 1.33969, 0.495444]
f_666: 8151.40038 [0.0574114, -1.05032, -1.06015, 0.320905, -2.10703, 0.197965, 1.33963, 0.495511]
f_667: 8151.4158 [0.0566742, -1.05025, -1.05961, 0.320898, -2.10654, 0.197158, 1.33985, 0.495581]
f_668: 8151.40041 [0.057458, -1.05027, -1.05971, 0.320765, -2.10726, 0.196836, 1.34003, 0.495358]
f_669: 8151.40091 [0.0572759, -1.05025, -1.05985, 0.320979, -2.10614, 0.197287, 1.33986, 0.495239]
f_670: 8151.40034 [0.0574166, -1.05027, -1.05955, 0.320958, -2.10602, 0.197186, 1.33976, 0.495328]
f_671: 8151.40033 [0.057427, -1.0503, -1.05948, 0.320808, -2.1059, 0.196938, 1.3398, 0.495307]
f_672: 8151.40032 [0.0574505, -1.05035, -1.05913, 0.32091, -2.10595, 0.196342, 1.33983, 0.495261]
f_673: 8151.40031 [0.057461, -1.05041, -1.05883, 0.321074, -2.10584, 0.195929, 1.33984, 0.495247]
f_674: 8151.40038 [0.0575238, -1.05048, -1.05814, 0.321435, -2.10637, 0.194487, 1.33976, 0.495075]
f_675: 8151.40407 [0.0578263, -1.0504, -1.05858, 0.320733, -2.10543, 0.195865, 1.33994, 0.49511]
f_676: 8151.40034 [0.057447, -1.05045, -1.05906, 0.321848, -2.10615, 0.196285, 1.33974, 0.495398]
f_677: 8151.4012 [0.0572701, -1.05042, -1.05865, 0.321122, -2.10594, 0.196132, 1.33974, 0.495007]
f_678: 8151.4003 [0.0574603, -1.05043, -1.05901, 0.320952, -2.10592, 0.196008, 1.33958, 0.495237]
f_679: 8151.40031 [0.0574472, -1.05044, -1.05893, 0.320937, -2.1062, 0.196249, 1.33979, 0.495214]
f_680: 8151.40528 [0.0570348, -1.05043, -1.05901, 0.320886, -2.10585, 0.196, 1.33959, 0.495262]
f_681: 8151.40031 [0.0574576, -1.05043, -1.05897, 0.321197, -2.10596, 0.196057, 1.33962, 0.495173]
f_682: 8151.4003 [0.05746, -1.05044, -1.059, 0.320936, -2.10594, 0.196025, 1.33957, 0.495225]
f_683: 8151.4003 [0.057462, -1.05044, -1.05893, 0.320944, -2.10594, 0.196069, 1.33955, 0.495274]
f_684: 8151.4003 [0.05746, -1.05044, -1.05898, 0.32094, -2.10591, 0.196004, 1.33956, 0.495222]
f_685: 8151.4003 [0.0574618, -1.05044, -1.05896, 0.320904, -2.10588, 0.196007, 1.33959, 0.495245]
f_686: 8151.4003 [0.0574628, -1.05045, -1.05894, 0.32085, -2.10586, 0.195977, 1.33963, 0.4953]
f_687: 8151.4003 [0.0574698, -1.05047, -1.05906, 0.320895, -2.10582, 0.195852, 1.33961, 0.495348]
f_688: 8151.40029 [0.0574705, -1.05048, -1.059, 0.320742, -2.10571, 0.195797, 1.33962, 0.49541]
f_689: 8151.40028 [0.0574658, -1.0505, -1.0589, 0.320894, -2.10571, 0.195873, 1.33964, 0.495327]
f_690: 8151.40029 [0.0574621, -1.05053, -1.05874, 0.321133, -2.10576, 0.195895, 1.33961, 0.495136]
f_691: 8151.40127 [0.0572745, -1.05049, -1.05883, 0.320935, -2.10558, 0.195812, 1.33951, 0.495275]
f_692: 8151.40028 [0.0574589, -1.05051, -1.05886, 0.321092, -2.10555, 0.195939, 1.33968, 0.495382]
f_693: 8151.40028 [0.0574655, -1.05053, -1.05884, 0.321053, -2.10562, 0.195811, 1.33968, 0.495406]
f_694: 8151.40027 [0.0574655, -1.05054, -1.05881, 0.320945, -2.10557, 0.195813, 1.33962, 0.4953]
f_695: 8151.40027 [0.0574692, -1.05057, -1.0589, 0.320541, -2.10553, 0.195862, 1.33952, 0.495246]
f_696: 8151.40027 [0.0574815, -1.05059, -1.05865, 0.32053, -2.1054, 0.195454, 1.33957, 0.495178]
f_697: 8151.40142 [0.0572562, -1.05057, -1.05874, 0.320551, -2.10543, 0.195936, 1.33941, 0.495268]
f_698: 8151.40026 [0.0574668, -1.05058, -1.05885, 0.320713, -2.10534, 0.195894, 1.33958, 0.495336]
f_699: 8151.40026 [0.0574531, -1.05061, -1.05891, 0.321022, -2.10511, 0.196001, 1.33964, 0.495473]
f_700: 8151.40025 [0.0574513, -1.05065, -1.059, 0.32069, -2.10508, 0.196153, 1.33966, 0.495488]
f_701: 8151.40027 [0.057439, -1.05072, -1.05934, 0.320706, -2.10511, 0.19665, 1.33976, 0.495716]
f_702: 8151.40521 [0.0578846, -1.05066, -1.05921, 0.320687, -2.10487, 0.195938, 1.33951, 0.495399]
f_703: 8151.40025 [0.0574448, -1.05068, -1.05871, 0.320813, -2.10478, 0.196099, 1.33969, 0.495434]
f_704: 8151.40025 [0.0574518, -1.05073, -1.05823, 0.320968, -2.10425, 0.195562, 1.33962, 0.495389]
f_705: 8151.40422 [0.0578304, -1.05068, -1.05871, 0.320724, -2.10487, 0.195999, 1.33987, 0.495476]
f_706: 8151.40026 [0.0574257, -1.05069, -1.05875, 0.320761, -2.1043, 0.196333, 1.33957, 0.495251]
f_707: 8151.40038 [0.0573754, -1.05068, -1.05874, 0.320822, -2.10467, 0.196111, 1.33968, 0.495618]
f_708: 8151.40024 [0.0574458, -1.05069, -1.05881, 0.32094, -2.10488, 0.196057, 1.33959, 0.495349]
f_709: 8151.40024 [0.0574438, -1.0507, -1.05871, 0.320736, -2.10481, 0.196111, 1.33958, 0.495295]
f_710: 8151.40024 [0.0574446, -1.05072, -1.05876, 0.320737, -2.10464, 0.196045, 1.33962, 0.495327]
f_711: 8151.40053 [0.057544, -1.05071, -1.05869, 0.320838, -2.10484, 0.196186, 1.33959, 0.495313]
f_712: 8151.40024 [0.0574379, -1.05071, -1.05866, 0.320824, -2.1048, 0.196185, 1.3396, 0.495332]
f_713: 8151.40024 [0.0574368, -1.05071, -1.0587, 0.320899, -2.10472, 0.196204, 1.33958, 0.495359]
f_714: 8151.40024 [0.0574507, -1.0507, -1.05865, 0.320794, -2.1048, 0.196201, 1.33956, 0.495342]
f_715: 8151.40023 [0.0574414, -1.05071, -1.05864, 0.320816, -2.10486, 0.196153, 1.33961, 0.495326]
f_716: 8151.40023 [0.0574439, -1.05071, -1.0586, 0.320838, -2.10495, 0.196075, 1.33959, 0.495315]
f_717: 8151.40023 [0.0574477, -1.05072, -1.05853, 0.320804, -2.10497, 0.196019, 1.3396, 0.49528]
f_718: 8151.40023 [0.0574547, -1.05073, -1.05845, 0.320738, -2.10496, 0.195855, 1.33959, 0.495249]
f_719: 8151.40023 [0.0574513, -1.05075, -1.05838, 0.320688, -2.10482, 0.195882, 1.33962, 0.495225]
f_720: 8151.40023 [0.0574436, -1.05079, -1.05825, 0.320485, -2.10445, 0.195935, 1.33954, 0.49519]
f_721: 8151.40022 [0.0574483, -1.05077, -1.05836, 0.320704, -2.10476, 0.195925, 1.33962, 0.49523]
f_722: 8151.40022 [0.057453, -1.05079, -1.05845, 0.320694, -2.10483, 0.195908, 1.33965, 0.495291]
f_723: 8151.40021 [0.0574448, -1.05083, -1.05841, 0.320613, -2.10476, 0.196087, 1.33973, 0.495203]
f_724: 8151.4002 [0.0574467, -1.05087, -1.05843, 0.320606, -2.1048, 0.195965, 1.33969, 0.495362]
f_725: 8151.4002 [0.0574591, -1.0509, -1.05831, 0.320422, -2.10465, 0.195751, 1.33968, 0.495257]
f_726: 8151.40019 [0.0574401, -1.05093, -1.05819, 0.320549, -2.1047, 0.196154, 1.33972, 0.495272]
f_727: 8151.40019 [0.0574396, -1.05096, -1.05838, 0.320336, -2.10493, 0.196393, 1.33981, 0.495344]
f_728: 8151.40019 [0.0574235, -1.05099, -1.05828, 0.320689, -2.10467, 0.196525, 1.33989, 0.495303]
f_729: 8151.4031 [0.0571158, -1.05096, -1.0583, 0.320287, -2.10495, 0.196331, 1.33994, 0.495402]
f_730: 8151.40019 [0.0574377, -1.05097, -1.05821, 0.320552, -2.10493, 0.196327, 1.33984, 0.495335]
f_731: 8151.40019 [0.0574334, -1.05097, -1.05821, 0.320394, -2.10466, 0.19635, 1.3398, 0.495333]
f_732: 8151.40054 [0.0573216, -1.05097, -1.05818, 0.320515, -2.10496, 0.196368, 1.33983, 0.495262]
f_733: 8151.40018 [0.0574408, -1.05097, -1.05829, 0.320562, -2.10494, 0.196273, 1.3398, 0.495331]
f_734: 8151.40018 [0.0574476, -1.05099, -1.05836, 0.320483, -2.10496, 0.196162, 1.33979, 0.495381]
f_735: 8151.40018 [0.0574444, -1.05101, -1.05829, 0.320455, -2.10487, 0.196205, 1.33979, 0.495351]
f_736: 8151.40018 [0.0574484, -1.05105, -1.05831, 0.320302, -2.10465, 0.19608, 1.33978, 0.495402]
f_737: 8151.40017 [0.0574399, -1.05108, -1.05813, 0.320431, -2.10484, 0.196276, 1.33981, 0.495338]
f_738: 8151.40016 [0.0574282, -1.05116, -1.05808, 0.320603, -2.1053, 0.1967, 1.33986, 0.495388]
f_739: 8151.40015 [0.0574353, -1.05125, -1.05791, 0.320459, -2.10509, 0.19648, 1.33989, 0.495352]
f_740: 8151.40014 [0.0574096, -1.05141, -1.05751, 0.320302, -2.10417, 0.196724, 1.33989, 0.495357]
f_741: 8151.40013 [0.057419, -1.05148, -1.05768, 0.320376, -2.1052, 0.196955, 1.34001, 0.495426]
f_742: 8151.40012 [0.0574236, -1.05151, -1.05743, 0.320408, -2.10491, 0.196658, 1.33996, 0.495364]
f_743: 8151.40012 [0.0574298, -1.05155, -1.05717, 0.320771, -2.10483, 0.19634, 1.33994, 0.495384]
f_744: 8151.4273 [0.0564301, -1.05151, -1.0576, 0.320473, -2.10494, 0.196636, 1.33972, 0.495278]
f_745: 8151.40013 [0.057421, -1.05155, -1.05717, 0.320689, -2.10498, 0.196654, 1.34, 0.495292]
f_746: 8151.4033 [0.0570848, -1.05151, -1.05728, 0.320297, -2.10497, 0.196654, 1.34003, 0.49549]
f_747: 8151.40012 [0.0574353, -1.05152, -1.05744, 0.320271, -2.10488, 0.196448, 1.33985, 0.495469]
f_748: 8151.40012 [0.0574406, -1.05153, -1.0574, 0.320274, -2.10462, 0.196253, 1.33988, 0.495477]
f_749: 8151.40014 [0.0574571, -1.05152, -1.05744, 0.320373, -2.10484, 0.196494, 1.33981, 0.495549]
f_750: 8151.40012 [0.0574308, -1.05152, -1.05742, 0.32042, -2.10483, 0.196494, 1.33988, 0.495493]
f_751: 8151.40012 [0.0574392, -1.05151, -1.05744, 0.320324, -2.10492, 0.196405, 1.33985, 0.495469]
f_752: 8151.40012 [0.0574357, -1.05151, -1.05738, 0.320333, -2.10491, 0.196412, 1.33983, 0.495468]
f_753: 8151.40012 [0.0574392, -1.05151, -1.05744, 0.320326, -2.10492, 0.196402, 1.33985, 0.495467]
f_754: 8151.40012 [0.0574387, -1.05152, -1.05743, 0.320348, -2.10491, 0.196385, 1.33984, 0.495507]
f_755: 8151.40019 [0.0574882, -1.05152, -1.05745, 0.320333, -2.10487, 0.196382, 1.33984, 0.495446]
f_756: 8151.40011 [0.0574375, -1.05152, -1.05745, 0.32036, -2.1049, 0.196413, 1.33982, 0.495441]
f_757: 8151.4001 [0.0574374, -1.05152, -1.0575, 0.32042, -2.10494, 0.196402, 1.33978, 0.495433]
f_758: 8151.4001 [0.0574415, -1.05154, -1.05755, 0.320465, -2.10507, 0.196362, 1.33974, 0.495447]
f_759: 8151.40009 [0.0574462, -1.05156, -1.05758, 0.320493, -2.10516, 0.196319, 1.33973, 0.495395]
f_760: 8151.40009 [0.0574559, -1.0516, -1.05751, 0.320437, -2.10512, 0.196084, 1.33978, 0.495262]
f_761: 8151.40009 [0.0574511, -1.05161, -1.05753, 0.320387, -2.1049, 0.19624, 1.33977, 0.495324]
f_762: 8151.40008 [0.0574441, -1.05162, -1.05756, 0.320547, -2.10479, 0.196245, 1.33972, 0.495389]
f_763: 8151.40007 [0.0574503, -1.05166, -1.05759, 0.320366, -2.10446, 0.196233, 1.33963, 0.495328]
f_764: 8151.40008 [0.0574435, -1.05168, -1.05757, 0.320504, -2.10396, 0.196021, 1.33966, 0.49541]
f_765: 8151.40016 [0.0574992, -1.05167, -1.05756, 0.320202, -2.1046, 0.196243, 1.33971, 0.495431]
f_766: 8151.40007 [0.0574437, -1.05167, -1.05752, 0.320459, -2.10465, 0.196348, 1.33959, 0.495367]
f_767: 8151.40006 [0.0574434, -1.05168, -1.05773, 0.320469, -2.10475, 0.196345, 1.33968, 0.495346]
f_768: 8151.40007 [0.0574489, -1.05169, -1.05774, 0.320367, -2.10495, 0.196327, 1.33958, 0.495439]
f_769: 8151.40017 [0.0575084, -1.05169, -1.05773, 0.320469, -2.10473, 0.196327, 1.33968, 0.49535]
f_770: 8151.40006 [0.0574386, -1.05168, -1.05769, 0.320419, -2.10477, 0.196442, 1.33971, 0.495373]
f_771: 8151.40015 [0.0573849, -1.05168, -1.05771, 0.320512, -2.10477, 0.196345, 1.3397, 0.495324]
f_772: 8151.40006 [0.0574436, -1.05168, -1.05773, 0.320456, -2.10475, 0.196326, 1.33966, 0.495352]
f_773: 8151.40006 [0.0574441, -1.05169, -1.05773, 0.320448, -2.10479, 0.196346, 1.33966, 0.495315]
f_774: 8151.40006 [0.0574436, -1.05169, -1.05773, 0.320482, -2.10483, 0.196358, 1.33968, 0.495317]
f_775: 8151.40006 [0.0574436, -1.0517, -1.05772, 0.320535, -2.10485, 0.196364, 1.33966, 0.49527]
f_776: 8151.40006 [0.0574456, -1.05172, -1.05765, 0.320653, -2.10494, 0.196307, 1.33967, 0.495228]
f_777: 8151.40007 [0.0574666, -1.05172, -1.05769, 0.32058, -2.10486, 0.196311, 1.3397, 0.495263]
f_778: 8151.40005 [0.0574433, -1.05172, -1.05772, 0.32064, -2.10486, 0.196393, 1.33967, 0.495235]
f_779: 8151.40005 [0.057439, -1.05173, -1.05783, 0.320546, -2.1048, 0.1965, 1.33965, 0.495318]
f_780: 8151.40004 [0.0574446, -1.05177, -1.05798, 0.320255, -2.10473, 0.196514, 1.33958, 0.495315]
f_781: 8151.40003 [0.0574419, -1.05179, -1.05818, 0.320668, -2.10507, 0.196667, 1.33956, 0.495354]
f_782: 8151.40003 [0.0574334, -1.05183, -1.05808, 0.320983, -2.10518, 0.196815, 1.33957, 0.495352]
f_783: 8151.40003 [0.0574429, -1.05184, -1.05842, 0.320857, -2.10555, 0.196952, 1.33955, 0.495325]
f_784: 8151.40002 [0.057439, -1.05188, -1.05852, 0.320967, -2.10525, 0.196901, 1.33949, 0.49537]
f_785: 8151.40003 [0.0574363, -1.05191, -1.05873, 0.320822, -2.10541, 0.197155, 1.33942, 0.495542]
f_786: 8151.40008 [0.0574886, -1.05188, -1.05848, 0.320986, -2.10537, 0.196701, 1.33942, 0.495457]
f_787: 8151.40002 [0.0574346, -1.05189, -1.05856, 0.321131, -2.10535, 0.197024, 1.33956, 0.495313]
f_788: 8151.40002 [0.057433, -1.05192, -1.05875, 0.321363, -2.10568, 0.197218, 1.33956, 0.495313]
f_789: 8151.40348 [0.0570734, -1.05189, -1.05856, 0.321264, -2.10524, 0.19703, 1.33958, 0.49542]
f_790: 8151.40001 [0.0574396, -1.05191, -1.05856, 0.321012, -2.1053, 0.196893, 1.33966, 0.495335]
f_791: 8151.40001 [0.0574347, -1.05192, -1.05849, 0.321088, -2.10514, 0.196905, 1.33965, 0.495328]
f_792: 8151.40001 [0.0574377, -1.05194, -1.05864, 0.321091, -2.10526, 0.196938, 1.33961, 0.495325]
f_793: 8151.4 [0.0574406, -1.05198, -1.05879, 0.321189, -2.10545, 0.196993, 1.3396, 0.495318]
f_794: 8151.4 [0.0574394, -1.05206, -1.05905, 0.321446, -2.1055, 0.19711, 1.33953, 0.495339]
f_795: 8151.40124 [0.057225, -1.05199, -1.05857, 0.320892, -2.10554, 0.197078, 1.33941, 0.495221]
f_796: 8151.4 [0.0574374, -1.05202, -1.05884, 0.321167, -2.10569, 0.197202, 1.33952, 0.495238]
f_797: 8151.40111 [0.0572458, -1.05202, -1.05895, 0.320982, -2.10581, 0.19706, 1.33952, 0.495206]
f_798: 8151.40001 [0.0574491, -1.05202, -1.05895, 0.321107, -2.10578, 0.196985, 1.33946, 0.49528]
f_799: 8151.40001 [0.0574546, -1.05201, -1.05891, 0.32109, -2.10565, 0.19727, 1.3395, 0.495282]
f_800: 8151.4 [0.0574367, -1.05202, -1.05892, 0.321131, -2.10561, 0.197189, 1.33956, 0.495204]
f_801: 8151.39999 [0.0574399, -1.05202, -1.05897, 0.321054, -2.10555, 0.197169, 1.3396, 0.495256]
f_802: 8151.39999 [0.0574407, -1.05203, -1.05899, 0.32087, -2.10539, 0.197105, 1.33959, 0.495277]
f_803: 8151.39998 [0.057439, -1.05207, -1.05899, 0.320838, -2.10509, 0.197045, 1.33958, 0.495298]
f_804: 8151.39998 [0.0574451, -1.05211, -1.05921, 0.320761, -2.10543, 0.197166, 1.33958, 0.49529]
f_805: 8151.39998 [0.0574433, -1.05215, -1.05929, 0.320776, -2.10533, 0.197172, 1.33956, 0.495272]
f_806: 8151.39997 [0.0574483, -1.05223, -1.05969, 0.320802, -2.10578, 0.197391, 1.33955, 0.495265]
f_807: 8151.39997 [0.0574465, -1.05231, -1.05988, 0.320697, -2.10557, 0.197442, 1.33949, 0.495309]
f_808: 8151.41144 [0.0580977, -1.05223, -1.05938, 0.320919, -2.10598, 0.197317, 1.33966, 0.495339]
f_809: 8151.39997 [0.0574507, -1.05225, -1.05939, 0.321038, -2.10584, 0.197139, 1.33967, 0.495232]
f_810: 8151.39997 [0.0574441, -1.05228, -1.05955, 0.321253, -2.10547, 0.197261, 1.33972, 0.495257]
f_811: 8151.4018 [0.0571874, -1.05225, -1.05927, 0.320984, -2.10586, 0.197306, 1.33969, 0.495306]
f_812: 8151.39996 [0.0574519, -1.05226, -1.05947, 0.320946, -2.10569, 0.197185, 1.33962, 0.495212]
f_813: 8151.39997 [0.0574606, -1.05227, -1.05961, 0.320827, -2.10563, 0.197046, 1.33965, 0.495308]
f_814: 8151.39996 [0.0574508, -1.05226, -1.05948, 0.320878, -2.10567, 0.197211, 1.33969, 0.495162]
f_815: 8151.39996 [0.0574525, -1.05227, -1.05955, 0.320961, -2.10573, 0.197224, 1.33966, 0.495216]
f_816: 8151.40037 [0.0575759, -1.05226, -1.05945, 0.320933, -2.1057, 0.197186, 1.33963, 0.495224]
f_817: 8151.39996 [0.0574525, -1.05227, -1.05944, 0.320924, -2.10568, 0.19719, 1.33965, 0.495239]
f_818: 8151.39996 [0.0574508, -1.05227, -1.05937, 0.320885, -2.10559, 0.197183, 1.33969, 0.495264]
f_819: 8151.39996 [0.0574509, -1.05228, -1.05929, 0.320919, -2.10538, 0.19708, 1.33963, 0.495193]
f_820: 8151.39996 [0.0574512, -1.05228, -1.05939, 0.320887, -2.10559, 0.197184, 1.3397, 0.495255]
f_821: 8151.39996 [0.0574498, -1.05229, -1.05935, 0.320877, -2.10547, 0.197165, 1.33966, 0.495244]
f_822: 8151.39995 [0.0574517, -1.05229, -1.05928, 0.320894, -2.10545, 0.197081, 1.3397, 0.495241]
f_823: 8151.39995 [0.0574503, -1.0523, -1.05922, 0.320991, -2.10546, 0.197084, 1.33971, 0.495215]
f_824: 8151.39995 [0.0574533, -1.05231, -1.0592, 0.321, -2.10545, 0.197008, 1.33973, 0.4952]
  9.607621 seconds (1.44 M allocations: 148.847 MiB, 0.38% gc time)
Generalized Linear Mixed Model fit by maximum likelihood
  Formula: r2 ~ 1 + a + g + b + s + (1 | id) + (1 | item)
  Distribution: Distributions.Bernoulli{Float64}
  Link: GLM.LogitLink()

  Deviance: 8151.4000

Variance components:
          Column    Variance   Std.Dev.  
 id   (Intercept)  1.79487217 1.33972840
 item (Intercept)  0.24522279 0.49519975

 Number of obs: 7584; levels of grouping factors: 316, 24

Fixed-effects parameters:
              Estimate Std.Error  z value P(>|z|)
(Intercept)   0.197008  0.405161 0.486248  0.6268
a            0.0574533 0.0167575  3.42852  0.0006
g: M             0.321  0.191261  1.67834  0.0933
b: scold       -1.0592  0.256755 -4.12535   <1e-4
b: shout      -2.10545  0.258478 -8.14555  <1e-15
s: self       -1.05231  0.210261 -5.00476   <1e-6


````





This fit provided slightly better results (Laplace approximation to the deviance of 8151.400 versus 8151.583) but took 6 times as long.
That is not terribly important when the times involved are a few seconds but can be important when the fit requires many hours or days of computing time.

The comparison of the slow and fast fit is available in the optimization summary after the slow fit.

````julia
julia> mdl1.LMM.optsum
Initial parameter vector: [0.0543791, -1.01344, -1.0165, 0.304089, -2.0218, 0.208273, 1.33956, 0.496833]
Initial objective value:  8151.583340132032

Optimizer (from NLopt):   LN_BOBYQA
Lower bounds:             [-Inf, -Inf, -Inf, -Inf, -Inf, -Inf, 0.0, 0.0]
ftol_rel:                 1.0e-12
ftol_abs:                 1.0e-8
xtol_rel:                 0.0
xtol_abs:                 [1.0e-10, 1.0e-10]
initial_step:             [0.135142, 0.00558444, 0.0637411, 0.0858438, 0.0864116, 0.0702961, 0.05, 0.05]
maxfeval:                 -1

Function evaluations:     824
Final parameter vector:   [0.0574533, -1.05231, -1.0592, 0.321, -2.10545, 0.197008, 1.33973, 0.4952]
Final objective value:    8151.399954176272
Return code:              FTOL_REACHED


````

