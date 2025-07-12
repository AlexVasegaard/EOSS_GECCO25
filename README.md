# Evolutionary Algorithms for the Large-scale Earth Observation Satellite Scheduling Problem

## GECCO25 - Competition description

The discrete version of the agile Earth Observation Satellite Scheduling (EOSS) Problem is an NP-complete combinatorial optimization problem with a binary decision variable representing each imaging possibility of fitting customer requests along a discrete satellite path. A set of customer's requests have to be acquired on Earth, determined by different priority and acquisition constraints. In addition, when it comes to agile very high resolution satellites, the number of requests is way more important than the satellite acquisition capability. The optimization problem itself is equivalent to the maximum weighted clique problem and the generalized knapsack problem. The decision space is constrained by a the complex maneuverability check, which requires the execution of non linear models to be verified, a maximum amount of images per request and per time unit, as well as a strip request constraint allowing for the imaging of customer requests that are larger than the swath of the considered Earth Observation (EO) satellite. In addition, this mission plan has to be computed from the ground and uploaded to the satellite before each pass over a ground station. In order to include last minute requests, it is therefore important that the optimization algorithm used does not require more than a few minutes.

In this competition, we consider a Large-scale problem scenario consisting of a 7 day planning horizon with a time resolution (discretization) of 5 seconds. We have a fleet of high-resolution and highly agile EO satellites consisting of the Pleiades NEO 3, NEO 4 satellites, as well as the Spot 6 satellites. Agility for a satellite means that it can rotate around its 3 axis. Therefore, the mission schedule does not only consist in choosing the area on Earth to image but the exact date and satellite attitude at the moment the satellite acquires it. We consider spot and strip imaging capabilities (that is, single shot and multi-shot images of requests) to be allowed in the customer database consisting of 1000 customer requests spread around the globe. 

As previously said, the satellites agility is modeled by non linear simulators. However, the maneuver feasibility between two image acquisitions can be precomputed and we can therefore transform it into a linear constraint, and consequently we can write the EOSS problem as a linear programming problem in the following form:
```math
\begin{align}
    \text{Max}_{x\in X} F(x) \\
    \text{s.t.} \nonumber\\
    x \in \{0,1\}^D
\end{align}
```

where D is the number of attempts considered. The decision variable is consequently representing all the feasible imaging attempts considered in the time horizon. Before a pre-processing step removes attempts that violate off-nadir angle, sun elevation, and cloud cover forecasts, D is equal to the number of requests times the number of time steps considered in the discretized time horizon.
```math
\begin{align}
    Mx \leq m 
\end{align}
```
This is the maneuverability infeasibility constraint. It comes from the pairwise infeasibility matrix $H$ in which only consecutive images that follow the satellites maneuverability capabilities are allowed. H has dimensions D times D and is binary with $H[i,j]=0$ if attempts $x(i)$ and $x(j)$ are possible in the same tasking schedule, if $H[i,j]=1$ then the pair of attempts are not feasible to execute consecutively due to the acquisition duration of attempt x(i) and maneuverability time between attempts i and j is too much to meet the acquisition time of x(j). Note in H only the infeasible attempts forward in time from attempt i is considered, thus having ones only in the upper triangular elements of H. We convert H to the matrix M by having pairs of attempts that acquired together are infeasible or complete sets attempts that are infeasible. One of these infeasible sets are then represented by ones in a row of the matrix M. That is, to ensure we do not over-constrain the solution space. Consequently, M has dimensions equal to the number of infeasible sets/pairs of attempts times D. Here m equals 1 for each row there is in M, thus ensuring that when acquiring attempts only two attempts that are not infeasible together per M can be acquired. Lastly, the infeasibility is naturally only considered for set of attempt performed by the same satellite.
```math
\begin{align}
    Ax \leq a 
\end{align}
```
Thereby a maximum of a(t) images per time unit. Here, a(t) is equal to one for all t since the satellite cannot image two acquisitions at the same time. The matrix A has dimension T times D, where T is the number of time instances in which attempts can be acquired for each satellite, that is, we have removed all the time instances in which no attempts are considered. A is binary and represents for each time instance and each satellite all the attempts that occur in that time instance and is performed by the same satellite.
```math
\begin{align}
    Bx \leq b 
\end{align}
```
This constraint states that a maximum of b(r) images per request can be acquired. Note, for larger image requests than the swath of the satellite, we assume the acquisitions can be completed by acquiring the same request b(r) amount of times, where that corresponds to the width of the image request divided by the swath capabilities of the satellite. Here the B is a binary matrix with dimensions R times D, where R is the number of requests that are reachable by any other satellites and thus considered in the EOSS.

