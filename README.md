## Video tutorial repository: "intro_to_rstudio"

- - -

### An Introduction to R and RStudio

#### IMMERSE Project: Institute of Mixture Modeling for Equity-Oriented Researchers, Scholars, and Educators

#### Video Series Funded by IES Training Grant (R305B220021)

#### *Dina Arch*

- - -

# IMMERSE Project

![](images/IESNewLogo.jpg)

The Institute of Mixture Modeling for Equity-Oriented Researchers, Scholars, and Educators (IMMERSE) is an IES funded training grant (R305B220021) to support Education scholars in integrating mixture modeling into their research.

-   Please [visit our website](https://immerse.education.ucsb.edu/) to learn more and apply for the year-long fellowship.

-   Follow us on [Twitter](https://twitter.com/IMMERSE_UCSB)!

How to reference this walkthrough: *This work was supported by the IMMERSE Project* (IES - 305B220021)

Visit our [GitHub](https://github.com/immerse-ucsb) account to download the materials needed for this walkthrough.

------------------------------------------------------------------------

# Introduction to R and RStudio

This walkthrough is presented by the IMMERSE team and will go through some common tasks carried out in R.
There are many free resources available to get started with R and RStudio.
One of our favorites is [*R for Data Science*](https://r4ds.had.co.nz/).

------------------------------------------------------------------------

## PART 1: Installation

------------------------------------------------------------------------

### Step 0: Install R, RStudio, and Mplus

[Here](https://posit.co/download/rstudio-desktop/) you will find a guide to installing both R and R Studio.
You can also install Mplus [here](https://www.statmodel.com/orderonline/).

*Note*: The installation of Mplus requires a paid license with the mixture add-on.
IMMERSE fellows will be given their own copy of Mplus for use during the one year training.

------------------------------------------------------------------------

## PART 2: Set-up

------------------------------------------------------------------------

### Step 1: Create a new R-project in RStudio

R-projects help us organize our folders , filepaths, and scripts.
To create a new R project:

-   File --\> New Project...

Click "New Directory" --\> New Project --\> Name your project

### Step 2: Create an R-markdown document

An R-markdown file provides an authoring framework for data science that allows us to organize our reports using texts and code chunks.
This document you are reading was made using R-markdown!

To create an R-markdown:

-   File --\> New File --\> R Markdown...

In the window that pops up, give the R-markdown a title such as "**Introduction to R and RStudio**" Click "OK." You should see a new markdown with some example text and code chunks.
We want a clean document to start off with so delete everything from line 10 down.
Go ahead and save this document in your R Project folder.

### Step 3: Load packages

Your first code chunk in any given markdown should be the packages you will be using.
To insert a code chunk, etiher use the keyboard shortcut ctrl + alt + i or Code --\> Insert Chunk or click the green box with the letter C on it.
There are a few packages we want our markdown to read in:

```{r}
library(psych) # describe()
library(here) #helps with filepaths
library(gt) # create tables
library(tidyverse) #collection of R packages designed for data science
```

As a reminder, if a function does not work and you receive an error like this: `could not find function "random_function"`; or if you try to load a package and you receive an error like this: `` there is no package called `random_package` `` , then you will need to install the package using `install.packages("random_package")` in the console (the bottom-left window in R studio).
Once you have installed the package you will *never* need to install it again, however you must *always* load in the packages at the beginning of your R markdown using `library(random_package)`, as shown in this document.

The style of code and package we will be using is called [`tidyverse`](https://www.tidyverse.org/) .
Most functions are within the `tidyverse` package and if not, I've indicated the packages used in the code chunk above.

------------------------------------------------------------------------

## PART 3: Explore the data

------------------------------------------------------------------------

### Step 4: Read in data

To demonstrate mixture modeling in the training program and online resource components of the IES grant we utilize the *Civil Rights Data Collection (CRDC)* (CRDC) data repository.
The CRDC is a federally mandated school-level data collection effort that occurs every other year.
This public data is currently available for selected latent class indicators across 4 years (2011, 2013, 2015, 2017) and all US states.
In this example, we use the Arizona state sample.
We utilize six focal indicators which constitute the latent class model in our example; three variables which report on harassment/bullying in schools based on disability, race, or sex, and three variables on full-time equivalent school staff hires (counselor, psychologist, law enforcement).
This data source also includes covariates on a variety of subjects and distal outcomes reported in 2018 such as math/reading assessments and graduation rates.

```{r, echo=FALSE, eval=TRUE}

tribble(
   ~"Name",      ~"Label",  ~"Values",                                   
#--------------|--------------------------------|-----|,
  "leaid",   "District Identification Code", "",
  "ncessch",   "School Identification Code", "",
  "report_dis",   "Number of students harassed or bullied on the basis of disability",  "0 = No reported incidents, 1 = At least one reported incident",
  "report_race",  "Number of students harassed or bullied on the basis of race, color, or national origin",  "0 = No reported incidents, 1 = At least one reported incident",
  "report_sex", "Number of students harassed or bullied on the basis of sex",  "0 = No reported incidents, 1 = At least one reported incident",
  "counselors_fte", "Number of full time equivalent counselors hired as school staff",  "0 = No staff present, 1 = At least one staff present",  
  "report_sex", "Number of full time equivalent psychologists hired as school staff",  "0 = No staff present, 1 = At least one staff present",
  "counselors_fte", "Number of full time equivalent law enforcement officers hired as school staff",  "0 = No staff present, 1 = At least one staff present") %>% 
  gt() %>% 
  tab_header(
    title = "LCA indicators"  # Add a title
  ) %>%
  tab_options(
    table.width = pct(75)
  ) %>%
  tab_footnote(
    footnote = "Civil Rights Data Collection (CRDC)",
    location = cells_title()) 

#PDF Option: 
#kableExtra::kable(df, caption = "LCA Indicators", booktabs = TRUE) %>% 
#  kableExtra::kable_styling(latex_options=c("striped","scale_down"))

```

**To read in data in R**:

```{r}
data <- read_csv(here("data", "crdc_lca_data.csv")) 

# Ways to view data in R:
# 1. click on the data in your Global Environment (upper right pane) or use...
View(data)
# 2. summary() gives basic summary statistics & shows number of NA values
# *great for checking that data has been read in correctly*
summary(data)
# 3. names() provides a list of column names. Very useful if you don't have them memorized!
names(data)
# 4. head() prints the top x rows of the dataframe
head(data)
```

### Step 5: Descriptive Statistics

Let's look at descriptive statistics for each variable.
Because looking at the ID variables' (`leaid`) and (`necessch`) descriptives is unnecessary, we use `select()` to remove the variable by using the minus (`-`) sign:

```{r}
data %>% 
  select(-leaid, -ncessch) %>% 
  summary()
```

Alternatively, we can use the `psych::describe()` function to give more information:

```{r}
data %>% 
  select(-leaid, -ncessch) %>% 
  describe()
```

What if we want to look at a subset of the data?
For example, what if we want to subset the data to observe a specific school district? (`leaid`)
We can use `tidyverse::filter()` to subset the data using certain criteria.

```{r}
data %>% 
  filter(leaid == "0408800") %>% 
  describe() 


#You can use any operator to filter: >, <, ==, >=, etc.
```

Since we have binary data (0,1), it would be helpful to look at the proportions:

```{r}
data %>% 
  drop_na() %>% 
  pivot_longer(report_dis:law_fte, names_to = "variable") %>% 
  group_by(variable) %>% 
  summarise(prop = sum(value)/n(),
            n = n()) %>%
  arrange(desc(prop))
```
------------------------------------------------------------------------

## References

Hallquist, M. N., & Wiley, J. F. (2018). MplusAutomation: An R Package for Facilitating Large-Scale Latent Variable Analyses in Mplus. Structural equation modeling: a multidisciplinary journal, 25(4), 621-638.

Muthén, L.K. and Muthén, B.O. (1998-2017). Mplus User's Guide. Eighth Edition. Los Angeles, CA: Muthén & Muthén

US Department of Education Office for Civil Rights. (2014). Civil rights data collection data snapshot: School discipline. Issue brief no. 1.

R Core Team (2017). R: A language and environment for statistical computing. R Foundation for Statistical Computing, Vienna, Austria. URL http://www.R-project.org/

Wickham et al., (2019). Welcome to the tidyverse. Journal of Open Source Software, 4(43), 1686, https://doi.org/10.21105/joss.01686

------------------------------------------------------------------------

![](images/UCSB_Navy_mark.png)
