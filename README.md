# Exploratory Data Analysis Final Project
 
##Exploring Trends in PM2.5 Emissions in the United States from 1999 to 2008
 
 
Fine particulate matter (PM2.5) is an ambient air pollutant for which there is strong evidence that it is harmful to human health. In the United States, the Environmental Protection Agency (EPA) is tasked with setting national ambient air quality standards for fine PM and for tracking the emissions of this pollutant into the atmosphere. Approximately every 3 years, the EPA releases its database on emissions of PM2.5. This database is known as the National Emissions Inventory (NEI). There is more information about the NEI at the EPA National Emissions Inventory web site.

For each year and for each type of PM source, the NEI records how many tons of PM2.5 were emitted from that source over the course of the entire year. The data used for this assignment are from 1999, 2002, 2005, and 2008.

The data for this assignment were taken from the course website. There are two files, which are both included in this repo:

PM2.5 Emissions Data (summarySCC_PM25.rds): This file contains a data frame with all of the PM2.5 emissions data for 1999, 2002, 2005, and 2008. For each year, the table contains the number of tons of PM2.5 emitted from a specific type of source for the entire year. Here are the first few rows.

```R
     fips      SCC Pollutant Emissions  type year
 4  09001 10100401  PM25-PRI    15.714 POINT 1999
 8  09001 10100404  PM25-PRI   234.178 POINT 1999
 12 09001 10100501  PM25-PRI     0.128 POINT 1999
 16 09001 10200401  PM25-PRI     2.036 POINT 1999
 20 09001 10200504  PM25-PRI     0.388 POINT 1999
 24 09001 10200602  PM25-PRI     1.490 POINT 1999
```

`fips`: A five-digit number (represented as a string) indicating the U.S. county

`SCC`: The name of the source as indicated by a digit string (see source code classification table)

`Pollutant`: A string indicating the pollutant

`Emissions`: Amount of PM2.5 emitted, in tons

`type`: The type of source (point, non-point, on-road, or non-road)

`year`: The year of emissions recorded

Source Classification Code Table (Source_Classification_Code.rds): This table provides a mapping from the SCC digit strings in the Emissions table to the actual name of the PM2.5 source. The sources are categorized in a few different ways from more general to more specific. For example, source “10100101” is known as “Ext Comb /Electric Gen /Anthracite Coal /Pulverized Coal”.

Each of the two files were read using the `readRDS()` function in R. Since there are nearly two million observations in the PM2.5 Emissions Summary file, it does take some time for the data to be read into R. 

###The overall goal of this project is to explore the National Emissions Inventory database and see what it says about fine particulate matter pollution in the United states over the 10-year period 1999–2008. 

To run any of these scripts, the user must have both data files in the working directory. The files are available in this repo.

Question 1. Have total emissions from PM2.5 decreased in the United States from 1999 to 2008? Using the base plotting system, make a plot showing the total PM2.5 emission from all sources for each of the years 1999, 2002, 2005, and 2008.

