# TitanicProject-MSSQL-PowerBi
### DATASET     
     https://www.kaggle.com/datasets/vinicius150987/titanic3

# About Dataset
The sinking of the Titanic is one of the most infamous shipwrecks in history.
On April 15, 1912, during her maiden voyage, the widely considered “unsinkable” RMS Titanic sank after colliding with an iceberg. Unfortunately, there weren’t enough lifeboats for everyone onboard, resulting in the death of 1502 out of 2224 passengers and crew.

This work is describing how much can I get the information from Titanic dataset (this dataset contains 1309 rows, this is largest as I can found in the internet). I will try to show why some people stayed alive and will try to provide facts which are make affect on their surviving. Also I will show regularity.
I will process the data with help of SQL queries. My workbench will be MSSQL. My target is also to show how much SQL can help in this research.

In the beginning, I will join all tables to see the whole data. I have selected top 10 rows.
```
-- Joining all tables

SELECT TOP 10 PT1.*, TI2.fare, DT3.embarked, DT3.home_dest, CI4.pclass
FROM Titanik..Passengers_titanik PT1
JOIN Titanik..Ticket_info TI2
ON PT1.ticket = TI2.ticket
JOIN Titanik..Destination_titanik DT3
ON PT1.personal_id = DT3.ticket_id
JOIN Titanik..Cabine_info CI4
ON PT1.ticket = CI4.ticket
```
![1231233333](https://github.com/remunaration/TitanicProject-MSSQL-PowerBi/assets/160137989/e400158a-60ab-46d3-b362-38d54a432935)

 
Let`s start with the analysis of the percentage of those who survived.

## 1. Survival rate
```
-- Percentage of people who survived vs died

SELECT if_survived, COUNT(if_survived) AS survival_rate, CONCAT(CAST(CAST(COUNT(if_survived) AS DECIMAL (10,2))
/CAST((SELECT COUNT(if_survived) FROM Titanik..Passengers_titanik) AS DECIMAL (10,2))AS DECIMAL (10,2))*100, ' %') AS percentage,
(SELECT COUNT(if_survived) FROM Titanik..Passengers_titanik WHERE if_survived <> 'Unknown') AS total
FROM Titanik..Passengers_titanik
WHERE if_survived <> 'Unknown'
GROUP BY if_survived
ORDER BY survival_rate;
```
 
![2222](https://github.com/MatthewBluebird/TitanicProject-MSSQL-PowerBi/assets/160137989/10012fe2-159d-4dea-836b-6a1be458a7f1)

As we can see from 1309 passengers survived only 500 of them it's 38% percent.
Let's deep into the data and try to understand who survived.

I will analyze the difference between gender and then age.

## 2. Gender
```
-- Survived(The difference between gender)
   
WITH total_table1 AS
(
SELECT gender, COUNT(personal_id) AS total_passengers,
CONCAT(CAST(CAST(COUNT(personal_id)AS DECIMAL (10,2))/(SELECT COUNT(personal_id) FROM Titanik..Passengers_titanik) *100 AS DECIMAL(10,2)), ' %') AS gender_rate
FROM Titanik..Passengers_titanik
WHERE gender IS NOT NULL
GROUP BY gender
), total_table2 AS
(
SELECT gender,COUNT(if_survived) AS count_suevived
FROM Titanik..Passengers_titanik
WHERE if_survived = 'Yes'
GROUP BY gender
)
SELECT tab1.gender , tab2.count_suevived, tab1.total_passengers, tab1.gender_rate, 
CONCAT(CAST((CAST(tab2.count_suevived AS DECIMAL(10,2))/tab1.total_passengers)*100 AS DECIMAL(10,2)), ' %') AS survival_rate
FROM total_table1 tab1
LEFT JOIN total_table2 tab2
ON tab1.gender = tab2.gender
```
 
![333333](https://github.com/MatthewBluebird/TitanicProject-MSSQL-PowerBi/assets/160137989/dd7bc1d2-0728-41a6-8d8b-0e4c851fea64)

On board of the Titanic were 1317 passengers and 908 crew members, a total amount were 2225 people.
According to Great Britain law on the ship should have been 20 boats, which could handle 1178 passengers.
Captain understand that this not enough for all members, so he gave order to save females and children first.

As we can see on the analyze females have more survival rate than males, 72% vs 19%.
According to this, females were in twice less than males, 35% vs 64%.
So, we can be sure that the main sign of survival was gender.

Let`s check the age variations, to make sure that the children were saved first.

## 3. Age
```
-- Survived(The difference between age_variations)

WITH total_table3 AS
(
SELECT gender, variations, COUNT(if_survived) AS count_survived
FROM Titanik..Passengers_titanik
WHERE gender IS NOT NULL AND if_survived = 'Yes'
GROUP BY gender, variations
), total_table4 AS
(
SELECT gender, variations, COUNT(personal_id) AS count_total
FROM Titanik..Passengers_titanik
WHERE gender IS NOT NULL
GROUP BY gender, variations
)
SELECT tab3.gender, tab3.variations, tab3.count_survived, tab4.count_total,
CONCAT(CAST((CAST(tab3.count_survived AS DECIMAL(10,2))/tab4.count_total)*100 AS DECIMAL(10,2)), ' %') AS percent_survived
FROM total_table3 tab3
RIGHT JOIN total_table4 tab4
ON tab3.variations = tab4.variations AND tab3.gender = tab4.gender
ORDER BY gender, variations
```
 ![44444](https://github.com/MatthewBluebird/TitanicProject-MSSQL-PowerBi/assets/160137989/35762b57-46e2-4808-8b2d-59d408088c22)

Earlier I made the variations by age cause the range was huge, so only 5 variations.
Splitting the age through the groups we can see that all females have more survived rate in any age category than males.
The highest survival rate had the males in category “0-20” than others, so the children were prioritized.

Also, we can analyze what is the survival rate by the title name of people.
Let`s filter by the title name and find how many people have survived.

## 4. Title
```
--Survived(The difference between title name)

WITH total_table5 AS
(
SELECT title, COUNT(CASE WHEN if_survived = 'Yes' THEN 1 ELSE NULL END) AS count_survived
FROM Titanik..Passengers_titanik
WHERE gender IS NOT NULL AND title <> ' Martin (Elizabeth L'
GROUP BY title
),total_table6 AS
(
SELECT title, COUNT(personal_id) AS count_total
FROM Titanik..Passengers_titanik
WHERE gender IS NOT NULL AND title <> ' Martin (Elizabeth L'
GROUP BY title
)
SELECT tab6.title, tab5.count_survived, tab6.count_total, CONCAT(CAST((CAST(tab5.count_survived AS DECIMAL(10,2))/tab6.count_total)*100 AS DECIMAL(10,2)), ' %') AS percent_survived
FROM total_table5 tab5
RIGHT JOIN total_table6 tab6
ON tab5.title = tab6.title
```
```
--Another variant of the previous query

SELECT title, COUNT(CASE WHEN if_survived = 'Yes' THEN 1 ELSE NULL END) AS count_survived ,COUNT(personal_id) AS count_total,
CONCAT(CAST((CAST(COUNT(CASE WHEN if_survived = 'Yes' THEN 1 ELSE NULL END) AS DECIMAL(10,2))/COUNT(personal_id))*100 AS DECIMAL(10,2)), ' %') AS percent_survived
FROM Titanik..Passengers_titanik
WHERE gender IS NOT NULL AND title <> ' Martin (Elizabeth L'
GROUP BY title
```
![55555](https://github.com/MatthewBluebird/TitanicProject-MSSQL-PowerBi/assets/160137989/1b0abea7-c060-4367-a3f2-0f39eb51a56d)

In total we have 18 title names, and as we can see a huge amount is situated in title names ‘Master’(children), ‘Mr’(men), ‘Mrs’(not married woman), ‘Miss’(married woman).
So, we can unite all data by this 4 main title names.
```
--Grouping title names

WITH table_tem1 AS
(
SELECT
CASE
	WHEN TRIM(title) IN ('Dr', 'Rev', 'Col', 'Major', 'Jonkheer', 'Sir', 'Don', 'Capt') AND gender = 'Male' THEN 'Mr'
	WHEN TRIM(title) IN ('Mlle', 'Ms') AND gender = 'Female' THEN 'Miss'
	WHEN TRIM(title) IN ('Dr', 'the Countess', 'Mme', 'Lady', 'Dona','Martin (Elizabeth L') AND gender = 'Female' THEN 'Mrs'
	ELSE TRIM(title)
END AS grouping_title_names,
(CASE WHEN if_survived = 'Yes' THEN 1 ELSE NULL END) AS count_survived, personal_id
FROM Titanik..Passengers_titanik
WHERE title IS NOT NULL
)
SELECT grouping_title_names, COUNT(count_survived) AS count_survived, COUNT(personal_id) AS count_total, 
CONCAT(CAST((CAST(COUNT(count_survived)AS DECIMAL(10,2))/COUNT(personal_id))*100 AS DECIMAL(10,2)), ' %') AS percent_survived
FROM table_tem1
GROUP BY grouping_title_names
HAVING COUNT(count_survived) <> 0
```
 ![66666](https://github.com/MatthewBluebird/TitanicProject-MSSQL-PowerBi/assets/160137989/deca7e8d-cf71-414f-861f-4cd122c8276d)

As we can see females(79% and 67%) and children(50%) have the highest survival rate.
So, the decision to save females and children was prioritized.


The next analyze we can make though the cabin class and boarding place.
The Titanic`s cabins were divided into three classes
1.	First class was closest to the deck of Titanic
2.	Second class was in the middle part of Titanic
3.	Third class was closest to the bottom of the ship

## 5. Cabin class
```
--Survived(The difference between cabin class)

SELECT CI2.pclass, COUNT(CASE WHEN PT1.if_survived = 'Yes' THEN 1 ELSE NULL END) AS count_survived, COUNT(PT1.personal_id) AS count_total, 
CONCAT(CAST((CAST(COUNT(CASE WHEN PT1.if_survived = 'Yes' THEN 1 ELSE NULL END) AS DECIMAL(10,2))/COUNT(PT1.personal_id)*100)AS DECIMAL(10,2)), ' %') AS percent_survived
FROM Titanik..Passengers_titanik PT1
JOIN Titanik..Cabine_info CI2
ON PT1.ticket = CI2.ticket
GROUP BY CI2.pclass
ORDER BY CI2.pclass
```
 ![777777](https://github.com/MatthewBluebird/TitanicProject-MSSQL-PowerBi/assets/160137989/3409a477-e0ac-40a1-9d04-f6e471c748a6)

It is possible to see a trend of decreasing survival rate with decrease in the level of cabins 61% for first class, 42% for second class, 25% for third class and the overwhelming majority of passengers were in third class cabins.
Now let`s analyze how many males and females were in these classes.
```
--Survived(The difference between cabin class and gender)

SELECT CI2.pclass, COUNT(PT1.personal_id) AS count_total,
COUNT(CASE WHEN PT1.gender = 'Male' THEN 1 ELSE NULL END) AS male_count_total, COUNT(CASE WHEN PT1.gender = 'Female' THEN 1 ELSE NULL END) AS female_count_total,
CONCAT(CAST((CAST(COUNT(CASE WHEN PT1.gender = 'Male' AND PT1.if_survived = 'Yes' THEN 1 ELSE NULL END)AS DECIMAL(10,2))/COUNT(CASE WHEN PT1.gender = 'Male' THEN 1 ELSE NULL END))*100 AS DECIMAL(10,2)), ' %') AS male_survived_rate,
CONCAT(CAST((CAST(COUNT(CASE WHEN PT1.gender = 'Female' AND PT1.if_survived = 'Yes' THEN 1 ELSE NULL END)AS DECIMAL(10,2))/COUNT(CASE WHEN PT1.gender = 'Female' THEN 1 ELSE NULL END))*100 AS DECIMAL(10,2)), ' %') AS female_survived_rate,
COUNT(CASE WHEN PT1.gender = 'Male'AND PT1.if_survived = 'Yes' THEN 1 ELSE NULL END) AS male_count_survived, COUNT(CASE WHEN PT1.gender = 'Female' AND PT1.if_survived = 'Yes' THEN 1 ELSE NULL END) AS female_count_survived,
COUNT(CASE WHEN PT1.if_survived = 'Yes' THEN 1 ELSE NULL END) AS count_survived
FROM Titanik..Passengers_titanik PT1
JOIN Titanik..Cabine_info CI2
ON PT1.ticket = CI2.ticket
GROUP BY CI2.pclass
ORDER BY CI2.pclass
```
![88888](https://github.com/MatthewBluebird/TitanicProject-MSSQL-PowerBi/assets/160137989/f37a1eb3-00a9-4f74-928c-5a2ff8d5976f)


As we can see on the analyze females have more survival rate than males.

The last thing that I will analyze will be the survival by the amount of families. I wanna split all data by the variations like ‘alone’(solo passenger), ‘one_family’(X amount of passengers from same family), ‘mix_family’(X amount of passengers what have the same ticket number and the same cabin, so they were together)

## 6. Family type
```
--The difference between family type

SELECT  
CASE 
	WHEN f.family_count = 1 AND f.count_passengers = 1 THEN 'alone' 
	WHEN f.family_count = 1 AND f.count_passengers > 1 THEN 'one_falmily' 
	WHEN f.family_count > 1 AND f.count_passengers > 1 THEN 'mix_falmily' 
ELSE '0' 
END AS type_falmily, COUNT(f.ticket) AS count_ticket, SUM(count_passengers) AS total_passengers
FROM 
(
SELECT ticket, COUNT(DISTINCT(family)) AS family_count,
COUNT(personal_id) AS count_passengers
FROM Titanik..Passengers_titanik
GROUP BY ticket
HAVING COUNT(DISTINCT(family)) <> 0
)f
GROUP BY CASE 
	WHEN f.family_count = 1 AND f.count_passengers = 1 THEN 'alone' 
	WHEN f.family_count = 1 AND f.count_passengers > 1 THEN 'one_falmily' 
	WHEN f.family_count > 1 AND f.count_passengers > 1 THEN 'mix_falmily' 
ELSE '0' 
END
```
![99999](https://github.com/MatthewBluebird/TitanicProject-MSSQL-PowerBi/assets/160137989/19601051-3e12-4deb-9cae-44edb42c1e7d)


Here we can see how many passengers traveled alone, with family or have mix family.
Now I will these families by gender.
```
--Survived(The difference between family type by gender)

SELECT  
CASE 
	WHEN f.family_count = 1 AND f.count_passengers = 1 THEN 'alone' 
	WHEN f.family_count = 1 AND f.count_passengers > 1 THEN 'one_falmily' 
	WHEN f.family_count > 1 AND f.count_passengers > 1 THEN 'mix_falmily' 
ELSE '0' 
END AS type_falmily, COUNT(f.ticket) AS count_ticket, SUM(f.male_count) AS male_count_survived, 
SUM(f.female_count) AS female_count_survived, SUM(count_passengers) AS count_passengers_total , SUM(survival_count) AS survival_count,
CONCAT(CAST((CAST(SUM(f.male_count)AS DECIMAL(10,2))/SUM(survival_count))*100 AS DECIMAL (10,2)), ' %') AS survival_rate_male,
CONCAT(CAST((CAST(SUM(f.female_count)AS DECIMAL(10,2))/SUM(survival_count))*100 AS DECIMAL (10,2)), ' %') AS survival_rate_female
FROM 
(
SELECT ticket, COUNT(DISTINCT(family)) AS family_count, COUNT(CASE WHEN gender = 'Male' AND if_survived = 'Yes' THEN 1 ELSE NULL END) AS male_count,
COUNT(CASE WHEN gender = 'Female' AND if_survived = 'Yes' THEN 1 ELSE NULL END) AS female_count,
COUNT(personal_id) AS count_passengers , COUNT(CASE WHEN if_survived = 'Yes' THEN 1 ELSE NULL END) AS survival_count
FROM Titanik..Passengers_titanik
GROUP BY ticket
HAVING COUNT(DISTINCT(family)) <> 0
)f
GROUP BY CASE 
	WHEN f.family_count = 1 AND f.count_passengers = 1 THEN 'alone' 
	WHEN f.family_count = 1 AND f.count_passengers > 1 THEN 'one_falmily' 
	WHEN f.family_count > 1 AND f.count_passengers > 1 THEN 'mix_falmily' 
ELSE '0' 
END
```
![000000](https://github.com/MatthewBluebird/TitanicProject-MSSQL-PowerBi/assets/160137989/2b4554c0-a18f-4b45-a01d-22e7af7dffa4)


As we can see on the analyze females have more survival rate than males in all family types.
At the conclusion, the females and children have the most survival rate. First class have the most survival rate. 

### Hypothesis: The Captain gave order to save females and children first, confirmed.



