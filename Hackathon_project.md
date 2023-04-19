# Introduction

The coexistence of multiple health conditions in an individual is a common scenario in todays society. Due to interactions between diseases, the burden for treating and managing multiple coexisting chronic diseases is often greater than that of the sum of individual diseases. In 2018, 5.4 million Dutch citizens had multimorbidity (defined as 2 or more chronic conditions), with the prevelance of this increasing as people age. Unfortunately, the quality of care for people with multimorbidity is limited by our poor understanding of the interactions between diseases over time. 

Recently in psychopathology [Disease Networks](https://onlinelibrary.wiley.com/doi/epdf/10.1002/wps.20375) have been proposed as a way to conceptualizing mental disorders and better understand interactions between symptoms. Today we are going to adapt this idea to patients with multimorbidity. We wish go go beyond the traditional approach of explaining a patients symptoms by linking them to a single disease. Instead we want to highlight the complexities of diseases and symptoms interacting, so clinicians can be better informed when presented with highly complex patients. 

# Model Setup

First, think of and create a simple toy model of a network of two types of dynamical variables: symptoms and diseases. A symptom could be modeled as a binary variable or as a continuous number between [0,1]; a disease can be a binary or discrete variable, or if you wish and like a challenge, [also] a continuous variable. This choice will also depend on the choice of modelling (below) and is up to you.

Now, let us assume that symptoms can influence other symptoms, symptoms can influence diseases, but diseases cannot influence other diseases directly. A suggestion is to also not let diseases influence symptoms, but feel free to explore such an option if time is left. 

A particular (and particularly naive...) model of 'human biology' can be simulated as follows. Create N symptom variables and M disease variables. Generate a directed network between these variables that reflects the causal influences, subject to the constraints of the previous bullet point. You may generate a random network (for instance, erdos-renyi) or use a fixed network drawn by yourself, or downloaded from a public source. 

# Adding Dynamics

Now it is time to add some extent of dynamics to the variables of this network (the network structure itself remains fixed). From this dynamical system we would then like to generate surrogate data which we can analyze later. You are free to choose your favorite method for doing this. Below some suggestions, but feel free to be creative and not limit yourself to these suggestions:

* A simple way, but with limited 'dynamics', would be to define a Bayesian network (vanilla version, so without time taken into account). This network will essentially define a joint probability distribution of all variables in the system. This means that you can use this model to generate samples (one value for each of the variables) in a cross-sectional sense, but there is no temporal dynamics, as for instance in the further suggestions below. This could be seen as measuring each patient in a cohort once. Nevertheless we may draw interesting conclusions from this way of modelling already. Moreover, a lot of medical datasets are (approximately) cross-sectional, so it matches the type of data we typically have in research. This approach (as some others) will require that you define a marginal probability distribution for the (initial) states of symptoms that have no causal influences (no incoming edges). Feel free to make a choice here, such as a constant value, a uniform distribution, a normal distribution (truncated), etc. It will be easiest if these 'root cause' variables are drawn independently (or even i.i.d.).
  * A possible extension of the BN with temporal dynamics is referred to as a Dynamical BN or DBN. Here, variables have a time 't' index. Samples could then be taken of one patient over time, or of multiple patients over time
    
    
* 	Another way would be to use a (kinetic) Ising spin model on a network. See for inspiration for instance [Borsboom, Denny. "A network theory of mental disorders." World psychiatry 16.1 (2017): 5-13] and later citing articles such as the more concrete https://psyarxiv.com/sqhje/ preprint or the paper at https://www.tandfonline.com/doi/full/10.1080/00273171.2017.1379379 . A more technical but more specific description of a system dynamics over time can be found in https://royalsocietypublishing.org/doi/full/10.1098/rsif.2013.0568 (Eq. 2.1). In short, each variable is binary. The probability of the next state of a node is governed by for instance the Glauber dynamics. This can be done one randomly selected node per time step, or all nodes simultaneously (using a shadow copy of the system state, i.e., synchronous updating rule). Only (states of neighbouring nodes from) incoming edges are taken into account in the probability of the next states of a node. 

* 	Yet another way could be to define a system of ordinary differential equations. A relatively easy way would be to use equations inspired by Lotka-Volterra (predator-prey models), with one important difference: there is no conservation of mass/energy (so if a 'predator' variable increases because of a high 'prey' population then it does not imply that the 'prey' variable must decrease simultaneously). What is useful about these type of equations is the simple functional forms as well as the use of the logistic function to keep variables bounded (here, [0,1]).

**General tip:** if you interpret each symptom variable as low=good, high=bad (e.g., hypertension, vascular damage, etc.) then all interaction parameters (depending on the model) for the diseases can be constrained to be positive (i.e., more symptom leads to more likely/severe disease outcome) for added simplicity. 

**Another tip:** to generate multiple samples you will need a model with specific parameters. For instance, for the Ising model you will need a matrix (usually called J) of interaction coefficients; for the Lotka-Volterra model you will need the coefficients of the terms in the equations (typically written as greek lower-case letters). These parameters will define a hypothetical description of biological health, namely how symptoms in general influence each other and how diseases are defined in terms of symptoms. Let us assume for this assignment that there is only one such model that describes a population of persons, i.e., we do not consider biological variation among humans (i.e., every person could be described by a different set of variable values but is governed by the same set of parameter values). The question is of course, how to find these parameters? You may be creative here. You could either generate a random set of parameters (within certain bounds) before generating samples. Another option is to use the same parameter value everywhere, so every symptom has equal influence. Another, more advanced, option may be to optimize the set of parameters for some fitness function, for example to find a parameter set for which 50% of the samples have at least one disease (so that there is sufficient variation for computing correlation measures).


# Experiments

With this model you are asked to perform at least one simple analysis in the spirit of the description given in the introduction. We invite you to try to be creative. Below are some suggestions to help you on your way but feel free to think of other options. Keep in mind, though, the feasibility given the time constraint.

* A concrete and relatively feasible analysis could be the following. Consider a single disease (M=1) model. Suppose that the disease is influenced by K symptoms. How does the pairwise correlation between one symptom and the disease variable behave as function of K? This could be averaged over multiple realizations (sampled patients, or multiple measurements for an individual patient, or a combination).

* Consider M=2. Let each disease have K causing symptoms, but not necessarily the same set of symptoms. Let L<=K be the number of symptoms that both diseases share. How does the correlation between the two diseases behave as function of L?

* Another possibility is to define a 'multimorbidity score' R, equal to the number of disease variables (or sum of disease values). It will be a number between 0 and M. One could also define a 'symptom score' S, which is similarly defined for the symptoms. Then we could investigate:
  * How does R scale with M? 
  * How does the correlation between a single (randomly chosen) symptom and R behave, as function of M and N? 
  * And the correlation between a single disease and R? 
  * And the correlation between R and S?

**General note:** for computing a ‘correlation’ value you are free to pick a suitable measure, depending on the variable type in your model. Example correlation measures include Pearson, Kendall, Cramer’s V, etc., as long as you ascertain the appropriateness of the measure in your case. A slightly more advanced option is the use of Shannon’s mutual information (see e.g. the package https://github.com/gregversteeg/NPEET ).

# Outcomes
The important thing about this assignment is to show off your computational modelling skills, your creativity, your technical analysis skills, and your ability to convey what you did in an accessible way to non-technical listeners. It is important to us that you feel free to be creative rather than following our description to the letter, but keep in mind the skills that we would like to assess as well as the general spirit of the project. It is not relevant to us whether your analyses actually come up with reasonable results -- it is perfectly imaginable that given the time constraints, your model does not yet make much sense and that therefore some of your graphs will have unexpected trends, wildly varying trends across models, or no trend at all. It is, however, important that you can explain your results and the observed (absence of) trends given how you approached the problem.

Any programming language can be used. After the presentation please upload both your slides and source code to Canvas.

Good luck!
