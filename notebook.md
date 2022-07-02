
## Airbnb listings in New York City
<p><img src="https://assets.datacamp.com/production/project_1230/img/nyc.jpg" alt="New York City skyline" width="600px"></p>
<p>Welcome to New York City, one of the most-visited cities in the world. As a result, there are many <a href="https://www.airbnb.com/"><em>Airbnb</em></a> listings in New York City to meet the high demand for temporary lodging for anywhere between a few nights to many months. In this notebook, we will take a closer look at the New York Airbnb market by combining data from multiple file types like <code>.csv</code>, <code>.tsv</code>, and <code>.xlsx</code> (Excel files).</p>
<p>A <strong>CSV</strong>, or comma-separated-values, file is one of the most common ways that tabular data is stored. In a CSV file, each value is separated by a comma and each row is separated by a newline. Since this file format is so widely used and is non-proprietary, it's great for sharing data with others and can be parsed by a variety of software.</p>
<pre><code>"listing_id","price","nbhood_full"
2595,"225 dollars","Manhattan, Midtown"
3831,"89 dollars","Brooklyn, Clinton Hill"
5099,"200 dollars","Manhattan, Murray Hill"
</code></pre>
<p>A <strong>TSV</strong>, or tab-separated-values, file is similar to a CSV file, but tabs are used to separate values rather than commas:</p>
<pre><code>listing_id    host_name   last_review
2595    Jennifer    May 21 2019
3831    LisaRoxanne July 05 2019
5099    Chris   June 22 2019
</code></pre>
<p><strong>Excel files</strong> are often used by spreadsheet users. Excel files contain information about formatting and formulas created in Excel, but these things aren't usually necessary when working with data in R. Excel files can also contain multiple tables, so these files need to be imported carefully to make sure the correct table is used. The Excel file format is also proprietary, so there's a more limited pool of software that can read it.</p>
<p><img src="https://assets.datacamp.com/production/project_1230/img/airbnb_room_type.png" alt="Excel file" width="700px"></p>
<p>The three files that are available contain data on 2019 Airbnb listings. Here are the details:</p>
<div style="background-color: #efebe4; color: #05192d; text-align:left; vertical-align: middle; padding: 15px 25px 15px 25px; line-height: 1.6;">
<div style="font-size:20px"><b>datasets/airbnb_price.csv</b></div>
This is a CSV file containing data on the prices and neighborhoods of Airbnbs.
<ul>
<li><b><code>listing_id</code>:</b> unique identifier of listing</li>
<li><b><code>price</code>:</b> nightly listing price in USD</li>
<li><b><code>nbhood_full</code>:</b> name of borough and neighborhood where listing is located</li>
</ul>
</div>
<div style="background-color: #efebe4; color: #05192d; text-align:left; vertical-align: middle; padding: 15px 25px 15px 25px; line-height: 1.6;">
<div style="font-size:20px"><b>datasets/airbnb_room_type.xlsx</b></div>
This is an Excel file containing data on Airbnb listing descriptions and room types.
<ul>
<li><b><code>listing_id</code>:</b> unique identifier of listing</li>
<li><b><code>description</code>:</b> listing description</li>
<li><b><code>room_type</code>:</b> Airbnb has three types of rooms: shared rooms, private rooms, and entire homes/apartments</li>
</ul>
</div>
<div style="background-color: #efebe4; color: #05192d; text-align:left; vertical-align: middle; padding: 15px 25px 15px 25px; line-height: 1.6;">
<div style="font-size:20px"><b>datasets/airbnb_last_review.tsv</b></div>
This is a TSV file containing data on Airbnb host names and review dates.
<ul>
<li><b><code>listing_id</code>:</b> unique identifier of listing</li>
<li><b><code>host_name</code>:</b> name of listing host</li>
<li><b><code>last_review</code>:</b> date when the listing was last reviewed</li>
</ul>
</div>
<p><strong>Note:</strong> This project lets you apply the skills from the <a href="https://learn.datacamp.com/skill-tracks/importing-cleaning-data-with-r">Importing and Cleaning Data with R skill track</a>, including importing from different file types and cleaning numerical, categorical, and date data. We recommend that you take the courses in this track before starting this project.</p>


```R
#Packages
library(dplyr)
library(readr)
library(readxl)
library(stringr)
```


```R
#Reading first table with prices
airbnb_price <- read_csv("datasets/airbnb_price.csv", show_col_types = FALSE)


#Changing the price column to a integer type with cutting "dollar" in the string  
airbnb_price$price <- str_extract(airbnb_price$price, "\\w+")

airbnb_price$price <- as.integer(airbnb_price$price)
 
# Table has no NAs
airbnb_price %>%
filter(is.na(nbhood_full))
```


<table>
<caption>A spec_tbl_df: 0 × 3</caption>
<thead>
	<tr><th scope=col>listing_id</th><th scope=col>price</th><th scope=col>nbhood_full</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;chr&gt;</th></tr>
</thead>
<tbody>
</tbody>
</table>




