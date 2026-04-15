
<!-- README.md is generated from README.Rmd. Please edit the README.Rmd file -->

# Lab report \#3 - instructions

Follow the instructions posted at
<https://ds202-at-isu.github.io/labs.html> for the lab assignment. The
work is meant to be finished during the lab time, but you have time
until Monday evening to polish things.

Include your answers in this document (Rmd file). Make sure that it
knits properly (into the md file). Upload both the Rmd and the md file
to your repository.

All submissions to the github repo will be automatically uploaded for
grading once the due date is passed. Submit a link to your repository on
Canvas (only one submission per team) to signal to the instructors that
you are done with your submission.

# Lab 3: Avenger’s Peril

## As a team

Extract from the data below two data sets in long form `deaths` and
`returns`

``` r
av <- read.csv("https://raw.githubusercontent.com/fivethirtyeight/data/master/avengers/avengers.csv", stringsAsFactors = FALSE)
head(av)
```

    ##                                                       URL
    ## 1           http://marvel.wikia.com/Henry_Pym_(Earth-616)
    ## 2      http://marvel.wikia.com/Janet_van_Dyne_(Earth-616)
    ## 3       http://marvel.wikia.com/Anthony_Stark_(Earth-616)
    ## 4 http://marvel.wikia.com/Robert_Bruce_Banner_(Earth-616)
    ## 5        http://marvel.wikia.com/Thor_Odinson_(Earth-616)
    ## 6       http://marvel.wikia.com/Richard_Jones_(Earth-616)
    ##                    Name.Alias Appearances Current. Gender Probationary.Introl
    ## 1   Henry Jonathan "Hank" Pym        1269      YES   MALE                    
    ## 2              Janet van Dyne        1165      YES FEMALE                    
    ## 3 Anthony Edward "Tony" Stark        3068      YES   MALE                    
    ## 4         Robert Bruce Banner        2089      YES   MALE                    
    ## 5                Thor Odinson        2402      YES   MALE                    
    ## 6      Richard Milhouse Jones         612      YES   MALE                    
    ##   Full.Reserve.Avengers.Intro Year Years.since.joining Honorary Death1 Return1
    ## 1                      Sep-63 1963                  52     Full    YES      NO
    ## 2                      Sep-63 1963                  52     Full    YES     YES
    ## 3                      Sep-63 1963                  52     Full    YES     YES
    ## 4                      Sep-63 1963                  52     Full    YES     YES
    ## 5                      Sep-63 1963                  52     Full    YES     YES
    ## 6                      Sep-63 1963                  52 Honorary     NO        
    ##   Death2 Return2 Death3 Return3 Death4 Return4 Death5 Return5
    ## 1                                                            
    ## 2                                                            
    ## 3                                                            
    ## 4                                                            
    ## 5    YES      NO                                             
    ## 6                                                            
    ##                                                                                                                                                                              Notes
    ## 1                                                                                                                Merged with Ultron in Rage of Ultron Vol. 1. A funeral was held. 
    ## 2                                                                                                  Dies in Secret Invasion V1:I8. Actually was sent tto Microverse later recovered
    ## 3 Death: "Later while under the influence of Immortus Stark committed a number of horrible acts and was killed.'  This set up young Tony. Franklin Richards later brought him back
    ## 4                                                                               Dies in Ghosts of the Future arc. However "he had actually used a hidden Pantheon base to survive"
    ## 5                                                      Dies in Fear Itself brought back because that's kind of the whole point. Second death in Time Runs Out has not yet returned
    ## 6                                                                                                                                                                             <NA>

Get the data into a format where the five columns for Death\[1-5\] are
replaced by two columns: Time, and Death. Time should be a number
between 1 and 5 (look into the function `parse_number`); Death is a
categorical variables with values “yes”, “no” and ““. Call the resulting
data set `deaths`.

``` r
deaths <- av |>
  pivot_longer(cols = c("Death1", "Death2", "Death3", "Death4", "Death5"),
               names_to = "Time",
               values_to = "Death") |>
  filter(Death != "")

deaths$Time <- parse_number(deaths$Time)

head(deaths)
```

    ## # A tibble: 6 × 18
    ##   URL                 Name.Alias Appearances Current. Gender Probationary.Introl
    ##   <chr>               <chr>            <int> <chr>    <chr>  <chr>              
    ## 1 http://marvel.wiki… "Henry Jo…        1269 YES      MALE   ""                 
    ## 2 http://marvel.wiki… "Janet va…        1165 YES      FEMALE ""                 
    ## 3 http://marvel.wiki… "Anthony …        3068 YES      MALE   ""                 
    ## 4 http://marvel.wiki… "Robert B…        2089 YES      MALE   ""                 
    ## 5 http://marvel.wiki… "Thor Odi…        2402 YES      MALE   ""                 
    ## 6 http://marvel.wiki… "Thor Odi…        2402 YES      MALE   ""                 
    ## # ℹ 12 more variables: Full.Reserve.Avengers.Intro <chr>, Year <int>,
    ## #   Years.since.joining <int>, Honorary <chr>, Return1 <chr>, Return2 <chr>,
    ## #   Return3 <chr>, Return4 <chr>, Return5 <chr>, Notes <chr>, Time <dbl>,
    ## #   Death <chr>

