# Data Manipulation in R
*Created by [Beth Simms](mailto:s1724875@ed.ac.uk), The University of Edinburgh, 05/12/19*

## What is data manipulation and why do we use it?

Data Manipulation is an effective way to sort through, organise and clear data so that it is easier to work with later on in R. It merges relevent columns, removes unnesesary entries and can create new coumns of useful statistics for example. Further manipulation can be carried out such as selecting only certain aspects of the data that you want to focus in on, or creating summaries of specific variables. Data manipulation will make raw data set tidy and flexible . 


## Aims of this tutorial:

* To learn how to create a tidy data set. 
* To learn how to explore, organise, sort through and draw relevent information from a large and messy data set. 


## Step 1 - Getting started 

In this tutorial we will be working with data from a survey on rare plant species in the Anthabasca region, Alberta, Canada.The data can be downloaded from [this repository](https://github.com/EdDataScienceEES/tutorial-beth-simms/tree/master/Data) and saved on to your personal computer. Now open up R studio and make sure you are working in the correct repositpry (the current repository name is in the top right of the screen), open a new script and you are ready to begin!


**Packages to install and download for this tutorial**

You will need to install the following packages using the `install.packages("")` function if you havent already, then load them in using `library()`. 

* tidyr 
* dplyr 


## Step 2 - Downloading the dataset 

Download the data from where you saved it on your computer using the file path followed by the file name.The first line shown below will load the data in to your environment, then the you can the use the 'View' function, shown on the second line, to display the data in a new window. 

:pencil2: 
```r
rare_plants <- read.csv("~/Downloads/YEAR_3/tutorial-beth-simms/Data/ABMI_Rare_Plants_2012-2015_Data (1).csv")
View(rare_plants)
```


To return to the main script after viewing the data set, click on the script tab at the top left of the data frame.
Now the dataset is downloaded, you will see from just scrolling around it that it is enormous! Lets see if we can get an idea of what on earth it is all about!



## Step 3 - Eyeballing the dataset

Explore the premise of the data set, start with using the `head()` function, this will give you the name of the  columns and their first six entries. This will give you an idea of what sort of variables are compared and how many columns are in the dataset. 


:pencil2: 
```r
head(rare_plants)
```

The first 6 rows only show one site, so we can already see that this data is in depth. The column names are at the top of each of the lists of 6 observation entries, by skimming over what we have here we can see that this data set shows the year the data was collected, the exact coordinates of each site it was taken from, information on the slope of the sites, the dominant species, water cover, soil pHs and rare species lists and ranks. 

By using a combination of the `length()` and `unique()` functions we can see the number of sites sampled, this  will help show the magnitude of the study. 


:pencil2: 
```r
length(unique(rare_plants$site)) # Shows the number of different sites sampled in the study.
```


The rare plants data set contains information recorded from the sampling of 610 sites!From our investigations, we can conclude that the rare plants data set contains A LOT of information. Here we must ask ourselves ... do we need it all? To answere this, we need to think back to our original research question : "How have the statuses and habitat preferences of the rare orchid species in Anthabasca region changed". This research question requires only a few sections of the rare_plants dataframe, so lets get manipulating!


On to Manipulating the data!


## Step 4 - Filter out the data of inetrest

We will start this process by making a dataframe of just orchid observations, as this is the family of plants we   want to investigate . To do this we will use the `filter()` function. We will assign this to a new object, this             will create a new dataframe containing only observations of orchid species . 


:pencil2: 
```r
orchids <- filter(rare_plants, Family == "Orchidaceae")
View(orchids)
```


The line beneath the filter function applies `View()` to the new dataframe 'orchids' which, when run, will automatically display the orchids data frame in the script window. Check the only entry in the 'Family' column reads 'Orchidaceae'. 

>
>
> :interrobang: **Recap question!** 
> 
> <details>
> <summary> Q) What code function could provide a quick check that all our data in the family column read Orchidaceae?        </summary>
> <br>
> unique().
> </details>
>
>


Use the tabs at the top of this window to navigate back to the script.Now we have made our orchid exclusive dataframe, we need to consider whether all of the columns are nessesary for answering our research question. Use the `colnames()` function to show a list of the variables that were recorded in this dataset. 


:pencil2: 
```r
colnames(orchids)
```


To investigate how the status and habitat preferences have changed from year to year in the Anthrabasca region, the only columns we are likely to require are : 

site, year, sci_name, Common_Name_ACIMS, curr_largest_nutrient, dom_tree_sci_name, water_cover, bare_ground_cover, soil_ph1, soil_ph2, soil_ph3, Srank2014, Srank2015, Origin_ACIMS
 
