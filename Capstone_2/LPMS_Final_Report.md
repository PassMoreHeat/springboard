# Predicting Vessel Delays for Corps of Engineers-Lock Performance Management System Data.
__Springboard Capstone 2 Final Report__

_By Heather A. Passmore, Ph.D._

_The following is the final report written by H. Passmore for the Springboard Career Track Capstone 2: Predicting Vessel Delays for Corps of Engineers-Lock Performance Management System._
****

### Report Outline:
1. Problem Definition and Objectives
2. Client Profile
3. Data Sources
	4. Lock Performance Data
	5. NOAA Weather Station Data 
6. Data Wrangling Approach
	4. Methods for Cleaning and Wrangling
	5. Preprocessing Steps for Machine Learning
4. Exploratory Analyses and Statistics
5. Delay Classification
	6. Feature Preparation and Data Splitting
	7. Model Selection
	8. Model Fitting
	9. Model Evaluation
6. Caveats and Future Directions
7. Recommendation to USACE
9. Acknowledgements
10. Helpful Resources
11. Key to Codes and Variables


## 1.0 Problem Definition and Objectives

Cargo vessel traffic on United States waterways and through US Army Corps of Engineers-managed locks plays an important role in the transport of foreign and domestic goods into and out of the United States. Locks are owned and operated by the US Army Corps of Engineers (USACE), range in age from 144 to 14 years since construction, and are considered by many to have surpassed their life-expectancy. __Delays and failures of locks have immediate financial implications for shipping companies and for the USACE.__ 

My focus is to determine what factors predict delays for vessels as they approach locks and await lockage. The Lock Performance Management System (LPMS) logs 'delay-time' for individual vessels approaching a lock. Delay-time is the time elapsed from the arrival of a vessel at a lock to the start of lockage. While all vessels have some delay-time, at times the delay before lockage is extensive ranging from x to y over the last 17 years. __My goal is to determine what factors predict extended delays.__ To build predictive models of extended delay I selected ten focal locks with high traffic and high proportions of vessels with longer delay-times. Using logistic regression and random forest models my goal is to classify the conditions associated with vessel delays and identify features useful for predicting delays. My supervised learning models are built from daily vessel traffic at 10 focal locks over a period of 5 recent years (one vessel per lock per day as available). 

## 2.0 Client Profile
The Institute for Water Resources (IWR) headquartered in Alexandria, VA is a Field Operating Activity of the United States Army Corps of Engineers (USACE) responsible for water resources planning and water management programs. One focused center of IWR is the Navigation and Civil Works Decision Support Center (NDC) and its Waterborne Commerce Statistical Center (WCSC) in New Orleans, LA. These entities focus on data collection organization for waterborne commerce, vessel characteristics, port facilities, dredging information, and information on navigation locks.

My contact at USACE is Steven Riley from the Navigation and Civil Works Decision Support Center. The NDC regularly analyzes commodity and transport data collected from lock operations. However, they have identified a need for a deeper analysis of the national network of waterway locks. Data exploration to find relationships relevant to budgeting, maintenance and scheduling would inform their planning for future operations. Specifically, NDC would like to know if increased lockage times indicate that a stoppage is imminent. Are there factors that could be used to predict delays and stoppages? 

In this Final Report for Springboard and my written report to USACE I include information on the factors that are most influential on vessel delays. USACE will be able to use general findings about influential variables from my report to frame future analysis and potentially future lock maintenance schedules. My findings will inform stakeholders interal and external to the USACE. Further, through my exploration and modeling I have a deeper understanding of the LPMS system. I am eager to recommend and continue developing models that add additional insight for improving the efficiency of our national lock and dam system.

## 3.0 Data Sources

The predictive models for the project incorporate data from two sources: The US Army Corps of Engineers Lock Performace Management System database and The National Oceanic and Atmospheric Administration's (NOAA) weather stations.

### 3.1 Lock Performace Data

The LPMS data are tables from US Army Corps of Engineers (USACE) Oracle Database with lock data from year 2000 through 2017. The full database includes separate tables for vessels, barges, flotillas, stall stoppages, and vesel traffic. Additional tables contain identifiers and details for lock locations, lock chambers, locks, rivers, and districts. For this initial effort I focused on vessel traffic data,  metrics from stall stoppage records, lock locations, and lock, river, and district identifiers.

