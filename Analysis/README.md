# This folder contains data, figures and table related to Master Project
---
title: "MSc Project"
author: "SOBOYEJO LUKMAN ADEBOYE"
date: "01/06/2020"
output: html_document
---

```{r global_options, include = FALSE}
# knitr::opts_knit$set(message=FALSE)
# tidy.opts=list(width.cutoff=60)) 
```

## Packages Installed ----
```{r}
# pkgs <-
  (c("forecast", "patchwork", "ggplot2", "plotly", "tidyverse", "pander", "ggthemes", "reshape"))
# install.packages(pkgs)

library(forecast)   # For the moving avearage timeseries
library(patchwork)  # For combining plots
library(ggplot2)    # For Visualization
library(plotly)     # For interactive plot
library(lubridate)  #
library(knitr)
library(tidyverse)
library(pander)
library(ggthemes)
library(reshape2)
```

## Theme ----
```{r}
ann.theme <- function(){
    #   ## ggthemes
    # theme_solarized_2(base_size = 10, light = FALSE) +
    theme(plot.title = element_text(hjust=,
                                  face = "plain",
                                  size = 16),
          axis.text = element_text(face = "plain",
                                 size =  8),
          axis.title = element_text(face = "plain",
                                  size = 8))
}

mon.theme <- function(){
    # ## ggthemes
    # theme_solarized_2(base_size = 10, light = FALSE) +
    theme(plot.title = element_text(hjust=,
                                  face = "plain",
                                  size = 16),
          axis.text = element_text(face = "bold",
                                 size =  8),
          axis.title = element_text(face = "bold",
                                  size = 8))
}

season.theme <- function(){
    #   ## ggthemes
    # theme_solarized_2(base_size = 10, light = FALSE) +
    theme(plot.title = element_text(hjust=,
                                  face = "plain",
                                  size = 16),
          axis.text = element_text(face = "plain",
                                 size =  8),
          axis.title = element_text(face = "plain",
                                  size = 8))
}

season.summary <- function(){
      # Add a red line to the mean
    stat_summary(aes(ymax = ..y.., ymin = ..y..),
               fun = "mean",
               geom = "pointrange", 
               # Use geom_errorbar to add line as mean
               color = "red",
               position = position_dodge(width = 0.75), 
               # Add the line to each group
               show.legend = FALSE)
}

avg.theme <- function() {
      #   ## ggthemes
    # theme_solarized_2(base_size = 10, light = FALSE) +
    theme(plot.title = element_text(hjust=,
                                  face = "plain",
                                  size = 16),
          axis.text = element_text(face = "plain",
                                 size =  8),
          axis.title = element_text(face = "plain",
                                  size = 8),
          legend.position = "right")
}

budget.theme <- function() {
      #   ## ggthemes
    # theme_solarized_2(base_size = 10, light = FALSE) +
    theme(plot.title = element_text(hjust=,
                                  face = "plain",
                                  size = 16),
          axis.text = element_text(face = "plain",
                                 size =  8),
          axis.title = element_text(face = "plain",
                                  size = 8),
          legend.position = "bottom")
}

drought.theme <- function() {
#   ## ggthemes
    # theme_solarized_2(base_size = 10, light = FALSE) +
    theme(plot.title = element_text(hjust=,
                                  face = "plain",
                                  size = 16),
          axis.text = element_text(face = "plain",
                                 size =  8),
          axis.title = element_text(face = "plain",
                                  size = 8),
          legend.position = "bottom")
}
```


## Read file into R ----
```{r}
# Monthly Time series

san.mon.data <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/Sanvitale.R/SanVitale Basin/san.mon.csv")
ras.mon.data <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/Rasponi.R/Rasponi Basin/ras.mon.csv")
quin.mon.data <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/Fosso_Ghiaia.R/Quinto Basin/quin.mon.csv")


# Yearly Time series

quin.ann.data <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/Fosso_Ghiaia.R/Quinto Basin/quin.ann.csv")
ras.ann.data <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/Rasponi.R/Rasponi Basin/ras.ann.csv")
san.ann.data <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/Sanvitale.R/SanVitale Basin/san.ann.csv")

## Seasonal Average
wat_bal_san <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/Sanvitale.R/SanVitale Basin/wat_bal_san.csv")
wat_bal_ras <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/Rasponi.R/Rasponi Basin/wat_bal_ras.csv")
wat_bal_quin <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/Fosso_Ghiaia.R/Quinto Basin/wat_bal_quin.csv")

## Water Budget
water_budget <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/dat.csv")


## Drought Values
spi.data <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/Code/Drought_Index.csv")
Drought_Index <- read.csv("D:/Bologna Project/Land_Reclamation_Excel_Data/Code/Drought_Index.csv")

## Subsidence
subsidence <- read.delim("D:/Bologna Project/Land_Reclamation_Excel_Data/subsidence.txt")

# kable()
# pander()
```


