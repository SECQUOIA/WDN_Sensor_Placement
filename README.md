# Sensor Placement in Water Distribution Networks (WDN)

This repository consists of notebooks using which, we formulate an optimization problem to identify the optimal placement of pressure sensors in a Water Distribution Network (WDN). The WDN is represented on a constrained graph `G(V,E)`, where `V` is the set of vertices and `E` is the set of edges.

The solution to this graph problem is explored using different reformulations of the problem.

First, we formulate the graph optimization problem as a Mixed Integer Program (MIP) (Speziali et al. (2021)):

```
min_{x} sum_{i in V} c_{i}x_{i} + sum_{(i, j) in E} w_{ij}(1 - x_{j} - x_{i} + x_{i}x_{j})
s.t. sum_{i in V} x_{i} = s
x_{i} in {0,1}^{n}
```

Here, `c_{i}` represents the cost of the `i`-th node, `w_{ij}` represents the weight corresponding to the edge between nodes `i` and `j`, and `x_{i} in {0, 1}^{n}` is a binary decision variable that indicates whether a sensor is placed at the `i`-th node. `s` is the predefined total number of sensors.

Next, the Quadratic Unconstrained Binary Optimization (QUBO) formulation of the graph problem is explored. Consider the MIP model from before:

```
min_{x} sum_{i in V} c_{i}x_{i} + sum_{(i, j) in E} w_{ij}(1 - x_{j} - x_{i} + x_{i}x_{j})
s.t. sum_{i in V} x_{i} = s
x_{i} in {0,1}^{n}
```

To implement the problem as a QUBO, the constraint should be lifted up into the objective function such that the problem becomes unconstrained. This is performed as follows:

```
min_{x} sum_{i in V} c_{i}x_{i} + sum_{(i, j) in E} (w_{ij}(1 - x_{j} - x_{i} + x_{i}x_{j})) + rho(sum_{i in V} x_{i} - s)^2
```

where `rho` is a scalar penalty term. Further simplification leads to

```
min_{x} sum_{i in V} c_{i}x_{i} + sum_{(i, j) in E} w_{ij} - sum_{(i, j) in E} w_{ij}x_{j} - sum_{(i, j) in E} w_{ij}x_{i} + sum_{(i, j) in E} w_{ij}x_{i}x_{j} + rho sum_{i in V} x_{i}^2 - 2rho sum_{i in V} x_{i}s + rho s^2
```

Since `x_{i}` is a binary variable, we can write `x_{i}` as `x_{i}^2`. Taking this into account, the problem now becomes:

```
min_{x} sum_{i in V} c_{i}x_{i}^2 + sum_{(i, j) in E}w_{ij} - sum_{(i, j) in E} w_{ij}x_{j}^2 - sum_{(i, j) in E} w_{ij}x_{i}^2 + sum_{(i, j) in E} w_{ij}x_{i}x_{j} + rho sum_{i in V} x_{i}^2 - 2rho sum_{i in V} x_{i}^2s + rho s^2
```

Performing some algebraic manipulations, we have our QUBO problem:

```
min_{x} ( sum_{i in V} (c_i + rho - 2rho s - w_{ij})x_i^2 + sum_{(i,j) in E} w_{ij}x_i x_j + rho s^2 + sum_{(i,j) in E} w_{ij} )
```

where `s` is the total number of sensors that is predefined.

The optimal sensor placement problem is solved using five different solvers: Gurobi, Tabu Search, Simulated Annealing, Hybrid Quantum-Classical and Quantum Annealing. via **[neal](https://github.com/dwavesystems/dwave-neal)**.

Additionally, we leverage **[Networkx](https://networkx.github.io/)** for network models and graphs.
