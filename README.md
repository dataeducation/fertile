
<!-- README.md is generated from README.Rmd. Please edit that file -->
[![Travis-CI Build Status](https://travis-ci.org/baumer-lab/fertile.svg?branch=master)](https://travis-ci.org/baumer-lab/fertile)

fertile: creating optimal conditions for reproducibility
========================================================

The goal of `fertile` is to make creating a reproducible project as easy as possible, for users of all levels of sophistication.

`fertile` provides a wide variety of checks that can be run on your project to test different aspects of its reproducibility--including clean project structure, portability of paths, and use of randomnes--as well as several functions that will create reproducibility reports for you with information about referenced packages and file paths.

Installation
------------

You can install `fertile` from GitHub with:

``` r
# install.packages("devtools")
devtools::install_github("baumer-lab/fertile")
```

Reproducibility Reports
-----------------------

From within any R project directory, you can create a report of reproducibility with `proj_test()` or its smaller component functions.

This report will contain information about packages referenced in project code, files in your directory and suggestions for reorganizing them, as well a list of absolute and/or non-portable paths passed to functions in your code.

``` r
proj_test("tests/testthat/project_noob/")
#> ── Checking for reproducibility ─────────────────────── fertile 0.0.0.9026 ──
#> ── Generating reproducibility report... ─────────────── fertile 0.0.0.9026 ──
#> Checking for absolute paths...
#> Checking for paths outside project directory...
#> ── Analysis of reproducibility for project_noob ─────── fertile 0.0.0.9026 ──
#> ──   Packages referenced in source code ─────────────── fertile 0.0.0.9026 ──
#> # A tibble: 3 x 3
#>   package       N used_in                               
#>   <chr>     <int> <chr>                                 
#> 1 fertile       1 tests/testthat/project_noob/simple.Rmd
#> 2 readr         1 tests/testthat/project_noob/simple.Rmd
#> 3 rmarkdown     1 tests/testthat/project_noob/simple.Rmd
#> ──   Files present in directory ─────────────────────── fertile 0.0.0.9026 ──
#> # A tibble: 2 x 4
#>   file               ext          size mime           
#>   <fs::path>         <chr> <fs::bytes> <chr>          
#> 1 project_noob.Rproj Rproj         204 text/rstudio   
#> 2 simple.Rmd         Rmd           400 text/x-markdown
#> ──   Suggestions for moving files ───────────────────── fertile 0.0.0.9026 ──
#> # A tibble: 1 x 3
#>   path_rel   dir_rel    cmd                                                     
#>   <fs::path> <fs::path> <chr>                                                   
#> 1 simple.Rmd vignettes  file_move('tests/testthat/project_noob/simple.Rmd', fs:…
#> ──   Problematic paths logged ───────────────────────── fertile 0.0.0.9026 ──
#> # A tibble: 2 x 6
#>   path     path_abs            func    path1   problem         solution         
#>   <chr>    <chr>               <chr>   <chr>   <chr>           <chr>            
#> 1 ../data… /Users/audreyberti… readr:… ../dat… Path is not co… Move the file an…
#> 2 ../data… /Users/audreyberti… utils:… ../dat… Path is not co… Move the file an…
```

Reproducibility Checks
----------------------

There are several functions allowing you to run checks on project reproducibility.

The function `proj_check()` runs 15 different tests to check your project for reproduciblity, and provides a summary of checks that you passed as well as ones to work on and how to improve them. Each of these checks can also be run individually.

`proj_check_some()` will complete a selection of the checks run by `proj_check()`, specified by the user through a `dplyr::select`-style statement.

``` r
proj_check_some("tests/testthat/project_miceps", contains("tidy"), ends_with("root"), has_only_used_files)
#> ✔ Checking for nested .Rproj files within project
#> ✔ Checking for no *.R scripts at root level
#> ✔ Checking for no *.rda files at root level
#> ✔ Checking for no A/V files at root level
#> ● Checking for no image files at root level
#>    Problem: Image files in root directory clutter project
#>    Solution: Move source files to img/ directory
#>    See for help: ?fs::file_move
#> # A tibble: 2 x 2
#>   culprit                          expr                                         
#>   <fs::path>                       <glue>                                       
#> 1 tests/testthat/project_miceps/c… fs::file_move('tests/testthat/project_miceps…
#> 2 tests/testthat/project_miceps/p… fs::file_move('tests/testthat/project_miceps…
#> ● Checking for no raw data files at root level
#>    Problem: Raw data files in root directory clutter project
#>    Solution: Move raw data files to data-raw/ directory
#>    See for help: ?fs::file_move
#> # A tibble: 3 x 2
#>   culprit                           expr                                        
#>   <fs::path>                        <glue>                                      
#> 1 tests/testthat/project_miceps/Bl… fs::file_move('tests/testthat/project_micep…
#> 2 tests/testthat/project_miceps/CS… fs::file_move('tests/testthat/project_micep…
#> 3 tests/testthat/project_miceps/mi… fs::file_move('tests/testthat/project_micep…
#> ✔ Checking for no source files at root level
#> ✔ Checking for single .Rproj file at root level
#> ● Checking to see if all files in directory are used in code
#>    Problem: You have files in your project directory which are not being used.
#>    Solution: Use or delete files.
#>    See for help: ?fs::file_delete
#> # A tibble: 2 x 1
#>   path_abs                                                                      
#>   <chr>                                                                         
#> 1 /Users/audreybertin/Documents/fertile/tests/testthat/project_miceps/Estrogen_…
#> 2 /Users/audreybertin/Documents/fertile/tests/testthat/project_miceps/mice.csv  
#> 
#> 
#> ✔ Reproducibility checks passed: 6
#> ● Reproducibility checks to work on: 3
#> ● Checking for no image files at root level
#>    Problem: Image files in root directory clutter project
#>    Solution: Move source files to img/ directory
#>    See for help: ?fs::file_move
#> # A tibble: 2 x 2
#>   culprit                          expr                                         
#>   <fs::path>                       <glue>                                       
#> 1 tests/testthat/project_miceps/c… fs::file_move('tests/testthat/project_miceps…
#> 2 tests/testthat/project_miceps/p… fs::file_move('tests/testthat/project_miceps…
#> ● Checking for no raw data files at root level
#>    Problem: Raw data files in root directory clutter project
#>    Solution: Move raw data files to data-raw/ directory
#>    See for help: ?fs::file_move
#> # A tibble: 3 x 2
#>   culprit                           expr                                        
#>   <fs::path>                        <glue>                                      
#> 1 tests/testthat/project_miceps/Bl… fs::file_move('tests/testthat/project_micep…
#> 2 tests/testthat/project_miceps/CS… fs::file_move('tests/testthat/project_micep…
#> 3 tests/testthat/project_miceps/mi… fs::file_move('tests/testthat/project_micep…
#> ● Checking to see if all files in directory are used in code
#>    Problem: You have files in your project directory which are not being used.
#>    Solution: Use or delete files.
#>    See for help: ?fs::file_delete
#> # A tibble: 2 x 1
#>   path_abs                                                                      
#>   <chr>                                                                         
#> 1 /Users/audreybertin/Documents/fertile/tests/testthat/project_miceps/Estrogen_…
#> 2 /Users/audreybertin/Documents/fertile/tests/testthat/project_miceps/mice.csv
```

`fertile` has two modes:

-   diagnostic or retrospective
-   interactive or prospective

Reproducibility reports and checks make up the diagnostic part of `fertile`. The interactive component comes from a system of path logging:

Logging and Reporting of Paths
------------------------------

When you have fertile loaded, the package will interactively edit a log file located in your project directory, which will record the paths/arguments passed to commonly-used input and output functions that you execute in files or in the console. You can access this file using `log_report()`, which reads the log, and `log_clear()`, which erases the log and starts it over.

``` r
log_clear()
library(mime)
library(fertile)
set.seed(10)
read.csv("tests/testthat/data/data.csv")
#>   var1 var2
#> 1    a    2
log_report()
#> # A tibble: 4 x 4
#>   path            path_abs                         func      timestamp          
#>   <chr>           <chr>                            <chr>     <dttm>             
#> 1 package:mime    <NA>                             base::li… 2020-01-09 15:29:55
#> 2 package:fertile <NA>                             base::li… 2020-01-09 15:29:55
#> 3 seed:10         <NA>                             base::se… 2020-01-09 15:29:55
#> 4 tests/testthat… /Users/audreybertin/Documents/f… utils::r… 2020-01-09 15:29:55
```

``` r
log_clear()
log_report()
#> # A tibble: 0 x 0
```

The retrospective functions `proj_check()`, `proj_check_some()`, `proj_test()`, and the functions related to them harness this same logging system to produce reports. However, all editing of the interactive log is independent from the retrospective log, which is not user-accessible.

Implementation
--------------

### Stage 1 (fall 2018)

-   Implement `proj_check()` for diagnosing and cleaning up an existing codebase
    -   modeled on `devtools::check()` and `goodpractice::gp()`
    -   Diagnostic part
        -   scans code files and finds potentially bad paths
        -   checks URLs to ensure they are valid/active
        -   checks DBI connections
        -   scans project directory (recursively) and suggests re-organization of files
        -   grep on file extensions and probably scan through text files
    -   Fix part
        -   fixes paths to be relative to project root (`here::here()`)
        -   move `data` to `data/`
        -   move code to `R/`, or `Rmd/` (or `src/` ?)
        -   move other crap to `inst/`

### Stage 2 (spring 2019)

-   Implement some kind of painless `make`-like functionality
-   See also: [easyMake](https://github.com/GShotwell/easyMake), [drake](https://github.com/ropensci/drake)

### Stage 3 (?)

-   Self-bundling
-   Certification
