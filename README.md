# Individual NFL Sacks Statistics Compared to College Sack Statistics
# [IN PROGRESS]
## Introduction
This is my first data analysis project that I have set out on. This project was primarily meant to be a learning experience for myself, but I was also interested in possibly discovering some insights from this data. In the future I hope to try larger projects and continue to add tools to my toolbelt. One hobby of mine is football. Not only watching it, but studying it as well. In particular, I enjoy the draft and scout college players for the upcoming draft in my free time. There has been a big debate in all sports about the use of data analytics versus instincts or the "eye test". Personally, I believe that both of these are important when scouting players. One comment that I have heard from scouts is that a player that get sacks in college will get them in the NFL, and vice versa for a player that does not have many sacks in college. This study is focused on that statement and trying to find if that is true or if it is not that simple.
## Guiding Question
*Is there a correlation between the number of sacks that a football player achieves in college and the number that same player achieves in the NFL?*
### Disclaimers
- In order to keep things simple for this project, I am only focusing on NFL players who attended an FBS school
- Only players that played at least one season in college and the NFL are considered (12 games for college, 16 games for NFL)
- Due to a difference in formatting for NCAA archived statistics beginning in 2013, I only used data from 2014-2023. In the future, I may take on the data from earlier years and use Python to clean and format it like the later years.
## Data Sources
- NFL sack statistics are pulled from a GitHub repository called nflverse: https://github.com/nflverse/nflverse-data/releases
- College sack statistics are pulled from the NCAA archived national stats: https://web1.ncaa.org/stats/StatsSrv/rankings?doWhat=archive&sportCode=MFB
### Data Formats
The data pulled from the sources above were not in a similar format. The college football (CFB) spreadsheets were separated by year and showed the players' season totals for sacks. As stated earlier, the CFB spreadsheets only go back to 2013 before their format changes entirely. The NFL spreadsheet, on the other hand, was a massive collection of data which included every player who had achieved some form of defensive statistic (tackle, sack, interception, etc.) and every game that player played. The NFL spreadsheet dates all the way back to the 1999 season.
### Raw Spreadsheet Examples
**NFL**

