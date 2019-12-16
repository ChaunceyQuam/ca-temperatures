### Final Project- Examing Occurances of Extreme Temperatures in California Between 1989-2018

#### *In this project I will be examining daily temperature observations for selected counties in California during the past 30 years, from 1989-2018. These counties were selected because they are all major agricultural producing counties in California. Extreme temperature can have a drastic effect on crop yield. As climate in California changes our current agricultural production regions of California might become less productive with longer instances of days with extreme temperatures. This project attempts to explore instances of extreme temperatures, classified in this study as temperatures of 105 degrees or higher, to see if temperatures are rising in California. This project will not explore the relationship of extreme temperatures and decrease in crop yeild but simply tries to show that occurences of extreme temperatures have been increasing in our state over the past 30 years.*

#### **Load Packages For Data Analysis**

    #install.packages("tidyverse")
    #install.packages("tidyr")
    #install.packages("dplyr")
    #install.packages("lubridate")
    #install.packages("ggplot2")
    #install.packages("maps")
    library("tidyverse")

    ## Warning: package 'tidyverse' was built under R version 3.5.2

    ## ── Attaching packages ──────────────────────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.2.1     ✓ purrr   0.3.3
    ## ✓ tibble  2.1.3     ✓ dplyr   0.8.3
    ## ✓ tidyr   1.0.0     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.4.0

    ## Warning: package 'ggplot2' was built under R version 3.5.2

    ## Warning: package 'tibble' was built under R version 3.5.2

    ## Warning: package 'tidyr' was built under R version 3.5.2

    ## Warning: package 'purrr' was built under R version 3.5.2

    ## Warning: package 'dplyr' was built under R version 3.5.2

    ## Warning: package 'stringr' was built under R version 3.5.2

    ## Warning: package 'forcats' was built under R version 3.5.2

    ## ── Conflicts ─────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

    library("dplyr")
    library("lubridate")

    ## 
    ## Attaching package: 'lubridate'

    ## The following object is masked from 'package:base':
    ## 
    ##     date

    library("ggplot2")
    library("maps")

    ## 
    ## Attaching package: 'maps'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     map

#### **Reading Data Files**

##### I began this project by downloading temperature data from the [NOAA climate website](https://www.climate.gov/maps-data). I hand selected counties to request this data for. The data files I received and am using in this project are daily temperature observations from stations located within major agricultural producing counties of California.

##### I loaded in data from the CSV files and created one table with all of the temperature observation files.

    df_1 <- read_csv("CSV data files/1929501.csv", col_types = cols(TMAX = col_integer(), TMIN = col_integer()),col_names=TRUE)
    df_2 <- read_csv("CSV data files/1929502.csv", col_types = cols(TMAX = col_integer(), TMIN = col_integer()),col_names=TRUE)
    df_3 <- read_csv("CSV data files/1929482.csv", col_types = cols(TMAX = col_integer(), TMIN = col_integer()),col_names=TRUE)
    df_4 <- read_csv("CSV data files/1929489.csv", col_types = cols(TMAX = col_integer(), TMIN = col_integer()),col_names=TRUE)
    df_5 <- read_csv("CSV data files/1929497.csv", col_types = cols(TMAX = col_integer(), TMIN = col_integer()),col_names=TRUE)

    all_data <- rbind(df_1, df_2, df_3, df_4, df_5)
    head(all_data)

    ## # A tibble: 6 x 8
    ##   STATION   NAME        LATITUDE LONGITUDE ELEVATION DATE        TMAX  TMIN
    ##   <chr>     <chr>          <dbl>     <dbl>     <dbl> <date>     <int> <int>
    ## 1 US1CASR0… BARSTOW 0.…     34.9     -117.      795. 2010-01-22    NA    NA
    ## 2 US1CASR0… BARSTOW 0.…     34.9     -117.      795. 2010-01-27    NA    NA
    ## 3 US1CASR0… BARSTOW 0.…     34.9     -117.      795. 2010-02-06    NA    NA
    ## 4 US1CASR0… BARSTOW 0.…     34.9     -117.      795. 2010-02-07    NA    NA
    ## 5 US1CASR0… BARSTOW 0.…     34.9     -117.      795. 2010-02-10    NA    NA
    ## 6 US1CASR0… BARSTOW 0.…     34.9     -117.      795. 2010-02-28    NA    NA

