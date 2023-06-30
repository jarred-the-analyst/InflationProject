# Analyzing the Soaring Cost of Living and Its Consequences for Americans.
For this project, I will analyze the impact of inflation on Americans using data sourced exclusively from reliable institutions such as Fred, the U.S. Bureau of Labor Statistics, and the U.S. Census.
analyzing the data I will be looking at how inflation is effcting americans after I will be making recommendations to both the Federal Reserve and consumers on what they can do to enhance their future.
While the Consumer Price Index (CPI) is commonly used to measure inflation, relying solely on it may not provide a comprehensive understanding of inflation. The CPI calculates price 
changes by considering a fixed basket of diverse goods and services, with each item assigned a weight based on its relative importance in average consumer spending. However, relying solely on the CPI can be 
misleading when examining specific categories of inflation. For instance, if we want to analyze the inflation effects on higher education, relying solely on the CPI would overlook the significant difference between 
the CPI and the college inflation rate. Additionally, the CPI attempts to adjust for changes in the quality of goods and services, but determining the appropriate quality adjustments can be challenging and subjective. 
Therefore, incorporating different types of measurements can offer better insights into inflation dynamics.
###  1. how is inflation effecting the majority of americans? 
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
