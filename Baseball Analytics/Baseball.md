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




    
![png](output_5_0.png)
    



Though the table is self explantory, the graph provides a neat visualisation, comparing colleges based on the number of players it has sent to the major league. UT Austin, USC, Stanford, and Arizona state are significantly leading in numbers, whereas Missisipi state, South Carolina, and Auburn trail in the top 25. If the database had given the information about whether the college is D1, D2, or D3, it would have been interesting to compare how that affects the count of players going into the major league. 

2) Which teams have done significantly well in the last 5 years?

This could be used to see the top performing teams and give a starting point for analysts to dive deep into what these teams do that makes them stand out. In order to execute this query we needed data from both the teasm table and franchise table. We used a subquery to extract the names of each teams to provide a better visual experience. Again, due to the extensiveness of the Lahman database, there were a lot of teams. Since we wanted to examine the top performing teams, we sorted our results in descending order and used a limit of 10 to get only the top 10 teams


```sql
%%sql
select		yearID,franchName,sum(W) as Wins
from		teams
inner join	TeamsFranchises
on			teams.franchID = TeamsFranchises.franchID
group by	teamID,franchName,yearID
order by	Wins DESC
limit 10;
```

     * mysql://root:***@localhost/
    10 rows affected.





<table>
    <thead>
        <tr>
            <th>yearID</th>
            <th>franchName</th>
            <th>Wins</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1906</td>
            <td>Chicago Cubs</td>
            <td>116</td>
        </tr>
        <tr>
            <td>2001</td>
            <td>Seattle Mariners</td>
            <td>116</td>
        </tr>
        <tr>
            <td>1998</td>
            <td>New York Yankees</td>
            <td>114</td>
        </tr>
        <tr>
            <td>1954</td>
            <td>Cleveland Indians</td>
            <td>111</td>
        </tr>
        <tr>
            <td>1927</td>
            <td>New York Yankees</td>
            <td>110</td>
        </tr>
        <tr>
            <td>1909</td>
            <td>Pittsburgh Pirates</td>
            <td>110</td>
        </tr>
        <tr>
            <td>1961</td>
            <td>New York Yankees</td>
            <td>109</td>
        </tr>
        <tr>
            <td>1969</td>
            <td>Baltimore Orioles</td>
            <td>109</td>
        </tr>
        <tr>
            <td>1970</td>
            <td>Baltimore Orioles</td>
            <td>108</td>
        </tr>
        <tr>
            <td>2018</td>
            <td>Boston Red Sox</td>
            <td>108</td>
        </tr>
    </tbody>
</table>




```python
Image(filename = 'topTeams.png',width=1500,height=1500)
```




    
![png](output_9_0.png)
    



Using this visual, we can clearly see that New York Yankees have done tremendously well over the past few years, followed by the St. Louis Cardinals. We will see in the coming visualizations one potential reason as to why New York Yankees has been a top performer

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
Image(filename = 'allstars.png',width=1500,height=1500)
```




    
![png](output_13_0.png)
    



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
(SELECT 	yearID,
			teamID,
			franchID,
            Teams.W AS Wins 
            FROM Teams),
c as
(SELECT 	franchID,
			FranchName 
FROM 		TeamsFranchises)
SELECT 	c.FranchName,
        round(avg(b.Wins),0) as Wins,
        avg(Avgsalary) as Avgsalary
from	a
join	b
on		a.yearID = b.yearID
and		a.teamID = b.teamID
join	c
on		b.franchID = c.franchID
group by	 
			franchName
order by	WINS DESC, Avgsalary ASC;
```

     * mysql://root:***@localhost/
    30 rows affected.