Initial processing for tables involved dividing large files by datetime fields, formatting fields, and creating calculated time events. Once  my first machine learning questions developed toward predicting where and when longer delays occurred, the data table most useful to me became the vessel traffic table which documents the passage of each vessel through locks. I also used the stall-stopage table to link scheduled and unscheduled events where navigation is either slowed (stalls) or halted (stoppages) to individual locks and days. Secondarily I used identifiers from river-master, lock-master, and districts to identify rivers and locks by name and lock-locations to build maps (Figure 1).

![MapOfLocks](Images/LPMS_lock_map.png)

__Figure 1.__ Locations of 196 locks owned or managed by the US Army Corps of Engineers and monitored with the Lock Performance Monitoring System (LPMS). The lock-locations table includes latitude and longitude coordinates for each lock. With these I used matplotlib's basemap to plot maps of all lock locations

For my data mining, exploration and modeling efforts I sampled a subset of the available data. The full database of LPMS records covers 17 years (from 2000 through 2017) of vessel traffic through 196 locks on 38 rivers in the eastern half of the US plus Washington and Orgegon. Details of my initial data acquisition and wrangling are described in my [LPMS Milestone Report](https://github.com/PassMoreHeat/springboard/blob/master/Capstone_2/LPMS_Milestone_Report.md). My first subset of data focused on just the last 5 years of vessel records (n = 3,039,626 records). From this I took a uniform random sample of 392,000 rows of vessel data. Through exploration of the sampled vessel data I determined a defintion for extended vessel delay by lock and selected 10 focal locks with high traffic and high proportions of delayed vessels for my machine learning and classification study.

### 3.2 NOAA Weather Station Data 

In addition to Lock Performance Management System data provided by USACE I acquired historical weather data to pair with locks by location. The National Oceanic and Atmospheric Administration's (NOAA) National Centers for Environmental Information provides free data from certain weather station across the United States (Figure 3). Using the ['Data Tool: Find a Station'](https://www.ncdc.noaa.gov/cdo-web/datatools/findstation) feature I identified 7 airport weather stations near focal lock locations. Three of these weather stations were near to two locks each and the data will be applied to both locations. 

![WeatherMap](Images/weather_map.png)

__Figure 3.__ Locations of available historical daily temperature, precipitation, and wind weather records from NOAA's National Centers for Environmental Information (https://www.ncdc.noaa.gov/cdo-web/datatools/findstation)

For all seven airport weather stations I was able to acquire 100% of daily summaries for the five-year study period. The data categories I selected were: Air Temperature, Precipitation, Wind, and Weather Type. Please see [Data Wrangling Report](https://github.com/PassMoreHeat/springboard/blob/master/Capstone_2/Data_Wrangling_Report.md) for details of these variables. 

My final dataframe for the machine learning stage of this project includes one vessel per day per lock at ten different USACE locks with high traffic. Linked to each lock per day I have daily weather summaries in these categories relevant to vessel traffic on waterways.

## 4.0 Data Wrangling Approach

### 4.1 Methods for Cleaning and Wrangling

Following initial sampling of the vessel traffic table my main dataframe consists of 392,000 rows with eight 'datetime' columns, four float continuous variable (float) columns, four integer columns, and seven text columns (object or string fields). With this even sample over the five-year study period I 1) explore and define extended delay per lock, 2) link metrics from unplanned stall-stoppages, 3) link daily weather summaries and 4) identify 10 focal locks for machine learning. Ultimately, with a focus on 10 locks I explore the distribution of delay-time for districts, rivers, locks as I determine my working definition of 'delay' and begin work to predict delay from lock, vessel and weather characteristics.

__Definition of delay__ Through exploration of vessel traffic data I determined that defining the same length of delay for all locks is not appropriate. Including only non-zero delay-times I calculated the 75th percentile of delay time and assigned delay 'True' or 'False' to all vessels in the traffic dataframe. With delay-time of 110 minutes or more the overall proportion of vessels delayed was 0.16. However, the proportion of delays per lock varied widely. Two locks had over 85% of vessels 'delayed,' seven other locks had 40 to 60% of vessels with delay-times over 110 minutes. One definition of extended delay across all locks does not make sense

