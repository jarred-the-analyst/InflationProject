# Analyzing the Soaring Cost of Living and Its Consequences for Americans.
For this project, I will analyze the impact of inflation on Americans using data sourced exclusively from reliable institutions such as Fred, the U.S. Bureau of Labor Statistics, and the U.S. Census this will be data from 1985 to 2021.
analyzing the data I will be looking at how inflation is affecting the most important areas of the economy after I will be making recommendations to both the Federal Reserve and consumers on what they can do to combat inflation.
While the Consumer Price Index (CPI) is commonly used to measure inflation, relying solely on it may not provide a comprehensive understanding of inflation. The CPI calculates price 
changes by considering a fixed basket of diverse goods and services, with each item assigned a weight based on its relative importance in average consumer spending. However, relying solely on the CPI can be 
misleading when examining specific categories of inflation. For instance, if we want to analyze the inflation effects on higher education, relying solely on the CPI would overlook the significant difference between 
the CPI and the college inflation rate. Additionally, the CPI attempts to adjust for changes in the quality of goods and services, but determining the appropriate quality adjustments can be challenging and subjective. 
Therefore, incorporating different types of measurements can offer better insights into inflation dynamics.<be>

![enter image description here](https://www.midasgoldgroup.com/wp-content/uploads/2020/06/fed-printing-money.jpg)
 ## questions

 **1.** what is the year-over-year change in inflation between the top four categories?<br>
 **2.** Is there a significant disparity between the Consumer Price Index (CPI) and other categories inflation rate such as home prices, health prices, and wages?<br>
 **3.** How did inflation affect housing during the build-up to the 2008 financial crisis?<br>
 **4.** How are wages affected by inflation? Is it keeping up with inflation?

###  my approach
To assess how inflation is impacting aspects of the economy, the first step is to analyze Americans primary expenses. By examining the most significant expenditures, we can find the most important categories to analyze. According to the U.S. Bureau of Labor Statistics, the top four expenditures for Americans are **housing, transportation, food, and healthcare.** To understand the specific impact I will calculate the inflation rates for these four categories. Additionally, I will separate rent and homeownership costs, resulting in a total of five calculations. To evaluate inflation trends in the housing market, I will utilize the median prices for  homes. By conducting these analyses, we can gain a better understanding of how inflation influences the key expenses that significantly impact the majority of Americans.
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

<div class='tableauPlaceholder' id='viz1689255440362' style='position: relative'><noscript><a href='#'><img alt='Dashboard 1 ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;in&#47;inflationproject&#47;Dashboard1&#47;1_rss.png' style='border: none' /></a></noscript>

### sample of the top four expenditures inflation rate
| year | cpi | home_cpi| renter|trasnportation_cpi|food_cpi|healthcare_cpi  	  
|--|--|--| --|--|--|--|    		
|2016 |1.3  |4.97076020	|  4.76723510|-4.77743880|	0.84682440| 6.21833250
|2017|2.1|1.02135560	|  3.72698130|	5.82384790	|7.75500120	|6.85169120
|2018|2.4|-1.501225500| 2.12675710|	1.93191310|	2.31492090|	0.81168830	
|2019|1.8|4.79004660 | 5.66004150	|10.05019970|	4.00985660|	4.52898550 
2020|1.3|	 17.86880370| 4.54264690|	-8.52727620	|6.28903720|	-0.30810710
2021|4.7|15.28582220| 5.36040390	|11.55098710|6.56534950|5.31195670


#### 1. what is the year-over-year change in inflation between the top four categories?
Housing prices are increasing at a faster rate than the Consumer Price Index (CPI), and considering that housing is the primary expense for many Americans, it puts significant inflationary pressure on housing and rent costs. This can also be observed in healthcare, which is another major expense. However, transportation and food costs, although rising faster than the CPI, are not increasing at a rate higher than wages, making them less of a concern for the average American.
looking at the numbers inflation typically affects housing the most out of all these categories.  looking at some of these years the difference between the cpi and the individual inflation in these four categories are sometimes vastly different. to address the next  question, I will need to calculate the average inflation rate for each of the four categories. This will enable me to assess the overall inflation effects on Americans for these specific expenditure categories and identify any notable distinctions between them and the CPI.


```sql
-- measuring the average difference between the overall cpi and the four major categories inflation rate.
-- using the formula:  inflation_rate = ((current_price/ previous_price) *100) - 100 to calculate the inflation 
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

#### 2. Is there a significant disparity between the Consumer Price Index (CPI) and other categories inflation rate such as home prices, health prices, and wages?
Among the four categories, housing and healthcare have inflation rates that are significantly higher than the CPI. Transportation and food costs also surpass the CPI, but the difference is not as significant. Therefore, expenses related to homeownership and renting tend to increase more rapidly compared to the CPI. 


## housing inflation during the build up of 2008 financial crisis
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

#### 3. how did inflation affect housing during the build-up to the 2008 financial crisis? 
Upon examining the data for the first 12 years (1996-2008), it is evident that the inflation rate for homes experienced a significant increase. The peak occurred in 2003, 
with a 13.3% inflation rate for homes, while the peak CPI during that period was 3.8% in 2008. This example highlights the discrepancy between CPI numbers and the actual inflation 
experienced by Americans. In 2003, while the CPI was 2.3%, housing prices rose by 13.2%.  this discrepancy usually manifests with the FED pointing to an average
cpi number for the year while Americans feel the wrath of their number one expenditure housing increase by more than the cpi and wages.
It is important to note that while high increases in home prices may benefit certain individuals, such as the wealthy or those in the real estate business, they often harm the poor and contribute to wealth inequality. Understanding this, it is now crucial to analyze how wages are affected by inflation, as it directly influences the purchasing power and people's financial well-being 
![enter image description here](https://www.whitesidesca.com/wp-content/uploads/2016/10/shutterstock_279344075-wages-files-e1478791568392.jpg)


## wages and inflation
```sql
 -- calculating the average cpi minus the average wage increase
with tmp as 
 (
 select 
	(Wages_salaries-
	lag(Wages_salaries) over () )/(lag(Wages_salaries) over ())*100.0000 as w
    ,(cpi)
from 
	consumer_index
    )
 
select concat(round(avg(w)-avg(cpi),2) ,'%') diff_cpi_wage
from tmp ;
```
### average cpi vs  average wage inflation 
| cpi | wage | diff
|--|--|--|
| 2.67%|3.08%| .4%
#### 4. how Is wages affected by inflation? Is it keeping up with inflation?
wages only have increased .4% more than inflation. this has been making it increasingly more harder for the lower class to keep up with their housing expenses and healthcare cost which increase far above the inflation rate.


##  correlation between m2 and prices
```sql
-- analyzing the m2 money supply finding the rate of change and the distribution of the change.
select
	ms.year,
    ms.m2,
    ms.m2-ms2.m2 rate_change,
    100*((ms.m2-ms2.m2)/(sum(ms.m2-ms2.m2) over ())) as precentage_o_ratechange
from money_supply as ms join money_supply as ms2 on ms.year=ms2.year + interval 1 year
```
### Exponential increase of the money supply
 year|m2|rate_change|percentage_change
|--|--|--|--|
1986-01-01|		2613|	196|			1.0823
1987-01-01|		2782|	169|			0.9332
1988-01-01|		2931|	149	|		0.8228
1989-01-01|		3054|	123	|		0.6792
1990-01-01|		3222|	168	|		0.9277
1991-01-01|		3342|	120	|		0.6627
1992-01-01|		3405|	63	|		0.3479
1993-01-01|		3440|	35	|		0.1933
1994-01-01|		3483|	43	|		0.2375
1995-01-01|		3556|	73	|		0.4031
1996-01-01|		3729|	173	|		0.9553
1997-01-01|		3926|	197	|		1.0879
1998-01-01|		4207|	281	|		1.5517
1999-01-01|		4517|	310	|		1.7119
2000-01-01|		4790|	273	|		1.5075
2001-01-01|		5204|	414	|		2.2862
2002-01-01|		5591|	387	|		2.1371
2003-01-01|		5981|	390	|		2.1536
2004-01-01|		6267|	286	|		1.5793
2005-01-01|		6535|	268	|		1.4799
2006-01-01|		6877|	342	|		1.8886
2007-01-01|		7298|	421	|		2.3248
2008-01-01|		7791|	493	|		2.7224
2009-01-01|		8416|	625	|		3.4513
2010-01-01|		8626|	210	|		1.1596
2011-01-01|		9256|	630	|		3.4789
2012-01-01|		10050|	794	|		4.3846
2013-01-01|		10727|	677	|		3.7385
2014-01-01|		11389|	662	|		3.6556
2015-01-01|		12045|	656	|		3.6225
2016-01-01|		12860|	815	|		4.5005
2017-01-01|		13591|	731	|		4.0367
2018-01-01|		14104|	513	|		2.8328
2019-01-01|		14818|	714	|		3.9428
2020-01-01|		17651|	2833 |		15.6442
2021-01-01|		20526|	2875 |		15.8761

#### money supply the exponential function
first, M2 money supply refers to a measure of money that includes physical currency (coins and banknotes) in circulation, 
demand deposits (such as checking accounts), and certain types of savings deposits and money market securities.  it includes components that are easily convertible into cash or used for making payments. simply put it's how much money is in circulation and we can see that it has seen tremendous growth in tandem with the increase in inflation. this explains why in 1984 median housing prices were 84,000 and today it is now 457,000. One interesting insight is that 2020 and 2021 are responsible for 31.5% of the change in the money supply between 1984 and now.

![](https://www.varchev.com/wp-content/uploads/2015/10/FED.jpg)

# conclusion
In conclusion, housing stands as the primary expenditure for Americans, and its costs are escalating at a significantly faster pace than 
both inflation and wages. To address this issue, the Federal Reserve should implement less inflationary monetary policies, such as reducing 
quantitative easing (QE) measures. While QE was necessary during the unpredictability of the COVID-19 pandemic, the excessive injection of money 
into the economy led to a substantial increase in median home prices by 17.8% in 2020 and 15.2% in 2021. The problem is worsened by wages staying the same while the costs of three out of the top five expenses are increasing faster. This makes it harder for people who are poor or in the middle class. To tackle this issue, one suggestion is for the government to provide subsidies to companies that pay their employees higher wages. This would encourage companies to pay better wages and help improve the financial situation of workers. In general, inflation is a big concern for most Americans and needs the full attention of both the Federal Reserve and the government to keep it under control. It's important to take necessary actions to make housing more affordable, address the lack of wage growth, and implement policies that promote economic stability and fairness.<br>

Additionally, consumers can now have a better understanding of what parts of their budget they need to plan for the long run. knowing on average housing prices and healthcare have been significantly increasing faster than wages I would advise people to find jobs that have great healthcare benefits so that, that expense would be less of a burden. also furthermore, I recommend individuals consider options such as having roommates or living with relatives instead of prematurely moving out on their own. Sharing living expenses can help mitigate the financial strain caused by soaring housing costs. 
By considering this, individuals can better manage their budget and navigate the challenges posed by rising housing and healthcare expenses relative to wages.
