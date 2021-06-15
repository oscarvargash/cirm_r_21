# Tutorial 2, using Dplyr, basic stats, and loops

The power of coding relies heavily on being able to analyze large datasets with few lines of code. A good package to work with table-like datasets is dplyr. We will import a dataset of plant occurrences and then calculate several statistics on those.

## Filtering data and getting some basic statistics 

- First create a folder in your documents or desktop named `day_2`.

- Then download the following dataset and add it to the folder. The best way to do this is to access this github page from inside vlab.

[Abronia dataset](https://www.dropbox.com/s/z583pnzvyail7pr/abronia_2.csv?dl=0) 

- Now that you have `abronia_2.csv` inside the folder `day_2` go to Rstudio and open a new script.

- Save the new script as `abronia.r` inside the folder `day_2`

### Installing our first R package

R functions like a dock in which you can install packages for analyses. A very useful package for working with dataframes is dplyr.

```
install.packages("dplyr")
```

Now that the library is installed we can load it.
```
library(dplyr)
```

Now we need to tell R about your working folder. Look for the address of one of your files inside your `day_2` folder,

```
setwd("/Users/ov20/Desktop/Abronia")
```

We can always check our working directory by typing:
```
getwd()
dir()
```

Now we can import our dataset
```
data = read.csv("abronia_2.csv")
```

This dataframe is pretty large so there is no point in printing it all to the screen. To check that our dataset has been successfully imported, we can print to screen only the “head” of the dataset.
```
head(data)
```
Because this is a dataset with numerous columns, R will stack the rows on top of each other.

Similarly, we can print the bottom or tail of the dataset, asking R to print the 10 last rows.
```
tail(data, 10)
```
We can see here that we have more than 17 thousand records in our dataset.

We can also see the structure of the dataframe by doing:
```
str(data)
```

Our dataset contains museum and inaturalist records for all the species of sand verbenas (a charismatic plant that can be found at the dunes in Arcata). We are interested in studying the distribution of sand verbenas, and therefore we would like to know several statistics about the latitudinal range for the genus and species.

Since we are interested in describing the latitudinal range for these plants, a first calculation is to find the mean of the latitude.

```
mean(data$decimalLatitude)
```

Opps!!!!

We can see that there is something wrong with the command or the dataset, any ideas what can be wrong? Let's check the last 100 values of the Latitude:

```
tail(data$decimalLatitude, 100)
```

We can see that there are multiple rows without values “NAs”.  with dplyr we can easily filter out those. Let's create a new dataframe with only data that has latitude `geodata`.

```
geodata = data %>% filter(!is.na(decimalLatitude))
```
Dplyr uses the following syntax:

NEWDATASET = DATASET %>% FILTER

In our case, we want to keep numeral entries; for this we are using the function `filter` and the function `is.na`. If we were to use `data %>% filter(is.na(decimalLatitude))`, we will only keep the rows with NAs, which is the opposite of what we want. The character `!` means “is not”, asking R keep in the new dataframe everything that is not NA.


Let’s check that our dataset does not have NAs in the latitude column:

```
tail(geodata$decimalLatitude, 50)
```

Now let’s try to calculate the mean latitude for all species combined.
```
mean(data$decimalLatitude)
```

***NOTE*** There are always numerous ways of solving a problem while coding; another way of calculating the mean while ignoring the NAs is `mean(data$decimalLatitude, na.rm = T)`. Working with a clean dataset usually helps to run code more efficiently, so we will keep working with `geodata`.

We can also calculate other statistics on the latitude.

```
median(geodata$decimalLatitude)
min(geodata$decimalLatitude)
```

## Exercise. Using the filter `filter(species == "Species of interest")` (notice that this is just filter of the line necessary for the line). Find the maximum longitude for “Abronia latifolia”.



## Basic plotting and model testing

We can easily compare the latitudinal range among species using a boxplot.
```
boxplot(geodata$decimalLatitude ~ geodata$species, las = 2)
```

We will the dataset of Abronia latifolia (our local sand verbena) that you subsetted in the past exercise for plotting and fitting a linear model. Slide the bar to the right to see the answer for subsetting the data.

<details>
  <summary>Click to see an answer!</summary>
  
```
abla = geodata %>% filter(species == "Abronia latifolia")
```
  

</details>

We can start by plotting the latitude 
```
plot(abla$decimalLatitude)
```

We can see that when we plot only latitude our X axis represents the index number inside the vector created by calling only the latitude.

Another way of viewing our data is by creating a histogram of the latitude.

```
hist(abla$decimalLatitude)
```

In a similar fashion, we can inspect the longitude data

```
plot(abla$decimalLongitude)
hist(abla$decimalLongitude)
```

We can also plot latitude and longitude in a single plot, which in this case is pretty much a basic map.

```
plot(abla$decimalLongitude, abla$decimalLatitude)
```
What do you see?


Just for fun, we can test whether or not we can predict the latitude by the longitude. ***Note*** This test might not be appropriate because we are not sure if our data is normal. 
```
model1 = lm(abla$decimalLatitude ~ abla$decimalLongitude)
summary(model1)
```

We can see the fit of the model by plotting
```
plot(abla$decimalLongitude, abla$decimalLatitude)
abline(model1)
```


## Using loops 

So far it has taken us a bit of time to analyze just the data for Abronia latifolia. We can employ a loop to iterate a task over every species. So instead of writing 23 lines of code we write a couple of lines making our code more elegant. Let’s calculate the mean latitude for every species.

First we need to create a dataframe that contains the list of items we want to iterate over. In this case we need a list of Abronia species. We can use dplyr for this task:

```
Ab_sp = geodata %>% group_by(species) %>% tally()
```
This data frame contains the number of record per species. Notice that if you want to extract a vector that contains only species names you can do:

```
Ab_sp$species
```

This vector will be of great use for the loop, we can create a new vector called species in the following fashion:

```
spp = Ab_sp$species
```

Let’s remove the first element of `spp`, which are non-specific records.

```
spp = spp[2:23]
``` 

Here is an example of a simple loop that just prints every element in the list:

```
for (item in (spp)) {
	print(item)
}
```
Notice the syntax in the loop. And WOW!! How cool! You have run your first loop!!!!!!

***Exercise*** Add lines to the loop so it prints the average latitude for every species. Hint you need to add a line inside the loop to subset the data and a line to calculate the mean.

<details>
  <summary>Click to see an answer!</summary>
  
```
for (item in (spp)) {
	print(item)
	subset = geodata %>% filter(species == item)
	mn = mean(subset$decimalLatitude)
	print(mn)
}
```
  

</details>

***Optional exercise*** While the output to the screen is very useful, it will be better if we can store the output as a dataframe. Hint, you first need to create a dataframe that you will populate, then you will need that 

<details>
  <summary>Click to see an answer!</summary>
  
```
for (item in (spp)) {
	print(item)
	subset = geodata %>% filter(species == item)
	mn = mean(subset$decimalLatitude)
	print(mn)
}
```
  

</details>







