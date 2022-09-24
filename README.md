HW1
================
Andrew Li
2022-09-24

1.  How many flights have a missing dep_time? What other variables are
    missing? What might these rows represent?

``` r
flights %>%
  filter(is.na(dep_time))
```

    ## # A tibble: 8,255 x 19
    ##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
    ##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
    ##  1  2013     1     1       NA           1630        NA       NA           1815
    ##  2  2013     1     1       NA           1935        NA       NA           2240
    ##  3  2013     1     1       NA           1500        NA       NA           1825
    ##  4  2013     1     1       NA            600        NA       NA            901
    ##  5  2013     1     2       NA           1540        NA       NA           1747
    ##  6  2013     1     2       NA           1620        NA       NA           1746
    ##  7  2013     1     2       NA           1355        NA       NA           1459
    ##  8  2013     1     2       NA           1420        NA       NA           1644
    ##  9  2013     1     2       NA           1321        NA       NA           1536
    ## 10  2013     1     2       NA           1545        NA       NA           1910
    ## # ... with 8,245 more rows, and 11 more variables: arr_delay <dbl>,
    ## #   carrier <chr>, flight <int>, tailnum <chr>, origin <chr>, dest <chr>,
    ## #   air_time <dbl>, distance <dbl>, hour <dbl>, minute <dbl>, time_hour <dttm>

Here we can see that 8,255 flights are missing a `dep_time` value. Other
missing variables include `dep_delay`, `arr_time`, `arr_delay`, and
`air_time`. These rows may represent any flights that were canceled.

2.  Currently `dep_time` and `sched_dep_time` are convenient to look at,
    but hard to compute with because they’re not really continuous
    numbers. Convert them to a more convenient representation of number
    of minutes since midnight.

``` r
flights %>%
  mutate(dep_time = ((dep_time %/% 100) * 60) + dep_time %% 100,
         sched_dep_time = ((sched_dep_time %/% 100) * 60) + sched_dep_time %% 100)
```

    ## # A tibble: 336,776 x 19
    ##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
    ##    <int> <int> <int>    <dbl>          <dbl>     <dbl>    <int>          <int>
    ##  1  2013     1     1      317            315         2      830            819
    ##  2  2013     1     1      333            329         4      850            830
    ##  3  2013     1     1      342            340         2      923            850
    ##  4  2013     1     1      344            345        -1     1004           1022
    ##  5  2013     1     1      354            360        -6      812            837
    ##  6  2013     1     1      354            358        -4      740            728
    ##  7  2013     1     1      355            360        -5      913            854
    ##  8  2013     1     1      357            360        -3      709            723
    ##  9  2013     1     1      357            360        -3      838            846
    ## 10  2013     1     1      358            360        -2      753            745
    ## # ... with 336,766 more rows, and 11 more variables: arr_delay <dbl>,
    ## #   carrier <chr>, flight <int>, tailnum <chr>, origin <chr>, dest <chr>,
    ## #   air_time <dbl>, distance <dbl>, hour <dbl>, minute <dbl>, time_hour <dttm>

3.  Look at the number of canceled flights per day. Is there a pattern?
    Is the proportion of canceled flights related to the average delay?
    Use multiple `dyplr` operations, all on one line, concluding with
    `ggplot(aes(x= ,y=)) + geom_point()`

``` r
flights %>%
  group_by(day) %>%
  summarise(average_delay = mean(dep_delay, na.rm=T),
            total_flights = n(),
            canceled_flights = sum(is.na(dep_delay)),
            proportion_canceled = canceled_flights / total_flights) %>%
  ggplot(aes(x = average_delay, y = proportion_canceled)) + geom_point()
```

![](README_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

There seems to be a positive correlation between the `average_delay` and
`proportion_cancelled`. That is, the larger the departure delay, the
more likely it is that the flight is cancelled altogether.
