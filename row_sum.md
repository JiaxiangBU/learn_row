row wise summary
================
Jiaxiang Li
2019-02-26

参考[github](https://github.com/jennybc/row-oriented-workflows/blob/master/ex09_row-summaries.md)

按行相加其实使用的是`purrr:pmap`函数。

``` r
library(tidyverse)
```

    ## -- Attaching packages -------------------------------------------------------------------------------- tidyverse 1.2.1 --

    ## √ ggplot2 3.1.0     √ purrr   0.2.5
    ## √ tibble  2.0.1     √ dplyr   0.7.8
    ## √ tidyr   0.8.2     √ stringr 1.3.1
    ## √ readr   1.3.1     √ forcats 0.3.0

    ## -- Conflicts ----------------------------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
df <- tribble(
  ~ name, ~ t1, ~t2, ~t3,
  "Abby",    1,   2,   3,
  "Bess",    4,   5,   6,
  "Carl",    7,   8,   9
)
```

``` r
df %>% 
    mutate(sum = pmap_dbl(list(t1,t2,t3),sum))
```

    ## # A tibble: 3 x 5
    ##   name     t1    t2    t3   sum
    ##   <chr> <dbl> <dbl> <dbl> <dbl>
    ## 1 Abby      1     2     3     6
    ## 2 Bess      4     5     6    15
    ## 3 Carl      7     8     9    24

简单的写法

``` r
df %>% 
    mutate(sum = pmap_dbl(select_if(.,is.numeric),sum))
```

    ## # A tibble: 3 x 5
    ##   name     t1    t2    t3   sum
    ##   <chr> <dbl> <dbl> <dbl> <dbl>
    ## 1 Abby      1     2     3     6
    ## 2 Bess      4     5     6    15
    ## 3 Carl      7     8     9    24

`pmap_dbl(...,sum)`=`rowSums`

``` r
df %>% 
    mutate(sum = rowSums(select_if(.,is.numeric)))
```

    ## # A tibble: 3 x 5
    ##   name     t1    t2    t3   sum
    ##   <chr> <dbl> <dbl> <dbl> <dbl>
    ## 1 Abby      1     2     3     6
    ## 2 Bess      4     5     6    15
    ## 3 Carl      7     8     9    24

`select`函数进行扩展

``` r
df %>%
    mutate(sum = 
               pmap_dbl(
                   select(.
                          ,starts_with('t')
                          )
                   ,sum
                   )
           )    
```

    ## # A tibble: 3 x 5
    ##   name     t1    t2    t3   sum
    ##   <chr> <dbl> <dbl> <dbl> <dbl>
    ## 1 Abby      1     2     3     6
    ## 2 Bess      4     5     6    15
    ## 3 Carl      7     8     9    24

``` r
df %>%
    mutate(sum = 
               pmap_dbl(
                   select(.
                          ,starts_with('t')
                          )
                   ,lift_vd(mean)
                   )
           )    
```

    ## # A tibble: 3 x 5
    ##   name     t1    t2    t3   sum
    ##   <chr> <dbl> <dbl> <dbl> <dbl>
    ## 1 Abby      1     2     3     2
    ## 2 Bess      4     5     6     5
    ## 3 Carl      7     8     9     8

`mean()`是向量化的，因此`pmap_dbl`对其产生报错，这里`lift_vd`将其去向量化。

`fun`换成其他函数
参考[github](https://github.com/jennybc/row-oriented-workflows/blob/master/ex06_runif-via-pmap.md)

``` r
df %>%
  set_names(
    'name','n','min','max'
  ) %>% 
  mutate(data = pmap(select(., -name), runif))
```

    ## # A tibble: 3 x 5
    ##   name      n   min   max data     
    ##   <chr> <dbl> <dbl> <dbl> <list>   
    ## 1 Abby      1     2     3 <dbl [1]>
    ## 2 Bess      4     5     6 <dbl [4]>
    ## 3 Carl      7     8     9 <dbl [7]>
