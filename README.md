# linpath: A Stata Module for Analysis of Path-Specific Effects using Linear Models

## Overview

**linpath** is a Stata module that estimates path-specific effects using linear models.

## Syntax

```stata
linpath depvar mvars [if] [in] [pweight], dvar(varname) d(real) dstar(real) [options]
```

### Required Arguments

- `depvar`: Specifies the outcome variable.
- `mvars`: Specifies the mediators in causal order, starting from the first in the hypothesized causal sequence to the last. Up to 5 mediators can be included.
- `dvar(varname)`: Specifies the treatment (exposure) variable.
- `d(real)`: Specifies the reference level of treatment.
- `dstar(real)`: Specifies the alternative level of treatment. Together, (d - dstar) defines the treatment contrast of interest.

### Options

- `cvars(varlist)`: Specifies the list of baseline covariates to be included in the analysis. Categorical variables must be coded as dummy variables.
- `nointeraction`: Specifies whether treatment-mediator interactions are excluded from the outcome models. By default, interactions are included.
- `cxd`: Includes all two-way interactions between the treatment and baseline covariates in the mediator and outcome models.
- `cxm`: Includes all two-way interactions between the mediators and baseline covariates in the outcome models.
- `detail`: Prints the fitted models for the mediators and outcome used to construct effect estimates.
- `bootstrap_options`: All `bootstrap` options are available.

## Description

`linpath` estimates path-specific effects by fitting linear models for the mediators and outcome, and it computes inferential statistics using the nonparametric bootstrap. If there are K causally ordered mediators, `linpath` provides estimates for:

1. A direct effect of the exposure on the outcome that does not operate through any of the mediators.
2. K path-specific effects, with each path operating through one mediator and net of the mediators preceding it in causal order.

If only one mediator is specified, `linpath` computes conventional natural direct and indirect effects.

To compute path-specific effects with K causally ordered mediators, `linpath` proceeds as follows:

1. Fit models for the mediators and outcome. For k = 1, 2, . . ., K:

    (a) Fit a linear model for the kth mediator conditional on the exposure and baseline confounders.

    (b) Fit a linear model for the outcome conditional on the exposure, baseline confounders, and all the mediators in Mk={M1,...,Mk}.

2. Calculate estimates for the path-specific effects using coefficients from the models fit in step 1.

`linpath` allows pweights, but it does not internally rescale them for use with the bootstrap. If using weights from a complex sample design that require rescaling to produce valid boostrap estimates, the user must be sure to appropriately specify the `strata`, `cluster`, and `size` options from the `bootstrap` command so that Nc-1 clusters are sampled within from each stratum, where Nc denotes the number of clusters per stratum. Failure to properly adjust the bootstrap sampling to account for a complex sample design that requires `pweights` could lead to invalid inferential statistics.

## Examples

### Example 1: Two causally ordered mediators, default settings

```stata
. use nlsy79.dta
. linpath std_cesd_age40 ever_unemp_age3539 log_faminc_adj_age3539, dvar(att22) cvars(female black hispan paredu parprof parinc_prank famsize afqt3) d(1) dstar(0)
```

### Example 2: Three causally ordered mediators, bootstrap with 1000 replications

```stata
. linpath std_cesd_age40 cesd_1992 ever_unemp_age3539 log_faminc_adj_age3539, dvar(att22) cvars(female black hispan paredu parprof parinc_prank famsize afqt3) d(1) dstar(0) reps(1000)
```

### Example 3: Two mediators with all two-way interactions

```stata
. linpath std_cesd_age40 ever_unemp_age3539 log_faminc_adj_age3539, dvar(att22) cvars(female black hispan paredu parprof parinc_prank famsize afqt3) d(1) dstar(0) cxd cxm 
```

### Example 4: Two mediators without interaction terms, printing detailed output

```stata
. linpath std_cesd_age40 ever_unemp_age3539 log_faminc_adj_age3539, dvar(att22) cvars(female black hispan paredu parprof parinc_prank famsize afqt3) d(1) dstar(0) nointer detail
```

## Saved Results

The following results are saved in `e()`:

- **Matrices:**
  - `e(b)`: Matrix containing the total and path-specific effect estimates.

## Author

**Geoffrey T. Wodtke**  
Department of Sociology  
University of Chicago  
Email: [wodtke@uchicago.edu](mailto:wodtke@uchicago.edu)

## References

- Wodtke, GT and X Zhou. *Causal Mediation Analysis*. In preparation.

## See Also

- Stata manual: [regress](https://www.stata.com/manuals/rregress.pdf), [bootstrap](https://www.stata.com/manuals/rbootstrap.pdf)