Instead, I calculated the 75th percentile of delay-time by lock. I applied this definition on a lock-by-lock basis to define 'delay-catagory' as a binary variable, and my ultimate target variable in machine learning, where 0 is no-extreme delay and 1 is extreme-delay. For each vessel approaching a lock, if the column delay-time is greater than the 75th percentile of delay for that lock, then that vessel is catagroized as 'delayed' at that lock. Within the five-year sample of vessel traffic data, under this definition of extreme delay there are n = 333,264 vessels at locks with no delay, and 58,736 vessel recods in the extreme delay catagory or 15% of all vessels in the sample.

__Unplanned stall-stoppage metric__ The purpose of the stall-stoppage data for this project was two-fold. First, I used records of 'scheduled' stalls to remove vessel records at a lock on a day when there was a scheduled stoppage. Scheduled stoppages include regular maintenance, periods of time when personnel are not attending the lock, etc. These stall events may result in longer delay times as vessels approach locks, but I do not want to include them in my predictive model in my effort to determine what externals events cause delays for vessels. Second, I used the stall-stoppage table to extract information about unplanned stalls and stoppages and to connect the duration and reason-code of these unplanned stalls to the lock/day records in my machine learning dataset.

The five-year dataframe of stall stoppage data has 288818 entries. In subsequent steps I divided the table into separate dataframes for scheduled and unscheduled stalls (scheduled stop = True or False). Where there was a scheduled stall stoppage at a lock on a day I removed data for that lock/day from the Traffic dataframe so that scheduled stalls did not inflate the measure of delay  or influence model predictions of delay. Further, I used pivot_table on the dataframe of unscheduled stalls to produce a data frame with one record per lock per day with columns for each REASON-CODE and the mean of delay-time for that day/reason in each cell. This table, merged with the traffic table increases the type of information available for building the models.

![EROC_Delays](Images/prop.delay.by.eroc.overall.png)

__Figure 2.__ Proportions of vessels in the extended delay catagory for each USACE Division-District ('EROC', or Engineer Reporting Office Code) in the full 5-year data set. Some districts have much higher proportions of delayed vessels than others.

__Identifying Focal Locks for Machine Learning__  To determine a subset of focal locks I explored patterns of delay per district, river, lockage type, and lock (Figure 2 & 3). Next, I assigned delay categories HIGH or LOW to individual locks based on proportion of delays per lock. The division between HIGH and LOW delays is based on the frequency distribution of delays (Figure 4). In order to eliminate locks with low rates of delayed vessels I dropped all vessels/lock data for locks with LOW proportions of delay. By exploring patterns of delays over time and rates of delays at different scales I identified outlier locks and dropped those locks from the dataframe. Outliers included locks with very low traffic but high proportions of delay, individual rivers with fewer than 10 delays over 5 years, and individual locks with fewer than 1000 delays over 5-years. From the remaining locks I identified 15 with the highest vessel traffic and proportions of delays. I selected 10 of these with the goal of having focal locks across several districts and rivers (Figure 5).

![LockageType_Delays](Images/prop.delay.by.lockagetype.overall.png)

__Figure 3.__ _Proportions of vessels in the extended delay catagory for each Lockage Type (the configuration of vessels as they transit a lock; see section 11.0 for key to lockage type codes) in the full 5-year data set. Lockage type 'J' is a 'Jackknife' where the flotilla is rearranged. This lockage is associated with a higher proportion of delays, but is also rare and did not end up in the final machine learning dataset._

![Histogram_delay_freq](Images/histo.prop.delay.png)

__Figure 4.__ _The distribution of delay proportions for locks. The lock with nearly 50% of vessels delayed was an oulier with a total of two vessels during the study period, one of which was catagorized as delayed. From this distribution I determined the artificial cut-off for 'low' and 'high' proportions of delays and dropped all locks with delay proportions less than 0.15 from the pool of locks for machine learning._

Finally, I randomized the data, dropped duplicates per lock/day for delayed and non-delayed locks separately, re-combined the two delay catagories again and dropped duplicates if there were delay/no-delay duplicate per lock/day. In these cases I kept the vessel that was delayed. From this full dataset of many locks and one vessel/day I created the dataframe for machine learning with vessel traffic for 10 focal locks.

