# Crime Patterns and Socioeconomic Conditions in Louisville (2020) project

## Data Chosen

I chose a file published by Louisville Metro Government of a list of occurred/reported crimes in Louisville in 2020, with ~70,000 rows and 15 fields/columns:
`INCIDENT_NUMBER,DATE_REPORTED,DATE_OCCURED,CRIME_TYPE,UOR_DESC,NIBRS_CODE,UCR_HIERARCHY,ATT_COMP,LMPD_DIVISION,LMPD_BEAT,PREMISE_TYPE,BLOCK_ADDRESS,City,ZIP_CODE,ObjectId`. 
Source: Louisville Metro Government Open Data Portal, https://data.louisvilleky.gov/datasets/louisville-metro-ky-crime-data-2020/about.

I also chose to download a file from the US Census Bureau, which contained counts for Population, Poverty Level (incomes below, or at and above), Educational Attainment, and Gender by ZIP Code for Louisville, with 39 rows and 24 columns:
`ZIP_Code,Total_Population,Income_Below_Poverty,Inc-<-PL_Male,Inc-<-PL_Male_Not-high-school-grad,Inc-<-PL_Male_High-school-grad,Inc-<-PL_Male_Some-college-associates,
Inc-<-PL_Male_Bachelors-higher,Inc-<-PL_Female,Inc-<-PL_Female_Not-high-school-grad,Inc-<-PL_Female_High-school-grad,Inc-<-PL_Female_Some-college-associates,Inc-<-PL_Female_Bachelors-higher,Income_At_Above_Poverty,Inc->=-PL_Male,Inc->=-PL_Male_Not-high-school-grad,Inc->=-PL_Male_High-school-grad,Inc->=-PL_Male_Some-college-associates,Inc->=-PL_Male_Bachelors-higher,Inc->=-PL_Female,Inc->=-PL_Female_Not-high-school-grad,Inc->=-PL_Female_High-school-grad,Inc->=-PL_Female_Some-college-associates,Inc->=-PL_Female_Bachelors-higher`.
Source: U.S. Census Bureau. (n.d.). POVERTY STATUS IN THE PAST 12 MONTHS OF INDIVIDUALS BY SEX BY EDUCATIONAL ATTAINMENT. American Community Survey, ACS 5-Year Estimates Detailed Tables, Table B17003, https://data.census.gov/table/ACSDT5Y2020.B17003?t=Educational+Attainment:Income+and+Poverty:Official+Poverty+Measure:Populations+and+People:Poverty&g=860XX00US40023,40025,40056,40059,40118,40177,40202,40203,40204,40205,40206,40207,40208,40209,40210,40211,40212,40213,40214,40215,40216,40217,40218,40219,40220,40221,40222,40223,40228,40229,40231,40241,40242,40243,40245,40258,40272,40280,40291,40299&y=2020&d=ACS+5-Year+Estimates+Detailed+Tables&moe=false&tp=true&tableFilters=ag-Grid-AutoColumn~(Margin+of+Errorundefined).

Note that the Total_Population in this dataset includes ages 25 and up for whom poverty level has been determined ONLY, as that is the data the Census Bureau had available.


## Data Cleaning and Wrangling

For the Crime Data, I identified columns with both anomalous and missing/Null/NaN values and renamed the columns in mixed case for better readability. I then converted the 2 date fields (`Date_Reported` and `Date_Occurred`) to `DateTime` format and split each of those 2 fields into new separate fields for Year, Month, Day, Time, and Day of Week.

For the Census Data, I renamed several of the columns for better readability, created new socioeconomic metrics columns for Poverty Level and Educational Attainment, then removed most of the fields pertaining to Gender and Educational Attainment (approximately 20 in all), as they became superfluous.

## EDA

For Crime Data, I created functions to either fill in inaccurate or missing/null/NaN ZIP Codes with valid data:

1. First removed legacy floating points (".0") from the end of the ZIP_Code column. I had identified those in the Data Cleaning phase.
2. Corrected known erroneous entries in the ZIP_Code column, including overwriting the value "40056" (a ZIP_Code apparently used as a placeholder) with "40231," having identified that as a ZIP Code in Jefferson County that does not correspond with a specific area of town, but was included in the Census Data.
3. Continued fixing ZIP_Code errors, including replacing missing/null/NaN values, by comparing records with missing/null/NaN ZIP_Codes to other records to fill in with accurate data, using a lambda function.
4. Also fixed a handful of identified City value errors.

Lastly, I created another function to fill in missing/null/NaN values in 3 other fields with "Unknown".

The Census Data didn't require any cleaning.

For my analysis, I first classified the Crime Data into Violent and Property categories to allow for comparisons. I included the "OTHER" Crime_Type in the Violent category, as it appears many of that type are violent crimes that are not explicitly labeled as such in the dataset (including Domestic Violence, Missing Persons, etc.), so further analysis may be needed to identify and classify these incidents appropriately. I aggregated counts for Total_Crimes, Violent_Crimes, and Property_Crimes by ZIP_Code in a new table/dataframe called "crime_summary". 

I merged the new crime_summary table with the Census Data, joined on ZIP Code, to create another new table called "merged", with 36 records and new fields for Total_Population, Income_Below_Poverty, Income_At_Above_Poverty, Poverty_Rate, HS_Grad_Rate, Crime_Rate, Violent_Rate, Property_Rate, and Poverty_Group. Most of the new fields were calculated using the Crime Data and Census Data to come up with rates of occurrence and other useful calculations.

## Plotting

Having concluded cleaning and analysis, I chose to plot charts that asked questions about whether and/or how socioeconomic factors impact or affect crime rates. The first scatter plot chart displays the Poverty Rate vs. the Violent Crime Rate for Louisville in 2020. Although there are a couple of outliers, a clear correlation between higher poverty rates and higher violent crime rates is visible and obvious. The next bar chart illustrates a comparison of average overall crime rates by poverty level/group. Again, both High and Very High poverty levels show a correlation with higher crime rates. In the third chart, I compare the Poverty Rate vs. Property Crime Rate in a similar way as chart 1, and again a distinct correlation can be observed between higher poverty rates and property crime rates. The last chart is a line chart which explicitly outlines a correlation between higher Educational Attainment (i.e., high school graduation rate) and lower crime rates by ZIP Code.

## Summary and Acknowledgements

I started this project with assumptions and questions about certain socioeconomic factors that impact crime, namely do negative economic factors in Louisville neighborhoods affect crime rates? The answer from this data seems to be "Yes!" For example, there is a strong negative correlation (-0.81) between high school graduation rates and crime rates across Louisville ZIP Codes. Areas with higher educational attainment consistently show lower levels of crime. This strong inverse relationship suggests that educational attainment may be a key indicator of broader socioeconomic conditions influencing crime, rather than a direct causal driver.

While most of these assumptions can be considered validated by the findings, there are a number of other questions I have about this data that could affect them:

1. There were signifcant protests in Louisville in the spring and summer of 2020 about the murders of Breonna Taylor and George Floyd, in particular, that could skew crime stats for certain parts of town (namely Downtown, in the 40202 ZIP Code), as there were many arrests during those protests. How could those impact these findings?
2. The murder rate in the city during this period was also higher than recent years. Did more murders mean more violent crime occurred then, as well?

NOTE: I used Google and ChatGPT in my project to verify my code, especially when I experienced issues with it. In certain cases, ChatGPT provided direct code to use.

ALSO: I tried my best to do the Database and SQL coding portion, but consistently ran into issues with the code there. If allowed, I'd love to attempt to continue my efforts there, even after the class is over.