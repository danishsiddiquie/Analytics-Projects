```python
import pandas as pd
import sqlalchemy as sa
import json
from IPython.display import Image
```


```python
# You will need to include this cell and the subsequent cell in any notebook in which you connect to the database 
# server.  
# These first two lines of code setup the connection to the database server.

import pymysql
pymysql.install_as_MySQLdb()
# This line loads the sql magic, allowing an individual line (or cell) to be interpreted as SQL code.
%reload_ext sql
# This code lets you connect to the databases
%sql mysql://root:danish98@localhost/
%sql USE lahmansbaseballdb;
```

     * mysql://root:***@localhost/
    0 rows affected.





    []



The database we chose to work on was lahman2016, which provided information about baseball from 1903 up till 2016. The database consisted of multiple relational tables, such as Salaries, Schools, Appearances etc.. Since we were not as adept in the knowledge of baseball, but still liked the sport, we focused more on varibales such as Salaries, colleges the players attended, and the wins of the team, rather than on baseball playing statistics. Moreover, I believe our analysis would be more interesting to a larger population--who do not understand baseball as much--due to the familiarity with the variables we have chosen. 

Our first table we created was used to investigate:

1) Which College program sent the most players to the major league?

This could be used to see which colleges have really good baseball programs, because their players consistently move on to play in the major leauge, or which colleges have baseball programs that may not be as good. In order to execute this query we needed data from both the Schools and CollegePlaying tables. We used a subquery to count the number of students who played baseball in college for each college, and selected both the schoolID and the full name of the school. Again, due to the extensiveness of the Lahman database, there were over a thousand different schools in our result. Since we wanted to examine the schools that send the most players to the majors, sorted our results in descending order and used a limit of 25 to get only the top 25 schools.


```sql
%%sql

#ORIGINAL data set record count

SELECT Schools.schoolID,(Schools.name_full) AS College,
    COUNT(CollegePlaying.playerID) AS num_players
    FROM CollegePlaying
    INNER JOIN Schools USING (schoolID)
    GROUP BY CollegePlaying.schoolID
    ORDER BY num_players DESC
    LIMIT 25;
```

     * mysql://root:***@localhost/
    25 rows affected.





<table>
    <thead>
        <tr>
            <th>schoolID</th>
            <th>College</th>
            <th>num_players</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>texas</td>
            <td>University of Texas at Austin</td>
            <td>265</td>
        </tr>
        <tr>
            <td>usc</td>
            <td>University of Southern California</td>
            <td>250</td>
        </tr>
        <tr>
            <td>stanford</td>
            <td>Stanford University</td>
            <td>248</td>
        </tr>
        <tr>
            <td>arizonast</td>
            <td>Arizona State University</td>
            <td>236</td>
        </tr>
        <tr>
            <td>michigan</td>
            <td>University of Michigan</td>
            <td>191</td>
        </tr>
        <tr>
            <td>ucla</td>
            <td>University of California, Los Angeles</td>
            <td>180</td>
        </tr>
        <tr>
            <td>holycross</td>
            <td>College of the Holy Cross</td>
            <td>167</td>
        </tr>
        <tr>
            <td>california</td>
            <td>University of California, Berkeley</td>
            <td>162</td>
        </tr>
        <tr>
            <td>arizona</td>
            <td>University of Arizona</td>
            <td>161</td>
        </tr>
        <tr>
            <td>alabama</td>
            <td>University of Alabama</td>
            <td>155</td>
        </tr>
        <tr>
            <td>unc</td>
            <td>University of North Carolina at Chapel Hill</td>
            <td>154</td>
        </tr>
        <tr>
            <td>floridast</td>
            <td>Florida State University</td>
            <td>152</td>
        </tr>
        <tr>
            <td>lsu</td>
            <td>Louisiana State University</td>
            <td>149</td>
        </tr>
        <tr>
            <td>illinois</td>
            <td>University of Illinois at Urbana-Champaign</td>
            <td>141</td>
        </tr>
        <tr>
            <td>florida</td>
            <td>University of Florida</td>
            <td>138</td>
        </tr>
        <tr>
            <td>clemson</td>
            <td>Clemson University</td>
            <td>138</td>
        </tr>
        <tr>
            <td>gatech</td>
            <td>Georgia Institute of Technology</td>
            <td>137</td>
        </tr>
        <tr>
            <td>oklahoma</td>
            <td>University of Oklahoma</td>
            <td>135</td>
        </tr>
        <tr>
            <td>notredame</td>
            <td>University of Notre Dame</td>
            <td>134</td>
        </tr>
        <tr>
            <td>okstate</td>
            <td>Oklahoma State University</td>
            <td>132</td>
        </tr>
        <tr>
            <td>calstfull</td>
            <td>California State University Fullerton</td>
            <td>131</td>
        </tr>
        <tr>
            <td>texasam</td>
            <td>Texas A&amp;M University</td>
            <td>129</td>
        </tr>
        <tr>
            <td>auburn</td>
            <td>Auburn University</td>
            <td>122</td>
        </tr>
        <tr>
            <td>scarolina</td>
            <td>University of South Carolina</td>
            <td>119</td>
        </tr>
        <tr>
            <td>missst</td>
            <td>Mississippi State University</td>
            <td>118</td>
        </tr>
    </tbody>
