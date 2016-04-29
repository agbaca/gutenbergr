<!-- README.md is generated from README.Rmd. Please edit that file -->



gutenbergr: R package to search and download public domain texts from Project Gutenberg
----------------

**Authors:** [David Robinson](http://varianceexplained.org/)<br/>
**License:** [MIT](https://opensource.org/licenses/MIT)

[![Build Status](https://travis-ci.org/dgrtwo/gutenbergr.svg?branch=master)](https://travis-ci.org/dgrtwo/gutenbergr)

Download and process public domain works from the [Project Gutenberg](https://www.gutenberg.org/) collection. Includes

* A function `gutenberg_download()` that downloads one or more works from Project Gutenberg by ID: e.g., `gutenberg_download(84)` downloads the text of Frankenstein.
* Metadata for all Project Gutenberg works as R datasets, so that they can be searched and filtered:
  * `gutenberg_metadata` contains information about each work, pairing Gutenberg ID with title, author, language, etc
  * `gutenberg_authors` contains information about each author, such as aliases and birth/death year
  * `gutenberg_subjects` contains pairings of works with Library of Congress subjects and topics

### Installation

Install using [devtools](https://github.com/hadley/devtools) with:


```r
devtools::install_github("dgrtwo/gutenbergr")
```

### Examples

The `gutenberg_works()` function retrieves, by default, a table of metadata for all unique English-language Project Gutenberg works that have text associated with them. (The `gutenberg_metadata` dataset has all Gutenberg works, unfiltered).



Suppose we wanted to download Emily Bronte's "Wuthering Heights." We could find the book's ID by filtering:


```r
library(dplyr)

gutenberg_works() %>%
  filter(title == "Wuthering Heights")
#> Source: local data frame [1 x 8]
#> 
#>   gutenberg_id             title        author gutenberg_author_id
#>          (int)             (chr)         (chr)               (int)
#> 1          768 Wuthering Heights Brontë, Emily                 405
#>   language                                 gutenberg_bookshelf
#>      (chr)                                               (chr)
#> 1       en Gothic Fiction/Best Books Ever Listings/Movie Books
#> Variables not shown: rights (chr), has_text (lgl)

# or just:
gutenberg_works(title == "Wuthering Heights")
#> Source: local data frame [1 x 8]
#> 
#>   gutenberg_id             title        author gutenberg_author_id
#>          (int)             (chr)         (chr)               (int)
#> 1          768 Wuthering Heights Brontë, Emily                 405
#>   language                                 gutenberg_bookshelf
#>      (chr)                                               (chr)
#> 1       en Gothic Fiction/Best Books Ever Listings/Movie Books
#> Variables not shown: rights (chr), has_text (lgl)
```

Since we see that it has `gutenberg_id` 768, we can download it with the `gutenberg_download()` function:


```r
wuthering_heights <- gutenberg_download(768)
wuthering_heights
#> Source: local data frame [12,085 x 2]
#> 
#>    gutenberg_id
#>           (int)
#> 1           768
#> 2           768
#> 3           768
#> 4           768
#> 5           768
#> 6           768
#> 7           768
#> 8           768
#> 9           768
#> 10          768
#> ..          ...
#>                                                                       text
#>                                                                      (chr)
#> 1                                                        WUTHERING HEIGHTS
#> 2                                                                         
#> 3                                                                         
#> 4                                                                CHAPTER I
#> 5                                                                         
#> 6                                                                         
#> 7    1801.--I have just returned from a visit to my landlord--the solitary
#> 8  neighbour that I shall be troubled with.  This is certainly a beautiful
#> 9  country!  In all England, I do not believe that I could have fixed on a
#> 10    situation so completely removed from the stir of society.  A perfect
#> ..                                                                     ...
```

`gutenberg_download` can download multiple books, which works well when downloading multiple from the metadata. For example, we could get the text of all Aristotle's works with:


```r
aristotle_metadata <- gutenberg_works(author == "Aristotle")
aristotle_books <- gutenberg_download(aristotle_metadata$gutenberg_id)

aristotle_books
#> Source: local data frame [39,950 x 2]
#> 
#>    gutenberg_id
#>           (int)
#> 1          1974
#> 2          1974
#> 3          1974
#> 4          1974
#> 5          1974
#> 6          1974
#> 7          1974
#> 8          1974
#> 9          1974
#> 10         1974
#> ..          ...
#>                                                                      text
#>                                                                     (chr)
#> 1                                                THE POETICS OF ARISTOTLE
#> 2                                                                        
#> 3                                                            By Aristotle
#> 4                                                                        
#> 5                                          A Translation By S. H. Butcher
#> 6                                                                        
#> 7                                                                        
#> 8         [Transcriber's Annotations and Conventions: the translator left
#> 9  intact some Greek words to illustrate a specific point of the original
#> 10   discourse. In this transcription, in order to retain the accuracy of
#> ..                                                                    ...
```

### FAQ

#### What do I do with the text once I have it?

We recommend the [tidytext](https://github.com/juliasilge/tidytext) package for tokenization and analysis!

#### How were the metadata R files generated?

See the [data-raw](data-raw) directory for the scripts that generate these datasets. As of now, these were generated from [the Project Gutenberg catalog](https://www.gutenberg.org/wiki/Gutenberg:Feeds#The_Complete_Project_Gutenberg_Catalog) on **April 27 2016**.

#### Do you respect the rules regarding robot access to Project Gutenberg?

Yes! The package respects [these rules](https://www.gutenberg.org/wiki/Gutenberg:Information_About_Robot_Access_to_our_Pages) and complies to the best of our ability. Namely:

* Project Gutenberg allows wget to harvest Project Gutenberg using [this list of links](http://www.gutenberg.org/robot/harvest?filetypes[]=html). The gutenbergr package visits that page once to find the recommended mirror for the user's location.
* We retrieve the book text directly from that mirror using links in the same format. For example, Frankenstein (book 84) is retrieved from `http://www.gutenberg.lib.md.us/8/84/84.zip`.
* We retrieve the .zip file rather than txt to minimize bandwidth on the mirror.

Still, this package is *not* the right way to download the entire Project Gutenberg corpus (or all from a particular language). For that, follow [their recommendation](https://www.gutenberg.org/wiki/Gutenberg:Information_About_Robot_Access_to_our_Pages) to use wget or set up a mirror. This package is recommended for downloading a single work, or works for a particular author or topic for analysis.

### Code of Conduct

This project is released with a [Contributor Code of Conduct](CONDUCT.md). By participating in this project you agree to abide by its terms.