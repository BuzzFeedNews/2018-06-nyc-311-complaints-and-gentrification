# NYC 311 complaints and demographic analysis — 2010 to 2018
This repository contains data, analytic code, and findings that support portions of the BuzzFeed News article, “They Played Dominoes Outside Their Apartment For Decades. Then The White People Moved In And Police Started Showing Up.,” published June 29, 2018. Please read that article, which contains important context and details, before proceeding.    
## Data

The data used in this analysis come from two sources: New York City’s 311 database, and the U.S. Census Bureau.    
### 311 complaints
311 complaints were downloaded in bulk [from New York City's open data portal](https://nycopendata.socrata.com/Social-Services/311-Service-Requests-from-2010-to-Present/erm2-nwe9) and are not included in this repository due to their size. (To reproduce the findings in this repository, you will need to download the complaints from the open data portal, and save them as “311_Service_Requests_from_2010_to_Present.csv” in this repository’s data/nyc/ folder.)     

The dataset includes all 311 complaints filed in New York City from 2010 to 2018, and includes the following headers relevant to the analysis:    
- `Created Date` — The date the complaint was received.
- `Incident Address` — The address associated with the 311 complaint
- `Complaint Type` — The main category of complaint. E.g., Noise - Residential or Noise - Street/Sidewalk.
- `Descriptor` — The subtype of complaint the complaint. E.g., `Loud Music/Party` or `Loud Music/Party`.
- `Resolution Description`— A categorical variable describing how the complaint was resolved . E.g., `The Police Department responded to the complaint and took action to fix the condition.`.
- `Latitude` — Latitude of location associated with complaint.
- `Longitude` — Longitude of location associated with complaint.
### Census data
The analysis uses two Census datasets, described below.     
#### 2016 American Community Survey
For access to the most recent demographic data from the Census, the analysis uses the American Community Survey’s 5-year estimates for the 2012-2016.  The [01-census-api-scraper.ipynb](notebooks/01-census-api-scraper.ipynb) notebook in this repository contains the code used to downloaded this data from the [Census’s API](https://www.census.gov/developers/).     
The data were downloaded for every tract in the [New York-Newark-Jersey City, NY-NJ-PA Metropolitan Statistical Area](https://en.wikipedia.org/wiki/New_York_metropolitan_area#Metropolitan_Statistical_Area) (MSA). The county list for the  MSA was sourced from [here.](https://www.bea.gov/regional/docs/msalist.cfm?mlist=45)  
For each tract in the MSA, the variables obtained include the following:     
* `geoid` — Census tract ID
* `total_population` — The tract’s total population
* `median_income` — Median income (1,000,001 is the upper limit for this column)
* `median_home_value` — Median home value (1,000,001 is the upper limit for this column)
* `educational_attainment` — The number people who are 25 or older and have the equivalent of a 4-year college degree     
* `white_alone` — The number of people whose race is white alone, and are not Hispanic
* `black_alone` — The number people who are black or African American alone, and are not Hispanic
* `native` — The number of people who are American Indian and Alaska Native alone
* `asian` — The number of people who are Asian alone
* `native_hawaiian_pacific_islander` — The number of people who are Native Hawaiian and Other Pacific Islander alone
* `some_other_race_alone` — The number of people who are some other race alone
* `two_or_more` — The number of people who are two or more races
* `hispanic_or_latino` — The number of people who are Hispanic or Latino    
#### 2000 Decennial Census
2000 decennial Census data standardized to match 2010 census tracts was downloaded from the [`US2010 Longitudinal Tract Data Base`](https://s4.ad.brown.edu/Projects/Diversity/Researcher/Bridging.htm) (LTDB). The file [`edited_LTDB_Std_2000_fullcount_sample.csv`](data/edited_LTDB_Std_2000_fullcount_sample.csv) was harmonized by researchers to allow for analyses of Census data from various decades, while still being able to refer to 2010 Census tract geographies. The file includes a subset of the LTDB’s columns and was re-published with permission from the researchers who produced the [`US2010 Longitudinal Tract Data Base`](https://s4.ad.brown.edu/Projects/Diversity/Researcher/Bridging.htm). The longitudinal data set can be downloaded in its entirety [here.](https://s4.ad.brown.edu/Projects/Diversity/Researcher/LTBDDload/Default.aspx)  
Columns in [`edited_LTDB_Std_2000_fullcount_sample.csv`](data/edited_LTDB_Std_2000_fullcount_sample.csv) include the same variables obtained from the American Community Survey, but with slightly different variable names. (The dictionary for the data can be found [here](https://s4.ad.brown.edu/Projects/Diversity/Researcher/LTBDDload/Dfiles/codebooks.pdf).)    
#### Census Tract Shapefiles
A shapefile detailing the geographic boundaries of all New York state Census tracts was also obtained from the Census Bureau’s site, [here.](https://www.census.gov/geo/maps-data/data/cbf/cbf_tracts.html)  
## Gentrification Methodology
The gentrification measure was adopted from a [methodology devised by Governing Magazine](http://www.governing.com/gov-data/gentrification-report-methodology.html) (which in turn is similar to the definition from a [Columbia University study ](http://uar.sagepub.com/content/40/4/463.abstract)). It includes analysis of median income, median home value and educational attainment data.    
The methodology is comprised of the following two tests, as described by Governing Magazine:    
##### Test 1: Does the tract qualify for gentrification?
- The tract had a population of at least 500 residents at the beginning and end of a decade and was located within a central city
- The tract’s median household income was in the bottom 40th percentile when compared to all tracts within its metro area at the beginning of the decade.
- The tract’s median home value was in the bottom 40th percentile when compared to all tracts within its metro area at the beginning of the decade.
##### Test 2: Has it gentrified?
- An increase in a tract’s educational attainment, as measured by the percentage of residents age 25 and over holding bachelor’s degrees, was in the top third percentile of all tracts within a metro area.
- A tract’s median home value increased when adjusted for inflation.
- The percentage increase in a tract’s inflation-adjusted median home value was in the top third percentile of all tracts within a metro area.
## Data analysis
The data analysis was performed in the following two Jupyter notebooks, using the Python programming language.    
### 02-gentrification_measure_and_race_analysis.ipynb
The notebook [`02-gentrification_measure_and_race_analysis.ipynb`](notebooks/02-gentrification_measure_and_race_analysis.ipynb) combines and analyzes the 2016 American Community Survey data and the 2000 decennial Census data to determine the following:

* Whether a tract gentrified or not according to the methodology above
* Percentage-point changes for different, non-overlapping racial groups    

The notebook produces the following files:
* [`output/census_data_nyc_metro.csv`](data/census_data_nyc_metro.csv) — a merged spreadsheet of NYC metro area Census data from 2016 and 2000.
* [`output/gentrification.csv`](data/gentrification.csv) — a spreadsheet of all New York City Census tracts that includes the following columns that are relevant to the analysis and graphics (among others):
  * `GEOID` — Census tract ID
  * `total_population` — The tract’s total population in 2016
  * `gentrified` — Whether the tract gentrified between 2000 and 2016
  * `low_population` — Whether the tract's population was too low to qualify for gentrification
  * `eligible_for_gentrification` — Whether a tract was eligible for gentrification based on Test 1 above
  * `pct_white_alone_change` — Percentage-point change for population that was white alone
  * `pct_black_alone_change` — Percentage-point change for population that was black alone
  * `pct_native_alone_change` — Percentage-point change for population that was Native American
  * `pct_asian_alone_change` — Percentage-point change for population that was Asian alone
  * `pct_hispanic_or_latino_alone_change` — Percentage-point change for population that was Hispanic or Latino alone
  * `pct_native_hawaiian_pacific_islander_change` — Percentage-point change for population that was Native Hawaiian or Pacific Islander    

### 03-311-call-nyc-analysis.ipynb
The other notebook, [`03-311-call-nyc-analysis.ipynb`](notebooks/03-311-call-nyc-analysis.ipynb), first analyzes 311 data the following way:
* Loads the 311 data and creates a subset of complaints submitted in 2016 (so we can calculate per capita rates for that year)
* Creates a subset of complaints that belong to particular categories that show neighbors complaining about neighbors (as identified through sociologist [Joscha Legewie’s research](http://jlegewie.com/files/Legewie-Schaeffer-2016-ContestedBoundaries.pdf) and editorial decisions made by BuzzFeed News)
* Assigns each complaint to a Census tract based on that latitude and longitude of the 311 complaint
* Counts the number of 311 complaints per tract    
Then the notebook adds the demographic and gentrification calculations from from the previous notebook and does the following:
* Joins and the 311 data with the gentrification data and race/ethnicity data * Calculates rates of complaints per capita
* Calculates rate for unique complaints per capita. (To account for people who may file duplicative complaints, BuzzFeed News calculations count multiple complaints issued in the same category, for the same address, on the same day were counted as one, i.e.  “unique complaints”.)  
* Calculates medians and means for gentrified tracts vs. other types of tracts
* Explores complaints for a few particular two tracts and blocks with high complaint rates

The notebook outputs the following files:   

Tract- or block-specific data:
* [`all_complaints_block.csv`](output/all_complaints_block.csv) — Raw data of complaints for one particular block and was used for on the ground reporting.
* [`dated_responses_to_complaints.csv`](output/dated_responses_to_complaints.csv) — Complaints to which the police ‘responded’ and in response to which police issued summonses between 2015 and 2017    
The following outputs were produced for data visualizations:
* [`all_complaints_block_for_viz.csv`](output/all_complaints_block_for_viz.csv) — Used to chart all complaints related to one block over time. * [merged_complaints_final.json](output/merged_complaints_final.json) — .json file output with data for geolocation, gentrification, 311-call and race and ethnicity percentage point changes. This file underlies the maps embedded in the article.
* [`block_level_dataviz.csv`](output/block_level_dataviz.csv) — Produced to visualize all complaints per address between 2015 and 2017 on a specific block in Harlem.

Other files:
* [`call-descriptor-counts.csv`](output/call-descriptor-counts.csv) and [`subtypes_complaints.csv`](output/subtypes_complaints.csv) — These file give an overview of the prevalence of the types of complaints. They allowed BuzzFeed News editorial staff to review which categories should be included in the analysis.    
## Licensing
All code in this repository is available under the [MIT License](https://opensource.org/licenses/MIT). All data files in the output/ directory are available under the [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0) license. All data files in the data/ directory are available, under their own terms, from the sources described above.    
## Feedback / Questions?
Contact Lam Thuy Vo at lam.vo@buzzfeed.com and [Lo Bénichou](https://twitter.com/lobenichou?lang=en) from [Mapbox](https://www.mapbox.com/?utm_source=buzzfeedcollab&utm_medium=buzzfeed&utm_content=main-page&utm_campaign=narrativescollab) at lo.benichou@mapbox.com.    
Looking for more from BuzzFeed News? [Click here for a list of our open-sourced projects, data, and code.](https://github.com/buzzfeednews/)  
