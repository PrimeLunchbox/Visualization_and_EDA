Visualization Part 1
================

Import the weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"), # assess data online allowed by rnoaa package
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") %>%
  mutate(
    name = case_match(
      id, 
      "USW00094728" ~ "CentralPark_NY", 
      "USW00022534" ~ "Molokai_HI",
      "USS0023B17S" ~ "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## using cached file: C:/Users/ASUS/AppData/Local/R/cache/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2025-09-25 10:34:01.511602 (8.763)

    ## file min/max dates: 1869-01-01 / 2025-09-30

    ## using cached file: C:/Users/ASUS/AppData/Local/R/cache/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2025-09-25 10:34:21.229769 (4.018)

    ## file min/max dates: 1949-10-01 / 2025-09-30

    ## using cached file: C:/Users/ASUS/AppData/Local/R/cache/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2025-09-25 10:34:27.538198 (1.08)

    ## file min/max dates: 1999-09-01 / 2025-09-30

\## Scatterplots

Create scatterplot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

new approach, same plot

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

save and edit plot object

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax))

weather_plot = geom_point()
```

## Advanced scatterplot

start with the same one

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() + 
  geom_smooth(se = FALSE) # does not show confidence interval band
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

what about ‘aes’ placement?

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name)) +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

facet some things

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.2, size = .2) + # alpha level defines transparency
  geom_smooth(se = FALSE, size = .2) +
  facet_grid(. ~ name) # show graph separately by column, according to name of the place.
```

    ## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
    ## ℹ Please use `linewidth` instead.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

by default, the larger value of variable in ‘alpha = variable’, the
lower the transparency. To reverse this, use ‘scale_alpha(trans =
“reverse”)’ for ***continuous variable*** and
scale_alpha_discrete(reverse = TRUE) for ***discrete variable***.

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name, alpha = tmin)) +
  geom_point() + 
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name) +
  scale_alpha(trans = "reverse")
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: The following aesthetics were dropped during statistical transformation: alpha.
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?
    ## The following aesthetics were dropped during statistical transformation: alpha.
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?
    ## The following aesthetics were dropped during statistical transformation: alpha.
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

combine elements and try new plot

``` r
weather_df %>%
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point(alpha = .5, aes(size = prcp)) + 
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 19 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## some small notes

how many geom have to exist

have whatever geoms you want

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

![](viz_1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

use a neat geom

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_density2d() +
  geom_point(alpha = .3)
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_density2d()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

## Univeriate plots

``` r
weather_df %>% 
  ggplot(aes(x = tmin)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](viz_1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

## add some color

“color =” give teh frame color “fill =” fill the object with color

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) + 
  # arrange the bars to be listed respectively
  geom_histogram(position = "dodge") + 
  facet_grid(. ~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](viz_1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

new geometry

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) + 
  geom_density(alpha = .3, adjust = .5)
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_density()`).

![](viz_1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

boxplots

``` r
weather_df %>%
  ggplot(aes(x = name, y = tmin)) + 
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_boxplot()`).

![](viz_1_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

trendy plots

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin, fill = name)) + 
  geom_violin(alpha = .5) +
  stat_summary()
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_summary()`).

    ## No summary function supplied, defaulting to `mean_se()`

![](viz_1_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

ridge plots

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = name)) + 
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.41

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_density_ridges()`).

![](viz_1_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

## save plots and embed

save

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .3)

ggsave("weather_plot.pdf", weather_plot, width = 8, height = 5)
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

embeding

``` r
weather_plot
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

``` r
weather_plot
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_1_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->
