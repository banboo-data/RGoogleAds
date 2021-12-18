
# r4googleads -<br> R Client Library for Google's Ads Interface

<!-- badges: start -->
[![CRAN status](https://www.r-pkg.org/badges/version/r4googleads)](https://CRAN.R-project.org/package=r4googleads)
[![CRAN monthly downloads](https://cranlogs.r-pkg.org/badges/r4googleads)](https://cran.r-project.org/package=r4googleads)
[![Lifecycle: stable](https://img.shields.io/badge/lifecycle-stable-brightgreen.svg)](https://lifecycle.r-lib.org/articles/stages.html#stable)
<!-- badges: end -->

Welcome to the source code repository of the {**r4googleads**} package. If you came for the user documentation and howto, please visit 
{**r4googleads**}' our *pkgdown* [documentation site](https://banboo-data.github.io/r4googleads). 

The {**r4googleads**} package allows to conveniently load data from *Google Ads* into an **R** environment. The package uses Google Ads' application programming interface (API) which suceeds Google's *Adwords API* and consequently, {**r4googleads**} suceeds its popular predecessor API wrapper R package {**RAdwords**}.


## Installation

Install the latest stable version of {**r4googleads**} from R's official Comprehensive R Archive Network (CRAN) or 

```r{eval=FALSE,message=FALSE, warning=FALSE}
install.packages("r4googleads")
```

install the latest development release from GitHub using the [{remotes}](https://cran.r-project.org/web/packages/remotes/index.html) R package. 
(the below example code assumes the {remotes} package is installed.)

```R
remotes::install_github("banboo-data/r4googleads")
```

## Basic Example - Load Data

```r
library(r4googleads)
```

To specify which data  you want to load into your R session you can use Google's own [SQL reminiscant query language](https://developers.google.com/google-ads/api/fields/v9/overview_query_builder). Visit the link to find out about available reports and validate your queries online.

```R
g_query <- "SELECT
                campaign.name, 
                campaign.status,
                segments.device, 
                metrics.impressions,
                metrics.clicks, 
                metrics.ctr,
                metrics.average_cpc, 
                metrics.cost_micros
              FROM campaign
              WHERE segments.date DURING LAST_30_DAYS
              AND metrics.impressions > 0
              PARAMETERS include_drafts=true"
```

The next step is to create a *service object* that contains the query string, your Google Ads Account ID and the API version
you want to use. The example uses one of our 3 constructors to create different service objects: *googleAdsSearch*, *googleAdsFields*,
*listAccessibleCustomers*. 

```R
query_service <- googleAdsSearch(
  aid = '***-****-***', # Google Ads Account ID
  query = sql_query,
  api_version = 'v9'
  )
```

the resulting service query object can be passed on to the *query_google_ads* functions which sends the service object to Google's actual 
API service. Note, that a *MCC* ID is needed here. The handler inside *query_google_ads* processes the service object depending on its class and starts the corresponding request. 

```R
data <- query_google_ads(
  mcc_id = '***-***-****', # Google Ads My Client Center ID
  google_auth = google_auth,
  service = query_service,
  raw_data = F
)
```