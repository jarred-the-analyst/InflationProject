# Analyzing the Soaring Cost of Living and Its Consequences for Americans.
For this project, I will analyze the impact of inflation on Americans using data sourced exclusively from reliable institutions such as Fred, the U.S. Bureau of Labor Statistics, and the U.S. Census this will be data from 1985 to 2021.
analyzing the data I will be looking at how inflation is effcting americans after I will be making recommendations to both the Federal Reserve and consumers on what they can do to enhance their future.
While the Consumer Price Index (CPI) is commonly used to measure inflation, relying solely on it may not provide a comprehensive understanding of inflation. The CPI calculates price 
changes by considering a fixed basket of diverse goods and services, with each item assigned a weight based on its relative importance in average consumer spending. However, relying solely on the CPI can be 
misleading when examining specific categories of inflation. For instance, if we want to analyze the inflation effects on higher education, relying solely on the CPI would overlook the significant difference between 
the CPI and the college inflation rate. Additionally, the CPI attempts to adjust for changes in the quality of goods and services, but determining the appropriate quality adjustments can be challenging and subjective. 
Therefore, incorporating different types of measurements can offer better insights into inflation dynamics.
 ## questions

 **1.** how is inflation effecting the majority of americans?<br>
 **2.**  are their major distinctions between the four top categories and the cpi?<br>
 **3.** how did inflation effect housing during the build up to the 2008 finacial crisis? what insight can we draw?<br>
 **4.** how is wages effected by inflation? is it keeping up with inflation?

###  my approach
To assess how inflation is impacting the majority of Americans, the first step is to analyze their primary expenses. By examining the most significant expenditure categories, we can gain insights into how the majority of Americans are affected. According to the U.S. Bureau of Labor Statistics, the top four expenditures for Americans are **housing, transportation, food, and healthcare.** To understand the specific impact on the majority of Americans, I will calculate the inflation rates for these four categories. Additionally, I will separate rent and homeownership costs, resulting in a total of five calculations. To evaluate inflation trends in the housing market, I will utilize the average median price for the home calculation. By conducting these analyses, we can gain a better understanding of how inflation influences the key expenses that significantly impact the majority of Americans.
  ```mysql
    select years,
    -- This code calculates the inflation rate by subtracting the previous price from the current price, dividing the result by the 
    -- previous price, and then multiplying it by 100. The formula can be rewritten as follows: inflation_rate = ((current_price - previous_price) / previous_price) * 100
    	cpi,
    	COALESCE((home_owner-
    	lag(home_owner) over () )/(lag(home_owner) over ()) *100.0000, 'unknown') as home_cpi,
        COALESCE((renter-
    	lag(renter) over () )/(lag(renter) over ())*100.0000 , 'unknown')  as renter_cpi,
    	COALESCE((Transportation-
    	lag(Transportation) over ()) /(lag(Transportation) over ())*100.0000, 'unknown') as Transportation_cpi,	
    	COALESCE(((Food-
    	lag(Food) over () )/(lag(Food) over ())*100.0000) , 'unknown') as Food_cpi,
    	COALESCE(((Healthcare-
    	lag(Healthcare) over () )/(lag(Healthcare) over ())*100.0000) , 'unknown') as healthcare_cpi
    from 
    	consumer_index;
```
### sample of the top four expenditures inflation rate
| year | cpi | home_cpi| renter|trasnportation_cpi|food_cpi|healthcare_cpi  	  
|--|--|--| --|--|--|--|    		
|2016 |1.3  |4.97076020	|  4.76723510|-4.77743880|	0.84682440| 6.21833250
|2017|2.1|1.02135560	|  3.72698130|	5.82384790	|7.75500120	|6.85169120
|2018|2.4|-1.501225500| 2.12675710|	1.93191310|	2.31492090|	0.81168830	
|2019|1.8|4.79004660 | 5.66004150	|10.05019970|	4.00985660|	4.52898550 
2020|1.3|	 17.86880370| 4.54264690|	-8.52727620	|6.28903720|	-0.30810710
2021|4.7|15.28582220| 5.36040390	|11.55098710|6.56534950|5.31195670


