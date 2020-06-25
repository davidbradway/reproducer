---
title: "Report on Whale Data"
author: "David Bradway"
date: "2/4/2020"
output:
  html_document:
    keep_md: TRUE
---



## Load the Libraries



## Read the Raw Data


```
## 
## > kahuna <- read_csv(here::here("data", "2018-11-26_2017-Cape-Hatteras-BRS-kahuna-CEE.csv"))
```

```
## Parsed with column specification:
## cols(
##   date = col_character(),
##   time = col_time(format = ""),
##   longitude = col_double(),
##   latitude = col_double(),
##   ship = col_character(),
##   status = col_character()
## )
```

```
## 
## > kStart <- kahuna %>% filter(status == "start")
## 
## > gm182UP <- read_csv(here::here("data", "2018-11-27_Gm182-UserPoints-Start-CEE-Locations-Kahuna.csv")) %>% 
## +     mutate(status = "userPoints")
```

```
## Parsed with column specification:
## cols(
##   trackNum = col_double(),
##   time = col_datetime(format = ""),
##   dfOrig.x = col_double(),
##   dfOrig.y = col_double()
## )
```

```
## 
## > gm182 <- read_csv(here::here("data", "2018-11-27_Gm182-Start-CEE-Locations-Kahuna.csv")) %>% 
## +     mutate(status = "noUserPoints")
```

```
## Parsed with column specification:
## cols(
##   trackNum = col_double(),
##   time = col_datetime(format = ""),
##   dfOrig.x = col_double(),
##   dfOrig.y = col_double()
## )
```

## Wrangle Data


```
## 
## > gmpts <- bind_rows(gm182, gm182UP)
## 
## > colnames(gmpts) <- c("trackNum", "time", "longitude", 
## +     "latitude", "status")
```

## Do Analysis


```
## 
## > gmpts$d2ship <- rdist.earth.vec(cbind(kStart$longitude, 
## +     kStart$latitude), cbind(gmpts$longitude, gmpts$latitude))
## 
## > gmpts %>% group_by(status) %>% summarize(mean = mean(d2ship, 
## +     na.rm = TRUE))
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```
## # A tibble: 2 x 2
##   status        mean
##   <chr>        <dbl>
## 1 noUserPoints 4.43 
## 2 userPoints   0.962
## 
## > gmpts.fit <- with(gmpts, lmer(d2ship ~ status + (1 | 
## +     trackNum)))
## 
## > gmpts.fit
## Linear mixed model fit by REML ['lmerMod']
## Formula: d2ship ~ status + (1 | trackNum)
## REML criterion at convergence: 793.1435
## Random effects:
##  Groups   Name        Std.Dev.
##  trackNum (Intercept) 0.09388 
##  Residual             1.74937 
## Number of obs: 200, groups:  trackNum, 100
## Fixed Effects:
##      (Intercept)  statususerPoints  
##            4.427            -3.465  
## 
## > summary(gmpts.fit)
## Linear mixed model fit by REML ['lmerMod']
## Formula: d2ship ~ status + (1 | trackNum)
## 
## REML criterion at convergence: 793.1
## 
## Scaled residuals: 
##     Min      1Q  Median      3Q     Max 
## -2.0117 -0.1996 -0.0319  0.1487  4.4918 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev.
##  trackNum (Intercept) 0.008813 0.09388 
##  Residual             3.060291 1.74937 
## Number of obs: 200, groups:  trackNum, 100
## 
## Fixed effects:
##                  Estimate Std. Error t value
## (Intercept)        4.4275     0.1752   25.27
## statususerPoints  -3.4652     0.2474  -14.01
## 
## Correlation of Fixed Effects:
##             (Intr)
## statssrPnts -0.706
```

## Stargazer Table


> stargazer(gmpts.fit, type = "html", header = F, title = "Table 1: Linear Mixed-Effects Model Results", 
+     digits = 1, dep.var.labels = "Distance ..." ... [TRUNCATED] 

<table style="text-align:center"><caption><strong>Table 1: Linear Mixed-Effects Model Results</strong></caption>
<tr><td colspan="2" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left"></td><td>Distances to Ship</td></tr>
<tr><td colspan="2" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left">statususerPoints</td><td>-3.5<sup>***</sup></td></tr>
<tr><td style="text-align:left"></td><td>(0.2)</td></tr>
<tr><td style="text-align:left"></td><td></td></tr>
<tr><td style="text-align:left">Constant</td><td>4.4<sup>***</sup></td></tr>
<tr><td style="text-align:left"></td><td>(0.2)</td></tr>
<tr><td style="text-align:left"></td><td></td></tr>
<tr><td style="text-align:left"><em>N</em></td><td>200</td></tr>
<tr><td style="text-align:left">Log Likelihood</td><td>-396.6</td></tr>
<tr><td style="text-align:left">Akaike Inf. Crit.</td><td>801.1</td></tr>
<tr><td style="text-align:left">Bayesian Inf. Crit.</td><td>814.3</td></tr>
<tr><td colspan="2" style="border-bottom: 1px solid black"></td></tr><tr><td style="text-align:left"><em>Notes:</em></td><td style="text-align:right"><sup>***</sup>Significant at the 1 percent level.</td></tr>
<tr><td style="text-align:left"></td><td style="text-align:right"><sup>**</sup>Significant at the 5 percent level.</td></tr>
<tr><td style="text-align:left"></td><td style="text-align:right"><sup>*</sup>Significant at the 10 percent level.</td></tr>
</table>

## Including Plots

You can also embed plots, for example:

```
## 
## > ggplot(gmpts, aes(longitude, latitude, group = status)) + 
## +     scale_fill_gradient(low = "grey70", high = "grey30", guide = "none") + 
## +     xlab( .... [TRUNCATED]
```

![](RMarkdown_Report_files/figure-html/plot-1.png)<!-- -->

<br>

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
