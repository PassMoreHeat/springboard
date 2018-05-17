# Data Wrangling Process for US Army Corps of Engineers-Lock Performace Management System (LPMS) Data.
__Springboard Capstone 2 Data Wrangling Report__

****

### Table of Contents:
1. Data Aquisition & Description
2. LPMS Data Table Merges
3. Traffic Data Subsampling
4. Map of Lock Locations
5. Additional Data: NOAA Weather

## 1. Data Aquisition & Description
The LPMS data are tables from US Army Corps of Engineers (USACE) Oracle Database with lock data from 2000 to 2017. Data provided in comma-separated-values format (.csv) included the following tables:

* TRAFFIC (1048576 rows)
* BARGE-TRAFFIC (3947393 rows)
* BARGES (15328548 rows)
* FLOTILLAS (7132360 rows)
* VESSELS (27058 rows)
* STALL-STOPPAGE (288818 rows)
* CHAMBERMSTR (240 chambers)
* LOCKMSTR (196 locks)
* RIVERMSTR (39 rivers)
* DISTRICTS (33 districts)

Initial processing for tables involved dividing large files by datetime fields, formating fields, and creating calculated time events. These steps are outlined below:

__TRAFFIC.csv__

* Use command line to subset 17-year dataset into individual years for last 5 years ✅
	* To extract by year: “csvgrep -c 10 -m “/2017” TRAFFIC.csv > TRAFFIC-EXTRACT-2017.csv”
	* -c 10 indicates the column where the date data are stored
	* -m “/2017” specifies the exact content to match
	* I ran a line like this for each of 5 recent years to get 5 separate .csv files per year
* In Jupyter Notebook read each of the 5 TrafficExtractXXX.csv files and pickle them ✅
* Save each pickled file as individual years.
* Concatenate all 5 years into one Dataframe with 3039625 rows
	* 5-year Traffic Data: ‘traffic-2013-2017.pickle’ ✅
	* Converted all dates to datetime64[ns] dtype
	* Calculated ENTRY-TIME (Bow Over Sill to End Of Exit) ✅
	* Calculated EXIT-TIME (Start Of Exit to End Of Lockage) ✅
	* Calculated DELAY-TIME (Arrival Date to Start of Lockage) ✅
	* Primary Key: TRFC-PK: EROC, RIVER-CODE, LOCK-NO, CHAMBER-NO, LOCKAGE-TYPE, OPS-ID, VESSEL-NO 

__BARGE_TRAFFIC.csv__

* Read into Jupyter Notebook with pd.read-csv
* BARGE-TRAFFIC does not have a datetime field.
* Relationship between TRAFFIC and BARGE-TRAFFIC is not one-to-one because each vessel in TRAFFIC may have multiple barges.
* HEADERS:
	* EROC
	* RIVER-CODE
	* LOCK-NO
	* CHMBR-NO
	* OPS-ID
	* FLOTILLA-NO
	* VESSEL-NO
	* PVESSEL-NO
	* BARGE-ID
	* BARGE-TYPE
	* NUMBER-BARGES
	* NUMBER-PROCESSED
	* LINE-SEQ-NO: Renamed as "BT-line-seq" because not linked to column of same name in TRAFFIC
* PRIMARY KEY: EROC, RIVER-CODE, LOCK-NO, CHMBR-NO, LOCKAGE-TYPE, OPS-ID, VESSEL-NO, PVESSEL-NO, FLOTILLA-NO, BARGE-ID

__BARGES.csv__

* Read into Jupyter Notebook with pd.read-csv
* HEADERS:
	* EROC
	* FLOTILLA-NO
	* PVESSEL-NO
	* BARGE-ID
	* NUMBER-BARGES
	* BARGE-TYPE
	* BARGE-WIDTH
	* BARGE-LENGTH
* Primary Key: BRG-PK (PVESSEL-NO, FLOTILLA-NO, EROC, BARGE-ID)

__FLOTILLAS.csv__

