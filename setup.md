Assign the variable `setup_packages` a function as follows:


```r
setup_packages <- function(){
```

`c` is a function that means "concatenate".  In this case, we are concatenating multiple strings
into a data structure known as a vector in R.

Assign to `packages_workshop` a vector of packages used in the workshop.


```r
  packages_workshop <- c('dplyr', 'ggplot2', 'broom', 'magrittr')
```

Also, assign to `packages_utils` a vector of packages being used in this setup script only.


```r
  packages_utils <- c('xlsx', 'purrr')
```

The `installed.packages` function returns a dataframe of packages that are installed.
Assign to `installed_packages_as_vector` a vector of installed packages by name.


```r
  installed_packages_as_vector <- installed.packages()[,"Package"]
```

`setdiff` is a nifty function that can get the differences between things, such as two vectors.
Any element in the first argument that's missing from the second will be returned in a vector.

First, we concatenate the two vectors of packages that need to be installed.
Then, we get the difference of packages needed from what is already installed.
`packages_to_install` is assigned the difference.


```r
  packages_to_install <- setdiff(c(packages_workshop, packages_utils), installed_packages_as_vector)
```

If there is more than 0 packages to install,


```r
  if(length(packages_to_install) > 0){
```

put a nice message in the console,


```r
    message(paste0("Installing ", packages_to_install))
```

and then install them!


```r
    install.packages(packages_to_install)
  }
}
```

Assign the variable `write_dataset_to_files` a function as follows:


```r
write_dataset_to_files <- function(dataset_name, working_path, source_url_path) {
```

This function takes three parameters, `dataset_name`, `working_path`, and source_url_path.
`exists` is a function that will check R's environment for the existence of any variables
 by the dataset_name that was passed in as the first parameter to this `write_dataset_to_files` function.

If the dataset exists as one of the default datasets loaded and installed with R by default,


```r
  if(exists(dataset_name)){
```

assign `dataset_as_dataframe` the value of the dataset.


```r
    dataset_as_dataframe <- get(dataset_name)
```

Put a nice message in the console.


```r
    message(paste0("Getting ", dataset_name, " from environment."))
  } else {
```

Otherwise, assign `dataset_as_dataframe` the result of reading in the csv from it's location
on the internet.  `paste0` is a function that takes its inputs and "pastes" them together.


```r
    csv_url_path <- paste0(source_url_path, dataset_name, '.csv')
    dataset_as_dataframe <- read.csv(csv_url_path)
```

Put a nice message in the console.


```r
    message(paste0("Fetching ", dataset_name, " from ", csv_url_path))
  }
```

Create a directory if it doesn't already exist using the path passed in as the second input to
this `write_dataset_to_files` function.


```r
  dir.create(file.path(working_path), showWarnings = FALSE)
```

Put a nice message in the console.


```r
  message(paste0("Creating ", working_path, " in ", getwd()))
```

From the `xlsx` package, use the `write.xlsx` function to write the dataset as a xlsx to the
`working_path`.


```r
  xlsx_file_path <- paste0(working_path, '/', dataset_name, '.xlsx')
  xlsx::write.xlsx(x = dataset_as_dataframe, file = xlsx_file_path)
```

Put a nice message in the console.


```r
  message(paste0("Creating ", xlsx_file_path, " in ", getwd()))
```

Use the `write.csv` function to write the dataset as a csv to the `working_path`.


```r
  csv_file_path <- paste0(working_path, '/', dataset_name, '.csv')
  write.csv(x = dataset_as_dataframe, file = paste0(working_path, '/', dataset_name, '.csv'))
```

Put a nice message in the console.


```r
  message(paste0("Creating ", csv_file_path, " in ", getwd()))
}
```

Assign to `setup_datasets` a function as follows:


```r
setup_datasets <- function(working_path, source_url_path){
```

This function takes two parameters, `working_path` and `source_url_path`.
Load the magrittr library to enable the use of `%>%` -- the piping operator.


```r
  library('magrittr')
```

Assign to `datasets` a vector of the datasets we as files on this computer.


```r
  datasets <- c('crickets', 'airquality')
```

Take the `datasets`
and using `walk` from the `purrr` package,
call the `write_dataset_to_files` using each item from `datasets` as the first argument to
`write_dataset_to_files`.

The second argument is `working_path` from `setup_datasets`'s input.
The last argument is `source_url_path` from `setup_datasets`'s input.


```r
  datasets %>%
    purrr::walk(~ write_dataset_to_files(., working_path, source_url_path))
}
```

Assign to `setup_for_workshop` a function as follows:


```r
setup_for_workshop <- function(){
```

Assign to `working_path` our path for the workshop.


```r
  working_path <- './intro-to-R-20161112'
  source_url_path <- 'https://raw.githubusercontent.com/HoustonUseRs/intro-to-R-guide/master/'
```

Call the functions we previously defined.


```r
  setup_packages()
  setup_datasets(working_path, source_url_path)
```

`setwd` sets our working directory to the `working_path`.


```r
  setwd(working_path)
```

Put a nice message in the console.


```r
  message(paste0("Setting our working directory to ", working_path))
```

Just something silly.


```r
  writeLines(readLines(paste0(source_url_path, 'scripts/congrats-cat.txt')))
}
```

Call the full setup function.


```r
setup_for_workshop()
```

