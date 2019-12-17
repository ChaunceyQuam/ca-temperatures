
[![Build Status](https://travis-ci.com/espm-157/individual-final-project-ChaunceyQuam.svg?token=PDP11MwdLNKu1ZrvFoxt&branch=master)](https://travis-ci.com/espm-157/individual-final-project-ChaunceyQuam)
- Chauncey Quam, ChaunceyQuam


## assignment
My final project is titled `Final_Project_Submission.md`. Unfortunately I am unable to commit my rendered output file `Final_Project_Submission.md` into the  `assignment` directory so it is located in the main file directory.
There are accompanying files for this assignment located in the `assignment` directory. My work is located in the `.Rmd` notebook.

## Special files

All team repositories will also include most of the special files found here:

### Common files

- `README.md` this file, a general overview of the repository in markdown format.  
- `.gitignore` Optional file, ignore common file types we don't want to accidentally commit to GitHub. Most projects should use this. 
- `<REPO-NAME>.Rproj` Optional, an R-Project file created by RStudio for it's own configuration.  Some people prefer to `.gitignore` this file.
- `CSV data files` is a folder containing necessary csv files for the analysis.


### Infrastructure for Testing

- `.travis.yml`: A configuration file for automatically running [continuous integration](https://travis-ci.com) checks to verify reproducibility of all `.Rmd` notebooks in the repo.  If all `.Rmd` notebooks can render successfully, the "Build Status" badge above will be green (`build success`), otherwise it will be red (`build failure`).  
- `DESCRIPTION` a metadata file for the repository, based on the R package standard. It's main purpose here is as a place to list any additional R packages/libraries needed for any of the `.Rmd` files to run.
- `tests/render_rmds.R` an R script that is run to execute the above described tests, rendering all `.Rmd` notebooks. 




