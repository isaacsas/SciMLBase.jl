# The SciML Common Interface for Julia Equation Solvers

The SciML common interface ties together the numerical solvers of the Julia
package ecosystem into a single unified interface. It is designed for maximal
efficiency and parallelism, while incorporating essential features for large-scale
scientific machine learning such as differentiability, composability, and sparsity.

This documentation is made to pool together the docs of the various SciML libraries
to paint the overarching picture, establish development norms, and document the
shared/common functionality.

## Domains of SciML

The SciML common interface covers the following domains:

- Linear systems (`LinearProblem`)
  - Direct methods for dense and sparse
  - Iterative solvers with preconditioning
- Nonlinear Systems (`NonlinearProblem`)
  - Systems of nonlinear equations
  - Scalar bracketing systems
- Differential Equations
  - Discrete equations (function maps, discrete stochastic (Gillespie/Markov)
    simulations) (`DiscreteProblem`)
  - Ordinary differential equations (ODEs) (`ODEProblem`)
  - Split and Partitioned ODEs (Symplectic integrators, IMEX Methods) (`SplitODEProblem`)
  - Stochastic ordinary differential equations (SODEs or SDEs) (`SDEProblem`)
  - Stochastic differential-algebraic equations (SDAEs) (`SDEProblem` with mass matrices)
  - Random differential equations (RODEs or RDEs) (`RODEProblem`)
  - Differential algebraic equations (DAEs) (`DAEProblem` and `ODEProblem` with mass matrices)
  - Delay differential equations (DDEs) (`DDEProblem`)
  - Neutral, retarded, and algebraic delay differential equations (NDDEs, RDDEs, and DDAEs)
  - Stochastic delay differential equations (SDDEs) (`SDDEProblem`)
  - Experimental support for stochastic neutral, retarded, and algebraic delay differential equations (SNDDEs, SRDDEs, and SDDAEs)
  - Mixed discrete and continuous equations (Hybrid Equations, Jump Diffusions) (`DEProblem`s with callbacks)
- Optimization (`OptimizationProblem`)
  - Nonlinear (constrained) optimization
- (Stochastic/Delay/Differential-Algebraic) Partial Differential Equations (`PDESystem`)
  - Finite difference and finite volume methods
  - Interfaces to finite element methods
  - Physics-Informed Neural Networks (PINNs)
  - Integro-Differential Equations
  - Fractional Differential Equations

The SciML common interface also includes
[ModelingToolkit.jl](https://github.com/SciML/ModelingToolkit.jl)
for defining such systems symbolically, allowing for optimizations like automated
generation of parallel code, symbolic simplification, and generation of sparsity
patterns.

## Common Interface High Level

The SciML interface is common as the usage of arguments is standardized across
all of the problem domains. Underlying high level ideas include:

- All domains use the same interface of defining a `SciMLProblem` which is then
  solved via `solve(prob,alg;kwargs)`, where `alg` is a `SciMLAlgorithm`. The
  keyword argument namings are standardized across the organization.
- `SciMLProblem`s are generally defined by a `SciMLFunction` which can define
  extra details about a model function, such as its analytical Jacobian, its
  sparsity patterns and so on.
- There is an organization-wide method for defining linear and nonlinear solvers
  used within other solvers, giving maximum control of performance to the user.
- Types used within the packages are defined by the input types. For example,
  packages attempt to internally use the type of the initial condition as the
  type for the state within differential equation solvers.
- `solve` calls should be thread-safe and parallel-safe.
- `init(prob,alg;kwargs)` returns an iterator which allows for directly iterating
  over the solution process
- High performance is key. Any performance that is not at the top level is considered
  a bug and should be reported as such.
- All functions have an in-place and out-of-place form, where the in-place form
  is made to utilize mutation for high performance on large-scale problems and
  the out-of-place form is for compatibility with tooling like static arrays and
  some reverse-mode automatic differentiation systems.

## User-Facing Solver Libraries

- [DifferentialEquations.jl](https://diffeq.sciml.ai/stable/)
    - Multi-package interface of high performance numerical solvers of
      differential equations
- [ModelingToolkit.jl](https://mtk.sciml.ai/stable/)
    - The symbolic modeling package which implements the SciML symbolic common
      interface.
- [NonlinearSolve.jl](https://github.com/JuliaComputing/NonlinearSolve.jl)
    - High performance numerical solving of nonlinear systems.
- [GalacticOptim.jl](https://github.com/SciML/GalacticOptim.jl)
    - Multi-package interface for numerical solving of optimization problems.
- [NeuralPDE.jl](https://github.com/SciML/NeuralPDE.jl)
    - Physics-Informed Neural Network (PINN) package for transforming partial
      differential equations into optimization problems.
- [DiffEqOperators.jl](https://github.com/SciML/DiffEqOperators.jl)
    - Automated finite difference method (FDM) package for transforming partial
      differential equations into nonlinear problems and ordinary differential
      equations.
- [DiffEqFlux.jl](https://github.com/SciML/DiffEqFlux.jl)
    - High level package for scientific machine learning applications, such as
      neural and universal differential equations, solving of inverse problems,
      parameter estimation, nonlinear optimal control, and more.

## Interface Implementation Libraries

- [SciMLBase.jl](https://github.com/SciML/SciMLBase.jl)
    - The core package defining the interface which is consumed by the modeling
      and solver packages.
- [DiffEqSensitivity.jl](https://github.com/SciML/DiffEqSensitivity.jl)
    - A package which pools together the definition of derivative overloads to
      define the common `sensealg` automatic differentiation interface.
- [DiffEqNoiseProcess.jl](https://github.com/SciML/DiffEqNoiseProcess.jl)
    - A package which defines the stochastic `AbstractNoiseProcess` interface
      for the SciML ecosystem.
- [RecursiveArrayTools.jl](https://github.com/SciML/RecursiveArrayTools.jl)
    - A package which defines the underlying `AbstractVectorOfArray` structure
      used as the output for all time series results.
- [ArrayInterface.jl](https://github.com/JuliaArrays/ArrayInterface.jl)
    - The package which defines the extended `AbstractArray` interface employed
      throughout the SciML ecosystem.

## Using-Facing Modeling Libraries

There are too many to name here and this will be populated when there is time!

## Solver Libraries

There are too many to name here. Check out the
[SciML Organization Github Page](https://github.com/SciML) for details.

## Contributing

- Please refer to the
  [SciML ColPrac: Contributor's Guide on Collaborative Practices for Community Packages](https://github.com/SciML/ColPrac/blob/master/README.md)
  for guidance on PRs, issues, and other matters relating to contributing to SciML.
- There are a few community forums:
    - The #diffeq-bridged and #sciml-bridged channels in the
      [Julia Slack](https://julialang.org/slack/)
    - [JuliaDiffEq](https://gitter.im/JuliaDiffEq/Lobby) on Gitter
    - On the Julia Discourse forums (look for the [modelingtoolkit tag](https://discourse.julialang.org/tag/modelingtoolkit)
    - See also [SciML Community page](https://sciml.ai/community/)