<table>
    <thead>
        <tr>
            <th>FranchName</th>
            <th>Wins</th>
            <th>Avgsalary</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>New York Yankees</td>
            <td>90</td>
            <td>4098415.416875</td>
        </tr>
        <tr>
            <td>St. Louis Cardinals</td>
            <td>86</td>
            <td>2223063.126875</td>
        </tr>
        <tr>
            <td>Boston Red Sox</td>
            <td>86</td>
            <td>2959742.2059375</td>
        </tr>
        <tr>
            <td>Atlanta Braves</td>
            <td>85</td>
            <td>2246972.876875</td>
        </tr>
        <tr>
            <td>Los Angeles Dodgers</td>
            <td>84</td>
            <td>2817324.11125</td>
        </tr>
        <tr>
            <td>Oakland Athletics</td>
            <td>83</td>
            <td>1471157.2306250003</td>
        </tr>
        <tr>
            <td>Toronto Blue Jays</td>
            <td>83</td>
            <td>2017740.4471875003</td>
        </tr>
        <tr>
            <td>Los Angeles Angels of Anaheim</td>
            <td>83</td>
            <td>2379961.4581250004</td>
        </tr>
        <tr>
            <td>San Francisco Giants</td>
            <td>83</td>
            <td>2458494.2815624997</td>
        </tr>
        <tr>
            <td>New York Mets</td>
            <td>82</td>
            <td>2510974.5181249995</td>
        </tr>
        <tr>
            <td>Texas Rangers</td>
            <td>81</td>
            <td>2148670.0618749997</td>
        </tr>
        <tr>
            <td>Chicago White Sox</td>
            <td>81</td>
            <td>2220740.001875</td>
        </tr>
        <tr>
            <td>Cleveland Indians</td>
            <td>80</td>
            <td>1682759.3537499998</td>
        </tr>
        <tr>
            <td>Cincinnati Reds</td>
            <td>80</td>
            <td>1798502.0387500003</td>
        </tr>
        <tr>
            <td>Houston Astros</td>
            <td>80</td>
            <td>1803587.9459375</td>
        </tr>
        <tr>
            <td>Philadelphia Phillies</td>
            <td>79</td>
            <td>2336500.3024999998</td>
        </tr>
        <tr>
            <td>Arizona Diamondbacks</td>
            <td>79</td>
            <td>2555746.617894737</td>
        </tr>
        <tr>
            <td>Washington Nationals</td>
            <td>78</td>
            <td>1563719.46375</td>
        </tr>
        <tr>
            <td>Minnesota Twins</td>
            <td>78</td>
            <td>1743779.7431250003</td>
        </tr>
        <tr>
            <td>Seattle Mariners</td>
            <td>78</td>
            <td>2159551.0856250003</td>
        </tr>
        <tr>
            <td>Chicago Cubs</td>
            <td>78</td>
            <td>2395720.6790625</td>
        </tr>
        <tr>
            <td>San Diego Padres</td>
            <td>77</td>
            <td>1542320.2475</td>
        </tr>
        <tr>
            <td>Detroit Tigers</td>
            <td>77</td>
            <td>2431279.6324999994</td>
        </tr>
        <tr>
            <td>Milwaukee Brewers</td>
            <td>76</td>
            <td>1613285.869375</td>
        </tr>
        <tr>
            <td>Baltimore Orioles</td>
            <td>76</td>
            <td>2068332.3443750003</td>
        </tr>
        <tr>
            <td>Florida Marlins</td>
            <td>75</td>
            <td>1481394.4208333332</td>
        </tr>
        <tr>
            <td>Kansas City Royals</td>
            <td>75</td>
            <td>1566376.6303124998</td>
        </tr>
        <tr>
            <td>Tampa Bay Rays</td>
            <td>75</td>
            <td>1681849.8394736846</td>
        </tr>
        <tr>
            <td>Pittsburgh Pirates</td>
            <td>74</td>
            <td>1299344.696875</td>
        </tr>
        <tr>
            <td>Colorado Rockies</td>
            <td>74</td>
            <td>2194197.0858333334</td>
        </tr>
    </tbody>
</table>




```python
Image(filename = 'winSal.png',width=1500,height=1500)
```




    
![png](output_17_0.png)
    



As we can see from the scatter plot, the New York yankees have done tremendously well over the years, while paying a relatively larger average salary to players. On the other hand, the Arizone diamondbakcs did relatively poorly while paying small amounts of salaries to players. From the spread of the graph, we can see that there is a correlation between Salaries and Wins, and hence paying higher average salaries to players may bring you more wins, as one would think. 

To sum up, this project uses SQL queries to retrieve specific tables from the database Lehman2016 (baseball database) and perform operations to the columns in the tables in such a way that the table contains all the information necessary to answer the specific questions about baseball game. All the visual plots were created in Tableau and have been imported to this notebook. Overall, this project contains four different analysis. It shows number of baseball players sent to the major league by different colleges. It shows the changes in average salaries for each position of baseball throughout the years. The birth state and birth country of all star baseball players ranked by the number of all star players. Finally, the correlation between the average salaries different team, and their respective wins.

