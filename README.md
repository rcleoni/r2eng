
<!-- README.md is generated from README.Rmd. Please edit that file -->

# {r2eng}

ɑː ˈtuː /eng/

<!-- badges: start -->

[![Project Status: WIP – Initial development is in progress, but there
has not yet been a stable, usable release suitable for the
public.](https://www.repostatus.org/badges/latest/wip.svg)](https://www.repostatus.org/#wip)
<!-- badges: end -->

Make R speakable\!

The goal of {r2eng} (as in ‘R to English’) is to take an R expression
and ‘translate’ it to an English sentence.

The package is intended to:

  - improve communication between teachers and learners
  - make R discussions in English more accessible to non-English
    speakers
  - provide an extra audio evaluation tool for users who are blind or
    have low vision
  - for any R user that’s curious about how to ‘speak R’

The project was inspired by [Amelia
McNamara](https://twitter.com/AmeliaMN)‘s useR\! 2020 talk called
’Speaking R’ ([YouTube](https://www.youtube.com/watch?v=ckW9sSdIVAc),
[slides](https://www.amelia.mn/SpeakingR/#1)).

This project is a work in progress and highly opinionated. Contributions
are welcome, but please see the [Code of Conduct](#conduct).

## Installation

You can install the development version of {r2eng} from GitHub with:

``` r
remotes::install_github("matt-dray/r2eng")
```

This package depends on {lintr}, {purrr} and {rlang}.

## Examples

The main function in the package is `r2eng()`. It uses [non-standard
evaluation](http://adv-r.had.co.nz/Computing-on-the-language.html), so
you pass it a bare R expression like this:

``` r
r2eng::r2eng(variable <- 1, speak = TRUE)
# Original expression: variable <- 1
# English expression: variable gets 1
```

Set speak to `TRUE` for a system call that will read the English
sentence out loud.

A more complex example:

``` r
obj <- r2eng::r2eng(
  hello <- c(TRUE, FALSE, 'banana' %in% c('apple', 'orange')),
  speak = FALSE
)

obj
# Original expression: hello <- c(TRUE, FALSE, "banana" %in% c("apple", "orange"))
# English expression: hello gets a vector of open paren TRUE , FALSE , string "banana" matches a vector of open paren string "apple" , string "orange" close paren close paren
```

### Methods

An `r2eng` object has two methods: `speak` and `evaluate`.

Use `speak` to speak the English sentence.

``` r
r2eng::speak(obj)
```

Use `evaluate` to evaluate the expression.

``` r
r2eng::evaluate(obj)
hello
# [1]  TRUE FALSE FALSE
```

From your r2eng object you can access the original R expression
(`r_expression`), English translation (`eng_expression`) and quoted
expression (`quoted_expression`). You can also see the parse tree output
via {lintr}:

``` r
head(obj$translation_map)
#                   token  text          eng
# 45                 expr                   
# 1                SYMBOL hello        hello
# 3                  expr                   
# 2           LEFT_ASSIGN    <-         gets
# 43                 expr                   
# 4  SYMBOL_FUNCTION_CALL     c a vector of
```

### Further examples

Here’s an example using the pipe (`%>%`) and two types of ‘equals’:

``` r
library(magrittr)
r2eng::r2eng(
  mtcars %>% filter(mpg > 22) %>% mutate(gear4 = gear == 4),
  speak = FALSE
)
# Original expression: mtcars %>% filter(mpg > 22) %>% mutate(gear4 = gear == 4)
# English expression: mtcars then filter of open paren mpg is greater than 22 close paren then mutate of open paren gear4 = gear double equal 4 close paren
```

This example uses the ‘plus’ constructor from {ggplot2}:

``` r
r2eng::r2eng(
  ggplot(diamonds, aes(x=carat, y=price, color=cut)) + geom_point() + geom_smooth(),
  speak = FALSE
)
# Original expression: ggplot(diamonds, aes(x = carat, y = price, color = cut)) + geom_point() + 
#  Original expression:     geom_smooth()
# English expression: ggplot of open paren diamonds , aes of open paren x = carat , y = price , color = cut close paren close paren + geom_point of open paren close paren + geom_smooth of open paren close paren
```

This example shows what happens when you pass vectors:

``` r
r2eng::r2eng(plot(x = c(1, 2, 3), y = c(5, 6, 7)), speak = FALSE)
# Original expression: plot(x = c(1, 2, 3), y = c(5, 6, 7))
# English expression: plot of open paren x = a vector of open paren 1 , 2 , 3 close paren , y = a vector of open paren 5 , 6 , 7 close paren close paren
```

### Passing a string

The `r2eng()` function understands the meaning of `=` when used for
assignment versus specifying arguments, but feeding an expression such
as `x = c(1, 2, 3)` would confuse `r2eng()` that you want to pass an
argument `c(1, 2, 3)` to the parameter `x`.

This is because `r2eng()` uses [non-standard
evaluation](http://adv-r.had.co.nz/Computing-on-the-language.html).

In such cases, you must use `r2eng_from_string()` instead:

``` r
r2eng::r2eng_from_string("x = c(1, 2, 3)", speak = FALSE)
# Original expression: x = c(1, 2, 3)
# English expression: x gets a vector of open paren 1 , 2 , 3 close paren
```

Another exceptional case for `r2eng_from_string()` is when piping and
expression:

``` r
"non_english <- c('ceci n est pas une pipe', 'Ich bin ein Berliner', '我其實唔識講廣東話')" %>% 
  r2eng::r2eng_from_string(speak = TRUE)
# Original expression: non_english <- c("ceci n est pas une pipe", "Ich bin ein Berliner", 
#  Original expression:     "我其實唔識講廣東話")
# English expression: non_english gets a vector of open paren string "ceci n est pas une pipe" , string "Ich bin ein Berliner" , string "我其實唔識講廣東話" close paren
```

## Work in progress (WIP)

There is much to do.

  - [ ] Expand the dictionary
  - [x] Split out parentheses for evaluation
  - [ ] Ensure multi-line translation
  - [x] Smart check of expression structure (e.g. ‘=’ will be used as
    gets if used for assignment, but will be ‘is’ elsewhere)
  - [ ] Allow for variant opinions on translations
  - [ ] Account for dialects (dollar, formula, tidyverse, etc, notation)
  - [ ] Test\!
  - [ ] Add documentation (vignettes, {pkgdown} site)

## Code of Conduct

Contributions are welcome from everyone.

Please note that the {r2eng} project is released with a [Contributor
Code of
Conduct](https://contributor-covenant.org/version/2/0/CODE_OF_CONDUCT.html).
By contributing to this project, you agree to abide by its terms.