#### **Cleaning Dataset**

##### I decided to omit all NA variables for temperature values using the code below.

    all_data <- all_data[complete.cases(all_data$TMAX), ]

##### I seperated the full date into three columns by year, month, day.

    all_data <- separate(all_data,DATE, c("YEAR", "MONTH", "DAY"),"-")

#### **Isolating Name of Town**

##### I am doing this by first taking the names column and parcing into seperate columns, seperating by a space or a comma. I then drop all added columns becides CITY.

    all_data <- separate(all_data, NAME, c("CITY","STATE","COUNTRY", "x1", "x2", "x3", "x4"), 
        sep = "([\\ \\,])")

    ## Warning: Expected 7 pieces. Missing pieces filled with `NA` in 221976
    ## rows [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19,
    ## 20, ...].

    all_data <- all_data %>%
      select(-c(STATE, COUNTRY,x1,x2,x3,x4))
    head(all_data)

    ## # A tibble: 6 x 10
    ##   STATION  CITY  LATITUDE LONGITUDE ELEVATION YEAR  MONTH DAY    TMAX  TMIN
    ##   <chr>    <chr>    <dbl>     <dbl>     <dbl> <chr> <chr> <chr> <int> <int>
    ## 1 USC0004… BLYT…     33.6     -115.      81.7 1989  01    01       61    NA
    ## 2 USC0004… BLYT…     33.6     -115.      81.7 1989  01    02       65    28
    ## 3 USC0004… BLYT…     33.6     -115.      81.7 1989  01    03       65    40
    ## 4 USC0004… BLYT…     33.6     -115.      81.7 1989  01    04       64    49
    ## 5 USC0004… BLYT…     33.6     -115.      81.7 1989  01    05       65    39
    ## 6 USC0004… BLYT…     33.6     -115.      81.7 1989  01    06       55    40

##### Check to see what every city name is.

    unique(all_data$CITY) 

    ##  [1] "BLYTHE"     "SQUAW"      "GOLD"       "PICACHO"    "BARSTOW"   
    ##  [6] "IMPERIAL"   "LORAINE"    "CAHUILLA"   "BUTTERCUP"  "HOLLISTER" 
    ## [11] "PAICINES"   "RED"        "WOODLAND"   "CHICO"      "LINCOLN"   
    ## [16] "STOCKTON"   "TRACY"      "STONYFORD"  "PETALUMA"   "SACRAMENTO"
    ## [21] "EAST"       "HANFORD"    "DELANO"     "COALINGA"   "MERCED"

##### I have selected only the first word for each town name. I will now add the words that were omitted from town names manually so that town names are complete in the dataset.

    id <- c("SQUAW", "GOLD", "IMPERIAL", "RED", "EAST")
    names<- c("SQUAW LAKE", "GOLD ROCK RANCH", "IMPERIAL SAND DUNES", "RED BLUFF", "EAST PARK RESERVOIR")

    for (i in c(1:length(id))) {
      all_data$CITY <- ifelse(all_data$CITY == id[i],
                              yes = names[i],
                              no = all_data$CITY)
    }
    unique(all_data$CITY)

    ##  [1] "BLYTHE"              "SQUAW LAKE"          "GOLD ROCK RANCH"    
    ##  [4] "PICACHO"             "BARSTOW"             "IMPERIAL SAND DUNES"
    ##  [7] "LORAINE"             "CAHUILLA"            "BUTTERCUP"          
    ## [10] "HOLLISTER"           "PAICINES"            "RED BLUFF"          
    ## [13] "WOODLAND"            "CHICO"               "LINCOLN"            
    ## [16] "STOCKTON"            "TRACY"               "STONYFORD"          
    ## [19] "PETALUMA"            "SACRAMENTO"          "EAST PARK RESERVOIR"
    ## [22] "HANFORD"             "DELANO"              "COALINGA"           
    ## [25] "MERCED"