We can exclude columns such as exact site location for example, as they provide too much detail. Whilst they might provide interesting findings, they will distract from the key points we will need if we were to later adress this question. To pull out the above required columns, we will use the `select()` function. We will assign the command to the orchids dataframe so it will be overwritten with the selected columns. 


:pencil2: 
```r
orchids <- orchids %>% select(site, year, sci_name, Common_Name_ACIMS, curr_largest_nutrient, dom_tree_sci_name, water_cover, bare_ground_cover, soil_ph1, soil_ph2, soil_ph3, Srank2014, Srank2015, Origin_ACIMS)
```


We have now filtered the number of columns down from 37 to only 14! This will make it much easier and quicker to access the information relevent to the research question and save heaps of time! 



## Step 5 - A wee exploration of the Orchid data set

We know how our variable columns changed in our data set as we specified which ones we would like, however the little number at the bottom left of the orchids data frame shows that the number of 'entries' has decreased. This suggests that the orchids dataset contains fewer sites than the origional rare plants data frame. Like in step 2, we can perform the `length()` and `unique()` functions on the orchids data frame, this will show   the changes in sites that are suspected to have occured.


:pencil2: 
```r
length(unique(orchids$site))
```


There are only 283 sites in the orchids data frame, it makes sense that this number of sites is smaller than the 610 found in the rare plants data set as orchids were not present in all sites. 



## Step 6 - Clean and arrange the data

Take a quick scroll around the orchid dataframe and you will quickly notice that there are several different ways of indicating when no values were applicable, depending on which column the data is in. For example, non-applicable observations in the soil pH column were denoted by 'VNA' where as in the dom_tree_species column, values which were not applicable were denoted as 'NONE' or left as blank cells. This variety of ways to show the same scenario will complicate further investigation in to the data and so for now we will overwrite all non-applicable cells as `NA` . We will do this using the following code:


:pencil2: 
```r
orchids[orchids ==""]<- NA #Replaces all blank cells with NA
orchids[orchids =="VNA"] <- NA   # Replaces all VNA cells with NA 
orchids[orchids =="NONE"] <- NA  #The code references the data frame we need to be looking at, followed by the entries of   the specific text that needs to be replaced by NA if it is present.
```


For this study, we would like the data observations to be shown in order of the year they were sampled. In order   to arrange the data in such a way we will use the `arrange()` function, as shown below.


:pencil2: 
```r
orchids <- arrange(orchids, year) #Arranging the data so it is ordered by the years it was collected (2012 through to 2015).
```


Take a look at the individual site IDs, they are a random mix of letters and numbers. There is no relation between A sites and B sites, their names are purely for differentiation. Since there is no reason for the site names to be this long and complicated, it will be useful to change them to a simple numbering system starting from 1. This will make it easier to refer between sites form the origional data rare plants data set, and browse the findings in future tasks. 

To rename the sites we will take a quick look at what we are dealing with. Since site is a factor, and each level is a different site (sampled more than once), we can view the site names using the `levels()` function. This is an           alterantive to `unique()` in this case, as each level is unique.  

:pencil2: 
```r
levels(orchids$site) #Call a list of all the levels in the site variable across the whole rareplants dataset 
levels(orchids$site) <- c("1": "610") # Rename the site ID to make it easier to distinguish which is which, although the                                           # orchids data set only contains 283 sites/levels, 1- 610 levels are renamed to as                                           # this is the number of sites/levels in the rare plants data set. 
```

One more thing we could do to make this data set super clean and easy to handle is to remove the non numeric values in the 'Srank' columns. As you can see they both have an S before each rank score, removing this will make it easier to perform numerical comparisons to compare changes in rarity of the orchids later. We will use the `parse number()` function to do this. 


:pencil2: 
```r 
orchids$Srank2014 <- parse_number(orchids$Srank2014) # Removing the characters and extracting only the numerical rank.
orchids$Srank2015 <- parse_number(orchids$Srank2015) 
```

You should find that to receive this error message: 

```r 
Error in parse_vector(x, col_number(), na = na, locale = locale, trim_ws = trim_ws) : 
  is.character(x) is not TRUE
  ```

This is because the SRank data is in the incorrect class to be formatted in to the `parse ()` function. This next section will adress this issue.  

Now we have our official orchid data, we need to ensure it is in longform before we go any further. To do this we will use the `groupby()` function from the dplyr package.