## Time series ----
link to learn more about ggthemes
[link](https://jrnold.github.io/ggthemes/reference/index.html)


## Monthly Prec
```{r}
## Date Conversion to POSXCIT
newyear = parse_date_time(san.mon.data[,1], "by")
date = as.Date.POSIXct(newyear)
# # change to date format
# mydate.ch <- format(date, "%b %Y")
# # change to factor
# mydate.fac <- factor(mydate.ch, unique(mydate.ch))

# Data Manipulation for P
# Time Series of precipitation
rain.ts <- ts(san.mon.data$p, frequency = 12, start = c(1971,1))
# moving average
rain.ma = ma(rain.ts, order = 12, centre = T)
# unclass rain moving average
unclass.rain <- unclass(rain.ma)
# dput

# combined different type of object
df.rain <- tibble(date, unclass.rain)


# Data Visualization - ggplot2
(ppt = ggplot(df.rain,
             aes(x = date, y = unclass.rain)) +
  geom_line() +
  geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE,
              na.rm = TRUE) +
    labs(caption = "Data Source: ARPAe Emilia Romagna",
    title = "Precipitation",
       y = "P (mm)",
       x = "Year",
       tag = "") +
        scale_x_date(date_breaks = "5 year",
               date_minor_breaks = "5 year",
               date_labels = "%Y") +
  mon.theme())

# ggsave(filename = "ppt.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()

```

## Monthly AET
```{r}
## Data Manipulation for AET
# Time Series of AET
aet.ts <- ts(san.mon.data$aet, frequency = 12, start = c(1971,1))
# moving average
aet.ma = ma(aet.ts, order = 12, centre = T)
# unclass moving average
unclass.aet <- unclass(aet.ma)
# combined different type of object using tibble
df.aet <- tibble(date, unclass.rain)
head(df.aet)

## Data Visualization for AET
(aet = ggplot(df.aet,
             aes(x = date, y = unclass.aet)) +
  geom_line() +
  geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE,
              na.rm = TRUE) +
  labs(caption = "Data source: ARPAe Emilia Romagna",
       title = "Actual Evapotranspiration",
       y = "AET (mm)",
       x = "Year",
       tag = "") +
    mon.theme())

# ggsave(filename = "aet.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```

## Monthly PET
```{r}
## Data Manipulation for PET
# Time Series of AET
pet.ts <- ts(san.mon.data$pet, frequency = 12, start = c(1971,1))
# moving average
pet.ma = ma(pet.ts, order = 12, centre = T)
# unclass moving average
unclass.pet <- unclass(pet.ma)
# combined different type of object using tibble
df.pet <- tibble(date, unclass.rain)



## Data Visualization for PET
(pet = ggplot(df.pet,
             aes(x = date, y = unclass.pet)) +
  geom_line() +
  geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE,
              na.rm = TRUE) +
  labs(caption = "Data source: ARPAe Emilia Romagna",
       title = "Potential Evapotranspiration",
       y = "PET(mm)",
       x = "Year",
       tag = "") +
    scale_x_date(date_breaks = "5 year",
               date_minor_breaks = "5 year",
               date_labels = "%Y")+
    mon.theme())

# ggsave(filename = "pet.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```

## San Vitale Pu
```{r}
## Data Manipulation for Pumping in San Vitale
# Time series of Pumping
san.pump.ts <- ts(san.mon.data$pu, frequency = 12, start = c(1971,1))
# forecast using moving average
san.pump.ma = ma(san.pump.ts, order = 12, centre = T)
# combined different object to dataframe
unclass.san.pump <- unclass(san.pump.ma)
df.san.pump <- tibble(date, unclass.san.pump)


## pumping in san vitale basin 
(san.pump = ggplot(df.san.pump,
  aes(x = date, y = unclass.san.pump)) +
    geom_line() +
    geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE, 
              na.rm = TRUE) +
    labs(caption = "Data source: Land Reclamation Authority (LRA)",
       title = "San Vitale Pumping",
       y = "Pu(mm)",
       x = "Year", 
       tag = "") +
  mon.theme())
# ggsave(filename = "san.pump.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()

```

## San Vitale Pu/P
```{r}
## Data Manipulation for Pu/P
# Time Series
san.pup.ts <- ts(san.mon.data$pu.p, frequency = 12, start = c(1971,1))
# forecast using moving average
san.pup.ma = ma(san.pup.ts, order = 12, centre = T)
unclass.san.pup <- unclass(san.pup.ma)
# combined different object to data frame
df.san.pup <- tibble(date, unclass.san.pup)



## Visualization with ggplot
(san.pup = ggplot(df.san.pup,
                       aes(x = date, y = unclass.san.pup)) +
  geom_line() +
  geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE,
              na.rm = TRUE)  +
  labs(caption = "",
       title = "San Vitale - Pumping / Precipitation",
       y = "Pu/P (mm)",
       x = "Year", 
       tag = "") +
  mon.theme() +
  geom_hline(yintercept = 1, linetype = "dashed", color = "red"))

# ggsave(filename = "san.pup.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```

## San Vitale Infl
```{r}
# Data Manipulation for Infl
# Time Series
san.inf.ts <- ts(san.mon.data$infl, frequency = 12, start = c(1971,1))

# forecast using moving average
san.inf.ma = ma(san.inf.ts, order = 12, centre = T)
unclass.san.inf <- unclass(san.inf.ma)
# combined different object to data frame
df.san.inf <- tibble(date, unclass.san.inf)

## Infiltration in San Vitale
(san.infl = ggplot(df.san.inf,
                      aes(x = date, y = unclass.san.inf)) +
  geom_line() +
  geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE,
              na.rm = TRUE)  +
  labs(caption = "",
       title = "San Vitale - Infiltration (empirical)",
       y = "Infl (mm)",
       x = "Year", 
       tag = "") +
    mon.theme()) 

# ggsave(filename = "san.infl.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```

## San Vitale dS
```{r}
# Data Manipulation for storage
# Time Series
san.ds.ts <- ts(san.mon.data$ds, frequency = 12, start = c(1971,1))
# forecast using moving average
san.ds.ma = ma(san.ds.ts, order = 12, centre = T)

# combined different object to data frame
unclass.san.ds <- unclass(san.ds.ma)
df.san.ds <- tibble(date, unclass.san.ds)

## Data Visualization with ggplot2
(san.ds = ggplot(df.san.ds,
                      aes(x = date, y = unclass.san.ds)) +
  geom_line() +
  geom_smooth(aes(x = date, y = unclass.san.ds),
              method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE,
              na.rm = TRUE)  +
  labs(caption = "",
       title = "San Vitale Change in Water Storage (empirical)",
       y = "dS (mm)",
       x = "Year", 
       tag = "") +
  mon.theme() +
    geom_hline(yintercept = 0, linetype = "dashed", color = "red"))

# ggsave(filename = "san.ds.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()

```

## Rasponi Pu
```{r}

## Data Manipulation
# Time series
ras.pump.ts <- ts(ras.mon.data$pu, frequency = 12, start = c(1971,1))
# forecast using moving average
ras.pump.ma = ma(ras.pump.ts, order = 12, centre = T)
# combined different object to dataframe
unclass.ras.pump <- unclass(ras.pump.ma)
df.ras.pump <- tibble(date, unclass.ras.pump)


## pumping in san vitale basin 
(san.pump = ggplot(df.ras.pump,
  aes(x = date, y = unclass.ras.pump)) +
    geom_line() +
    geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE, 
              na.rm = TRUE) +
  labs(caption = "Data Source: Land Reclamation Authority (LRA)",
       title = "Rasponi Pumping",
       y = "Pu(mm)",
       x = "Year", 
       tag = "") +
    mon.theme()) 

# ggsave(filename = "ras.pump.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
  
```

## Rasponi Pu / P
```{r}
## Data Manipulation
# Times series
ras.pup.ts <- ts(ras.mon.data$pu.p, frequency = 12, start = c(1971,1))
# forecast using moving average
ras.pup.ma = ma(ras.pup.ts, order = 12, centre = T)

# combined different object to data frame
unclass.ras.pup <- unclass(ras.pup.ma)
df.ras.pup <- tibble(date, unclass.ras.pup)


## Pumping over Precipitation
(ras.pup = ggplot(df.ras.pup,
                     aes(x = date, y = unclass.ras.pup)) +
  geom_line() +
  geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE,
              na.rm = TRUE)  +
  labs(caption = "",
       title = "Rasponi Pumping / Precipitation",
       y = "Pu/P(mm)",
       x = "Year", 
       tag = "") +
  mon.theme() +
    geom_hline(yintercept = 1, linetype = "dashed", color = "red"))

# ggsave(filename = "ras.pup.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```

## Rasponi Infl
```{r}
## Data Manipulation
# Time Series
ras.inf.ts <- ts(ras.mon.data$infl, frequency = 12, start = c(1971,1))
# forecast using moving average
ras.inf.ma = ma(ras.inf.ts, order = 12, centre = T)
# combined different object to data frame
unclass.ras.inf <- unclass(ras.inf.ma)
df.ras.inf <- tibble(date, unclass.ras.inf)


## Infiltration
(ras.infl = ggplot(df.ras.inf,
                      aes(x = date, y = unclass.ras.inf)) +
  geom_line() +
  geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE, 
              na.rm = TRUE)  +
  labs(caption = "",
       title = "San Vitale Infiltration (Empirical)",
       y = "Infl (mm)",
       x = "Year", 
       tag = "") +
  mon.theme())

# ggsave(filename = "ras.infl.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```

## Rasponi dS
```{r}
## Data Manipulation
# Time series
ras.ds.ts <- ts(ras.mon.data$ds, frequency = 12, start = c(1971,1))
# forecast using moving average
ras.ds.ma = ma(ras.ds.ts, order = 12, centre = T)
unclass.ras.ds <- unclass(ras.ds.ma)

# combined different object to data frame
df.ras.ds <- tibble(date, unclass.ras.ds)


## Infiltration
(ras.ds = ggplot(df.ras.ds,
                    aes(x = date, y = unclass.ras.ds)) +
  geom_line() +
  geom_smooth(aes(x = date, y = unclass.ras.ds),
              method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE, 
              na.rm = TRUE)  +
  labs(caption = "",
       title = "San Vitale Change in Water Storage (Emprical)",
       y = "dS(mm)",
       x = "Year", 
       tag = "") + 
      mon.theme())

# ggsave(filename = "ras.ds.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```

## Quinto P
```{r}
## Data Manipulation
# Time Series
quin.pump.ts <- ts(quin.mon.data$pu, frequency = 12, start = c(1971,1))
# forecast using moving average
quin.pump.ma = ma(quin.pump.ts, order = 12, centre = T)
# combined different object to data frame
unclass.quin.pump <- unclass(quin.pump.ma)
df.quin.pump <- tibble(date, unclass.quin.pump)

## pumping 
(quin.pump = ggplot(df.quin.pump,
                      aes(x = date, y = unclass.quin.pump)) +
  geom_line() +
  geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE, 
              na.rm = TRUE)  +
  labs(caption = "Data Source: Land Reclamation Consortium Ravenna",
       title = "Quinto Pumping",
       y = "Pu (mm)",
       x = "Year", 
       tag = "") +
  mon.theme())

# ggsave(filename = "quin.pump.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Quinto Pu/P
```{r}
## Data Manipulation
# Time series
quin.pup.ts <- ts(quin.mon.data$pu.p, frequency = 12, start = c(1971,1))
# forecast using moving average
quin.pup.ma = ma(quin.pup.ts, order = 12, centre = T)
# combined different object to data frame
unclass.quin.pup <- unclass(quin.pup.ma)
df.quin.pup <- tibble(date, unclass.quin.pup)

## Data Visualization with ggplot2
(quin.pup = ggplot(df.quin.pup,
                     aes(x = date, y = unclass.quin.pup)) +
  geom_line() +
  geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE, 
              na.rm = TRUE)  +
  labs(caption = "",
       title = "Quinto Pumping / Precipitation",
       y = "Pu/P",
       x = "Year", 
       tag = "") +
    mon.theme())