</table>




```sql
%%sql
SELECT Schools.schoolID,(Schools.name_full) AS College,
    COUNT(CollegePlaying.playerID) AS num_players
    FROM CollegePlaying
    INNER JOIN Schools USING (schoolID)
    GROUP BY CollegePlaying.schoolID
    ORDER BY num_players DESC
    LIMIT 25;
```

     * mysql://root:***@localhost/
    25 rows affected.





<table>
    <thead>
        <tr>
            <th>schoolID</th>
            <th>College</th>
            <th>num_players</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>texas</td>
            <td>University of Texas at Austin</td>
            <td>265</td>
        </tr>
        <tr>
            <td>usc</td>
            <td>University of Southern California</td>
            <td>250</td>
        </tr>
        <tr>
            <td>stanford</td>
            <td>Stanford University</td>
            <td>248</td>
        </tr>
        <tr>
            <td>arizonast</td>
            <td>Arizona State University</td>
            <td>236</td>
        </tr>
        <tr>
            <td>michigan</td>
            <td>University of Michigan</td>
            <td>191</td>
        </tr>
        <tr>
            <td>ucla</td>
            <td>University of California, Los Angeles</td>
            <td>180</td>
        </tr>
        <tr>
            <td>holycross</td>
            <td>College of the Holy Cross</td>
            <td>167</td>
        </tr>
        <tr>
            <td>california</td>
            <td>University of California, Berkeley</td>
            <td>162</td>
        </tr>
        <tr>
            <td>arizona</td>
            <td>University of Arizona</td>
            <td>161</td>
        </tr>
        <tr>
            <td>alabama</td>
            <td>University of Alabama</td>
            <td>155</td>
        </tr>
        <tr>
            <td>unc</td>
            <td>University of North Carolina at Chapel Hill</td>
            <td>154</td>
        </tr>
        <tr>
            <td>floridast</td>
            <td>Florida State University</td>
            <td>152</td>
        </tr>
        <tr>
            <td>lsu</td>
            <td>Louisiana State University</td>
            <td>149</td>
        </tr>
        <tr>
            <td>illinois</td>
            <td>University of Illinois at Urbana-Champaign</td>
            <td>141</td>
        </tr>
        <tr>
            <td>florida</td>
            <td>University of Florida</td>
            <td>138</td>
        </tr>
        <tr>
            <td>clemson</td>
            <td>Clemson University</td>
            <td>138</td>
        </tr>
        <tr>
            <td>gatech</td>
            <td>Georgia Institute of Technology</td>
            <td>137</td>
        </tr>
        <tr>
            <td>oklahoma</td>
            <td>University of Oklahoma</td>
            <td>135</td>
        </tr>
        <tr>
            <td>notredame</td>
            <td>University of Notre Dame</td>
            <td>134</td>
        </tr>
        <tr>
            <td>okstate</td>
            <td>Oklahoma State University</td>
            <td>132</td>
        </tr>
        <tr>
            <td>calstfull</td>
            <td>California State University Fullerton</td>
            <td>131</td>
        </tr>
        <tr>
            <td>texasam</td>
            <td>Texas A&amp;M University</td>
            <td>129</td>
        </tr>
        <tr>
            <td>auburn</td>
            <td>Auburn University</td>
            <td>122</td>
        </tr>
        <tr>
            <td>scarolina</td>
            <td>University of South Carolina</td>
            <td>119</td>
        </tr>
        <tr>
            <td>missst</td>
            <td>Mississippi State University</td>
            <td>118</td>
        </tr>
    </tbody>