##### I also notice that there are some cities that have two observation stations reporting data. I want to remove these double counts to avoid a bias in my results.

    all_data <- all_data %>%
           distinct(CITY,YEAR,MONTH,DAY, .keep_all = TRUE)
    nrow(all_data)

    ## [1] 198764

#### **Exploring Temperature Data**

##### I begin by isolating the occurences of extreme temperature above 105 degrees and count total occurences of extreme temperature by year and by city. I create a table that I can use to visually explore my data. Some cities do not have any occurences of extreme temperature and give back an NA value in the table.

    #create dummy variable for when average temp is above 105
    # avg count above 105 btwn 1989-2010. 
    all_data2 <- all_data %>%
      mutate(above105 = TMAX >= 105,
             above105 = as.numeric(above105)) %>%
      filter(above105==1) %>%
      group_by(CITY,YEAR) %>%
      summarize(count = n()) %>%
      select(CITY, YEAR, count) %>%
      spread(YEAR, count)
    all_data2

    ## # A tibble: 24 x 31
    ## # Groups:   CITY [24]
    ##    CITY  `1989` `1990` `1991` `1992` `1993` `1994` `1995` `1996` `1997`
    ##    <chr>  <int>  <int>  <int>  <int>  <int>  <int>  <int>  <int>  <int>
    ##  1 BARS…     17     25     13      8      7     31     29     41      9
    ##  2 BLYT…     90     75     71     74     96    102     92     96     90
    ##  3 BUTT…     NA     NA     NA     NA     NA     NA     NA     NA     NA
    ##  4 CAHU…     NA     NA     NA     NA     NA     NA     NA     NA     NA
    ##  5 CHICO      1      6      4      1     NA      1      1      4      2
    ##  6 COAL…      4     16     14     11      7     15     16     41      4
    ##  7 DELA…     NA     NA     NA     NA     NA     NA     NA     NA     NA
    ##  8 EAST…     NA      5      3      3      3     NA     NA      5      1
    ##  9 GOLD…     70     51     60     69     82    100     96     NA     NA
    ## 10 HANF…      1      6      6      2      2     NA     NA      3      2
    ## # … with 14 more rows, and 21 more variables: `1998` <int>, `1999` <int>,
    ## #   `2000` <int>, `2001` <int>, `2002` <int>, `2003` <int>, `2004` <int>,
    ## #   `2005` <int>, `2006` <int>, `2007` <int>, `2008` <int>, `2009` <int>,
    ## #   `2010` <int>, `2011` <int>, `2012` <int>, `2013` <int>, `2014` <int>,
    ## #   `2015` <int>, `2016` <int>, `2017` <int>, `2018` <int>

##### I see that some cities have high occurences of extreme temperatures while others have none.

##### I now want to see cumulative counts of extreme temperature by year. My first question is if occurences of extreme temperatures are increasing throughout California. I want to see if my yearly data from all of the selected cities have volatile counts or trends.

    all_data3 <- all_data %>%
      mutate(above105 = TMAX >= 105,
             above105 = as.numeric(above105)) %>%
      filter(above105==1) %>%
      group_by(YEAR) %>%
      summarize(count = n()) %>%
      select(YEAR, count)
    all_data3

    ## # A tibble: 30 x 2
    ##    YEAR  count
    ##    <chr> <int>
    ##  1 1989    319
    ##  2 1990    340
    ##  3 1991    273
    ##  4 1992    304
    ##  5 1993    319
    ##  6 1994    411
    ##  7 1995    485
    ##  8 1996    497
    ##  9 1997    237
    ## 10 1998    283
    ## # … with 20 more rows

    ggplot(all_data3) +
      geom_line(mapping = aes(x=as.numeric(YEAR), y= count), color= "blue") +
      labs(title = "Occurences of Temperatures above 105(F)", x = "Year", y= "Count")

![](Final_Project_Submission_files/figure-markdown_strict/plot%20of%20yearly%20counts-1.png)