# ggsave(filename = "quin.pup.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Quinto Infl
```{r}
## Data Manipulation 
# Time Series
quin.inf.ts <- ts(quin.mon.data$infl, frequency = 12, start = c(1971,1))
# forecast using moving average
quin.inf.ma = ma(quin.inf.ts, order = 12, centre = T)
# unclass the time series
unclass.quin.inf <- unclass(quin.inf.ma)
# combined different object to data frame
df.quin.inf <- tibble(date, unclass.quin.inf)


## Data Visualization with ggplot2
(quin.infl = ggplot(df.quin.inf,
                      aes(x = date, y = unclass.quin.inf)) +
  geom_line() +
  geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE, 
              na.rm = TRUE)  +
  labs(caption = "",
       title = "Quinto Infiltration (Empirical)",
       y = "Infl (mm)",
       x = "Year", 
       tag = "") +
  mon.theme())

# ggsave(filename = "quin.infl.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Quinto dS
```{r}
## Data Manipulation 
# Time series
quin.ds.ts <- ts(quin.mon.data$ds, frequency = 12, start = c(1971,1))
# forecast using moving average
quin.ds.ma = ma(quin.ds.ts, order = 12, centre = T)
# combined different object to data frame
unclass.quin.ds <- unclass(quin.ds.ma)
df.quin.ds <- tibble(date, unclass.quin.ds)


