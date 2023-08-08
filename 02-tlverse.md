# (PART) Part 1: Preliminaries {-}

# About the `tlverse` {#tlverse}

## What is the `tlverse`?

The `tlverse` is a new framework for Targeted Learning in `R`, inspired by
the [`tidyverse` ecosystem](https://tidyverse.org) of `R` packages.

By analogy to the [`tidyverse`](https://tidyverse.org/):

> The `tidyverse` is an opinionated collection of `R` packages designed for data
> science. All packages share an underlying design philosophy, grammar, and data
> structures.

So, the [`tlverse`](https://tlverse.org) is

> An opinionated collection of `R` packages for Targeted Learning sharing an
> underlying design philosophy, grammar, and core set of data structures. The
> `tlverse` aims to provide tools both for building Targeted Learning-based
> data analyses and for implementing novel, state-of-the-art Targeted Learning
> methods.

## Anatomy of the `tlverse`

All Targeted Learning methods are targeted maximum likelihood (or minimum
loss-based) estimators (i.e., TMLEs). The construction of any Targeted Learning
estimator proceeds through a two-stage process:

1. Flexibly learning particular components of the data-generating distribution
   often through machine learning (e.g., Super Learning), resulting in _initial
   estimates_ of nuisance parameters.
2. Use of a carefully constructed parametric model-based update, via maximum
   likelihood estimation (i.e., MLE), incorporating the initial estimates
   produced by the prior step to produce a TML estimator.

The packages making up the core components of the `tlverse` software ecosystem
-- `sl3` and `tmle3` -- address the above two goals, respectively. Together, the
general functionality exposed by both allows one to build specific TMLEs
tailored exactly to a particular statistical estimation problem.

The software packages that make up the **core** of the `tlverse` are

* [`sl3`](https://github.com/tlverse/sl3): Modern Super Machine Learning
  * _What?_ A modern object-oriented implementation of the Super Learner
    algorithm, employing recently developed paradigms in `R` programming.
  * _Why?_ A design that leverages modern ideas for faster computation, is
    easily extensible and forward-looking, and forms one of the cornerstones of
    the `tlverse`.

* [`tmle3`](https://github.com/tlverse/tmle3): An Engine for Targeted Learning
  * _What?_ A generalized framework that simplifies Targeted Learning by
    identifying and implementing a series of common statistical estimation
    procedures.
  * _Why?_ A common interface and engine that accommodates current algorithmic
    approaches to Targeted Learning and yet remains a flexible enough engine to
    power the implementation of emerging statistical techniques as they are
    developed.

Beyond these engines that provide the driving force behind the `tlverse`, there
are a few supporting packages that play important roles in the **background**:

* [`origami`](https://github.com/tlverse/origami): A Generalized Framework for
   Cross-Validation [@coyle2018origami]
  * _What?_ A generalized framework for flexible cross-validation.
  * _Why?_ Cross-validation is a key part of ensuring error estimates are honest
    and in preventing overfitting. It is an essential part of both the Super
    Learner ensemble modeling algorithm and in the construction of TML
    estimators.

* [`delayed`](https://github.com/tlverse/delayed): Parallelization Framework for
   Dependent Tasks
  * _What?_ A framework for delayed computations (i.e., futures) based on task
    dependencies.
  * _Why?_ Efficient allocation of compute resources is essential when deploying
    computationally intensive algorithms at large scale.

A key principle of the `tlverse` is extensibility. That is, the software
ecosystem aims to support the development of novel Targeted Learning estimators
as they reach maturity. To achieve this degree of flexibility, we follow the
model of implementing new classes of estimators, for distinct causal inference
problems in separate packages, all of which rely upon the core machinery
provided by `sl3` and `tmle3`. There are currently three examples:

* [`tmle3mopttx`](https://github.com/tlverse/tmle3mopttx): Optimal Treatments
  in the `tlverse`
  * _What?_ Learn an optimal rule and estimate the mean outcome under the rule.
  * _Why?_ Optimal treatments are a powerful tool in precision healthcare and
    other settings where a one-size-fits-all treatment approach is not
    appropriate.

* [`tmle3shift`](https://github.com/tlverse/tmle3shift): Stochastic Shift
  Interventions based on Modified Treatment Policies in the `tlverse`
  * _What?_ Stochastic shift interventions for evaluating changes in
    continuous-valued treatments.
  * _Why?_ Not all treatment variables are binary or categorical. Estimating the
    total effects of intervening on continuous-valued treatments provides a way
    to probe how an effect changes with shifts in the treatment variable.

* [`tmle3mediate`](https://github.com/tlverse/tmle3mediate): Causal Mediation
  Analysis in the `tlverse`
  * _What?_ Techniques for evaluating the direct and indirect effects of
    treatments through mediating variables.
  * _Why?_ Evaluating the total effect of a treatment does not provide
    information about the pathways through which it may operate. When mediating
    variables have been collected, one can instead evaluate direct and indirect
    effect parameters that speak to the _action mechanism_ of the treatment.

## Primer on the `R6` Class System

The `tlverse` is designed using basic object oriented programming (OOP)
principles and the [`R6` OOP framework](https://adv-r.hadley.nz/r6.html). While
we've tried to make it easy to use the `tlverse` packages without worrying much
about OOP, it is helpful to have some intuition about how the `tlverse` is
structured. Here, we briefly outline some key concepts from OOP. Readers
familiar with OOP basics are invited to skip this section.

### Classes, Fields, and Methods

The key concept of OOP is that of an object, a collection of data and functions
that corresponds to some conceptual unit. Objects have two main types of
elements:

1. _fields_, which can be thought of as nouns, are information about an object,
   and
2. _methods_, which can be thought of as verbs, are actions an object can
   perform.

Objects are members of classes, which define what those specific fields and
methods are. Classes can inherit elements from other classes (sometimes called
base classes) -- accordingly, classes that are similar, but not exactly the
same, can share some parts of their definitions.

Many different implementations of OOP exist, with variations in how these
concepts are implemented and used. `R` has several different implementations,
including `S3`, `S4`, reference classes, and `R6`. The `tlverse` uses the `R6`
implementation. In `R6`, methods and fields of a class object are accessed using
the `$` operator. For a more thorough introduction to `R`'s various OOP systems,
see http://adv-r.had.co.nz/OO-essentials.html, from Hadley Wickham's _Advanced
R_ [@wickham2014advanced].

### Object Oriented Programming: `Python` and `R` {-}

OO concepts (classes with inheritance) were baked into Python from the first
published version (version 0.9 in 1991). In contrast, `R` gets its OO "approach"
from its predecessor, `S`, first released in 1976. For the first 15 years, `S`
had no support for classes, then, suddenly, `S` got two OO frameworks bolted on
in rapid succession: informal classes with `S3` in 1991, and formal classes with
`S4` in 1998. This process continues, with new OO frameworks being periodically
released, to try to improve the lackluster OO support in `R`, with reference
classes (`R5`, 2010) and `R6` (2014). Of these, `R6` behaves most like Python
classes (and also most like OOP focused languages like C++ and Java), including
having method definitions be part of class definitions, and allowing objects to
be modified by reference.

# Software Setup {#setup}

## Setting up `R` and RStudio

**`R`** and **RStudio** are separate downloads and installations. `R` is the
underlying statistical computing environment. RStudio is a graphical integrated
development environment (IDE) that makes using `R` much easier and more
interactive. You need to install `R` before you install RStudio.

### Windows

#### If you already have `R` and RStudio installed: {-}

* Open RStudio, and click on "Help" > "Check for updates". If a new version is
  available, quit RStudio, and download the latest version for RStudio.
* To check which version of `R` you are using, start RStudio and the first thing
  that appears in the console indicates the version of `R` you are
  running. Alternatively, you can type `sessionInfo()`, which will also display
  which version of `R` you are running. Go on the [CRAN
  website](https://cran.r-project.org/bin/windows/base/) and check whether a
  more recent version is available. If so, please download and install it. You
  can [check here](https://cran.r-project.org/bin/windows/base/rw-FAQ.html#How-do-I-UNinstall-R_003f)
  for more information on how to remove old versions from your system if you
  wish to do so.

#### If you don't have `R` and RStudio installed: {-}

* Download `R` from
  the [CRAN website](http://cran.r-project.org/bin/windows/base/release.htm).
* Run the `.exe` file that was just downloaded
* Go to the [RStudio download page](https://www.rstudio.com/products/rstudio/download/#download)
* Under *Installers* select **RStudio x.yy.zzz - Windows
  XP/Vista/7/8** (where x, y, and z represent version numbers)
* Double click the file to install it
* Once it's installed, open RStudio to make sure it works and you don't get any
  error messages.

### macOS / Mac OS X

#### If you already have `R` and RStudio installed: {-}

* Open RStudio, and click on "Help" > "Check for updates". If a new version is
  available, quit RStudio, and download the latest version for RStudio.
* To check the version of `R` you are using, start RStudio and the first thing
  that appears on the terminal indicates the version of `R` you are running.
  Alternatively, you can type `sessionInfo()`, which will also display which
  version of `R` you are running. Go on the [CRAN
  website](https://cran.r-project.org/bin/macosx/) and check whether a more
  recent version is available. If so, please download and install it.

#### If you don't have `R` and RStudio installed: {-}

* Download `R` from
  the [CRAN website](http://cran.r-project.org/bin/macosx).
* Select the `.pkg` file for the latest `R` version
* Double click on the downloaded file to install R
* It is also a good idea to install [XQuartz](https://www.xquartz.org/) (needed
  by some packages)
* Go to the [RStudio download
  page](https://www.rstudio.com/products/rstudio/download/#download)
* Under *Installers* select **RStudio x.yy.zzz - Mac OS X 10.6+ (64-bit)**
  (where x, y, and z represent version numbers)
* Double click the file to install RStudio
* Once it's installed, open RStudio to make sure it works and you don't get any
  error messages.

### Linux

* Follow the instructions for your distribution
  from [CRAN](https://cloud.r-project.org/bin/linux), they provide information
  to get the most recent version of `R` for common distributions. For most
  distributions, you could use your package manager (e.g., for Debian/Ubuntu run
  `sudo apt-get install r-base`, and for Fedora `sudo yum install R`), but we
  don't recommend this approach as the versions provided by this are
  usually out of date. In any case, make sure you have the most recent version 
  of `R`.
* Go to the [RStudio download
  page](https://www.rstudio.com/products/rstudio/download/#download)
* Under *Installers* select the version that matches your distribution, and
  install it with your preferred method (e.g., with Debian/Ubuntu `sudo dpkg -i
  rstudio-x.yy.zzz-amd64.deb` at the terminal).
* Once it's installed, open RStudio to make sure it works and you don't get any
  error messages.

These setup instructions are adapted from those written for [Data Carpentry: R
for Data Analysis and Visualization of Ecological
Data](http://www.datacarpentry.org/R-ecology-lesson/).

## Install `tlverse` {#installtlverse}

The `tlverse` ecosystem of packages are currently hosted at
https://github.com/tlverse, not yet on [CRAN](https://CRAN.R-project.org/). You
can use the [`usethis` package](https://usethis.r-lib.org/) to install them:


```r
install.packages("devtools")
devtools::install_github("tlverse/tlverse")
```

The `tlverse` depends on a large number of other packages that are also hosted
on GitHub. Because of this, you may see the following error:

```
Error: HTTP error 403.
  API rate limit exceeded for 71.204.135.82. (But here's the good news:
  Authenticated requests get a higher rate limit. Check out the documentation
  for more details.)

  Rate limit remaining: 0/60
  Rate limit reset at: 2019-03-04 19:39:05 UTC

  To increase your GitHub API rate limit
  - Use `usethis::browse_github_pat()` to create a Personal Access Token.
  - Use `usethis::edit_r_environ()` and add the token as `GITHUB_PAT`.
```

This just means that `R` tried to install too many packages from GitHub in too
short of a window. To fix this, you need to tell `R` how to use GitHub as your
user (you'll need a GitHub user account). Follow these two steps:

1. Type `usethis::browse_github_pat()` in your `R` console, which will direct
   you to GitHub's page to create a New Personal Access Token (PAT).
2. Create a PAT simply by clicking "Generate token" at the bottom of the page.
3. Copy your PAT, a long string of lowercase letters and numbers.
4. Type `usethis::edit_r_environ()` in your `R` console, which will open your
   `.Renviron` file in the source window of RStudio.

    a. If your `.Renviron` file does not pop-up after calling
       `usethis::edit_r_environ()`; then try inputting
       `Sys.setenv(GITHUB_PAT = "yourPAT")`, replacing your PAT with inside the
       quotes. If this does not error, then skip to step 8.

5. In your `.Renviron` file, type `GITHUB_PAT=` and then paste your PAT after
   the equals symbol with no space.
6. In your `.Renviron` file, press the enter key to ensure that your `.Renviron`
   ends with a new line.
7. Save your `.Renviron` file. The example below shows how this syntax should
   look.

  
  ```r
  GITHUB_PAT=yourPAT
  
  ```

8. Restart R. You can restart `R` via the drop-down menu on RStudio's "Session"
   tab, which is located at the top of the RStudio interface. You have to
   restart `R` for the changes to take effect!

After following these steps, you should be able to successfully install the
package which threw the error above.
