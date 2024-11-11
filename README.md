# EOSS_GECCO25

## Evolutionary Algorithms for the Large-scale Earth Observation Satellite Scheduling Problem

The discrete version of the agile Earth Observation Satellite Scheduling (EOSS) Problem is an NP-complete optimization problem with a binary decision variable representing each imaging possibility of fitting customer requests along a discrete satellite path. The decision space is constrained by a the complex maneuverability constraint, a maximum amount of images per request and per time unit, as well as a strip request constraint allowing for the imaging of customer requests that are larger than the swath of the considered EO satellite. 

In this competition, we consider a Large-scale problem scenario consisting of a 7 day planning horizon with a time resolution (discretization) of 5 seconds. We have a fleet of high-resolution and highly agile EO satellites consisting of the Pleiades NEO 3, NEO 4, 1A, and 1B satellites, as well as spot 6. We consider spot and strip imaging capabilites (that is, single shot and multi-shot images of requests) to be allowed in the customer database consisting of 1000 customer requests spread around the globe. 

The satellites agility are assumed to be linear and consequently we can write the EOSS as a linear programming problem in the form of:

$Max F(x)$

s.t. 

$x \in \{0,1\}^D$ (where D is the number of attempts considered. The decision variable is consequently representing all the feasible imaging attempts considered by )

$Fx <= f$ (maneuverability infeasibility constraint - it comes from the pairwise infeasbility matrix $H$. In $H$ only consecutive images that follow the satellites maneuverability capabilities are allowed. H has dimensions D times D and is binary with H[i,j]=0 if attempts x(i) and x(j) are possible in the same tasking schedule, if H[i,j]=1 then the pair of attempts are not feasible to execute consecutively due to the acquisition duration of attempt x(i) and maneuverability time between attempts i and j is too much to meet the acquisition time of x(j). Note in H only the infeasible attempts forward in time from attempt i is considered, thus having ones only in the upper triangular elements of H. We convert H to the matrix F by having only complete sets of infeasible maneuvers in each row of the matrix F. That is, to ensure we do not over-constrain the solution space. Here f equals 1 for each row there is in F, thus ensuring that when acquiring attempts only two attempts that are not infeasible together per F can be acquired. Lastly, the infeasibility is naturally only considered for set of attempt performed by the same satellite!)

$Ax <= a$ (maximum of a(t) image per time unit. Here, a(t) is equal to one for all t. The matrix A has dimension T times D, where T is the number of time instances in which attempts can be acquired for each satellite, that is, we have removed all the time instances in which no attempts are considered. A is binary and represents for each time instance and each satellite all the attempts that occur in that time instance and is performed by the same satellite.)

$Bx <= b$ (maximum of b(r) images per request - note, for larger image requests than the swath of the satellite, we assume the acquisitions can be completed by acquiring the same request b(r) amount of times, where that corresponds to the width of the image reqeust divided by the swath capabilities of the satellite. Here the B is a binary matrix with dimensions R times D, where R is the number of requests that are reachable by any other satellites and thus considered in the EOSS.)

The competition consists of two phases! 

In the first phase, we consider a single objective version of the above EOSS problem, seeking to maximize the number of acquisitions during the planning horizon. 

In the second phase, we consider a multi-objective version of the EOSS problem. That is, the objective function set, consists of:

maximize number of acquisitions,

minimize the average cloud coverage, and

minimize the average depointing angle.

Thus we seek to get the solution set, that best represents the Pareto front of the stated problem. We therefore evaluate based on number of non-dominated solutions, the hyper volume of the solution set, and the average diversity between neighboring solutions.

Since both phases are evaluated in a single-shot manner, we will evaluate the evolutionary algorithm that performs the best after a run time of 100 seconds. In the second phase only a population of 100 solutions should be returned. The algorithms are run on the same computer with specifications of that of a regular laptop. We encourage participants to submit their algorithm in Python, but it is not necessary. 