```R
#Reading second table with room_types

airbnb_type <- read_excel("datasets/airbnb_room_type.xlsx")
#Fixing differences in string in Room_type to have 3 original types
airbnb_type <- airbnb_type %>% 
mutate(room_type = tolower(room_type)) %>%
#changing room_type to a factor 
mutate(room_type = as.factor(room_type))




# Table has 10 NAs in Description (wouldn't affect)


 

```


```R
#Reading third table with last reviews 

airbnb_lrev <- read_tsv("datasets/airbnb_last_review.tsv", show_col_types = FALSE)


#Changing the last_review to a date type 

 
airbnb_lrev$last_review <- as.Date(airbnb_lrev$last_review, format = "%B %d %Y")


# Table has 8 NAs in host name 

airbnb_lrev %>%
filter(is.na(last_review))



```


<table>
<caption>A spec_tbl_df: 0 × 3</caption>
<thead>
	<tr><th scope=col>listing_id</th><th scope=col>host_name</th><th scope=col>last_review</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;date&gt;</th></tr>
</thead>
<tbody>
</tbody>
</table>




```R
# Joining tables together 
#FULL joint not tom miss any units

airbnb <- airbnb_price %>%
full_join(airbnb_type, by = "listing_id") %>%
full_join(airbnb_lrev, by = "listing_id") 
head(airbnb)


#Checking for duplicated

sum(duplicated(airbnb$listing_id)) # no duolucates




```


<table>
<caption>A tibble: 6 × 7</caption>
<thead>
	<tr><th scope=col>listing_id</th><th scope=col>price</th><th scope=col>nbhood_full</th><th scope=col>description</th><th scope=col>room_type</th><th scope=col>host_name</th><th scope=col>last_review</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;fct&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;date&gt;</th></tr>
</thead>
<tbody>
	<tr><td>2595</td><td>225</td><td>Manhattan, Midtown        </td><td>Skylit Midtown Castle                    </td><td>entire home/apt</td><td>Jennifer   </td><td>2019-05-21</td></tr>
	<tr><td>3831</td><td> 89</td><td>Brooklyn, Clinton Hill    </td><td>Cozy Entire Floor of Brownstone          </td><td>entire home/apt</td><td>LisaRoxanne</td><td>2019-07-05</td></tr>
	<tr><td>5099</td><td>200</td><td>Manhattan, Murray Hill    </td><td>Large Cozy 1 BR Apartment In Midtown East</td><td>entire home/apt</td><td>Chris      </td><td>2019-06-22</td></tr>
	<tr><td>5178</td><td> 79</td><td>Manhattan, Hell's Kitchen </td><td>Large Furnished Room Near B'way          </td><td>private room   </td><td>Shunichi   </td><td>2019-06-24</td></tr>
	<tr><td>5238</td><td>150</td><td><span style=white-space:pre-wrap>Manhattan, Chinatown      </span></td><td><span style=white-space:pre-wrap>Cute &amp; Cozy Lower East Side 1 bdrm       </span></td><td>entire home/apt</td><td><span style=white-space:pre-wrap>Ben        </span></td><td>2019-06-09</td></tr>
	<tr><td>5295</td><td>135</td><td>Manhattan, Upper West Side</td><td>Beautiful 1br on Upper West Side         </td><td>entire home/apt</td><td>Lena       </td><td>2019-06-22</td></tr>
</tbody>
</table>




0



```R
#Finding the average price 

avg_price <- mean(airbnb$price)
avg_price

#Finding the number of private rooms 
nb_private_rooms <- as.numeric(airbnb %>%
count(room_type) %>%
filter(room_type == "private room"))
nb_private_rooms


review_dates <- airbnb %>%

summarize( last_reviewed = max(last_review), first_reviewed =  min(last_review))


```


141.777936451267



<style>
.list-inline {list-style: none; margin:0; padding: 0}
.list-inline>li {display: inline-block}
.list-inline>li:not(:last-child)::after {content: "\00b7"; padding: 0 .5ex}
</style>
<ol class=list-inline><li>2</li><li>11356</li></ol>




