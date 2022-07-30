# LME-workflow

This is a step-by-step tutorial of how to analyze psycholinguistic data using the Generalized Linear Mixed Effect Model. It's based on my experience and the inputs I got from various workshops, papars, and web posts. I cannot guranteen this is a one-size-fit-all tutorial for any linguistic study. Having this repo here is mainly to make my analysis method replicable. If it could also help anyone at some point, it's my honor :)

## Step 0 (Most importantly!) Have a valid design with sufficient data

The dataset was adapted from one of the studies I carried out during the first year of my PHD at RUG. The design is as bellow:

| Predictors | Description |
| :---: | --- |
| training days | 1) day 1, 2) day 2, 3) day 3 |
| testing stimuli | 1) trained tonal stimuli, 2) untrained tonal stimuli |
| voice of speaker | 1) trained voice, 2) untrained voice |
| --- | --- |
| **Dependent Variable** | judgement on testing stimuli ('yes'/'no' response to a question of 'Did you hear this testing word in the training phase?') |

To further describe the design, it aimed at training non-tonal language speakers to pick up lexical tones in an artificial language learning setting. There were 3 training days. Participants first went through a training phase where they heard some artificial tonal word. Then they were examined with how well did they learn the lexcial tones in the testing phase. The hypothesis was  as the training went on, participants could show more 'yes' to the trained stimuli but less 'yes' to the counterpart because the trained stimuli only differ from the untrained ones in the lexical tones. 


## Step 1 Organize the dataframe
* select variables of interest from the original data file. 
```
filter, dplyr::select
```
* remove NA values.
```
drop.na
```
* check the descriptive data. Questions to be considered: 
  - does each condition has comparable amount of data? 
  - whether the average means are in line with my hypothesis? 
  - do the data follow normal distribution (if necessary (@mitang01 need to check))?
```
tapply, aggregate, boxplot
```


## Step 2 Choose a coding scheme
The following schemes are ranked by my preference.
* sum coding/ deviation coding.
Suits ANOVA design.
* user defined coding.
* helmert/ reverse helmert coding.
* forward/ backward difference coding.
* contrast coding.
* treatment coding.
```
factor, contrasts, contr.sum, matrix, contr.treatment, solve(t())
```

## Step 3 Try the full Model
* first go with the full model based on my study design and theoretical rationales.
  - choose the predictors (fixed effect structure).
  - decide the random effect structure.
  - decide random slopes.
* feed the dataframe to the model
* inspect the outputs. Questions to be considered:
  - is the model well converged?
  - is there any 0/1 correlation in random effects?
  - whether the model is singular fitted (i.e., the random slope is too small@mitang01 is this right?)?
```
glmer, summary, isSingular
```
* plot the residuals of the model to check if the residuals follow normal distribution. ***Bimodel distribution could mean I either chose a wrong model or missed an important predictor***
```
plot(fitted(), resid()), qqnorm(resid()), qqline(resid()), plot(density(resid())), shapiro.test(resid())
```


<details><summary>when the full model fails to converge</summary>
<p>
  
try the belows:
  
- change an optimizer. 
```
all_fit, glmerControl
```
- increase possible iterations.
- drop unecessary random slopes. But only do this when I'm sure the random slope to be dropped do not vary too much on its random effect.
- drop the random effect of item. But only do this when I'm sure that the intercepts of item do not vary.
```
coef
```

</p>
</details>

## Step 4 Reduce the model
* Try one of the following methods:
  - reduce it as an addition model if the full model is an interaction model (NB, an additional model is the reduced model of an interaction model when the fixed effects are the same).
  - remove less interesting predictors
  - remove certain levels of a predictor
  - drop unecessary random slopes. But only do this when I'm sure the predictor involved in the random slope do not vary too much on its random effect. Plotting the  intercepts can be helpful. 
  - drop the random effect of item. But only do this when I'm sure the intercepts of item do not vary.
* plot the residuals of the model to check if the residuals of the reduced model follow normal distribution.



## Step 5 Decide the simplest and best fitted model
* Repeat step 3 & 4 until I find the best model. Criteria of well fitted models are: (@mitang01 insert some refs here, maybe Barr D.J.?)
  - with normal distribued residuals
  - small ACI and df
  - no warning of convergence issues
  - no under/over fitting issues (i.e., no 0/1 correlation between any two random effects or fixed effects)
* spot influential data points (Nieuwenhuis, te Grotenhuis, & Pelzer, 2012; Winter, B., 2013)
```
influence.ME, numeric + for loop
```

<details><summary>(if necessary) interactions, main effects, and multiple comparisons</summary>
<p>

## Get the statistics of main effects or interactions
* perform the likelihood ratio test between a full model and a reduced model
```
anova
```

## Get the statistics of multiple comparisons within a predictor 
* Use the Tukey method (@mitang01 do I have to always use the Tukey method? Any other method avaliable?)

## Get the statistics of multiple comparisons within an interaction
* Mutate a new interaction variable in the dataframe
* Model the interaction variable
* Tukey comparison
```
interaction, summary(glht())
```

</p>
</details>


## Step 5 Plot the fixed effects
* Make a dataframe for plotting
```
summarise(group_by())
```
* Remove NA value
* Plot
```
pirateplot, ggplot
```

## Step 6 Results and citiations
* Write up the results, including 
  - model
  - fixed effects (specify whether interaction is included)
  - random effects (specify random slopes)
  - residual normal distribution
  - χ2, df, and p-value from likelihood ratio tests
  - (if applicable) optimizer and the maximum number of iterations
* cite references involved
  - R
  - lme4
  - other packages 



## References
Winter, B. (2013). Linear models and linear mixed effects models in R with linguistic applications. arXiv:1308.5499. [http://arxiv.org/pdf/1308.5499.pdf]

Nieuwenhuis, R., te Grotenhuis, M., & Pelzer, B. (2012). Influence.ME: Tools for Detecting Influential Data in Mixed Effects Models. R Journal, 4(2): pp. 38-47.
