writing functions
================
Daniel Ojeranti
11/7/2020

``` r
library(tidyverse)
```

    ## -- Attaching packages ------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.2     v purrr   0.3.4
    ## v tibble  3.0.3     v dplyr   1.0.2
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.5.0

    ## -- Conflicts ---------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(rvest)
```

    ## Loading required package: xml2

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     pluck

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

# Somthing simple

``` r
set.seed(1)

x.vec = rnorm(25, mean = 5, sd = 3)

(x.vec - mean(x.vec)) / sd(x.vec)
```

    ##  [1] -0.83687228  0.01576465 -1.05703126  1.50152998  0.16928872 -1.04107494
    ##  [7]  0.33550276  0.59957343  0.42849461 -0.49894708  1.41364561  0.23279252
    ## [13] -0.83138529 -2.50852027  1.00648110 -0.22481531 -0.19456260  0.81587675
    ## [19]  0.68682298  0.44756609  0.78971253  0.64568566 -0.09904161 -2.27133861
    ## [25]  0.47485186

Want a function to compute z scores

``` r
z.scores = function(x) {
  
  z = (x - mean(x)) / sd(x)
  z
}

z.scores(x.vec)
```

    ##  [1] -0.83687228  0.01576465 -1.05703126  1.50152998  0.16928872 -1.04107494
    ##  [7]  0.33550276  0.59957343  0.42849461 -0.49894708  1.41364561  0.23279252
    ## [13] -0.83138529 -2.50852027  1.00648110 -0.22481531 -0.19456260  0.81587675
    ## [19]  0.68682298  0.44756609  0.78971253  0.64568566 -0.09904161 -2.27133861
    ## [25]  0.47485186

Trying function on other things

``` r
z.scores(3)
```

    ## [1] NA

``` r
z.scores("Mehoy Nehoy")
```

    ## Warning in mean.default(x): argument is not numeric or logical: returning NA

    ## Error in x - mean(x): non-numeric argument to binary operator

``` r
z.scores(mtcars)
```

    ## Warning in mean.default(x): argument is not numeric or logical: returning NA

    ## Error in is.data.frame(x): 'list' object cannot be coerced to type 'double'

``` r
z.scores(c(TRUE ,TRUE ,FALSE ,TRUE))
```

    ## [1]  0.5  0.5 -1.5  0.5

update function

``` r
z.scores = function(x) {
  
  if (!is.numeric(x)){
    stop("Input must be numeric")
  }
  

  if (length(x) < 3){
    stop("Input must have at least 3 numbers")
  }
  z = (x - mean(x)) / sd(x)
  z
}
```

These should give errors

``` r
z.scores(3)
```

    ## Error in z.scores(3): Input must have at least 3 numbers

``` r
z.scores("Mehoy Nehoy")
```

    ## Error in z.scores("Mehoy Nehoy"): Input must be numeric

``` r
z.scores(mtcars)
```

    ## Error in z.scores(mtcars): Input must be numeric

``` r
z.scores(c(TRUE ,TRUE ,FALSE ,TRUE))
```

    ## Error in z.scores(c(TRUE, TRUE, FALSE, TRUE)): Input must be numeric

# Multiple outputs

``` r
mean.and.Sd = function(x) {
  
  if (!is.numeric(x)){
    stop("Input must be numeric")
  }

  if (length(x) < 3){
    stop("Input must have at least 3 numbers")
  }
  
mean.x = mean(x)
sd.x = sd(x)

tibble(
  mean = mean.x,
  sd = sd.x
)

}
```

Check that function works

``` r
x.vec = rnorm(100, mean = 3, sd = 5)
mean.and.Sd(x.vec)
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.46  4.29

# Multiple Inputs

I’d like to do this with a function

``` r
sim.data =
  tibble(
    x = rnorm(n = 100, mean = 4, sd = 3)
  )

sim.data %>% 
  summarize(
    mean = mean(x),
    sd = sd(x)
)
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.85  3.11

``` r
sim.mean.sd = function(n, mu, sigma) {
  
# Generates a data frame that follows the n,mu, sigma from the function command
  
  sim.data =
  tibble(
    x = rnorm(n = n, mean = mu, sd = sigma)
  )

# Takes the information from the tibble and summarizes(outputs) the mean an standard deviation from it.

sim.data %>% 
  summarize(
    mean = mean(x),
    sd = sd(x)
)
  
}
sim.mean.sd(100,6,3)
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  6.24  3.02

## Review Napolean Dynomite

``` r
url = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber=1"

dynamite.html = read_html(url)

review_titles = 
  dynamite.html %>%
  html_nodes(".a-text-bold span") %>%
  html_text()

review_stars = 
  dynamite.html %>%
  html_nodes("#cm_cr-review_list .review-rating") %>%
  html_text() %>%
  str_extract("^\\d") %>%
  as.numeric()

review_text = 
  dynamite.html %>%
  html_nodes(".review-text-content span") %>%
  html_text() %>% 
  str_replace_all("\n", "") %>% 
  str_trim()

reviews = tibble(
  title = review_titles,
  stars = review_stars,
  text = review_text
)
```

What about the next page of reviews… Lets turn that code into a function

``` r
read.page.reviews = function(url) {
  
  html = read_html(url)
  
  review_titles = 
    html %>%
    html_nodes(".a-text-bold span") %>%
    html_text()
  
  review_stars = 
    html %>%
    html_nodes("#cm_cr-review_list .review-rating") %>%
    html_text() %>%
    str_extract("^\\d") %>%
    as.numeric()
  
  review_text = 
    html %>%
    html_nodes(".review-text-content span") %>%
    html_text() %>% 
    str_replace_all("\n", "") %>% 
    str_trim()
  
  reviews =
    tibble(
    title = review_titles,
    stars = review_stars,
    text = review_text
  )

}
```

Test the function

``` r
dynomite.url = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber=1"

read.page.reviews(dynomite.url)
```

Lets read a few pages of reviews

From Step 1:

``` r
read.page.reviews = function(url) {
  
  html = read_html(url)
  
  review_titles = 
    html %>%
    html_nodes(".a-text-bold span") %>%
    html_text()
  
  review_stars = 
    html %>%
    html_nodes("#cm_cr-review_list .review-rating") %>%
    html_text() %>%
    str_extract("^\\d") %>%
    as.numeric()
  
  review_text = 
    html %>%
    html_nodes(".review-text-content span") %>%
    html_text() %>% 
    str_replace_all("\n", "") %>% 
    str_trim()
  
  reviews =
    tibble(
    title = review_titles,
    stars = review_stars,
    text = review_text
  )

}
```

``` r
dynomite.url.base = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber="

dynomite.urls = str_c(dynomite.url.base, 1:5)

dynamite.reviews = 
  bind_rows(
    read.page.reviews(dynomite.urls[1]),
    read.page.reviews(dynomite.urls[2]),
    read.page.reviews(dynomite.urls[3]),
    read.page.reviews(dynomite.urls[4]),
    read.page.reviews(dynomite.urls[5])
)   
```

# Mean scoping example

``` r
f = function(x){
  z = x + y
  z
}

x = 1
y = 2

f(x = y)
```

    ## [1] 4
