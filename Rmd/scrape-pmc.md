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
profil_df %>% kable() %>% kable_styling("striped") %>%
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
<tr>
<td style="text-align:left;">
Yeo Ai Ling
</td>
<td style="text-align:left;">
PMC-10884
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
WILD ADVERTISING AND MARKETING
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8121 5816
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding

Areas of Consultancy Services Provided: Branding Advertising Content
Marketing & Social Media
</td>
</tr>
<tr>
<td style="text-align:left;">
Kirpal Singh Sidhu
</td>
<td style="text-align:left;">
PMC-10025
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
ADLERBLICK PTE LTD
</td>
<td style="text-align:left;">
6779 6377
</td>
<td style="text-align:left;">
9755 5104
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Change Management SMART Consultancy Leadership Coaching Leadership &
Team Building Strategic Planning Supervisory Management Skills Creative
Thinking Skills Human Capital Management
</td>
</tr>
<tr>
<td style="text-align:left;">
XU SIKAIYI
</td>
<td style="text-align:left;">
PMC-11040
</td>
<td style="text-align:left;">
Marketing Director
</td>
<td style="text-align:left;">
Pu Hua International Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
84984016
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Benchmarking Branding Budgeting & Cashflow
Planning Business Continuity Management (BCM) Business Excellence
Business Management Skills Business Planning CaseTrust Change Management
Change Valuations Compensation & Benefits Competency Training Corporate
Training Cost Optimisation Courseware Development Crisis Management
Customer Centric Initiative (CCI) Customer Satisfaction Research
Customer Service Training Cybersecurity Data Protection Digital
Marketing Digital Transformation EduTrust Employee Engagement
Environment and Sustainability Environmental Management System (ISO)
Executive Coaching Export Strategy F&B Operations Facility Planning
Family Business Financial Management Franchising Good Manufacturing
Practices Hazard Analysis Critical Control Points (HACCP) Human Capital
Management Human Resource Management (HRM) Human Resource Management
(HRM) Training Industry Relations (IR) Training Information Systems
Information Technology (IT) Management Information Technology (IT)
Performance Management Instructional Design Intellectual Property
Management (IPM) International Business Layout Leadership Leadership
Coaching Lean Management Logistics Optimisation Management Training
Marketing Measurement System Merger/Acquisition Negotiation Skills New
Marketing Development Occupational Safety and Health Adminstration
(OSHA) Omni-Channel Commerce Organisation Development Organisation
Restructuring Performance Management Process Re-Engineering Product
Innovation Product Mix Productivity Diagnosis Productivity/Quality
Management Project Management Public Relations & Media Relations Quality
Management System (ISO) Relocation Research & Development (R&D)
Technology Resource Management Retail Operations Retail Performance
Measurement Risk Management Sales Sales Training Security Service
Excellence Service Training Situation Management SME Management Action
For Results (SMART) Initiative Strategic Alliance Strategic
Planning/Management Supervisory Skills Supply Chain Management
Sustainability Sustainability Impact Management & Measurement Talent
Management Team Excellence Technology Adoption Technology License
Development Telecommunications Training & Development Wage Restructuring
& Flexible Wage Systems Work-Life Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Ming En Melvin
</td>
<td style="text-align:left;">
PMC-11046
</td>
<td style="text-align:left;">
Director/Principal Consultant
</td>
<td style="text-align:left;">
CURRENCY DESIGN
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
91712784
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Marketing
</td>
</tr>
<tr>
<td style="text-align:left;">
Vasu Kolla
</td>
<td style="text-align:left;">
PMC-11048
</td>
<td style="text-align:left;">
Engagement Director
</td>
<td style="text-align:left;">
PebbleRoad Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
84444903
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Change Management Customer Centric Initiative
(CCI) Customer Satisfaction Research Digital Transformation Employee
Engagement Information Systems Leadership Leadership Coaching Process
Re-Engineering Product Innovation Product Mix Project Management
Technology Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
Teo Hoon Jee Trina
</td>
<td style="text-align:left;">
PMC-11045
</td>
<td style="text-align:left;">
Founder
</td>
<td style="text-align:left;">
Ascend Marche Pte. Ltd. 
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
+6596852943
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Cheak Seck Fai, Paul
</td>
<td style="text-align:left;">
PMC-11042
</td>
<td style="text-align:left;">

- </td>
  <td style="text-align:left;">

  - </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    97812132
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Business Continuity Management (BCM) Business Management Skills
    Business Planning Change Management Competency Training Corporate
    Training Crisis Management Cybersecurity EduTrust Employee
    Engagement Executive Coaching Facility Planning Information Systems
    Instructional Design Leadership Leadership Coaching Management
    Training Occupational Safety and Health Administration (OSHA)
    Organisation Development Organisation Restructuring Performance
    Management Public Relations & Media Relations Research & Development
    (R&D) Technology Resource Management Risk Management Service
    Training Situation Management Strategic Planning/Management
    Supervisory Skills Team Excellence Technology Adoption Training &
    Development
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Wu Sining
    </td>
    <td style="text-align:left;">
    PMC-11043
    </td>
    <td style="text-align:left;">
    Team Leader - ESG & Climate Specialists, Asia Pacific
    </td>
    <td style="text-align:left;">
    Equilibrium World Pte Ltd
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    88366233
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Environment and Sustainability Project Management Risk Management
    Sustainability Sustainability Impact Management & Measurement
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Geoffrey Simon Leeming
    </td>
    <td style="text-align:left;">
    PMC-11032
    </td>
    <td style="text-align:left;">
    Co Founder
    </td>
    <td style="text-align:left;">
    Pragma Pte Ltd
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    81869602
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Cybersecurity Risk Management
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Paul Tung Weng Cheong
    </td>
    <td style="text-align:left;">
    PMC-11019
    </td>
    <td style="text-align:left;">
    Senior Consultant
    </td>
    <td style="text-align:left;">
    Freemansland Consultancy Pte Ltd Email: <paul@freemansland.co>
    Website: www.freemansland.co l nicholastung.com
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    9389 3009
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Civil and Building Contract Claims (Private/Government) Quantity
    Surveying Contract Administration (Daily submission of claims or
    letters) Cost and Quantity Estimation Budget and Cashflow Planning
    (Prepare and control) Contractual letters and Claims Corporate and
    Individual Consultancy Quantity surveyor Training of Quantity
    surveyor (Any level of quantity surveyor expertise) Executive
    Coaching Performance Management Project Management Leadership
    Management Cost Optimisation Information Technology (IT) Management
    (Hardware) Competency Training Strategic Planning and Management
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Dorothy Chiang Kar Fong
    </td>
    <td style="text-align:left;">
    PMC-10521
    </td>
    <td style="text-align:left;">
    Director
    </td>
    <td style="text-align:left;">
    BLUTRUST PTE LTD
    </td>
    <td style="text-align:left;">
    6323 5628
    </td>
    <td style="text-align:left;">
    9818 9313
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Benchmarking Budgeting & Cashflow Planning Business Continuity
    Management (BCM) Business Planning Financial Management
    Merger/Acquisition Business Valuation for acquisition, disposal,
    mergers, dispute, ESOP, benchmarking and FRS compliance Corporate
    and personal Tax Consultancy SME Financing Consultancy Licence and
    other general statutory compliance required by a Singapore Company
    Government Grant Application
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Derrick Tang Keng Siang
    </td>
    <td style="text-align:left;">
    PMC-10536
    </td>
    <td style="text-align:left;">
    Director/Principal Consultant
    </td>
    <td style="text-align:left;">
    ADVENT MANAGEMENT CONSULTING PTE LTD
    </td>
    <td style="text-align:left;">
    6866 3880
    </td>
    <td style="text-align:left;">
    8339 2829
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Benchmarking Branding Business Excellence Human Resource Management
    (HRM) Leadership Organisation Development Process Re-Engineering
    Product Innovation Quality Management System (ISO) Service
    Excellence Strategic Planning/Management Team Excellence

Areas of Consultancy Services Provided: Business Excellence
Certifications (SQC, PD, I-Class & S-Class) Business Excellence Awards
(SQA, People Excellence Award, Innovation Excellence Award) School
Excellence Model (SEM) Work-Life Strategy Quality Management Systems ISO
14001 Environment Management System OHSAS 18000 Occupational Health &
Safety AS 9100/9110/9120 Aerospace Standards Pro-Family Business (PFB)
Mark Strategic Planning Balanced Scorecard Creativity & Innovation
Design Thinking
</td>
</tr>
<tr>
<td style="text-align:left;">
Rita Kathleen Yong Whee Lee
</td>
<td style="text-align:left;">
PMC-10675
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
6633 7063
</td>
<td style="text-align:left;">
9138 7601
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Dace Certified Curriculum Developer ISO 9001 : 2015 Lead Auditor ISO
45001 : 2018 Lead Auditor PDPA Consultancy

Application of government grants (Enterprise Singapore, IMDA, SMF,MDA,
etc..) Professional Conversion Programme Mentor

Lean Management F & B Operations Facilities Management Human
Resource(HR)-Performance Management

Process Re-engineering Automation & Mechanisation Technology Adoption
Productivity/Quality Management Information Technology(IT) Performance
Management

International Business New Markets Development Customer Satisfaction
Research

Organisation Restructuring Marketing Strategic Planning / Management
Business Planning & Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Leong Wei Hong Adrian
</td>
<td style="text-align:left;">
11037
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Seah Ewe Ming Edwin
</td>
<td style="text-align:left;">
11036
</td>
<td style="text-align:left;">
Chief Executive Officer
</td>
<td style="text-align:left;">
Enhanzcom Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Digital Transformation Information Systems Information Technology (IT)
Management Process Re-Engineering Project Management Sales Technology
Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
Pearl Cheng Wei Ming
</td>
<td style="text-align:left;">
11035
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
Accounting Professionals Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Benchmarking Branding Budgeting & Cashflow
Planning Business Continuity Management (BCM) Business Excellence
Business Management Skills Business Planning CaseTrust Change Management
Compensation & Benefits Competency Training Corporate Training Cost
Optimisation Company Valuations Crisis Management Digital Marketing
Digital Transformation Employee Engagement Environment and
Sustainability Environmental Management System (ISO) F&B Operations
Family Business Financial Management Franchising Leadership
Sustainability
</td>
</tr>
<tr>
<td style="text-align:left;">
Stacy Tan Bei Yi
</td>
<td style="text-align:left;">
11038
</td>
<td style="text-align:left;">
Brand Strategist
</td>
<td style="text-align:left;">
Yellow Octopus
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Marketing
</td>
</tr>
<tr>
<td style="text-align:left;">
Loh Zhi Lin Annabelle
</td>
<td style="text-align:left;">
11039
</td>
<td style="text-align:left;">
Branding & Marketing Consultant
</td>
<td style="text-align:left;">
Quality Zone Technologies Pte. Ltd. 
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Management Skills Digital Marketing Digital
Transformation International Business Marketing New Marketing
Development Project Management Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Giang Sovaan
</td>
<td style="text-align:left;">
11033
</td>
<td style="text-align:left;">
Senior Director
</td>
<td style="text-align:left;">
RSM Risk Advisory Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Voo Hui Ming
</td>
<td style="text-align:left;">
11034
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
A TAX ADVISOR PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
97893997
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Budgeting & Cashflow Planning Business
Excellence Business Management Skills Business Planning Competency
Training Corporate Training Cost Optimisation Data Protection Digital
Transformation Employee Engagement Executive Coaching Export Strategy
Financial Management Leadership Leadership Coaching Management Training
New Marketing Development Supervisory Skills Team Excellence Technology
Adoption Training & Development Tax & Accounting and IRAS audit
</td>
</tr>
<tr>
<td style="text-align:left;">
Joshua Teo Wei Chiang
</td>
<td style="text-align:left;">
10341
</td>
<td style="text-align:left;">
Principal and Co-founder
</td>
<td style="text-align:left;">
Rhindon
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
90488817
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Swee Wan
</td>
<td style="text-align:left;">
11029
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
TRS Forensics Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
97557010
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Cybersecurity Data Protection Digital Transformation Information Systems
Information Technology (IT) Management Process Re-Engineering Risk
Management Technology Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
Maish Ramlal Nichani
</td>
<td style="text-align:left;">
11028
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
PebbleRoad Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
98719260
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Excellence Change Management Customer Centric Initiative (CCI)
Customer Satisfaction Research Digital Marketing Digital Transformation
Employee Engagement Environment and Sustainability Executive Coaching
Human Capital Management Information Systems Information Technology (IT)
Management Instructional Design Leadership Leadership Coaching
Measurement System Omni-Channel Commerce Organisation Development
Performance Management Product Innovation Product Mix Research &
Development (R&D) Technology Service Excellence Sustainability Talent
Management Technology Adoption Work-Life Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Wachirawuth Rattiwarakorn
</td>
<td style="text-align:left;">
11030
</td>
<td style="text-align:left;">
Founder
</td>
<td style="text-align:left;">
Korn Consultancy
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
97869353
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Leong Boon
</td>
<td style="text-align:left;">
11024
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Liu Weixian (Issac)
</td>
<td style="text-align:left;">
11026
</td>
<td style="text-align:left;">
MANAGING DIRECTOR
</td>
<td style="text-align:left;">
OKTOS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Yee Ling Sharon
</td>
<td style="text-align:left;">
11023
</td>
<td style="text-align:left;">
Head of Communications
</td>
<td style="text-align:left;">
Ad.Wright Communications Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
P. Palaniappan (Palani)
</td>
<td style="text-align:left;">
11027
</td>
<td style="text-align:left;">
Group CEO
</td>
<td style="text-align:left;">
AAARYA Business College Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
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
Management Competency Training Corporate Training Cost Optimisation
Customer Service Training EduTrust Employee Engagement Environment and
Sustainability Environment and Sustainability Financial Management
Information Systems Leadership Leadership Coaching Management Training
Negotiation Skills Organisation Development Organisation Restructuring
Performance Management Process Re-Engineering Sales Sales Training
Strategic Planning/Management Talent Management Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Ang Chiang Meng
</td>
<td style="text-align:left;">
11025
</td>
<td style="text-align:left;">
Managing Partner
</td>
<td style="text-align:left;">
Argile Partners
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Budgeting & Cashflow Planning Business Management Skills
Business Planning Change Management Cost Optimisation Crisis Management
Export Strategy F&B Operations Financial Management Human Resource
Management (HRM) International Business Leadership Lean Management
Logistics Optimisation Marketing Merger/Acquisition Negotiation Skills
Organisation Restructuring Performance Management Process Re-Engineering
Product Innovation Productivity Diagnosis Project Management Sales
Strategic Alliance Strategic Planning/Management Supply Chain Management
Technology Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
Japnit Singh
</td>
<td style="text-align:left;">
11021
</td>
<td style="text-align:left;">
Chief Operating Officer
</td>
<td style="text-align:left;">
Spire Research and Consulting Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
94870664
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Branding Business Planning Customer Centric Initiative
(CCI) Customer Satisfaction Research Export Strategy International
Business Marketing Product Mix Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Ju Hui Jude
</td>
<td style="text-align:left;">
11005
</td>
<td style="text-align:left;">
Consultant, Advisory
</td>
<td style="text-align:left;">
Korn Ferry
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
96567587
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Compensation & Benefits Competency Training Human Resource
Management (HRM) Merger/Acquisition Negotiation Skills Organisation
Development Organisation Restructuring Performance Management Project
Management Strategic Workforce Planning Talent Management Training &
Development Wage Restructuring & Flexible Wage Systems
</td>
</tr>
<tr>
<td style="text-align:left;">
Dennis Lee Hock Leong
</td>
<td style="text-align:left;">
11013
</td>
<td style="text-align:left;">
Partner
</td>
<td style="text-align:left;">
RSM Risk Advisory Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
91006941
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Change Management Cost Optimisation Financial Management Good
Manufacturing Practices
</td>
</tr>
<tr>
<td style="text-align:left;">
Liew Weida Andrew
</td>
<td style="text-align:left;">
11016
</td>
<td style="text-align:left;">
Partner
</td>
<td style="text-align:left;">
Qicstart Private Limited
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
98981622
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Benchmarking Branding Budgeting & Cashflow
Planning Compensation & Benefits Competency Training Corporate Training
Cost Optimisation Customer Satisfaction Research Employee Engagement
Executive Coaching Financial Management Human Resource Management (HRM)
Human Resource Management (HRM) Training Marketing Measurement System
Merger/Acquisition New Marketing Development Organisation Development
Organisation Restructuring Performance Management Process Re-Engineering
Product Innovation Productivity Diagnosis Resource Management Retail
Performance Measurement Sales Sales Training Talent Management
Technology Adoption Training & Development Wage Restructuring & Flexible
Wage Systems Work-Life Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Soon Chee Wee (Adrian)
</td>
<td style="text-align:left;">
11015
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
Digital Marketing Buzz Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
90098858
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Information Technology (IT) Management Marketing New Marketing
Development Project Management Technology Adoption Technology License
Development Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Kim Thiam (Keith)
</td>
<td style="text-align:left;">
11017
</td>
<td style="text-align:left;">
Associate Director
</td>
<td style="text-align:left;">
RSM Risk Advisory Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Budgeting & Cashflow Planning Business Management Skills Business
Planning Change Management Environment and Sustainability
Merger/Acquisition Organisation Development Organisation Restructuring
Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Baharudin Nordin
</td>
<td style="text-align:left;">
11020
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
Independent
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
84681650
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Budgeting & Cashflow Planning Business
Continuity Management (BCM) Business Excellence Business Management
Skills Business Planning Change Management Competency Training Corporate
Training Customer Centric Initiative (CCI) Customer Satisfaction
Research Customer Service Training Executive Coaching F&B Operations
Human Resource Management (HRM) Human Resource Management (HRM) Training
Information Systems Information Technology (IT) Management Information
Technology (IT) Performance Management Intellectual Property Management
(IPM) Leadership Leadership Coaching Management Training Marketing
Organisation Development Performance Management Process Re-Engineering
Product Innovation Productivity/Quality Management Project Management
Research & Development (R&D) Technology Resource Management Retail
Operations Retail Performance Measurement Sales Sales Training Service
Excellence Service Training SME Management Action For Results (SMART)
Initiative Strategic Alliance Strategic Planning/Management Supervisory
Skills Talent Management Team Excellence Technology Adoption Technology
License Development Training & Development Digital Transformation
Omni-Channel Commerce Workforce Transformation
</td>
</tr>
<tr>
<td style="text-align:left;">
Roy Augustus Ng
</td>
<td style="text-align:left;">
11018
</td>
<td style="text-align:left;">
Data Protection Officer
</td>
<td style="text-align:left;">
ALA Consulting Pte Ltd. 
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
93882213
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Excellence Business Planning Competency Training Corporate
Training Cost Optimisation Intellectual Property Management (IPM)
International Business Leadership Management Training Marketing
Merger/Acquisition Organisation Development Product Innovation Project
Management Situation Management Strategic Alliance Strategic
Planning/Management Supervisory Skills Training & Development
Intellectual Property Protection Immigration Consultancy Arbitration
Consultancy Personal Data Protection Compliance and Management
Consultancy
</td>
</tr>
<tr>
<td style="text-align:left;">
Yeo Hock Seng
</td>
<td style="text-align:left;">
10893
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
97406547
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Keoy Soo Earn
</td>
<td style="text-align:left;">
10623
</td>
<td style="text-align:left;">
Partner and Executive Director
</td>
<td style="text-align:left;">
Deloitte & Touche LLP
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Merger/Acquisition Valuation and Modelling Intellectual Asset Management
Public Accounting
</td>
</tr>
<tr>
<td style="text-align:left;">
Lim Kian Boon Daniel
</td>
<td style="text-align:left;">
10976
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
VATPAR Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Branding Business Continuity Management (BCM) Business
Excellence Business Planning Cost Optimisation Customer Satisfaction
Research Export Strategy Facility Planning Financial Management Good
Manufacturing Practices Lean Management Organisation Development
Organisation Restructuring Performance Management Project Management
Retail Operations Retail Performance Measurement Sales Training
Strategic Alliance Strategic Planning/Management Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Dane Hudson
</td>
<td style="text-align:left;">
11004
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Ku Hock Heng (Luke)
</td>
<td style="text-align:left;">
11012
</td>
<td style="text-align:left;">
Manager
</td>
<td style="text-align:left;">
Sin-Yun Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Change Management Crisis Management Information Systems Information
Technology (IT) Management Information Technology (IT) Performance
Management Process Re-Engineering Productivity/Quality Management
Project Management Research & Development (R&D) Technology Technology
Adoption Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Anne-Marie Leong
</td>
<td style="text-align:left;">
11014
</td>
<td style="text-align:left;">
Partner
</td>
<td style="text-align:left;">
Maise
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Planning Crisis Management Customer Centric Initiative
(CCI) Export Strategy Layout Leadership Management Training Marketing
New Marketing Development Product Innovation Project Management
Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Daniel George Wan
</td>
<td style="text-align:left;">
11011
</td>
<td style="text-align:left;">
Creative Director / Managing Partner
</td>
<td style="text-align:left;">
CATALYST
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Marketing
</td>
</tr>
<tr>
<td style="text-align:left;">
Valerie Chai Hui Yee
</td>
<td style="text-align:left;">
11009
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
Hyrule Advuisory Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
97587925
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Budgeting & Cashflow Planning Business Management Skills Change
Valuations Corporate Training Cost Optimisation Courseware Development
Digital Marketing Digital Transformation Financial Management
Intellectual Property Management (IPM) Marketing Merger/Acquisition Risk
Management Strategic Alliance Technology Adoption Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Rao Yitian
</td>
<td style="text-align:left;">
11001
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
Cheese Pte. Ltd. 
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Management Skills Marketing New Marketing Development
Public Relations & Media Relations
</td>
</tr>
<tr>
<td style="text-align:left;">
Gopikrishna Rengasamy (Gopi)
</td>
<td style="text-align:left;">
10861
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
KPMG SERVICES PTE. LTD.
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
HERNI FADLINA SULEIMAN
</td>
<td style="text-align:left;">
10372
</td>
<td style="text-align:left;">
Principal Halal Consultant
</td>
<td style="text-align:left;">
Experigence Consultancy Pte. Ltd. \| GetHalal Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Lakshmi Narasimhan TCA
</td>
<td style="text-align:left;">
11007
</td>
<td style="text-align:left;">
Senior Consultant
</td>
<td style="text-align:left;">
Korn Ferry Pte. Ltd. 
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Cost Optimisation Human Resource Management (HRM) Organisation
Development Performance Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Bhavik Bhatt
</td>
<td style="text-align:left;">
11002
</td>
<td style="text-align:left;">
Strategy Director
</td>
<td style="text-align:left;">
The Bonsey Design Partnership
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Branding Business Excellence Business Management Skills
Business Planning Change Management Corporate Training Customer Centric
Initiative (CCI) Customer Satisfaction Research Customer Service
Training Employee Engagement Environment and Sustainability Executive
Coaching International Business New Marketing Development Organisation
Development Product Innovation Product Mix Service Excellence Talent
Management Team Excellence
</td>
</tr>
<tr>
<td style="text-align:left;">
Amanda Chor Li Jun
</td>
<td style="text-align:left;">
11008
</td>
<td style="text-align:left;">
Senior Brand Strategist
</td>
<td style="text-align:left;">
Louken Group
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding
</td>
</tr>
<tr>
<td style="text-align:left;">
Xu Yang (Sophia)
</td>
<td style="text-align:left;">
11003
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
Noris Global Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
96660842
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Environmental Management System (ISO) Information Technology (IT)
Management Marketing Occupational Safety and Health Adminstration (OSHA)
Productivity/Quality Management Quality Management System (ISO) Sales
Sales Training
</td>
</tr>
<tr>
<td style="text-align:left;">
Poh Chin Heng Alan
</td>
<td style="text-align:left;">
11000
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
ACP Computer Training School Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
97483122
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Business Planning Competency Training
Corporate Training Human Resource Management (HRM) Training Information
Systems Information Technology (IT) Management Information Technology
(IT) Performance Management Product Innovation Technology Adoption
Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Melvin Teo Koon Guan
</td>
<td style="text-align:left;">
10987
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
Mojito DME Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
81182882
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Excellence Business Management Skills Business
Planning F&B Operations Human Resource Management (HRM) Marketing New
Marketing Development Organisation Restructuring Process Re-Engineering
Product Innovation Productivity Diagnosis Productivity/Quality
Management Project Management Public Relations & Media Relations Retail
Operations SME Management Action For Results (SMART) Initiative
Strategic Planning/Management Supervisory Skills
</td>
</tr>
<tr>
<td style="text-align:left;">
Corrado Chr. Lillelund Forcellati
</td>
<td style="text-align:left;">
10998
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
Paia Consulting
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
97125234
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Competency Training Corporate Training Employee Engagement
Environmental Management System (ISO) Leadership Leadership Coaching
Management Training Organisation Development Organisation Restructuring
Performance Management Project Management Strategic Alliance Strategic
Planning/Management Talent Management Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Chye Hee Gilbert
</td>
<td style="text-align:left;">
10999
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
e2i
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
90900020
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
ZARA ROBERTS
</td>
<td style="text-align:left;">
10997
</td>
<td style="text-align:left;">
Client Partner, Business Director
</td>
<td style="text-align:left;">
Cowan Singapore
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
88767057
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding
</td>
</tr>
<tr>
<td style="text-align:left;">
Charanjit Singh
</td>
<td style="text-align:left;">
10996
</td>
<td style="text-align:left;">
Managing Partner
</td>
<td style="text-align:left;">
Construct Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
+6582287161
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Digital Transformation Information Systems Information
Technology (IT) Management Marketing Project Management Technology
Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
TAN HEANG KIAT JOSHUA
</td>
<td style="text-align:left;">
10994
</td>
<td style="text-align:left;">
Partner
</td>
<td style="text-align:left;">
Lizana and Company Asia-pacific Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
93685900
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Benchmarking Budgeting & Cashflow Planning
Business Excellence Business Planning Cost Optimisation Financial
Management International Business Merger/Acquisition Organisation
Development Organisation Restructuring Process Re-Engineering Product
Innovation Project Management SME Management Action For Results (SMART)
Initiative Strategic Alliance Strategic Planning/Management Supply Chain
Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Suk Phern Florence
</td>
<td style="text-align:left;">
10585
</td>
<td style="text-align:left;">
DIRECTOR
</td>
<td style="text-align:left;">
CORPORATE FINEDGE PTE LTD
</td>
<td style="text-align:left;">
67023226
</td>
<td style="text-align:left;">
96653759
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Budgeting & Cashflow Planning Business Continuity Management (BCM)
Business Management Skills Business Planning Change Management Financial
Management Human Resource Management (HRM) International Business
Merger/Acquisition Productivity/Quality Management Project Management
Work-Life Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
ANG SENG HAU, ANSON
</td>
<td style="text-align:left;">
10394
</td>
<td style="text-align:left;">
DIRECTOR
</td>
<td style="text-align:left;">
OSTENDO PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
94573538
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Branding Business Excellence Cost Optimisation
F&B Operations Information Systems Logistics Optimisation Process
Re-Engineering Productivity/Quality Management Sales Service Excellence
Technology Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
Lim Puay Huang, Judith
</td>
<td style="text-align:left;">
10265
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
Advast Consultancy LLP
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
98525198
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Branding Business Continuity Management (BCM)
Business Planning Customer Service Training Export Strategy F&B
Operations Financial Management Franchising Information Technology (IT)
Management Intellectual Property Management (IPM) International Business
Layout Lean Management Marketing Merger/Acquisition New Marketing
Development Organisation Restructuring Process Re-Engineering Product
Innovation Productivity/Quality Management Public Relations & Media
Relations Retail Operations SME Management Action For Results (SMART)
Initiative Strategic Planning/Management Supply Chain Management
Technology Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
NG KOK CHUAN
</td>
<td style="text-align:left;">
10892
</td>
<td style="text-align:left;">
CHIEF TRANSFORMATION CONSULTANT
</td>
<td style="text-align:left;">
XI3 CONSULTING PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
98286371
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Excellence Change Management Corporate Training
EduTrust Information Technology (IT) Management Lean Management
Organisation Development Organisation Restructuring Performance
Management Process Re-Engineering
</td>
</tr>
<tr>
<td style="text-align:left;">
Edwin Yap Seng Wee
</td>
<td style="text-align:left;">
10774
</td>
<td style="text-align:left;">
Vice President
</td>
<td style="text-align:left;">
Chubb Global Risk Advisors Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
97309200
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Branding Budgeting & Cashflow Planning Business Continuity
Management (BCM) Business Excellence Business Management Skills Business
Planning Change Management Compensation & Benefits Competency Training
Corporate Training Cost Optimisation Crisis Management Customer
Satisfaction Research Customer Service Training Environmental Management
System (ISO) Executive Coaching Facility Planning Financial Management
Good Manufacturing Practices Hazard Analysis Critical Control Points
(HACCP) Human Resource Management (HRM) Human Resource Management (HRM)
Training Information Systems Information Technology (IT) Management
Information Technology (IT) Performance Management Layout Leadership
Leadership Coaching Lean Management Management Training Occupational
Safety and Health Adminstration (OSHA) Organisation Development
Performance Management Productivity/Quality Management Project
Management Public Relations & Media Relations Quality Management System
(ISO) Sales Sales Training Service Excellence Service Training Situation
Management Supervisory Skills Talent Management Team Excellence Training
& Development Work-Life Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Dalvinder Singh Sidhu
</td>
<td style="text-align:left;">
10992
</td>
<td style="text-align:left;">
REGIONAL SALES DIRECTOR
</td>
<td style="text-align:left;">
PYTHEAS INFOSYS
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
96481842
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Branding Business Continuity Management (BCM)
Business Excellence Business Management Skills Business Planning
Corporate Training Customer Service Training Executive Coaching
Leadership Leadership Coaching Negotiation Skills New Marketing
Development Sales Sales Training Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Derick Ng
</td>
<td style="text-align:left;">
10990
</td>
<td style="text-align:left;">
CEO & Co-founder
</td>
<td style="text-align:left;">
Clickr Media Pte. Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9624 8275
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Information Systems Marketing Technology Adoption New Marketing
Development
</td>
</tr>
<tr>
<td style="text-align:left;">
TAN CHIN HUA
</td>
<td style="text-align:left;">
10809
</td>
<td style="text-align:left;">
OWNER/ PRINCIPAL CONSULTANT
</td>
<td style="text-align:left;">
TCH SAFETY & HEALTH CONSULTANCY
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
93808987
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
PAUL GOH TECK HONG
</td>
<td style="text-align:left;">
10617
</td>
<td style="text-align:left;">
EXECUTIVE DIRECTOR
</td>
<td style="text-align:left;">
ED&C
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
97382038
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Excellence Business Management Skills Business
Planning Executive Coaching Leadership Leadership Coaching Management
Training Marketing New Marketing Development Product Innovation Project
Management Service Excellence Service Training Strategic Alliance
Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
CHOO PENG LEONG PHILLIP
</td>
<td style="text-align:left;">
10995
</td>
<td style="text-align:left;">
Vice President and Managing Director
</td>
<td style="text-align:left;">
Michelman Asia-Pacific
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
97681222
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Executive Coaching Financial Management Leadership Leadership Coaching
Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
KOH CHENG GUAN
</td>
<td style="text-align:left;">
10989
</td>
<td style="text-align:left;">
DIRECTOR
</td>
<td style="text-align:left;">
WPR ASIA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
81253277
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Management Skills Business Planning Franchising
Marketing New Marketing Development Public Relations & Media Relations
Sales Training Strategic Planning/Management Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Daylon Soh
</td>
<td style="text-align:left;">
10977
</td>
<td style="text-align:left;">
Director & Design Educator
</td>
<td style="text-align:left;">
CuriousCore.com
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Change Management Competency Training Corporate Training Customer
Centric Initiative (CCI) Executive Coaching Management Training
Organisation Development Organisation Restructuring Product Innovation
Productivity Diagnosis Productivity/Quality Management Training &
Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Jasmine Low Mui Wan
</td>
<td style="text-align:left;">
10993
</td>
<td style="text-align:left;">
Director/Behavioural Specialist for Sustainability and ESG Integration
</td>
<td style="text-align:left;">
SED CONSULTING
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
<jlmw@sed-consulting.com>
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

