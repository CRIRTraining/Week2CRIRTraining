---
title: "CCPEBaseline"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
Cleaning HCS data
```{r}
setwd("S:/Indiana Research & Evaluation/Matthew Hanauer/HealthCapitalScale")
HCS = read.csv("CIL_RCS_Variables_6142018.csv", header = TRUE)
library(lavaan)
library(psych)
library(semTools)
library(dplyr)
head(HCS)
HCS = HCS[c("work_environment", "work_job", "fin_resources", "leg_comply", "rel_partner", "rel_family", "rel_friends", "rel_clean_users", "rel_groups_access", "rel_group_member", "rel_sponsor", "rel_online", "rel_sup_others", "liv_symbols", "men_profound", "rel_participate", "form_sup_rec_prog", "form_sup_pcp", "rel_program", "drug_mat", "form_sup_insurance", "med_health", "health_plan", "men_rituals", "prob_skills", "men_self", "men_purpose", "rel_service", "men_hopes", "rel_values", "liv_safe", "liv_nut_meals", "liv_transport", "liv_clothes", "drug_environment", "men_activity", "drug_abstain", "form_sup_bhealth_prog", "men_manage")]
HCS = HCS[c("work_environment", "work_job", "fin_resources", "rel_clean_users", "rel_group_member", "rel_sponsor", "rel_sup_others", "drug_environment", "men_activity", "drug_abstain", "men_manage", "men_hopes")]
head(HCS)
```
Get descriptives
```{r}
describe(HCS)
```


Build model for initial testing.  Get rid of missing values for cronbach alpha

Now get the reliability for the three measures on their own. 
model1 = "Work =~ work_environment+ work_job + fin_resources
          Rel =~ rel_clean_users + rel_group_member + rel_sponsor + rel_sup_others
          Live =~ drug_environment + men_activity + drug_abstain + men_manage + men_hopes"
```{r}

write.csv(HCS, "HCS.csv", row.names = FALSE)
HCS = read.csv("HCS.csv", header = TRUE, na.strings = c("NULL"))
Work = HCS[,1:3]
Rel = HCS[,4:7]
Live = HCS[,8:12]
HCSAlpha = list(Work, Rel, Live)
lapply(HCSAlpha, function(x) alpha(x))
alpha(HCS)
```
Ok try this based on rotated EFA:
Factor 1 = work_environment, work_job, fin_resources
Factor 2 = rel_clean_users, rel_group_member, rel_sponsor, rel_sup_others  
Factor 3 = liv_safe, liv_nut_meals, drug_environment, men_activity, drug_abstain, men_manage
```{r}
model1 = "Work =~ work_environment+ work_job + fin_resources
          Rel =~ rel_clean_users + rel_group_member + rel_sponsor + rel_sup_others
          Live =~ drug_environment + men_activity + drug_abstain + men_manage + men_hopes"

fit1 = cfa(model1, estimator = "MLR", missing = "fiml", std.lv = TRUE, data = HCS)
summary(fit1, fit.measures = TRUE, standardized = TRUE)
```



