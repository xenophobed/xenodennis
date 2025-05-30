---
layout: post
title: "Potential Outcomes Causal Inference Framework"
date: 2025-05-30
categories: DataScience
tags: [Causal Inference]
cover_image: https://images.unsplash.com/photo-1741516600623-20799f115964?q=80&w=3871&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
cover_caption: "Causal Inference Potentail Outcomes"
lang: en
---

### Potential Outcomes Notation 

- **Z** : represents the treatment conditions ( *it could be categorical or continuous variable* )

- **Y** : represents the observed value of the outcome variable  

In the potential outcomes framework, we consider how the Y values we observe would change if the treatment were different 

- When $Z$ is binary, the two potential outcomes are represented with $Y_0$ and $Y_1$

<div class="image-container small">
  <img src="{{ site.baseurl }}/assets/posts_assets/2025-05-30/Theoretical_ITE.png" alt="Individual Treatment Effect (ITE)">
  <span class="image-caption">Figure 1: Individual Treatment Effect (ITE) represents the difference between potential outcomes Y₁ and Y₀ for each individual</span>
</div>

### A Missing Data Problem 

If we knew both potential observations for every individual, we could use them to estimate several different statistics that summarize the effect of the treatment: 

<p> The <span style="color: blue; font-weight: bold;">individual treatment effect(ITE)</span> is computed as Y₀ - Y₁. This statistic directly compares the two potential outcomes for each individual</p>

Hold up! You may be wondering, "How are we supposed to calculate the individual treatment effect if we can never observe the counterfactual outcome?"

<div class="image-container small">
  <img src="{{ site.baseurl }}/assets/posts_assets/2025-05-30/Reality_ITE.png" alt="Reality of Individual Treatment Effect (ITE)">
  <span class="image-caption">Figure 2: In reality, we can only observe one potential outcome for each individual - either Y₁ or Y₀, but never both</span>
</div>

Indeed, causal inference is essentially a missing data problem; since we can only observe the outcome that actually happened, we are always missing the counterfactual outcome. 

### Estimating the ATE 

Because we can never know both potential outcomes for an individual, we need to use the randomization method to estimate the causal effects. 

<span style="color: blue; font-weight: bold;">Randomization</span> is a method of treatment group assignment that is essentially a coin flip to determine whether an individual receives the treatment or the control. This ensures that, for a large enough sample size, the treatment groups will be similar on average with respect to all factors EXCEPT for the treatment condition.

<div class="image-container medium">
  <img src="{{ site.baseurl }}/assets/posts_assets/2025-05-30/Estimated_ATE.png" alt="Estimated Average Treatment Effect (ATE)">
  <span class="image-caption">Figure 3: Estimated Average Treatment Effect (ATE) represents the difference between the average outcomes of treated and control groups</span>
</div>

### Confounders and Selection Bias

A **confounder** is a variable that influences both the treatment and the outcome, creating a spurious association between them. Think of it as a "third variable" that muddles the true causal relationship.

**Selection bias** occurs when the way participants are selected or assigned to groups creates systematic differences between groups that affect the outcome.

#### Real-Life Example: Coffee and Heart Disease

Let's illustrate both concepts with a study examining the relationship between coffee consumption and heart disease:

**Confounder Example:**
- Treatment: Coffee consumption (high vs. low)
- Outcome: Heart disease
- Confounder: Smoking

In this case, smoking is a confounder because:
1. Smokers tend to drink more coffee
2. Smoking independently increases heart disease risk
3. Without accounting for smoking, we might falsely conclude that coffee causes heart disease

**Selection Bias Example:**
If we only study coffee drinkers who visit coffee shops in the morning, we might miss:
- People who drink coffee at work
- People who brew coffee at home
- People who don't drink coffee at all

This selection bias could lead to incorrect conclusions about the relationship between coffee and heart disease.

## Estimating the ATE with counfounders 

Suppose that instead of randomizing patients to receive therapy animal services, we allow the twelve hospital patients to CHOOSE whether or not they want therapy. Imagine that we also have data for a new confounding variable X that represents whether an individual does (X = 1) or does not (X = 0) have a diagnosis of anxiety disorder. Here, X is a confounder and impacts both the treatment and the outcome: patients who have anxiety might be more likely to choose to receive therapy animal services AND have higher cortisol levels generally.

<div class="image-container medium">
  <img src="{{ site.baseurl }}/assets/posts_assets/2025-05-30/Nonrandomized_treatment_ignoring_X.png" alt="Nonrandomized Treatment Ignoring X">
  <span class="image-caption">Figure 4: Nonrandomized treatment assignment ignoring the confounding variable X (anxiety diagnosis)</span>
</div>

This confounding is problematic because it means there may be more people with anxiety in the treatment group than in the control group. More people with anxiety means the treatment group may have a higher average cortisol level compared to that of the control group before therapy animal services even occur!

<div class="image-container small">
  <img src="{{ site.baseurl }}/assets/posts_assets/2025-05-30/estimated_ATE_X=1.png" alt="Estimated ATE for X=1">
  <span class="image-caption">Figure 5: Estimated Average Treatment Effect (ATE) for patients with anxiety (X=1)</span>
</div>

When the treatment groups are unbalanced with respect to confounders, the treatment groups are not exchangeable: we would observe different outcomes if the treatment groups swapped treatment conditions.

<div class="image-container small">
  <img src="{{ site.baseurl }}/assets/posts_assets/2025-05-30/estimated_ATE_X=0.png" alt="Estimated ATE for X=0">
  <span class="image-caption">Figure 6: Estimated Average Treatment Effect (ATE) for patients without anxiety (X=0)</span>
</div>

To avoid making poor comparisons between potentially imbalanced treatment groups, we have to be able to assume conditional exchangeablility:

**Conditional exchangeability** means that the treatment groups are exchangeable if we take into account confounding variables.This is also called *ignorability* or *unconfoundedness*.

By taking anxiety diagnosis (variable X) into account, we avoid getting a biased estimate of the cortisol levels produced by those receiving therapy animal services in comparison to those who do not.

## Other Assumptions 

> Stable Unit Treatment Value Assumption (SUTVA)

1. An individual's treatment assignment doesn't impact the outcome of other individuals. Using the example of the hospital patients, this would mean that one individual getting therapy animal services doesn't impact the stress level of other individuals in the hospital.
2. The treatment (or control) is applied exactly the same way to all patients. For example, the patients receiving therapy animal services should receive therapy for the same amount of time and ideally from the same exact animal to ensure consistent treatment.

> overlap

The assumption of overlap means that all subgroups of patients divided by their characteristics have a positive, non-zero probability of getting either treatment assignment. Overlap is also referred to as the common support or positivity assumption.

<div class="image-container medium">
  <img src="{{ site.baseurl }}/assets/posts_assets/2025-05-30/assumptions.png" alt="Assumptions">
  <span class="image-caption">Figure 7: Key assumptions for causal inference</span>
</div>

## Causal Inference Process 

We can think of causal inference as a two-step process.

1. Identification: During this stage we determine which causal estimand we will estimate based both on what we want to know and on what we are able to compute. We must also determine whether we will be able to meet the three assumptions in order to infer that the relationship is causal in nature.
   1. Conditional Exchangeability
   2. SUTVA
   3. Overlap
2. Estimation: Now that we've reasoned what can compute and that this measure will reflect a causal relationship between variables, we must carry out the statistical model to compute the treatment effect.

