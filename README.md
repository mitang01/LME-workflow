# LME-workflow

This is a step-by-step tutorial of how to analyze psycholinguistic data using the Generalized Linear Mixed Effect Model. It's based on my experience and the inputs I got from various workshops, papars, and web posts. I cannot guranteen this is a one-size-fit-all tutorial for any linguistic study. Having this repo here is mainly to make the method of my data analysis replicable. If it could also help anyone at some point, it's my honor :)

## Step 0 (Most importantly!) A valid design with sufficient data

The dataset was adapted from one of the studies I carried out during the first year of my PHD at RUG. The design is as bellow:

| Predictors | Description |
| :---: | --- |
| training days | 1) day 1, 2) day 2, 3) day 3 |
| testing stimuli | 1) trained tonal stimuli, 2) untrained tonal stimuli |
| voice of speaker | 1) female, 2) male |
| --- | --- |
| **Dependent Variable** | judgement on testing stimuli ('yes'/'no' response to a question of 'Did you hear this testing word in the training phase?') |

To further describe the design, it aimed at training non-tonal language speakers to pick up lexical tones in an artificial language learning setting. There were 3 training days. Participants first went through a training phase where they heard some artificial tonal word. Then they were examined with how well did they learn the lexcial tones in the testing phase. The hypothesis was  as the training went on, participants could show more 'yes' to the trained stimuli but less 'yes' to the counterpart because the trained stimuli only differ from the untrained ones in the lexical tones. 


## Step 1 Dataframe organizing
* select variables of interest from the original data file. 
```
filter, select
```
* remove NA values.
```
drop.na
```
* check the descriptive data. Questions to be considered: 
  - does each condition has comparable amount of data? 
  - whether the average means are in line with my hypothesis? 
  - do the data follow normal distribution (if necessary (@mitang01 need to check))?


## Step 2 Choose a coding scheme
The following schemes are ranked by my preference.
* sum coding/ deviation coding.
Suits ANOVA design.
* user defined coding.
* helmert/ reverse helmert coding.
* forward/ backward difference coding.
* contrast coding.
* treatment coding.


## Step 3 Full Model
* first go with the full model based on my study design and theoretical rationales.
  - choose the predictors (fixed effect structure).
  - decide the random effect structure.
  - decide random slopes.
* feed the dataframe to the model
* inspect the outputs. Questions to be considered:
  - is the model well converged?
  - is there any 0/1 correlation in random effects?
  - whether the model is singular fitted (@mitang01 is this right?)?
```
glmer, summary, isSingular
```
* plot the residuals of the model to check if the residuals follow normal distribution. ***Bimodel distribution could mean I either chose a wrong model or missed an important predictor***


<details><summary>when the full model fails to converge</summary>
<p>
  
try the belows:
  
- change an optimizer. 
```
all_fit
```
- increase possible iterations.
- drop unecessary random slopes. But only do this when I'm sure the random slope to be dropped do not vary too much on its random effect.
- drop the random effect of item. But only do this when I'm sure that the intercepts of item do not vary.

</p>
</details>

## Step 4 Reduce the model
* Try one of the following methods:
  - change an interaction model into an addition model.
  - remove less interesting predictors
  - remove certain levels of a predictor
  - drop unecessary random slopes. But only do this when I'm sure the predictor involved in the random slope do not vary too much on its random effect. Plotting the  intercepts can be helpful. 
  - drop the random effect of item. But only do this when I'm sure that the intercepts of item do not vary.
* plot the residuals of the model to check if the residuals of the reduced model follow normal distribution.

## Step 5 Repeat step 3 & 4 and decide the simplest but best fitted model (@mitang01 stopped here on 28/07/2022)

* criterions of well fitted models are
  - small ACI and df
  - no warning of convergence issues
  - no under/over fitting issues (i.e., no 0/1 correlation between any two random effects or fixed effects)
