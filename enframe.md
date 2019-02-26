enframe for display
================
Jiaxiang Li
2019-02-26

如果直接`unnest`那么`value`之前就没有名字

``` r
suppressMessages(library(tidyverse))
iris %>%
    group_by(Species) %>%
    summarise(pl_qtile = list(quantile(Petal.Length, c(0.25, 0.5, 0.75)))) ->
    tmp
tmp %>%
    unnest()
```

    ## # A tibble: 9 x 2
    ##   Species    pl_qtile
    ##   <fct>         <dbl>
    ## 1 setosa         1.4 
    ## 2 setosa         1.5 
    ## 3 setosa         1.58
    ## 4 versicolor     4   
    ## 5 versicolor     4.35
    ## 6 versicolor     4.6 
    ## 7 virginica      5.1 
    ## 8 virginica      5.55
    ## 9 virginica      5.88

``` r
tmp %>%
    mutate(pl_qtile = map(pl_qtile, enframe, name = "quantile")) %>% 
    unnest()
```

    ## # A tibble: 9 x 3
    ##   Species    quantile value
    ##   <fct>      <chr>    <dbl>
    ## 1 setosa     25%       1.4 
    ## 2 setosa     50%       1.5 
    ## 3 setosa     75%       1.58
    ## 4 versicolor 25%       4   
    ## 5 versicolor 50%       4.35
    ## 6 versicolor 75%       4.6 
    ## 7 virginica  25%       5.1 
    ## 8 virginica  50%       5.55
    ## 9 virginica  75%       5.88