## Step 7 - Ensure data are the correct class

There are 6 main "data types" in R, these distinguish the different forms that data can be recognised as, these data types are then later combined in to data structures such as data frames and matrices. It is important that R recognises each data point as the correct data type; for example in our orchids data set, the problem experienced at the end of the last step occured because the `parse_number()` function cannot be applied to the current class type of the Srank data.

To investigate the structure of our orchids dataframe, we can use the `str()` function. This will display a list showing each column name, the data type R recognises the contents as, and a short preview of the variables contained. We can then go through this list and check if we are happy with the default data types. 


:pencil2: 
```r
str(orchids)
```

The list should show that apart from 'year', all the variables have been classed as factors. The site is a factor, meaning  the sites are stored as a vector of integer values with a corresponding set of character values to use when the factor is displayed. A factor is an efficient way to store variables such as site name, because it can deal with the categorical nature of the data and the fact that the same sites were sampled more than once. It will also still allow us to compare observations between sites if required. 

Year is stored as an integer, `integer` is a subcategory of numeric, and can be used when the data do not have decimals/are whole numbers. 
 
Let us now adress our parsing issue from step 6, consider the error message that was obtained: 


:pencil2: 
```r 
Error in parse_vector(x, col_number(), na = na, locale = locale, trim_ws = trim_ws) : 
  is.character(x) is not TRUE
  ```
  
  
It looks as though the parse_number() function requires the Srank data to be in the character class, if we observe the results from our `str()` investigation, it is clear this is not the case and they are infact classed as factors. The following piece of code will convert the Srank data from factor to character classified data. We will then reattempt to input the columns in to the `parse_number()` function.


:pencil2: 
```r
orchids$Srank2014 <- as.character(orchids$Srank2014) #Changing the classification of the Srank data
orchids$Srank2015 <- as.character(orchids$Srank2015) 

orchids$Srank2014 <- parse_number(orchids$Srank2014) # Removing the characters and extracting only the numerical rank.
orchids$Srank2015 <- parse_number(orchids$Srank2015) 

```

Now we just have nice clean numbers in our Srank data that can be used easily in the future! 

Bare ground cover and water are currently stored as factors however for future ease, should be stored as numeric values. We can convert these variables to numeric using the `as.numeric()` function. When using this function we must specify the name of the data set and the variable column that the function needs to be applied to. 


:pencil2: 
```r
orchids$water_cover = as.numeric(orchids$water_cover)
as.numeric$bare_ground_cover = (orchids$bare_ground_cover) 
```

We will also transform the 3 soil pH variables from the factor to the numeric class. This is because they are     continuous data with decimals. Whilst integer values can be dealt with by the factor class, these data points have           decimals, hence cannot be classed as integers, only numeric. 


:pencil2: 
```r
orchids$soil_ph1 = as.numeric(orchids$soil_ph1) 
orchids$soil_ph2 = as.numeric(orchids$soil_ph2)  # Changing soil_ph variables from factors to numerics. 
orchids$soil_ph3 = as.numeric(orchids$soil_ph3)
```

The `is.numeric()` function can be used to check whether the above transformation of the soil pH variables has      been successful. 


:pencil2: 
```r
is.numeric(orchids$soil_ph1) # Checking that the soil phs are now classed as numeric not as factors The function should                                  # return 'TRUE' in the console if this outcome has been acheived.

```



## Step 8 - Adding new columns

To understand what raw data sets show, especially large ones such as the orchids data set, it is often helpful to summarise findings across the many rows or columns. A way to provide in depth numerical studies is to create additional columns which draw observations from several columns and combine them in to a summative measure such as a mean value. 

For example, in our orchids data set, soil pH measurements were repeated 3 times.To get a better idea of the overall pHs in each observation, we will make a new column using the `mutate()` function, this column will contain the mean pH for each observation which will be calculated using the `rowMeans()` function. We will call this column "ave_soil_ph". 


:pencil2: 
```r
orchids <- orchids %>% mutate(ave_soil_pH = rowMeans(cbind(soil_ph1,soil_ph2,soil_ph3), na.rm=T)) 
```




## Useful links 

### More data manipulation practice 

* [Basic data manipulation tutorial](https://ourcodingclub.github.io/2017/01/06/data-manip-intro.html)
* [Efficient data manipulation tutorial](https://ourcodingclub.github.io/2017/01/06/data-manip-efficient.html)

### The next steps - make use of your beautifully organised data! 

* [Data visualisation](https://ourcodingclub.github.io/2017/01/29/datavis.html) 