</table>




```python
Image(filename = 'college_players.png',width=1500,height=1500)
```




    
![png](output_6_0.png)
    



Though the table is self explantory, the graph provides a neat visualisation, comparing colleges based on the number of players it has sent to the major league. UT Austin, USC, Stanford, and Arizona state are significantly leading in numbers, whereas Missisipi state, South Carolina, and Auburn trail in the top 25. If the database had given the information about whether the college is D1, D2, or D3, it would have been interesting to compare how that affects the count of players going into the major league. 

Next, we looked at:

2) What are the changes in average salaries for each position of baseball throughout the years?

Knowing postions that get paid the most could provide us with some insight as to which positions are the most important. From the Lahman2016 database, we concluded that to answer this question we would need data from the Fielding and Salaries tables. We joined these two tables on the playerID column. We used the group by clause to group all of the different positions together and calculated the average salary for each positon for a given year. [Note that the salaries table only had data beginning from 1985, and not 1903]


```sql
%%sql
SELECT FieldingPost.POS, Salaries.yearID, AVG(Salaries.salary) AS Avg_Salary
    FROM FieldingPost  
    INNER JOIN Salaries 
    On FieldingPost.playerID= Salaries.playerID
    WHERE Salaries.yearID IN (1985,1990,1995,2000,2005,2010,2015)
    GROUP BY yearID, POS
    ORDER BY yearID
    limit 10;
```

     * mysql://root:***@localhost/
    10 rows affected.





<table>
    <thead>
        <tr>
            <th>POS</th>
            <th>yearID</th>
            <th>Avg_Salary</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1B</td>
            <td>1985</td>
            <td>640402.9174311927</td>
        </tr>
        <tr>
            <td>2B</td>
            <td>1985</td>
            <td>459998.05102040817</td>
        </tr>
        <tr>
            <td>3B</td>
            <td>1985</td>
            <td>765103.2887323943</td>
        </tr>
        <tr>
            <td>C</td>
            <td>1985</td>
            <td>662133.8303571428</td>
        </tr>
        <tr>
            <td>CF</td>
            <td>1985</td>
            <td>560218.3949579832</td>
        </tr>
        <tr>
            <td>LF</td>
            <td>1985</td>
            <td>672867.4551282051</td>
        </tr>
        <tr>
            <td>P</td>
            <td>1985</td>
            <td>525067.8783151326</td>
        </tr>
        <tr>
            <td>RF</td>
            <td>1985</td>
            <td>555100.1372549019</td>
        </tr>
        <tr>
            <td>SS</td>
            <td>1985</td>
            <td>580426.2021276596</td>
        </tr>
        <tr>
            <td>1B</td>
            <td>1990</td>
            <td>912434.0243902439</td>
        </tr>
    </tbody>
</table>




```python
Image(filename = 'AvgSalary_Pos.png',width=1500,height=1500)
```




    
![png](output_10_0.png)
    



To see the changes over time in salaries of players, we used seven different years as parameters for the user and compared with average salaries. The average salaries increased for every increase in 5 years. The average salaries for all baseball positions in 1985 were much smaller than average salaries in 2015. We believe the main reasons for this trend are inflation over the years and the greater amount of money spent on professional baseball in recent years. Looking at average salaries for different position, first base position has  the highest average salary by 2015. It's average salary increased from 640,403 in 1985 to 11,790,655 in 2015. The position with lowest average salary is the Pitcher position. Their average salary is 6,929,854 in 2015. Overall, the range of average salary have increased significantly with every additional 5 years. The average salary range from minimum of 525,600 to maximum 765,000 in 1985 and minimum of 6,929,854 to maximum 11,790,655 in 2015.

