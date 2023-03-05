# ant-colony-model
Simulation of an ant colony where pheromones are used for various colony actions such as trail creation and other signaling purposes.

1) Goals
This notebook is a repository for a model of an ant colony. The goals of this model are to reproduce foraging, reproduction, larvae care, genetic variation of individuals, trait selection on the colony and individual level, pheromones, development and use of caste systems, monogyny/oligogyny/polygyny and the formation of super colonies, and variation among how often individuals commit the "best action" when presented with stimuli.

Additionally, we can use this model to test extreme cases, such as how many "dumb" workers can a colony have while still meeting caloric needs and being able to continue growing population size.

2) Design
The colony is composed of individuals. These individuals will also be the same species and family of course, however they can still vary genetically from one another. Depending on the complexity of the species, individuals may belong to certain castes, with genotypes and phenotypes that vary greater from others not part of their caste. The simplest caste separation is the queen/worker caste system, in which there is one queen and the remaining colony members are workers.

To incorperate flexibility in variations of individuals, the phenotypes of each member will depend on its genotypes. This will allow us to see how differences in alleles can effect an individual's behavior and in effect the colony itself. There is a cyclic affect here where the individual's genotype is shaped by the colony, and the colony is shaped by the collective phenotypes of individuals. Tracking how these two features effect eachother will be an additional focus of this model.

Each ant will have a set of genotypes. There will be a separate caste-identifying function (CIF) that will output what caste an ant belongs to using its set of genotypes as the input. Each caste will have a criteria of genotypes that the CIF will use. The reason for using the CIF instead of hardcoding each ants' caste is too allow for ants to transition from one caste to another, as occurs in the more primitive species of eusocial ants. This may also allow us to see the solidification of established caste-systems when running evolutionary simulations that cycle through many generations as to view long-term adaptions made by a colony.

ant.genome = [genome 1 ; expression a, genome 2 ; expression c,...]

Each ant will also have:

Age
Hunger
Gender
Caste = CIF(ant.genome)
Size = getSize(Caste, Gender, Age)
The environment the ants travel through will be a 3-D space in which we will plant food sources for them to find. In this space, ants will also be able to lay pheromones after finding a food source or when headed toward a known food source. Ants can also follow pheromone trails if the scent is within their detection radius. An exmaple of a detection radius where r=1 is shown below. If a pheromone or food source are within this radius then the ants will be able to detect it and have a high statistical chance of traveling to the detected entity.

Now comes the question, once an ant has arrived at a pheromone, how can they know what direction to travel after encountering the pheromone? The solution to this question involves two features of pheromone trails

The decay rate of the pheromone.
The minimum distance between pheromones.
Using these two features, once an ant arrives at a pheromone they will be able to determine what direction to travel to next. First, we must ensure that once an ant finds a pheromone that it can distinguish how "fresh" the pheromone is. In this model, pheromone will start with a freshness of 1.0, and then decrease by some predetermined decay rate for the pheromone. Secondly, the ant will compare the "freshness" of the pheromone at its current position with the nearest pheromone it can find.

If the ant is following a pheromone trail laid FROM the food source TO the colony, then they would follow the trail by finding a pheromone that is less "fresh" than the pheromone it came from.
If the ant is following a pheromone trail laid FROM the colony TO the food source, then they would follow the trail by finding a pheromone that is more "fresh" than the pheromone it came from.
The ideal situation for the ant would be to always have the closest pheromone (other than the pheromone at its current position) within its detection radius. However, in the case that the nearest pheromone is not within the ant's detection radius, we will have to prepare for the case that the ant may encounter a separate pheromone trail. We would not want the ant to compare pheromones from different trails, so we will build in a decay of how much an ant values a pheromone as well dependent on time passed. In other words, the longer it takes for an ant to find another pheromone, the less the ant will value the pheromone it previously encountered.

2a) Mapping
There will be two maps:

cMap and env.mapping (these are both copies of each other). These maps will hold the locations of pheromones and foods.

aMap. This map will hold the locations of all ants.

ALTERNATIVE: Create a map that holds objects and a dictionary that holds a list of positions for each object type

Barriers
To represent a 2-D barrier, we can use the objects boundaries in its two axis to create limits for the movement of agents through the space. Example: A square with diangal coordinates (2,2) and (5,5) can have its remaining two corners derived, (2,5) and (5,2). We can therefore set the limits of agent movement during its choosing of its next location as below:

NOTE: ensure that ant does not spawn in these locations that will be barriers or surrounded by barriers. Such spawning will cause agents to be locked in boundaries. Also, note food will be unreachable if in a boundary.

if next_pos.x >= start_x or next_pos.y >= start_y or next_pos.x <= end_x or next_pos.y <= end_y: random_walk()

To represent a 3-D barrier, we can use a similar set of equalities to create our barriers.

A 3-D cube will have start_coordinates = (3,3,3) and end_coordinates = (5,5,5) The remaining coordinates can be deduced as (3,5,3),(5,3,3),(3,3,5),(5,5,3),(3,5,5),and (5,3,5).

We can represent this 3-D barrier as

*if x_start <= next_pos.x <= x_end y_start <= next_pos.y <= y_end z_start <= next_pos.z <= z_end: random_walk() *