## Data Visualization with ggplot2
(quin.ds = ggplot(df.quin.ds,
                    aes(x = date, y = unclass.quin.ds)) +
  geom_line() +
  geom_smooth(aes(x = date, y = unclass.quin.ds),
              method = "loess",
              formula = y ~ x,
              span = 0.3,
              level = 0.95,
              se = FALSE, 
              na.rm = TRUE) +
  labs(caption = "",
       title = "Quinto Change in Storage",
       y = "dS (mm)",
       x = "Year", 
       tag = "") +
    mon.theme())

# ggsave(filename = "quin.ds.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
# Modeling ----
# Annual P
```{r}
(rain.ann = ggplot(data = ras.ann.data, 
           mapping = aes(x = year, y = p)) +
  geom_line() + 
  geom_smooth(method = "loess", 
              formula = y ~ x, 
              span = 0.5, 
              level = 0.95, 
              se = TRUE, 
              na.rm = FALSE)+
  labs(x = "Year", 
       y = "P", 
       title = "Precipitation over the Area") +
   ann.theme())

# ggsave(filename = "rain.ann.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()

```

## Annual AET
```{r}
(aet.ann = ggplot(data = ras.ann.data, 
                 mapping = aes(x = year, y = aet)) +
  geom_line() + 
  geom_smooth(method = "loess", 
              formula = y ~ x, 
              span = 0.5, 
              level = 0.95, se = TRUE, 
              na.rm = FALSE) +
  labs(x = "Year", 
       y = "AET", 
       title = "Actual ET over the Area") +
    ann.theme())

# ggsave(filename = "aet.ann.pump.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```

## Annual PET
```{r}
(pet = ggplot(data = ras.ann.data, 
             mapping = aes(x = year, y = pet)) +
  geom_line() + 
  geom_smooth(method = "loess", 
              formula = y ~ x, 
              span = 0.5, 
              level = 0.95, 
              se = TRUE, 
              na.rm = FALSE) +
  labs(x = "Year", 
       y = "PET", 
       title = "Potential ET over the Area") +
    #   ## ggthemes
    # theme_solarized_2(base_size = 10, light = FALSE) +
    ann.theme())

# ggsave(filename = "pet.ann.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
  
```

# Annual Rasponi
```{r}
## Visualization with ggplot
(ras.ann.pump = ggplot(data = ras.ann.data, 
              mapping = aes(x = year, y = pu)) +
    geom_line() + 
   geom_smooth(method = "loess", 
              formula = y ~ x, 
              span = 0.5, 
              level = 0.96, 
              se = TRUE, 
              na.rm = FALSE)+
    labs(x = "Year", 
       y = "Pu (mm)", 
       title = "Rasponi Annual Pumping") +
    #   ## ggthemes
    # theme_solarized_2(base_size = 10, light = FALSE) +
    ann.theme())

# ggsave(filename = "ras.ann.pump.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()

```

## Annual San Vitale
```{r}
(san.ann.pump = ggplot(data = san.ann.data,
                      mapping = aes(x = year, y = pu)) +
    geom_line() + 
    geom_smooth(method = "loess", 
              formula = y ~ x, 
              span = 0.5, 
              level = 0.95, 
              se = TRUE, 
              na.rm = FALSE) +
   labs(x = "year", 
       y = "pu (mm)", 
       title = "San Annual Vitale Pumping") +
   ann.theme())

# ggsave(filename = "san.ann.pump.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Annual Quinto
```{r}
(quin.ann.pump = ggplot(data = quin.ann.data,
            mapping = aes(x = year, y = pu)) +
    geom_line() + 
    geom_smooth(method = "loess",
              formula = y ~ x,
              span = 0.5, 
              level = 0.95,
              se = TRUE, 
              na.rm = FALSE) +
    labs(x = "Year", 
       y = "Pu (mm)", 
       title = "Quinto Basin (Pumping)") +
    #   ## ggthemes
    # theme_solarized_2(base_size = 10, light = FALSE) +
    theme(plot.title = element_text(hjust=,
                                  face = "plain",
                                  size = 16),
          axis.text = element_text(face = "plain",
                                 size =  8),
          axis.title = element_text(face = "plain",
                                  size = 8)))

# ggsave(filename = "quin.ann.pump.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```

## Seasonality ----
## Rainfall
```{r, warning= FALSE}
# select the column
rain <- san.mon.data[,2]
# Combined Observations into data frame
df.rain <- tibble(date, rain)

season.rain <- df.rain %>%
  # Convert data frame from lwide format to long format
  gather(season.rain, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.rain = str_replace(season.rain, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.rain$Month = factor(season.rain$Month, levels = month.abb)

# Visualization with ggplot
(ppt = ggplot(season.rain, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "PPT (mm)",
         title = "Precipitation") +
    season.theme() +
    season.summary())

ggplotly(ppt)

# ggsave(filename = "mm.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## PET
```{r,warning=FALSE}
# select the column
pet <- san.mon.data[,3]
# Combined Observations into data frame
df.pet <- tibble(date, pet)