* Read into Jupyter Notebook with pd.read-csv
* HEADERS:
	* EROC (with PVESSEL-NO = Foreign Key)
	* FLOTILLA-NO (with EROC, PVESSEL-NO = Primary Key
	* PVESSEL-NO (with EROC = Foreign Key)
	* FLOT-LENGTH
	* FLOT-WIDTH
	* FLOT-DRAFT-FT
	* FLOT-DRAFT-IN
	* STOP-CODE
	* NUM-LOADED-BRG
	* NUM-EMPTY-BRG
Primary Key: EROC , VESSEL-NO

__VESSELS.csv__

* Read into Jupyter Notebook with pd.read-csvDataFrame = vessels
* PRIMARY KEY: EROC,  VESSEL-NO


__STALL_STOPPAGE.csv__

* To make 5-year DataFrame:
	* Read into LPMS-data-merging-calculations.ipynb notebook with pd.read-csv ✅
	* BEG-STOP-DATE made to.datetime dtype and made index ✅
	* END-STOP-DATE made to.datetime dtype ✅
	* Calculated stop-minutes and stop-days from BEG-STOP-DATE and END-STOP-DATE ✅
	* Filtered out 5 years of recent data: 1/1/2013 to 12/31/2017 = stall-stop-13-17 ✅
* Pickled data file: ‘stall-stop-2013-2017’✅
* PRIMARY KEY: EROC, RIVER-CODE, LOCK-NO, CHMBR-NO, BEG-STOP-DATE

__CHAMBERMSTR.csv__

* Read into Jupyter Notebook with pd.read-csv
* DataFrame = chambermstr
* PRIMARY KEY: EROC, RIVER-CODE, LOCK-NO, CHMBR-NO

__LOCKMSTR.csv__

* Read into Jupyter Notebook with pd.read-csv
* DataFrame = lockmstr
* PRIMARY KEY: EROC, RIVER-CODE, LOCK-NO

__RIVERMSTR.csv__

* Read into Jupyter Notebook with pd.read-csv
* DataFrame = rivermstr
* PRIMARY KEY: RIVER-CODE

__DISTRICTS.csv__

* Read into Jupyter Notebook with pd.read-csv
* DataFrame = districts
* PRIMARY KEY: EROC

## 2. LPMS Data Table Merges

The main datafram 'traffic_13_17' is the basis for data analysis for this project because each row is an event of a vessel passing through a lock. At this stage we did not merge traffic_13_17 with other tables because the relationships were not one-to-one. Timestamped lock management events from January 1, 2013 through December 31, 2017 in these rows include calculated time events for delays and will be the focus of the predictive modeling.

The 'barge_traffic' dataframe does not contain datetime fields and is instead a record of barges passing through specific locks. Some vessels tow multiple barges so there are multipe rows of data per lockage event. Using the primary key for 'barge-traffic' I merged 3,947,393 rows with the 'barges' dataframe on the 'barge-traffic' primary key. 

`barge_traf_barges = pd.merge(barge_traffic, barges, how='inner', on=['PVESSEL_NO',` `'FLOTILLA_NO', 'EROC', 'BARGE_ID', 'NUMBER_BARGES', 'BARGE_TYPE'])`

The resulting dataframe 'barge-traf-barges' has an equal number of rows as barge-traffic due to the inner join. I merged this dataframe with the 'flotillas' dataframe using the 'barges' primary key. Finally, I merged the combined dataframe with 'vessels'. The resulting dataframe with 3,947,393 rows is pickled as 'barge-flot-vessles.pickle', and includes 25 columns.

The third working dataframe of merged LPMS data tables is based on the 'stall-stoppage' table. Stall-stoppage is a record of both planned and unplanned halts in navigation. These events are linked to specific chamber, lock, river, district details and have datetime formatted beginning and ending times. From these times I calulated stop times in minutes ('stop-minutes') and days ('stop-days'). Two additional calculate time events can be calculated using the stop times: 

* Approach Time: SOL (start of lockage) to BOS (bow over sill) from TRAFFIC table minus ‘stoppage time’ from STALL_STOPPAGE table.
* Chambering Time: EOE (end of entry) to SOE (start of exit) from TRAFFIC table minus ‘stoppage time’ from STALL_STOPPAGE table.

Starting with the ‘stall-stop-2013-2017’ dataframe I merged several tables containing identifiers and descriptors: stall-stoppage -> chambermstr -> lockmstr -> rivermstr -> districts. The resulting dataframe has 83492 rows.

## 3. Traffic Data Subsampling

Using uniform random sampling I took a subset of rows from the five-year 'traffic_13_17' dataframe. I calculated my sample size to include the number of rows equal to 2000 rows of vessel data for each of the 196 locks over the five-year period of study (size = 392,000). To take the random uniform sample from the dataframe of 3039626 rows I created an array of indices using numpy.random.choice without replacement:

`chosen_idx = np.random.choice(3039626, replace=False, size=392000)`

I then used the array of indices to subsample the 'traffic-13-17' dataframe:

`traf_sample = traffic_13_17.iloc[chosen_idx]`

My next steps are 1) to explore the distributions of delay times in my subsampled 'traffic' dataframe and use these findings to define a 'delay' for lockage proceedures. 2) Identify at least 7500 rows where yes/no is there a delay for 1 lock from each major regions over the 5 years. 3) Choose 1 vessel for each day for each lock, if there’s a delay, choose a ship with a delay. 4) Merge vessel data with barge data fromt the merged dataframe compiling barge-traffic + barges + flotilla + vessel details. 5) Merge vessel data with weather data and delay data into into one dataset for machine learning and predictive analytics on delays.

## 4. Map of Lock Locations

Locks in the LPMS are located along 39 rivers mostly in the eastern half of the US but also in Washington state, especially along the southern boarder and in Northern Oregon (Figure 1).

![MapOfLocks](Images/LPMS_lock_map.png)

__Figure 1.__ Locations of 196 locks owned or managed by the US Army Corps of Engineers and monitored with the Lock Performace Monitoring System (LPMS). 

## 5. Additional Data: NOAA Weather

In addition to Lock Performance Management System data provided by USACE I also have identified a source for historical weather data. The National Oceanic and Atmospheric Administration's (NOAA) National Centers for Environmental Information provides free data from certain weather station across the United States (Figure 2). I will link groups of locks to regional weather stations and incorporate the aggregated weather data into my predictive models.

![WeatherMap](Images/weather_map.png)

__Figure 2.__ Locations of available historical daily temperature, precipitation, and wind weather records from NOAA's National Centers for Environmental Information (https://www.ncdc.noaa.gov/cdo-web/datatools/findstation)
****