```R
# These packages need to be loaded in the first @tests cell
library(testthat) 
library(IRkernel.testthat)

# One or more tests of the student's code
# The @solution should pass the tests
# The purpose of the tests is to try to catch common errors and
# to give the student a hint on how to resolve these errors

# There are two tests in this cell. The first one tests that the
# correct package was loaded. The second one tests that the
# correct data were read in.

# Load the necessary packages
library(readr)
library(readxl)
library(dplyr)
library(stringr)

# Import CSV for prices
airbnb_price <- read_csv('datasets/airbnb_price.csv')

# Import TSV for room types
airbnb_room_type <- read_excel('datasets/airbnb_room_type.xlsx')

# Import Excel file for review dates
airbnb_last_review <- read_tsv('datasets/airbnb_last_review.tsv')

# Join the three data frames together into one
listings <- airbnb_price %>%
  inner_join(airbnb_room_type, by = "listing_id") %>%
  inner_join(airbnb_last_review, by = "listing_id")


# Question 1: What is the average listing price? 
# To convert price to numeric, remove "dollars" from each value
soln_avg_price <- listings %>%
  mutate(price_clean = str_remove(price, " dollars") %>%
        as.numeric()) %>%
  # Take the mean of price_clean
  summarize(avg_price = mean(price_clean)) %>%
  # Convert from a tibble to a single number
  as.numeric()


# Question 2: How many of the listings are private rooms? 
# Since there are differences in capitalization, make capitalization consistent
private_room_count <- listings %>%
  mutate(room_type = str_to_lower(room_type)) %>%
  # Then count the number of each room_type
  count(room_type) %>%
  # Get row containing count for private rooms only
  filter(room_type == "private room") 

# Extract number of rooms
soln_nb_private_rooms <- private_room_count$n


# Question 3: Which listing was most recently reviewed? 
# In order to use a function like max() on the last_review column, it needs to be converted to Date
soln_review_dates <- listings %>%
  # Convert to date using the format 'Month DD YYYY'
  mutate(last_review_date = as.Date(last_review, format = "%B %d %Y")) %>%
  # Use max() and min() to take the latest and earliest dates
  summarize(first_reviewed = min(last_review_date),
            last_reviewed = max(last_review_date))


run_tests({
    test_that("avg_price is correct", {
        expect_true(avg_price >= floor(soln_avg_price) | avg_price <= ceiling(soln_avg_price), 
                     info = "avg_price contains the wrong value.")
    })
    
    
    test_that("nb_private_rooms is correct", {
        expect_equal(nb_private_rooms, nb_private_rooms, 
                     info = "nb_private_rooms contains the wrong value. Make sure you have included values regardless of capitalization.")
    })
    test_that("nb_private_rooms takes capitalization into account", {
        expect_false(avg_price == 2248 | avg_price == 7241 | avg_price == 1867, 
                     info = "There are more private rooms than what is currently contained in `nb_private_rooms`. Make sure you are considering what to do with differences in capitalization.")
    })
    
    
    test_that("review_dates has 1 row", {
        expect_true(nrow(review_dates) == 1,
                   info = "review_dates should have one row that contains the earliest/latest dates.")
    })
    test_that("review_dates has 2 cols", {
        expect_true(ncol(review_dates) == 2,
                   info = "review_dates should have two columns - `first_reviewed` and `last_reviewed`.")
    })
    test_that("first_reviewed column exists in review_dates", {
        expect_true("first_reviewed" %in% colnames(review_dates),
                    info = "review_dates should have a column called first_reviewed.")    
    })
    test_that("last_reviewed column exists in review_dates", {
        expect_true("last_reviewed" %in% colnames(review_dates),
                    info = "review_dates should have a column called last_reviewed.")    
    })
    test_that("review_dates$first_reviewed is correct", {
        expect_equal(pull(review_dates, first_reviewed),
                     pull(soln_review_dates, first_reviewed),
                     info = "The first_reviewed column of review_dates contains the wrong value. It should contain one `Date` which is the earliest last_review date in the dataset.")
    })
    test_that("review_dates$last_reviewed is correct", {
        expect_equal(pull(review_dates, last_reviewed),
                     pull(soln_review_dates, last_reviewed),
                     info = "The last_reviewed column of review_dates contains the wrong value. It should contain one `Date` which is the latest last_review date in the dataset.")
    })
})
```

    
    Attaching package: ‘testthat’
    
    
    The following objects are masked from ‘package:readr’:
    
        edition_get, local_edition
    
    
    The following object is masked from ‘package:dplyr’:
    
        matches
    
    
    [1mRows: [22m[34m25209[39m [1mColumns: [22m[34m3[39m
    
    [36m──[39m [1mColumn specification[22m [36m────────────────────────────────────────────────────────[39m
    [1mDelimiter:[22m ","
    [31mchr[39m (2): price, nbhood_full
    [32mdbl[39m (1): listing_id
    
    
    [36mℹ[39m Use [30m[47m[30m[47m`spec()`[47m[30m[49m[39m to retrieve the full column specification for this data.
    [36mℹ[39m Specify the column types or set [30m[47m[30m[47m`show_col_types = FALSE`[47m[30m[49m[39m to quiet this message.
    
    [1mRows: [22m[34m25209[39m [1mColumns: [22m[34m3[39m
    
    [36m──[39m [1mColumn specification[22m [36m────────────────────────────────────────────────────────[39m
    [1mDelimiter:[22m "\t"
    [31mchr[39m (2): host_name, last_review
    [32mdbl[39m (1): listing_id
    
    
    [36mℹ[39m Use [30m[47m[30m[47m`spec()`[47m[30m[49m[39m to retrieve the full column specification for this data.
    [36mℹ[39m Specify the column types or set [30m[47m[30m[47m`show_col_types = FALSE`[47m[30m[49m[39m to quiet this message.
    







    9/9 tests passed