season.pet <- df.pet %>%
  # Convert data frame from lwide format to long format
  gather(season.pet, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.pet = str_replace(season.pet, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.pet$Month = factor(season.pet$Month, levels = month.abb)

# Visualization with ggplot
(pet = ggplot(season.pet, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "PET",
         title = "Potential ET") +
    season.theme() +
    season.theme())

ggplotly(pet)

# ggsave(filename = "mm.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## AET
```{r}
# select the column
aet <- san.mon.data[,4]
# Combined Observations into data frame
df.aet <- tibble(date, aet)

season.aet <- df.aet %>%
  # Convert data frame from lwide format to long format
  gather(season.aet, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.aet = str_replace(season.aet, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.aet$Month = factor(season.aet$Month, levels = month.abb)

# Visualization with ggplot
(aet = ggplot(season.aet, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "AET",
         title = "Potential ET") +
    season.theme() +
    season.theme())

ggplotly(aet)

# ggsave(filename = "mm.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```

## San Vitale Pu
```{r,warning= FALSE}
# select the column
pu <- san.mon.data[,5]
# Combined Observations into data frame
df.pu <- tibble(date, pu)

season.pu <- df.pu %>%
  # Convert data frame from lwide format to long format
  gather(season.pu, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.pu = str_replace(season.pu, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.pu$Month = factor(season.pu$Month, levels = month.abb)

# Visualization with ggplot
(san_pu <- ggplot(season.pu, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "Pu (mm)",
         title = "Potential ET") +
    season.theme() +
    season.theme())

ggplotly(san_pu)

# ggsave(filename = ".png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## SanVitale Pu/P
```{r, warning= FALSE}
# select the column
pup <- san.mon.data[,6]
# Combined Observations into data frame
df.pup <- tibble(date, pup)

season.pup <- df.pup %>%
  # Convert data frame from lwide format to long format
  gather(season.pup, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.pup = str_replace(season.pup, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.pup$Month = factor(season.pup$Month, levels = month.abb)

# Visualization with ggplot
(san_pup = ggplot(season.pup, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "Pu/P (mm)",
         title = "Precipitation / Pumping Ratio") +
    season.theme() +
    season.theme() +
    scale_y_continuous(limits = c(0, 3)))

ggplotly(san_pup)

# ggsave(filename = "pup.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```

## San Vitale Infl
```{r, warning= FALSE}
# select the column
infl <- san.mon.data[,7]
# Combined Observations into data frame
df.infl <- tibble(date, infl)

season.infl <- df.infl %>%
  # Convert data frame from lwide format to long format
  gather(season.infl, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.infl = str_replace(season.infl, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.infl$Month = factor(season.infl$Month, levels = month.abb)

# Visualization with ggplot
(san_infl = ggplot(season.infl, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "Infl (mm)",
         title = "Infiltration") +
    season.theme() +
    season.theme() + 
  scale_y_continuous(limits = c(0, 100)))

ggplotly(san_infl)

# ggsave(filename = "pu.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## San Vitale dS
```{r,warning= FALSE}
# select the column
dS <- san.mon.data[,8]
# Combined Observations into data frame
df.dS <- tibble(date, dS)

season.dS <- df.dS %>%
  # Convert data frame from lwide format to long format
  gather(season.dS, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.dS = str_replace(season.dS, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.dS$Month = factor(season.dS$Month, levels = month.abb)

# Visualization with ggplot
(san_dS = ggplot(season.dS, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "dS (mm)",
         title = "Change in Water Storage") +
    season.theme() +
    season.theme())

ggplotly(san_dS)

# ggsave(filename = "pu.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Rasponi Pu
```{r, warning= FALSE}
# select the column
pu <- ras.mon.data[,5]
# Combined Observations into data frame
df.pu <- tibble(date, pu)

season.pu <- df.pu %>%
  # Convert data frame from lwide format to long format
  gather(season.pu, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.pu = str_replace(season.pu, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.pu$Month = factor(season.pu$Month, levels = month.abb)

# Visualization with ggplot
(ras_pu <- ggplot(season.pu, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "Pu (mm)",
         title = "Potential ET") +
    season.theme() +
    season.theme())

ggplotly(ras_pu)

# ggsave(filename = ".png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Rasponi Pu/P
```{r, warning= FALSE}
# select the column
pup <- ras.mon.data[,6]
# Combined Observations into data frame
df.pup <- tibble(date, pup)

season.pup <- df.pup %>%
  # Convert data frame from lwide format to long format
  gather(season.pup, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.pup = str_replace(season.pup, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.pup$Month = factor(season.pup$Month, levels = month.abb)

# Visualization with ggplot
(ras_pup = ggplot(season.pup, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "Pu/P (mm)",
         title = "Precipitation / Pumping Ratio") +
    season.theme() +
    season.theme() +
    scale_y_continuous(limits = c(0, 3)))

ggplotly(ras_pup)

# ggsave(filename = "pup.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Rasponi Infl
```{r, warning = FALSE}
# select the column
infl <- ras.mon.data[,7]
# Combined Observations into data frame
df.infl <- tibble(date, infl)

season.infl <- df.infl %>%
  # Convert data frame from lwide format to long format
  gather(season.infl, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.infl = str_replace(season.infl, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.infl$Month = factor(season.infl$Month, levels = month.abb)

# Visualization with ggplot
(ras_infl = ggplot(season.infl, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "Infl (mm)",
         title = "Infiltration") +
    season.theme() +
    season.theme() + 
  scale_y_continuous(limits = c(0, 100)))

ggplotly(ras_infl)

# ggsave(filename = "pu.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Rasponi dS
```{r,warning= FALSE}
# select the column
dS <- ras.mon.data[,8]
# Combined Observations into data frame
df.dS <- tibble(date, dS)

season.dS <- df.dS %>%
  # Convert data frame from lwide format to long format
  gather(season.dS, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.dS = str_replace(season.dS, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.dS$Month = factor(season.dS$Month, levels = month.abb)

# Visualization with ggplot
(ras_dS = ggplot(season.dS, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "dS (mm)",
         title = "Change in Water Storage") +
    season.theme() +
    season.theme())

ggplotly(ras_dS)

# ggsave(filename = "pu.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Quinto Pu
```{r, warning= FALSE}
# select the column
pu <- quin.mon.data[,5]
# Combined Observations into data frame
df.pu <- tibble(date, pu)

season.pu <- df.pu %>%
  # Convert data frame from lwide format to long format
  gather(season.pu, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.pu = str_replace(season.pu, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.pu$Month = factor(season.pu$Month, levels = month.abb)

# Visualization with ggplot
(quin_pu <- ggplot(season.pu, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "Pu (mm)",
         title = "Potential ET") +
    season.theme() +
    season.theme())

ggplotly(ras_pu)

# ggsave(filename = ".png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Quinto Pu/P
```{r, warning= FALSE}
# select the column
pup <- quin.mon.data[,6]
# Combined Observations into data frame
df.pup <- tibble(date, pup)

season.pup <- df.pup %>%
  # Convert data frame from lwide format to long format
  gather(season.pup, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.pup = str_replace(season.pup, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.pup$Month = factor(season.pup$Month, levels = month.abb)

# Visualization with ggplot
(quin_pup = ggplot(season.pup, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "Pu/P (mm)",
         title = "Precipitation / Pumping Ratio") +
    season.theme() +
    season.theme() +
    scale_y_continuous(limits = c(0, 3)))

ggplotly(quin_pup)

# ggsave(filename = "pup.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Quinto Infl
```{r, warning= FALSE}
# select the column
infl <- quin.mon.data[,7]
# Combined Observations into data frame
df.infl <- tibble(date, infl)

season.infl <- df.infl %>%
  # Convert data frame from lwide format to long format
  gather(season.infl, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.infl = str_replace(season.infl, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.infl$Month = factor(season.infl$Month, levels = month.abb)

# Visualization with ggplot
(quin_infl = ggplot(season.infl, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "Infl (mm)",
         title = "Infiltration") +
    season.theme() +
    season.theme() + 
  scale_y_continuous(limits = c(0, 100)))

ggplotly(quin_infl)

# ggsave(filename = "pu.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Quinto dS
```{r, warning= TRUE}
# select the column
dS <- quin.mon.data[,8]
# Combined Observations into data frame
df.dS <- tibble(date, dS)

season.dS <- df.dS %>%
  # Convert data frame from lwide format to long format
  gather(season.dS, value, -date) %>%
  # Remove "obs" in the Observation column
  mutate(season.dS = str_replace(season.dS, "date", "")) %>%
  # Convert the DATE column to date class
  mutate(DATE = ymd(date)) %>%
  # Create Month column
  mutate(Month = month(date)) %>%
  # Create Season column
  mutate(Month = case_when(
    Month %in% c(1)      ~ "Jan",
    Month %in% c(2)       ~ "Feb",
    Month %in% c(3)       ~ "Mar",
    Month %in% c(4)     ~ "Apr",
    Month %in% c(5)      ~ "May",
    Month %in% c(6)       ~ "Jun",
    Month %in% c(7)       ~ "Jul",
    Month %in% c(8)     ~ "Aug",
    Month %in% c(9)      ~ "Sep",
    Month %in% c(10)       ~ "Oct",
    Month %in% c(11)       ~ "Nov",
    Month %in% c(12)     ~ "Dec",
    TRUE                        ~ NA_character_
  ))

# Convert to factor
season.dS$Month = factor(season.dS$Month, levels = month.abb)

# Visualization with ggplot
(quin_dS = ggplot(season.dS, aes(x = Month, y = value,
                      fill = Month, 
                      class = mon_class)) +
    # Specify the geom to be boxplot
    geom_boxplot() +
    labs(x = "Month",
         y = "dS (mm)",
         title = "Change in Water Storage") +
    season.theme() +
    season.theme())

ggplotly(quin_dS)

# ggsave(filename = "pu.png",
#        dpi = 1200, height = 4,
#        width = 8,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
```
## Seasonal AVG ----
## Hydroclimate 
```{r}
wat_bal_san$mont = factor(wat_bal_san$Mon, levels = month.abb)

(san_avg <- ggplot(wat_bal_san)+
  geom_line(aes(mont,  P, group = 1, color = "P"),
            size = 1) +
  geom_line(aes(mont,  AET, group = 1, color = "AET"),
            size = 1) +
  geom_line(aes(mont,  PET, group = 1, color = "PET"),
            size = 1) +
  scale_color_manual(name = "mm/month",
                     values=c("P" = "307FE2",
                              "AET" = "1E3F63", 
                              "PET" ="steelblue")) +
  labs(x = "Months",
       y = "Average Value",
       title = "Hydroclimatic Components") +
  avg.theme() + 
  annotate("text", x = c(2,7, 11.5),
           y = c(28,100, 35),
           label = c("Surpus\nP > AET", "Deficit\nPET > AET", "Surplus\nP > AET"), size = 3))
```

## San Vitale
```{r}
(ggplot(wat_bal_san)+
  geom_line(aes(mont,  P, group = 1, color = "Precipitation"),
            size = 1) +
  geom_line(aes(mont,  Pu, group = 1, color = "Pumping"),
            size = 1) +
  geom_line(aes(mont,  Infl, group = 1, color = "Infiltration"),
            size = 1)+
  geom_line(aes(mont,  dS, group = 1, color = "dS_in_Storage"),
            size = 1)+
  scale_color_manual(name = "mm/month",
                     values=c("Precipitation" = "307FE2",
                              "Pumping" = "1E3F63", 
                              "Infiltration" ="steelblue",
                              "dS_in_Storage" = "#FAAB18")) +
  avg.theme() +
  labs(x = "Months", 
       y = "Average Value", 
       title = "San Vitale Basin",
       tag = "")+
  annotate(geom = "segment", 
           x = c(10.5,4,8), 
           xend = c(11,3.5,9.5), 
           y = c(45,70, 80), 
           yend = c(20,35, 70), 
           arrow = arrow(type = "closed",
                         length = unit(0.10, "inches")))+
  geom_label(aes(x = 10.5, y = 45, 
                 label = "high water table"),
             hjust = 0.5,
             vjust = 0.5,
             fill = "white", 
             label.size = NA,
             size = 4)+
  geom_label(aes(x = 4, y = 70, 
                 label = "deficit onset"),
             hjust = 0.5,
             vjust = 0.5,
             fill = "white", 
             label.size = NA,
             size = 4)+
  geom_label(aes(x = 7, y = 80, 
                 label = "high rainfall"),
             hjust = 0.5,
             vjust = 0.5,
             fill = "white", 
             label.size = NA,
             size = 4)+
  geom_hline(yintercept = 0, 
             linetype="dashed"))
```
## Rasponi
```{r}
wat_bal_ras$mont = factor(wat_bal_ras$Mon, levels = month.abb)

ggplot(wat_bal_ras)+
  geom_line(aes(mont,  P, group = 1, color = "Precipitation"),
            size = 1) +
  geom_line(aes(mont,  Pu, group = 1, color = "Pumping"),
            size = 1) +
  geom_line(aes(mont,  Infl, group = 1, color = "Infiltration"),
            size = 1)+
  geom_line(aes(mont,  dS, group = 1, color = "dS_in_Storage"),
            size = 1)+
  scale_color_manual(name = "mm/month",
                     values=c("Precipitation" = "307FE2",
                              "Pumping" = "1E3F63", 
                              "Infiltration" ="steelblue",
                              "dS_in_Storage" = "#FAAB18")) +
  avg.theme() +
  labs(x = "Months", 
       y = "Average Value", 
       title = "Rasponi Basin",
       tag = "")+
  annotate(geom = "segment", 
           x = c(10.5,4,8), 
           xend = c(11,3.5,9.5), 
           y = c(55,35, 80), 
           yend = c(25,25, 70), 
           arrow = arrow(type = "closed",
                         length = unit(0.10, "inches")))+
  geom_label(aes(x = 10, y = 55, 
                 label = "high water table"),
             hjust = 0.5,
             vjust = 0.5,
             fill = "white", 
             label.size = NA,
             size = 4)+
  geom_label(aes(x = 4, y = 38, 
                 label = "deficit onset"),
             hjust = 0.5,
             vjust = 0.5,
             fill = "white", 
             label.size = NA,
             size = 4)+
  geom_label(aes(x = 7, y = 80, 
                 label = "high rainfall"),
             hjust = 0.5,
             vjust = 0.5,
             fill = "white", 
             label.size = NA,
             size = 4)+
  geom_hline(yintercept = 0, 
             linetype="dashed")
```
## Quinto
```{r}
wat_bal_quin$mont = factor(wat_bal_quin$Mon, levels = month.abb)

ggplot(wat_bal_quin)+
  geom_line(aes(mont,  P, group = 1, color = "Precipitation"),
            size = 1) +
  geom_line(aes(mont,  Pu, group = 1, color = "Pumping"),
            size = 1) +
  geom_line(aes(mont,  Infl, group = 1, color = "Infiltration"),
            size = 1)+
  geom_line(aes(mont,  dS, group = 1, color = "dS_in_Storage"),
            size = 1)+
  scale_color_manual(name = "mm/month",
                     values=c("Precipitation" = "307FE2",
                              "Pumping" = "1E3F63", 
                              "Infiltration" ="steelblue",
                              "dS_in_Storage" = "#FAAB18")) +
  avg.theme() + 
  labs(x = "Months", 
       y = "Average Value", 
       title = "Quinto Basin",
       tag = "")+
  annotate(geom = "segment", 
           x = c(10.5,4,8), 
           xend = c(11,3.5,9.5), 
           y = c(55,35, 80), 
           yend = c(30,25, 70), 
           arrow = arrow(type = "closed",
                         length = unit(0.10, "inches")))+
  geom_label(aes(x = 10, y = 55, 
                 label = "high water table"),
             hjust = 0.5,
             vjust = 0.5,
             fill = "white", 
             label.size = NA,
             size = 4)+
  geom_label(aes(x = 4, y = 42, 
                 label = "high pumping"),
             hjust = 0.5,
             vjust = 0.5,
             fill = "white", 
             label.size = NA,
             size = 4)+
  geom_label(aes(x = 7, y = 80, 
                 label = "high rainfall"),
             hjust = 0.5,
             vjust = 0.5,
             fill = "white", 
             label.size = NA,
             size = 4)+
  geom_hline(yintercept = 0, 
             linetype="dashed")
```

## Water Budget ----
## San Vitale
```{r}
df1 <- melt(water_budget[,c("Sub_period",
                   "Pump_SanVitale","Actual_ET", 
                   "Precipitation", "Seep_SanVitale")], id.vars = 1)


(plt1 <- ggplot(df1,
                aes(x = Sub_period,
                    y =value)) + 
      geom_bar(aes(fill = variable), 
           stat = "identity", 
           position = "dodge",
           width = 0.8) + 
      geom_line() +
      labs(x = "Sub-period",
           y = "Δmm",
           title = "Change in San Vitale Water Budget") +
      scale_fill_brewer(palette="Reds") +
      budget.theme() +
      annotate(geom = "text", 
           x = c(1,2,3, 4, 5),  
           y = c(200,200, -200, 200,200), 
           label = c("",
                     "",
                     "",
                     "",
                     ""),
           size = 3) +
     geom_hline(yintercept = 0)+
     geom_vline(xintercept = 1.5))
```

## Rasponi 
```{r}
df2 <- melt(water_budget[,c("Sub_period",
                   "Pump_Rasponi","Actual_ET", 
                   "Precipitation", "Seep_Rasponi")], id.vars = 1)


(plt2 <- ggplot(df2,
                aes(x = Sub_period,
                    y =value)) + 
      geom_bar(aes(fill = variable), 
           stat = "identity", 
           position = "dodge",
           width = 0.8) + 
      geom_line() +
      labs(x = "Sub-period",
           y = "Δmm",
           title = "Change in Rasponi Water Budget") +
      scale_fill_brewer(palette="Reds") +
      budget.theme() +
      annotate(geom = "text", 
           x = c(1,2,3, 4, 5),  
           y = c(200,200, -200, 200,200), 
           label = c("",
                     "",
                     "",
                     "",
                     ""),
           size = 3) +
     geom_hline(yintercept = 0)+
     geom_vline(xintercept = 1.5))
```

## Quinto
```{r}
df3 <- melt(water_budget[,c("Sub_period",
                   "Pump_Quinto","Actual_ET", 
                   "Precipitation", "Seep_Quinto")], id.vars = 1)


(plt3 <- ggplot(df3,
                aes(x = Sub_period,
                    y =value)) + 
      geom_bar(aes(fill = variable), 
           stat = "identity", 
           position = "dodge",
           width = 0.8) + 
      geom_line() +
      labs(x = "Sub-period",
           y = "Δmm",
           title = "Change in Quinto Water Budget") +
      scale_fill_brewer(palette="Reds") +
      budget.theme() +
      annotate(geom = "text", 
           x = c(1,2,3, 4, 5),  
           y = c(200,200, -200, 200,200), 
           label = c("",
                     "",
                     "",
                     "",
                     ""),
           size = 3) +
     geom_hline(yintercept = 0)+
     geom_vline(xintercept = 1.5))
```

## Drought Index ----
## SPI
```{r, warning= FALSE}
# Concert date to POSXCIT
newyear <- parse_date_time(spi.data[,1], "by")
dates <- as.Date.POSIXct(newyear)

Drought_pos1 <- ifelse(spi.data$spi <= 0, 0, spi.data$spi)
Drought_neg1 <- ifelse(spi.data$spi >= 0, 0, spi.data$spi)

df <- tibble(spi.data, Drought_neg1, Drought_pos1, dates)

(drought.spi <- ggplot(df) +
    geom_area(aes(dates, Drought_pos1, fill = "wetter_period")) +
    geom_area(aes(dates, Drought_neg1, fill = "drier_period")) +
    labs(x = "Years", 
       y = "Value", 
       title = "Standardized Precipitation Index, SPI") +
    drought.theme() +
     scale_x_date(date_breaks = "5 year",
               date_minor_breaks = "5 year",
               date_labels = "%Y") +
    scale_fill_manual("SPI", 
                    values=c(wetter_period ="blue", 
                             drier_period="red")))

# ggsave(filename = "img.spi.png",
#        dpi = 1200, height = 4,
#        width = 10,
#        path = "D:/Bologna Project/Land_Reclamation_Excel_Data/Plot")
# dev.off()
  
#+scale_color_manual(" ", values=c(wetter="blue", drier="red")) 
```
## SPEI
```{r}
newyear = parse_date_time(Drought_Index[,1], "by")
dates = as.Date.POSIXct(newyear)

#spei index
Drought1 = Drought_Index
Drought_pos <- ifelse(Drought1$spei <= 0, 0, Drought1$spei)  

Drought2 = Drought_Index
Drought_neg <- ifelse(Drought2$spei >= 0, 0, Drought2$spei) 

df <- cbind.data.frame(Drought_Index, Drought_neg, Drought_pos)

(drought.spei = ggplot(df) + 
    geom_area(aes(dates, Drought_pos,
                  fill = "wetter_period"),
                  alpha = 1) +
    geom_area(aes(dates, Drought_neg,
                  fill = "drier_period"), alpha = 1) +
    labs(x = "Years", 
       y = "Value", 
       title = "Standardized Precipitation Evaporation Index, SPEI") +
    drought.theme() +
    scale_fill_manual(" ", 
                    values=c(wetter_period ="blue", 
                     drier_period="red", 
                     alpha = 0.5)))  
```

## Subsidence ----
```{r}
## Columns of Subsidence
 Q <- subsidence[,2]
 R <- subsidence[,3]
 S <- subsidence[,4]
## Adding up in cumulative value
 Q <- cumsum(subsidence$Q) 
 R <- cumsum(subsidence$R)
 S <- cumsum(subsidence$S)
year <- subsidence$Year
## Combined into data frame
 df <- cbind.data.frame(Q, R, S, year)
 head(df)
(p1 = ggplot(df)+ 
      geom_line(aes(year, 
                    Q, 
                    color = "Quinto_basin"), 
                    size = 3 ) +
      geom_line(aes(year, 
                    S, 
                    color = "S.Vitale_basin"), 
                    size = 3 ) +
  geom_line(aes(year, 
                R, 
                color = "Rasponi_basin"), 
                size = 3 )+
  labs(x = "Year", 
       y = "subsidence", 
       title = "Cummulative Subsidence; 1972 to 2016") +
       values=c("Quinto_basin" = "307FE2", 
                              "S.Vitale_basin" ="steelblue",
                              "Rasponi_basin" = "#FAAB18")) +
  theme(axis.title = element_text(face = "bold", 
                                  size = 12),
                panel.border = element_rect(size = 1.5),
                axis.text = element_text(face = "bold", 
                                         size = 12),
                panel.grid = element_blank(),
                plot.title = element_text(hjust=0.5, 
                                          size = 16),
                legend.justification=c(0.8,0.2),
                legend.position=c(0.95,0.1)) +
  annotate(geom = "segment",
           x = 1979, 
           xend = 1974, 
           y = 200, 
           yend = 200,
           arrow = arrow(type = "closed",
           length = unit(0.10, "inches"))) +
  geom_label(aes(x = 1980, y = 200, 
                 label = "Anthropogenic"),hjust = 0.5,
                 vjust = 0.5,  
                 fill = "white", label.size = NA,
                size = 4)
p1

```











