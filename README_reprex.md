``` r
library(data.table)
```

I have a problem with a join that should produce extra rows in data table, I have a subresult which is this one:

``` r
subresult <- structure(list(cell = c(1447386L, 1447386L, 1447387L), species = c(
  "Abies nordmanniana",
  "Abies nordmanniana", "Abies nordmanniana"
), Habitat = c("WetRich", "DryRich", "DryRich")), class = c(
  "data.table",
  "data.frame"
), row.names = c(NA, -3L))
```

this looks like this:

|    cell | species            | Habitat |
|--------:|:-------------------|:--------|
| 1447386 | Abies nordmanniana | WetRich |
| 1447386 | Abies nordmanniana | DryRich |
| 1447387 | Abies nordmanniana | DryRich |

Here we have a species that can inhabit in 2 different habitats in one cell, and 1 in another, all of which are available in this cell, now those habitats can become different landuses, so we have this other dataset

``` r
Feasible_Landuses <- structure(list(species = c(
  "Abies nordmanniana", "Abies nordmanniana",
  "Abies nordmanniana", "Abies nordmanniana", "Abies nordmanniana",
  "Abies nordmanniana", "Abies nordmanniana"
), Landuse = c(
  "ForestDryRich",
  "ForestDryPoor", "ForestWetRich", "ForestWetPoor", "OpenDryRich",
  "OpenDryPoor", "OpenWetPoor"
), Habitat = c(
  "DryRich", "DryPoor",
  "WetRich", "WetPoor", "DryRich", "DryPoor", "WetPoor"
)), row.names = c(
  NA,
  -7L
), class = c("data.table", "data.frame"), sorted = "species")
```

which can be seen here

| species            | Landuse       | Habitat |
|:-------------------|:--------------|:--------|
| Abies nordmanniana | ForestDryRich | DryRich |
| Abies nordmanniana | ForestDryPoor | DryPoor |
| Abies nordmanniana | ForestWetRich | WetRich |
| Abies nordmanniana | ForestWetPoor | WetPoor |
| Abies nordmanniana | OpenDryRich   | DryRich |
| Abies nordmanniana | OpenDryPoor   | DryPoor |
| Abies nordmanniana | OpenWetPoor   | WetPoor |

Now the goal would be to make a join resulting in this:

``` r
Final_Result <- structure(list(cell = c(1447386L, 1447386L, 1447386L, 1447387L, 1447387L), species = c(
  "Abies nordmanniana", "Abies nordmanniana",
  "Abies nordmanniana", "Abies nordmanniana",
  "Abies nordmanniana"
), Landuse = c(
  "ForestDryRich", "ForestWetRich",
  "OpenDryRich", "ForestDryRich", "OpenDryRich"
), Habitat = c("DryRich", "WetRich", "DryRich", "DryRich", "DryRich")), row.names = c(
  NA,
  -5L
), class = c("data.table", "data.frame"))
```

Which should look like this:

|    cell | species            | Landuse       | Habitat |
|--------:|:-------------------|:--------------|:--------|
| 1447386 | Abies nordmanniana | ForestDryRich | DryRich |
| 1447386 | Abies nordmanniana | ForestWetRich | WetRich |
| 1447386 | Abies nordmanniana | OpenDryRich   | DryRich |
| 1447387 | Abies nordmanniana | ForestDryRich | DryRich |
| 1447387 | Abies nordmanniana | OpenDryRich   | DryRich |

When I try this this works fine

``` r
Final_Result <- subresult[Feasible_Landuses, on = .(Habitat, species), nomatch = 0]
```

as can be seen here

|    cell | species            | Habitat | Landuse       |
|--------:|:-------------------|:--------|:--------------|
| 1447386 | Abies nordmanniana | DryRich | ForestDryRich |
| 1447387 | Abies nordmanniana | DryRich | ForestDryRich |
| 1447386 | Abies nordmanniana | WetRich | ForestWetRich |
| 1447386 | Abies nordmanniana | DryRich | OpenDryRich   |
| 1447387 | Abies nordmanniana | DryRich | OpenDryRich   |

However on a larget dataset I get this error:

``` r
Error in vecseq(f__, len__, if (allow.cartesian || notjoin || !anyDuplicated(f__,  : 
  Join results in 19648 rows; more than 11143 = nrow(x)+nrow(i). Check for duplicate key values in i each of which join to the same group in x over and over again. If that's ok, try by=.EACHI to run j for each group to avoid the large allocation. If you are sure you wish to proceed, rerun with allow.cartesian=TRUE. Otherwise, please search for this error message in the FAQ, Wiki, Stack Overflow and data.table issue tracker for advice.
```

## Session info

<details style="margin-bottom:10px;">
<summary>
Standard output and standard error
</summary>

