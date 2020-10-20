This repository contains homework and project ideas for Cultural Data Science 2020/21
Below, I will describe my current project idea. It involves mapping data from all refugees twho came to Denmark between 1990 and 2020. Furthermore, I would like to visualize this interactively on the world map.

The data is from StatBank Denmark and link to the specific data is provided here: https://www.statbank.dk/VAN5

Firstly, I downloaded the table as a .csv file before I loaded the necessary packages and data in R. In order to be able to work with the data some columns and rows were removed.

```r
#loading packages
pacman::p_load(tidyverse, reshape2, janitor, ggmap, maptools, MAP)
#read file
ASYLUM_DK <- read.csv("data/ASYLUM_DK_ENGLISH.csv", header=FALSE, comment.char="#")
#making the first row to column names
ASYLUM_DK <- row_to_names(ASYLUM_DK, row_number = 1)
```

Looking at the data now we see the a column for the countries where asylum seekers has citizenship and quantiles for every year from 1990-2020. 
I want to summarize the data within every year and change the format so there is an observation per year per row per country. I solve this by applying a loop that takes every fourth column and adds this together with the four subsequent columns and save it in a new column containing the first four digits from the other columns (ie. "2020Q1" becomes "2020"). Afterwards, the data is changed to "long format" where I specify that all columns from 1990-2020 should be saved in a new column called "year" and the data in every column in another one called "no_migrants". This results in the following dataframe: 

Looping through columns
```r
lis<- c(2, seq(from =5, to =118, by=4))

for (i in lis) {
  print(i)
  if(i == 2){
  new_col <- 
    as.numeric(as.character(ASYLUM_DK[,i])) + 
    as.numeric(as.character(ASYLUM_DK[,i+1])) + 
    as.numeric(as.character(ASYLUM_DK[,i+2]))
    name <- str_extract(colnames(ASYLUM_DK[i]), "[0-9]*")
    colnames(ASYLUM_DK)[length(ASYLUM_DK)] <- name
  
  }
  else{
  ASYLUM_DK[,length(ASYLUM_DK)+1] <- 
    as.numeric(as.character(ASYLUM_DK[,i])) + 
    as.numeric(as.character(ASYLUM_DK[,i+1])) + 
    as.numeric(as.character(ASYLUM_DK[,i+2])) + 
    as.numeric(as.character(ASYLUM_DK[,i+3]))
  name <- str_extract(colnames(ASYLUM_DK[i]), "[0-9]*")
  colnames(ASYLUM_DK)[length(ASYLUM_DK)] <- name
  
  }
}

```
Selecting only summed years and making it long format
```{r}
summed_asylum_dk <- ASYLUM_DK %>% 
  select(1, 122:length(ASYLUM_DK)) %>% 
  gather(year, no_migrants, -region)

```

```r
head(summed_asylum_dk)
```

We now have the following columns

`Country` | the citizenship of the asylum seekers | ---
--- | --- | ---
`Year` | The year asylum was sought | ---
`No_seek` | the number of asylum seekers | ---

`Country` | the citizenship of the asylum seekers | 
`Year` | The year asylum was sought | 
`No_seek` | the number of asylum seekers | 
