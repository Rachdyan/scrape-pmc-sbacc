Scrape SBACC
================
Rachdyan
2023-02-02

## Introduction

We want to scrape the Professional Manager Certification (PMC) data from
SBACC website here: <https://sbacc.org.sg/services/pmc-search/>

First, load the needed libraries for the project

``` r
library(rvest)
library(tidyr)
library(dplyr)
library(stringr)
library(knitr)
library(kableExtra)
```

## Get all the profile links

Get all the profile links from the directory

``` r
link <- c()

## There are a total of 35 pages in the directory
for(i in 1 :35){
  url <- paste("https://sbacc.org.sg/services/pmc-search/page/", i, "/?wpbdp_view=search&dosrch=1&listingfields%5B11%5D&listingfields%5B1%5D", sep = "")
  page <- read_html(url)
  temp_link <- page %>% html_nodes("div[class='listing-title']") %>% html_nodes("a") %>% html_attr("href")
  link <- c(link, temp_link)
}

## See the link example
head(link)
```

    ## [1] "https://sbacc.org.sg/services/pmc-search/12701/huang-zuxian-benjamin/" 
    ## [2] "https://sbacc.org.sg/services/pmc-search/12699/espen-ng-tian-bao/"     
    ## [3] "https://sbacc.org.sg/services/pmc-search/12697/terry-ng/"              
    ## [4] "https://sbacc.org.sg/services/pmc-search/12689/cheryl-lim/"            
    ## [5] "https://sbacc.org.sg/services/pmc-search/12641/tee-chin-keat-benjamin/"
    ## [6] "https://sbacc.org.sg/services/pmc-search/12557/zhou-ying-zoey/"

## Scrape data from the profile link

Get all the profile links from the directory

``` r
profil_df <- data.frame()

for(i in 1:length(link)){
  url <- link[i]
  page <- read_html(url)
  name <- page %>% html_nodes("h1[class='page-header-title clr']") %>%  html_text2()
  
  pmc_code <- page %>%  html_nodes("div[class='wpbdp-field-display wpbdp-field wpbdp-field-value field-display field-value wpbdp-field-pmc_code wpbdp-field-meta wpbdp-field-type-textfield wpbdp-field-association-meta  ']") %>% 
    html_nodes("div[class='value']") %>% html_text2() %>% ifelse(identical(., character(0)), NA, .)
  
  position <- page %>% html_nodes("div[class='wpbdp-field-display wpbdp-field wpbdp-field-value field-display field-value wpbdp-field-position wpbdp-field-meta wpbdp-field-type-textfield wpbdp-field-association-meta  ']") %>% 
    html_nodes("div[class='value']") %>% html_text2() %>% ifelse(identical(., character(0)), NA, .)
  
  company <- page %>% html_nodes("div[class='wpbdp-field-display wpbdp-field wpbdp-field-value field-display field-value wpbdp-field-company wpbdp-field-meta wpbdp-field-type-textarea wpbdp-field-association-meta  ']") %>% 
    html_nodes("div[class='value']") %>% html_text2() %>% ifelse(identical(., character(0)), NA, .)
  
  office <- page %>% html_nodes("div[class='wpbdp-field-display wpbdp-field wpbdp-field-value field-display field-value wpbdp-field-office wpbdp-field-meta wpbdp-field-type-textfield wpbdp-field-association-meta  ']") %>% 
    html_nodes("div[class='value']") %>% html_text2() %>% ifelse(identical(., character(0)), NA, .)
  
  phone <- page %>% html_nodes("div[class='wpbdp-field-display wpbdp-field wpbdp-field-value field-display field-value wpbdp-field-phone_number wpbdp-field-meta wpbdp-field-type-textfield wpbdp-field-association-meta  ']") %>% 
    html_nodes("div[class='value']") %>% html_text2() %>% ifelse(identical(., character(0)), NA, .)
  
  verif <- page %>% html_nodes("div[class='wpbdp-field-display wpbdp-field wpbdp-field-value field-display field-value wpbdp-field-pmc_verification wpbdp-field-meta wpbdp-field-type-radio wpbdp-field-association-meta  ']") %>% 
    html_nodes("div[class='value']") %>% html_text2() %>% ifelse(identical(., character(0)), NA, .)
  
  status <- page %>% html_nodes("div[class='wpbdp-field-display wpbdp-field wpbdp-field-value field-display field-value wpbdp-field-certification_status wpbdp-field-meta wpbdp-field-type-radio wpbdp-field-association-meta  ']") %>% 
    html_nodes("div[class='value']") %>% html_text2() %>% ifelse(identical(., character(0)), NA, .)
  
  experties <- page %>% html_nodes("div[class='wpbdp-field-display wpbdp-field wpbdp-field-value field-display field-value wpbdp-field-area_of_expertise wpbdp-field-meta wpbdp-field-type-textarea wpbdp-field-association-meta  ']") %>% 
    html_nodes("div[class='value']") %>% html_text2() %>% ifelse(identical(., character(0)), NA, .)
  
  temp_df <- data.frame(name = name, pmc_code = pmc_code, position = position, 
                        company = company, office = office, phone = phone, 
                        verif = verif, status = status, experties = experties)
  profil_df <- rbind(profil_df, temp_df)
}
```

