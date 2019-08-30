# Rsmallmachinelearning
---
title: "Models"
author: "Max Ashton-Lelliott"
date: "7 February 2019"
output:
  pdf_document: default
  html_document: default
---

```{r setup, include=FALSE}
Sys.setenv(PATH = paste(Sys.getenv("PATH"), "C:\\Users\\Max\\AppData\\Local\\Programs\\MiKTeX 2.9\\miktex\\bin\\x64", sep=.Platform$path.sep))
knitr::opts_chunk$set(echo = TRUE)
```

```{r models, echo=FALSE}
calc <- function(vals){
  acc <- (vals[4]+vals[1])/(vals[1]+vals[2]+vals[3]+vals[4])
  sen <-vals[4]/(vals[4]+vals[2])
  return(c(acc,sen))
}
info <- readRDS('C:\\Users\\Max\\Documents\\R work\\loan_data_ch1.rds')
# Make the necessary replacements in the coarse classification example below 
info$ir_cat <- rep(NA, length(info$int_rate))
info$ir_cat[which(info$int_rate <= 8)] <- "0-8"
info$ir_cat[which(info$int_rate > 8 & info$int_rate <= 11)] <- "8-11"
info$ir_cat[which(info$int_rate > 11 & info$int_rate <= 13.5)] <- "11-13.5"
info$ir_cat[which(info$int_rate > 13.5)] <- "13.5+"
info$ir_cat[which(is.na(info$int_rate))] <- "Missing"
info$ir_cat <- as.factor(info$ir_cat)

set.seed(426)
index_train <- sample(1:nrow(info), 2 / 3 * nrow(info))
training_set <- info[index_train, ]
test_set <- info[-index_train, ]

model <- lm(loan_status~loan_amnt*grade*annual_inc+int_rate+emp_length+home_ownership, data = training_set)
suppressWarnings(log_model_multi <- glm(loan_status~age+ir_cat+grade+loan_amnt+annual_inc, data = training_set, family = "binomial" ))
log_model_logit <- glm(loan_status ~ age + grade + ir_cat + loan_amnt,
                       family = binomial(link = logit), data = training_set)
log_model_probit <- glm(loan_status ~ age + grade + ir_cat + loan_amnt,
                        family = binomial(link = probit), data = training_set)
log_model_cloglog <-  glm(loan_status ~ age + grade + ir_cat + loan_amnt,
                          family = binomial(link = cloglog), data = training_set)

models <- predict(model, newdata = test_set, type = "response")
predictions_all_full <- predict(log_model_multi, newdata = test_set, type = "response")
predictions_logit <- predict(log_model_logit, newdata = test_set, type = "response")
predictions_probit <- predict(log_model_probit, newdata = test_set, type = "response")
predictions_cloglog <- predict(log_model_cloglog, newdata = test_set, type = "response")
specificitie <-c()
sensitivitie <-c()
total <- c()
is <- c()
for (i in seq(0, 0.99, by = 0.01)){
  
  cutoff <- i
  modelp <- ifelse(models > cutoff, 1, 0)
  pred_cutoff <- ifelse(predictions_all_full > cutoff, 1, 0)
  class_pred_logit <- ifelse(predictions_logit > cutoff, 1, 0)
  class_pred_probit <- ifelse(predictions_probit > cutoff, 1, 0) 
  class_pred_cloglog <- ifelse(predictions_cloglog > cutoff, 1, 0)
  
  conf_matrix <- table(test_set$loan_status, modelp)
  conf_matrix2 <- table(test_set$loan_status, pred_cutoff)
  tab_class_logit <- table(test_set$loan_status,class_pred_logit)
  tab_class_probit <- table(test_set$loan_status,class_pred_probit)
  tab_class_cloglog <- table(test_set$loan_status,class_pred_cloglog)
  
  acc1 <- calc(conf_matrix)[1]
  sen1 <- calc(conf_matrix)[2]
  acc2 <- calc(conf_matrix2)[1]
  sen2 <- calc(conf_matrix2)[2]
  acc3 <- calc(tab_class_logit)[1]
  sen3 <- calc(tab_class_logit)[2]
  acc4 <- calc(tab_class_probit)[1]
  sen4 <- calc(tab_class_probit)[2]
  acc5 <- calc(tab_class_cloglog)[1]
  sen5 <- calc(tab_class_cloglog)[2]
  
  specificities <- c(acc1, acc2, acc3, acc4, acc5)
  sensitivities <- c(sen1, sen2, sen3, sen4, sen5)
  totals <- specificities + sensitivities
  specificities[is.na(specificities)] <- 0
  sensitivities[is.na(sensitivities)] <- 0
  totals[is.na(totals)] <- 0
  specificitie <- c(specificitie, specificities)
  sensitivitie <- c(sensitivitie, sensitivities)
  total <- c(total, totals)
  is <- c(is, rep(c(i), times = 5))
}

names <- c("Linear", "Generalised Linear Binomial", "GLB with logit", "GLB with probit", "GLB with cloglog")
modelperf <- data.frame("Model" = rep(names, times = 100), "Cutoff" = is, "Specificity" = round(as.numeric(specificitie), 3), "Sensitivity" = round(as.numeric(sensitivitie), 3), "Totals" = round(as.numeric(total), 3))
```
This is a small project I have undertaken in order to learn more about machine learning and R markdown. The aim is to create a simple model that can predict whether or not someone has recieved a loan based on certain criteria. Then to programmatically analyse the performance of these models and see which one performs best. Then finally to create a markdown document summarising the results. A sample of the data that I am using in this project can be seen below:
```{r info, echo=FALSE}
head(info)
```
Here are some of the results from the model performance table:
```{r perform, echo=FALSE}
modelperf[sample(nrow(modelperf), 10), ]
```
The models with maximum specificity (the proportion of the true negatives correctly identified) are:
```{r results11, echo=FALSE}
print(modelperf[which(max(modelperf$Specificity) == modelperf$Specificity), ], row.names = FALSE)
```
The models with maximum sensitivity (the proportion of true positives that are correctly identified) are:
```{r results2, echo=FALSE}
print(modelperf[which(max(modelperf$Sensitivity) == modelperf$Sensitivity), ], row.names = FALSE)
```
The best overall model is:
```{r results3, echo=FALSE}
print(modelperf[which(max(modelperf$Totals) == modelperf$Totals), ], row.names = FALSE)

```
While the models as not quite as accurate as I had hoped, I do feel that the project has been a success and that I have a greater understanding of machine learning and R markdown.
