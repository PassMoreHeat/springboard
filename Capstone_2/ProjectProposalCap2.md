# Determine factors causing vessel delays at US Army Corps of Engineers-managed locks on US waterways.
__Springboard Capstone 2 Project Proposal__

### Introduction:
The inland waterways of the United States are highways for domestic and foreign commerce. Commodities carried on barges and container ships move up and down US rivers and pass through locks owned and/or operated by the US Army Corps of Engineers (USACE). The Institute of Water Resources (IWR) within USACE collects data to document vessel transit through the 192 locks on 38 rivers and waterways. The Lock Performance Monitoring System (LPMS) developed and used by USACE holds both lockage information (including number of barges processed, direction of vessel traffic, and lock and chamber details) and event timing data (vessel arrival time, processing time and delay time). Some of this data is publicly available through the Freedom of Information Act through the [LPMS Public Web](http://corpslocks.usace.army.mil/lpwb/f?p=121:1:0:::::).

Various factors may affect delay times for vessel passage through locks. One important variable could be the age and maintenance status of individual locks. Currently, Montgomery Point built in 2004 on the McClellan-Kerr Arkansas River system is the youngest USACE owned lock (FactCard2016). In the first 12 years of operation 20,137 vessels passed through the Montgomery Point lock. In contrast, the oldest locks owned and operated by the Corps are the Willamette Falls locks on the Willamette River constructed in 1873 (FactCard2016). Although time since lock construction may directly affect vessel delay time, regular maintenance of lock parts could complicate calculations of the actual age of a given lock. 

Other factors influencing vessel delay time could include weather events, season, vessel traffic, or vessel features. The consequences of delays, or worse, failures of locks, are serious. Total waterborne commerce of the United States consists foreign and domestic commerce and totaled 2,292,042,500 short tons (2000 pounds) in 2016 (Waterborne Commerce National Summary, 2016). The highest volumes of national internal commodities (goods moving exclusively on waterways within the boundaries of the United States) transported on waterways in 2016 were petroleum, coal, crude materials, and food and farm goods. Projected economic impacts of single lock closures indicate steep decreases in aggregate economic activity and job losses depending on the specific lock, the length of closure and the main commodity type on affected waterways (Yu et al. 2016).

### Problem Statement:
Vessel traffic on US waterways and through USACE-managed locks plays an important role in the transport of foreign and domestic goods into and out of the US. Locks are owned and operated by the US Army Corps of Engineers, range in age from 144 to 14 years since construction, and are considered by many to have surpassed their life-expectancy. Delays and failures of locks have immediate financial implications for shipping companies and for the USACE. Using a 17 year dataset of vessel traffic through 193 locks on 38 rivers I will explore patterns and use supervised learning and regression to determine conditions that lead to delays in lockage at individual locks. I am specifically interested in modeling delays and generally focused on identifying cost-based consequences to trends in lock operations.

### Client profile:
The Institute for Water Resources (IWR) headquartered in Alexandria, VA is a Field Operating Activity of the United States Army Corps of Engineers (USACE) responsible for water resources planning and water management programs. One focused center of IWR is the Navigation and Civil Works Decision Support Center (NDC) and its Waterborne Commerce Statistical Center (WCSC) in New Orleans, LA. These entities focus on data collection organization for waterborne commerce, vessel characteristics, port facilities, dredging information, and information on navigation locks.

I am working directly with Steven Riley from the Navigation and Civil Works Decision Support Center. The NDC regularly analyzes commodity and transport data collected from lock operations. However, they have identified a need for a deeper analysis of the national network of waterway locks. Data exploration to find relationships relevant to budgeting, maintenance and scheduling would inform their planning for future operations. Specifically, NDC would like to know if increased lockage times indicate that a stoppage is imminent. Are their factors that could be used to predict delays and stoppages? 

### Data Sources
Data are tables from USACE Oracle Database with lock data from 2000 to 2017. Data with relevant identifiers and fields will include the following tables:

* BARGES: BARGE-TYPE, BARGE-WIDTH, BARGE-LENGTH (all are VARCHAR2)
BARGE-TRAFFIC: NUMBER-PROCESSED (number)
* FLOTILLAS: FLOT-LENGTH, FLOT-WIDTH, FLOT-DRAFT-FT, FLOT-DRAFT-IN, HAZARD-CODE (all are VARCHAR2)
* VESSELS: VESSEL-TYPE (VARCHAR2)
* TRAFFIC: SOL-DATE (start of lockage), ARRIVAL-DATE, END-OF-LOCKAGE, BOW-OVER-SILL, END-OF-ENTRY, START-OF-EXIT (all are date-time)
* STALL-STOPPAGE: BEG-STOP-DATE, END-STOP-DATE (both date-time), SCHEDULED, REASON-CODE (both VARCHAR2).

From these tables and fields I will construct Calculated Time Events with the same formulae used by NDC. Time events include: Approach Time, Chambering Time, Delay Time, Entry Time, Exit Time, Idle Time, and Processing Time.


### Capstone Project Outline:
1. Acquire database tables in CSV format from Steve Riley of NDC. 

   1. Connect tables
   2. Build formula to produce Calculated Time Events
   3. Clean data and check for missing values
   4. Create a short document (1-2 pages) in your Github describing the data wrangling steps that you undertook to clean your capstone project data set. What kind of cleaning steps did you perform? How did you deal with missing values, if any? Were there outliers, and how did you decide to handle them? This document will eventually become part of your milestone report.
2. Construct and develop data story for the lock delay data

   1. Count lockage events and other events related to lock usage
   2. Explore trends
   		1. On different waterways
   		2. For key locks
   		3. Seasonally
   3. Produce histograms to visualize patterns in data
   4. Compare related quantities (seasonally, on different waterways, etc.)
   5. Produce scatterplots, violin plots and other graphs to visualize patterns in the data
   6. Explore time-series.
   		1. Are there patterns that indicate lock wear and tear?
   		2. Are there vessel traffic patterns?
   		3. Are there changes in lock usage over time?
   		4. Do Calculate Time Events increase or decrease over time?
   	7. Having made these plots, what are some insights you get from them? Do you see any correlations? Is there a hypothesis you would like to investigate further? What other questions do they lead you to ask?
3. Implement inferential statistical analyses. Write a short report (1-2 pages) on the inferential statistics steps you performed and your findings. Check this report into your Github and submit a link to it. Eventually, this report can be incorporated into your Milestone report.
4. Submit Milestone Report.
	1. Define the problem
	2. Identify my client
	3. Describe my data set, and how I cleaned/wrangled it
	4. List other potential data sets I could use
	5. Explain my initial findings
5. Machine Learning with linear regression. Incorporate the following methods:

   1. Multiple linear regression to predict Delay Time for individual locks
   2. Classification to predict failure/not failure of individual locks
   3. Can I build a model that updates when given new data?
6. Write Final Report
	1. Summarize findings for USACE
	2. Build slide deck for presentations
	3. Present findings to Springboard Office Hours
	4. Present findings to USACE Institute of Water Resources.

   
### Deliverables:
My contact at USACE, Steven Riley, is very interested in the findings of my analysis. He views this as an opportunity for data mining and to explore the factors that influence lockage delays and stoppages. My Final Report for Springboard and written report to USACE will include information on the variables that are most influential. These factors will likely differ for individual locks as each lockage routine and equipment can affect timing. USACE will be able to use general findings about influential variables from my report to frame future analysis and potentially future lock maintenance schedules. If delay times are difficult to predict or if outcomes vary widely across locations the results will still be valuable to USACE.

Internal Use: Predictive models of lockage delays will be useful to USACE for internal planning and scheduling. My model potentially can be designed to update and re-tune when additional years of data are available. These models could be useful to USACE and NDC into the future.

External Use: USACE makes some of their lock data available to the public through the [LPMS Public Web](http://corpslocks.usace.army.mil/lpwb/f?p=121:1:7534946428825:::::). They also produce annual reports for related topics like Waterborne Container Traffic, Commodity Indicators, Ports and State Data, Domestic Waterborne Traffic by Commerce Type, etc. Results of my analysis may inform some of this publicly shared analysis of US waterways traffic and locks. Having outwardly visible products is an important part of the mission of USACE and IWR to "provide forward-looking analysis and research in developing planning methodologies to aid the Civil Works program" and to provide " (a) analysis of emerging water resources trends and issues, (b) state-of-the-art planning and hydrologic engineering methods, models and training, and (c) national data management of results-oriented program and project information across Civil Works business lines."

### Sources:
FactCard, 2016. The U.S. Waterway System 2016 Transportation Facts & Information. Navigation and Civil Works Decision Support Center, US Army Corps of Engineers. http://www.navigationdatacenter.us/factcard/FactCard2016.pdf

Waterborne Commerce National Summary, 2016. In Waterborne Commerce of the United States, Calendar year 2016, Part 5-National Summaries. Compiled under the supervision of the Institute of Water Resources, USACE, Alexandria, VA. http://www.navigationdatacenter.us/wcsc/pdf/wcusnatl16.pdf

Yu, T.E, B.C. English and R.J. Menard. Economic Impacts Analysis of Inland Waterway Disruption on the Transport of Corn and Soybeans. Staff Report #AE16-08. Department of Agricultural and Resource Economics, University of Tennessee. September 2016.