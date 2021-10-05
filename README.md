# spfl-analytics
#### Datasets and R tutorials for making visualisations of Scottish football


This is just a page where I'll post the code I use to create the various data visualisations I share on my Twitter (same @ as here) and share some of the datasets I've collected. I learned the basics of these plot styles from FC R Stats but have put my own twist on them with colour schemes, annotations etc. so hopefully you find something useful in here.



## Player xG v xA Scatterplot

This was the first plot I learned to code in R and is probably the most common you'll see on Football Analytics Twitter. Modern Fitba have kindly allowed me to share their dataset from the 20/21 Scottish Premiership season

[Modern_Fitba_Player_2021.csv](https://github.com/cunningcolin/spfl-analytics/files/7009842/Modern_Fitba_Player_2021.csv)


I start my session by loading the following packages (some of them may be overkill!) and then the CSV. You'll need to save this down to your machine then copy and paste your folder address into the brackers after _file="_)


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

Prem <- read.csv(file="C:/Users/User/Desktop/R Code & Graphics/Modern Fitba/Prem_202021.csv", header=TRUE, sep=",")
```
<br />
Now I upload the logos that I'll use later to watermark my plot (again you'll need to save these down then copy and paste your folder address into the brackers after _readPNG_) <br />
<br />
<img src="https://user-images.githubusercontent.com/87502071/129967459-0e827676-190d-47b5-973e-5464af84685d.png" height="100">      <img src="https://user-images.githubusercontent.com/87502071/129967192-066ebb98-aa2c-4d05-b1cc-818a60fc684b.png" height="50">



```
img <- png::readPNG("C:/Users/User/Desktop/R Code & Graphics/Modern Fitba/ortecsports.png")
rast <- grid::rasterGrob(img, interpolate = T)

img1 <- png::readPNG("C:/Users/User/Desktop/R Code & Graphics/Modern Fitba/modern-fitba.png")
rast1 <- grid::rasterGrob(img1, interpolate = T)
```


<br />
That dataset contains a lot of columns so I use the data.frame function to select the ones I'm interested in and then subset to remove any players with less than 900 minutes played and who aren't midfielders or forwards


 
```
df <- data.frame(Prem$Player.Name, Prem$Expected.Assists..p90, Prem$Expected.Goals.p90..exl.pens., Prem$Team, Prem$Total.minutes, Prem$Position.Group, Prem$Total.Goals - Prem$Penalty.Goals - Prem$Expected.Goals...exl.pens.)
df <- subset(df, Prem.Total.minutes > 900 & (Prem.Position.Group == "Centre Midfield" | Prem.Position.Group == "Striker" | Prem.Position.Group == "Winger/Wide Midfield" ))
```

Colour pallette for the teams in alphabetical order

```
Prem_colors <- c("#E2001A", "#16973B", "#f29400", "#CC383F", "#005000", "#2F368F", "#ffcc00", "#FFBE00", "#0E00F7", "#040957", "#243F90", "Black")   
names(Prem_colors) <- levels(factor(c(levels(df$Prem.Team))))
```

Now I create the scatterplot:
- x and y inside the _aes()_ are my xG and xA
- _color_ and _label_ are self-explanatory
- _geom_point_ is the style and shape of the points 
- _geom_abline_ is just an x=y line to highlight players that have an equal spread between the two stats
- _scale_x_continuous_ is the axis title and the limits

```
Plot <- ggplot(data = df, aes(x=Prem.Expected.Goals.p90..exl.pens., y=Prem.Expected.Assists..p90, color=Prem.Team, label=Prem.Player.Name))+ 
geom_point(size=3, shape=20) +
geom_abline(intercept = 0, linetype = 2, colour = "darkblue") + 
scale_x_continuous(name = "Non-penalty Expected Goals per 90", limits = c(0, 0.61)) +
scale_y_continuous(name = "Expected Assists per 90", limits = c(0, 0.60)) + 
theme_bw() +
scale_colour_manual(name = df$Prem.Team, values = Prem_colors)
```


<img src="https://user-images.githubusercontent.com/87502071/136096009-1194e347-8d30-46b2-ab94-ebce48458fee.png" height="500">

That's a basic scatter but what about jazzing it up:
- Create a subset for outliers and label them using _geom_label_repel_
- Add in the watermarks using _annotation_custom_ (I find the positioning and sizing to be trial and error)
- _ggtitle_ for a header

```
Plot1 <- Plot + geom_label_repel(data=df1, 
                  box.padding   = 0.15, 
                  point.padding = 0.2,
		  #nudge_y = 0.1,
		  #nudge_x = 0.1,
                  size          = 5,
                  force         = 15,
show.legend=FALSE,
                  segment.size  = 0.2,
                  segment.color = "grey50") + theme(legend.position="bottom") + annotation_custom(rast, ymin = 0.47, ymax = 0.62, xmin = 0, xmax =0.2) + 
annotation_custom(rast1, ymin=0.4, ymax = 0.5, xmin = -0.05, xmax =0.1) + 
ggtitle("Expected goal contributions in Scottish Premiership 2020/21") +
theme(plot.title=element_text(size=18, colour="black", hjust=0), axis.title=element_text(size=16)) +
theme(legend.title = element_text(colour="white"), legend.text = element_text(size=14)) +
theme(axis.text.x = element_text(size=14), axis.text.y = element_text(size=14))
```

<img src="https://user-images.githubusercontent.com/87502071/136097733-f79fe5b3-7fc0-4cff-b65b-f482de4802be.png" height="500">


Once you're happy the last thing to do is save!

```
ggsave("Prem xG.png", plot = last_plot(), path="C:/Users/User/Desktop/R Code & Graphics/Modern Fitba/", height=9, width=16)
```







