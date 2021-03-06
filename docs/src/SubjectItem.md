````julia
julia> using DataFrames, MixedModels, RData

julia> const dat = Dict(Symbol(k)=>v for (k,v) in 
    load(joinpath(dirname(pathof(MixedModels)), "..", "test", "dat.rda")));

````



````julia
julia> mm1 = fit(LinearMixedModel, @formula(Y ~ 1+S+T+U+V+W+X+Z+(1+S+T+U+V+W+X+Z|G)+(1+S+T+U+V+W+X+Z|H)), dat[:kb07])
Linear mixed model fit by maximum likelihood
 Formula: Y ~ 1 + S + T + U + V + W + X + Z + ((1 + S + T + U + V + W + X + Z) | G) + ((1 + S + T + U + V + W + X + Z) | H)
     logLik        -2 logLik          AIC             BIC       
 -1.42931596×10⁴  2.85863192×10⁴  2.87483192×10⁴  2.91930068×10⁴

Variance components:
              Column     Variance   Std.Dev.    Corr.
 G        (Intercept)   90782.9823 301.302145
          S              5183.6887  71.997838 -0.43
          T              5545.2694  74.466566 -0.47  0.07
          U              7592.2351  87.133433  0.21 -0.20  0.41
          V              8828.0524  93.957716  0.20 -0.76 -0.54 -0.20
          W              1822.0044  42.684943  0.47 -0.53 -0.11 -0.44  0.28
          X              7424.6857  86.166616 -0.10  0.13 -0.05 -0.86 -0.06  0.70
          Z              3801.7944  61.658693 -0.48  0.41 -0.39 -0.09  0.18 -0.78 -0.39
 H        (Intercept)  130028.5528 360.594721
          S              1855.5643  43.076261 -0.34
          T             62432.1537 249.864271 -0.68 -0.45
          U              2955.9678  54.368813  0.20 -0.02 -0.18
          V              1040.6601  32.259264  0.57 -0.76  0.02  0.01
          W              1616.8109  40.209587  0.28 -0.04 -0.27  0.44 -0.21
          X              4699.1718  68.550505  0.08 -0.24  0.21 -0.13 -0.26  0.02
          Z              4821.2935  69.435535  0.04 -0.47  0.32 -0.68  0.65 -0.69 -0.10
 Residual              399589.9278 632.131258
 Number of obs: 1790; levels of grouping factors: 56, 32

  Fixed-effects parameters:
             Estimate Std.Error  z value P(>|z|)
(Intercept)   2180.63   76.8621  28.3706  <1e-99
S            -66.9897   19.3344 -3.46479  0.0005
T            -333.881   47.6791 -7.00268  <1e-11
U             78.9869   21.2419  3.71846  0.0002
V             22.1517    20.333  1.08945  0.2760
W            -18.9246   17.5022 -1.08127  0.2796
X             5.26219    22.421 0.234699  0.8144
Z            -23.9509   21.0195 -1.13946  0.2545


````



````julia
julia> mm1.optsum
Initial parameter vector: [1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0  …  1.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 1.0, 0.0, 1.0]
Initial objective value:  30014.369768606295

Optimizer (from NLopt):   LN_BOBYQA
Lower bounds:             [0.0, -Inf, -Inf, -Inf, -Inf, -Inf, -Inf, -Inf, 0.0, -Inf  …  0.0, -Inf, -Inf, -Inf, 0.0, -Inf, -Inf, 0.0, -Inf, 0.0]
ftol_rel:                 1.0e-12
ftol_abs:                 1.0e-8
xtol_rel:                 0.0
xtol_abs:                 [1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10  …  1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10]
initial_step:             [0.75, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.75, 1.0  …  0.75, 1.0, 1.0, 1.0, 0.75, 1.0, 1.0, 0.75, 1.0, 0.75]
maxfeval:                 -1

Function evaluations:     2093
Final parameter vector:   [0.476645, -0.0494739, -0.0557462, 0.0295519, 0.0292019, 0.031941, -0.013964, -0.0464088, 0.102591, -0.0171748  …  0.0243508, -0.0518579, -0.0435887, 0.0582836, 0.00661947, -0.00588158, -0.00248396, 0.0, -1.69798e-5, 0.0]
Final objective value:    28586.319170623105
Return code:              FTOL_REACHED


````



````julia
julia> mm1.trms[1].Λ
8×8 LinearAlgebra.LowerTriangular{Float64,Array{Float64,2}}:
  0.476645     ⋅           ⋅          …    ⋅            ⋅           ⋅ 
 -0.0494739   0.102591     ⋅               ⋅            ⋅           ⋅ 
 -0.0557462  -0.0171748   0.102346         ⋅            ⋅           ⋅ 
  0.0295519  -0.0165941   0.078039         ⋅            ⋅           ⋅ 
  0.0292019  -0.110815   -0.0944958        ⋅            ⋅           ⋅ 
  0.031941   -0.0243539   0.00504511  …   0.0           ⋅           ⋅ 
 -0.013964    0.0125809  -0.0133861       0.000105014  0.0          ⋅ 
 -0.0464088   0.021845   -0.0650391      -4.02353e-5   6.50416e-5  0.0

````



````julia
julia> mm1.trms[2].Λ
8×8 LinearAlgebra.LowerTriangular{Float64,Array{Float64,2}}:
  0.570443      ⋅            ⋅          …    ⋅            ⋅           ⋅ 
 -0.023398     0.0640016     ⋅               ⋅            ⋅           ⋅ 
 -0.267225    -0.287873     0.0442821        ⋅            ⋅           ⋅ 
  0.017337     0.00426167  -0.006672         ⋅            ⋅           ⋅ 
  0.0290524   -0.0304972   -0.0146136        ⋅            ⋅           ⋅ 
  0.0178871    0.00395068  -0.0207794   …   0.00661947    ⋅           ⋅ 
  0.00853432  -0.0241396    0.0954565      -0.00588158   0.0          ⋅ 
  0.00489295  -0.0537545   -0.00619495     -0.00248396  -1.69798e-5  0.0

````


