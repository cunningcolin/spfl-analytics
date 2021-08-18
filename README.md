# spfl-analytics
Datasets and R tutorials for making visualisations of Scottish football


This is just a page where I'll post the code I use to create the various data visualisations I share on my Twitter (same @ as here) and share some of the datasets I've collected. I learned the basics of these plot styles from FC R Stats but have put my own twist on them with colour schemes, annotations etc. so hopefully you find something useful in here.



## Player xG v xA Scatterplot

This was the first plot I learned to code in R and is probably the most common you'll see on Football Analytics Twitter. Modern Fitba have kindly allowed me to share their dataset from the 20/21 Scottish Premiership season

[Modern_Fitba_Player_2021.csv](https://github.com/cunningcolin/spfl-analytics/files/7009842/Modern_Fitba_Player_2021.csv)



I start my session by loading the following packages, although some of them may be overkill

```
library(data.table)
library(dplyr)
library(formattable)
library(tidyr) 
library(htmltools)
library(webshot)
library(knitr)
library(kableExtra) 
library(ggplot2)
library(ggrepel)
library(ggpubr)
```

Now I add the logos that I'll use later to watermark my plot (you'll need to update the address to wherever you save these on your machine)

<img src="https://user-images.githubusercontent.com/87502071/129967192-066ebb98-aa2c-4d05-b1cc-818a60fc684b.png" width="100">



```
img <- png::readPNG("C:/Users/User/Desktop/R Code & Graphics/Modern Fitba/ortecsports.png")
rast <- grid::rasterGrob(img, interpolate = T)

img1 <- png::readPNG("C:/Users/User/Desktop/R Code & Graphics/Modern Fitba/modern-fitba.png")
rast1 <- grid::rasterGrob(img1, interpolate = T)
```