``` sh
Warning messages:
1: In normalizePath(Sys.getenv("TMPDIR", Sys.getenv("TMP"))) :
  path[1]="": No such file or directory
2: In normalizePath(Sys.getenv("TMPDIR", Sys.getenv("TMP"))) :
  path[1]="": No such file or directory
3: In normalizePath(Sys.getenv("TMPDIR", Sys.getenv("TMP"))) :
  path[1]="": No such file or directory
4: In eng_r(options) :
  Failed to tidy R code in chunk 'unnamed-chunk-10'. Reason:
Error in parse_safely(text, keep.source = TRUE) : 
  <text>:1:7: unexpected 'in'
1: Error in
          ^

```

</details>
<details style="margin-bottom:10px;">
<summary>
Session info
</summary>

``` r
sessioninfo::session_info()
#;-) ─ Session info ───────────────────────────────────────────────────────────────
#;-)  setting  value
#;-)  version  R version 4.3.2 (2023-10-31)
#;-)  os       Ubuntu 20.04.6 LTS
#;-)  system   x86_64, linux-gnu
#;-)  ui       X11
#;-)  language en_US:en
#;-)  collate  en_US.UTF-8
#;-)  ctype    en_US.UTF-8
#;-)  tz       Europe/Copenhagen
#;-)  date     2023-12-07
#;-)  pandoc   2.19.2 @ /usr/lib/rstudio/resources/app/bin/quarto/bin/tools/ (via rmarkdown)
#;-) 
#;-) ─ Packages ───────────────────────────────────────────────────────────────────
#;-)  package     * version date (UTC) lib source
#;-)  cli           3.6.1   2023-03-23 [3] CRAN (R 4.2.3)
#;-)  data.table  * 1.14.8  2023-02-17 [1] CRAN (R 4.3.0)
#;-)  digest        0.6.33  2023-07-07 [1] CRAN (R 4.3.1)
#;-)  evaluate      0.23    2023-11-01 [1] CRAN (R 4.3.2)
#;-)  fansi         1.0.5   2023-10-08 [1] CRAN (R 4.3.1)
#;-)  fastmap       1.1.1   2023-02-24 [3] CRAN (R 4.2.2)
#;-)  fs            1.6.3   2023-07-20 [1] CRAN (R 4.3.1)
#;-)  glue          1.6.2   2022-02-24 [3] CRAN (R 4.1.2)
#;-)  htmltools     0.5.7   2023-11-03 [1] CRAN (R 4.3.2)
#;-)  knitr         1.45    2023-10-30 [1] CRAN (R 4.3.2)
#;-)  lifecycle     1.0.4   2023-11-07 [3] CRAN (R 4.3.2)
#;-)  magrittr      2.0.3   2022-03-30 [3] CRAN (R 4.1.3)
#;-)  pillar        1.9.0   2023-03-22 [3] CRAN (R 4.2.3)
#;-)  purrr         1.0.2   2023-08-10 [3] CRAN (R 4.3.1)
#;-)  R.cache       0.16.0  2022-07-21 [1] CRAN (R 4.3.0)
#;-)  R.methodsS3   1.8.2   2022-06-13 [1] CRAN (R 4.3.0)
#;-)  R.oo          1.25.0  2022-06-12 [1] CRAN (R 4.3.0)
#;-)  R.utils       2.12.2  2022-11-11 [1] CRAN (R 4.3.0)
#;-)  reprex        2.0.2   2022-08-17 [1] CRAN (R 4.3.0)
#;-)  rlang         1.1.2   2023-11-04 [1] CRAN (R 4.3.2)
#;-)  rmarkdown     2.25    2023-09-18 [1] CRAN (R 4.3.1)
#;-)  rstudioapi    0.15.0  2023-07-07 [3] CRAN (R 4.3.1)
#;-)  sessioninfo   1.2.2   2021-12-06 [3] CRAN (R 4.1.2)
#;-)  styler        1.10.0  2023-05-24 [1] CRAN (R 4.3.0)
#;-)  utf8          1.2.4   2023-10-22 [1] CRAN (R 4.3.1)
#;-)  vctrs         0.6.4   2023-10-12 [1] CRAN (R 4.3.1)
#;-)  withr         2.5.2   2023-10-30 [1] CRAN (R 4.3.2)
#;-)  xfun          0.41    2023-11-01 [1] CRAN (R 4.3.2)
#;-)  yaml          2.3.7   2023-01-23 [1] CRAN (R 4.3.1)
#;-) 
#;-)  [1] /home/au687614/R/x86_64-pc-linux-gnu-library/4.3
#;-)  [2] /usr/local/lib/R/site-library
#;-)  [3] /usr/lib/R/site-library
#;-)  [4] /usr/lib/R/library
#;-) 
#;-) ──────────────────────────────────────────────────────────────────────────────
```

</details>