3) Which birth State and birth Country has the most number of Allstar baseball Players?

This is an interesting general question to ask about baseball as often times some places are known for the birthplace of great sports' stars. To answer this question and to produce a simple visual that shows the number of allstar players by the birth state and birth country, we retrieved a subset of table AllstarFull and table Master from Lahman2016 database. From Table AllstarFull we retrieved columns playerID and teamID of all stars players. Then we joined this table with table Master to retrieve columns birthState and birthCountry. We also joined FranchID from table TeamsFranchises to the previous table which gives an alternative view of looking at all star players by its Franchise teamname. This table can be used to plot a visual for count of all star players by the birth state and birth country or the teams they are from.


```sql
%%sql
SELECT  COUNT(AllstarFull.playerID) AS Count_of_Allstar, AllstarFull.teamID, birthState, birthcountry
    FROM AllstarFull
    INNER JOIN people
    USING (playerID)
    INNER JOIN Teams
    USING (yearID)
    INNER JOIN TeamsFranchises
    USING (FranchID)
    GROUP BY teamID, birthState, birthcountry
    limit 10;
```

     * mysql://root:***@localhost/
    10 rows affected.





<table>
    <thead>
        <tr>
            <th>Count_of_Allstar</th>
            <th>teamID</th>
            <th>birthState</th>
            <th>birthcountry</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1348</td>
            <td>NYA</td>
            <td>CA</td>
            <td>USA</td>
        </tr>
        <tr>
            <td>112</td>
            <td>BOS</td>
            <td>NC</td>
            <td>USA</td>
        </tr>
        <tr>
            <td>910</td>
            <td>NYA</td>
            <td>NY</td>
            <td>USA</td>
        </tr>
        <tr>
            <td>540</td>
            <td>DET</td>
            <td>MI</td>
            <td>USA</td>
        </tr>
        <tr>
            <td>448</td>
            <td>CHA</td>
            <td>PA</td>
            <td>USA</td>
        </tr>
        <tr>
            <td>48</td>
            <td>WS1</td>
            <td>CA</td>
            <td>USA</td>
        </tr>
        <tr>
            <td>64</td>
            <td>WS1</td>
            <td>TN</td>
            <td>USA</td>
        </tr>
        <tr>
            <td>48</td>
            <td>CHA</td>
            <td>WI</td>
            <td>USA</td>
        </tr>
        <tr>
            <td>188</td>
            <td>NYA</td>
            <td>MD</td>
            <td>USA</td>
        </tr>
        <tr>
            <td>276</td>
            <td>CLE</td>
            <td>WA</td>
            <td>USA</td>
        </tr>
    </tbody>
</table>




```python
Image(filename = 'birthplace_AllStar.jpeg',width=1500,height=1500)
```




    
![jpeg](output_14_0.jpg)
    



The above treemap shows birth State and birth Country of all the allstar baseball players in major baseball league in between 1933 to 2016. The size of each birth state and the country of that state represents the number of all players that were born in each of these states. Thus, US, California has the largest number of all star baseball players (19,348) born in that state. Internationally, most all star base ball players were born in US followed by  Puerto Rico and Dominican Republic.

Our final query was the most complicated and asked the question:

4) Is there a correlation between the average salaries offered by a team, and their respective wins?

To create this query, we first grouped the Salaries table by year and team to get the Avg Salaries of each team ID. We then joined that table with the grouped Teams table to get each team's franchise ID. After we had the franchise ID, we joined the table with TeamsFranchises to get the repsective names of teams from their franchise IDs. Finally, we again grouped the entire query by yearID and franchID.

I believe this analysis would be quite useful for stakeholders concerned with the expenditure of teams on the players' wages, and if it is actually helping the overall outcome. It would provide a good comparison for them to compare their team's salary to win ratio to another team in the league, and then take appropriate measures if required. 


