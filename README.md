# The Modified Glucose SAT Solver: finding all solutions

Source code for the Glucose SAT Solver came from author website: https://www.labri.fr/perso/lsimon/research/glucose/

_IMPORTANT_: All credit for Glucose SAT Solver goes to it's authors. This repository is intended to present ONLY modification of Glucose SAT Solver that allows it to iterate all feasible solutions of the problem.

Please use this code ONLY after consulting the Glucose SAT Solver website for citation requirements. 

## Purpose of the modification

Boolean falsifiability problem is a kind of optimization problem without objective function. In other words: if there is multiple feasible solutions, then all are equally good. 

Glucose SAT Solver (like all other solvers I tested) checks for the feasibility of the model and allows displaying feasible solution. Glucose SAT Solver displays only one solution, even if there is many solutions. 

I found myself in situation where I needed to know all feasible solutions. 

### Constraint generation approach:

Listing multiple feasible solutions can be achieved by using the constraint generation approach:
1. Solve original model 
2. Remember the solution
3. Negate the solution and add to the model as constraint
4. Solve the model with additional constrain(s)
5. Repeat 2. 3. and 4. until model becomes infeasible

This method requires using solver as many times as there are feasible solutions (actually one time more if your are really precise). 

### Modified Glucose SAT Solver

Modified Glucose SAT Solver available in this repository goes through the constraint generation process internally in the solver. This allows for great performance benefit. 

## Usage

Let's consider sample model with 4 variables (`model.cnf` in DIMACS format):
```
p cnf 4 5
1 2 -3 4 0
-2 3 0
1 2 0
-1 -2 -3 0
-1 -4 0
```

Running Modified Glucose SAT Solver: `./glucose model.cnf` will result in output with number of feasible solutions:
```
Found 4 solutions
```

Running solver with `-model` flag will display all solutions (`./glucose -model model.cnf`):
```
v -1 2 3 -4 0
v 1 -2 3 -4 0
v 1 -2 -3 -4 0
v -1 2 3 4 0
Found 4 solutions
```

### Concept of `Important Variables`

In some cases we might be interested only in iterating all feasible subsets of all variables. Let's say we are only interested in feasible combinations of variables `1` and `2`. We need to add that information to our DIMACS file (`model.cnf`):
```
p cnf 4 5
i 1 2 0
1 2 -3 4 0
-2 3 0
1 2 0
-1 -2 -3 0
-1 -4 0
```

Running solver with `-model` flag will display all solutions for variables `1` and `2` (`./glucose -model model.cnf`):
```
v -1 2 0
v 1 -2 0
Found 2 solutions
```

## Important notes

This modification was an afternoon experiment. The testing was limited. Only `-model` flag was tested - changed code might not work correctly with other flags. 

Use with caution (like avoid using for important stuff / production environment)!