Is COVID-19 Data Reliable? Testing for Inaccuracy in COVID-19 Data Using
Benford’s Law
================
Brandon Wu

# Executive Summary

The goal of my study was to investigate possible inaccuracies in
COVID-19 data. During this pandemic, many Americans have kept up to date
with the newest data regarding COVID-19 cases and deaths. However, data
from different sources may report conflicting numbers on the true cases
and deaths. In my study, I want to investigate one data source from the
New York Times and compare it to the ideal distribution of Benford’s Law
to detect any inaccuracies. Benford’s Law gives an expected distribution
of the relative frequencies of the first digits in a given set of
numbers which I can compare to the actual distribution.

I extracted a data source from the New York Times which consisted of the
cumulative deaths and cases of the 50 states over the course of around
545 days. To start off, I cleaned the data and added new columns for
daily deaths and daily cases, which would be the set of numbers I
investigate using Benford’s Law. Then using the benford.analysis package
I were able to perform a chi-squared goodness of fit test for the data
from each of the fifty states. The package analyzed the first digit of
each value for daily deaths and daily cases and compared it to the trend
given by Benford’s Law. Using the p-values from the chi-squared test, I
created a heatmap of the cases and deaths of each of the fifty states.
The lower the p-value, the more likely that the given state was
providing inaccurate data. I also created a scatterplot of the case
p-values vs the death p-values to see if there was any correlation.

After performing my analysis, I concluded that many states, according to
Benford’s Law, provided inaccurate data for daily cases and deaths. More
states gave inaccurate data for daily cases than deaths. I also found
that there was no correlation between political affiliation and the
states that tended to give inaccurate case and death data. Finally, I
also concluded that there was no relationship between the p-value for
deaths and the p-value for cases, which meant that states were most
likely not purposefully giving inaccurate data.

# Introduction

## 2.1 Purpose

In the past year there has been much speculation as to the accuracy of
reported data by the US government and each individual state and county.
To determine the validity of these claims, I analyzed Covid-19 data
using Benford’s Law to determine the accuracy of each state’s reported
cases and deaths. If inaccuracies are found to be likely, it could have
potentially massive ramifications on a national and even global scale-
if the United States of America could not correctly assess and report
COVID-19 data within its own borders, it would also raise doubts about
the credibility of other nations’ data around the globe. Additionally,
if the data is found to have a high likelihood of inaccuracy major
doubts and uneasiness would spread throughout the nation about the
competence of its own welfare and healthcare systems.

## 2.2 Benford’s Law Explanation

Benford’s Law is an observation of the first digits of any real world
numbers and their distributions over time. If they were uniform like one
might expect, each digit would present itself an average of 11.1% of the
time, however, this is not the case, and in reality over 30% of all
naturally occurring numbers start with the digit 1, 17% start with a 2,
12% start with a 3, etc. Benford’s Law has been used to investigate
fraud in data before. The IRS and other Taxation Agencies use the
properties of Benford’s law in order to discern whether or not to audit
a given person or company. Based on their payroll and the distribution
of first digits of all of the numbers within it, each agency has an
algorithm much like the one I have used to sniff out any potential
fraudulent records.

Further resource on Benford’s Law:

