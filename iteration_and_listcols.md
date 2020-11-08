Iteration and list columns
================
Daniel Ojeranti
11/8/2020

## Lists

You can put anything in a list

``` r
l = list(
  vec_numeric = 5:8,
  mat         = matrix(1:8, 2, 4),
  vec_char = c("My", "name", "is", "Jeff"),
  vec_logical = c(TRUE, TRUE, TRUE, FALSE),
  summary     = summary(rnorm(1000))
)

l$vec_numeric
```

    ## [1] 5 6 7 8

``` r
l[[1]]
```

    ## [1] 5 6 7 8

``` r
l[[1]][1:3]
```

    ## [1] 5 6 7

## For loop

Create list

``` r
list.norms = 
  list(
    a = rnorm(20, 3, 1),
    b = rnorm(40, 0, 5),
    c = rnorm(30, 10, .2),
    d = rnorm(20, -3, 1)
  )
```

Old function

``` r
mean.and.sd = function(x) {
  
  if (!is.numeric(x)) {
    stop("Argument x should be numeric")
  } 
  
  else if (length(x) == 1) {
    stop("Cannot be computed for length 1 vectors")
  }
  
  mean_x = mean(x)
  sd_x = sd(x)

  tibble(
    mean = mean_x, 
    sd = sd_x
  )
}
```

Apply function to lists

