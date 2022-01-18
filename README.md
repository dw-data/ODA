# Development aid to African countries

Idea: [Jean-Michel Bos](https://twitter.com/jmbos)

Data analysis and visualization: [Gianna-Carina Grün](https://twitter.com/giannagruen)

Research and writing: [Jean-Michel Bos](https://twitter.com/jmbos)

You can read the story in [French](https://dw.com/a-)


# Data Sources

We started our research and analysis based on the OECD data on official development aid (ODA) spendings. OECD publishes multiple datasets for this purpose (see below for details on which ones we used).

Despite being published by the OECD, the database not only contains entries submitted by OECD members, but goes beyond this group of countries.
It includes submissions by DAC (Development Assistance Committee) members, as well as non-DAC members and multilateral organisations. An overview of which donors are included can be found [here](https://www.oecd.org/dac/financing-sustainable-development/development-finance-standards/dacdatasubmitters.htm).

For our analysis, we **included both bilateral (country to country) as well as multilateral (international organisation to country) development aid** spending.

Furthermore, the OECD has set **eligibility criteria** on which countries can receive development aid: low- and middle-income countries based on gross national income (GNI) per capita (according to the World Bank) as well as the least developed countries (according to the UN. We used the [list for 2020](https://www.oecd.org/dac/financing-sustainable-development/development-finance-standards/daclist.htm) according to which all African countries are eligible.

For the main part of the analysis we **focus on ODA**. However, ODA is only one stream of money flow. There are other official flows (OOF), which were included in the time series visualiation. Additional money streams like foreign direct investments, remittances, and private grants for example were not included in this analysis.

## Terminology and definitions

**Official development aid (ODA)** is [defined by OECD](https://www.oecd.org/dac/financing-sustainable-development/development-finance-standards/officialdevelopmentassistancedefinitionandcoverage.htm) as follows: "Official Development Assistance (ODA) is the term used by Development Assi stance Committee (DAC) members to refer to what most people would call aid. To be counted as ODA, public money must be given outright or loaned on concessional (non-commercial) terms, and be used to support the welfare or development of developing countries."

The term **"Official donors"** coined by OECD comprises DAC + Non-DAC + multilateral donors

## OECD QWIDS database - for yearly disbursements

The data on yearly ODA disbursments by Official Donors were extracted from OECD's [QWIDS database](https://stats.oecd.org/qwids/).

We selected the dataset `2a Disbursements` and subsequently changed the layout to:
* rows: recipients
* columns: donors
* flow type: disbursements
* flow details: 22 selected
* amoutn: current prices
* flow: ODA
* Sectors: all sectors

As for the time period, we extracted all datasets from 2002 to 2019 as .csv files.

Each of this datasets, we applied a couple of cleaning steps in order to read the .csv files properly into a pandas dataframe

* we deleted the first row of metadata
* replace first two column titles with `Recipient,"FakeColumn",`
* delete empty row 2 `"Recipient(s)",`
<!-- * replace `df_melted['currentPrices_Million_USD']` with nothing -->

## OECD QWIDS - for ODA / OOF spending by Official Donors to Subsahara Africa

To create the timeline, we used a [different selection settings in QWIDS](
https://stats.oecd.org/qwids/#?x=1&y=6&f=2:242,4:1,7:1,9:85,3:51,5:3,8:85&q=2:242+4:1,2+7:1+9:85+3:51+5:3+8:85+1:2,25,26+6:2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019) to extract the data for Subsahara Africa overall, for both ODA spendings as well as OOF spendings.


## OECD CRS database - Delivery Channels

To identify which delivery channels DAC members used for their development aid, we used data from OECD's **Creditor reporting system (CRS)** that can be bulk-downloaded as a pipe-separated txt file from [here](https://stats.oecd.org/DownloadFiles.aspx?HideTopMenu=yes&DatasetCode=CRS1). Please note that this file isn't included in the data folder in this repo due to the fact that it was too large to include. It's referenced in the code as `CRS2019data.csv`.

Given that as of January 7th, 2022, the data for 2020 were incomplete, we opted to use the data for 2019 instead. 

In addition to this dataset, we used the excel file provided on this page on [DAC codes](https://www.oecd.org/dac/financing-sustainable-development/development-finance-standards/dacandcrscodelists.htm) to correctly decipher the `ParentChannelCode` column in the dataset.

## AidData by the University William and Mary

We wanted to integrate development aid spending by China into our analysis. As China does not report to the OECD, we needed to find a different data source covering China.

Initial research showed that there is not adequate Chinese database we could use, but most Chinese academics reference the AidData dataset compiled by researchers at the University William and Mary.

The dataset was last updated in September 2021, containing data from 2000 to 2017.

Before conducting our analysis, we applied some filtering steps:

* We selected only those entries that were labelled as suitable for aggregates
* Since the OECD has criteria for ODA eligibility, we also applied the same criteria to China's development aid spending, to only include financing for countries that would be eligible under OECD rules (see above)
* For the main analysis, we only selected those entries that were labelled "ODA-like", excluding "OOF-like" entries. The latter were only included for creating a timeline of spendings over the years

# Data processing

## Unit conversion

All datasets obtained were using different units. The QWIDS data was in current prices (million USD), the CRS data was in constant 2019 prices (million USD) and AidData was in constant 2017 prices (USD).

The main difference between current prices and constant prices is that the latter are adjusted for inflation.

We converted all amounts to constant 2020 prices (million USD).

For this, consumer price indices were sourced from the [US Census Bureau's website](https://www.census.gov/topics/income-poverty/income/guidance/current-vs-constant-dollars.html) that provides an Excel file `CPI_U_RS.xlsx` holding the "Annual Average Consumer Price Index Research Series (CPI-U-RS): 1947 to 2020".

Prices already in constant dollar but with a different base year were converted to constant 2020 prices like this

`constantPrice of year a * (CPI-U-RS of 2020 / CPI-U-RS of year a`

Current prices were converted to constant 2020 prices like this

`currentPrice of year b * (CPI-U-RS of 2020 / CPI-U-RS of year b)`

with `b` changing throughout the dataset.

## Aggregation: Development aid over the past 18 years (sums)

Instead of only looking at the aid spending in one year, we made the editorial choice to sum up the data over the last years of available data.

The AidData on China's development aid is available for 18 years from 2000 to 2017 (last updated: September 2021).

Accordingly, we also selected the last 18 years of available data for the OECD data, the timeframe from 2002 to 2019.

## Delivery channels

For the delivery channels, we only looked at the most recent years, for both Subsahara Africa overall as well as for the three countries we were particularly interested in: the DRC, Mali and Cameroon as the three biggest francophone recipients.

We filtered the raw data mentioned in the data sources section:
* for the `Category` column to only include entries with the value `10`, as that equals ODA, according to the DAC code list
* for the column `RegionName` to only include entries with the value `South of Sahara`

We noticed that some project entries were development aid to entire regions within Subsahara Africa, such as Eastern Africa or Western Africa rather than ODA spendings in individual countries. We opted to still include these grants in our overview of delivery channels.

# Data analysis

For a detailed step-by-step overview of data processing and data analysis, please refer to the two jupyter notebooks in the [code folder](/code).