##### It seems that there are increasing yearly counts of days with extreme temperatures, but possibly this is not completely accurate. Not all stations have the same number of days during the year that are being reported.

##### Possibly it is better to take an average by year and plot this, rather than yearly count. I decide to see how different this plots in comparison to the yearly count plot.

    all_data4 <- all_data %>%
      mutate(above105 = TMAX >= 105,
             above105 = as.numeric(above105)) %>%
      filter(above105==1) %>%
      group_by(CITY,YEAR) %>%
      summarize(count = n()) %>%
      group_by(YEAR) %>%
      summarize(avg = mean(count))
    head(all_data4) 

    ## # A tibble: 6 x 2
    ##   YEAR    avg
    ##   <chr> <dbl>
    ## 1 1989   22.8
    ## 2 1990   24.3
    ## 3 1991   18.2
    ## 4 1992   20.3
    ## 5 1993   21.3
    ## 6 1994   41.1

    ggplot(all_data4) +
      geom_line(mapping = aes(x=as.numeric(YEAR), y= avg), color= "blue") +
      labs(title = "Average Yearly Occurences of Temperatures Above 105(F)", x = "Year", y= "Average Occurences")

![](Final_Project_Submission_files/figure-markdown_strict/plot%20of%20average%20yearly%20counts%20of%20extreme%20tempaverages-1.png)

##### This plot is slightly different than the first plot. I feel more comfortable with the graph that shows the average aggregate count of days with extreme temperatures. However they are both showing the same trend of increasing aggregate counts of days with extreme temperatures.

##### I have taken the yearly average number of days of extreme temperature in all the cities per year with 95% confidence intervals. Signifcance is indicated by shifts beyond 95% confidence range for each year.

    all_data %>%
      mutate(above105 = TMAX >= 105,
             above105 = as.numeric(above105)) %>%
      filter(above105==1) %>%
      group_by(CITY,YEAR) %>%
      summarize(freq = n()) %>%
      group_by(YEAR) %>%
      summarize(avg = mean(freq),
                stdev = sd(freq),
                upper = avg + (stdev*1.96/sqrt(n())),
                lower = avg - (stdev*1.96/sqrt(n()))
                ) %>%
      ggplot() +
      geom_point(aes(x = as.character(YEAR), y = avg)) +
      geom_linerange(aes(x = as.character(YEAR), ymax = upper, ymin = lower)) +
      labs(title = "Yearly Average of Days with Temperatures Above 105(F)", x= "Year", y= "Average Number of Days")

![](Final_Project_Submission_files/figure-markdown_strict/other%20plots%20of%20data-1.png)

##### This shows that there are sligtly large error bars, but in general the average aggregate count of days with extreme temperatures per year is slightly increasing.

##### I want to examine my data more closely to understand average counts of days with extreme temperature by city. I created a new table with the spread of counted times that temperature was above 105 per year in each city.

    all_data5 <- all_data %>%
      mutate(above105 = TMAX >= 105,
             above105 = as.numeric(above105)) %>%
      filter(above105==1) %>%
      group_by(CITY,YEAR, LATITUDE,LONGITUDE) %>%
      summarize(count = n()) %>%
      group_by(CITY, LATITUDE, LONGITUDE) %>%
      summarize(avg = mean(count)) 
    head(all_data5) 

    ## # A tibble: 6 x 4
    ## # Groups:   CITY, LATITUDE [6]
    ##   CITY      LATITUDE LONGITUDE   avg
    ##   <chr>        <dbl>     <dbl> <dbl>
    ## 1 BARSTOW       34.9     -117. 21.6 
    ## 2 BLYTHE        33.6     -115. 74.5 
    ## 3 BLYTHE        33.6     -115. 28.7 
    ## 4 BUTTERCUP     32.7     -115. 92.5 
    ## 5 CAHUILLA      33.0     -115. 89.1 
    ## 6 CHICO         39.7     -122.  4.83

    ggplot(all_data5) +
      geom_col(mapping= aes(x= CITY, y = avg)) +
      labs(title="Average Number of Days with Temperatures Above 105(F)", x= "City", y="Average Number of Days")