![MapOf10Locks](Images/lock_10_map_colors.png)

__Figure 5.__ _Locations of 10 focal locks for the machine learning steps incorporating weather data. Lock locations are marked with red, blue and magenta triangles to make it easier to distinguish nearby locks. Focal locks are located in 5 states: Texas (3 locks), Indiana (1), Illinois (1), Louisiana (4), and Washington (1)._

The focal lock data consists of 15538 vessel records including columns related to vessel types, lockage types, and maximum duration of unplanned delays. I then merged this data with nearby daily airport weather station data. Weather data includes temperature, wind, and precipitation varialbes as well as a binomial field for occurances of extreme weather types (fog, hail, tornado, etc.). This focal data set contains 8435 vessels that not delayed and 7103 vessels in the extreme delay catagory.

### 4.2 Preprocessing Steps for Machine Learning

In order to apply classification algorithms to the LPMS data from 10 focal locks I processed the data to encode binary variables, create dummy columns for catagorical variables, create date features from the date-time stamp, imputation of the rare missing value in a column, and replacing 'NaN' cells with '0'. Following these preprocessing steps the machine learning lock dataframe contains the following catagories of variables.

__Target (1):__

*  'delay-cat', the target variable for classification, delay = 1. 

__Binary fields (5):__ 

* Did a vessel require mechanical or vessel-powered assistance at the lock? _'assist'_ where 1 = yes.
* Was there one or more recorded extreme weather types that day? _'WType'_ where 1 = yes.
* Three binary date features, (_'weekday', 'night', 'holiday'_), where 1 means 'True' for the variable name.

__Catagorical fields made into binary 'dummies' columns:__ 

* Unique identifier for District/River/Lock, _'E_R_L'_, 9 binary columns.
* Code for type of lockage, _'lockage'_, 5 binary columns.
* Code for Vessel Function Type, _'VFT'_, 7 binary columns.
* Field for each year in study data, _'year'_, 4 binary columns.
* Date-time data lumped into four seasons, _'season'_, 3 binary columns.

__Numerical fields:__

* The maximum duration of an unplanned stall at a lock: 'unplan_max_stop'
* Ten weather metrics (_'AWND', 'PGTM', 'PRCP', 'SNOW', 'SNWD', 'TAVG', 'TMAX', 'TMIN', 'WSF2', 'WSF5'_).

Note: see section 11.0 below for a key to 'lockage', 'VFT', and weather codes.

## 5.0 Exploratory Analyses and Statistics

Through exploration of first the whole 5-year vessel traffic data set, and alter the focal locks paired with weather data, I identified factors associated with differences between delayed and not-delayed vessels. Overall, the number of delayed vessels at USACE locks increased over the five-year study period (Figure 6). In addition, seasonal patterns of vessel delays indicate delay rates are reduced by half during winter months (Figure 7).

![overall_yearly](Images/yearly.vessel.delay.overall.png)

__Figure 6.__ _Yearly counts of delayed vessels across all USACE locks from 2013 through 2017. Frequency of extended delays increases over time. Delay is defined as the recorded delay-times greater than the 75th percentile of delay time at a given lock._

![overall_monthly](Images/monthly.vessel.delay.overall.png)

__Figure 7.__ _Monthly counts of delayed vessels across all USACE locks from 2013 through 2017. Delay frequency has a seasonal pattern with fewer delays, and perhaps less vessel traffic during the colder seasons. Delay is defined as the recorded delay-times greater than the 75th percentile of delay time at a given lock._

Focal locks are located within 5 separate Districts and on 5 different rivers. During the period of study half of the focal locks have higher numbers of delay-days (days when there is at least one delayed vessel approaching the lock) than non-delay days (Figure 8). One factor that may affect vessel passage through locks is lockage-type. Lockage-type is the configuration of vessels as they transit a lock. Although this factor may be more likely to affect approach time and delay time for subsequent vessels than for the focal vessel (if different lockage-types have more complex proceedures for entering and exiting locks) we find reason to include it in this study. In the focal lock dataset Open Passage (O) and Straight (S) lockages are the most common (Figure 9).

![lock_count](Images/lock_delay_count.png)