```sql
%%sql
with a as
(Select		yearID, 
			teamID,
			round(avg(salary),2) as Avgsalary
from		salaries
group by 	yearID,teamID),
b as
(SELECT Teams.teamID,Teams.franchID,Teams.W AS Wins FROM Teams),
c as
(SELECT TeamsFranchises.franchID AS franchID,TeamsFranchises.FranchName AS FranchName FROM 
        TeamsFranchises)
select 	distinct a.yearID,
		a.teamID,
        b.franchID,
        c.FranchName,
        Wins,
        Avgsalary
from	a
inner join b
on		a.teamID = b.teamID
left join c
on		b.franchID = c.franchID
limit 25;
```

     * mysql://root:***@localhost/
    25 rows affected.





<table>
    <thead>
        <tr>
            <th>yearID</th>
            <th>teamID</th>
            <th>franchID</th>
            <th>FranchName</th>
            <th>Wins</th>
            <th>Avgsalary</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1985</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>577405.32</td>
        </tr>
        <tr>
            <td>1986</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>555102.1</td>
        </tr>
        <tr>
            <td>1987</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>550307.65</td>
        </tr>
        <tr>
            <td>1988</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>524767.92</td>
        </tr>
        <tr>
            <td>1989</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>444500.0</td>
        </tr>
        <tr>
            <td>1990</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>439483.87</td>
        </tr>
        <tr>
            <td>1991</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>927026.68</td>
        </tr>
        <tr>
            <td>1992</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>1065345.93</td>
        </tr>
        <tr>
            <td>1993</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>1230833.31</td>
        </tr>
        <tr>
            <td>1994</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>1170559.13</td>
        </tr>
        <tr>
            <td>1995</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>922057.31</td>
        </tr>
        <tr>
            <td>1996</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>1002454.55</td>
        </tr>
        <tr>
            <td>1997</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>1317354.16</td>
        </tr>
        <tr>
            <td>1998</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>1639935.48</td>
        </tr>
        <tr>
            <td>1999</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>1684945.95</td>
        </tr>
        <tr>
            <td>2000</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>2017977.77</td>
        </tr>
        <tr>
            <td>2001</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>2396882.7</td>
        </tr>
        <tr>
            <td>2002</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>2703244.04</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>2852440.46</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>3122758.62</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>3108319.04</td>
        </tr>
        <tr>
            <td>2006</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>3372303.54</td>
        </tr>
        <tr>
            <td>2007</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>3691493.78</td>
        </tr>
        <tr>
            <td>2008</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>4383179.0</td>
        </tr>
        <tr>
            <td>2009</td>
            <td>CHN</td>
            <td>CHC</td>
            <td>Chicago Cubs</td>
            <td>52</td>
            <td>5392360.0</td>
        </tr>
    </tbody>
</table>




```python
Image(filename = 'SalaryWins.png',width=1500,height=1500)
```




    
![png](output_18_0.png)
    



As we can see from the scatter plot, the Arizona Diamondbacks did tremendously well in 2016, despite paying a smaller average salary to players. On the other hand, the Detroit tigers did very poorly despite paying large amounts of salaries to players. From the spread of the graph, we can see that there is no correlation between Salaries and Wins, and hence paying higher average salaries to players does not guarantee more wins, as one would think. Moreover, I would infer that higher salaries are usually given to strong players in the game. Thus, this possibly also suggests that having the best players in the team does not guarantee wins. However, it would be more interesting to see if there would be any correlation between the rating of the players and the wins.

To sum up, this project uses SQL queries to retrieve specific tables from the database Lehman2016 (baseball database) and perform operations to the columns in the tables in such a way that the table contains all the information necessary to answer the specific questions about baseball game. All the visual plots were created in Tableau and have been imported to this notebook. Overall, this project contains four different analysis. It shows number of baseball players sent to the major league by different colleges. It shows the changes in average salaries for each position of baseball throughout the years. The birth state and birth country of all star baseball players ranked by the number of all star players. Finally, the correlation between the average salaries different team, and their respective wins.

