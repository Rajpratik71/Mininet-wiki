## Plan
2 Week iterations
### Iterations
1. First iteration, starting week of June 17, Extend API tests
2. CLI testing, starting week of July 1
3. ...

## Iteration 1
I want to start with testing the classes and functions of the Mininet API.
Extending what already exist in the https://github.com/mininet/mininet/tree/master/mininet/test directory with more tests testing the operations on nodes and arguments that the Mininet constructor takes.

A test's workflow would look like this. 
* Set up topology
* Validate connectivity
* Make API call, (change something)
* Validate expected connectivity

For most cases a simple topology will do but enabling the test to be done on any topo might be useful (eg. when using large topologies when testing scale)