__Figure 8.__ _For 50% of focal locks there are more delay-days than non-delay-days over the period of study. Codes for lock identification, 'E_R_L', indicate the District, River, and Lock codes. Target binomial delay-cat, delay = 1, no-delay = 0._

![lockage_type_count](Images/lockage_type_delay_count.png)

__Figure 9.__ _Lockage-type, the configuration of vessels as they transit a lock, is more likely to affect approach time and delay time for subsequent vessels if passage is delayed during lockage. Here Open Passage (O) and Straight (S) lockages are the most common lockage types._

Weather conditions may affect delay-times as vessels approach locks. Although this is not a factor that USACE can control, understanding the effects of weather types and extreme weather events could help USACE develop contingency plans for various seasons and specific weather conditions. In order to constrain the spread of delay-time (in minutes) I log-transformed this variable for graphing and statistical tests. Average wind speed (AWIND) appears to have a positive linear relationship with log-delay-time for vessels in the extreme delay catagory (Figure 10). A t-test of average wind speed for delay and non-delay vessels indicates a significant difference between the groups (t = 2.023, p = 0.04) however, the mean average wind speed is slightly greater for the non-delay group.

![yearly_boxplot](Images/awind_scatter.png)

__Figure 10.__ _Average wind speed and log-delay time may have a linear relationship, but a comparison of wind speeds for the two delay catagories does not indicate a meaningful difference. In fact, average wind speed was slightly greater on the days with non-delay vessels._

Other weather variables may be associated with delay-days for vessels at locks. Daily minimum temperature during winter seasons may negatively influence delay times. Differences in daily minimum temperatures were significantly different betwen the delay catagories (t = -4.5, p=0.0). Overall mean minimum temperatures are very similar (Figure 11).

![yearly_boxplot](Images/delay_cat_tmin_box.png)

__Figure 11.__ _Average minimum temperature is significantly different between delay and non-delay vessels but overall means are similar._

One measure of snow fall ('SNOW') from NOAA stations is uncommon. While there may be a linear relationship between snowfall and log-delay-time in the two delay catagories (Figure 12) there is not a statistical difference between snowfall amounts for the two delay catagories based on a t-test.

![yearly_boxplot](Images/snow_scatter.png)

__Figure 12.__ _Measures of daily snowfall may be linearly related to delay-time but snowfall comparisons between delay and non-delay groups are not significantly different._

While some quantitative comparisons of time, weather, vessel, and lock-related variables may indicate differences between the binary target catagory delay/no-delay, there are not strong indications of significant statistical differences for most metrics. Combining these variables to build classification algorithms can potentially give us more insight into the factors affecting longer vessels delays.

## 6.0 Delay Classification

My approach for the Lock Performace Management System study of vessel delays is a supervised learning classification problem. My binary target variable is delay (y) as defined by my prior exploratory analyses. The features included as predictive variables include binary and catagorical features in three catagories: 1) lock or vessel related, 2) weather metrics, 3) date/time-derived features. The null hypothesis for this classificaton problem is that no vessels are delayed as they approach a lock. My modeling task is to predict which vessels will be delayed with a greater accuracy than the null.

### 6.1 Feature Preparation and Data Splitting

The dataframe prepped for machine learning, prior to splitting for training and testing, included 15538 rows where each row is a vessel at a lock on a day. I started with 44 features plus the target, 'delay-cat'. The 44 features comprise 11 continuous variables and 33 binary fields. Five of the binary fields are single variables (not greated with the pandas get_dummies function). The remaining 28 columns of X are binary dummy variables made from five catagorical fields ('E-R-L', 'lockage', 'VFT', 'year', and 'season'). Each of these has one fewer columns that the number of levels for that catagory to avoid redundancy in the model. The features included in the initial model are those vetted and checked through exploratory data analysis steps. Composite levels of some catagories were created when several catagories were infrequent.

After separating the array of features X from the target array (y) I divided my full dataset into training and test sets with a 70%/30% split with stratification on the target variable (y). I used the training data (X-train and y-train) to fit the models and tune the hyperparameters and the test data (X-test and y-test) to evaluate the tuned models and to identify the best performing model. To compare model performace I use the area under the reciever operator curve, or AUC score. In addition to the full train and test datasets for X and y, I further subsampled these dataframes using masks for each individual lock. Thus I was able to compare not just different classifiers, but the same classifiers on the subset of data for each focal lock. Since delay times varied greatly by lock and I defined delay based on delay times for that lock these single-lock models have the potential to be better classifiers for delay.