[“What is Benford’s Law and Why is it so Important for Data
Science?”](https://towardsdatascience.com/what-is-benfords-law-and-why-is-it-important-for-data-science-312cb8b61048)

# Predictions

I predicted that there may be some correlation between political
affiliation of any given state and inaccuracies within that state’s
data, so states associated with one party might be more likely to lie or
have false recordings over states affiliated with other parties. I also
thought that relatively few states would have inaccurate data. Finally,
I thought that states that inaccurately report the number of cases would
also inaccurately report the number of deaths as they may have had some
issues with their data collection methods.

# Data Source

I found my data from the New York Times on Github, which is the data
Google uses when searching for any Covid-19 statistics. I chose to use
case and death data per state over time, which gave us around 545 days
of data per state. And Since Washington had the first case, it has more
days of data than the other states.

[New York Times Covid-19 Case & Death
Data](github.com/nytimes/covid-19-data/blob/master/us-states.csv)

The primary package used for my Benford’s analysis is the Benford’s
Package from R documentation.org, the plot and summary function was used
to gather data for Exploratory Data Analysis

[Benford.Analysis
package](https://www.rdocumentation.org/packages/benford.analysis/versions/0.1.5)

# Data Cleaning

I removed the FIPS column (county identification) and only used state
names as it was unnecessary information when concerning my hypotheses.
Since the data was cumulative, I created additional columns that showed
the rate at which cases and deaths increased per day, and I looked at
the first digits of these rates. I also created an error column, which
returns true if daily_new_deaths is negative. I also grouped by state to
be able to easily scan through the data to see if there are any missing
days or states.

``` r
head(data2)
```

    ## # A tibble: 6 x 7
    ## # Groups:   state [1]
    ##   date       state   cases deaths daily_new_cases daily_new_deaths error
    ##   <chr>      <chr>   <int>  <int>           <dbl>            <dbl> <lgl>
    ## 1 2020-03-13 Alabama     6      0               6                0 FALSE
    ## 2 2020-03-14 Alabama    12      0               6                0 FALSE
    ## 3 2020-03-15 Alabama    23      0              11                0 FALSE
    ## 4 2020-03-16 Alabama    29      0               6                0 FALSE
    ## 5 2020-03-17 Alabama    39      0              10                0 FALSE
    ## 6 2020-03-18 Alabama    51      0              12                0 FALSE

# Exploratory Data Analysis

I used benford.analysis to find P-values when the first digit of
daily_new_cases and daily_new_deaths is compared to Benford’s Law. The
function also creates a distribution chart of the first digits and the
frequency of those variables compared to Benford’s Law. I subset the
data frame to only include data from a specific state, for example
California, and used the function benford on the new subset. I then plot
the distribution and print the analysis.

## 6.1 Hypothesis Test

I tested the accuracy of the New York Times COVID-19 data by comparing
it to Benford’s Law and the predicted distribution of first digits. The
null hypothesis was that the COVID-19 statistic, which was either number
of daily cases or daily deaths, conforms to the curve designated by
Benford’s Law, essentially meaning that the data was unlikely to be
inaccurate. The alternate hypothesis was that the covid statistic
(case/death) does not conform to Benford’s Law and has a higher chance
of being inaccurate. The test will consist of a significance level of
0.01 as a 0.01 level is used widely in the medicinal and professional
field when making important decisions in statistical testing. A lower
alpha level of 0.01, instead of 0.05, would also help prevent type I
errors which is crucial as it could be extremely dangerous to mistakenly
reject the null and cause unnecessary suspicion surrounding an already
divisive topic.

## 6.2 Chi-Squared Explanation

To test this conformity, I used a Chi Squared Goodness of Fit Test which
compares a given null distribution to the actual data by subtracting the
expected value from every observed piece of data, squaring it, and
dividing by the expected value, I then added all of these up to get a
chi squared statistic. I then find the p-value by finding the area under
the curve to the right of the chi-squared test statistic, and use the
p-value to determine if I reject or fail to reject the null hypothesis.

## 6.3 Benford’s Analysis

The Digits Distribution plot reveals the distribution of the digits in a
certain data set type of a given state.

The Chi-Squared plot of each analysis reveals the similarity between the
expected distribution of digits versus the observed distribution. A high
Chi-Squared value reflects a greater difference of the observed
distribution in comparison to the expected distribution.

The red dotted line on the Digits Distribution plot represents the
expected distribution.

### Benford Analysis - New York

#### Cases

The Benford’s Analysis of cases in a state such as New York reveals a
Digits Distribution that conforms to the expected trend from Benford’s
Law.

![](Covid_19_Project_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->![](Covid_19_Project_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

#### Deaths

I imitated the process of analysis for New York case data to produce the
plots for death data using the daily_new_deaths data set.

![](Covid_19_Project_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->![](Covid_19_Project_files/figure-gfm/unnamed-chunk-3-2.png)<!-- -->

### Benford Analysis - Georgia

#### Cases

The Benford’s Analysis of cases in a state such as Georgia reveals a
Digits Distribution that conforms to the expected trend from Benford’s
Law.

![](Covid_19_Project_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->![](Covid_19_Project_files/figure-gfm/unnamed-chunk-4-2.png)<!-- -->

#### Deaths

I imitated the process of analysis for Georgia case data to produce the
plots for death data using the daily_new_deaths data set.

![](Covid_19_Project_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->![](Covid_19_Project_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

## 6.4 Benford Summaries

The Benford’s analysis was summarized in order to find the p-values,
which provided a numeric likelihood of data set inaccuracy.

    ## 
    ## Benford object:
    ##  
    ## Data: state.cases$daily_new_deaths 
    ## Number of observations used = 489 
    ## Number of obs. for second order = 235 
    ## First digits analysed = 1
    ## 
    ## Mantissa: 
    ## 
    ##    Statistic  Value
    ##         Mean  0.506
    ##          Var  0.094
    ##  Ex.Kurtosis -1.274
    ##     Skewness -0.137
    ## 
    ## 
    ## The 5 largest deviations: 
    ## 
    ##   digits absolute.diff
    ## 1      2         20.11
    ## 2      8          7.99
    ## 3      7          6.64
    ## 4      4          4.39
    ## 5      6          4.26
    ## 
    ## Stats:
    ## 
    ##  Pearson's Chi-squared test
    ## 
    ## data:  state.cases$daily_new_deaths
    ## X-squared = 10, df = 8, p-value = 0.3
    ## 
    ## 
    ##  Mantissa Arc Test
    ## 
    ## data:  state.cases$daily_new_deaths
    ## L2 = 0.01, df = 2, p-value = 0.003
    ## 
    ## Mean Absolute Deviation (MAD): 0.0113
    ## MAD Conformity - Nigrini (2012): Acceptable conformity
    ## Distortion Factor: 4.34
    ## 
    ## Remember: Real data will never conform perfectly to Benford's Law. You should not focus on p-values!

I now have the p-values from the Pearson’s Chi-Squared Goodness of fit
needed to visualize my data.

## 6.5 Heatmaps

I created a csv of all 50 states’ p-values for cases and deaths given
the data from Benford’s Summaries. my significance value was determined
to be 0.01 because most medical studies use this value when conducting
COVID-19 tests. Therefore, I set the maximum p-value of deaths and cases
to be 0.01 by cleaning my .csv file.

### Case p-value Heatmap

This is the US case heat map produced from the data frame of P-values. A
high P-value signifies a lower likelihood of false reporting so the
color of states with high P-values are lighter. A low P-value signifies
a higher likelihood of false reporting. As reflected by the map a
majority of US states have some likelihood of inaccurate case reports.

![](Covid_19_Project_files/figure-gfm/Fraud_Map_Cases-1.png)<!-- -->

### Death p-value Heatmap

This is the US death heat map produced from the data frame of P-values.
A high P-value signifies a lower likelihood of false reporting so the
color of states with high P-values are lighter. A low P-value signifies
a higher likelihood of false reporting. According to the map, inaccuracy
in death reporting seems to be less common than case reports.

![](Covid_19_Project_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

# Relationship of logDeaths and logCases

## 7.1 Scatter Plot p-values Comparison

I wanted to test if there was a correlation between inaccuracies in
death reporting vs inaccuracies in case reporting. I used ggplot to
create a scatterplot of log(deaths) vs log(cases). Using log, I were
able to make the data better fit the scales since the original range of
the data from 2 \*10-16 to 0.8 was too large. I also separated by state
to see which states had higher and lower p-values. After graphing the
data, I plotted a line of best fit to see if there was a linear model.
Just looking at the graph directly without performing a summary on the
linear regression, it appears that there is no correlation between the
inaccuracies for death reporting vs the inaccuracies in case reporting.

![](Covid_19_Project_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## 7.2 Linear Regression

In this summary of the regression, I can focus on 2 values, the P-value
of the Case P-value variable, and the Multiple R-Squared value of the
model. my NULL hypothesis for analysis is that the P value of cases does
not have an affect on the P-value of deaths. The P-value of the P-value
of cases is 0.3, which is too high to reject the Null Hypothesis. Based
on the linear regression R squared value of 2.27% , there is almost no
correlation between Case report inaccuracy and Death report inaccuracy.

    ## 
    ## Call:
    ## lm(formula = P.value_Death ~ P.value_Case, data = data4)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -0.2378 -0.1577 -0.0757  0.1387  0.6951 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)    0.1578     0.0326    4.84  1.4e-05 ***
    ## P.value_Case   0.1572     0.1488    1.06      0.3    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.209 on 48 degrees of freedom
    ## Multiple R-squared:  0.0227, Adjusted R-squared:  0.00239 
    ## F-statistic: 1.12 on 1 and 48 DF,  p-value: 0.296

# Limitations

The most obvious limitation is that Benford’s Law may not be a perfect
mechanism to determine inaccurate reporting. Since there have been
precedents for using Benford’s Law to detect fraud, I believe that
Benford’s Law is applicable in this situation. Another limitation was
incorrect values in data set itself. For example, there were negative
values for columns like daily deaths which doesn’t make sense,
indicating either a faulty data collection method or purposeful
misreporting. However, these inaccurate values were fairly rare and
didn’t affect my data too much.

# Key Findings

I found that there was a much higher likelihood of inaccuracy concerning
the data from case reportings of each individual state when compared to
the corresponding death reportings when Benford’s law is taken into
account. This may be due to the fact that there were many more cases
than deaths, from a numerical standpoint, which could have caused more
mistakes in recording, as well as the fact that reporting a death has a
much greater ramification than reporting a case which may make any
handlers of data much more weary of false reports.

I also concluded from the low r^2 value of 0.02 in my linear regression
that if a US state had a high likelihood for false case reporting, it
would be no more likely to have false death reporting than any other
state. Therefore there is no proof of purposeful falsification of data
as there was little to no correlation between the p-values of the 2
variables, cases and deaths. But I do have statistical evidence that
there is a high likelihood some states could have inaccurate data based
on some of the very low p-values I collected.

It was also found that there is no perceivable correlation between
political party and potential inaccuracy in COVID-19 data contrary to my
prior predictions.

All prior Benford’s Law tests on COVID-19 data on google scholar were
scoured and as far as I could find, there has yet to be a Benford
analysis of COVID data on a state level, but there have been many on a
national and international level. This is exciting as it could lead to
new findings and patterns that have remained undiscovered until now.

If I were to further my research,I would look into county wide data
across the nation in an attempt to pinpoint where certain suspected
inaccuracies originate from and eventually focus on specific testing
centers in those counties.

# Appendix

    ## [1] "date: yyyy-mm-dd every day since Jan 21, 2020 for all 50 states"

    ## [1] "fips: Individual county identifier in each state"

    ## [1] "state: state out of 50 states in USA"

    ## [1] "cases: Cumulative recorded cases in state as of that date"

    ## [1] "deaths:Cumulative recorded deaths in state as of that date"

    ## [1] "daily_new_cases: New recorded cases on that date"

    ## [1] "daily_new_deaths: New recorded deaths on that date"

    ## [1] "error: TRUE = negative value (incorrect input), FALSE = positive value (correct input)"

    ## [1] "Chi.Squared_Case: Chi-squared of daily_new_cases value of a state for all dates in comparison to Benford's Distribution"

    ## [1] "P.value_Case: P-value of daily_new_cases value of a state for all dates in comparison to Benford's Distribution"

    ## [1] "Chi.Squared_Death: Chi-squared of daily_new_deaths value of a state for all dates in comparison to Benford's Distribution"

    ## [1] "P.value_Death: P-value of daily_new_deaths value of a state for all dates in comparison to Benford's Distribution"

    ## [1] "Freq: number of appearances of the first digit given all values in the data set of a state"

# Works Cited

<center>
Nytimes. “Covid-19-Data/Us-States.Csv at Master ·
Nytimes/Covid-19-Data.” GitHub, 28 July 2021,
github.com/nytimes/covid-19-data/blob/master/us-states.csv.
</center>
