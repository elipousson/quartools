
<!-- README.md is generated from README.Rmd. Please edit that file -->

# quartools

<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
[![Codecov test
coverage](https://codecov.io/gh/ElianHugh/quartools/branch/main/graph/badge.svg)](https://app.codecov.io/gh/ElianHugh/quartools?branch=main)
<!-- badges: end -->

*quartools* allows for the creation of quarto-compliant markdown via R
function calls. As *quartools* generates quarto-compliant markdown, and
*not* HTML tags, the content will work on any quarto output format.

## Why quartools?

At work, I ran into an issue where I was generating hundreds of
parameterised reports that would require parts of the report to be
dynamically populated. I found myself leaning on R for programmatic
markup creation, which meant that I could have one master document that
I worked on. My prototype version (in other words, functions I threw
together for work) required a lot of constant chunk configuration, and
wasn’t particularly user-friendly nor elegant. *quartools* is a more
streamlined version of my prototype, with the added benefit of it
requiring little to no setup on the end user’s part.

## Installation

### Release build

``` r
install.packages('quartools', repos = 'https://elianhugh.r-universe.dev')
```

### Development build

``` r
# install.packages("devtools")
devtools::install_github("ElianHugh/quartools")
```

## Example

``` r
library(quartools)
```

### Basic usage

The simplest way to create a div block element with quartools is via the
`qto_div` function. Note that the chunk configuration of `results: asis`
is not necessary.

``` r
div_example <- qto_div(
  "It is also possible to supply attributes to the div block element via the `id`, `class`, and `.attributes` parameters.",
  id = "qto-div-example"
)

print(div_example)
```


    ::: {#qto-div-example}
    It is also possible to supply attributes to the div block element via the `id`, `class`, and `.attributes` parameters. 
    :::

The `qto_callout()` function creates a callout styled div:

``` r
callout_example <- qto_callout(
 "Callouts provide a simple way to attract attention, for example, to this warning.",
 type = "warning"
)

print(callout_example)
```


    ::: {.callout-warning}
    Callouts provide a simple way to attract attention, for example, to this warning. 
    :::

Other simple functions include `qto_heading` or `qto_definition_list`:

``` r
heading_example <- qto_heading("Heading 1")

print(heading_example)
```

    # Heading 1

``` r
definition_list_example <- qto_definition_list("Term" = "Definition")

print(definition_list_example)
```



    :   Definition

### Using loops to create Quarto Markdown

We can leverage the `apply` family of functions ability to loop over
list elements to simplify creating many divs at once.

Normally this would require a workaround to allow for printing directly
as markdown content, but `{quartools}` exports the `mdapply` function
(read: “markdown apply”) which allows for directly printing the result
as valid markdown content:

``` r
input <- list(
  list(type = "tip", msg = "a tip"),
  list(type = "warning", msg = "a warning"),
  list(type = "message", msg = "a message")
)

mdapply(input, function(x) div(x$msg, attr = sprintf(".callout-%s", x$type))) |>
  print()
```



    :::{.callout-tip}

    a tip

    :::


    :::{.callout-warning}

    a warning

    :::


    :::{.callout-message}

    a message

    :::

`map_qto()` is a similar alternative and allows users to set the
function using the `.type` parameter:

``` r
qto_list <- map_qto(list("This is a note.", "And this is a note.", "And this is a note"), .type = "callout")

print(qto_list)
```

    [[1]]

    ::: {.callout-note}
    This is a note. 
    :::


    [[2]]

    ::: {.callout-note}
    And this is a note. 
    :::


    [[3]]

    ::: {.callout-note}
    And this is a note 
    :::