![Alt text](https://github.com/AlexVasegaard/EOSS_GECCO25/blob/main/html_output2.png)
Figure 1: A graphical representation of a solution for the EOSS problem.

### Phases
The competition consists of two phases:

**In the first phase**, we consider a single objective version of the above EOSS problem. That is the objective function is:
```math
\begin{align}
    F(X) := \text{maximize the number of acquisitions during the planning horizon. }
\end{align}
```
**In the second phase**, we consider a multi-objective version of the EOSS problem. That is, the objective function set $F(X) := \{f_1, f_2, f_3\}$, where:
```math
\begin{align}
 f_1 :&= \text{maximize number of acquisitions,}\\
 f_2 :&= \text{maximize number of prioritized (emergency) acquisitions, and}\\
 f_3 :&= \text{minimize the average cloud coverage.}
\end{align}
```
Thus we seek to get the solution set, that best represents the Pareto front of the stated problem. We therefore evaluate based on number of non-dominated solutions, the hyper volume of the solution set, and the average diversity between neighboring solutions.

Since both phases are evaluated in a single-shot manner, we will evaluate the evolutionary algorithm that performs the best after a run time of 100 seconds. In the second phase only a population of 100 solutions should be returned. The algorithms are run on the same computer with specifications of that of a regular laptop. We encourage participants to submit their algorithm in Python, but it is not necessary. 

We encourage participants to share their algorithm with the organizers through GitHub (see the judging segment!). 

The large scale and highly constrained nature of the provided problem scenarios should yield participants good means for developing novel operators to efficiently search the solution space and comply with the constraints in an intuitive and evolutionary manner. Especially the infeasibility constraint is expected to challenge participants! 

We look very much forward to seeing your contributions! 

## URL
Find the EOSS problem scenarios at:

https://github.com/AlexVasegaard/EOSS_GECCO25

## Deadline

July 1st 2025

## Prizes
We will provide a certificate to the winner along with Airbus merchandises! 
AND we might up the ante later in the process! 

## Judging
As written in the competition description, we will perform single-shot evaluation of the submitted algorithms.

Participants can submit their algorithms via GitHub by creating private repositories and adding the organizers as collaborators. This process ensures that each submission remains confidential and secure from other participants. By keeping the repositories private, only the organizer will have access to review and evaluate the code, maintaining the integrity and fairness of the competition. Once added as a collaborator, the organizer will download and run the algorithms on a standardized local environment to ensure consistent performance assessment across all submissions. This environment has an i5-4590 CPU with 3.30 GHz processors and 8.0 GB RAM. After the competition, participants can freely remove collaborator access, reinforcing the confidentiality of their solutions. 

Participants should write a small description in their readme.md file for the reviewers and possible for the GECCO25 audience to learn from the participants trials and developments.
In their readme file, participants should select one of four possible dissemination possibilities: 1) everything can be freely shared after the competition as long as participants are acknowledged by name and the algorithms are identified as the intellectual property of the participant, 2) Only the algorithmic overview can be shared after the competition along with results of the submission while participants should be acknowledged by name, 3) Only the results and the participants name can be disseminated post competition, and lastly option 4) Only the results can be shared along with an alias. Note, if option 4) is selected please indicate an alias. If options 1), 2), or 3) are selected the names of the participants will be shared. 

## Encouraging Newcomers
The space industry is currently in a very exciting phase with many new agents in the game, and new specializations and products appearing almost weekly. The optimization community and especially the evolutionary optimization environment holds many minds that are inspired and eager to contribute. But this is not only a possibility for the optimization community to contribute to the space domain and vice versa, it also serves as an interesting problem domain for newcomers in both worlds to be inspired by. 