Similarly, deal with the returns of characters.

``` r
returns <- av |>
  pivot_longer(cols = c("Return1", "Return2", "Return3", "Return4", "Return5"),
               names_to = "Time",
               values_to = "Return") |>
  filter(Return != "")

returns$Time <- parse_number(returns$Time) 

head(returns)
```

    ## # A tibble: 6 × 18
    ##   URL                 Name.Alias Appearances Current. Gender Probationary.Introl
    ##   <chr>               <chr>            <int> <chr>    <chr>  <chr>              
    ## 1 http://marvel.wiki… "Henry Jo…        1269 YES      MALE   ""                 
    ## 2 http://marvel.wiki… "Janet va…        1165 YES      FEMALE ""                 
    ## 3 http://marvel.wiki… "Anthony …        3068 YES      MALE   ""                 
    ## 4 http://marvel.wiki… "Robert B…        2089 YES      MALE   ""                 
    ## 5 http://marvel.wiki… "Thor Odi…        2402 YES      MALE   ""                 
    ## 6 http://marvel.wiki… "Thor Odi…        2402 YES      MALE   ""                 
    ## # ℹ 12 more variables: Full.Reserve.Avengers.Intro <chr>, Year <int>,
    ## #   Years.since.joining <int>, Honorary <chr>, Death1 <chr>, Death2 <chr>,
    ## #   Death3 <chr>, Death4 <chr>, Death5 <chr>, Notes <chr>, Time <dbl>,
    ## #   Return <chr>

Based on these datasets calculate the average number of deaths an
Avenger suffers.

``` r
avg_deaths <- deaths |>
  group_by(Name.Alias) |>
  summarise(Total_Deaths = sum(Death == "YES"), .groups = "drop") |>
  summarise(Avg_Deaths = mean(Total_Deaths))

avg_deaths
```

    ## # A tibble: 1 × 1
    ##   Avg_Deaths
    ##        <dbl>
    ## 1      0.546

## Individually

For each team member, copy this part of the report.

Each team member picks one of the statements in the FiveThirtyEight
[analysis](https://fivethirtyeight.com/features/avengers-death-comics-age-of-ultron/)
and fact checks it based on the data. Use dplyr functionality whenever
possible.

### FiveThirtyEight Statement

> Quote the statement you are planning to fact-check.

Kate Passwater: Quote: Code:

Samara Feldhacker: Quote: Code:

Shashank Koneru:  
Quote: “But you can only tempt death so many times. There’s a 2-in-3
chance that a member of the Avengers returned from their first stint in
the afterlife, but only a 50 percent chance they recovered from a second
or third death.”  
Code:

``` r
initial_stats <- av |>
  summarise(
    initial_deaths = sum(Death1 == "YES", na.rm = TRUE),
    initial_returns = sum(Return1 == "YES", na.rm = TRUE),
    initial_return_rate = initial_returns / initial_deaths
  )

two_third_stats <- av |>
  summarise(
    two_third_deaths = sum(Death2 == "YES", na.rm = TRUE) + sum(Death3 == "YES", na.rm = TRUE),
    two_third_returns = sum(Return2 == "YES", na.rm = TRUE) + sum(Return3 == "YES", na.rm = TRUE),
    two_third_return_rate = two_third_returns / two_third_deaths
  )

initial_stats
```

    ##   initial_deaths initial_returns initial_return_rate
    ## 1             69              46           0.6666667

``` r
two_third_stats
```

    ##   two_third_deaths two_third_returns two_third_return_rate
    ## 1               18                 9                   0.5

Summary for Shashank’s Results: This statement is backed up by the data
and the results. Out of the 69 initial deaths, 46 were followed by a
return, which provided a return rate of 0.667 (roughly 2 in 3). For the
second and third deaths combined, there were 18 deaths followed by 9
returns. This is a return rate of 50%, making the article’s claim
accurate.

Sebastian Naranjo: Quote: Code:

### Include the code

Make sure to include the code to derive the (numeric) fact for the
statement

### Include your answer

Include at least one sentence discussing the result of your
fact-checking endeavor.

Upload your changes to the repository. Discuss and refine answers as a
team.