![image](https://github.com/user-attachments/assets/d9481ce3-1268-4434-b351-1427b296c0c4)

**CFB**

![image](https://github.com/user-attachments/assets/3d771f34-1c83-4a19-9ad4-fc8fdc0bf0ae)

## How will correlation be established?
Due to the fact that a full season in college is different from that of the NFL, I did not think that total sacks was a good metric to use. Instead, I decided to use sacks per game as the primary metric since this would account for a difference in games played. However, to account for a small sample size of games, I chose to only include players that played at least one full season in both college and the NFL.
## Tools Used
- Microsoft Excel
- BigQuery
## Data Cleaning
### CFB Sheets
Since the two datasets were in completely different formats, I knew that a lot of data manipulation would be needed. The CFB spreadsheets were in a pretty good state already since they were already arranged by season and they also contained my primary metric (sacks per game). The only cleaning that was needed for the CFB spreadsheets was deleting all the rows above the header and changing the names of the headers to something that was more SQL friendly. All of the header names were changed to lower case and a few of them were changed. Below I have a table showing the different column names that were used. The CFB spreadsheets were added to a dataset titled "CFB" in BigQuery in separate tables, each titled by whatever year they represent. These tables were then joined together using UNION ALL in BigQuery into a table titled, "FBS_Sacks".

As a side objective to this project, I wanted to see if there was more correlation when you broke it down by position. Therefore, I needed to do a little more work on the positions. I used SQL queries to see if there was more than one position listed for a player across different seasons. (Quick side bar: position naming conventions are not always standardized in the sense that a player may play multiple positions during a season. However, for this study I am assigning a player either LB, DB, DT, or DE depending on which one fits best). In total there were 35 players that had multiple positions listed for them. I took each and these and put them in a word document (Word document in repository) listing all the positions that had been assigned to them. Most of these only consist of two positions. I then went through each of these and decided whcih of the positions listed best fit that player. I then replace these positions in SQL.
### NFL Sheets
The NFL spreadsheet was not nearly as simple as the CFB sheets, which were pretty much completely cleaned in Excel. The NFL sheet was almost entirely cleaned in BiqQuery. The raw data sheet was added to a dataset in BigQuery titled, "NFL_Players.Defense" (I should have named it "NFL", but I named this dataset when I was first starting and was not sure what all I was going to be adding at the time). From there, the data was aggregated into one table titled, "NFL_Players.A".
## SQL Queries
```
CREATE TABLE NFL_Players.A AS
  SELECT player_id, player_display_name, COUNT(player_id) AS nfl_games, SUM(def_sacks) AS nfl_total_sacks
  FROM NFL_Players.Defense
  GROUP BY player_id, player_display_name
  ORDER BY player_display_name ASC
```
First, Takes each entry specific to a player_id and counts it to give me the total number of games that player played in his career, then sums up each sack that player achieved in those games. Second, it creates a table, NFL_Players.A, to store the results.
```
ALTER TABLE NFL_Players.A
ADD COLUMN nfl_sacks_per_game float64
```
Adds a column to the NFL table for sacks per game since this statistic was not included in the original NFL data.
```
UPDATE NFL_Players.A
SET nfl_sacks_per_game = nfl_total_sacks / nfl_games
WHERE true
```
Sets sacks per game as total sacks divided by total games played.
```
DELETE
FROM NFL_Players.A
WHERE nfl_games < 16
```
Removes all players from the NFL_Players.A table that have played less than 16 total games (one full season).
```
CREATE TABLE CFB.FBS_Sacks AS
  SELECT * FROM CFB.2023
  UNION ALL
  SELECT * FROM CFB.2022
  UNION ALL
  SELECT * FROM CFB.2021
  UNION ALL
  SELECT * FROM CFB.2020
  UNION ALL
  SELECT * FROM CFB.2019
  UNION ALL
  SELECT * FROM CFB.2018
  UNION ALL
  SELECT * FROM CFB.2017
  UNION ALL
  SELECT * FROM CFB.2016
  UNION ALL
  SELECT * FROM CFB.2015
  UNION ALL
  SELECT * FROM CFB.2014
  ORDER BY player_display_name ASC
```
Creates the CFB.FBS_Sacks table by combining all the individual CFB tables.
```
SELECT DISTINCT POS
FROM CFB.FBS_Sacks
```
Shows each position that is in the table, CFB.FBS_Sacks. All returns were defensive positions except one which was RB.
```
SELECT *
FROM CFB.FBS_Sacks
WHERE Pos = 'RB'
```
Shows which players have their position listed as RB. Returned one player named "Charles Wright".
```
UPDATE CFB.FBS_Sacks
SET Pos = "LB"
WHERE player_display_name = "Charles Wright"
```
Changes Charles Wright's position from RB to LB.
```
CREATE TABLE CFB.C AS
  SELECT *
  FROM 
    (SELECT
    player_display_name, Pos, SUM(cfb_games) AS cfb_games, SUM(solo_sack) AS cfb_solo_sacks, SUM(asst_sack) AS cfb_asst_sacks, SUM(cfb_total_sacks) AS cfb_total_sacks, AVG(cfb_sacks_per_game) AS cfb_sacks_per_game
    FROM CFB.FBS_Sacks
    GROUP BY player_display_name, Pos) AS ds
  WHERE ds.cfb_games >= 12
  ORDER BY player_display_name ASC
```
Takes the table, CFB.FBS_Sacks, and adds up each statistic for each player over multiple seasons to get career numbers. Then it returns only those players that have played at least a total of 12 games over their college career. Team is not included in this query since there are some players that have played for mulitple teams in college. This data is then stored in a new table titled, "CFB.C".
```
SELECT C.player_display_name, C.pos, cfb_games, cfb_total_sacks, cfb_sacks_per_game, nfl_games, nfl_total_sacks, nfl_sacks_per_game
FROM CFB.C
FULL OUTER JOIN NFL_Players.A
ON B.player_display_name = A.player_display_name
WHERE cfb_sacks_per_game is not null AND nfl_sacks_per_game is NOT NULL
ORDER BY nfl_total_sacks DESC
```
```
SELECT *
FROM
  (SELECT player_id, MAX(nfl_games) AS most_pos_games, SUM(nfl_games) AS total_games
  FROM NFL_Players.D
  GROUP BY player_id
  HAVING COUNT(DISTINCT(pos)) > 1
  ORDER BY player_id DESC) AS ds
WHERE ds.total_games >= 16
```