- Business Development, Entrepreneurship, Consulting, Interdisciplinary
  Research, Stakeholder Engagement, Strategic Partnership Development,
  Training, Tripartite Development.
- Climate change and global warming, Food waste and management, Genetic
  conservation and biodiversity, Renewable Energy, Social and
  behavioural sciences, Sustainable development, Water conservation
  </td>
  </tr>
  <tr>
  <td style="text-align:left;">
  EBRAHIM MUTALLIB KAZI (KAZI)
  </td>
  <td style="text-align:left;">
  10518
  </td>
  <td style="text-align:left;">
  PARTNER
  </td>
  <td style="text-align:left;">
  OPTIMOTTO LLP
  </td>
  <td style="text-align:left;">
  NA
  </td>
  <td style="text-align:left;">
  93903818
  </td>
  <td style="text-align:left;">
  VERIFIED
  </td>
  <td style="text-align:left;">
  LIVE
  </td>
  <td style="text-align:left;">
  Branding Customer Experience Customer Satisfaction Research Employee
  Engagement Intellectual Property Management (IPM) Marketing
  </td>
  </tr>
  <tr>
  <td style="text-align:left;">
  Jabin Lim
  </td>
  <td style="text-align:left;">
  10986
  </td>
  <td style="text-align:left;">
  Business Consultant
  </td>
  <td style="text-align:left;">
  Cap Management Consulting
  </td>
  <td style="text-align:left;">
  NA
  </td>
  <td style="text-align:left;">
  +65 9189 3533
  </td>
  <td style="text-align:left;">
  VERIFIED
  </td>
  <td style="text-align:left;">
  LIVE
  </td>
  <td style="text-align:left;">
  Branding Business Continuity Management (BCM) Business Excellence
  Business Management Skills Business Planning Change Management
  Employee Engagement International Business Lean Management Management
  Training Marketing Negotiation Skills New Marketing Development
  Organisation Development Organisation Restructuring Product Innovation
  Productivity Diagnosis Project Management Public Relations & Media
  Relations Sales Sales Training Service Excellence Situation Management
  Strategic Alliance Strategic Planning/Management Team Excellence
  Technology Adoption Training & Development
  </td>
  </tr>
  <tr>
  <td style="text-align:left;">
  Jonathan Ng Kiat
  </td>
  <td style="text-align:left;">
  10984
  </td>
  <td style="text-align:left;">
  Partner, Innovation Design
  </td>
  <td style="text-align:left;">
  Common Design Pte. Ltd. 
  </td>
  <td style="text-align:left;">
  NA
  </td>
  <td style="text-align:left;">
  +65 9386 0998
  </td>
  <td style="text-align:left;">
  VERIFIED
  </td>
  <td style="text-align:left;">
  LIVE
  </td>
  <td style="text-align:left;">
  Branding Customer Satisfaction Research Customer Centric
  Initiative (CCI) Research & Development (R&D) Technology Product
  Innovation Service Excellence Training & Development
  </td>
  </tr>
  <tr>
  <td style="text-align:left;">
  Jill Murdoch
  </td>
  <td style="text-align:left;">
  10988
  </td>
  <td style="text-align:left;">
  MANAGING PARTNER
  </td>
  <td style="text-align:left;">
  ELMWOOD DESIGN
  </td>
  <td style="text-align:left;">
  NA
  </td>
  <td style="text-align:left;">
  +65 9172 1890
  </td>
  <td style="text-align:left;">
  VERIFIED
  </td>
  <td style="text-align:left;">
  LIVE
  </td>
  <td style="text-align:left;">
  Branding Employee Engagement Marketing New Marketing Development
  Project Management Strategic Planning/Management
  </td>
  </tr>
  <tr>
  <td style="text-align:left;">
  SANJOY BANERJEE
  </td>
  <td style="text-align:left;">
  10985
  </td>
  <td style="text-align:left;">
  MANAGING DIRECTOR
  </td>
  <td style="text-align:left;">
  PRICEWATERHOUSECOOPERS
  </td>
  <td style="text-align:left;">
  NA
  </td>
  <td style="text-align:left;">
  NA
  </td>
  <td style="text-align:left;">
  VERIFIED
  </td>
  <td style="text-align:left;">
  LIVE
  </td>
  <td style="text-align:left;">
  Benchmarking Corporate Training Negotiation Skills Process
  Re-Engineering Project Management Governance, Risk Management and
  Internal Controls
  </td>
  </tr>
  <tr>
  <td style="text-align:left;">
  Chang Chi Hsung (Alan)
  </td>
  <td style="text-align:left;">
  PMC-10983
  </td>
  <td style="text-align:left;">
  MANAGING DIRECTOR
  </td>
  <td style="text-align:left;">
  OA Corporate Advisory Pte Ltd
  </td>
  <td style="text-align:left;">
  +65 69141114
  </td>
  <td style="text-align:left;">

  - 65 83683945
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Budgeting & Cashflow Planning Business Continuity Management (BCM)
    Business Excellence Business Planning Financial Management
    Merger/Acquisition Performance Management Process Re-Engineering
    Productivity Diagnosis Project Management Strategic
    Planning/Management
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Tan Chee Kwang Michael
    </td>
    <td style="text-align:left;">
    PMC-10979
    </td>
    <td style="text-align:left;">
    Director
    </td>
    <td style="text-align:left;">
    Info121 Pte Ltd
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    96803378
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    Information Systems Information Technology (IT) Management
    Information Technology (IT) Performance Management Process
    Re-Engineering Productivity Diagnosis Productivity/Quality
    Management Supply Chain Management Digital Transformation IT
    Innovation Productivity and Process Improvements
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Naveen Gupta
    </td>
    <td style="text-align:left;">
    SPMC-10975
    </td>
    <td style="text-align:left;">
    Managing Director
    </td>
    <td style="text-align:left;">
    Engee Advisors Pte. Ltd. 
    </td>
    <td style="text-align:left;">
    +91-9820283757 (India)
    </td>
    <td style="text-align:left;">
    +65-81286288 (Singapore)
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Business Excellence Business Management Skills Business Planning
    Executive Coaching Financial Management Human Resource Management
    (HRM) Human Resource Management (HRM) Training Leadership Leadership
    Coaching Marketing Negotiation Skills New Marketing Development
    Organisation Development Organisation Restructuring Product
    Innovation Product Mix Sales Sales Training Strategic Alliance
    Strategic Planning/Management Training & Development Revenue growth
    strategy Process re-engineering Financial services FinTech
    Innovation Negotiations Customer experience
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Chee Yuen Li Andrea
    </td>
    <td style="text-align:left;">
    PMC-10978
    </td>
    <td style="text-align:left;">
    Managing Director
    </td>
    <td style="text-align:left;">
    AEI Legal LLC, Singapore
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    97954673
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Merger/Acquisition
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Terence Yeung
    </td>
    <td style="text-align:left;">
    PMC-10982
    </td>
    <td style="text-align:left;">
    LEADING BRAND CONSULTANT
    </td>
    <td style="text-align:left;">
    DELITIER & CO.
    </td>
    <td style="text-align:left;">
    90019820
    </td>
    <td style="text-align:left;">
    81391133
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Branding Digital Marketing
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Toh Zhan Jing, Jasper
    </td>
    <td style="text-align:left;">
    PMC-10974
    </td>
    <td style="text-align:left;">
    Principal Consultant
    </td>
    <td style="text-align:left;">
    Impact Best Pte Ltd
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    90480626
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Compensation & Benefits Employee Engagement Human Resource
    Management (HRM) Human Resource Management (HRM) Training
    Performance Management Talent Management Training & Development Wage
    Restructuring & Flexible Wage Systems Work-Life Strategy
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Frazer Neo Macken
    </td>
    <td style="text-align:left;">
    10980
    </td>
    <td style="text-align:left;">
    Director
    </td>
    <td style="text-align:left;">
    Definitive Communications
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    92772699
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Branding Brand Audit Brand Strategy Branding & Brand Identity Brand
    Communication Design Business Writing Change Management Competency
    Training Corporate Training Crisis Management Digital Marketing
    Employee Engagement Effective Business Communication Effective
    Presentation Executive Coaching Integrated Marketing Strategy
    Leadership Leadership Coaching Management Training Marketing Media
    Training New Marketing Development Public Relations & Media
    Relations Performance Marketing Reputation Management Service
    Training Social Media Marketing Strategic Planning/Management
    Supervisory Skills
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Jules Judd Labarthe
    </td>
    <td style="text-align:left;">
    PMC-10981
    </td>
    <td style="text-align:left;">
    Managing Partner
    </td>
    <td style="text-align:left;">
    PLANNER AT LARGE LLP
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    97719120
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Branding Employee Engagement Marketing New Marketing Development
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Leong Kean Wye, Ken
    </td>
    <td style="text-align:left;">
    PMC-10970
    </td>
    <td style="text-align:left;">
    Managing Director
    </td>
    <td style="text-align:left;">
    VISIBILITY DESIGN PTE LTD
    </td>
    <td style="text-align:left;">
    63454338
    </td>
    <td style="text-align:left;">
    98186108
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Branding Business Planning Information Systems International
    Business Marketing New Marketing Development Omni-Channel Commerce
    Project Management Public Relations & Media Relations Supervisory
    Skills Technology Adoption
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Dr Yu Sing Ong, Victor
    </td>
    <td style="text-align:left;">
    PMC-10969
    </td>
    <td style="text-align:left;">
    Chief Commercial Officer
    </td>
    <td style="text-align:left;">
    Innova Medical Group
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    97709108
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Branding Budgeting & Cashflow Planning Business Continuity
    Management (BCM) Business Excellence Business Management Skills
    Business Planning Change Management Change Valuations Compensation &
    Benefits Competency Training Corporate Training Crisis Management
    Customer Centric Initiative (CCI) Customer Satisfaction Research
    Customer Service Training Digital Marketing Digital Transformation
    EduTrust Employee Engagement Executive Coaching F&B Operations
    Family Business Financial Management Franchising Good Manufacturing
    Practices Human Capital Management Human Resource Management (HRM)
    Human Resource Management (HRM) Training Industry Relations (IR)
    Training International Business Leadership Leadership Coaching Lean
    Management Logistics Optimisation Management Training Marketing
    Merger/Acquisition Negotiation Skills New Marketing Development
    Organisation Development Organisation Restructuring Performance
    Management Process Re-Engineering Product Innovation Productivity
    Diagnosis Productivity/Quality Management Project Management Public
    Relations & Media Relations Resource Management Retail Operations
    Retail Performance Measurement Risk Management Sales Sales Training
    Service Excellence Service Training Situation Management SME
    Management Action For Results (SMART) Initiative Strategic Alliance
    Strategic Planning/Management Supervisory Skills Supply Chain
    Management Talent Management Team Excellence Training & Development
    Work-Life Strategy
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Lim Oon Hee Gerard
    </td>
    <td style="text-align:left;">
    PMC-10973
    </td>
    <td style="text-align:left;">
    Co-founder
    </td>
    <td style="text-align:left;">
    PPG-21 (<https://www.ppg-21.com>)
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    96393958
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Branding Business Excellence Business Management Skills Business
    Planning Change Management Executive Coaching Leadership Leadership
    Coaching Management Training Marketing New Marketing Development
    Organisation Development Public Relations & Media Relations
    Situation Management Strategic Planning/Management Supervisory
    Skills Work-Life Strategy Brand Analysis, Strategy & Development
    Marketing Analysis, Strategy, Development & Communications Digital
    Marketing Transformation
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Lee Wai Yip
    </td>
    <td style="text-align:left;">
    PMC-10971
    </td>
    <td style="text-align:left;">
    General Manager
    </td>
    <td style="text-align:left;">
    HURCO (S.E. ASIA) PTE LTD
    </td>
    <td style="text-align:left;">
    67426177
    </td>
    <td style="text-align:left;">
    97943360
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Automation, Mechanisation Budgeting & Cashflow Planning Financial
    Management International Business Process Re-Engineering Automation
    for Precision Engineering Industry Process improvement – CNC
    Machining / Turning Overseas Market Entry – Commercial and tax
    implications Financial Management – Budgeting, Cashflow, Tax,
    Financial Reporting
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Ng Chee Chiu
    </td>
    <td style="text-align:left;">
    PMC-10972
    </td>
    <td style="text-align:left;">
    Project Director
    </td>
    <td style="text-align:left;">
    WEBSPARKS PTE. LTD
    </td>
    <td style="text-align:left;">
    62924654
    </td>
    <td style="text-align:left;">
    97963479
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Branding Information Systems Information Technology (IT) Management
    Information Technology (IT) Performance Management Management
    Training Productivity/Quality Management Project Management
    Technology Adoption Agile Project Management Agile Training/Coaching
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Tan Su Yi, Angeline
    </td>
    <td style="text-align:left;">
    PMC-10967
    </td>
    <td style="text-align:left;">
    Regional Operations Director
    </td>
    <td style="text-align:left;">
    THE AUDIENCE MOTIVATION COMPANY ASIA PTE LTD
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    9743 4348
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    NA
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Chinnu Palanivelu
    </td>
    <td style="text-align:left;">
    PMC-10966
    </td>
    <td style="text-align:left;">
    Managing Partner
    </td>
    <td style="text-align:left;">
    Stamford Assurance PAC
    </td>
    <td style="text-align:left;">
    6970 5911
    </td>
    <td style="text-align:left;">
    91056755
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Budgeting & Cashflow Planning Corporate Training Financial
    Management Merger/Acquisition
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Loong Meng Onn
    </td>
    <td style="text-align:left;">
    PMC-10968
    </td>
    <td style="text-align:left;">
    Consultant
    </td>
    <td style="text-align:left;">
    ECOGREEN LABORATORIES LLC
    </td>
    <td style="text-align:left;">
    6238 1502
    </td>
    <td style="text-align:left;">
    9816 8422
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Automation, Mechanisation Corporate Training Layout Lean Management
    Management Training Process Re-Engineering Productivity/Quality
    Management Technology Adoption Digital Transformation Strategy
    Digitalisation / Industry 4.0 Implementation Production Systems
    Automation and Technology Lean Implementation Business Strategy
    Development Process Re-Engineering
    </td>
    </tr>
    <tr>
    <td style="text-align:left;">
    Yang Zhouquan, David
    </td>
    <td style="text-align:left;">
    PMC-10962
    </td>
    <td style="text-align:left;">
    Associate Director
    </td>
    <td style="text-align:left;">
    LIT STRATEGY PTE LTD
    </td>
    <td style="text-align:left;">
    NA
    </td>
    <td style="text-align:left;">
    8116 9230
    </td>
    <td style="text-align:left;">
    VERIFIED
    </td>
    <td style="text-align:left;">
    LIVE
    </td>
    <td style="text-align:left;">
    Business Planning Cost Optimisation Financial Management Marketing
    Merger/Acquisition Strategic Planning/Management

Strategy Planning: Advise on the long term direction for your company
and how to get there Business Transformation: Prepare your company to be
ready for changes in the market Financial Management: Help your company
better manage its finances and ensure fiscal discipline Market Entry:
Grow your business overseas or help you deliver a new good or service
Data Analysis: Analyse and interpret data to help your company make
informed decisions
</td>
</tr>
<tr>
<td style="text-align:left;">
Calvin Loh Ying Kit
</td>
<td style="text-align:left;">
PMC-10964
</td>
<td style="text-align:left;">
Head of Strategy
</td>
<td style="text-align:left;">
MVP GROUP PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9450 1327
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Branding Business Planning Customer Centric
Initiative (CCI) Executive Coaching Leadership Coaching Marketing
Product Innovation Product Mix Project Management Public Relations &
Media Relations Strategic Planning/Management Technology Adoption
DIGITAL MARKETING BRAND STRATEGY COMMUNICATION DESIGN, STRATEGY AND
PLANNING BUSINESS STRATEGY AND PLANNING
</td>
</tr>
<tr>
<td style="text-align:left;">
David Charles Paske Blower, Charlie
</td>
<td style="text-align:left;">
PMC-10965
</td>
<td style="text-align:left;">
Managing Partner
</td>
<td style="text-align:left;">
Blak Labs
</td>
<td style="text-align:left;">
6396 0338
</td>
<td style="text-align:left;">
+6590078195
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Digital Marketing Digital Transformation Marketing New
Marketing Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Murugesan Shree Valliyammai
</td>
<td style="text-align:left;">
PMC-10963
</td>
<td style="text-align:left;">
Senior Consultant
</td>
<td style="text-align:left;">
BRADBURY CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6305 7533
</td>
<td style="text-align:left;">
9114 8038
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Planning Change Management Compensation & Benefits Customer
Service Training Executive Coaching F&B Operations Franchising Human
Resource Management (HRM) Training International Business Leadership
Coaching Lean Management Management Training Merger/Acquisition
Negotiation Skills Organisation Restructuring Performance Management
Service Excellence Strategic Planning/Management Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Koh Wee Chee, George
</td>
<td style="text-align:left;">
PMC-10956
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
CBS ASIA PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Branding Budgeting & Cashflow Planning
Business Continuity Management (BCM) Business Management Skills Business
Planning Change Management Compensation & Benefits Competency Training
Corporate Training Cost Optimisation Customer Centric Initiative (CCI)
Customer Satisfaction Research Employee Engagement Facility Planning
Financial Management Franchising Human Resource Management (HRM) Human
Resource Management (HRM) Training Information Systems Information
Technology (IT) Management Information Technology (IT) Performance
Management Intellectual Property Management (IPM) International Business
Management Training Marketing Merger/Acquisition Negotiation Skills New
Marketing Development Organisation Development Organisation
Restructuring Performance Management Process Re-Engineering Product
Innovation Product Mix Productivity Diagnosis Productivity/Quality
Management Project Management Public Relations & Media Relations
Relocation Research & Development (R&D) Technology Resource Management
Sales Sales Training Service Excellence Strategic Planning/Management
Talent Management Team Excellence Technology Adoption Technology License
Development Telecommunications Training & Development Wage Restructuring
& Flexible Wage Systems Work-Life Strategy Business Strategy Innovation
& Transformation - Information Systems - Automation / AI - Strategic
Technology Road Mapping - Training and Development Digital Business
Transformation - Branding / Marketing Development - Digital Sales &
Marketing Transformation - International Market Expansion Strategic
Business Consulting - Change Management/Transformation - New Business
Model Transformation - Human Resource Productivity Management, Job
Re-design - Financial Advisory Financial Management - Capital Markets
Research & Valuation - Mergers & Acquisition - Corporate Restructuring -
Private Equity - Direct Real Estate – Retirement Homes, PBSD, Social
Housing Education Services - Tertiary Institution - Financial CPD
Training Intellectual Property - Valuation
</td>
</tr>
<tr>
<td style="text-align:left;">
Dr. John Wong
</td>
<td style="text-align:left;">
PMC-10961
</td>
<td style="text-align:left;">
Senior Advisor
</td>
<td style="text-align:left;">
M CONNECT PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9477 7717
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Budgeting & Cashflow Planning Business Excellence Business
Management Skills Business Planning Corporate Training Cost Optimisation
Executive Coaching F&B Operations International Business Leadership
Coaching Management Training Marketing New Marketing Development Project
Management Public Relations & Media Relations Sales Training Performance
Improvement in Leadership Business and Organizational Management Brand
Strategy, Development and Strategic Brand Communications
</td>
</tr>
<tr>
<td style="text-align:left;">
Tung Kai Sheng
</td>
<td style="text-align:left;">
PMC-10960
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
NT22 Pte Ltd \| Nick Tung Email: <tungkaisheng@gmail.com> Website:
www.nicktung.com l nicholastung.com
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8668 4687
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Continuity Management (BCM) Business Excellence
Business Management Skills Business Planning Competency Training
Corporate Training Cost Optimisation Customer Centric Initiative (CCI)
Customer Satisfaction Research Customer Service Training Data Protection
as a Service Data Protection Officer as a Service Data Protection Trust
Mark Employee Engagement Executive Coaching Human Resource Management
(HRM) Human Resource Management (HRM) Training Information Systems
Information Technology (IT) Management Information Technology (IT)
Performance Management Leadership Management Training Marketing
Negotiation Skills New Marketing Development Organisation Development
Performance Management Product Innovation Project Management Resource
Management Retail Operations Retail Performance Measurement Sales Sales
Training Strategic Alliance Strategic Planning/Management Talent
Management Technology Adoption Training & Development Business Strategic
Planning, Management and Development Cashing in and out of Businesses
Strategic Alliance, Business Continuity Management Development Return of
Investment Development and Management Leadership, Corporate and Sales
Training, Management and Development Conceptualization and Monetization
Development Human Resource Redeployment and Upgrading Development (Shu
Zi Methodology) Branding, Digital and External PR Management and
Development Culture, Operational, Productivity and Innovative Management
and Development Talent Acquisition and Customer Retainment Management
Info Tech, Block Chain Strategies Management and Development Crisis and
Threat Management Digital Transformation and Sustainability
</td>
</tr>
<tr>
<td style="text-align:left;">
Leong Qi Wen, Evangeline
</td>
<td style="text-align:left;">
PMC-10959
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
KOBE GLOBAL TECHNOLOGIES PTE LTD
</td>
<td style="text-align:left;">
6255 5662
</td>
<td style="text-align:left;">
9457 6598
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Marketing New Marketing Development Project Management Public
Relations & Media Relations Sales Strategic Planning/Management Training
& Development Social Media Management & Marketing to Grow and Transform
Organizations Influencer Marketing and Key Opinion Leader engagement for
Content Creation Content Marketing for Brand Alignment and Social Media
Transformation
</td>
</tr>
<tr>
<td style="text-align:left;">
Ng Ying Thong
</td>
<td style="text-align:left;">
PMC-10958
</td>
<td style="text-align:left;">
Army Officer
</td>
<td style="text-align:left;">
SINGAPORE ARMED FORCES
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9030 9882
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Ong Bei Shi, Canny
</td>
<td style="text-align:left;">
PMC-10957
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
C.O ENTERPRISE PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9766 3115
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Excellence Business Management Skills Business
Planning F&B Operations Leadership Leadership Coaching Marketing Product
Innovation Productivity/Quality Management Public Relations & Media
Relations Sales Training Training & Development Wage Restructuring &
Flexible Wage Systems
</td>
</tr>
<tr>
<td style="text-align:left;">
Subhalakshmi Iyer Narayanan
</td>
<td style="text-align:left;">
PMC-10626
</td>
<td style="text-align:left;">
Managing Partner
</td>
<td style="text-align:left;">
H.R. STRATEGIES
</td>
<td style="text-align:left;">
6762 1642
</td>
<td style="text-align:left;">
9731 8395
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Areas of Consultancy Services Provided • Manpower Planning &
Optimisation • Human Resource Development • Organisation design
</td>
</tr>
<tr>
<td style="text-align:left;">
Cheng Soon Keong
</td>
<td style="text-align:left;">
PMC-10954
</td>
<td style="text-align:left;">
Executive Director
</td>
<td style="text-align:left;">
BDO ADVISORY PTE LTD
</td>
<td style="text-align:left;">
6829 9627
</td>
<td style="text-align:left;">
9438 1521
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Management Skills Business Planning Change Management Cost
Optimisation Financial Management Merger/Acquisition Performance
Management Process Re-Engineering Project Management Strategic
Planning/Management Transaction Advisory Services to assist companies in
business acquisition Core Capabilities Analysis for Strategic
Development Re-organisation of businesses as part of M&A for value
proposition determination M&A advisory for target identification, deal
origination and post merger management
</td>
</tr>
<tr>
<td style="text-align:left;">
Lee Yong Heng Willie
</td>
<td style="text-align:left;">
PMC-10951
</td>
<td style="text-align:left;">
Boss
</td>
<td style="text-align:left;">
THAT MARKETING GUY
</td>
<td style="text-align:left;">
6203 4069
</td>
<td style="text-align:left;">
9636 0813
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Excellence Employee Engagement Marketing New Marketing
Development Public Relations & Media Relations Branding Strategy
Marketing Strategy Business Strategy Advertising Strategy Public
Relations Strategy Media Strategy Digital Strategy Integrated Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Thomas Goh Toh Wee
</td>
<td style="text-align:left;">
PMC-10953
</td>
<td style="text-align:left;">
Chief Digital Officer
</td>
<td style="text-align:left;">
IN2 MARKETING AND CONSULTING
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9152 5799
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Executive Coaching Marketing New Marketing Development Public
Relations & Media Relations Strategic Planning/Management Technology
Adoption Branding Digital Advertising Web and App Development Event and
Experiential Management Content Development and Marketing Social Media
Marketing
</td>
</tr>
<tr>
<td style="text-align:left;">
Chin Yuanhong
</td>
<td style="text-align:left;">
PMC-10955
</td>
<td style="text-align:left;">
Managing and Creative Director
</td>
<td style="text-align:left;">
DARLING VISUAL COMMUNICATIONS PTE LTD
</td>
<td style="text-align:left;">
6344 8483
</td>
<td style="text-align:left;">
9007 2981
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Planning Marketing New Marketing Development Product
Innovation Project Management Strategic Alliance Strategic
Planning/Management Brand Strategy Communication Strategy Branding and
Identity Design Art Direction Graphic Design Digital Design UI/UX Design
Editorial and Publication Design Packaging Design and Production Spatial
Design and Wayfinding
</td>
</tr>
<tr>
<td style="text-align:left;">
Chia Yan An
</td>
<td style="text-align:left;">
PMC-10952
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
FUTURE ENTERPRISE ONE PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9698 6420
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Excellence Business Management Skills Business Planning Change
Management Competency Training Corporate Training Executive Coaching
Information Technology (IT) Management Information Technology (IT)
Performance Management Leadership Leadership Coaching Management
Training Organisation Development Performance Management Product
Innovation Team Excellence Technology Adoption Training & Development
Digital Transformation Enterprise Agility Design Thinking & Customer
Experience Innovation & Leadership Learning and Development (L&D)
</td>
</tr>
<tr>
<td style="text-align:left;">
Soh Beng Hock
</td>
<td style="text-align:left;">
PMC-10399
</td>
<td style="text-align:left;">
Managing Consultant
</td>
<td style="text-align:left;">
TIJ CONSULTANTS SINGAPORE
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9649 3390
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Environmental Management System (ISO) Good Manufacturing Practices
Hazard Analysis Critical Control Points (HACCP) Human Resource
Management (HRM) Training Lean Management Occupational Safety and Health
Adminstration (OSHA) Productivity/Quality Management Quality Management
System (ISO) Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Simon Charles Bell
</td>
<td style="text-align:left;">
PMC-10560
</td>
<td style="text-align:left;">
SEA Managing Director
</td>
<td style="text-align:left;">
COWAN ASIA PTY LTD (SINGAPORE BRANCH)
</td>
<td style="text-align:left;">
6236 0709
</td>
<td style="text-align:left;">
9776 7985
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Budgeting & Cashflow Planning Business Planning Cost
Optimisation Crisis Management Financial Management Leadership Coaching
Lean Management Management Training Organisation Development
Organisation Restructuring Performance Management Productivity/Quality
Management Strategic Planning/Management Brand strategy and experience
strategy Brand positioning, brand architecture, Naming, Customer Journey
Brand audits & Benchmarking, Brand Design, Guidelines
</td>
</tr>
<tr>
<td style="text-align:left;">
Lilyanna Ali
</td>
<td style="text-align:left;">
PMC-10729
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
MIX MEDIA MARKETING PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9837 8533
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Branding Information Technology (IT)
Management Intellectual Property Management (IPM) International Business
Marketing New Marketing Development Public Relations & Media Relations
</td>
</tr>
<tr>
<td style="text-align:left;">
Ng Jun Liang, Kevin
</td>
<td style="text-align:left;">
PMC-10950
</td>
<td style="text-align:left;">
Assistant Vice President (Finance & Investments)
</td>
<td style="text-align:left;">
CES EDUCATION PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
96150311
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Budgeting & Cashflow Planning Financial Management Marketing
Merger/Acquisition Strategic Alliance Technology Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
Winston Kum Seng Chye
</td>
<td style="text-align:left;">
PMC-10542
</td>
<td style="text-align:left;">
Business Development Advisor
</td>
<td style="text-align:left;">
SME <CENTRE@ASME>
</td>
<td style="text-align:left;">
63863777
</td>
<td style="text-align:left;">
97576263
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Business Continuity Management (BCM) Change
Management Competency Training Cost Optimisation F&B Operations Facility
Planning Information Systems Information Technology (IT) Management
Layout Lean Management Marketing Organisation Development Performance
Management Process Re-Engineering Product Mix Productivity Diagnosis
Productivity/Quality Management Project Management Service Excellence
Strategic Planning/Management Technology Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
Cho Choon Fatt, Wilson
</td>
<td style="text-align:left;">
PMC-10660
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
FM ONE INTERNATIONAL PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9698 8839
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Continuity Management (BCM) Business Management Skills
Competency Training Crisis Management Environmental Management System
(ISO) Facility Planning Information Systems Occupational Safety and
Health Administration (OSHA) Performance Management Project Management
Quality Management System (ISO)

Areas of Consultancy Services Provided: DIGITAL TRANSFORMATION Mobile
App and Web-based Management System Development & Implementation

EMERGENCY & SAFETY & HEALTH MANAGEMENT Crisis & Emergency Response
Planning Fire Safety Management Workplace Safety & Health Management
Training & Development

FACILITY MANAGEMENT Project Management Professional Engineer Services

STANDARDS ADOPTION ISO 9001, 14001, 45001, 27001, 22000, 22301, 41000
etc SS564
</td>
</tr>
<tr>
<td style="text-align:left;">
Zhang Xue Yuan
</td>
<td style="text-align:left;">
PMC-10052
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
TNZ GROUP(S) PTE LTD
</td>
<td style="text-align:left;">
6280 6382
</td>
<td style="text-align:left;">
9004 5618
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Marketing and Sales in Branding & Strategy Implementation Sales Team
Building & special training in sales skill SMART, BE, Quality,
Environmental and Safety System Development Business Model Innovation
</td>
</tr>
<tr>
<td style="text-align:left;">
Zhang Junxian
</td>
<td style="text-align:left;">
PMC-10771
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
BDSA PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9731 8934
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Excellence Productivity Business Model Transformation
</td>
</tr>
<tr>
<td style="text-align:left;">
Yu Jintao, Debby
</td>
<td style="text-align:left;">
PMC-10946
</td>
<td style="text-align:left;">
Managing Director/Co-Founder
</td>
<td style="text-align:left;">
STUDIO DAM PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9731 2644
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Corporate Training Franchising Marketing Product Innovation
Project Management Strategic Planning/Management Brand Analysis Brand
Discovery & Research Brand Guidelines Brand Positioning Brand Naming B
Brand Identities Brand Management Tone of Voice Art Direction New Brand
Creation Corporate / Product Rebranding Brand Refresh Visual Systems
Style Guides Messaging Collateral, Print & Packaging Design Styling /
Content Creation for Social Media Graphic Environment Exhibition Design
Set & Retail Design Spatial Planning
</td>
</tr>
<tr>
<td style="text-align:left;">
Yeo Puay Lin, Pauline
</td>
<td style="text-align:left;">
PMC-10786
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
BIOQUEST ADVISORY PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9011 2216
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Process Re-Engineering Productivity Diagnosis Project Management
Technology Adoption Supply Chain Management Strategic
Planning/Management Lean Management Logistics Optimisation Automation,
Mechanisation Information Systems Information Technology (IT) Management
Information Technology (IT) Performance Management Financial Management
Cost Optimisation Business Management Skills Business Planning Budgeting
& Cashflow Planning Change Management Organisation Development
Organisation Restructuring Performance Management Measurement System
Talent Management Resource Management Service Excellence Service
Training Customer Centric Initiative (CCI) Customer Satisfaction
Research Customer Service Training Productivity/Quality Management
Product Innovation Research & Development (R&D) Technology Human
Resource Management (HRM) Leadership Supervisory Skills Team Excellence
Employee Engagement Wage Restructuring & Flexible Wage Systems Work-Life
Strategy Compensation & Benefits Relocation Product Mix
Merger/Acquisition International Business New Marketing Development
Situation Management Facility Planning Human Resource Management (HRM)
Training Competency Training Management Training Corporate Training
Business Excellence Good Manufacturing Practices Leadership Coaching
Negotiation Skills Training & Development Business Continuity Management
(BCM) Crisis Management Executive Coaching F&B Operations

She specialises in: • Back-office Process Improvement (Finance, HR,
Operations) • End-to-End IT implementation Support (Business Case,
Vendor Selection, Project Management, Business/Functional Requirements,
Tactical Solutions, Testing/Go Live) • Supply Chain Optimisation
(Planning, Procurement, Inventory, Warehouse, Logistics) • Customer
Excellence (Service Standards, Global/Regional Service Centres,
Training, Incentive Model) • New Technology (Fintech, Digitalisation,
Robotics Process Automation (RPA), Cloud Computing, Internet of Things
(IoT)) • Business Strategy (Growth Strategy, Business Restructuring,
Going Global, Merger & Acquisition, Target Operating Model) • HR
Strategy (Talent Strategy, Succession Planning, Performance Management)
• Enterprise Risk Management (Tech Risk, Ops Risk, Outsourcing Risk) •
Financial Services Regulatory Services (Reg & Risk Reporting, AML/KYC,
Compliance, Internal Audi, Fintech)
</td>
</tr>
<tr>
<td style="text-align:left;">
Yap Pik Hwee, Raymond
</td>
<td style="text-align:left;">
PMC-10949
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
Tetra Excellence Consulting Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
+6597911639
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Continuity Management (BCM) Business Excellence Corporate
Training Executive Coaching Leadership Leadership Coaching Organisation
Development Service Excellence SME Management Action For Results (SMART)
Initiative Team Excellence Training & Development Business Continuation
Planning (BCP) with 4 Elements (TetraMap®) Approach Leadership
Effectiveness Process (LEP) Business Excellence (BE) Framework
</td>
</tr>
<tr>
<td style="text-align:left;">
William Thien Wei Leong
</td>
<td style="text-align:left;">
PMC-10107
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
EON Consulting & Training Pte Ltd
</td>
<td style="text-align:left;">
6220 4008
</td>
<td style="text-align:left;">
97858545
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Change Management Compensation & Benefits Competency Training Corporate
Training Customer Satisfaction Research Customer Service Training
Employee Engagement Human Capital Management Human Resource Management
(HRM) Human Resource Management (HRM) Training Instructional Design
Leadership Management Training Performance Management Supervisory Skills
Talent Management Team Excellence Training & Development Wage
Restructuring & Flexible Wage Systems
</td>
</tr>
<tr>
<td style="text-align:left;">
Valerie Lee Siew Yi
</td>
<td style="text-align:left;">
PMC-10701
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
APPLIVON PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Tiong Kheng Hua
</td>
<td style="text-align:left;">
PMC-10637
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
MCCOY BESPOKE PTE LTD
</td>
<td style="text-align:left;">
6515 2988
</td>
<td style="text-align:left;">
9833 1161
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Seck Leng, Stanley
</td>
<td style="text-align:left;">
PMC-10418
</td>
<td style="text-align:left;">
Brand Director
</td>
<td style="text-align:left;">
IMMORTAL THE DESIGN STATION
</td>
<td style="text-align:left;">
+65 6227 9406
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Puay Ching
</td>
<td style="text-align:left;">
PMC-10105
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
P&C MANAGEMENT & TRAINING CENTRE
</td>
<td style="text-align:left;">
6222 3931
</td>
<td style="text-align:left;">
9631 1681
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Customer-centric Initiative Business Excellence Human Resource
Management Productivity Improvement Management & Supervisory Training
Managing Change through Team Building
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Bee Lay
</td>
<td style="text-align:left;">
PMC-10480
</td>
<td style="text-align:left;">
Director/Principal Consultant
</td>
<td style="text-align:left;">
SYR SOLUTIONS PTE LTD
</td>
<td style="text-align:left;">
6381 6350
</td>
<td style="text-align:left;">
8686 2366
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Transformation Business Process Re-engineering Quality &
Information Management Sustainability Impact Management & Measurement

Areas of Consultancy Services Provided: ISCC EU & Plus AA1000 Assurance
ISO 27001 ISO 14001
</td>
</tr>
<tr>
<td style="text-align:left;">
Susan Chua
</td>
<td style="text-align:left;">
PMC-10461
</td>
<td style="text-align:left;">
Management Consultant
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Functional: Business & Digital Transformation, Optimisation and
Innovation M&A Post-merger Integration Management Organisational Change
Management & Implementation Retail Management Start-ups Strategic
Top-Line and Cost Reduction

Industry: Consumer Goods & Retail Cross-border E-commerce Healthcare &
Pharma Private Equity Technologies
</td>
</tr>
<tr>
<td style="text-align:left;">
Steve Tay Kim Boon
</td>
<td style="text-align:left;">
PMC-10673
</td>
<td style="text-align:left;">
Director/Principal Consultant
</td>
<td style="text-align:left;">
ASSURE SAFETY PTE LTD
</td>
<td style="text-align:left;">
6684 9133
</td>
<td style="text-align:left;">
9745 6170
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Workplace Safety & Health Productivity Management Quality Management
Automation Mechanisation Innovation Process Re-Engineering Training &
Development Environmental Management System (ISO) Occupational Safety
and Health Adminstration (OSHA) Work-Life Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Stella Lim Yang Kim
</td>
<td style="text-align:left;">
PMC-10108
</td>
<td style="text-align:left;">
Founder
</td>
<td style="text-align:left;">
SERVICEWORKS PTE LTD
</td>
<td style="text-align:left;">
6871 8878
</td>
<td style="text-align:left;">
9796 3681
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Service Excellence Productivity and Quality Management Strategic
Business Planning
</td>
</tr>
<tr>
<td style="text-align:left;">
Shaun Sho
</td>
<td style="text-align:left;">
PMC-10921
</td>
<td style="text-align:left;">
Director & Creative Director
</td>
<td style="text-align:left;">
NEIGHBOR PTE LTD
</td>
<td style="text-align:left;">
6327 4668
</td>
<td style="text-align:left;">
9743 3106
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Brand Audit & Development Brand Strategy & Identity Graphic & Digital
Design Advertising Marketing & PR Art Direction Ideation & Concepts
Integrated Strategies Consultation
</td>
</tr>
<tr>
<td style="text-align:left;">
Roland Yeow Seng Tuck
</td>
<td style="text-align:left;">
PMC-10027
</td>
<td style="text-align:left;">
Managing Consultant
</td>
<td style="text-align:left;">
DURHAM BUSINESS CONSULTANTS
</td>
<td style="text-align:left;">
6293 9528
</td>
<td style="text-align:left;">
9648 3595
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Financial Management Human Resources Productivity & Quality Management
Strategic Business Planning
</td>
</tr>
<tr>
<td style="text-align:left;">
Roger Loo Peng Siang
</td>
<td style="text-align:left;">
SPMC-10045
</td>
<td style="text-align:left;">
Executive Director
</td>
<td style="text-align:left;">
BDO Consultants Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
81575587
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Branding Budgeting & Cashflow Planning Business Excellence
Business Management Skills Change Management Customer Centric Initiative
(CCI) Customer Satisfaction Research Digital Marketing Digital
Transformation Export Strategy F&B Operations Family Business Human
Capital Management Human Resource Management (HRM) Human Resource
Management (HRM) Training International Business Leadership Leadership
Coaching Marketing Merger/Acquisition New Marketing Development
Performance Management Retail Operations Sales Sales Training Service
Excellence Strategic Planning/Management Sustainability Training &
Development Wage Restructuring & Flexible Wage Systems Work-Life
Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Peh Kiam Choon
</td>
<td style="text-align:left;">
PMC-10207
</td>
<td style="text-align:left;">
International Business Consultant
</td>
<td style="text-align:left;">
ABBA CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6398 0988
</td>
<td style="text-align:left;">
9739 5800
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Productivity Human Resources Information Systems
</td>
</tr>
<tr>
<td style="text-align:left;">
Nav Qirti
</td>
<td style="text-align:left;">
PMC-10218
</td>
<td style="text-align:left;">
Managing Partner
</td>
<td style="text-align:left;">
IDEACTIO PTE LTD
</td>
<td style="text-align:left;">
6396 7803
</td>
<td style="text-align:left;">
9455 3208
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Brand Strategy Design Thinking
</td>
</tr>
<tr>
<td style="text-align:left;">
Malaravan S/O Ponniah
</td>
<td style="text-align:left;">
PMC-10743
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
SECURISTATE PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Lim Yew Loon
</td>
<td style="text-align:left;">
PMC-10203
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
UNIVERSAL STAGE PTE LTD
</td>
<td style="text-align:left;">
6222 2461
</td>
<td style="text-align:left;">
9387 7015
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Productivity Management Business Process Reengineering Strategic
Business Planning
</td>
</tr>
<tr>
<td style="text-align:left;">
Lim Sek Seong
</td>
<td style="text-align:left;">
PMC-10103
</td>
<td style="text-align:left;">
Managing Consulting
</td>
<td style="text-align:left;">
Agility Resilience Solutions
</td>
<td style="text-align:left;">
6922 8089
</td>
<td style="text-align:left;">
88249753
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Continuity Management (BCM) Crisis Management Cybersecurity
Data Protection Risk Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Lee Szu Ming, Zack
</td>
<td style="text-align:left;">
PMC-10948
</td>
<td style="text-align:left;">
Senior Consultant
</td>
<td style="text-align:left;">
GREENSAFE INTERNATIONAL PTE LTD
</td>
<td style="text-align:left;">
6429 1224
</td>
<td style="text-align:left;">
8571 1073
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Environmental Management System (ISO) Good Manufacturing Practices
Hazard Analysis Critical Control Points (HACCP) Occupational Safety and
Health Adminstration (OSHA) Quality Management System (ISO) HACCP
(Hazard Analysis & Critical Control Points) Food Safety System SS 590
(Hazard Analysis & Critical Control Points) Food Safety System ISO 9001
Quality Management System ISO 22000 Food Safety Management System FSSC
22000 Food Safety Management System Food Safety System Yearly
Maintenance Program ISO 45001 Occupational Health and Safety Management
System ISO 14001 Environmental Management System Quality Management
System Yearly Maintenance Program Occupational Health and Safety
Management System Yearly Maintenance Program Environmental Management
System Yearly Maintenance Program Management System Training Quality,
Food Safety, Health & Safety and Environmental Audit
</td>
</tr>
<tr>
<td style="text-align:left;">
Lee Dah Khang
</td>
<td style="text-align:left;">
PMC-10296
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
YANG LEE CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6463 6377
</td>
<td style="text-align:left;">
9760 3387
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Financial Management Mergers and Acquisitions Financial Due Diligence
Sustainability Reporting IPO Related Services Internal Control Advisory
Internal Audit Risk Advisory Accounting Solutions Payroll and Benefits
Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Lawrence Low Kai Fong
</td>
<td style="text-align:left;">
PMC-10093
</td>
<td style="text-align:left;">
FOOD SAFETY DIRECTOR
</td>
<td style="text-align:left;">
GOURMET FOOD SAFETY CONSULTANCY
</td>
<td style="text-align:left;">
6297 6048
</td>
<td style="text-align:left;">
91797675
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
F&B Operations Good Manufacturing Practices Hazard Analysis Critical
Control Points (HACCP) Layout FOOD SAFETY
</td>
</tr>
<tr>
<td style="text-align:left;">
Law Beng Hui, Lawrence
</td>
<td style="text-align:left;">
PMC-10947
</td>
<td style="text-align:left;">
Founder and Managing Partner
</td>
<td style="text-align:left;">
HILLCREST CONSULTING LLP
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9727 6880
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Change Management Competency Training Customer Satisfaction
Research International Business Marketing New Marketing Development
Product Innovation Product Mix Training & Development Brand Strategy and
Architecture Development Marketing Strategy and Management Innovation
Development, Design and Management Brand Product Portfolio Development
and Management Brand, Marketing and Innovation Training
</td>
</tr>
<tr>
<td style="text-align:left;">
Laurence Lau Yoke Soon
</td>
<td style="text-align:left;">
PMC-10723
</td>
<td style="text-align:left;">
Founder/Managing Director
</td>
<td style="text-align:left;">
APEXCEL SOLUTIONS PTE LTD
</td>
<td style="text-align:left;">
6100 0063
</td>
<td style="text-align:left;">
9189 4711
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Competitive Advantage Analysis & Innovation Financial Management
Strategic Planning Sustainability
</td>
</tr>
<tr>
<td style="text-align:left;">
Lars Barslev
</td>
<td style="text-align:left;">
PMC-10945
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
JLB ALLIANCE PTE LTD
</td>
<td style="text-align:left;">
6594 7873
</td>
<td style="text-align:left;">
9730 9402
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Budgeting & Cashflow Planning Business Planning Financial
Management International Business Merger/Acquisition Negotiation Skills
Performance Management Project Management Strategic Planning/Management
M&A advisory (Buy-side and Sell-side Financial Advisory) Business
Planning Financial Management Financial Modelling Business Valuation,
Financial Instrument Valuation, Intellectual Property Valuation
</td>
</tr>
<tr>
<td style="text-align:left;">
Koh Sing Ming
</td>
<td style="text-align:left;">
PMC-10037
</td>
<td style="text-align:left;">
Managing Consultant
</td>
<td style="text-align:left;">
SPECTRUM MANAGEMENT CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6385 0983
</td>
<td style="text-align:left;">
9734 7088
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Excellence (SQA, SQC) Consulting Business Continuity Management
(BCM) Consulting and Training BE Niche Standards Consulting (Service
Class, Innovation Class, People Developer) Customer Experience
Management and Service Excellence Consulting Mission, Vision and Core
Values Facilitation and Consulting Strategic Business Planning Design,
Development and Delivery of Corporate Interventions Leadership
Competency Model Performance Management Leadership Development and
Coaching
</td>
</tr>
<tr>
<td style="text-align:left;">
Koh Geok Ling (Xu Yuling)
</td>
<td style="text-align:left;">
PMC-10612
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
AURAGO CONSULTING PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9049 7620
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Training & Development Marketing & Business Strategy Market Research
Business Roadmapping Productivity Consultancy
</td>
</tr>
<tr>
<td style="text-align:left;">
John Ong Tun Kwok
</td>
<td style="text-align:left;">
PMC-10022
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
FT CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6222 8511
</td>
<td style="text-align:left;">
9684 9492
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Strategic Planning Franchise Development Brand Development Technology
License Development Intellectual Property Strategy (PMC-IPM Certified)
</td>
</tr>
<tr>
<td style="text-align:left;">
Jerry Goh Jia Liang
</td>
<td style="text-align:left;">
PMC-10877
</td>
<td style="text-align:left;">
Business Owner
</td>
<td style="text-align:left;">
STUDIO GRAIN
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9066 4260
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Jacqueline Gwee Siok Chuan
</td>
<td style="text-align:left;">
PMC-10056
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
AADVANTAGE CONSULTING GROUP PTE LTD
</td>
<td style="text-align:left;">
6853 2658
</td>
<td style="text-align:left;">
9047 8547
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

BUSINESS EXCELLENCE Singapore Quality Class People Developer Standard
Innovation Class Service Class SMART Assessment

HUMAN RESOURCE Human Capital & Culture Transformation Organisation
Design Job Redesign Competency Development Performance Management
Work-Life Employee Engagement Culture Measurement

CUSTOMER ADVOCACY & LOYALTY Customer Experience Net Promoter Programme
</td>
</tr>
<tr>
<td style="text-align:left;">
Hsien Naidu
</td>
<td style="text-align:left;">
PMC-10034
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
ASTREEM CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6877 6984
</td>
<td style="text-align:left;">
9171 1373
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

BRANDING Total Brand Corporate Identity Development, Brand Strategy
Development, Market Research

BUSINESS STRATEGY DEVELOPMENT Business Planning

FRANCHISING Franchise Strategy SOP Development Franchise Audit
development

BUSINESS TRANSFORMATION ERP development for various Industries (F&B,
Logistics, Retail) Franchise Digitisation Franchise Management System
Implementation

RELATED CERTIFICATION Business Excellence ACTA Certified Franchise
Executive (International) IP Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Ho Geok Choo
</td>
<td style="text-align:left;">
PMC-10374
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
HUMAN CAPITAL (SINGAPORE) PTE LTD
</td>
<td style="text-align:left;">
6603 8043
</td>
<td style="text-align:left;">
9788 8009
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Harriet Emily Marsden
</td>
<td style="text-align:left;">
PMC-10916
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
SI PARTNERS ASIA LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Doreen Quek
</td>
<td style="text-align:left;">
PMC-10485
</td>
<td style="text-align:left;">
Partner
</td>
<td style="text-align:left;">
RSM CORPORATE ADVISORY PTE LTD
</td>
<td style="text-align:left;">
6594 7827
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
M&A Advisory Financial Management Advisory Corporate Restructuring IPO
Readiness Assessment Business Planning
</td>
</tr>
<tr>
<td style="text-align:left;">
Christine Rovina Cheung Wing Yim
</td>
<td style="text-align:left;">
PMC-10018
</td>
<td style="text-align:left;">
Consulting Director
</td>
<td style="text-align:left;">
ASIAWIDE FRANCHISE CONSULTANTS PTE LTD/ASIAWIDE BUSINESS CONSULTANTS PTE
LTD
</td>
<td style="text-align:left;">
6743 2282
</td>
<td style="text-align:left;">
9862 1522
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
IP Business Diagnostic IP Management Business Excellence (Singapore
Quality Class, Service Class, I-Class, People Developer, Singapore
Quality Award) Certification Consulting – CASETRUST, EDUTRUST Work
Improvement Productivity Improvement
</td>
</tr>
<tr>
<td style="text-align:left;">
Chiam Pey Feng
</td>
<td style="text-align:left;">
PMC-10919
</td>
<td style="text-align:left;">
Business Manager
</td>
<td style="text-align:left;">
<EDC@ASME>
</td>
<td style="text-align:left;">
6222 2461
</td>
<td style="text-align:left;">
9367 2181
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Chia Bee Hock
</td>
<td style="text-align:left;">
PMC-10110
</td>
<td style="text-align:left;">
Managing Consultant
</td>
<td style="text-align:left;">
SYNERGISTIC INTELLIGENCE
</td>
<td style="text-align:left;">
6282 7017
</td>
<td style="text-align:left;">
9734 7644
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Strategy Strategic Planning & KPI Development Business
Excellence and Change Management Knowledge Management Human Capital
Management Human Resource IT system Employee Handbook Employee
Engagement development Productivity Diagnosis and Measurement (SPRING
IMPACT Model) Productivity Improvement: Process Redesign and Improvement
Customer Service Excellence EDG Consulting Service Strategy, Scorecard
and Road Map Customer & Market Segment Analysis Service Audits and
Standard Development Case Trust Accreditation Government Grant and
Support SPRING, IE, WDA, IRAS & IDA Incentives and Assistance Scheme
Advisory Training for managers and frontline Customizing & design of
SSG- WSQ training programmes as stand-alone or part of the integrated
consultancy cum training solution Project management modules
customization and delivery for all industries Train and trainer as part
of the building in-house change implementation capabilities Psychometric
based leadership and team development
</td>
</tr>
<tr>
<td style="text-align:left;">
Chan Zheng Ting, Dean
</td>
<td style="text-align:left;">
PMC-10923
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
KAZEHI GLOBAL PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9177 7760
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Chan Cheow Soon, Winston
</td>
<td style="text-align:left;">
PMC-10023
</td>
<td style="text-align:left;">
Group Managing Partner
</td>
<td style="text-align:left;">
FT CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6222 8511
</td>
<td style="text-align:left;">
9113 7891
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Strategic Planning Franchise Development Technology License Development
Intellectual Property Strategy (PMC-IPM Certified)
</td>
</tr>
<tr>
<td style="text-align:left;">
Benson Leong Ow Chee
</td>
<td style="text-align:left;">
PMC-10010
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
SP CONSULTING (INTERNATIONAL) PTE LTD
</td>
<td style="text-align:left;">
6749 5698
</td>
<td style="text-align:left;">
9732 2304
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Excellence Standards - Singapore Quality Class - Service
Class - Innovation Class - People Developer - SMART Work-Life Strategy
Quality Management Systems

Areas of Consultancy Services Provided: ISO 14001 Environment Management
System OHSAS 18000 Occupational Health & Safety AS 9100/9110/9120
Aerospace Standards Strategic Business Planning Customer Centric
Initiative
</td>
</tr>
<tr>
<td style="text-align:left;">
Andrew Tan Eng Chuan
</td>
<td style="text-align:left;">
PMC-10757
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
TRUSTPRO PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8522 6751
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Specialised in Productivity / Automation - Business Process Review and
Recommendation - Facility Layout Optimization - Software and hardware
recommendation with implementation - Workflow review of current status
and propose revised workflow for better integration - Streamline
workflow and assist with supplemental tools - In-project consultancy to
ensure the project is satisfactorily accomplished - Improve productivity
by at least 20% to 80% - Review ERP system like SAP, SAGE, Odoo - Uses
software like Blender3D, CAD, Photoshop etc - 3D Printing technology and
design reviews projects - Assisted in customised system scoping and
propose industrial benchmarking

Business Model Transformation - Review current business model - Business
model recommendation using Blue Ocean, Business Model Canvas, Harvard’s
Framework and more - Business model strategy and roadmap with 4Ps and 4
Quadrants Business Mix - Open up new opportunity and market

Business Excellence - Check your company health against an international
framework - Submit your report to ESG

Overseas Business Expansion - Overseas marketing presence

Work experiences - Oil and Gas industry, roles as construction manager,
pre-commission, planning and control, project manager - Handled
\$200,000 to \$1 Billion project - Been in 5 Oil and Gas -related MNCs,
SMEs like Branding and Marketing Agency, Management consultancy,
IT-software - In countries like China, Indonesia, Saudi Arabia

Industry Exposure Oil and Gas - Tank Storage and Turnkey - Subsea -
Shipbuilding - Instrumentation

SME - Pharmaceutical - Semiconductor - Food manufacturing - Minimart -
All industries, except retail

PMC, PMP, Six Sigma, Google Analytics, FEInnovate!
</td>
</tr>
<tr>
<td style="text-align:left;">
Azmi Bin Abdul Samad
</td>
<td style="text-align:left;">
PMC-10214
</td>
<td style="text-align:left;">
CEO & Principal Consultant
</td>
<td style="text-align:left;">
HALALHUB CONSULTANTS PTE LTD HALALHUB BUSINESS MANAGEMENTS
</td>
<td style="text-align:left;">
6300 1400, 67340300
</td>
<td style="text-align:left;">
9180 1901
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Halal Consultancy (New / Renewal Applications) MUIS HalMQ System Halal
Consultant Annual Halal Management & Halal Maintenance Services Halal
e-commerce and Advertising & Promotions Halal Trainings & Seminars
International Halal Certification Consultant International and Local
Halal Audits Sertu - Ritual Cleansing Muslim Friendly & Syariah
Compliance Business Advisory Syariah-Compliance Branding Consultant
Qiblah / Mecca - Muslim Prayers Hall / Directional Advisory
International Overseas Qurban / Aqiqah
</td>
</tr>
<tr>
<td style="text-align:left;">
Chang Lo (Olivia Chang)
</td>
<td style="text-align:left;">
PMC-10944
</td>
<td style="text-align:left;">
Senior Business Development Manager
</td>
<td style="text-align:left;">
FLEX-SOLVER PTE LTD
</td>
<td style="text-align:left;">
63846598
</td>
<td style="text-align:left;">
82927294
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
F&B Operations Human Resource Management (HRM) Information Systems
Information Technology (IT) Management Process Re-Engineering Supply
Chain Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Hong Khai Seng
</td>
<td style="text-align:left;">
PMC-10943
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
STUDIO DOJO PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
93368253
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Competency Training Customer Centric Initiative (CCI) Customer
Satisfaction Research Employee Engagement Executive Coaching Information
Systems Leadership Leadership Coaching Organisation Development
Productivity/Quality Management Strategic Planning/Management Team
Excellence Training & Development

Areas of Consultancy Services Provided: Product and Service Innovation,
Design Thinking, UX Design Customer Research, Qualitative & Ethnographic
Research Strategic Foresight and Futures thinking, visioning,
roadmapping Advisory Services for setting up innovation and teams
Training, Coaching & Capability Building Organisation Development and
Design Leadership and Personal Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Lim Quan Heng
</td>
<td style="text-align:left;">
PMC-10942
</td>
<td style="text-align:left;">
Principal Consultant & Country Manager
</td>
<td style="text-align:left;">
PRIVASEC PTE LTD
</td>
<td style="text-align:left;">
66318375
</td>
<td style="text-align:left;">
+65 96276474 / +65 98341842
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Continuity Management (BCM) Competency Training Crisis
Management Information Systems Information Technology (IT) Management
Quality Management System (ISO)

Areas of Consultancy Services Provided: ISO 27001:2013 Information
Security Management System Consulting Secure Systems Development
Lifecycle Process Re-engineering Cyber Security Strategy, Governance,
Risk and Compliance
</td>
</tr>
<tr>
<td style="text-align:left;">
Jorge Rodriguez
</td>
<td style="text-align:left;">
PMC-10941
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
INFLUENTIAL BRANDS LLP
</td>
<td style="text-align:left;">
62235282
</td>
<td style="text-align:left;">
92330320
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Excellence Corporate Training Customer Centric
Initiative (CCI) Customer Satisfaction Research Employee Engagement
Executive Coaching Export Strategy Franchising Intellectual Property
Management (IPM) International Business Leadership Coaching Marketing
New Marketing Development Organisation Restructuring Product Innovation
Product Mix Public Relations & Media Relations Research & Development
(R&D) Technology Retail Performance Measurement SME Management Action
For Results (SMART) Initiative Strategic Alliance Technology Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
Ho Wing Yan
</td>
<td style="text-align:left;">
PMC-10940
</td>
<td style="text-align:left;">
Business Director
</td>
<td style="text-align:left;">
LH.M ADVERTISING PTE LTD
</td>
<td style="text-align:left;">
67426922
</td>
<td style="text-align:left;">
97128203
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Planning Customer Satisfaction Research Layout
Leadership Marketing New Marketing Development Performance Management
Product Mix Project Management Public Relations & Media Relations
Service Excellence Strategic Planning/Management

Areas of Consultancy Services Provided: Brand Strategy Brand Execution
Marketing Strategy Campaign Strategy and Implementation Digital
Marketing Performance Marekting Social Media Content Creation Website &
E-commerce Video Production & Content
</td>
</tr>
<tr>
<td style="text-align:left;">
Audrey Chen
</td>
<td style="text-align:left;">
PMC-10936
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
1103 STUDIOS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
97297686
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Strategy, Advertising, Social Media, Creative
</td>
</tr>
<tr>
<td style="text-align:left;">
Ian Tang Yi’en
</td>
<td style="text-align:left;">
PMC-10935
</td>
<td style="text-align:left;">
Head of Client Strategy
</td>
<td style="text-align:left;">
1103 STUDIOS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
91298691
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Areas of Consultancy Services Provided: Digital Transformation Brand
Strategy Marketing Strategy Content Strategy Paid Campaigns Viral
Campaigns Social Media Management Sales Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Jeremy Koh Teck Ming
</td>
<td style="text-align:left;">
PMC-10933
</td>
<td style="text-align:left;">
Founder
</td>
<td style="text-align:left;">
KOPI CLUB
</td>
<td style="text-align:left;">
+8615601776041
</td>
<td style="text-align:left;">
+8615601776041
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Yew Tiong Nelson
</td>
<td style="text-align:left;">
PMC-10932
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
A TAX ADVISOR PTE LTD
</td>
<td style="text-align:left;">
68718719
</td>
<td style="text-align:left;">
97917691
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Goh Songyao
</td>
<td style="text-align:left;">
PMC-10931
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
GYK PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
96327360
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Automation, Mechanisation Benchmarking Budgeting & Cashflow Planning
Business Excellence Business Planning Change Management Cost
Optimisation F&B Operations Financial Management Process Re-Engineering
Project Management

Industries: Financial Services (Banking, Insurance and Asset Management)
F&B (Services & Manufacturing) Hospitality Logistics Professional
Services (Management Consultancy firms - Business, Engineering and IT)
Retail Startups
</td>
</tr>
<tr>
<td style="text-align:left;">
Steven Nebel
</td>
<td style="text-align:left;">
PMC-10929
</td>
<td style="text-align:left;">
Managing Principal
</td>
<td style="text-align:left;">
LABHAUS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
81884191
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Randy Lam Mun Hoe
</td>
<td style="text-align:left;">
PMC-10927
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
LIGHTHOUSE GLOBAL TRAINING and CONSULTANCY PTE LTD
</td>
<td style="text-align:left;">
67474070
</td>
<td style="text-align:left;">
98519740
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
NA
</td>
</tr>
<tr>
<td style="text-align:left;">
Yuan Wenyun
</td>
<td style="text-align:left;">
PMC-10780
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
Y CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6830 8453
</td>
<td style="text-align:left;">
9695 2020
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Excellence Business Management
Skills Business Planning Change Management Cost Optimisation Financial
Management Merger/Acquisition Process Re-Engineering Project Management

Areas of Consultancy Services Provided: Financial Management Budgeting &
Cashflow Planning Process Re-Engineering Business Excellence Business
Management Skills Business Planning Cost Optimisation Change Management
Project Management Corporate Training Merger/Acquisition

Industry expertise: Manufacturing Hospitality Professional Services
</td>
</tr>
<tr>
<td style="text-align:left;">
Yong Teck Chong
</td>
<td style="text-align:left;">
PMC-10421
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
PS1 BUSINESS SYSTEMS PTE LTD
</td>
<td style="text-align:left;">
6316 4390
</td>
<td style="text-align:left;">
9817 1082
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Specialised in Enterprise Development Grant (EDG) The Model for Labour
Productivity Measurement The Model for Material Saving Measurement
Quality Management System (ISO 9001) Environmental Management System
(ISO 14001) Occupational Health & Safety (ISO 45001) BizSAFE Consultancy
</td>
</tr>
<tr>
<td style="text-align:left;">
Yeo Seng Kiat
</td>
<td style="text-align:left;">
PMC-10385
</td>
<td style="text-align:left;">
Managing Consultant
</td>
<td style="text-align:left;">
SKY DEVELOPMENT
</td>
<td style="text-align:left;">
6748 8979
</td>
<td style="text-align:left;">
9671 3722
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Continuity Management (BCM) CaseTrust EduTrust Environmental
Management System (ISO) Good Manufacturing Practices Occupational Safety
and Health Administration (OSHA) Productivity/Quality Management Project
Management Quality Management System (ISO)

Areas of Consultancy Services Provided: Management System Consulting and
Training (ISO 9001, ISO 14001, ISO 45001/OHSAS 18001/SS506) Safety
Management System (SMS), Risk Management BCA Green and Gracious Builder
Guide TS-01: Good Distribution Practice for Medical Devices ISO 13485
Medical Devices QMS Edu-Trust Certification Scheme Product Listing
Scheme (PLS) Class 1A Certification Scheme SAC-CT-06 Requirement
</td>
</tr>
<tr>
<td style="text-align:left;">
Robin Yeo Meng Cer
</td>
<td style="text-align:left;">
PMC-10196
</td>
<td style="text-align:left;">
Senior Consultant / Regional Manager
</td>
<td style="text-align:left;">
FT CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6222 8511
</td>
<td style="text-align:left;">
9386 9776
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Planning Franchising Human Resource Management (HRM)
Intellectual Property Management (IPM) Strategic Planning/Management
Technology License Development Human Resources Strategic Business
Planning Franchising
</td>
</tr>
<tr>
<td style="text-align:left;">
Wong Wee Teck
</td>
<td style="text-align:left;">
PMC-10629
</td>
<td style="text-align:left;">
Consultant
</td>
<td style="text-align:left;">
D’PURPLE ADVERTISING PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9682 2152
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Compensation & Benefits Competency Training Corporate Training
Employee Engagement Environmental Management System (ISO) Hazard
Analysis Critical Control Points (HACCP) Human Resource Management (HRM)
Human Resource Management (HRM) Training Information Systems Information
Technology (IT) Management Information Technology (IT) Performance
Management Lean Management Management Training Marketing Occupational
Safety and Health Adminstration (OSHA) Organisation Development
Organisation Restructuring Performance Management Productivity/Quality
Management Project Management Quality Management System (ISO) Research &
Development (R&D) Technology Resource Management Strategic
Planning/Management Talent Management Technology Adoption Technology
License Development Training & Development Wage Restructuring & Flexible
Wage Systems Work-Life Strategy

Areas of Consultancy Services Provided: Competency Development New
Marketing Development Standards Adoption HR Management IT Management
Branding & Marketing
</td>
</tr>
<tr>
<td style="text-align:left;">
Wong Kum Yoke
</td>
<td style="text-align:left;">
PMC-10193
</td>
<td style="text-align:left;">
Senior Consultant
</td>
<td style="text-align:left;">
SPECTRUM MANAGEMENT CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6385 0983
</td>
<td style="text-align:left;">
9022 1894
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Human Resource Management (HRM) Information Systems Productivity/Quality
Management Business Process Reengineering
</td>
</tr>
<tr>
<td style="text-align:left;">
Wilson Chew Huat Chye
</td>
<td style="text-align:left;">
PMC-10170
</td>
<td style="text-align:left;">
Partner
</td>
<td style="text-align:left;">
PRICEWATERHOUSE COOPERS BUSINESS ADVISORY SERVICES PTE LTD
</td>
<td style="text-align:left;">
6236 7016
</td>
<td style="text-align:left;">
9679 8331
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Marketing Sales Strategic Planning/Management Marketing and Sales
Strategic Business Planning Brand Strategy Formulation
</td>
</tr>
<tr>
<td style="text-align:left;">
Willy Leow
</td>
<td style="text-align:left;">
PMC-10819
</td>
<td style="text-align:left;">
Partner
</td>
<td style="text-align:left;">
BDO LLP
</td>
<td style="text-align:left;">
6828 9185
</td>
<td style="text-align:left;">
9687 2930
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Continuity Management (BCM) F&B
Operations Financial Management Human Resource Management (HRM)
Information Technology (IT) Management Process Re-Engineering

Areas of Consultancy Services Provided: IPO Internal Control Review
Enterprise Risk Management Services Internal Audit Services Development
of Operational and Financial Management Procedures Corporate Governance
Reviews
</td>
</tr>
<tr>
<td style="text-align:left;">
Wee Chin Chuan
</td>
<td style="text-align:left;">
PMC-10346
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
ORIEL MANAGEMENT CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6612 1298
</td>
<td style="text-align:left;">
9735 1298
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Planning Competency Training
Export Strategy Financial Management International Business Leadership
Coaching Management Training Merger/Acquisition New Marketing
Development Strategic Alliance Strategic Planning/Management

Areas of Consultancy Services Provided: Private Equity (Leveraged
buy-out transactions) Global Structured Trade Finance transactions
Global Project Finance Transactions
</td>
</tr>
<tr>
<td style="text-align:left;">
Vivienne Chiang Kok Ying
</td>
<td style="text-align:left;">
PMC-10370
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
CA FM CONSULTANTS PTE LTD
</td>
<td style="text-align:left;">
6327 9888
</td>
<td style="text-align:left;">
9626 7908
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Financial Management

Areas of Consultancy Services Provided: Planning and Budgeting Cash-Flow
and Working Capital Management Financial Control for SMEs Financial
Assessment for Growth Financial Management Advisory
</td>
</tr>
<tr>
<td style="text-align:left;">
Vivien Koh
</td>
<td style="text-align:left;">
PMC-10860
</td>
<td style="text-align:left;">
Managing Director / Senior Consultant
</td>
<td style="text-align:left;">
VK TRANSFORMATION PTE LTD
</td>
<td style="text-align:left;">
6818 5301
</td>
<td style="text-align:left;">
8228 5628
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Change Management International Business Leadership Coaching Marketing
New Marketing Development Performance Management Productivity Diagnosis
Sales Sales Training Strategic Planning/Management Team Excellence
Training & Development

Areas of Consultancy Services Provided: Digital Sales & Marketing
Transformation Digital Business Transformation Design Thinking for
Business Transformation Strategic Business Consulting New Marketing
Development/International Market Expansion Change
Management/Transformation Leadership Transformation Leadership Coaching
New Business Model Transformation Customer Acquisition (B2B and B2C)
Setting up Marketing Strategy for Success Sales Coaching Impactful
Solution Selling B2B Sales Acceleration Strategic Planning/Management
Team Excellence Performance Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Vincent Tay
</td>
<td style="text-align:left;">
PMC-10754
</td>
<td style="text-align:left;">
Executive Director
</td>
<td style="text-align:left;">
KPMG SERVICES PTE LTD
</td>
<td style="text-align:left;">
6507 1982
</td>
<td style="text-align:left;">
8118 9455
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Excellence Business Management
Skills Business Planning Change Management Cost Optimisation Financial
Management Information Systems Information Technology (IT) Management
Information Technology (IT) Performance Management Lean Management
Performance Management Process Re-Engineering Project Management Service
Excellence Strategic Planning/Management Supply Chain Management
Technology Adoption Technology License Development

Areas of Consultancy Services Provided: Digital Transformation ERP
Implementation
</td>
</tr>
<tr>
<td style="text-align:left;">
Vincent Ho
</td>
<td style="text-align:left;">
PMC-10174
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
AADVANTAGE CONSULTING GROUP PTE LTD
</td>
<td style="text-align:left;">
6853 2658
</td>
<td style="text-align:left;">
9009 4824
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Planning Change Management Corporate Training Customer Centric
Initiative (CCI) Customer Service Training Organisation Development
Organisation Restructuring Service Excellence Strategic
Planning/Management Team Excellence Strategic Business Planning

Areas of Consultancy Services Provided: Customer Experience Consulting
Business Facilitation Culture Development Digital Transformation Service
Training Culture Cascading Culture Training Business Transformation
</td>
</tr>
<tr>
<td style="text-align:left;">
Victoria, Cheng Kher Jia
</td>
<td style="text-align:left;">
10588
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
ASCELON PTE. LTD.
</td>
<td style="text-align:left;">
6780 6518
</td>
<td style="text-align:left;">
9221 5791
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Good Manufacturing Practices Information Systems Information Technology
(IT) Management Lean Management Logistics Optimisation Management
Training Occupational Safety and Health Adminstration (OSHA) Process
Re-Engineering Product Innovation Product Mix Productivity Diagnosis
Productivity/Quality Management Project Management Quality Management
System (ISO) Research & Development (R&D) Technology Training &
Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Veerasamy Bhuvaneswaran
</td>
<td style="text-align:left;">
PMC-10212
</td>
<td style="text-align:left;">
Director/Principal Consultant
</td>
<td style="text-align:left;">
AVANTA GLOBAL PTE LTD
</td>
<td style="text-align:left;">
6295 2819
</td>
<td style="text-align:left;">
9457 6199
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Continuity Management (BCM) Corporate Training Environmental
Management System (ISO) Good Manufacturing Practices Hazard Analysis
Critical Control Points (HACCP) Management Training Occupational Safety
and Health Adminstration (OSHA) Productivity/Quality Management Project
Management Quality Management System (ISO) Strategic Planning/Management
Training & Development Information Systems Productivity and Quality
Management Marketing and Sales Strategic Business Planning
</td>
</tr>
<tr>
<td style="text-align:left;">
Tony Tan Kong Hin
</td>
<td style="text-align:left;">
PMC-10924
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
QI INTEGRATED SOLUTIONS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Planning Marketing Negotiation Skills Project
Management Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Tong Nan Feng, Nafe
</td>
<td style="text-align:left;">
PMC-10870
</td>
<td style="text-align:left;">
Chief Creative Officer \| ESG Advocate
</td>
<td style="text-align:left;">
ABrandADay (ABAD PTE. LTD.)
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9847 3712
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
(F&B, Manufacturing, Health & Wellness, Technology & Energy Sectors)
Strategic Brand and Marketing Development Business Strategy Development
New Product to Market Development International Business Project
Management Leadership and Management Training
</td>
</tr>
<tr>
<td style="text-align:left;">
Tnay Yuan Hong, Jewel
</td>
<td style="text-align:left;">
PMC-10803
</td>
<td style="text-align:left;">
Lead Consultant
</td>
<td style="text-align:left;">
Dreams to Reality Pte. Ltd. 
</td>
<td style="text-align:left;">
6602 8285
</td>
<td style="text-align:left;">
+65 9060 5885
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Excellence Business Management Skills Business
Planning Courseware Development Customer Centric Initiative (CCI)
Customer Satisfaction Research Customer Service Training Employee
Engagement Environment and Sustainability Executive Coaching Export
Strategy F&B Operations Franchising Human Capital Management Human
Resource Management (HRM) Human Resource Management (HRM) Training
Information Technology (IT) Management Leadership Management Training
Marketing Negotiation Skills Organisation Development Performance
Management Product Innovation Sales Sales Training Service Excellence
Strategic Planning/Management Sustainability Talent Management
Technology Adoption Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Tay Shau Yin
</td>
<td style="text-align:left;">
PMC-10401
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
ASCENDO CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6398 0067
</td>
<td style="text-align:left;">
9688 3579
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Planning Competency Training Corporate Training F&B Operations
Hazard Analysis Critical Control Points (HACCP) Occupational Safety and
Health Adminstration (OSHA) Productivity/Quality Management Quality
Management System (ISO) Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Tay Kae Fong
</td>
<td style="text-align:left;">
PMC-10275
</td>
<td style="text-align:left;">
Founder
</td>
<td style="text-align:left;">
BINOMIAL PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9271 0809
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Customer Service Training Marketing Organisation Development
Product Innovation Product Mix Service Training Strategic
Planning/Management Design Thinking Digital Transformation Innovation
Change Management

Areas of Consultancy Services Provided: Incubator Strategy and Set-Up
Multi-Channel Communications Planning Unified Communications (Online &
Offline) Product Portfolio Strategy & Planning Physical/Digital Store
Innovation & Design
</td>
</tr>
<tr>
<td style="text-align:left;">
Tarun Shankar Mathur
</td>
<td style="text-align:left;">
PMC-10339
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
TANDEM LEAP PTE LTD
</td>
<td style="text-align:left;">
6521 3777
</td>
<td style="text-align:left;">
9027 2039
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
Branding Business Planning Corporate Training International Business
Marketing New Marketing Development Product Innovation Product Mix
Service Excellence Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Yip Wai, Ezekiel
</td>
<td style="text-align:left;">
PMC-10220
</td>
<td style="text-align:left;">
Executive Director / Principal Consultant
</td>
<td style="text-align:left;">
TEN TALENTUM INTERNATIONAL PTE LTD
</td>
<td style="text-align:left;">
6634 2909
</td>
<td style="text-align:left;">
8777 1110
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Excellence Executive Coaching Brand Strategy &
Implementation Performance Evaluation & Improvement Organization
Development & Restructuring Human Resource Planning & Management
Marketing Strategy & Implementation Human Behaviour Analysis
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Poh Kiong, Leo
</td>
<td style="text-align:left;">
PMC-10853
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
QUALITY ZONE TECHNOLOGIES PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9449 8690
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Automation, Mechanisation Branding Human Resource Management (HRM)
Information Systems Information Technology (IT) Management Information
Technology (IT) Performance Management Marketing Process Re-Engineering
Productivity Diagnosis Productivity/Quality Management Project
Management Technology Adoption

Areas of Consultancy Services Provided: Enterprise Resource Planning
Consultation Technology Solution Consultation Process Flow Optimization
Consultation Technology Integration Consultation Mobile App Consultation
Digital Marketing Strategy Consultation
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Lye Heng, Paul
</td>
<td style="text-align:left;">
PMC-10694
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
CA TRUST PAC
</td>
<td style="text-align:left;">
6336 8772
</td>
<td style="text-align:left;">
9674 2850
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Budgeting & Cashflow Planning Business Management Skills Business
Planning Executive Coaching Financial Management Leadership Coaching
Merger/Acquisition Negotiation Skills Organisation Restructuring
Performance Management Strategic Planning/Management Mergers &
Acquisition (M&A) Financial and Tax Due Diligence Post M&A integration
advisory Corporate Finance and Restructuring Business Transaction
Support including Business Valuation
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Ling Ling, Candy
</td>
<td style="text-align:left;">
PMC-10773
</td>
<td style="text-align:left;">
Co-Founder
</td>
<td style="text-align:left;">
IMPACT ANALYSIS CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6385 1171
</td>
<td style="text-align:left;">
8282 1100
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Excellence Business Planning Corporate Training Cost
Optimisation Customer Service Training Human Resource Management (HRM)
Layout Leadership Coaching Marketing Negotiation Skills Organisation
Development Process Re-Engineering
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Kee Huat
</td>
<td style="text-align:left;">
PMC-10047
</td>
<td style="text-align:left;">
Principal Consultant/Trainer
</td>
<td style="text-align:left;">
OPTIMAL BALANCE PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9618 4792
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Corporate Training Environmental Management System (ISO) Process
Re-Engineering Productivity/Quality Management Quality Management System
(ISO)

Areas of Consultancy Services Provided: ISO 9001, ISO 14001 and OHSAS
18001 Consultancy, Maintenance and Internal Audit Lead/ Internal Auditor
Courses for ISO 9001, ISO 14001 and OHSAS 18001 (Single or Integrated)
Process Improvements for Organisation (Consultancy and Training) Process
(Procedures) Documentation and Training Conduct Training Needs Analysis
and Competency Training Organisation Culture Training, Development and
Implementation Creativity, Productivity and Effectiveness Training
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Jiahui
</td>
<td style="text-align:left;">
PMC-10865
</td>
<td style="text-align:left;">
Managing & Creative Director
</td>
<td style="text-align:left;">
FABLE STUDIO PTE LTD
</td>
<td style="text-align:left;">
6386 8721
</td>
<td style="text-align:left;">
9745 9877
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Management Skills Business Planning Corporate Training
Employee Engagement Franchising Leadership Leadership Coaching
Management Training Process Re-Engineering Product Innovation Training &
Development

Areas of Consultancy Services Provided: Art Direction Brand Strategy
Brand Positioning Branding and Identity Design Communication Strategy
Digital Design Editorial and Packaging Design Spatial Design and
Wayfinding
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Hian Bing, Nolan
</td>
<td style="text-align:left;">
PMC-10129
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
6376 0777
</td>
<td style="text-align:left;">
9823 4445
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Strategic Planning/Management Customer Solutions Strategic Planning
Human Resource Management (HRM) Human Resource Development (HRD)
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Choo Kok, Anderson
</td>
<td style="text-align:left;">
PMC-10616
</td>
<td style="text-align:left;">
Founder / Director
</td>
<td style="text-align:left;">
Biipmi Pte Ltd
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
96988884
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Budgeting & Cashflow Planning Business Management Skills
Business Planning Competency Training Corporate Training Courseware
Development Customer Centric Initiative (CCI) Customer Satisfaction
Research Customer Service Training Data Protection Digital
Transformation Executive Coaching F&B Operations Family Business
Financial Management Hazard Analysis Critical Control Points (HACCP)
Human Capital Management Human Resource Management (HRM) Human Resource
Management (HRM) Training Information Technology (IT) Management
International Business Leadership Leadership Coaching Management
Training Organisation Development Organisation Restructuring Performance
Management Process Re-Engineering Productivity/Quality Management
Project Management Retail Operations Service Excellence Service Training
Strategic Alliance Strategic Planning/Management Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Tan Chiu Ping, Sharon
</td>
<td style="text-align:left;">
PMC-10858
</td>
<td style="text-align:left;">
Senior Manager
</td>
<td style="text-align:left;">
INSTITUTE OF SINGAPORE CHARTERED ACCOUNTANTS (ISCA)
</td>
<td style="text-align:left;">
6597 5519
</td>
<td style="text-align:left;">
9369 4390
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Productivity/Quality Management Technology Adoption Training &
Development

Areas of Consultancy Services Provided: Quality Assurance Review –
Firm’s System of Quality Control review (SSQC 1) Quality Assurance
Review – Signed-off Audit Engagement File review Quality Assurance
Review – Ethics Pronouncement (EP) 200 review Training and Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Syed Abdul Baseer Mansoor
</td>
<td style="text-align:left;">
PMC-10748
</td>
<td style="text-align:left;">
Regional Head of Delivery - Cloud Consultancy
</td>
<td style="text-align:left;">
POINTSTAR PTE LTD
</td>
<td style="text-align:left;">
6773 0987
</td>
<td style="text-align:left;">
+65 8333 0221 / +60 149583221
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Planning Information Systems Organisation Restructuring Process
Re-Engineering Project Management Resource Management Strategic
Planning/Management Supervisory Skills Supply Chain Management Training
& Development

Areas of Consultancy Services Provided: Project Management Organisation
Restructuring Process Re-Engineering Business Planning Training &
Development Supply Chain Management Resource Management Supervisory
Skills Information Systems Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Sun Ting Kung, Jeremy
</td>
<td style="text-align:left;">
PMC-10405
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
ORCADESIGN CONSULTANTS PTE LTD
</td>
<td style="text-align:left;">
6266 1366
</td>
<td style="text-align:left;">
9782 7017
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Customer Centric Initiative (CCI) Product Innovation Training &
Development

Areas of Consultancy Services Provided: Market Landscape Study
Ethnography Customer Insight Research Innovation Workshop Design
Thinking Training Product Design and Development Design Strategy and
Roadmap Design For Manufacturing UX/UI Design Experience and Service
Design 3D CAD and Visualisation
</td>
</tr>
<tr>
<td style="text-align:left;">
Sophia Lee Peck Hwee
</td>
<td style="text-align:left;">
PMC-10559
</td>
<td style="text-align:left;">
Executive Director
</td>
<td style="text-align:left;">
BRIDGE GAP SERVICES PTE LTD
</td>
<td style="text-align:left;">
6634 7112
</td>
<td style="text-align:left;">
9757 0990
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Management Skills Business
Planning Financial Management Project Management

Areas of Consultancy Services Provided: Financial Management Business
Process Improvement & Automation Productivity Improvement Business
Planning & Budgeting Cash Flow & Working Capital Management Internal
Audit & Due Diligence New Start-up Advisory Services Contract CFO
Services
</td>
</tr>
<tr>
<td style="text-align:left;">
Soh Tut Lin
</td>
<td style="text-align:left;">
PMC-10325
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
GOLEMAN CONSULTING (INTERNATIONAL) PTE LTD
</td>
<td style="text-align:left;">
6291 1939
</td>
<td style="text-align:left;">
9001 2000
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Environmental Management System (ISO) Organisation Development Project
Management Relocation Work-Life Strategy ISO 9001 Quality Management
System ISO 14001 Environmental Management System OHSAS 18001/SS506
Safety Management System & Risk Assessment Integrated Management System
(QMS, EMS & OHSMS)

Areas of Consultancy Services Provided: ANSI/API Specification Q1/ TS
29001 - Specification for Quality Program for the Petroleum,
Petrochemical and Natural Gas Industry ISO 13485 Medical Device Quality
Management System TS-01: Good Distribution Practice of Medical Device
Product Certification, e.g Kite Mark and AS
</td>
</tr>
<tr>
<td style="text-align:left;">
Soh Ju Hu
</td>
<td style="text-align:left;">
PMC-10883
</td>
<td style="text-align:left;">
Strategist
</td>
<td style="text-align:left;">
STRATEMENTAL PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9724 2915
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Management Skills Business Planning Customer Centric
Initiative (CCI) Information Systems Logistics Optimisation Research &
Development (R&D) Technology Service Excellence Strategic
Planning/Management Supply Chain Management Technology Adoption Training
& Development

Areas of Consultancy Services Provided: Business Strategy Business
Development Brand Strategy Software & Hardware Product Development UX
Design
</td>
</tr>
<tr>
<td style="text-align:left;">
Kang So-Young
</td>
<td style="text-align:left;">
10290
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
AWAKEN GROUP PTE LTD
</td>
<td style="text-align:left;">
6579 0204
</td>
<td style="text-align:left;">
8339 2644
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Change Management Customer Centric Initiative (CCI) Employee Engagement
Executive Coaching Leadership Leadership Coaching Organisation
Development Performance Management Productivity Diagnosis Service
Excellence Supervisory Skills Wage Restructuring & Flexible Wage Systems
</td>
</tr>
<tr>
<td style="text-align:left;">
Siraj Salman
</td>
<td style="text-align:left;">
PMC-10889
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
TEAM TORCH PTE LTD
</td>
<td style="text-align:left;">
8339 1529
</td>
<td style="text-align:left;">
9666 1947
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Excellence Business Management Skills Business
Planning Change Management Corporate Training Customer Service Training
Leadership Coaching Negotiation Skills Public Relations & Media
Relations Sales Training Service Training
</td>
</tr>
<tr>
<td style="text-align:left;">
Sharath Manohar Rao
</td>
<td style="text-align:left;">
PMC-10913
</td>
<td style="text-align:left;">
Principal Consultant & Director
</td>
<td style="text-align:left;">
ISO CONSULTANTS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8599 3818
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Continuity Management (BCM) Business Excellence Environmental
Management System (ISO) Good Manufacturing Practices Hazard Analysis
Critical Control Points (HACCP) Information Technology (IT) Management
Measurement System Occupational Safety and Health Adminstration (OSHA)
Process Re-Engineering Productivity Diagnosis Quality Management System
(ISO) Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Shahlan Bin Hairalah
</td>
<td style="text-align:left;">
PMC-10223
</td>
<td style="text-align:left;">
Chief Consultant
</td>
<td style="text-align:left;">
Sahl International Pte Ltd
</td>
<td style="text-align:left;">
6387 4050
</td>
<td style="text-align:left;">
90265396
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Management Skills F&B Operations Halal Certification specialty
</td>
</tr>
<tr>
<td style="text-align:left;">
Serene Ong Tee Yuh
</td>
<td style="text-align:left;">
PMC-10577
</td>
<td style="text-align:left;">
Director / Principal Consultant
</td>
<td style="text-align:left;">
LOVEJOY CONSULTANCY SERVICES PTE LTD
</td>
<td style="text-align:left;">
6651 4869
</td>
<td style="text-align:left;">
9862 0759
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Automation, Mechanisation Business Planning Change Management Cost
Optimisation F&B Operations Management Training Process Re-Engineering
Productivity Diagnosis Productivity/Quality Management Resource
Management Supply Chain Management Talent Management

Areas of Consultancy Services Provided: Skill Future Mentorship Business
Operations and Sustainability Training conduct in English or Chinese
Language
</td>
</tr>
<tr>
<td style="text-align:left;">
Ruth Peck Choon Lian
</td>
<td style="text-align:left;">
PMC-10104
</td>
<td style="text-align:left;">
Director / Principal Consultant
</td>
<td style="text-align:left;">
Eben Consultants (F.E.) Pte Ltd Email: <ruth@ebencon.com> Website:
www.ebencon.com
</td>
<td style="text-align:left;">
6566 9337
</td>
<td style="text-align:left;">
9119 2601
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

· Business Continuity Management (BCM) · Business Excellence (BE) ·
Service Excellence · Data Protection Trustmark Certification (DPTM) ·
Corporate Training · Customer Service Training · Human Resource
Management (HRM) Training · Factory Layout Consulting · Negotiation
Skills

Productivity & Management Systems · Enhancing Business Processes for
Productivity · Productivity Diagnosis and Measurement

ISO Management Systems: (i) Quality Management System - ISO 9001 (ii)
OH&S Health & Safety - ISO 45001 (iii) Environmental Management System -
ISO 14001 (iv) Food Safety & Factory Layout - ISO 22000 & Hazard
Analysis Critical Control Points (SS 590 HACCP) (v) Business Continuity
Management - ISO22301

Training & Development - WSQ Employability, Retail, Business Management
and Service Excellence Framework Training (Approved SSG Funded
courses) - Certified WSQ Trainer, Assessor & Developer ( ACTA & DACE
Qualifications) - Consulting Advice in setting up SSG - Approved
training organisation - WSQ Courseware development for SSG accreditation
</td>
</tr>
<tr>
<td style="text-align:left;">
Roshni Pandey
</td>
<td style="text-align:left;">
PMC-10868
</td>
<td style="text-align:left;">
Managing Partner / Director
</td>
<td style="text-align:left;">
LEXICON BLUE
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Budgeting & Cashflow Planning Business Planning Change
Management Customer Satisfaction Research Financial Management
Information Technology (IT) Management International Business Marketing
New Marketing Development Product Innovation Strategic
Planning/Management

Areas of Consultancy Services Provided: Strategy, Planning & Measurement
Branding, Innovation, Insights, Trends, Marketing Change Management,
Business Process Re-engineering, Digital Transformation, Financial
Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Rose Tan Hoon Hoon
</td>
<td style="text-align:left;">
PMC-10930
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
CROWD PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9815 1538
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Corporate Training Crisis Management Marketing Public Relations
& Media Relations

Areas of Consultancy Services Provided: Scenario Planning Media Training
Events Ideation
</td>
</tr>
<tr>
<td style="text-align:left;">
Ron Lim
</td>
<td style="text-align:left;">
PMC-10842
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
THEPIXELAGE PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9270 6181
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Information Systems Marketing New Marketing Development Project
Management

Areas of Consultancy Services Provided: Marketing & Branding Project
Management Information Systems
</td>
</tr>
<tr>
<td style="text-align:left;">
Roch Tay
</td>
<td style="text-align:left;">
PMC-10678
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
CLOVER CORPORATE ADVISORY PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9742 2005
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Management Skills Change Management Compensation & Benefits
Employee Engagement Financial Management Merger/Acquisition Organisation
Restructuring Strategic Alliance Strategic Planning/Management Talent
Management

Areas of Consultancy Services Provided: Financial Advisory Strategy
Advisory Risk Management Advisory Human Capital Advisory Mergers &
Acquisitions
</td>
</tr>
<tr>
<td style="text-align:left;">
Robin Woon Kong Meng
</td>
<td style="text-align:left;">
PMC-10051
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
BUSINESS QUALITY CONSULTANTS
</td>
<td style="text-align:left;">
6372 1365
</td>
<td style="text-align:left;">
9689 3998
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Budgeting & Cashflow Planning Business Continuity
Management (BCM) Business Excellence Business Planning Cost Optimisation
Crisis Management Environmental Management System (ISO) F&B Operations
Financial Management Good Manufacturing Practices Hazard Analysis
Critical Control Points (HACCP) Lean Management Occupational Safety and
Health Adminstration (OSHA) Performance Management Process
Re-Engineering Product Innovation Product Mix Productivity Diagnosis
Productivity Improvement Project Project Management Quality Management
System (ISO) Retail Operations Retail Performance Measurement SME
Management Action For Results (SMART) Initiative Strategic
Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Pua Seek Eng
</td>
<td style="text-align:left;">
PMC-10272
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
ANTICS HOLDINGS PTE LTD
</td>
<td style="text-align:left;">
6438 7592
</td>
<td style="text-align:left;">
9009 8923
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Planning Crisis Management Customer Satisfaction
Research Executive Coaching Marketing New Marketing Development Public
Relations & Media Relations Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Pooja Grover
</td>
<td style="text-align:left;">
PMC-10200
</td>
<td style="text-align:left;">
Brand Director
</td>
<td style="text-align:left;">
DIA BRAND CONSULTANTS PTE LTD
</td>
<td style="text-align:left;">
6735 3696
</td>
<td style="text-align:left;">
9069 8650
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Employee Engagement Marketing New Marketing Development
Strategic Planning/Management Training & Development

Areas of Consultancy Services Provided: Brand Research, Brand Strategy
Brand Communications Internal Alignment / Employee Engagement
</td>
</tr>
<tr>
<td style="text-align:left;">
Peh Chee Way, Larry
</td>
<td style="text-align:left;">
PMC-10769
</td>
<td style="text-align:left;">
Managing Director & Creative Director
</td>
<td style="text-align:left;">
&LARRY PTE LTD
</td>
<td style="text-align:left;">
6636 1308
</td>
<td style="text-align:left;">
9732 6798
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Corporate Training Employee Engagement Leadership Leadership
Coaching Management Training Product Innovation Project Management
Training & Development

Areas of Consultancy Services Provided: Design Consultancy Branding
Strategy Branding & Identity Design Art Direction & Graphic Design
Spatial Planning & Design Digital Design
</td>
</tr>
<tr>
<td style="text-align:left;">
Pearl Teo Bee Bee
</td>
<td style="text-align:left;">
PMC-10258
</td>
<td style="text-align:left;">
Managing Director & Principal Consultant
</td>
<td style="text-align:left;">
HOSPITALITY INTERNATIONAL SERVICES PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8388 4383
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Corporate Training Customer Centric Initiative (CCI) F&B Operations
Franchising Leadership New Marketing Development Productivity/Quality
Management Service Excellence Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Nicholas Paul
</td>
<td style="text-align:left;">
PMC-10888
</td>
<td style="text-align:left;">
Principal
</td>
<td style="text-align:left;">
ARETÉSE
</td>
<td style="text-align:left;">
6733 3367
</td>
<td style="text-align:left;">
9858 6181
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Benchmarking Branding Change Management Customer Satisfaction Research
Employee Engagement Marketing New Marketing Development Product
Innovation

Areas of Consultancy Services Provided: OBSERVATION Brand Audit,
Research and Analytics, Behavioural Insights, Media and Sentiment
Analysis, Trends Mapping, Forecast Evaluation EXPLORATION Future
Visioning, New Trajectories, Perceptual Mapping, Segmentation,
Experience Innovation, Business Modelling, Platforms & Eco Systems
DEFINITION Brand Idea, Brand Strategy, Value Proposition, Employee Value
Proposition, Visual and Verbal Identity System, Content and Messaging
Frameworks, Customer Journey, Digital and Marketing Strategy
TRANSFORMATION Brand Management Systems, Culture Alignment. Employee
Engagement
</td>
</tr>
<tr>
<td style="text-align:left;">
Ng Tong Yong
</td>
<td style="text-align:left;">
PMC-10357
</td>
<td style="text-align:left;">
Senior Management Consultant
</td>
<td style="text-align:left;">
SMC MANAGEMENT PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9225 8815
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Continuity Management (BCM) Environmental Management System
(ISO) Hazard Analysis Critical Control Points (HACCP) Management
Training Occupational Safety and Health Administration (OSHA)
Productivity/Quality Management Quality Management System (ISO) Supply
Chain Management

Areas of Consultancy Services Provided: Singapore Certified Energy
Manager (SCEM) Certified Ergonomic Assessor (CEA) Environmental Control
Officer (ECO) Ozone Depletion Study/Assessor (ODS) Company Emergency
Response Team Training (CERT) Fire Safety Manager (FSM) Environmental
Baseline Study (EBS) EHS Due Diligence Audit
</td>
</tr>
<tr>
<td style="text-align:left;">
Ng Swee Kheng, John
</td>
<td style="text-align:left;">
PMC-10838
</td>
<td style="text-align:left;">
Chief Passionary Officer
</td>
<td style="text-align:left;">
META CONSULTING
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9666 6470
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Change Management Customer Centric Initiative (CCI) Customer Service
Training Employee Engagement Executive Coaching Leadership Leadership
Coaching Management Training Organisation Development Team Excellence

Areas of Consultancy Services Provided: Leadership Development Cultural
Change Transformation Customer-Centric Transformation
</td>
</tr>
<tr>
<td style="text-align:left;">
Ng Mi Li
</td>
<td style="text-align:left;">
PMC-10744
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
ROCKBELL INTERNATIONAL SOFTWARE PTE LTD
</td>
<td style="text-align:left;">
6469 7720
</td>
<td style="text-align:left;">
9764 7008
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Excellence Business Planning Franchising Human Resource
Management (HRM) Human Resource Management (HRM) Training Information
Technology (IT) Management Information Technology (IT) Performance
Management Marketing Project Management Public Relations & Media
Relations Sales Training & Development

Areas of Consultancy Services Provided: Human Resource Management (HRM)
Information Technology (IT) Management Financial Management
Productivity/Quality Management Business Planning and Management Sales
Training and Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Ng Kok Chuan
</td>
<td style="text-align:left;">
PMC-10892
</td>
<td style="text-align:left;">
Chief Transformation Consultant
</td>
<td style="text-align:left;">
X13 CONSULTING PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9828 6371
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Excellence Change Management Corporate Training
EduTrust Information Technology (IT) Management Lean Management
Organisation Development Organisation Restructuring Performance
Management Process Re-Engineering Strategic Planning/Management

Areas of Consultancy Services Provided: Strategic Planning/Management
Information Technology (IT) Management Lean Management Branding Change
Management EduTrust Business Excellence Corporate Training Performance
Management Process Re-Engineering Organisation Restructuring
Organisation Development Digital Marketing
</td>
</tr>
<tr>
<td style="text-align:left;">
Ng Chee Yong
</td>
<td style="text-align:left;">
PMC-10825
</td>
<td style="text-align:left;">
Creative Director
</td>
<td style="text-align:left;">
SOMEWHERE ELSE
</td>
<td style="text-align:left;">
6297 7749
</td>
<td style="text-align:left;">
9684 9295
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Marketing New Marketing Development Product Innovation Public
Relations & Media Relations

Areas of Consultancy Services Provided: Brand Strategy Branding Design
</td>
</tr>
<tr>
<td style="text-align:left;">
Neo Tiong Wee
</td>
<td style="text-align:left;">
PMC-10309
</td>
<td style="text-align:left;">
General Manager
</td>
<td style="text-align:left;">
KAIZEN CONSULTING GROUP
</td>
<td style="text-align:left;">
6848 4109
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Corporate Training Lean Management Management Training Process
Re-Engineering Productivity/Quality Management

Areas of Consultancy Services Provided: Lean Six Sigma Business Strategy
Development Process Redesign Innovation and Productivity Product
Development Human Capital Development Service Excellence Strategic Brand
and Marketing Development Automation and technology Standards Adoption
Digital Marketing Digitalization Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Mok Hiang Teck
</td>
<td style="text-align:left;">
PMC-10429
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
GT CONSULTANCY AND TRAINING SERVICES
</td>
<td style="text-align:left;">
6767 5240
</td>
<td style="text-align:left;">
9780 5289
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Continuity Management (BCM) Business Excellence Business
Management Skills Business Planning Environmental Management System
(ISO) Occupational Safety and Health Adminstration (OSHA)
Productivity/Quality Management Quality Management System (ISO) Training
& Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Mohd Daud Abdul Rahim
</td>
<td style="text-align:left;">
PMC-10070
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
QIROM CONSULTING PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9170 8335
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Financial Management Information Systems Productivity/Quality Management
IT Project Management Business Process and Information System Analysis
E-learning and Instructional Design Training Software Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Michelle Pang Mei Chi
</td>
<td style="text-align:left;">
PMC-10216
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
GL CONSULTANCY & SERVICES
</td>
<td style="text-align:left;">
6353 6003
</td>
<td style="text-align:left;">
9682 6817
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
SS 444 Hazard Analysis and Critical Control Point (HACCP) System SS 590
Food Safety Management System ISO 9001 Quality Management System ISO
22000 Food Safety Management System FSSC 22000 Food Safety Management
System ISO 22716 Good Manufacturing Practices Good Manufacturing
Practices Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Michelle Chew Hoi Chian
</td>
<td style="text-align:left;">
PMC-10076
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
GREENWICH MANAGEMENT CONSULTANCY PTE LTD
</td>
<td style="text-align:left;">
6408 3308
</td>
<td style="text-align:left;">
9876 6828
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Continuity Management (BCM) Business Excellence CaseTrust
EduTrust Environmental Management System (ISO) Hazard Analysis Critical
Control Points (HACCP) Productivity/Quality Management Quality
Management System (ISO)

Full consultancy and training for Integrated Management Systems for
various standards / industries: ISO 22301 (Business Continuity
Management System) Emergency Response Planning and Exercises Scenario
Planning ISO 27001 (Information Security Management System) Digital
Project Implementation and Management B2B/B2C Apps Development and
Implementation Business Data Analytics Business Process Re-engineering
Compliance with PDPA and related data protection legislation Data
Protection Trustmark (DPTM) ISO 37001 (Anti-Bribery Management System)
Risk Management for Corruption and Bribery Whistle Blow for
(Anti-Bribery Management System) ISO 9001 (Quality Management System)
Industry specific standards: AS 9100, IATF 16949, ISO 13485, GDPMDS (SS
620), GDP, ISO 17025, ISO 17020, ISO 29990 Customised Productivity
Improvement Projects ISO 14000 (Environmental Management System) SS 506
/ OHSAS 18000 / ISO 45001 (Health & Safety Management System) BizSAFE SS
564 (Green Data Centres – Energy & Environmental Management System)
HACCP ISO 22000 Halal Product Registration (normal route) Priority
Review Route Special Access Routes (SAR) Singapore Quality Class/People
Developer/Service Class CaseTrust EduTrust
</td>
</tr>
<tr>
<td style="text-align:left;">
Michael M Lee
</td>
<td style="text-align:left;">
PMC-10703
</td>
<td style="text-align:left;">
Founder & Director
</td>
<td style="text-align:left;">
THE CFO DESK PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8506 0700
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Corporate Training Financial Management
Information Systems Information Technology (IT) Management Information
Technology (IT) Performance Management Merger/Acquisition

Areas of Consultancy Services Provided: Information Technology (IT)
Solutions IPO Advisory Corporate Training Finance Advisory
</td>
</tr>
<tr>
<td style="text-align:left;">
Magdelene Teo Hwee Cheng
</td>
<td style="text-align:left;">
PMC-10130
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
SINGAPORE PRODUCTIVITY CENTRE
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9747 8989
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Human Resource Management (HRM) Productivity/Quality Management
Strategic Planning/Management Human Resources Productivity and Quality
Management Strategic Business Planning
</td>
</tr>
<tr>
<td style="text-align:left;">
Mabel Tay Lai Wan
</td>
<td style="text-align:left;">
PMC-10592
</td>
<td style="text-align:left;">
Director/Senior Consultant
</td>
<td style="text-align:left;">
SALTUS CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6483 0483
</td>
<td style="text-align:left;">
9731 3001
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
Branding Marketing Public Relations & Media Relations Brand Analytics
Brand Strategy Brand Identity Design Brand Activation
</td>
</tr>
<tr>
<td style="text-align:left;">
Dr Lynda Wee
</td>
<td style="text-align:left;">
SPMC-10192
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
BOOTSTRAP PTE LTD
</td>
<td style="text-align:left;">
6592 0023
</td>
<td style="text-align:left;">
9618 9617
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Strategy Development Design Thinking Strategic Brand and
Marketing Development Strategic Human Capital Development Leadership
Development Workplace Learning Retail and Value Channel Creation
Customer Experience Excellence
</td>
</tr>
<tr>
<td style="text-align:left;">
Lum Kok Meng
</td>
<td style="text-align:left;">
PMC-10413
</td>
<td style="text-align:left;">
Managing Consultant
</td>
<td style="text-align:left;">
PENN TRADING & CONSULTANCY
</td>
<td style="text-align:left;">
6877 0285
</td>
<td style="text-align:left;">
9186 0083
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Environmental Management System (ISO) Hazard Analysis Critical Control
Points (HACCP) Management Training Occupational Safety and Health
Administration (OSHA) Productivity/Quality Management Quality Management
System (ISO) Supervisory Skills

Areas of Consultancy Services Provided: ISO 9001:2008(QMS)
ISO14001:2004(EMS) OHSAS 18001:2007 AS 9100 Aerospace ISO 13485 Medical
Devices ISO 17025 Laboratory ISO 2200:2005 (FSMS) API Q1 & Monograms Oil
& Gas Industries

BizSafe Level 3 & Star & Partner consulting cum certification

Management Training: 5SHousekeeping, Interaction management, 6N
concepts, Supervisory skills , Cycle-time reduction.
</td>
</tr>
<tr>
<td style="text-align:left;">
Dr. Luk Wai Lun, Alan
</td>
<td style="text-align:left;">
PMC-10898
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
CLOOUD CONSULTING LLP
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9367 6685
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Budgeting & Cashflow Planning Financial Management Human
Resource Management (HRM) Information Technology (IT) Management Lean
Management Marketing Process Re-Engineering Productivity/Quality
Management Sales Strategic Planning/Management Supply Chain Management

Areas of Consultancy Services Provided: Financial Management & Tax
Planning Shared CFO & Outsourcing IT & Business Solution Corporate
Governance & Internal Controls Risk Management & Corporate
Sustainability Company Incorporation & Company Secretary Sales,
Marketing & E-commerce Strategic Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Lucy Loo
</td>
<td style="text-align:left;">
PMC-10184
</td>
<td style="text-align:left;">
Lead Consultant
</td>
<td style="text-align:left;">
AIO CONSULTANCY PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9018 1437
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Budgeting & Cashflow Planning CaseTrust Customer Service Training
Financial Management Human Resource Management (HRM) Information Systems
Process Re-Engineering Productivity Diagnosis Productivity/Quality
Management Sales Training Training & Development Financial Management
Information Systems Business Process Reengineering
</td>
</tr>
<tr>
<td style="text-align:left;">
Lua Eng San, Richard Lua
</td>
<td style="text-align:left;">
PMC-10863
</td>
<td style="text-align:left;">
Creative Director
</td>
<td style="text-align:left;">
DALMATION PTE LTD
</td>
<td style="text-align:left;">
6522 2812
</td>
<td style="text-align:left;">
9109 9502
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Planning Corporate Training Customer Service Training
Layout Marketing New Marketing Development Project Management Public
Relations & Media Relations Service Training Strategic
Planning/Management Training & Development

Areas of Consultancy Services Provided: Branding Exercise Marketing
Campaign Creative Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Lu-Ann Ong
</td>
<td style="text-align:left;">
PMC-10479
</td>
<td style="text-align:left;">
Managing Partner / Principal Consultant
</td>
<td style="text-align:left;">
1920 INCORPORATE LLP. Phone Number: 8161 6220 / 8686 2366 Email:
<luann@1920inc.com> Website: www.1920inc.com
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8161 6220 / 8686 2366
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Strategic Brand and Communications Sustainability SME Strategy and
Growth Models Human Capital
</td>
</tr>
<tr>
<td style="text-align:left;">
Low Yee Chang
</td>
<td style="text-align:left;">
PMC-10633
</td>
<td style="text-align:left;">
Managing Consultant
</td>
<td style="text-align:left;">
DOUBLE BASS CONSULTING
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9825 6695
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Automation, Mechanisation Business Management Skills Change Management
Corporate Training Human Resource Management (HRM) Training Leadership
Coaching Lean Management Management Training Organisation Development
Performance Management Process Re-Engineering Product Innovation
Productivity Diagnosis Productivity/Quality Management Project
Management Technology Adoption Training & Development

Areas of Consultancy Services Provided: Lean Six Sigma Operational
Excellence for SMEs Program Management Best Practice Productivity
Diagnosis and Improvement SMEs Leadership Development SMEs Global
Business Skills Design Thinking Agile Enterprise Agile Product
Development

Design and Implement:

- Skills Future Mentorship Program
- Workplace Learning Program
- On-The-Job (OJT) Blueprint and 5 Steps Coaching
- Program Management Best Practice
- Lean Operational Excellence
  </td>
  </tr>
  <tr>
  <td style="text-align:left;">
  Low Gim Boon (Jimmy)
  </td>
  <td style="text-align:left;">
  PMC-10832
  </td>
  <td style="text-align:left;">
  Senior Manager
  </td>
  <td style="text-align:left;">
  WIZLEARN TECHNOLOGIES PTE LTD
  </td>
  <td style="text-align:left;">
  6776 2013
  </td>
  <td style="text-align:left;">
  9068 7800
  </td>
  <td style="text-align:left;">
  VERIFIED
  </td>
  <td style="text-align:left;">
  LIVE
  </td>
  <td style="text-align:left;">
  Corporate Training Customer Service Training Franchising Human
  Resource Management (HRM) Training Industry Relations (IR) Training
  Information Systems Information Technology (IT) Management Information
  Technology (IT) Performance Management Management Training Sales
  Training Service Training Situation Management Technology Adoption
  Training & Development
  </td>
  </tr>
  <tr>
  <td style="text-align:left;">
  Liu Siew Mui, Pamela
  </td>
  <td style="text-align:left;">
  PMC-10939
  </td>
  <td style="text-align:left;">
  Founder/Chief Executive
  </td>
  <td style="text-align:left;">
  EBIZ PTE LTD
  </td>
  <td style="text-align:left;">
  NA
  </td>
  <td style="text-align:left;">
  9435 7225
  </td>
  <td style="text-align:left;">
  VERIFIED
  </td>
  <td style="text-align:left;">
  LIVE
  </td>
  <td style="text-align:left;">
  Branding Business Planning Corporate Training Customer Satisfaction
  Research Executive Coaching Export Strategy F&B Operations Franchising
  Information Systems Information Technology (IT) Performance Management
  Intellectual Property Management (IPM) Leadership Leadership Coaching
  Management Training New Marketing Development Organisation Development
  Product Innovation Research & Development (R&D) Technology Retail
  Performance Measurement Sales Sales Training Strategic
  Planning/Management Team Excellence Technology Adoption Training &
  Development Work-Life Strategy

Areas of Consultancy Services Provided: End to end e-commerce: from
ideation to e-store to customer retention Moving companies online
end-to-end consultancy Digitization of brick/mortar companies or new
product lines for local, international or regional expansion
</td>
</tr>
<tr>
<td style="text-align:left;">
Lin Shaorong, Kelvin
</td>
<td style="text-align:left;">
PMC-10934
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
NEXUS MANAGEMENT SERVICES PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8322 2037
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Automation, Mechanisation Business Planning Change Management Human
Resource Management (HRM) Performance Management Process Re-Engineering
Product Innovation Productivity Diagnosis Productivity/Quality
Management Project Management Retail Operations Sales Service Excellence
Strategic Alliance Strategic Planning/Management Technology Adoption

Areas of Consultancy Services Provided: Business Transformation Business
Strategy Workflow Optimisation and Process Re-Engineering Change
Management HR & Operations Management Overseas Market Research and
Analysis
</td>
</tr>
<tr>
<td style="text-align:left;">
Lim Yeong Seng
</td>
<td style="text-align:left;">
PMC-10377
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
YVES STRATEGIC MANAGEMENT PTE LTD
</td>
<td style="text-align:left;">
6227 4180
</td>
<td style="text-align:left;">
9628 7047
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Planning Financial Management
Human Resource Management (HRM) Merger/Acquisition SME Management Action
For Results (SMART) Initiative Strategic Planning/Management

Areas of Consultancy Services Provided: Financial Controls Financial
Management Advisory Financial & Business Assessment for growth Cash-flow
& Working Capital Management Planning & Budgeting
</td>
</tr>
<tr>
<td style="text-align:left;">
Lim Siok Hwee
</td>
<td style="text-align:left;">
PMC-10202
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
GLOBAL NETWORK UNLIMITED PTE LTD
</td>
<td style="text-align:left;">
6226 5338
</td>
<td style="text-align:left;">
9853 3687
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Planning Customer Centric Initiative (CCI) Franchising
Intellectual Property Management (IPM)

Areas of Consultancy Services Provided: Intellectual Property Management
Branding Licensing /Franchising
</td>
</tr>
<tr>
<td style="text-align:left;">
Lim Chun Seong
</td>
<td style="text-align:left;">
PMC-10937
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
LANCIA CONSULT
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8223 2692
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Change Management Cost Optimisation Lean Management Project Management
Strategic Planning/Management Supply Chain Management IT and Digital
Transformation and Execution

Areas of Consultancy Services Provided: Business Strategy Development
Financial Management and Transformation Workforce and Human Capital
Strategy Service Design and Customer Experience Strategy Brand Strategy
and Digital Marketing Services Mergers and Acquisition Advisory and
Support
</td>
</tr>
<tr>
<td style="text-align:left;">
Liew Ee Bin
</td>
<td style="text-align:left;">
PMC-10815
</td>
<td style="text-align:left;">
Owner
</td>
<td style="text-align:left;">
ACCESS-2-HEALTHCARE
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9145 4556
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Management Skills Business Planning Competency Training
Executive Coaching Good Manufacturing Practices Information Systems
International Business Marketing Merger/Acquisition New Marketing
Development Productivity/Quality Management Project Management

Areas of Consultancy Services Provided: Product Commercialization Market
Entry - Global Regulatory Affairs and Trade Compliance Quality
Consulting Business due diligence (healthcare industry) – business
partner sourcing Medical technology screening and assessment for
institutions, funding agencies, and government Training and education of
medical technologies, standards, regulations, design & development
</td>
</tr>
<tr>
<td style="text-align:left;">
Leonard Ling
</td>
<td style="text-align:left;">
SPMC-10459
</td>
<td style="text-align:left;">
Director/Principal Consultant
</td>
<td style="text-align:left;">
SOLUTIONSATWORK PTE LTD
</td>
<td style="text-align:left;">
6632 3677
</td>
<td style="text-align:left;">
9675 2001
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Compensation & Benefits Human Resource Management (HRM) Organisation
Development Organisation Restructuring Performance Management Process
Re-Engineering Service Excellence Talent Management Team Excellence
Training & Development Wage Restructuring & Flexible Wage Systems
Work-Life Strategy

Areas of Consultancy Services Provided: Organization Structuring
Performance Management Balanced Scorecard Development Talent Management,
Career Progression and Succession Planning Competency Mapping
Instructional Design and Corporate Training Training Impact Measurement
Behavioural Profiling and Team-building
</td>
</tr>
<tr>
<td style="text-align:left;">
Lee Wan Khum
</td>
<td style="text-align:left;">
PMC-10444
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
PATMOS MANAGEMENT SERVICES PTE LTD
</td>
<td style="text-align:left;">
6692 2328
</td>
<td style="text-align:left;">
9820 7336
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Planning Financial Management
International Business Organisation Restructuring Process Re-Engineering
Project Management Strategic Planning/Management

Areas of Consultancy Services Provided: Contract CFO/FC Services
Financial Planning, Budgeting & Reporting, and Cash Flow Projections
Project Accounting & Cost Control for Operations Financial Modelling and
Strategic Business Advisory for Start-up, New Venture and Expansion
Projects Tax Planning, Advice and Compliance, including Global Entity
Structure for Projects, Government Incentives & Assistance Schemes
Restructuring of Finance and Accounting Organisation, including Post M&A
Integration and Transformation to Share Services Capability Business
Process Re-engineering and Formulation of Finance Handbook Internal
Controls and Finance Best Practice Review Implementation of Information
System/ERP Projects Company Secretarial Support Training on Financial
Management & Reporting, Process Improvement
</td>
</tr>
<tr>
<td style="text-align:left;">
Lee Meng Li, Adrian
</td>
<td style="text-align:left;">
PMC-10928
</td>
<td style="text-align:left;">
Sole Proprietor
</td>
<td style="text-align:left;">
3AI
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9620 8210
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Information Systems Information Technology (IT) Performance Management

Areas of Consultancy Services Provided: Security Technologies &
Management (Operations & Planning) Bomb (UXO/IED) Disposal Chemical,
Biological, Radiological & Explosive (CBRE) including Blast & CBR
modelling Crisis Management & Emergency Planning Business Continuity
Planning (BCP) Threat, Vulnerability & Risk Assessment (TVRA)
Counterterrorism (CT) Training and Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Lee May Lian, Miranda
</td>
<td style="text-align:left;">
PMC-10805
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
KPMG SERVICES PTE LTD
</td>
<td style="text-align:left;">
6507 1946
</td>
<td style="text-align:left;">
9669 8600
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Excellence Change Management Customer Satisfaction Research
Human Resource Management (HRM) Organisation Development Performance
Management Process Re-Engineering Project Management Strategic
Planning/Management Talent Management

Areas of Consultancy Services Provided: Strategic planning
Corporatisation/Privatisation Outsourcing and Shared Services Centre
Feasibility Study Organisational Design and Development Programme and
Project Management Process Re-engineering HR Transformation - Talent
Attraction and Retention Strategy - Compensation and Benefits Design -
Manpower Modelling - Career and Succession Planning - Learning and
Development - Functional Competency Development - Performance Management
System Change and Communications Customer Relationship Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Lee Kok Hung, Simon
</td>
<td style="text-align:left;">
PMC-10850
</td>
<td style="text-align:left;">
Managing & Creative Director
</td>
<td style="text-align:left;">
VANTAGE BRANDING
</td>
<td style="text-align:left;">
6722 3908
</td>
<td style="text-align:left;">
9046 2260
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Marketing Strategic Planning/Management Technology Adoption

Areas of Consultancy Services Provided: Defining Brands – Brand Strategy
– Brand Identity

Creating Brand Experiences – Websites – Retail & Environment – Print &
digital communications – Video
</td>
</tr>
<tr>
<td style="text-align:left;">
Lee Huang Han, Sean
</td>
<td style="text-align:left;">
PMC-10911
</td>
<td style="text-align:left;">
Principal Coach-Consultant
</td>
<td style="text-align:left;">
TOTUS LEARNING SOLUTIONS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9738 6112
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Automation, Mechanisation Branding Business Continuity Management (BCM)
Business Excellence Business Management Skills Business Planning
CaseTrust Change Management Competency Training Crisis Management
Customer Satisfaction Research Employee Engagement Environmental
Management System (ISO) Executive Coaching Export Strategy Franchising
Human Resource Management (HRM) Industry Relations (IR) Training
Leadership Leadership Coaching Organisation Development Strategic
Planning/Management Team Excellence Training & Development

Areas of Consultancy Services Provided: Leadership, Organisational
Development and Organisational Learning Strategic Thinking, Strategy,
Strategic Management and Change Management Security Management and
Business Continuity Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Lavanya D/O Novem
</td>
<td style="text-align:left;">
PMC-10624
</td>
<td style="text-align:left;">
Principal Consultant/Director
</td>
<td style="text-align:left;">
QUALITY SAFE CONSULTANTS PTE LTD
</td>
<td style="text-align:left;">
6261 8150
</td>
<td style="text-align:left;">
9760 0070
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Continuity Management (BCM) Competency Training Corporate
Training Crisis Management Environmental Management System (ISO) Good
Manufacturing Practices Hazard Analysis Critical Control Points (HACCP)
Management Training Occupational Safety and Health Administration (OSHA)
Productivity/Quality Management Quality Management System (ISO) Training
& Development

Areas of Consultancy Services Provided: ISO 9001:2015 (QMS) Consultancy
BS OHSAS 18001:2007 (OHSMS) Consultancy ISO 14001:2015 (EMS) Consultancy
ISO 22301:2012 (BCM) Consultancy ISO 29990:2010 (LSP) Consultancy ISO
22000:2005 (FSMS) Consultancy Hazard Analysis Critical Control Points
(HACCP) bizSAFE Program Integrated Management System ISO Standards WSH
Act Extension, Overtime Exemption, Factory Registration MOM Approved WSH
Audits MOM Registered WSH Officers SCDF Registered Fire Safety Managers
NEA Registered Environmental Control Officers
</td>
</tr>
<tr>
<td style="text-align:left;">
Kuppan Karuppiah
</td>
<td style="text-align:left;">
PMC-10404
</td>
<td style="text-align:left;">
Director/Principal Consultant
</td>
<td style="text-align:left;">
GREENSAFE INTERNATIONAL PTE LTD
</td>
<td style="text-align:left;">
6297 0118
</td>
<td style="text-align:left;">
9857 8717
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Continuity Management (BCM) Competency Training Corporate
Training Crisis Management Environmental Management System (ISO) Hazard
Analysis Critical Control Points (HACCP) Management Training
Occupational Safety and Health Administration (OSHA)
Productivity/Quality Management Project Management Quality Management
System (ISO) SME Management Action For Results (SMART) Initiative

Areas of Consultancy Services Provided: ISO 9001:2008 (QMS) / ISO
14001:2004 (EMS) / ISO 50001:2011 (EnMS) BS OHSAS 18001:2007 / SS 506 :
PART 1 & PART 3 ISO 26001 : 2010 (CSR) / ISO 14064 : 2006 SS 540 / BS
25999 (BCM) ISO 22000 : 2005 (FSMS) / ISO 27001 : 2005 (ISMS)
Environmental Sustainability Consulting / Energy Audit & Conservation
CONSASS Audit & Performance Improvement advisory EHS Legal Compliance
Audit / bizSAFE Program QEHS Audit support for specific customer audit
WSH Act Extension, Overtime Exemption, Factory Registration Risk
Management Consulting and Auditing on MOM and NEA Approved Safety Audits
MOM Registered WSH Officer MOM Approved Risk Consultant & Auditor SCDF
Registered Fire Safety Manager NEA Registered Environmental Control
Officer WDA & MOM Approved Trainer ( ACTA) Port facility security
officer ( PFSO) & Training services Construction & Project Management
Services
</td>
</tr>
<tr>
<td style="text-align:left;">
Kuo Yi Ju, Peggy
</td>
<td style="text-align:left;">
PMC-10845
</td>
<td style="text-align:left;">
Executive Director
</td>
<td style="text-align:left;">
APAC CONSULTING MEDIA PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9826 0663
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Budgeting & Cashflow Planning Business Management Skills
Business Planning Customer Centric Initiative (CCI) International
Business Leadership Lean Management Marketing Negotiation Skills New
Marketing Development Strategic Alliance

Areas of Consultancy Services Provided: Go-to-China Marketing Strategy
and Consulting Digital Marketing and Branding Management Digital and
eCommerce Strategy and Implementation APAC Regional Market Expansion and
Strategy Digital and Mobile Product Development Chinese Consumer Insight
research Competitors Analysis / SWOT Analysis
</td>
</tr>
<tr>
<td style="text-align:left;">
Koh Chin Beng
</td>
<td style="text-align:left;">
SPMC-10821
</td>
<td style="text-align:left;">
Partner
</td>
<td style="text-align:left;">
BDO LLP
</td>
<td style="text-align:left;">
6828 9151
</td>
<td style="text-align:left;">
9322 3934
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Continuity Management (BCM)
Environmental Management System (ISO) F&B Operations Financial
Management Merger/Acquisition Performance Management Process
Re-Engineering Project Management Strategic Planning/Management Supply
Chain Management Training & Development

Areas of Consultancy Services Provided: Business Process Improvement
Enterprise Risk Management Financial Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Kim Chun Wei
</td>
<td style="text-align:left;">
PMC-10112
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
UKULELE BRAND CONSULTANTS PTE LTD
</td>
<td style="text-align:left;">
6225 8100
</td>
<td style="text-align:left;">
9662 6621
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding

Areas of Consultancy Services Provided: - Branding - Branding Strategy
Development - Brand Audit & Research - Brand Identity Implementation -
Brand Communication Design & Development - Brand Systematisation - Brand
Culturalisation/Training - Certified CMC Consultant
</td>
</tr>
<tr>
<td style="text-align:left;">
Khin Ng
</td>
<td style="text-align:left;">
PMC-10255
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
IMAGINNE ASIA
</td>
<td style="text-align:left;">
6909 2779
</td>
<td style="text-align:left;">
9618 6539
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Design: Communications, Packaging, Retail Experience, Sound &
Animation
</td>
</tr>
<tr>
<td style="text-align:left;">
Kathuria Ujjwal Deep Kaur
</td>
<td style="text-align:left;">
PMC-10890
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
HUNET PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9220 9478
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Automation, Mechanisation Change Management Compensation & Benefits
Corporate Training Employee Engagement Human Resource Management (HRM)
Leadership Organisation Development Performance Management Process
Re-Engineering Talent Management Training & Development

Areas of Consultancy Services Provided: Human Resource Management (HRM)
Change Management Performance Management Training & Development
Compensation & Benefits Organisation Development Talent Management
Employee Engagement Corporate Training Process Re-Engineering Leadership
Development Human Capital Management Automation Human Resource
Outsourcing Human Resource Advisory Training and Assessment (ACTA)
</td>
</tr>
<tr>
<td style="text-align:left;">
Kairos Yu Yu Chiu
</td>
<td style="text-align:left;">
PMC-10454
</td>
<td style="text-align:left;">
Director Consultant
</td>
<td style="text-align:left;">
KAIROS GLOBAL PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8399 8322
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Excellence Business Planning
Change Management Corporate Training Cost Optimisation Executive
Coaching Financial Management International Business Merger/Acquisition
Productivity Diagnosis Strategic Planning/Management

Areas of Consultancy Services Provided: Financial Statements
Re-construction Tax Compilation Budgeting and Cashflow Planning ICV
Service Provider for Financial Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Julia Kwok Yung Chu
</td>
<td style="text-align:left;">
PMC-10292
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
OLEA PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9664 3747
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Cost Optimisation F&B Operations Financial
Management Merger/Acquisition Organisation Restructuring Strategic
Planning/Management

Areas of Consultancy Services Provided: Financial Planning & Management
Strategic Growth and Expansion Advisory Financial Management Coaching
</td>
</tr>
<tr>
<td style="text-align:left;">
Judith Chew
</td>
<td style="text-align:left;">
PMC-10779
</td>
<td style="text-align:left;">
Head of Business Services
</td>
<td style="text-align:left;">
WONG FONG ACADEMY PTE LTD
</td>
<td style="text-align:left;">
6863 3686
</td>
<td style="text-align:left;">
9247 6316
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Continuity Management (BCM) Business Excellence Business
Management Skills Business Planning Competency Training Corporate
Training Customer Service Training Management Training Process
Re-Engineering Project Management Sales Training Service Excellence

Areas of Consultancy Services Provided: bizSAFE Consultancy for every
level of the certification process Workplace Training where learning
takes place at work effectively Corporate Training where curriculum is
competency based and tailored to client’s business strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Joshua Chan Chen Koon
</td>
<td style="text-align:left;">
PMC-10279
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
EBEN CONSULTANTS (F.E.) PTE LTD
</td>
<td style="text-align:left;">
6566 9337
</td>
<td style="text-align:left;">
9154 0894
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Continuity Management (BCM) Environmental Management System
(ISO) Hazard Analysis Critical Control Points (HACCP) Lean Management
Merger/Acquisition Occupational Safety and Health Administration (OSHA)
Performance Management Process Re-Engineering Productivity Diagnosis
Productivity/Quality Management Strategic Planning/Management Training &
Development

B.Adoption of Standards to enhance productivity and business capability:
(i) ISO 22301:Business Continuity Management (BCM) (ii) ISO 22000 &
HACCP - Food Safety & Factory Layout ; HALQM F&B using auto payment and
equipment

Integrated Management Systems : (i) ISO 9001:2015 - Quality Management
System (ii) ISO 45001:2018 - Health & Safety (WSH Act 2007) (iii) ISO
14001:2015 - Environmental Management System

Innovation & Technology in processes and service (e.g. Security Services
using UAE Drones, Mobility Devices) WSQ Employability and Service
Framework Courses (Approved SSF Funded courses) Certified WSQ Trainer,
Assessor & Developer in WSQ Employability, Service & Retail Framework 14
WSQ Blended E-Learning Courses accredited by SSG centering on skills and
competent in Leadership, Solving Problem, Innovation, Team Work,
Specific service skills WSQ Course Developer Consulting Advice in
setting up SSG - Approved Training Organisation & Courseware Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Josephine Tam Chin Yen
</td>
<td style="text-align:left;">
PMC-10676
</td>
<td style="text-align:left;">
Associate Director
</td>
<td style="text-align:left;">
BDO CONSULTANTS PTE LTD
</td>
<td style="text-align:left;">
6829 9603
</td>
<td style="text-align:left;">
8322 7095
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Benchmarking Branding Business Excellence Business Planning Financial
Management International Business Marketing Merger/Acquisition SME
Management Action For Results (SMART) Initiative

Areas of Consultancy Services Provided: Financial Management Advisory
Business Excellence & Strategic Transformations Branding and Marketing
International Business Intelligence Human Resource Management Family
Business Advisory
</td>
</tr>
<tr>
<td style="text-align:left;">
Joseph Chian Ker Lit
</td>
<td style="text-align:left;">
PMC-10084
</td>
<td style="text-align:left;">
Principal Consultant & Director
</td>
<td style="text-align:left;">
VIABLE SYSTEMS INNOVATION
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9021 2278
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Benchmarking Branding Business Continuity Management (BCM) Business
Excellence Business Planning EduTrust Environmental Management System
(ISO) Lean Management Occupational Safety and Health Administration
(OSHA) Process Re-Engineering Productivity/Quality Management Strategic
Planning/Management

Areas of Consultancy Services Provided: ISO9001, ISO14001, ISO18001,
ISO17025, ISO13485, TS16949 ISO22001, HACCP / DOC 2 & Prime Law Standard
of Singapore Risk Assessment & Management, SS506, Safety Management
System GDPMDS, CaseTrust, EduTrust, SQCPEO & People Developer Award
Singapore Quality Award / Class & Singapore Service Class 7 QC &
Management Tools & Quality / Innovative Control Circle Process
Flow-Charting & Process Mapping Techniques Six Sigma & Statistical
Process / Quality Control SWOT analysis & Strategic Planning Strategy
Mapping & Balance Score Card Bench-Marking & Business Process
Re-engineering Failure Mode & Effect Analysis & Cost Of Quality 5 S
principles for Total Quality Work Environment Learning Needs Analysis,
OJT & Competency Certification Program Customer Relationship Management
& Supply Chain Management Customer Service Quality & Customer Loyalty
Programs Customer & Employee Satisfaction Survey & Analysis Vendor
Audit, Evaluation & Qualification Other Customized Training &
Consultancy Programs
</td>
</tr>
<tr>
<td style="text-align:left;">
Jon Lim
</td>
<td style="text-align:left;">
PMC-10761
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
PLUTUS CONSULTING
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9430 0210
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Strategic Planning/Management Organisation Restructuring Corporate
Finance
</td>
</tr>
<tr>
<td style="text-align:left;">
John Lin
</td>
<td style="text-align:left;">
SPMC-10907
</td>
<td style="text-align:left;">
Managing Partner
</td>
<td style="text-align:left;">
GET CONSULTING
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
96221785
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Change Management Merger/Acquisition Performance Management
Strategic Planning/Management Talent Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Johan Grundlingh
</td>
<td style="text-align:left;">
SPMC-10373
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
CARROTS CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6842 2131
</td>
<td style="text-align:left;">
9739 3216
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Benchmarking Business Excellence Change Management Compensation &
Benefits Human Resource Management (HRM) Measurement System Organisation
Development Performance Management Talent Management Training &
Development Wage Restructuring & Flexible Wage Systems

Areas of Consultancy Services Provided: Executive Compensation General
Rewards Strategy Mapping (Balance Scorecard) Performance Management Job
Evaluation, Grade & Salary Structure Benefits Design
</td>
</tr>
<tr>
<td style="text-align:left;">
Joey Cai Meiqi
</td>
<td style="text-align:left;">
PMC-10674
</td>
<td style="text-align:left;">
CEO & Principal Consultant
</td>
<td style="text-align:left;">
MARK-DNA
</td>
<td style="text-align:left;">
6926 7341
</td>
<td style="text-align:left;">
9060 5834
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Capability Development Expertise: - Branding and Marketing - Human
Capital Development - Service Excellence
</td>
</tr>
<tr>
<td style="text-align:left;">
Joerin Yao
</td>
<td style="text-align:left;">
PMC-10772
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
ENABLE CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6871 8801
</td>
<td style="text-align:left;">
8133 3727
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Areas of Consultancy Services Provided: Compensation and Benefits Review
HR Policies and Practices (Regional) Performance Management HR
Transformation
</td>
</tr>
<tr>
<td style="text-align:left;">
Dr Jerome Vijeyan Joseph Pillai
</td>
<td style="text-align:left;">
PMC-10208
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
THE BRAND THEATRE PTE LTD
</td>
<td style="text-align:left;">
6288 7812
</td>
<td style="text-align:left;">
9271 6973
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Corporate Training Customer Centric Initiative (CCI) Employee
Engagement Marketing New Marketing Development Occupational Safety and
Health Administration (OSHA) Product Innovation Service Excellence
Service Training Strategic Planning/Management Training & Development

Areas of Consultancy Services Provided: Brand Research Brand Strategy
Brand Management Internal Brand Alignment/ Employee Engagement Branded
Customer Experience Management Strategic Brand Communications Training
and Development in delivering Brand Experience
</td>
</tr>
<tr>
<td style="text-align:left;">
Jean Tan
</td>
<td style="text-align:left;">
PMC-10902
</td>
<td style="text-align:left;">
Owner/Brand and Marketing Consultant
</td>
<td style="text-align:left;">
AAGO CONSULTING
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9616 1463
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Planning Competency Training Marketing New Marketing
Development Public Relations & Media Relations Strategic
Planning/Management Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Jean Keow
</td>
<td style="text-align:left;">
PMC-10652
</td>
<td style="text-align:left;">
Managing Partner / Principal Consultant
</td>
<td style="text-align:left;">
NUCLEI BRAND MARKETING SOLUTIONS LLP
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9732 4388
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Excellence Marketing Negotiation Skills New Marketing
Development Organisation Development Performance Management Public
Relations & Media Relations Sales Sales Training Service Excellence
Talent Management

Areas of Consultancy Services Provided: MARKETING Establishing Brand
Equity, Identifying Brand DNA & Persona Profile, Creating Brand
Strategy, Communications Manual & Expanding Brand Reach

PUBLIC RELATIONS Communicate effectively with key opinion leaders/
influencers, trade & media stakeholders and end-consumers including
preparation & dissemination of press releases, organization of
interviews & events and covering social media.

CAPABILITY BUILDING Identify talent gaps through skills audit, propose
key action steps to bridge gaps, improve productivity and looking into
succession plan.

SALES Provide Sales Planning & Distribution guidance, create Value
Propositions to drive business, develop Distributors into Strong
Partners and explore white space opportunities.
</td>
</tr>
<tr>
<td style="text-align:left;">
Jason Ho
</td>
<td style="text-align:left;">
PMC-10498
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
RALOGEN PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8822 5542
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Excellence Business Management Skills Business
Planning Change Management Corporate Training Employee Engagement
Executive Coaching Human Resource Management (HRM) Human Resource
Management (HRM) Training International Business Management Training
Organisation Development Strategic Planning/Management Training &
Development

Areas of Consultancy Services Provided:
</td>
</tr>
<tr>
<td style="text-align:left;">
Jason Chia Sher Teck
</td>
<td style="text-align:left;">
PMC-10198
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
GLOBAL NETWORK UNLIMITED PTE LTD
</td>
<td style="text-align:left;">
6226 5338
</td>
<td style="text-align:left;">
9451 7238
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Financial Management Information Systems Marketing Productivity/Quality
Management Sales

Areas of Consultancy Services Provided: Intellectual Property Business
Diagnostic (PMC-IPM Certified) Corporate Branding Franchising and
Licensing
</td>
</tr>
<tr>
<td style="text-align:left;">
Jacqueline Yap Soon Kheng
</td>
<td style="text-align:left;">
PMC-10348
</td>
<td style="text-align:left;">
Consultancy Manager
</td>
<td style="text-align:left;">
CAPELLE CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6325 4982
</td>
<td style="text-align:left;">
9626 7005
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Competency Training Corporate Training Performance Management
Supervisory Skills Talent Management Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Jacob Su
</td>
<td style="text-align:left;">
PMC-10128
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
J TRIO CONSULTANCY PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8299 6688
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Planning CaseTrust Customer Service Training
Franchising Information Systems Management Training Marketing Sales
Sales Training Service Excellence Service Training Strategic
Planning/Management

Areas of Consultancy Services Provided: Corporate Training Overseas
Expansion Trademark Human Capability Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Jacky Tai Siew San
</td>
<td style="text-align:left;">
PMC-10191
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
UNBROKEN BRANDING PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9762 9042
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Marketing

Areas of Consultancy Services Provided: Business Model Innovation
Differentiation Strategy Naming Strategy Strategic Business Planning
</td>
</tr>
<tr>
<td style="text-align:left;">
Isaac Tan Ong Hoe
</td>
<td style="text-align:left;">
PMC-10197
</td>
<td style="text-align:left;">
Managing Partner / Principal Consultant
</td>
<td style="text-align:left;">
MINDSCAPE ENGAGEMENT SOLUTIONS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9003 7535
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
IT Consulting and Solutions Bespoke and Customized Web and Mobile
Applications Enterprise Resource Planning (ERP) Innovation and
Productivity Improvement Business Capability Improvement Strategic
Planning
</td>
</tr>
<tr>
<td style="text-align:left;">
Hon Sheryl
</td>
<td style="text-align:left;">
PMC-10846
</td>
<td style="text-align:left;">
Associate Director
</td>
<td style="text-align:left;">
TEO LIANG CHYE PAC
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9061 7556
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Productivity/Quality Management Technology Adoption Training &
Development

Areas of Consultancy Services Provided: Quality Assurance Review -
Firm’s system of quality control and audit engagement files Training and
Development Financial Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Ho Meow Choo
</td>
<td style="text-align:left;">
PMC-10244
</td>
<td style="text-align:left;">
Principal Consultant & General Manager
</td>
<td style="text-align:left;">
HUMAN CAPITAL (SINGAPORE) PTE LTD
</td>
<td style="text-align:left;">
6603 8042
</td>
<td style="text-align:left;">
9829 2715
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Compensation & Benefits Employee Engagement Human Resource Management
(HRM) Lean Management Performance Management Process Re-Engineering
Talent Management Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Ho Keat Ru, Melvin
</td>
<td style="text-align:left;">
PMC-10649
</td>
<td style="text-align:left;">
Director/Management Consultant
</td>
<td style="text-align:left;">
BIZSQUARE MANAGEMENT CONSULTANTS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9183 4413
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Budgeting & Cashflow Planning Corporate Training Cost
Optimisation Customer Service Training Financial Management Marketing
Merger/Acquisition New Marketing Development Productivity/Quality
Management Retail Operations Retail Performance Measurement

Area of Consultancy Services Provided: FINANCIAL MANAGEMENT FOR
COMPANIES Advisory on Company Cashflow , Trade Finance, Working Capital
Loans, Structuring Cashflow for investment, Merger & Acquisition, Risk
management, Credit Management, Property Loans consultancy, Audits, Cost
Budgeting for company, Fund Raising and investment deal origination.

BRANDING AND MARKETING Branding for Products, Branding on Social Media
platforms, online and offline Marketing advisory, Product Design and
Development, Branding and Marketing Training.

MANAGEMENT CONSULTING ON PRODUCTIVITY IMPROVEMENT Hardware, Software,
Re-engineering, Optimization, customer relationship improvement,
manpower cost reduction, improving of SOP efficiency, provide corporate
training on customer service and financial management
</td>
</tr>
<tr>
<td style="text-align:left;">
Henry Fong Tat Choy
</td>
<td style="text-align:left;">
PMC-10641
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
CF&C0 Consultancy Pte Ltd
</td>
<td style="text-align:left;">
6407 1402
</td>
<td style="text-align:left;">
9858 1300
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding CaseTrust Competency Training Corporate Training Data
Protection Industry Relations (IR) Training Quality Management System
(ISO) Service Excellence Service Training Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Gracia, Ng Bee Giok
</td>
<td style="text-align:left;">
PMC-10918
</td>
<td style="text-align:left;">
Principal Consultant & Trainer
</td>
<td style="text-align:left;">
WENIX TRAINING & CONSULTANCY
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9848 3368
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Environmental Management System (ISO) Information Systems Information
Technology (IT) Management Information Technology (IT) Performance
Management Quality Management System (ISO) Technology Adoption Training
& Development

Areas of Consultancy Services Provided: ISO 27001 Information Security
Management System ISO 20000-1 IT Service Management System SS 584
Multi-Tier Cloud Computing SS 564 Green Data Center ISO 9001 Quality
Management System, ISO 14001 Environment Management System, ISO 45001
Occupational Health & Safety Management System Training and Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Goh Yong Yau
</td>
<td style="text-align:left;">
PMC-10894
</td>
<td style="text-align:left;">
Country Head (SIngapore)
</td>
<td style="text-align:left;">
The Coal Group www.coal-group.com <yongyau.goh@coal-group.com>
</td>
<td style="text-align:left;">
6679 6167
</td>
<td style="text-align:left;">
9818 7432
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Excellence Corporate Training International Business
Marketing New Marketing Development Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Goh Wee Lee
</td>
<td style="text-align:left;">
PMC-10237
</td>
<td style="text-align:left;">
Senior Consultant & Managing Director
</td>
<td style="text-align:left;">
SRATEGIC VALUE CONSULTING PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9652 0451
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Compensation & Benefits Competency Training Corporate Training Executive
Coaching Financial Management Human Resource Management (HRM) Human
Resource Management (HRM) Training Management Training Performance
Management Talent Management Work-Life Strategy Work-Life Strategy &
Implementation Competency Profiling & Development HR Advisory
Competency-based Interviewing skills Managing Multi-generational &
Multi-cultural Workforce
</td>
</tr>
<tr>
<td style="text-align:left;">
Gerald Tan
</td>
<td style="text-align:left;">
PMC-10758
</td>
<td style="text-align:left;">
Managing Partner
</td>
<td style="text-align:left;">
FUTURE ASIA ADVISORY PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9641 8285
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Excellence Business Management
Skills Business Planning Change Management Crisis Management Financial
Management International Business Lean Management Marketing
Merger/Acquisition Organisation Restructuring

Areas of Consultancy Services Provided: Business Advisory (Valuations,
Financial Due-Diligence, Restructuring, M&A, Management Consulting)
Consumer and Marketing Research with Big Data Solutions Supply Chain and
Operations Optimisation Fund-Raising for Series A/B/C
</td>
</tr>
<tr>
<td style="text-align:left;">
Geoy May Li
</td>
<td style="text-align:left;">
PMC-10146
</td>
<td style="text-align:left;">
Managing Consultant
</td>
<td style="text-align:left;">
VINES CONSULTING LLP
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9667 8356
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Excellence Business Management Skills Corporate Training
Customer Centric Initiative (CCI) Customer Satisfaction Research
Customer Service Training Executive Coaching Management Training New
Marketing Development Service Excellence Service Training Situation
Management Strategic Planning/Management Customer Centric Initiatives
(CCI) Service Leadership Account Management Service Excellence Training
in Sales and Service Delivery
</td>
</tr>
<tr>
<td style="text-align:left;">
Foo Seck Peow, Ronny
</td>
<td style="text-align:left;">
PMC-10914
</td>
<td style="text-align:left;">
Founder & Principal Consultant
</td>
<td style="text-align:left;">
RF BUSINESS ADVISORY
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9618 2981
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Management Skills Business Planning Export Strategy
International Business Leadership Coaching New Marketing Development
Organisation Development Product Innovation Sales Situation Management
Technology Adoption Training & Development

Areas of Consultancy Services Provided: Business Transformation Business
Model Innovation Incorporating Design Thinking & Lean Concepts

Enterprise Capabilities Development in: Core Capabilities for growth and
transformation Innovation and Productivity to explore new areas of
growth and enhance efficiency New Market Access for overseas ventures

Growth Strategy with New Markets, Products or Services Business
Leader/Executive Coaching and Advisory
</td>
</tr>
<tr>
<td style="text-align:left;">
Florence, Fang Yoke Ling
</td>
<td style="text-align:left;">
PMC-10915
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
Flame Communications Pte Ltd
</td>
<td style="text-align:left;">
6259 3193
</td>
<td style="text-align:left;">
92769231
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Management Skills Business Planning Courseware
Development Crisis Management Digital Marketing Export Strategy
International Business Marketing Merger/Acquisition New Marketing
Development Product Innovation Project Management Public Relations &
Media Relations Sustainability Sustainability Impact Management &
Measurement
</td>
</tr>
<tr>
<td style="text-align:left;">
Eugene Chang Chee Kwong
</td>
<td style="text-align:left;">
PMC-10168
</td>
<td style="text-align:left;">
Senior Consultant
</td>
<td style="text-align:left;">
HAY GROUP PTE LTD
</td>
<td style="text-align:left;">
6323 1668
</td>
<td style="text-align:left;">
9796 1716
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Management Skills Business Planning Change Management
F&B Operations Franchising Intellectual Property Management (IPM) Public
Relations & Media Relations Strategic Planning/Management Supply Chain
Management Strategy Post-M&A Integration Managing Change Leadership &
Talent Organizational Culture & Health Diagnostics Performance
Management( Balanced Scorecard/KPI Setting)
</td>
</tr>
<tr>
<td style="text-align:left;">
Eric Tan
</td>
<td style="text-align:left;">
PMC-10710
</td>
<td style="text-align:left;">
Director \| Senior Advisor \| Consultant
</td>
<td style="text-align:left;">
MARKETTLINK (ASIA) PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
+65 9855 2739 (SG) \| +84 90 380 8282 (VN)
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Continuity Management (BCM) Business Management Skills Business
Planning Customer Service Training Export Strategy Franchising Human
Resource Management (HRM) International Business Marketing New Marketing
Development Process Re-Engineering Project Management Sales Service
Excellence Strategic Alliance Strategic Planning/Management Supply Chain
Management Training & Development Advisory & Consultation
</td>
</tr>
<tr>
<td style="text-align:left;">
Elizabeth Chan Lai Peng
</td>
<td style="text-align:left;">
PMC-10392
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
CENTER FOR COMPETENCY-BASED LEARNING & DEVELOPMENT PTE LTD
</td>
<td style="text-align:left;">
6339 9272
</td>
<td style="text-align:left;">
9853 7119
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Competency Training Corporate Training Human Resource Management (HRM)
Human Resource Management (HRM) Training Information Technology (IT)
Management Organisation Development Performance Management Retail
Performance Measurement Service Excellence Supervisory Skills Talent
Management Training & Development

Areas of Consultancy Services Provided: Developing industry competency
maps Developing industry competency standards Developing competency
framework (for organizational, departmental and functional levels)
Developing competency standards for jobs to meet organizational needs
Developing competency-based training programs, including classrooms,
on-the-job and e-learning programs (Workforce Skills Qualifications
(WSQ) and non-WSQ) Developing assessment plans (WSQ & Non-WSQ) Providing
independent assessment services including mystery shopping programs
Providing consultancy in setting up WSQ and non-WSQ training centres
Converting and aligning company’s existing training programs to WSQ
training programs Providing consultancy and training on the use of WSQ
Framework for human capital development and management Providing audit
services of WSQ and non-WSQ training centres Providing audit services of
WSQ and non-WSQ training programs Providing audit services of trainers
and assessors Leading and facilitating validation exercises for
organizations to enhance the organization’s training and assessment
system and trainers and assessors capabilities Developing classroom and
on–the-job trainers and assessors Providing coaching services to build
trainer capabilities Coaching assessors to enhance assessment
capabilities Developing competency-based curriculum developers (WSQ and
non-WSQ) Developing and conducting customised training programs to meet
organization’s needs
</td>
</tr>
<tr>
<td style="text-align:left;">
Elango Subramanian
</td>
<td style="text-align:left;">
PMC-10327
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
Morrison Management Pte Ltd
</td>
<td style="text-align:left;">
6538 9558
</td>
<td style="text-align:left;">
9672 3735
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Budgeting & Cashflow Planning Business Management Skills Business
Planning Cost Optimisation Financial Management International Business
Merger/Acquisition Performance Management Product Innovation Research &
Development (R&D) Technology Strategic Planning/Management Supply Chain
Management Financial Management Advisory Process Re-Engineering
(Accounts) Organisation Restructuring Relocation
</td>
</tr>
<tr>
<td style="text-align:left;">
Elaine Bernalyn S. Cercado
</td>
<td style="text-align:left;">
PMC-10260
</td>
<td style="text-align:left;">
Managing Director & Senior Consultant
</td>
<td style="text-align:left;">
POWERINU TRAINING AND COACHING LLP
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9667 5800
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Management Skills Change Management Executive Coaching
Leadership Management Training Organisation Restructuring Process
Re-Engineering Sales Training Service Excellence Supply Chain Management
Technology Adoption Technology License Development

Areas of Consultancy Services Provided: Management and Leadership
Development Team Building, People/Performance Management and Coaching
Strategic Business Planning, Development and Management in ASEAN/APAC
Strategic and Operational HR Organizational Development and Change
Management B2B Solution Selling and Key Account Management Personal and
Professional Empowerment (Mentoring) Training and Assessment
(ACTA-Certified)
</td>
</tr>
<tr>
<td style="text-align:left;">
Edward Walker
</td>
<td style="text-align:left;">
PMC-10938
</td>
<td style="text-align:left;">
Managing & Creative Director
</td>
<td style="text-align:left;">
AE MEDIA PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8111 0240
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Layout Marketing New Marketing Development Brand Strategy Brand
Execution Digital Marketing (website, ecommerce, SEO, SEM, Social Media)
Video production / content Corporate Sales Presentations Advertising
Content Creation Illustration Copywriting Social Media Marketing
Strategy Campaign strategy and execution Animation
</td>
</tr>
<tr>
<td style="text-align:left;">
Dr Nina Tan
</td>
<td style="text-align:left;">
PMC-10569
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
BUSINESS INTELLIGENCE & 8NALYTICS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9655 8948
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Cost Optimisation Customer Service
Training Human Resource Management (HRM) International Business
Leadership Coaching Marketing Merger/Acquisition Negotiation Skills
Organisation Restructuring Performance Management Sales Training

Areas of Consultancy Services Provided: - Provide Business Advisory
Services on relevant government grants and schemes available for
companies in aspects such as financial capabilities and legal
management, business excellence, branding, HR development, technology
innovation and overseas expansion.

- Provide guidance to enterprises in areas such as business expansion,
  franchising, financing, investment structuring, tax planning, Finance
  and HR capabilities development for overseas expansion.

- Derive Visual business intelligence through data analytics.

- Provide Qualified Corporate training to International Universities,
  Education Institutions, SMEs , MNCs, Professional Bodies in China,
  Indonesia, Malaysia, UK, US and Singapore.

  </td>
  </tr>
  <tr>
  <td style="text-align:left;">

  Derek Cheah

  </td>
  <td style="text-align:left;">

  PMC-10798

  </td>
  <td style="text-align:left;">

  Senior Consultant

  </td>
  <td style="text-align:left;">

  SSA Consulting Pte Ltd

  </td>
  <td style="text-align:left;">

  6565 1500

  </td>
  <td style="text-align:left;">

  +6598523905

  </td>
  <td style="text-align:left;">

  VERIFIED

  </td>
  <td style="text-align:left;">

  LIVE

  </td>
  <td style="text-align:left;">

  Business Continuity Management (BCM) Business Excellence Business
  Management Skills Crisis Management Data Protection Environment and
  Sustainability Environmental Management System (ISO) Good
  Manufacturing Practices Hazard Analysis Critical Control Points
  (HACCP) International Business Lean Management Marketing New Marketing
  Development Productivity/Quality Management Quality Management System
  (ISO) Risk Management Sustainability

  </td>
  </tr>
  <tr>
  <td style="text-align:left;">

  Dennis Lee Boon Seng

  </td>
  <td style="text-align:left;">

  PMC-10295

  </td>
  <td style="text-align:left;">

  Director

  </td>
  <td style="text-align:left;">

  LEFTFIELD CONCEPTS LLP

  </td>
  <td style="text-align:left;">

  6278 8933

  </td>
  <td style="text-align:left;">

  9339 9763

  </td>
  <td style="text-align:left;">

  VERIFIED

  </td>
  <td style="text-align:left;">

  LIVE

  </td>
  <td style="text-align:left;">

  Branding Financial Management New Marketing Development Strategic
  Planning/Management

Areas of Consultancy Services Provided: Strategic Business Management
Brand Building Business Innovation Product and Market Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Delane Lim Zi Xuan
</td>
<td style="text-align:left;">
PMC-10524
</td>
<td style="text-align:left;">
Managing Partner / Principal Consultant
</td>
<td style="text-align:left;">
Polygon Asia Consulting Enterprise / Global Outdoor Education Consulting
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
+6594243045
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Benchmarking Branding Budgeting & Cashflow Planning Business Continuity
Management (BCM) Business Excellence Business Management Skills Business
Planning Change Management Compensation & Benefits Competency Training
Corporate Training Courseware Development Crisis Management Customer
Centric Initiative (CCI) Executive Coaching Human Capital Management
Human Resource Management (HRM) Human Resource Management (HRM) Training
Industry Relations (IR) Training Information Systems Instructional
Design Leadership Leadership Coaching Lean Management Organisation
Development Organisation Restructuring Performance Management
Productivity/Quality Management Project Management Quality Management
System (ISO) Resource Management Risk Management Service Excellence
Service Training Situation Management SME Management Action For Results
(SMART) Initiative Strategic Planning/Management Supervisory Skills
Talent Management Team Excellence Training & Development Wage
Restructuring & Flexible Wage Systems Work-Life Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Deborah Chan Chew Lan
</td>
<td style="text-align:left;">
PMC-10453
</td>
<td style="text-align:left;">
Managing Partner
</td>
<td style="text-align:left;">
V-KNO MANAGEMENT SERVICES
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9636 7645
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Continuity Management (BCM) Competency Training Environmental
Management System (ISO) Good Manufacturing Practices Hazard Analysis
Critical Control Points (HACCP) Layout Occupational Safety and Health
Adminstration (OSHA) Productivity/Quality Management Project Management
Quality Management System (ISO) Relocation Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
David Chew Meng Fei
</td>
<td style="text-align:left;">
PMC-10210
</td>
<td style="text-align:left;">
Managing Consultant
</td>
<td style="text-align:left;">
DACH Consultancy Services
</td>
<td style="text-align:left;">
6423 2463
</td>
<td style="text-align:left;">
97392463
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Excellence Business Management Skills Business Planning Change
Management Competency Training Corporate Training Customer Service
Training Data Protection Environmental Management System (ISO) Executive
Coaching F&B Operations Leadership Leadership Coaching Management
Training Occupational Safety and Health Adminstration (OSHA) Performance
Management Process Re-Engineering Productivity Diagnosis
Productivity/Quality Management Quality Management System (ISO) Retail
Operations Service Training SME Management Action For Results (SMART)
Initiative Strategic Planning/Management Supervisory Skills Training &
Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Daryl Lim
</td>
<td style="text-align:left;">
PMC-10267
</td>
<td style="text-align:left;">
Lead Strategy Consultant
</td>
<td style="text-align:left;">
AMRICH CONSULTANCY PTE LTD
</td>
<td style="text-align:left;">
6656 5230
</td>
<td style="text-align:left;">
9298 1143
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Business Excellence Customer Service Training Franchising
Productivity Diagnosis Project Management Service Excellence Service
Training Work-Life Strategy
</td>
</tr>
<tr>
<td style="text-align:left;">
Danny Phoa
</td>
<td style="text-align:left;">
PMC 10217
</td>
<td style="text-align:left;">
Managing Partner
</td>
<td style="text-align:left;">
Ad.Wright Communications Pte Ltd
</td>
<td style="text-align:left;">
6227 7227
</td>
<td style="text-align:left;">
9672 9732
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Marketing Productivity Diagnosis Productivity/Quality
Management Strategic Planning/Management

Areas of Consultancy Services Provided: Brand Strategy & Development
Marketing Strategy & Development New Brand Creation Corporate & Product
Re-branding Brand Communication Capability Development Grant Global
Company Partnership Grant Corporate Financing Business Transformation
Certified Productivity Consultant
</td>
</tr>
<tr>
<td style="text-align:left;">
Colin David Anderson
</td>
<td style="text-align:left;">
PMC-10512
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
BRANDCOURAGE PTE LTD
</td>
<td style="text-align:left;">
6290 5763
</td>
<td style="text-align:left;">
9673 1452
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Planning Change Management Corporate Training
International Business Leadership Marketing New Marketing Development
Project Management Retail Operations Service Excellence Training &
Development

Areas of Consultancy Services Provided: Brand Strategy Brand
Communications Brand Experience
</td>
</tr>
<tr>
<td style="text-align:left;">
Cliff Goh Geok Lin
</td>
<td style="text-align:left;">
PMC-10388
</td>
<td style="text-align:left;">
Partner
</td>
<td style="text-align:left;">
ASSURANCE PARTNERS LLP
</td>
<td style="text-align:left;">
6702 3178
</td>
<td style="text-align:left;">
9649 7571
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Continuity Management (BCM)
Business Management Skills Business Planning Financial Management Human
Resource Management (HRM) Intellectual Property Management (IPM)
International Business Merger/Acquisition Productivity/Quality
Management Project Management Strategic Planning/Management

Areas of Consultancy Services Provided: Project/Business Strategic
Planning Business Advisory Services Planning & Budgeting Cash-flow &
Working Capital Management Business Continuity Management (BCM) Branding
Strategy & Implementation Financial Management Bank Financing, Equity
Funding, Government Incentives & Assistance Schemes Mergers, Acquisition
and Corporate Restructuring Audit Assurance & Internal Controls Advisory
Human Resource Management (HRM) Strategic Human Resource Planning
</td>
</tr>
<tr>
<td style="text-align:left;">
Clarence Nah Choon Loi
</td>
<td style="text-align:left;">
SPMC-10017
</td>
<td style="text-align:left;">
General Manager/Resident Consultant
</td>
<td style="text-align:left;">
ASIAWIDE FRANCHISE CONSULTANTS PTE LTD / ASIAWIDE BUSINESS CONSULTANTS
PTE LTD
</td>
<td style="text-align:left;">
6743 2282
</td>
<td style="text-align:left;">
9622 7369
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Budgeting & Cashflow Planning Business Excellence CaseTrust Customer
Centric Initiative (CCI) EduTrust Financial Management Franchising
Intellectual Property Management (IPM) Service Excellence SME Management
Action For Results (SMART) Initiative Strategic Planning/Management
Technology License Development Franchise Audit Process Improvement
</td>
</tr>
<tr>
<td style="text-align:left;">
Chua Choon Chye (Vincent)
</td>
<td style="text-align:left;">
PMC-10368
</td>
<td style="text-align:left;">
Principal Consultant / Director
</td>
<td style="text-align:left;">
FORESIGHT CONSULTING & TRAINING PTE LTD
</td>
<td style="text-align:left;">
6881 9608
</td>
<td style="text-align:left;">
9818 3867
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Lean Management Productivity Diagnosis Project Management Business
Diagnosis Factory Relocation Menu Re-Engineering Process Re-Engineering
Good Housekeeping Training Good Manufacturing Practices Training Digital
Project Management Services Training ISO 9001 Quality Management Systems
ISO 14001 Environmental Management Systems ISO 45001 Occupational Health
and Safety Management Systems ISO 22301 Business Continuity Management
Systems ISO 22000 Food Safety Management Systems SS 444 Hazard Analysis
and Critical Control Point (HACCP) System SS 590 HACCP-Based Food Safety
Management Systems SS 620 Good Distribution Practice for Medical Devices
ISO 13485 Medical Devices Quality Management Systems ISO 37001 Anti
Bribery Management Systems Supplier Quality Audit Bizsafe 3 Risk
Management Services Commercial & Industrial Property Consultancy
Services
</td>
</tr>
<tr>
<td style="text-align:left;">
Christina Koh Hwee Ping
</td>
<td style="text-align:left;">
PMC-10749
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
CLOUD ACCOUNTING & CONSULTANCY PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
6721 9545
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Budgeting & Cashflow Planning Business Continuity Management (BCM)
Business Management Skills Business Planning Change Management Cost
Optimisation Crisis Management Export Strategy Financial Management
Information Systems International Business Merger/Acquisition
Negotiation Skills Process Re-Engineering Product Mix Productivity
Diagnosis Project Management Situation Management Strategic Alliance
Strategic Planning/Management Technology Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
Chong Zhe Wei
</td>
<td style="text-align:left;">
PMC-10784
</td>
<td style="text-align:left;">
COO/Co-Founder
</td>
<td style="text-align:left;">
The Afternaut Group
</td>
<td style="text-align:left;">
6694 2110
</td>
<td style="text-align:left;">
9435 7653
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Continuity Management (BCM) Business Excellence
Business Management Skills Business Planning Change Management Cost
Optimisation Employee Engagement Human Resource Management (HRM)
Training International Business Marketing Measurement System

Areas of Consultancy Services Provided: Business Strategy & Innovation
Business Model Transformation Business Excellence Design Thinking /
Human-Centered Design Branding Marketing Human Capital Management
Business Process Review for Productivity
</td>
</tr>
<tr>
<td style="text-align:left;">
Chinnasamy Kirubakaran
</td>
<td style="text-align:left;">
PMC-10422
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
R STAR CONSULTATIONS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9671 2941
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Competency Training Corporate Training Environmental Management System
(ISO) Management Training Project Management Quality Management System
(ISO) Supervisory Skills Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Chiang Kiam Peng Spencer
</td>
<td style="text-align:left;">
PMC-10849
</td>
<td style="text-align:left;">
Chief Operation Officer/Principal Consultant
</td>
<td style="text-align:left;">
BIZPOINT INTERNATIONAL PTE LTD
</td>
<td style="text-align:left;">
6834 3001
</td>
<td style="text-align:left;">
8500 9045
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Excellence Business Planning Customer Centric Initiative (CCI)
F&B Operations Information Systems Organisation Restructuring
Performance Management Process Re-Engineering Productivity Diagnosis
Productivity/Quality Management Service Excellence Technology Adoption

Areas of Consultancy Services Provided: Business Strategy / Strategic
Planning/Management Business Excellence and Change Management Customer &
Market Segment Analysis Critical Thinking Processes Disruptive Strategy
and Solution Enterprise Resource Planning Government Grant and Support
Information Technology (IT) Management Organisation Development/
Restructuring Performance Management Process Re-Engineering Productivity
Improvement: Process Redesign and Improvement Project Management Retail
Performance Measurement SPRING CCI Consulting SPRING, IE, WDA, IRAS &
IDA Incentives and Assistance Scheme Strategic Planning & KPI
Development Supply Chain Management Technology Adoption Training &
Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Cheng Tim Jin
</td>
<td style="text-align:left;">
PMC-10891
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
WILBERFORCE TJC LAW CORPORATION
</td>
<td style="text-align:left;">
6223 3286
</td>
<td style="text-align:left;">
8138 0709
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Budgeting & Cashflow Planning Business Continuity Management (BCM)
Business Management Skills Business Planning Change Management Cost
Optimisation Crisis Management Employee Engagement Financial Management
Franchising Merger/Acquisition Negotiation Skills Organisation
Development Organisation Restructuring Project Management Strategic
Alliance Strategic Planning/Management

Areas of Consultancy Services Provided • Legal • Corporate Finance •
Overseas Expansion
</td>
</tr>
<tr>
<td style="text-align:left;">
Chee Wing Onn, Eddy
</td>
<td style="text-align:left;">
PMC-10823
</td>
<td style="text-align:left;">
Director/Managing Consultant
</td>
<td style="text-align:left;">
EXCELLENC CONSULTING & Q-TRAINING SERVICES PTE LTD
</td>
<td style="text-align:left;">
9384 5849
</td>
<td style="text-align:left;">
9069 4523
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Continuity Management (BCM) Environmental Management System
(ISO) Good Manufacturing Practices Lean Management Occupational Safety
and Health Administration (OSHA) Process Re-Engineering
Productivity/Quality Management Quality Management System (ISO)

Areas of Consultancy Services Provided: ISO 9001 Quality Management
System ISO 14001 Environmental Management System ISO 45001 Occupational
Health & Safety Management System OHSAS 18001/SS506 Safety Management
System & Risk Assessment Integrated Management System (QMS, EMS & OHSMS)
ISO 13485 Medical Device Quality Management System SS 620: Good
Distribution Practice of Medical Device IATF 16949 Quality Management
System for automotive production ISO/IEC 17025 General Requirement for
the Competence of Testing and Calibration Laboratory AS 9100/AS 9120
Aerospace Quality Management System Core Tools Training (Quality
Improvement Tools)
</td>
</tr>
<tr>
<td style="text-align:left;">
Charles Robin Scott
</td>
<td style="text-align:left;">
PMC-10906
</td>
<td style="text-align:left;">
Founder
</td>
<td style="text-align:left;">
TANGIBLE PTE LTD
</td>
<td style="text-align:left;">
6338 6320
</td>
<td style="text-align:left;">
8183 8632
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Change Management Customer Centric Initiative (CCI) Digital
Marketing Marketing Measurement System Organisation Development Product
Innovation Public Relations & Media Relations Service Excellence
</td>
</tr>
<tr>
<td style="text-align:left;">
Dato’ Dr. Charles Chen Tian Hoong
</td>
<td style="text-align:left;">
PMC-10089
</td>
<td style="text-align:left;">
Founder/Project Director
</td>
<td style="text-align:left;">
FOODNET INTERNATIONAL CONSULTING & PROJECTS PTE LTD
</td>
<td style="text-align:left;">
6841 6068
</td>
<td style="text-align:left;">
9691 2396
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Excellence Competency Training Good Manufacturing Practices
Hazard Analysis Critical Control Points (HACCP) Productivity/Quality
Management Project Management Quality Management System (ISO) HACCP
(Hazard Analysis & Critical Control Points) Food Safety System SS 590
(Hazard Analysis & Critical Control Points) Food Safety System ISO 9000
Quality Management System ISO 22000 Food Safety Management System FSSC
22000 Food Safety Management System British Retail Consortium (BRC) Food
Safety Management System Food Safety System Yearly Maintenance Program
Quality & Food Safety Audit Good Hygiene Practices (GHP) Good
Manufacturing Practices (GMP) - Food Industry Good Manufacturing
Practices (GMP) - Pharmaceutical Food Hygiene for Food Handlers Central
Kitchen/Plant Design & Layout Supplier Assessment MUIS Halal Consultancy
</td>
</tr>
<tr>
<td style="text-align:left;">
Chang Tuck Wah, Jonathan
</td>
<td style="text-align:left;">
PMC-10661
</td>
<td style="text-align:left;">
HR Business Consultant
</td>
<td style="text-align:left;">
EON CONSULTING & TRAINING PTE LTD
</td>
<td style="text-align:left;">
6220 4008
</td>
<td style="text-align:left;">
6222 4369
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Excellence Compensation & Benefits Competency Training Employee
Engagement Human Resource Management (HRM) Management Training
Performance Management Strategic Planning/Management Talent Management
Training & Development

Areas of Consultancy Services Provided: Key Performance indicators
Recruitment & selection system Performance management system Learning &
development system Competency dictionary & matrix Compensation &
benefits system Career management system Talent management Succession
planning Employee engagement Human resource management
</td>
</tr>
<tr>
<td style="text-align:left;">
Chang Keen Weng
</td>
<td style="text-align:left;">
PMC-10101
</td>
<td style="text-align:left;">
Managing Consultant
</td>
<td style="text-align:left;">
ALPHA CONSULTING & TRAINING PTE LTD
</td>
<td style="text-align:left;">
6396 4386
</td>
<td style="text-align:left;">
9671 0132
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Environmental Management System (ISO) Productivity/Quality Management

Areas of Consultancy Services Provided: - Consulting, Training,
Auditing, Coaching - Total Quality Management (TQM), Just-In-Time (JIT),
Total Productive Maintenance (TPM), 6 Sigma, Lean Manufacturing, Kaizen
& Advanced Kaizen - Leadership, Strategic Planning, Policy Deployment,
Balanced Scorecard - ISO 9001, ISO 14001, ISO/TS 16949, ISO 18001 -
Singapore Quality Award (SQA) & (SQC), SME Management Action for Results
( SMART ) - Systematic / Creative Problem Solving Techniques, 8D Method,
CEDAC Method, 5-why, Basic/New 7 QC Tools - Statistical Process Control
(SPC), Measurement System Analysis (MSA), Failure Mode Effect Analysis
(FMEA), Mistake Proofing (Poka-Yoke), Quick Changeover (SMED) - 5S
System, Visual Control, Kanban System - Team Building, QIT, WIT, QCC &
IQC
</td>
</tr>
<tr>
<td style="text-align:left;">
Chan Yi On
</td>
<td style="text-align:left;">
PMC-10695
</td>
<td style="text-align:left;">
Founder & Director
</td>
<td style="text-align:left;">
HELLO DONE PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9170 5432
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Budgeting & Cashflow Planning Business Planning Cost Optimisation
Financial Management Performance Management Productivity/Quality
Management Strategic Planning/Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Chan Meng Hong
</td>
<td style="text-align:left;">
PMC-10167
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
QIS TECHNOLOGY PTE LTD
</td>
<td style="text-align:left;">
6498 0198
</td>
<td style="text-align:left;">
9815 8124
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Emphasize and promote the need to re-starting and re-understanding ISO
Management Systems. to implement and realise ISO management systems.
Establish an effective, process-based, simple management system that
engages and with common understanding from all team members.

Assisted organisations with a two-member team or over thousand
employees. Regardless of staff strength, we focus on building people
before system. Establish a management system that entire team
participates and synchronizes.

Develop a flow-charting approach in documenting process procedures. This
closes the gap between employees and organisation procedures.
Flow-charting model greatly increase the ease of understanding,
maintenance and revision. It also improves training effectiveness.

We are also specialised in consolidating two or more international
management system as one unified integrated management system including
ISO 9001:2015, 14001:2015, 45001:2018, 13485:2014. This framework also
eases the integration of business continuity management, business
excellent, customer service, supply chain management.

强调并促进从新, 从心理解 ISO 管理体系的必要性。实施和实现ISO管理体系。
建立一个有效的、基于流程的、简单的管理系统，让所有团队成员参与并达成共识。

协助过拥有两人的公司或上千人的企业。 无论员工的数量，我们都以先强化团队,
后建立系统的原则为主导。全体员工参与并制定的管理系统。

工作程序以流程图模式来编制。 这拉近了全体员工和企业工作程序的距离。
流程图模型大大增加了理解、维护和修改的便利性。它还提高了培训效果。

擅长把两个或以上不同的管理系统, 如ISO 9001:2015, 14001:2015, 45001:2018,
13485:2014, 整合成一个综合管理体系.
这框架也适用于整合业务连续性管理系统, 卓越业务系统,
客户服务系统，供应链管理系统等等.
</td>
</tr>
<tr>
<td style="text-align:left;">
Chan Lup Fai
</td>
<td style="text-align:left;">
PMC-10442
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
ASSOCIATES CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6297 2700
</td>
<td style="text-align:left;">
9684 9769
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Environmental Management System (ISO) Occupational Safety and Health
Adminstration (OSHA) Quality Management System (ISO) Training &
Development Business Strategy Development HACCP /Food Safety Management
System (ISO)
</td>
</tr>
<tr>
<td style="text-align:left;">
Angela Tan Chay Yee
</td>
<td style="text-align:left;">
PMC-10331
</td>
<td style="text-align:left;">
Partner/Senior Consultant/Trainer
</td>
<td style="text-align:left;">
A&A CONSULTING AND SERVICES LLP
</td>
<td style="text-align:left;">
6841 3925
</td>
<td style="text-align:left;">
9673 6416
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Good Manufacturing Practices Hazard Analysis Critical Control Points
(HACCP) Quality Management System (ISO) Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Candice Chong Ooi Lin
</td>
<td style="text-align:left;">
PMC-10813
</td>
<td style="text-align:left;">
Consultant
</td>
<td style="text-align:left;">
MCCOY BESPOKE
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9046 5722
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Excellence Business Planning International Business Marketing
Negotiation Skills New Marketing Development Public Relations & Media
Relations Sales Strategic Planning/Management Training & Development

Areas of Consultancy Services Provided: Meetings, Incentives,
Conventions, Exhibitions (M.I.C.E) Business Mission Trips Business
Transformation Digital Marketing Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Brian Ling
</td>
<td style="text-align:left;">
PMC-10306
</td>
<td style="text-align:left;">
Design Director
</td>
<td style="text-align:left;">
DESIGN SOJOURN PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9760 0978
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Budgeting & Cashflow Planning Business Excellence Business Management
Skills Business Planning Corporate Training Customer Centric Initiative
(CCI) Customer Satisfaction Research Customer Service Training Executive
Coaching Management Training Research & Development (R&D) Technology
Technology Adoption Business Strategy and Innovation Design Thinking
Ethnographic Research Customer Insights Experience Design Industrial
Design UI/UX Design
</td>
</tr>
<tr>
<td style="text-align:left;">
Benny Koh
</td>
<td style="text-align:left;">
PMC-10677
</td>
<td style="text-align:left;">
Management Consultant
</td>
<td style="text-align:left;">
SEELBOND PTE LTD
</td>
<td style="text-align:left;">
6570 3359
</td>
<td style="text-align:left;">
9328 3216
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Excellence Business Planning Change Management
Customer Centric Initiative (CCI) Human Resource Management (HRM)
Information Systems Leadership Coaching Marketing Project Management
Strategic Planning/Management Training & Development

Areas of Consultancy Services Provided: Branding Business Excellence
Business Planning Business Strategy Innovation Customer Insights
Information Systems Marketing Strategic Technology Road Mapping Training
and Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Bana Inayat, Zack
</td>
<td style="text-align:left;">
PMC-10091
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
BEACON CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6873 9768
</td>
<td style="text-align:left;">
9786 0941
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Benchmarking Branding Business Continuity Management (BCM) Business
Excellence Business Management Skills Business Planning Change
Management Compensation & Benefits Competency Training Corporate
Training Customer Centric Initiative (CCI) Customer Satisfaction
Research

Areas of Consultancy Services Provided: Business Excellence Change
Management Corporate Training Customer Centric Initiative (CCI) Customer
Satisfaction Research Customer Service Training Employee Engagement
Executive Coaching Leadership Leadership Coaching Management Training
Negotiation Skills Performance Management Productivity Diagnosis
Productivity/Quality Management Project Management Retail Operations
Retail Performance Measurement Sales Sales Training Service Excellence
Service Training Strategic Planning/Management Supervisory Skills Team
Excellence Training & Development
</td>
</tr>
<tr>
<td style="text-align:left;">
Aw Kai Khim
</td>
<td style="text-align:left;">
PMC-10602
</td>
<td style="text-align:left;">
Consultant
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
6841 6068
</td>
<td style="text-align:left;">
9112 3761
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Excellence Competency Training Good Manufacturing Practices
Hazard Analysis Critical Control Points (HACCP) Productivity/Quality
Management Project Management Quality Management System (ISO)

Areas of Consultancy Services Provided: HACCP (Hazard Analysis &
Critical Control Points) Food Safety System SS 590 (Hazard Analysis &
Critical Control Points) Food Safety System ISO 9000 Quality Management
System ISO 22000 Food Safety Management System FSSC 22000 Food Safety
Management System British Retail Consortium (BRC) Food Safety Management
System Food Safety System Yearly Maintenance Program Quality & Food
Safety Audit Good Hygiene Practices (GHP) Good Manufacturing Practices
(GMP) - Food Industry Good Manufacturing Practices (GMP) -
Pharmaceutical Food Hygiene for Food Handlers Central Kitchen/Plant
Design & Layout Supplier Assessment
</td>
</tr>
<tr>
<td style="text-align:left;">
Arvin Tang
</td>
<td style="text-align:left;">
PMC-10763
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
TECHLYON PTE LTD (AKIN)
</td>
<td style="text-align:left;">
6494 9291
</td>
<td style="text-align:left;">
8201 2231
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Data Analysis Design Thinking Branding New Market Development Product
Innovation Digital Transformation Marketing Technology & Automation
Agile and Lean Methodologies Inbound and Integrated Marketing Merger &
Acquisition
</td>
</tr>
<tr>
<td style="text-align:left;">
Arumugam Balamurugan
</td>
<td style="text-align:left;">
PMC-10409
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
GREENSAFE INTERNATIONAL PTE LTD
</td>
<td style="text-align:left;">
6297 0388
</td>
<td style="text-align:left;">
9386 2655
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Business Continuity Management (BCM) Business Management Skills
Competency Training Corporate Training Customer Service Training
Employee Engagement Environmental Management System (ISO) F&B Operations
Hazard Analysis Critical Control Points (HACCP) Occupational Safety and
Health Adminstration (OSHA) Productivity/Quality Management Quality
Management System (ISO)

Areas of Consultancy Services Provided: ISO 9001:2008 (QMS) / ISO
14001:2004 (EMS) / ISO 50001:2011 (EnMS) BS OHSAS 18001:2007 / SS 506 :
PART 1 & PART 3 ISO 26001 : 2010 (CSR) / ISO 14064 : 2006 SS 540 / BS
25999 (BCM) ISO 22000 : 2005 (FSMS) / ISO 27001 : 2005 (ISMS)
Environmental Sustainability Consulting / Energy Audit & Conservation
CONSASS Audit & Performance Improvement advisory EHS Legal Compliance
Audit / bizSAFE Program QEHS Audit support for specific customer audit
WSH Act Extension, Overtime Exemption, Factory Registration Risk
Management Consulting and Auditing MOM and NEA Approved Safety Audits
MOM Registered WSH Officer MOM Approved Risk Consultant & Auditor SCDF
Registered Fire Safety Manager NEA Registered Environmental Control
Officer WDA & MOM Approved Trainer ( ACTA) Port facility security
officer ( PFSO) & Training services Construction & Project Management
Services
</td>
</tr>
<tr>
<td style="text-align:left;">
Ariel Lin Hsuan
</td>
<td style="text-align:left;">
PMC-10901
</td>
<td style="text-align:left;">
Managing Director
</td>
<td style="text-align:left;">
FLEX-SOLVER PTE LTD
</td>
<td style="text-align:left;">
6904 8091
</td>
<td style="text-align:left;">
9106 1250
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Planning Change Management F&B Operations Information
Systems Information Technology (IT) Management Marketing Process
Re-Engineering Productivity Diagnosis Project Management Retail
Operations Supply Chain Management

Areas of Consultancy Services Provided: Retail / F&B Operation IT
Project Management IT Performance Management Digital Transformation of
Business Process Change Management
</td>
</tr>
<tr>
<td style="text-align:left;">
Angela Loh
</td>
<td style="text-align:left;">
PMC-10908
</td>
<td style="text-align:left;">
CEO
</td>
<td style="text-align:left;">
LIZARD STORM PTE LTD
</td>
<td style="text-align:left;">
6738 0891
</td>
<td style="text-align:left;">
9748 9047
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Branding Marketing Communications
</td>
</tr>
<tr>
<td style="text-align:left;">
Ang Seng Hau, Anson
</td>
<td style="text-align:left;">
PMC-10394
</td>
<td style="text-align:left;">
Director
</td>
<td style="text-align:left;">
OSTENDO PTE LTD
</td>
<td style="text-align:left;">
6347 7778
</td>
<td style="text-align:left;">
9457 3538
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Automation, Mechanisation Branding Business Excellence Cost Optimisation
F&B Operations Information Systems Logistics Optimisation Process
Re-Engineering Productivity/Quality Management Sales Service Excellence
Technology Adoption
</td>
</tr>
<tr>
<td style="text-align:left;">
Ambrish Chaudhry
</td>
<td style="text-align:left;">
PMC-10885
</td>
<td style="text-align:left;">
Head of Strategy, Asia
</td>
<td style="text-align:left;">
SUPERUNION BRAND CONSULTING PTE LTD
</td>
<td style="text-align:left;">
6351 1338
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Business Excellence Employee Engagement Marketing New Marketing
Development Strategic Planning/Management

Areas of Consultancy Services Provided: Brand strategy Brand identity
creation, revitalisation and management Brand architecture and portfolio
optimisation Communication design, strategy and planning
</td>
</tr>
<tr>
<td style="text-align:left;">
Abdul Samad Abdul Kader
</td>
<td style="text-align:left;">
PMC-10638
</td>
<td style="text-align:left;">
Principal Consultant
</td>
<td style="text-align:left;">
A STAR SAFETY CONSULTANTS PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
8126 8203
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">
Business Continuity Management (BCM) CaseTrust Crisis Management
EduTrust Environmental Management System (ISO) Good Manufacturing
Practices Hazard Analysis Critical Control Points (HACCP) Occupational
Safety and Health Administration (OSHA) Productivity/Quality Management
Quality Management System (ISO) SME Management Action For Results
(SMART) Initiative Training & Development Aerospace, API, Security,
HACCP, Energy, CSR, GDPMDS, ISO, bizSAFE, CaseTrust, EduTrust, Risk
management, GMP, product certification, HALAL, GGBS, Audits Training
Needs Analysis, Courseware Design and Development, Training
</td>
</tr>
<tr>
<td style="text-align:left;">
Kelvin Emmanuel Ng
</td>
<td style="text-align:left;">
PMC-10187
</td>
<td style="text-align:left;">
Brand Strategy & Creative Director
</td>
<td style="text-align:left;">
POPPER ASIA PTE LTD
</td>
<td style="text-align:left;">
NA
</td>
<td style="text-align:left;">
9686 5714
</td>
<td style="text-align:left;">
VERIFIED
</td>
<td style="text-align:left;">
LIVE
</td>
<td style="text-align:left;">

Branding Corporate Training Marketing Measurement System New Marketing
Development Public Relations & Media Relations

Areas of Consultancy Services Provided: BRANDING Brand Architecture
Brand Expression & Communications Brand Strategy Consumer Branding
Corporate Branding

DESIGN Advertising Corporate Identity Development Creative Direction
Packaging Design Visual Communication Design

MARKETING Business Development – China Online to Offline Marketing
Marketing & Retail Activation Marketing Campaigns Marketing Events
Marketing Strategy
</td>
</tr>
</tbody>
</table>

</div>

## Export the data a CSV

``` r
write.csv(profil_df, "./data/PMC_data.csv", col.names = F)
```

    ## Warning in write.csv(profil_df, "./data/PMC_data.csv", col.names = F): attempt
    ## to set 'col.names' ignored