![](Final_Project_Submission_files/figure-markdown_strict/bar%20chart%20of%20averages-1.png)

##### This histogram is very cluttered along the x axis with city names. Instead of a bar chart I would like to see these yearly counts of extreme temperature for each city. It does show that some cities have much larger average counts than other cities. I want to more closely examine the yearly count of extreme temperatures in each city.

##### I seperate the counts of days per year each city had extreme temperatures, showing the total days per year for each city.

    all_data %>%
      mutate(above105 = TMAX >= 105,
             above105 = as.numeric(above105)) %>%
      filter(above105==1) %>%
      group_by(CITY,YEAR) %>%
      summarize(count = n()) %>%
      ggplot() +
      geom_line(aes(as.numeric(YEAR),count)) +
      facet_wrap(~CITY) +
      labs(title = "Number of Days Each City Had Temperatures Above 105(F)", x= "Year", y= "Count") +
      facet_wrap(~CITY)

![](Final_Project_Submission_files/figure-markdown_strict/yearly%20counts%20by%20city-1.png)

##### Some cities experienced more days of extreme temperature per year than others, with fluxuation by year while other cities do not have much fluxuation. There are noticably hotter regions in California than others. I also see that some stations do not provide data for the entire time series. If I were to repeat this analysis I would try to find station data that provides temperature measurements for the entire time series for the locations I am investigating. There are some cities that have a significant dip in days of extreme temperatures and I am curious about this. I have a feeling that those dips in the data are due to missing data, however I am not sure on this and wont be investigating further.

#### **Visualizing Location of Stations Reporting Temperatures in California**

##### I would now like to create a map of California that shows the location of each station reporting temperature data. I decided to take the average number of times there were extreme temperatures in each city, for all of the years being reported. I then use this to create a color gradient heat map that shows the cooler and hotter counties. I think that certain geographic locations of California have more instances of extreme temperature, on average, than other counties.

    ca_map <- map_data("state")
    ca_map <- ca_map %>%
      filter(region == "california")

##### This is my map of the points of the station locations that were used. I used a heat map option to show the comparative average count of extreme temperatues documented by each station location.

    ggplot() +
      geom_polygon(data=ca_map, mapping=aes(x = long*.995, y = lat*.99 ), inherit.aes = FALSE, fill = "gold", color = "blue", alpha = .9) +
      geom_point(data=all_data5, mapping=aes(x = LONGITUDE, y = LATITUDE, color=avg)) + labs(title = "Location Based Average Counts of Temperature Above 105(F)", x = "Longitude", y= "Latitude")

![](Final_Project_Submission_files/figure-markdown_strict/ca%20map%20with%20station%20locations-1.png)

##### I can see that there are a few counties that have two stations reporting. I tried to resolve this error earlier but I am not sure how to fix this with another method. It is showing that the further south in California the cities are located, the higher the instances of extreme temperatures. Also, many of these locations are closer to the valley floor and have typically hotter temperatres on average than locations along the coast or in the foothills. The map is slightly skewed because I manipulated its size to match the data points more accurately, as initially two costal points were located in the Pacific Ocean. Though it looks a little strange, I felt that this wasn’t an innacurate manipulation because it still accurately shows a general geographical trend of southern regions having more instances of extreme temperatues.

#### **Linear Regression and Causality**