![plot1](https://github.com/carahubbell/ExploratoryDataAnalysisFinalProject/blob/master/plot1.png)

This graph shows the mean PM2.5 emissions measured in tons over the years 1999, 2002, 2005, and 2008 in the United States. Here, we consider all observations (from all sites and for all types) of PM2.5 emissions within each of the years. We use the mean to control for the differences in numbers of observations for each year. Thus, we can conclude that the total PM2.5 emissions has decreased from 1999 to 2008. The following code generates this plot:

```R
# Read the data into R.
nei<-readRDS("summarySCC_PM25.rds")

#Calculate means of Emissions by year
x<-tapply(nei$Emissions, nei$year, mean)

#Create the plot in a png file
png("plot1efficient.png", width=480, height=480, units="px")

barplot(x, col=rainbow(4, s = 1, v = 1, start = .5, end = .7, alpha = 1))

title(main="U.S. Mean PM2.5 Emissions by Year (Tons)", xlab="Year", 
      ylab="Mean PM2.5 Emissions (Tons)")

dev.off()
```

Question 2. Have total emissions from PM2.5 decreased in the Baltimore City, Maryland (fips == "24510") from 1999 to 2008? Use the base plotting system to make a plot answering this question.

![plot2](https://github.com/carahubbell/ExploratoryDataAnalysisFinalProject/blob/master/plot2.png)

This graph shows the mean PM2.5 emissions measured in tons over the years 1999, 2002, 2005, and 2008 in Baltimore City, Maryland. Here, we consider all observations in Baltimore City of PM2.5 emissions within each of the years. We use the mean to control for the differences in numbers of observations for each year. Thus, we can conclude that the total PM2.5 emissions in Baltimore City has decreased from 1999 to 2008. The following code generates this plot:

```R
# Read the data into R.
require(dplyr)
nei<-readRDS("summarySCC_PM25.rds")

#Take only data from Baltimore City
data<-filter(nei, fips=="24510")

#Calculate the mean of Emissions by year
x<-tapply(data$Emissions, data$year, mean)

#Create the plot in a png file
png("plot2.png", width=480, height=480, units="px")

barplot(x, col=rainbow(4, s = 1, v = 1, start = .21, end = .43, alpha = 1))

title(main="Baltimore City Mean PM2.5 Emissions (Tons)", xlab="Year", 
      ylab="Mean PM2.5 Emissions (Tons)")

dev.off()
```

Question 3. Of the four types of sources indicated by the type (point, nonpoint, onroad, nonroad) variable, which of these four sources have seen decreases in emissions from 1999–2008 for Baltimore City? Which have seen increases in emissions from 1999–2008? Use the ggplot2 plotting system to make a plot answer this question.

![plot3](https://github.com/carahubbell/ExploratoryDataAnalysisFinalProject/blob/master/plot3.png)

This graph shows the total PM2.5 emissions measured in tons over the years 1999, 2002, 2005, and 2008 in Baltimore City, Maryland by source type (point, nonpoint, onroad, nonroad). From the graph, we can see that the total PM2.5 emissions in Baltimore City has decreased for three of the four source types (nonroad, nonpoint, and onroad) over the given time period, but increased for the point source type. The following code generates this plot:

```R
# Read the data into R.
require(dplyr)
nei<-readRDS("summarySCC_PM25.rds")
scc<-readRDS("Source_Classification_Code.rds")

#Only consider data from Baltimore City, MD
data<-filter(nei, fips=="24510")

#Create the plot in a png file
png("plot3.png", width=480, height=480, units="px")

g<-ggplot(data,aes(factor(year),Emissions,fill=type)) +
  geom_bar(stat="identity") +
  scale_fill_manual(values=c("#330006", "#660033", "#660066", "#990033")) +
  theme_bw() + guides(fill=FALSE)+
  facet_grid(.~type,scales = "free",space="free") + 
  xlab("Year") +
  ylab("Total PM2.5 Emission (Tons)") + 
  ggtitle("PM2.5 Emissions in Baltimore City by Source Type")

print(g)

dev.off()
```

Question 4. Across the United States, how have emissions from coal combustion-related sources changed from 1999–2008?

![plot4](https://github.com/carahubbell/ExploratoryDataAnalysisFinalProject/blob/master/plot4.png)

This graph shows the total PM2.5 emissions from all coal combustion-related sources measured in tons over the years 1999, 2002, 2005, and 2008 in the United States. From the graph, we can see that the total PM2.5 emissions from coal combustion sources has decreased over the given time period. The following code generates this plot:

```R
# Read the data into R. 
nei<-readRDS("summarySCC_PM25.rds") 
scc<-readRDS("Source_Classification_Code.rds") 

#Only consider data from coal combustion-related sources 
combustion <- grepl("comb", scc$SCC.Level.One, ignore.case=TRUE) 
coal <- grepl("coal", scc$SCC.Level.Four, ignore.case=TRUE) 

combcoal <- (combustion & coal) 
combcoalscc <- scc[combcoal,]$SCC 
combcoalnei <- nei[nei$SCC %in% combcoalscc,] 

x<-tapply(combcoalnei$Emissions, combcoalnei$year, sum)

#Create the plot in a png file 
png("plot4.png", width=480, height=480, units="px") 

barplot(x, col=rainbow(4, start = 1, end = .15, alpha = 1)) 

title(main="Total PM2.5 Emissions from Coal Combustion Sources", 
      xlab="Year", ylab="Total PM2.5 Emissions (Tons)") 

dev.off()
```

Question 5. How have emissions from motor vehicle sources changed from 1999–2008 in Baltimore City?

![plot5](https://github.com/carahubbell/ExploratoryDataAnalysisFinalProject/blob/master/plot5.png)

This graph shows the total PM2.5 emissions from all vehicle sources measured in tons over the years 1999, 2002, 2005, and 2008 in the Baltimore City, Maryland. From the graph, we can see that the total PM2.5 emissions from vehicle sources has decreased over the given time period. The following code generates this plot:

```R
# Read the data into R.
require(dplyr)
nei<-readRDS("summarySCC_PM25.rds")
scc<-readRDS("Source_Classification_Code.rds")

#Only consider Baltimore City, MD
baltimore<-filter(nei, fips=="24510")

#Consider all motor vehicle sources in Baltimore City
vehicle <- grepl("vehicle", scc$SCC.Level.Two, ignore.case=TRUE)
vehiclescc <- scc[vehicle,]$SCC
vehiclebc <- baltimore[baltimore$SCC %in% vehiclescc,]

x<-tapply(vehiclebc$Emissions, vehiclebc$year, sum)

#Create the plot in a png file
png("plot5.png", width=480, height=480, units="px")

barplot(x, col=rainbow(4, s = 1, v = 1, start = .98, end = .08, alpha = 1))

title(main="Baltimore City Total PM2.5 Emissions from Vehicles (Tons)", xlab="Year", 
      ylab="Total Vehicle PM2.5 Emissions (Tons)")

dev.off()
```

Question 6. Compare emissions from motor vehicle sources in Baltimore City with emissions from motor vehicle sources in Los Angeles County, California (fips == "06037"). Which city has seen greater changes over time in motor vehicle emissions? 

![plot6](https://github.com/carahubbell/ExploratoryDataAnalysisFinalProject/blob/master/plot6.png)

This graph shows the total PM2.5 emissions from all vehicle sources measured in tons over the years 1999, 2002, 2005, and 2008 in Baltimore City, Maryland and in Los Angeles, CA. From the graph, we can see that the total PM2.5 emissions from vehicle sources has decreased over the given time period in Baltimore City, but has increased with time in Los Angeles. The following code generates this plot:

```R
# Read the data into R.
require(dplyr)
require(RColorBrewer)
nei<-readRDS("summarySCC_PM25.rds")
scc<-readRDS("Source_Classification_Code.rds")

#Consider Baltimore City, MD and Los Angeles, CA
baltimore<-filter(nei, fips=="24510")
losangeles<-filter(nei, fips=="06037")

#Consider all motor vehicle sources only
vehicle <- grepl("vehicle", scc$SCC.Level.Two, ignore.case=TRUE)
vehiclescc <- scc[vehicle,]$SCC

vehiclebc <- baltimore[baltimore$SCC %in% vehiclescc,]
vehiclela <- losangeles[losangeles$SCC %in% vehiclescc,]

#Calculate the total for each year
j<-tapply(vehiclebc$Emissions, vehiclebc$year, sum)

k<-tapply(vehiclela$Emissions, vehiclela$year, sum)

#Create the plot in a png file
png("plot6.png", width=480, height=480, units="px")

par(mfrow=c(1,2), mar=c(5, 4, 4, 2), oma=c(0, 0, 2, 0))

barplot(j, col=brewer.pal(8,"PRGn"), ylim=c(0,7500))

title(main="Baltimore City", xlab="Year", 
      ylab="Total Vehicle PM2.5 Emissions (Tons)")

barplot(k, col=brewer.pal(8,"PRGn"), ylim=c(0,7500))

title(main="Los Angeles", xlab="Year", 
      ylab="Total Vehicle PM2.5 Emissions (Tons)")

mtext("Total Vehicle PM2.5 Emissions from 1999-2008", side=3, outer=TRUE, cex=1.5)

dev.off()
```


###Limitations and Conclusions 
For much of the analysis, means were used to investigate patterns over time. However, the data are extremely skewed right. Using the `skewness()` function from the `e1071` package, we see that:

```R
> skewness(data1999$Emissions)
[1] 231.9183
> skewness(data2002$Emissions)
[1] 1198.29
> skewness(data2005$Emissions)
[1] 292.4633
> skewness(data2008$Emissions)
[1] 159.9864
```
where `data1999`, etc. represents all data in the NEI file from the year 1999, etc. We also see that:

```R
> summary(data1999$Emissions)
    Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
    0.00     0.01     0.04     6.62     0.26 66700.00 
> summary(data2002$Emissions)
    Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
     0.0      0.0      0.0      3.3      0.1 647000.0 
> summary(data2005$Emissions)
    Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
    0.00     0.00     0.01     3.18     0.07 58900.00 
> summary(data2008$Emissions)
     Min.   1st Qu.    Median      Mean   3rd Qu.      Max. 
    0.000     0.000     0.005     1.753     0.062 20800.000 
```

It would appear that means are not the best measure to analyze trends over time because of the fact that we have such skewed data. Another measure to consider would be the sum of all emissions observations within each of the years. However, we can see that:

```R
> length(data1999$Emissions)
[1] 1108469
> length(data2002$Emissions)
[1] 1698677
> length(data2005$Emissions)
[1] 1713850
> length(data2008$Emissions)
[1] 1976655
```

This shows that there are 1.8 times as many observations in 2008 as there were in 1999. This is due to the fact that more source types were included across more counties over the years. Therefore, it does not seem reasonable to use sums to draw conclusions. It is because of this that we have chosen, in most cases, to use means over sums in an attempt to say something about PM2.5 emissions between 1999 and 2008. A better analysis would include comparison across years for each individual site and type of observation. However, that is beyond the scope of this project.

With this initial rough exploration of the EPA's PM2.5 data, we have shown that there is indeed a decrease in the overall PM2.5 emissions in the United States. 
