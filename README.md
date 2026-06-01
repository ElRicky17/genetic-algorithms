# Genetic Algorithms — Knapsack Problem

A Python notebook that solves the classic **0/1 Knapsack Problem** using a **Genetic Algorithm (GA)**. The project walks through each component of the GA (selection, crossover, mutation), visualizes fitness evolution, and provides a reusable object-oriented implementation.

---

## Table of Contents

- [Overview](#overview)
- [Problem Definition](#problem-definition)
- [Genetic Algorithm Design](#genetic-algorithm-design)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Parameters](#parameters)
- [Results](#results)
- [Requirements](#requirements)

---

## Overview

The Knapsack Problem is a classic combinatorial optimization problem: given `N` items with weights and prices, select a subset that **maximizes total value** without exceeding the knapsack's weight capacity `W`.

This notebook implements a **Genetic Algorithm** to find near-optimal solutions. It includes:
- Step-by-step explanation of GA components
- Visualization of the fitness function over generations
- A reusable `Genetic_Algorithm` base class and a `GA_KnapsackProblem` subclass

---

## Problem Definition

```
Maximize:   Σ (xᵢ × priceᵢ)   for i = 1..N
Subject to: 0 < Σ (xᵢ × weightᵢ) ≤ W
            xᵢ ∈ {0, 1}
```

Each individual (chromosome) is a **binary vector** of length `N`:
- `1` → item is included in the knapsack
- `0` → item is excluded

---

## Genetic Algorithm Design

### Selection — Roulette Wheel
Each individual is assigned a selection probability proportional to its fitness value. A random number selects an individual from the cumulative probability distribution.

### Crossover — Single-Point
Two parents are split at a random cut point. Their tails are swapped to produce two children. Only **valid** children (within weight limit) are kept.

```
Parent 1: [1, 0, 1 | 1, 0, 0]
Parent 2: [0, 1, 0 | 0, 1, 1]
           ──────────────────
Child 1:  [1, 0, 1 | 0, 1, 1]
Child 2:  [0, 1, 0 | 1, 0, 0]
```

### Mutation — Bit Flip
A single random bit in the chromosome is flipped (`0 → 1` or `1 → 0`). Applied with probability `mutation_probability`.

### Fitness Function
```python
fitness(x) = sum(x * prices)   # if valid
fitness(x) = 0.1               # if weight constraint is violated
```

---

## Project Structure

```
genetic-algorithms/
│
├── AlgoritmosGeneticos.ipynb    # Main notebook
└── README.md
```

### Class Hierarchy

```
Genetic_Algorithm (base)
└── GA_KnapsackProblem
```

**`Genetic_Algorithm` — Base Class**

| Method | Description |
|---|---|
| `fitnessFunct(x)` | Objective function (override in subclass) |
| `valid(x)` | Constraint check (override in subclass) |
| `generateFirstPopulation()` | Generates initial valid population |
| `roulette(population)` | Computes selection probabilities |
| `selection(population, prob)` | Selects one individual via roulette |
| `crossover(ind1, ind2)` | Single-point crossover |
| `mutate(ind)` | Single bit-flip mutation |
| `geneticAlgorithm()` | Runs the full GA loop |

---

## Getting Started

### Prerequisites

- Python 3.8+
- Jupyter Notebook or Google Colab

### Installation

```bash
pip install numpy pandas plotly matplotlib
```

### Run the Notebook

```bash
jupyter notebook AlgoritmosGeneticos.ipynb
```

---

## Usage

### Quick Run (default parameters)

The notebook includes interactive widgets for parameter input. Simply run all cells and configure:

- `N` — Number of items
- `W` — Max weight capacity (kg)
- `NP` — Population size
- `generations` — Number of GA iterations
- `mutation_probability` — Probability of mutation
- `crossover_probability` — Probability of crossover

### Extend to a New Problem

```python
from genetic_algorithm import Genetic_Algorithm

class MyProblem(Genetic_Algorithm):
    def fitnessFunct(self, x):
        return ...  # Define your objective

    def valid(self, x):
        return ...  # Define your constraint

problem = MyProblem(sizeIndividual=10, sizePopulation=50, generations=200)
best, best_fitness = problem.geneticAlgorithm()
```

---

## Parameters

| Parameter | Default | Description |
|---|---|---|
| `N` | `10` | Number of items |
| `W` | `15` | Knapsack capacity (kg) |
| `NP` | `30` | Population size |
| `generations` | `300` | Number of generations |
| `mutation_probability` | `0.1` | Probability of mutation per individual |
| `crossover_probability` | `0.9` | Probability of crossover per pair |
| `minprice` | `1` | Minimum item price |
| `maxprice` | `100` | Maximum item price |

---

## Results

After running the GA, the notebook outputs:
- The **best chromosome** (selected items as a binary vector)
- The **maximum achievable value** within the weight limit
- A **Plotly line chart** showing fitness improvement over generations

Example output:
```
Best items selected: ['p0', 'p4', 'p8', 'p9']
Best fitness value:  187.43
```

---

## Requirements

```txt
numpy
pandas
plotly
matplotlib
```

---

## Concepts

- **Genetic Algorithm:** A metaheuristic inspired by natural evolution. Uses selection, crossover, and mutation to evolve a population of candidate solutions over generations.
- **Chromosome:** Binary vector encoding a candidate solution.
- **Fitness Function:** Measures how good a solution is — the higher the total price within weight limit, the better.
- **Roulette Wheel Selection:** Probabilistic selection where fitter individuals have a higher chance of being chosen.
- **Elitism (implicit):** The best individual from each generation is tracked and the overall best is returned at the end.
