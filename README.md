# Rsmallmachinelearning
This is a small project I undertook in order to learn more about machine learning and R markdown. The aim was to create a simple model that could predict whether or not someone has recieved a loan based on certain criteria, then to programmatically analyse the performance of these models and see which one performs best. Finally to create a markdown document summarising the results, a small sample of the data that I am using in this project can be seen below:

    ##   loan_status loan_amnt int_rate grade emp_length home_ownership
    ## 1           0      5000    10.65     B         10           RENT
    ## 2           0      2400       NA     C         25           RENT
    ## 3           0     10000    13.49     C         13           RENT
    ## 4           0      5000       NA     A          3           RENT
    ## 5           0      3000       NA     E          9           RENT
    ## 6           0     12000    12.69     B         11            OWN
    ##   annual_inc age  ir_cat
    ## 1      24000  33    8-11
    ## 2      12252  31 Missing
    ## 3      49200  24 11-13.5
    ## 4      36000  39 Missing
    ## 5      48000  24 Missing
    ## 6      75000  28 11-13.5

Here are some of the results from the model performance table:

    ##                           Model Cutoff Specificity Sensitivity Totals
    ## 246                      Linear   0.49       0.894       0.002  0.896
    ## 159             GLB with probit   0.31       0.888       0.010  0.898
    ## 54              GLB with probit   0.10       0.506       0.704  1.210
    ## 33               GLB with logit   0.06       0.315       0.895  1.211
    ## 305            GLB with cloglog   0.60       0.000       0.000  0.000
    ## 407 Generalised Linear Binomial   0.81       0.000       0.000  0.000
    ## 41                       Linear   0.08       0.432       0.802  1.233
    ## 425            GLB with cloglog   0.84       0.000       0.000  0.000
    ## 227 Generalised Linear Binomial   0.45       0.000       0.000  0.000
    ## 17  Generalised Linear Binomial   0.03       0.120       0.995  1.115

The models with maximum specificity (the proportion of the true negatives correctly identified) are:

    ##   Model Cutoff Specificity Sensitivity Totals
    ##  Linear   0.43       0.894       0.003  0.897
    ##  Linear   0.44       0.894       0.003  0.897
    ##  Linear   0.45       0.894       0.003  0.897
    ##  Linear   0.46       0.894       0.003  0.897
    ##  Linear   0.47       0.894       0.003  0.897
    ##  Linear   0.48       0.894       0.003  0.897
    ##  Linear   0.49       0.894       0.002  0.896
    ##  Linear   0.50       0.894       0.001  0.895

The models with maximum sensitivity (the proportion of true positives that are correctly identified) are:

    ##             Model Cutoff Specificity Sensitivity Totals
    ##    GLB with logit   0.03       0.112           1  1.112
    ##  GLB with cloglog   0.03       0.112           1  1.112

The best overall model is:

    ##                        Model Cutoff Specificity Sensitivity Totals
    ##  Generalised Linear Binomial   0.09       0.474        0.77  1.244

While the models as not quite as accurate as I had hoped, I do feel that the project has been a success and that I have a greater understanding of machine learning and R markdown.