Display the data

``` r
profil_df %>% head() %>% kable() %>% kable_styling("striped") %>%
  scroll_box(height = "500px", width = "100%", fixed_thead = T)
```

<div
style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:500px; overflow-x: scroll; width:100%; ">

<table class="table table-striped" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">
name
</th>
<th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">
pmc_code
</th>
<th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">
position
</th>
<th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">
company
</th>
<th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">
office
</th>
<th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">
phone
</th>
<th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">
verif
</th>
<th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">
status
</th>
<th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;">
experties
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
Huang Zuxian, Benjamin
</td>
<td style="text-align:left;">
11056
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
TrellisWerkz Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
96727159
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Digital Marketing Digital Transformation Marketing Sales Sales
Training Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Espen Ng Tian Bao
</td>
<td style="text-align:left;">
11052
</td>
<td style="text-align:left;">
Consultant
</td>
<td style="text-align:left;">
SME Business Consultancy
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
88703371
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Management Skills Business Planning Cybersecurity Data
Protection Digital Transformation Financial Management Human Resource
Management (HRM) Information Technology (IT) Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Terry Ng Kirh Chien
</td>
<td style="text-align:left;">
11053
</td>
<td style="text-align:left;">
Growth Advisor
</td>
<td style="text-align:left;">
Alliance Advisory Private Limited
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
89097790
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Indonesia Market Specific Intelligence, Start-up & Business Turn-around
Strategies, Advisory Board Services
</td>
</tr>
<tr>
<td style="text-align:left;">
Cheryl Lim
</td>
<td style="text-align:left;">
11051
</td>
<td style="text-align:left;">
Partner
</td>
<td style="text-align:left;">
Deloitte Southeast Asia
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
90121154
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Risk Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Tee Chin Keat, Benjamin
</td>
<td style="text-align:left;">
11054
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
PwC
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
84883782
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Budgeting & Cashflow Planning F&B Operations Family Business
Financial Management Training & Development Corporate Strategy, Business
Exit Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Zhou Ying, Zoey
</td>
<td style="text-align:left;">
PMC-11050
</td>
<td style="text-align:left;">
CFO
</td>
<td style="text-align:left;">
PIF CAPITAL (S) PTE. LTD.
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
91509761
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Budgeting & Cashflow Planning Business Continuity Management (BCM)
Business Excellence Business Management Skills Business Planning Change
Management Change Valuations Cost Optimisation Employee Engagement
Environment and Sustainability Financial Management Human Capital
Management Human Resource Management (HRM) Human Resource Management
(HRM) Training Leadership Lean Management Management Training
Merger/Acquisition Negotiation Skills Organisation Development
Organisation Restructuring Performance Management Project Management
Risk Management Strategic Planning/Management Sustainability
Sustainability Impact Management & Measurement Training & Development
Wage Restructuring & Flexible Wage Systems Company Redomiciliation
</td>
</tr>
</tbody>
</table>

</div>

## Export the data

Export the data as a csv file

``` r
write.csv(profil_df, "./data/PMC_data.csv", col.names = F)
```

    ## Warning in write.csv(profil_df, "./data/PMC_data.csv", col.names = F): attempt
    ## to set 'col.names' ignored