### what the numbers say
looking at the numbers inflation typically affects housing the most out of all these categories.  looking at some of these years the difference between the cpi and the indivudal inflation in these four categories are sometimes vastly different. to address the first two questions though,I will need to calculate the average inflation rate for each of the four categories. This will enable me to assess the overall inflation effects on Americans for these specific expenditure categories and identify any notable distinctions between them and the CPI.
```sql
-- measuring the avarage difference between the overall cpi and the four major categories inflation rate.
-- using the formula:  inflation_rate = ((current_price/ previous_price) *100) - 100 to caculate the inflation 
with four_cat_cpi as (
select years,
	cpi,
	((home_owner/lag(home_owner) over () )*100.0000) -100 as home_cpi,
     
	((renter/lag(renter) over () )*100.0000) -100 as renter_cpi,
	
    ((Transportation/lag(Transportation) over () )*100.0000) -100 as Transportation_cpi,	
	
    ((Food/lag(Food) over () )*100.0000) -100 as Food_cpi,
    
    ((Wages_salaries/lag(Wages_salaries) over () )*100.0000) -100 as Wages_salaries_cpi,
	
    ((Healthcare/lag(Healthcare) over () )*100.0000) -100 as healthcare_cpi
from 
	consumer_index)

select 
-- finding the avg cpi and avg of the four different categories
	concat(round(avg(cpi),2),'%') avg_inflation,
    concat(round(avg(Wages_salaries_cpi),2),'%') Wages_salaries_cpi,
	concat(round(avg(home_cpi),2),'%') home_cpi,
    concat(round(avg(renter_cpi),2),'%')renter_cpi,
    concat(round(avg(Transportation_cpi),2 ),'%')Transportation_cpi,
   concat(round(avg(Food_cpi),2),'%') Food_cpi,
    concat(round(avg(healthcare_cpi),2 ),'%')healthcare_cpi
from
	four_cat_cpi;
```
### average inflation across categories from 1985 to 2021
| cpi|wages|home|rent|trans|food|healthcare
|--|--|--|--|--|--|--|
| 2.67%|3.08%|4.80%|3.83%|2.68%|2.74%|4.62%

## Exploring the Impact of Inflation on Americans: Housing, Wages, and Economic Disparities 

#### 1. how is inflation effecting the majority of americans?
 housing price increses faster than the cpi and wages with this being americans number one expense means that many americans feels alot of inflationary pressure towards housing and rent. this can be said the same with health care which is another major expense. on the other hand even though trasportation and food are rising faster than the cpi
it is not increasing faster than wages making it less of a worry for the avarage american.

#### 2. are their major distinctions between the four top categories and the cpi?
out of the four categories housing and healthcare were the two catagories that were significantly higher than the cpi. transportation and food was higher than the cpi also but not by much. so, expense toward homeowners and renters typically goes up more than the cpi. 

## housing inflation during the bulid up of 2008 finacial crisis
```sql
-- using two cte to have the data next to each other so that its side by side
with y_96 as (
select
	years,
    cpi, 
	 home_owner as h_96_08,
-- the column that their going to be joined on 
     row_number() over () as y_order
from 
	consumer_index
where years between 1995 and 2008
),
y_09 as 
	(select
	years,
    cpi, 
	home_owner as h_09_21,
     row_number() over () as y_order
from 
	consumer_index
where years between 2008 and 2021)
select 
	y_96.years, y_96.cpi,
	round(((h_96_08/lag(h_96_08) over () )*100.0000) -100,4) as cpi_h_96,
    
    y_09.years, y_09.cpi,
    round(((h_09_21/lag(h_09_21) over () )*100.0000) -100,4)as cpi_h_09
from 
	y_96  join y_09 on y_96.y_order=y_09.y_order;
```
### what the numbers show
| year|cpi from 96_08|housing prices_96_08|year|cpi from 09_21|housing prices_09_21
|---|---|---|---|---|---|
1996|2.9|4.2857|2009|-0.3|2.3535|
1997|2.3|4.4521|2010|1.6|2.4346|
1998|1.5|5.5738|2011|3.1|7.9225|
1999|	2.2|4.9689|	2012|2.1|						9.6656
2000|	3.4	|3.6686|2013|1.5|						7.2890
2001|	2.8	|7.0776|2014|1.6|						1.9757
2002|	1.6	|3.9446|2015|0.1|						4.6227
2003|	2.3	|13.3333|2016|1.3|						4.9708
2004|	2.7	|9.0045	|2017	|2.1|						1.0214
2005|	3.4	|2.3246	|2018	|2.4|						-1.5012
2006|	3.2	|0.5680	|2019	|1.8|						4.7900
2007|	2.9	|-6.3735|2020	|1.3|						17.8688
2008|	3.8	|-6.6351|2021	|4.7|						15.2858