### 6.2 Model Selection

To predict vessel delay catagories I fit the data with two classifiers: Logistic Regression (with LogisticRegressionCV for cross validation of hyperparameters) and Random Forest followed by hyperparameter tuning with GridSearchCV. I focused on these two appropriate models while incorporating several other methods in my approach:

1. Compare fit of original dataset to one where all continuous variables are scaled or standardized.
1. Explore effects of Recursive Feature Elimination from scikit-learn.
1. Improving models by incorporating importance values of each feature.
1. Modeling individual locks separately to improve prediction success.

Logistic regression for classification has the benefit of being simple and easy to interpret. In addition logistic regression classifiers return coefficients useful for assessing feature importances.

The ensemble decision tree classifier, random forest are good for fitting models with catagorical features, work well with high dimensional data, and usually run efficiently. While decision tree classifiers are prone to overfitting, random forest classifiers tend to overcome that problem because they are ensemble models. Futher, using feature importances, random forest models are generally easy to interpret.

### 6.3 Model Fitting

_Scaling and Standardization of continuous variables._ With the modules from scikit-learn preprocessing library I separately applied the MinMaxScaler and the 'scale' function to all continuous variables. Based on AUC scores for both Logistic Regression and Random Forest models fit with the scaled and standardized arrays these transformed did not improve predictions. Model performace was substantially lower (AUC = 0.57) than the models run on the untransformed data (AUC = 0.64) and these pre-processing steps will not be used.

_Recursive Feature Elimination (RFE)._ I applied RFE from scikit-learn preprocessing to both the Logistic Regression model and the Random Forest model, first with 10 features and then with 15, and with step=1. I then used the top ranked features (10 and 15 respectively) to re-fit the models

| Full Data Models         | Description                     |
| -----------------       |--------------------------------|
| LogisticRegressionCV                | info  | 
| Random Forest  | info     |
| info | info|


| Single Lock Models  | Description                     |
| -----------------   |--------------------------------|
| B2_GI-3  | info  | 
| B2-GI-4  | info     |
| B2-GI-77 | info |
| B3-MI-27 | info      |
| G3-WS-1	 | info |
| H2-OH-76 | info |
| M3-GI-11 | info |
| M3-GI-12 | info |
| M3-GI-14 | info |
| B2-FB-41 | info |
|          |                                 |



### 6.4 Model Evaluation

## 7.0 Future Directions and Caveats

## 8.0 Recommendation to USACE

Internal Use: Predictive models of lockage delays will be useful to USACE for internal planning and scheduling. My model potentially can be designed to update and re-tune when additional years of data are available. These models could be useful to USACE and NDC into the future.

