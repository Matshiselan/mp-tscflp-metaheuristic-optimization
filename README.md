# mp-tscflp-metaheuristic-optimization
# Hybrid Metaheuristic Optimization for the Multi-Plant Two-Stage Capacitated Facility Location Problem

> A computational Operations Research study of the Multi-Plant Two-Stage Capacitated Facility Location Problem (MP-TSCFLP), combining exact optimization, decomposition methods, and advanced metaheuristics.

---

## 1. Project Overview

The Multi-Plant Two-Stage Capacitated Facility Location Problem (MP-TSCFLP) is a multi-echelon supply-chain optimization problem involving:

- multiple plants,
- multiple intermediate depots/satellites,
- multiple customers,
- multiple products,
- facility-opening decisions,
- plant-to-depot transportation,
- depot-to-customer transportation,
- plant capacity constraints,
- depot capacity constraints, and
- product-specific customer demand.

The objective is to determine:

1. which plants should be opened;
2. which depots should be opened;
3. how products should flow from plants to depots; and
4. how products should flow from depots to customers,

while minimizing total fixed facility and transportation costs.

This repository develops and compares several Operations Research approaches for solving the MP-TSCFLP.

---

# 2. Mathematical Problem

Let:

- \(I\) = set of plants
- \(J\) = set of depots
- \(K\) = set of customers
- \(L\) = set of products

### Decision variables

\[
y_i =
\begin{cases}
1 & \text{if plant } i \text{ is opened}\\
0 & \text{otherwise}
\end{cases}
\]

\[
z_j =
\begin{cases}
1 & \text{if depot } j \text{ is opened}\\
0 & \text{otherwise}
\end{cases}
\]

\[
x_{ij\ell} \geq 0
\]

represents the quantity of product \(\ell\) transported from plant \(i\) to depot \(j\).

\[
w_{jk\ell} \geq 0
\]

represents the quantity of product \(\ell\) transported from depot \(j\) to customer \(k\).

---

## Objective Function

The objective minimizes facility-opening costs and transportation costs:

\[
\min
\left[
\sum_{i\in I} f_i y_i
+
\sum_{j\in J} g_j z_j
+
\sum_{i\in I}\sum_{j\in J}\sum_{\ell\in L}
c_{ij\ell}x_{ij\ell}
+
\sum_{j\in J}\sum_{k\in K}\sum_{\ell\in L}
d_{jk\ell}w_{jk\ell}
\right]
\]

where:

- \(f_i\) = fixed cost of opening plant \(i\);
- \(g_j\) = fixed cost of opening depot \(j\);
- \(c_{ij\ell}\) = transportation cost of product \(\ell\) from plant \(i\) to depot \(j\);
- \(d_{jk\ell}\) = transportation cost of product \(\ell\) from depot \(j\) to customer \(k\).

---

# 3. Constraints

### Customer demand

\[
\sum_{j\in J} w_{jk\ell}
\geq q_{k\ell}
\qquad
\forall k,\ell
\]

### Depot flow conservation

\[
\sum_{i\in I}x_{ij\ell}
\geq
\sum_{k\in K}w_{jk\ell}
\qquad
\forall j,\ell
\]

### Plant capacity

\[
\sum_{j\in J}x_{ij\ell}
\leq
b_{i\ell}y_i
\qquad
\forall i,\ell
\]

### Depot capacity

\[
\sum_{k\in K}w_{jk\ell}
\leq
p_{j\ell}z_j
\qquad
\forall j,\ell
\]

with:

\[
y_i,z_j\in\{0,1\}
\]

and:

\[
x_{ij\ell},w_{jk\ell}\geq0.
\]

---

# 4. Research Objective

The primary objective of this project is to investigate how different optimization paradigms perform on large-scale MP-TSCFLP instances.

The project focuses on four questions:

### Research Question 1

Can exact mathematical programming solve the MP-TSCFLP at realistic problem sizes?

### Research Question 2

Can decomposition methods exploit the structure of the two-stage network?

### Research Question 3

Can BRKGA-MP-IPR efficiently identify high-quality facility configurations?

### Research Question 4

Can a hybrid metaheuristic + exact recourse approach achieve high-quality solutions at substantially lower computational cost?

---

# 5. Optimization Approaches

The project will investigate the following approaches.

## 5.1 Mixed Integer Linear Programming

The complete MP-TSCFLP is formulated as a MILP.

The MILP formulation provides:

- exact solutions where computationally feasible;
- lower bounds;
- upper bounds;
- optimality gaps;
- benchmark solutions.

The MILP formulation serves as the principal exact benchmark.

---

## 5.2 Benders Decomposition

The problem has a natural decomposition structure.

### Master problem

The master problem determines:

\[
y_i,z_j
\]

i.e. which plants and depots are opened.

### Subproblem

Given a fixed facility configuration, the transportation variables:

\[
x_{ij\ell},w_{jk\ell}
\]

are determined by solving a continuous transportation/flow problem.

This produces Benders cuts that are added to the master problem.

The objective is to exploit the separation between:

\[
\text{facility location}
\]

and

\[
\text{transportation recourse}.
\]

---

## 5.3 Lagrangian Relaxation

Selected coupling constraints are relaxed using Lagrange multipliers.

The resulting Lagrangian problem provides:

- lower bounds;
- relaxed facility-location problems;
- subgradient-based optimization;
- insight into the economic value of network constraints.

The Lagrangian bound can also be used to evaluate metaheuristic solution quality.

---

## 5.4 BRKGA

A Random-Key Genetic Algorithm represents facility decisions using continuous random keys.

A chromosome contains:

\[
|I|+|J|
\]

genes.

For the benchmark instance:

\[
100+200=300
\]

random keys are required.

The chromosome is decoded into plant and depot opening decisions.

---

## 5.5 BRKGA-MP-IPR

The project also implements the Multi-Parent Biased Random-Key Genetic Algorithm with Implicit Path Relinking (BRKGA-MP-IPR).

Unlike a conventional two-parent BRKGA, BRKGA-MP-IPR incorporates:

- multiple-parent biased crossover;
- elite population management;
- mutant individuals;
- implicit path relinking;
- population evolution;
- diversification mechanisms.

The implementation is based on the framework developed by the authors of the BRKGA-MP-IPR methodology.

---

# 6. Hybrid BRKGA-MP-IPR + Exact Recourse

A major focus of the project is a hybrid architecture.

The metaheuristic searches over facility configurations:

\[
(y,z)
\]

while an exact LP determines the optimal transportation decisions:

\[
(x,w)
\]

for each promising configuration.

The resulting architecture is:

```text
Random-Key Chromosome
          |
          v
   Facility Decoder
          |
          v
      (y, z)
          |
          v
   Exact Transportation LP
          |
          v
      (x, w)
          |
          v
   True Objective Value
          |
          v
 BRKGA-MP-IPR Evolution
