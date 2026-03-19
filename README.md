# Louisville Crime Stats project

## Data Chosen

I chose a file published by Louisville Metro Government of a list of occurred/reported crimes in 2020, with ~70,000 rows and 15 fields/columns:
`INCIDENT_NUMBER,DATE_REPORTED,DATE_OCCURED,CRIME_TYPE,UOR_DESC,NIBRS_CODE,UCR_HIERARCHY,ATT_COMP,LMPD_DIVISION,LMPD_BEAT,PREMISE_TYPE,BLOCK_ADDRESS,City,ZIP_CODE,ObjectId`

## Data Cleaning and Wrangling

I identified the columns with both anomalous and missing/Null/NaN values and renamed the columns in mixed case for better readability.

I then converted the 2 date fields to `DateTime` format (`Date_Reported` and `Date_Occurred`) and then split each of those 2 fields into new separate fields for Year, Month, Day,  Time, and Day of Week, then moved those new fields to follow each of the original date fields in order. I also moved 2 crime identifier fields to come after all of the date fields.

## EDA

I then began the process to either fill in inaccurate or missing/null/NaN fields with valid data or place an "Unknown" in them:

1. I first removed legacy floating points (".0") from the end of the ZIP_Code column. I had identified those in the Data Cleaning phase.
2. I then corrected known erroneous entries in the ZIP_Code column, including overwriting the value "40056" (a non-Jefferson County ZIP_Code apparently used as a placeholder) with "40201," having identified that as a ZIP Code in Jefferson County that does not correspond with a specific area of town.
3. I continued fixing ZIP_Code errors, including replacing missing/null/NaN values, by comparing records with missing/null/NaN ZIP_Codes to other records to attempt to fill in with accurate data, using the lambda function.
4. I also fixed a handful of identified City value errors.
5. Lastly, I filled in missing/null/NaN values in 3 other fields with "Unknown".

## Plotting

Having concluded the cleaning, I began the process to plot charts, based on some questions about the data. Given that none of the original fields were numeric (except for the 2 converted DateTime fields and their split value fields), this required doing some math. I first decided to do a bar chart to compare the number of Incidents reported by Crime_Type (Bar Chart 1). This yielded a couple of interesting things: a) according to the data, only 2 DUI crimes were reported/occurred (although there is another Crime Type category called "Drugs/Alcohol Violations"), and b) Assault crimes were by far the largest reported/occurred in 2020.

I next plotted out the number of Crime_Type(s) per Incident. I elected to go with a histogram for this chart, and the most interesting thing there to me is the fact that for each grouping of Number of Crime Types (1 through 5), each "step down" was approximately 90% for groups 1 to 4. In other words, Group 2 had roughly 10% (5,247) of the number of Group 1 (52,505), Group 3 had approximately 10% (522) of Group 2's number, and Group 4 had almost 10% (60) of Group 3. I don't believe there's a true pattern there--it's probably a total coincidence, but an interesting one.

The last chart I plotted is notable in that it attempted to see whether the day of the week, namely weekend days, was a factor in crime frequency. This chart appears to indicate that weekend days were not a factor, as both Saturday and Sunday had fewer crimes than any of the weekdays (except Tuesday, the 2nd lowest, right above Sunday) overall.

## Summary

In summary, the charts included in this repo attempt to answer the following questions:

1. Bar Chart 1: How many "Incident Counts" were reported by "Crime Types"?

2. Histogram Chart 2: How many "Crime Types" were reported per "Incident"?

3. Line Chart 3: Did crime occur more frequently on weekends (Sat/Sun) vs. weekdays (Mon-Fri)?

This particular exercise left me with more questions than answers, honestly. It would be interesting to see whether Moon phases (i.e., a "Full Moon") has an affect on crime rates, as some contend. It would also be interesting to do a heat map of the city, highlighting the Zip Codes where crime was most prevalent, perhaps even including population counts to get a per capita rate, rather than just a raw count. This could involve obtaining historical data from WeatherAPI.com and the US Census Bureau APIs. These are questions I can attempt to answer in the Capstone Project deliverable.