External Use: USACE makes some of their lock data available to the public through the [LPMS Public Web](http://corpslocks.usace.army.mil/lpwb/f?p=121:1:7534946428825:::::). They also produce annual reports for related topics like Waterborne Container Traffic, Commodity Indicators, Ports and State Data, Domestic Waterborne Traffic by Commerce Type, etc. Results of my analysis may inform some of this publicly shared analysis of US waterways traffic and locks. Having outwardly visible products is an important part of the mission of USACE and IWR to "provide forward-looking analysis and research in developing planning methodologies to aid the Civil Works program" and to provide " (a) analysis of emerging water resources trends and issues, (b) state-of-the-art planning and hydrologic engineering methods, models and training, and (c) national data management of results-oriented program and project information across Civil Works business lines."

## 9. Acknowledgements
I am grateful to Springboard for supporting and facilitating my Data Science studies. I thank Ben Bell, my Springboard Mentor, for his guidance and support during my exploration of this project. Thanks also to the Community of Springbaord mentors and students for feedback along the way.


## 10.0 Helpful Resources
[FactCard](http://www.navigationdatacenter.us/factcard/FactCard2016.pdf), 2016. The US Waterway System 2016 Transportation Facts & Information. Navigation and Civil Works Decision Support Center, US Army Corps of Engineers. http://www.navigationdatacenter.us/factcard/FactCard2016.pdf

[LPMS Public Web](http://corpslocks.usace.army.mil/lpwb/f?p=121:1:7534946428825:::::) http://corpslocks.usace.army.mil/lpwb/f?p=121:1:7534946428825:::::

Pedregosa, F., Varoquaux, G., Gramfort, A., Michel, V., Thirion, B., Grisel, O., Blondel, M., Prettenhofer, P., Weiss, R., Dubourg, V., Vanderplas, J., Passos, A., Cournapeau, D., Brucher, M., Perrot, M., and Duchesnay, E. 2011. Scikit-learn: Machine Learning in Python, Journal of Machine Learning Research, 12, pp. 2825-2830.

[Waterborne Commerce National Summary](http://www.navigationdatacenter.us/wcsc/pdf/wcusnatl16.pdf), 2016. In Waterborne Commerce of the United States, Calendar year 2016, Part 5-National Summaries. Compiled under the supervision of the Institute of Water Resources, USACE, Alexandria, VA. http://www.navigationdatacenter.us/wcsc/pdf/wcusnatl16.pdf

Yu, T.E, B.C. English and R.J. Menard. 2016. Economic Impacts Analysis of Inland Waterway Disruption on the Transport of Corn and Soybeans. Staff Report #AE16-08. Department of Agricultural and Resource Economics, University of Tennessee.

## 11.0 Key to Codes and Variables
### Weather data codes:
* PRCP - Precipitation
* SNWD - Snow depth
* PGTM - Peak gust time
* TMAX - Maximum temperature
* WSF2 - Fastest 2-minute wind speed
* WSF5 - Fastest 5-second wind speed
* SNOW - Snowfall
* AWND - Average wind speed
* TAVG - Average Temperature.
* TMIN - Minimum temperature

* WType -  _'WType' = 1 indicates the occurance of one or more of the following weather types on that day. Weather Types were combined since each is more or less rare._
WT01 - Fog, ice fog, or freezing fog (may include heavy fog)
WT02 - Heavy fog or heaving freezing fog (not always distinguished from fog)
WT03 - Thunder
WT04 - Ice pellets, sleet, snow pellets, or small hail" 
WT05 - Hail (may include small hail)
WT06 - Glaze or rime 
WT07 - Dust, volcanic ash, blowing dust, blowing sand, or blowing obstruction
WT08 - Smoke or haze 
WT10 - Tornado, waterspout, or funnel cloud" 
WT11 - High or damaging winds
WT13 - Mist
WT14 - Drizzle
WT15 - Freezing drizzle 
WT16 - Rain (may include freezing rain, drizzle, and freezing drizzle)" 
WT17 - Freezing rain 
WT18 - Snow, snow pellets, snow grains, or ice crystals
WT19 - Unknown source of precipitation 
WT21 - Ground fog 
WT22 - Ice fog or freezing fog

### Lock-Type Codes (the configuration of vessels as they transit a lock).:
* S - Straight - The flotilla is not broken up for lockage (9349 occurances).
* O - Open Pass - The vessel traverses the lock with no chambering (both sets of gates open but thru the chamber; 6070 occurances).
* C - Consecutive - Dedicating the lock to servicing one flotilla's cuts continuously thru the lock in the same direction without any intervening lockages (53 occurances).
* K - Knockout - The towboat alone is separated from its barges and moved alongside the barges for lockage (47 occurances).
* X - Lumped catagory of A, B, T, V, and Z (Other), (10 occurances)
* N - Navigable Pass - The tow traverses the dam instead of the lock (actually navigates outside the lock walls; 9 occurances).

### Vessel Function Types Codes:
* T - T-Tow or Tug Boat, (12657 occurances)
* R - Recreational Vessel, (870 occurances)
* K - Crewboat Vessel, (702 occurances)
* Y - Lump Dry Cargo (C) and Liquid Cargo (E), (606 occurances)
* F - Fishing Vessel, (441 occurances)
* P - Passenger Boat or Ferry, (159 occurances)
* X - Lump Federal Government Vessels (G), Government Nonfederal Vessels (N), and Federal Government Contractor (U), (83 occurances)
* W - Lump Non-Cargo Vessel (M) with Other (Z), (20 occurances)

***
Copyright 2018 Heather Passmore