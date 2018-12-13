-   [Installation](#installation)
-   [Usage](#usage)

<center>
<img src="https://raw.githubusercontent.com/aljrico/aljrico.github.io/master/_posts/images/hogwarts-houses.png">
</center>

[![cran
version](http://www.r-pkg.org/badges/version/harrypotter)](https://cran.r-project.org/package=harrypotter)
[![rstudio mirror per-month
downloads](http://cranlogs.r-pkg.org/badges/harrypotter)](https://github.com/metacran/cranlogs.app)
[![rstudio mirror total
downloads](http://cranlogs.r-pkg.org/badges/grand-total/harrypotter?color=yellowgreen)](https://github.com/metacran/cranlogs.app)

This package provides a round of palettes inspired by the *Game of
Thrones* TV show.

At its first version, it simply contains the palettes of some of the
houses of Westeros. They have been chosen manually, taking into account
its consistency with all the existing branding of the franchise, but its
suitability for data visualisation.

> Most of us need to *listen* to the music to understand how beautiful
> it is. But often that’s how statistics are presented: we show the
> notes instead of playing the music.

The colour palette should be beautiful, useful for plotting data and
should relate to desired style; in this case, should relate to the *Song
of Ice and Fire* world. Some of the colours might change in future
versions, in order to find this balance between suitability for plotting
and relatability to the images from the universe.

<center>
<img src="https://raw.githubusercontent.com/aljrico/aljrico.github.io/master/_posts/images/show_scales2-1.png" >
</center>

Installation
------------

Just copy and execute this bunch of code and you’ll have the last
version of the package installed:

``` r
devtools::install_github("aljrico/gameofthrones")
```

And you can now use it:

``` r
library(gameofthrones)
```

Usage
-----

The default colour scale of the package is the one of the house
*Targaryen*. If you prefer to choose another one, you’ll need to specify
which house you want the palette from.

Let’s say that you want a palette made from the house **Targaryen**.

``` r
pal <- got(250, option = "Targaryen")
image(volcano, col = pal)
```

![](readme_raw_files/figure-markdown_github/unnamed-chunk-3-1.png)

<center>
<img src="https://raw.githubusercontent.com/aljrico/aljrico.github.io/master/_posts/images/unnamed-chunk-3-1.png" >
</center>

Or that you want burn something down using *wildfire*.

``` r
pal <- got(250, option = "Wildfire")
image(volcano, col = pal)
```

![](readme_raw_files/figure-markdown_github/unnamed-chunk-4-1.png)

### ggplot2

Of course, this package has specific functions to behave seamlessly with
the best data visiualisation library available. The package contains
colour scale functions for **ggplot2** plots: `scale_color_got()` and
`scale_fill_got()`.

Here is a made up example using the colours from the house of
**Martell**,

``` r
library(ggplot2)
ggplot(data.frame(x = rnorm(1e4), y = rnorm(1e4)), aes(x = x, y = y)) +
  geom_hex() + 
    coord_fixed() +
  scale_fill_got(option = "Martell") + 
    theme_bw()
```

![](readme_raw_files/figure-markdown_github/unnamed-chunk-5-1.png)

and **Baratheon**

``` r
ggplot(data.frame(x = rnorm(1e4), y = rnorm(1e4)), aes(x = x, y = y)) +
  geom_hex() + 
    coord_fixed() +
  scale_fill_got(option = "Baratheon") + 
    theme_bw()
```

![](readme_raw_files/figure-markdown_github/unnamed-chunk-6-1.png)

You can also use it to create this cloropeths of the U.S Unemployment:

``` r
unemp <- read.csv("http://datasets.flowingdata.com/unemployment09.csv",
                  header = FALSE, stringsAsFactors = FALSE)
names(unemp) <- c("id", "state_fips", "county_fips", "name", "year",
                  "?", "?", "?", "rate")
unemp$county <- tolower(gsub(" County, [A-Z]{2}", "", unemp$name))
unemp$county <- gsub("^(.*) parish, ..$","\\1", unemp$county)
unemp$state <- gsub("^.*([A-Z]{2}).*$", "\\1", unemp$name)
county_df <- map_data("county", projection = "albers", parameters = c(39, 45))
```

    ## 
    ## Attaching package: 'maps'

    ## The following object is masked _by_ '.GlobalEnv':
    ## 
    ##     unemp

``` r
names(county_df) <- c("long", "lat", "group", "order", "state_name", "county")
county_df$state <- state.abb[match(county_df$state_name, tolower(state.name))]
county_df$state_name <- NULL
state_df <- map_data("state", projection = "albers", parameters = c(39, 45))
choropleth <- merge(county_df, unemp, by = c("state", "county"))
choropleth <- choropleth[order(choropleth$order), ]
ggplot(choropleth, aes(long, lat, group = group)) +
  geom_polygon(aes(fill = rate), colour = alpha("white", 1 / 2), size = 0.2) +
  geom_polygon(data = state_df, colour = "white", fill = NA) +
  coord_fixed() +
  theme_minimal() +
  ggtitle("US unemployment rate by county") +
    labs(subtitle = "House Tully palette") +
  theme(axis.line = element_blank(), axis.text = element_blank(),
        axis.ticks = element_blank(), axis.title = element_blank()) +
  scale_fill_got(option = "Tully")
```

![](readme_raw_files/figure-markdown_github/unnamed-chunk-7-1.png)

But what if you want discrete scales? These functions also can be used
for discrete scales with the argument `discrete = TRUE`. This argument,
when TRUE, sets a finite number of sufficiently spaced colours within
the selected palette to plot your data.

``` r
library("ggplot2")
ggplot(mtcars, aes(factor(cyl), fill=factor(vs))) +  
    geom_bar(colour = "black") +
  scale_fill_got(discrete = TRUE, option = "Tyrell")
```

![](readme_raw_files/figure-markdown_github/unnamed-chunk-8-1.png)

Or you can do that using the shorter version: `scale_fill_got_d()`
stands for `scale_fill_got(discrete = TRUE)`.

``` r
library(gridExtra)

gg1 <- ggplot(diamonds, aes(carat, fill = cut)) +
  geom_density(position = "stack") +
    scale_fill_got(discrete = TRUE, option = "stark2")

gg2 <- ggplot(mpg, aes(class)) +
    geom_bar(aes(fill = drv), position = position_stack(reverse = TRUE), colour = "black") +
 coord_flip() +
    scale_fill_got(discrete = TRUE, option = "Tully") +
 theme(legend.position = "top") +
    ylab("") +
    xlab("Class")

grid.arrange(gg1,gg2)
```

![](readme_raw_files/figure-markdown_github/unnamed-chunk-9-1.png)

``` r
gg <- ggplot(diamonds, aes(carat, stat(count), fill = cut)) +
  geom_density(position = "fill") +
    xlab("") + 
    ylab("")
gg1 <- gg +
    scale_fill_got(discrete = TRUE, option = "white_walkers", name = "") +
    ggtitle("White Walkers Palette")

gg2 <- gg +
    scale_fill_got(discrete = TRUE, option = "Lannister", name = "") +
    ggtitle("House Lannister Palette")

gg3 <-gg +
    scale_fill_got(discrete = TRUE, option = "Tyrell", name = "") +
    ggtitle("House Tyrell Palette")

gg4 <- gg +
    scale_fill_got(discrete = TRUE, option = "Stark", name = "") +
    ggtitle("House Stark Palette")

gg5 <- gg +
    scale_fill_got(discrete = TRUE, option = "Tully", name = "") +
    ggtitle("House Tully Palette")

gg6 <- gg +
    scale_fill_got(discrete = TRUE, option = "Martell", name = "") +
    ggtitle("House Martell Palette")

grid.arrange(gg1,gg2,gg3,gg4,gg5,gg6)
```

![](readme_raw_files/figure-markdown_github/unnamed-chunk-10-1.png)