Outside of marketing and disseminating the competition through our existing channels in the virtual (LinkedIn, X, ResearchGate) and the physical realm (lecturing, supervision, colleagues), we expect to advertise for the competition through blog posts and a couple of tutorials in either written or video format.

We can also say, that we have a lot of ideas for extensions to the problems that we hope to raise in future competitions, e.g. incorporating the dynamic nature of the problem with a multi-shot evaluation framework, allowing for stochastic consideration, very-large scale problem scenarios (that have to be separated for a compute environment can handle them), many-objective versions of the problem, and the ability to model the time utility for request that are near their expiration. Consequently, lessons learned and algorithms developed can hopefully be applied if not to other problems directly, then for future iterations of this competition.

## Dissemination
We want to share as many algorithms as the participants see fit. We naturally want to respect participants' intellectual property while promoting their results and findings to the best of our ability to share knowledge. Submissions will remain private during the competition, and participants will retain full rights to their algorithms. After the competition, participants may choose to publicly share their work via a compilation of selected solutions, for which open-source licensing is encouraged but optional. Aggregate, anonymized results may also be published to highlight overall competition outcomes and insights. This approach provides participants with the choice to protect or share their work according to their preferences.

One of the organizers are currently setting up an EOSS python tool with which some of the solution approaches can be added. We will in the future suggest a set of benchmark problems with which selected competition participants are proposed to contribute to. 

We will make sure to create tutorial on how one can submit their contribution via GitHub!

## Other Info
### Problem scenario specific
The problem scenarios that are provided will be in the form of the above optimization problem with matrices and vectors of the stated names. Along with the matrices will follow a simple python script for evaluating whether a solution follows the constraints and to identify the objective value(s) for a provided solution x as a numpy array. We also attach a so-called performance matrix for the participants to better understand the problem. The performance matrix consists of rows with all the imaging attempts along with descriptive criteria e.g. location, satellite, cloud cover forecast, sun elevation, etc.

The problem scenario has the following size:
- Dimension of the decision variable (number of attempts): 5016
- Dimension of the F matrix (number of infeasible pairs/sets of attempts): 17306


### Problem formulation specific
For simplicity we do not consider the stochastic element of e.g. cloud coverage and uncertainty of acquisitions propagating from being scheduled far out in the future. Consequently, when dealing with minimizing the average cloud cover, we are interested in the solutions that minimizes the forecasted cloud cover and not the expected cloud cover. 

We also do not provide a reference vector for steering the direction of the multi-objective search, as we assume to be equally interested in all the objective trade-offs.

There is some underlying thresholds regarding sun elevation, off-nadir angle, and forecasted cloud cover, that we completely omit explaining. These are all taken care of in a pre-processing step that generated the problem scenario and can be investigated if interested in the papers by A. Vasegaard et al \cite{vasegaard2020multi, vasegaard2024three}

The problem of consideration is a simplified version of the full EOSS problem, as we neglect certain operational constraints regarding energy consumption, memory capacity, and downlink windows, as well as omit the downlink scheduling part that state which acquisitions are to be downloaded at which specific points in time given the location of downlink stations. 

### Solution approach specific
In the python script, we also append a couple of solution results serving as the starting benchmark solution approaches to beat! The phase 1 results stem from an extended longest path algorithm (ELPA) that has run for more than the 100 seconds (so it might not be possible to beat?), and for the second phase on evolutionary MOO we provide a NSGA-II implementation (with no hyper-parameter tuning or specially designed operators). 

The benchmark solution approaches that we consider for the competition has the following size and (extrema) results:
- Highest number of image acquisitions by the benchmark ELPA approach: 216
- Highest number of image acquisitions by the benchmark NSGA-II approach: 115
- Lowest average cloud cover by the benchmark NSGA-II approach: 12.91
- Lowest average off-nadir angle by the benchmark NSGA-II approach: 15.8


Due to the size of the decision variable and underlying complexity of the constraints is might serve you well to consider how to break it into sub-components and hierarchically combine the identified local-optimal solutions. 
Similarly, identifying operators that efficiently does exactly this is really a novel contribution to the field of large-scale evolutionary MOO.

As a version of the problem can be converted into a longest path problem that accomodates the infeasibility constraint it might be beneficiary to consider/investigate similar representations of the EOSS problem formulation to efficiently repair and generate feasible solutions. 