``` r
mean.and.sd(list.norms[[1]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.70  1.12

``` r
mean.and.sd(list.norms[[2]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.12  4.43

``` r
mean.and.sd(list.norms[[3]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.96 0.211

``` r
mean.and.sd(list.norms[[4]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.92  1.00

Lets use a for loop:

``` r
# We want output[[1]] = mean.and.sd(list.norms[[1]])

output = vector("list", length = 4)

for (i in 1:4 ){
  
output[[i]] = mean.and.sd(list.norms[[i]])

}
```

## Trying map

``` r
output = map(list.norms, mean.and.sd)
```

What if I want a different function…?

``` r
output =  map(list.norms, IQR)
```

## Different types of maps

``` r
output =  map_dbl(list.norms, IQR)

output =  map_df(list.norms, mean.and.sd, .id = "input")
```

## List columns

``` r
list.norms = 
  list(
    a = rnorm(20, 3, 1),
    b = rnorm(40, 0, 5),
    c = rnorm(30, 10, .2),
    d = rnorm(20, -3, 1)
  )

listcol.df =
  tibble(
    name = c("a", "b", "c", "d"),
    samp = list.norms
  )

listcol.df %>% pull(name)
```

    ## [1] "a" "b" "c" "d"

``` r
listcol.df %>% pull(samp)
```

    ## $a
    ##  [1] 3.0738305 3.7059456 3.3349801 3.5453878 1.5970941 3.6770539 2.2101996
    ##  [8] 2.5342711 2.8951479 1.3521489 2.9004630 2.5601424 2.2814885 2.4454024
    ## [15] 4.2454892 1.7410786 2.7846155 0.5280383 2.3258307 2.4987028
    ## 
    ## $b
    ##  [1]   7.7116289  -4.8100903  -4.3608977  -6.9881481   0.8990258   5.7704599
    ##  [7]  -5.9926681  -2.1286220   6.8315430  -3.4214870   3.4275610   1.9475177
    ## [13]  -6.5269796   6.0844401   3.9758701  -2.4410125  -4.5199673  -1.9520921
    ## [19]   4.0703171  -2.8212464  -9.3710266  -0.7145236   3.8595200  -5.7955897
    ## [25]  -1.1895776  -6.1109666   0.5840311  -0.6624981  -0.1684239  -3.1116321
    ## [31]  -3.5468173   4.3572271   0.5256901  -0.9346763 -16.0659427  -6.3780935
    ## [37]   3.8145316  -2.0340757  -6.0415889  -2.1966133
    ## 
    ## $c
    ##  [1]  9.924884  9.899712 10.099323 10.304176 10.197618 10.249225  9.934027
    ##  [8] 10.168869  9.803785  9.972156 10.437088  9.997434  9.938938  9.883157
    ## [15] 10.154254 10.421238 10.082431  9.947747 10.414757  9.844234 10.226307
    ## [22]  9.915731  9.795651 10.243661  9.640048  9.938350 10.003103  9.911536
    ## [29]  9.672398  9.871720
    ## 
    ## $d
    ##  [1] -4.557036 -1.076836 -4.856830 -5.106118 -2.302351 -2.092556 -3.195988
    ##  [8] -3.206820 -2.274957 -1.601281 -4.590555 -1.695503 -2.803988 -3.339343
    ## [15] -1.734856 -2.060226 -1.722048 -3.291160 -2.223828 -2.704274

``` r
listcol.df$samp[[1]] # First element from my list column
```

    ##  [1] 3.0738305 3.7059456 3.3349801 3.5453878 1.5970941 3.6770539 2.2101996
    ##  [8] 2.5342711 2.8951479 1.3521489 2.9004630 2.5601424 2.2814885 2.4454024
    ## [15] 4.2454892 1.7410786 2.7846155 0.5280383 2.3258307 2.4987028

``` r
mean.and.sd(listcol.df$samp[[1]]) #A pplying the function to first element
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.61 0.886

# Maping

``` r
map(listcol.df$samp, mean.and.sd)
```

    ## $a
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.61 0.886
    ## 
    ## $b
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -1.41  4.93
    ## 
    ## $c
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  10.0 0.212
    ## 
    ## $d
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.82  1.18

``` r
listcol.df = 
  listcol.df %>% 
  mutate(summary = map(samp, mean.and.sd))

listcol.df
```

    ## # A tibble: 4 x 3
    ##   name  samp         summary         
    ##   <chr> <named list> <named list>    
    ## 1 a     <dbl [20]>   <tibble [1 x 2]>
    ## 2 b     <dbl [40]>   <tibble [1 x 2]>
    ## 3 c     <dbl [30]>   <tibble [1 x 2]>
    ## 4 d     <dbl [20]>   <tibble [1 x 2]>

## Weather Data

``` r
weather.df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: C:\Users\danie\AppData\Local\cache/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2020-10-04 12:08:58 (7.537)

    ## file min/max dates: 1869-01-01 / 2020-10-31

    ## using cached file: C:\Users\danie\AppData\Local\cache/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2020-10-04 12:09:22 (1.703)

    ## file min/max dates: 1965-01-01 / 2020-03-31

    ## using cached file: C:\Users\danie\AppData\Local\cache/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2020-10-04 12:09:34 (0.882)

    ## file min/max dates: 1999-09-01 / 2020-10-31

Get our list columns…

``` r
weather.nest =
  weather.df %>% 
  nest(data = date:tmin)

weather.nest %>% pull(name)
```

    ## [1] "CentralPark_NY" "Waikiki_HA"     "Waterhole_WA"

``` r
weather.nest %>% pull(data)
```

    ## [[1]]
    ## # A tibble: 365 x 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01     0   8.9   4.4
    ##  2 2017-01-02    53   5     2.8
    ##  3 2017-01-03   147   6.1   3.9
    ##  4 2017-01-04     0  11.1   1.1
    ##  5 2017-01-05     0   1.1  -2.7
    ##  6 2017-01-06    13   0.6  -3.8
    ##  7 2017-01-07    81  -3.2  -6.6
    ##  8 2017-01-08     0  -3.8  -8.8
    ##  9 2017-01-09     0  -4.9  -9.9
    ## 10 2017-01-10     0   7.8  -6  
    ## # ... with 355 more rows
    ## 
    ## [[2]]
    ## # A tibble: 365 x 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01     0  26.7  16.7
    ##  2 2017-01-02     0  27.2  16.7
    ##  3 2017-01-03     0  27.8  17.2
    ##  4 2017-01-04     0  27.2  16.7
    ##  5 2017-01-05     0  27.8  16.7
    ##  6 2017-01-06     0  27.2  16.7
    ##  7 2017-01-07     0  27.2  16.7
    ##  8 2017-01-08     0  25.6  15  
    ##  9 2017-01-09     0  27.2  15.6
    ## 10 2017-01-10     0  28.3  17.2
    ## # ... with 355 more rows
    ## 
    ## [[3]]
    ## # A tibble: 365 x 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01   432  -6.8 -10.7
    ##  2 2017-01-02    25 -10.5 -12.4
    ##  3 2017-01-03     0  -8.9 -15.9
    ##  4 2017-01-04     0  -9.9 -15.5
    ##  5 2017-01-05     0  -5.9 -14.2
    ##  6 2017-01-06     0  -4.4 -11.3
    ##  7 2017-01-07    51   0.6 -11.5
    ##  8 2017-01-08    76   2.3  -1.2
    ##  9 2017-01-09    51  -1.2  -7  
    ## 10 2017-01-10     0  -5   -14.2
    ## # ... with 355 more rows

``` r
weather.nest$data[[3]]
```

    ## # A tibble: 365 x 4
    ##    date        prcp  tmax  tmin
    ##    <date>     <dbl> <dbl> <dbl>
    ##  1 2017-01-01   432  -6.8 -10.7
    ##  2 2017-01-02    25 -10.5 -12.4
    ##  3 2017-01-03     0  -8.9 -15.9
    ##  4 2017-01-04     0  -9.9 -15.5
    ##  5 2017-01-05     0  -5.9 -14.2
    ##  6 2017-01-06     0  -4.4 -11.3
    ##  7 2017-01-07    51   0.6 -11.5
    ##  8 2017-01-08    76   2.3  -1.2
    ##  9 2017-01-09    51  -1.2  -7  
    ## 10 2017-01-10     0  -5   -14.2
    ## # ... with 355 more rows

Want to regress tmax on tmin for each station.

``` r
lm( tmax ~ tmin, data = weather.nest$data[[1]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = weather.nest$data[[1]])
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.209        1.039

``` r
# Writing a function to do the regression

weather.lm = function(df){
  
lm( tmax ~ tmin, data = df)

  }

weather.lm(weather.nest$data[[1]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.209        1.039

``` r
output = vector("list", 3)

for (i in 1:3){
  
  output[[i]] = weather.lm(weather.nest$data[[i]])
}
```

``` r
#Transition in a map

map(weather.nest$data, weather.lm)
```

    ## [[1]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.209        1.039  
    ## 
    ## 
    ## [[2]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##     20.0966       0.4509  
    ## 
    ## 
    ## [[3]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.499        1.221

``` r
# What about a map in a list column

weather.nest %>% 
  mutate(models = map(data,weather.lm))
```

    ## # A tibble: 3 x 4
    ##   name           id          data               models
    ##   <chr>          <chr>       <list>             <list>
    ## 1 CentralPark_NY USW00094728 <tibble [365 x 4]> <lm>  
    ## 2 Waikiki_HA     USC00519397 <tibble [365 x 4]> <lm>  
    ## 3 Waterhole_WA   USS0023B17S <tibble [365 x 4]> <lm>