##### I decided to examine my data with a linear regression analysis. I want to see the effect that each variable has on temperature.

    regression_model <- lm(TMAX ~ ELEVATION + as.numeric(YEAR) + factor(CITY), data = all_data)
    summary(regression_model)

    ## 
    ## Call:
    ## lm(formula = TMAX ~ ELEVATION + as.numeric(YEAR) + factor(CITY), 
    ##     data = all_data)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -132.43  -13.05   -0.03   13.42  816.99 
    ## 
    ## Coefficients:
    ##                                   Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                     -28.052509   9.536666  -2.942  0.00327 ** 
    ## ELEVATION                        -0.003974   0.006158  -0.645  0.51866    
    ## as.numeric(YEAR)                  0.055509   0.004865  11.411  < 2e-16 ***
    ## factor(CITY)BLYTHE                6.331809   3.608644   1.755  0.07933 .  
    ## factor(CITY)BUTTERCUP             7.569596   3.776108   2.005  0.04501 *  
    ## factor(CITY)CAHUILLA              6.890490   3.661864   1.882  0.05988 .  
    ## factor(CITY)CHICO                -7.704933   3.754255  -2.052  0.04014 *  
    ## factor(CITY)COALINGA             -1.590293   2.917859  -0.545  0.58574    
    ## factor(CITY)DELANO               -4.868650   3.631662  -1.341  0.18005    
    ## factor(CITY)EAST PARK RESERVOIR  -7.700943   1.915275  -4.021 5.80e-05 ***
    ## factor(CITY)GOLD ROCK RANCH       6.658700   3.263003   2.041  0.04129 *  
    ## factor(CITY)HANFORD              -5.923064   3.713193  -1.595  0.11068    
    ## factor(CITY)HOLLISTER           -10.610299   3.527472  -3.008  0.00263 ** 
    ## factor(CITY)IMPERIAL SAND DUNES   8.940575   3.804984   2.350  0.01879 *  
    ## factor(CITY)LINCOLN              -6.216143   3.799659  -1.636  0.10185    
    ## factor(CITY)LORAINE              -8.100243   3.784220  -2.141  0.03231 *  
    ## factor(CITY)MERCED               -6.010159   3.884694  -1.547  0.12183    
    ## factor(CITY)PAICINES            -10.585135   2.481356  -4.266 1.99e-05 ***
    ## factor(CITY)PETALUMA            -12.837266   4.135800  -3.104  0.00191 ** 
    ## factor(CITY)PICACHO               3.763323   2.599561   1.448  0.14771    
    ## factor(CITY)RED BLUFF            -6.560271   3.511412  -1.868  0.06173 .  
    ## factor(CITY)SACRAMENTO           -8.488989   4.137062  -2.052  0.04018 *  
    ## factor(CITY)SQUAW LAKE            7.015731   3.610846   1.943  0.05202 .  
    ## factor(CITY)STOCKTON             -7.621160   4.124082  -1.848  0.06461 .  
    ## factor(CITY)STONYFORD            -6.303026   1.932211  -3.262  0.00111 ** 
    ## factor(CITY)TRACY                -5.548014   3.920422  -1.415  0.15702    
    ## factor(CITY)WOODLAND             -7.138162   4.047597  -1.764  0.07781 .  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 16.37 on 198737 degrees of freedom
    ## Multiple R-squared:  0.122,  Adjusted R-squared:  0.1219 
    ## F-statistic:  1062 on 26 and 198737 DF,  p-value: < 2.2e-16

##### Examing the regression I see that there is significant positive temperature increase over time. An incrase in elevation decreases the temperature. City variables can have a positive or negative effect on temperature, depending on where in California the city is located. However, my R-squared value is very low, 0.12, which tells me that I would like to have more data to strengthen the confidence in my results. I found this regression to be very interesting and informative for the effects my variables have on my outcome of temperature.

#### **Conclusion**

##### This project showed that on average temperatures do seem to be increasing in California during the past 30 years. I can see that some counties experience increasing counts of days above 105(F) as the years progress while other counties do not often experience days of extreme temperatures at all during the year. The map illustrates that the location of a city in California contributes greatly to the average count of days with extreme temperatures. I found the regression especially informative, even though my R-squared value was quite low. I can confidenly conclude that on average temperatures are indeed increasing throughout California. This will likely be a greater issue in the future, as global temperatues are projected to increase with climate change. I would like to continue this project and relate it to the agricultural sector to see if increases in extreme temperatues can be linked to decreases in agricultural revenue. I would also like to repeat this analysis with more comprehensive data for more station locations with complete observations for the past 30 years. I think this would increase my R-squared value in my linear regression and provide more robust results.
