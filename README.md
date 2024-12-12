# Individual NFL Sacks Statistics Compared to College Sack Statistics
# [IN PROGRESS]
## Introduction
This is my first data analysis project that I have set out on. This project was primarily meant to be a learning experience for myself, but I was also interested in possibly discovering some insights from this data. In the future I hope to try larger projects and continue to add tools to my toolbelt. One hobby of mine is football. Not only watching it, but studying it as well. In particular, I enjoy the draft and scout college players for the upcoming draft in my free time. There has been a big debate in all sports about the use of data analytics versus instincts or the "eye test". Personally, I believe that both of these are important when scouting players. One comment that I have heard from scouts is that a player that get sacks in college will get them in the NFL, and vice versa for a player that does not have many sacks in college. This study is focused on that statement and trying to find if that is true or if it is not that simple.
## Guiding Question
*Is there a correlation between the number of sacks that a football player achieves in college and the number that same player achieves in the NFL?*
### Disclaimers
- In order to keep things simple for this project, I am only focusing on NFL players who attended an FBS school
- Only players that played at least one season in college and the NFL are considered (13 games for college, 16 games for NFL)
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
Since the two datasets were in completely different formats, I knew that a lot of data manipulation would be needed. The CFB spreadsheets were in a pretty good state already since they were already arranged by season and they also contained my primary metric (sacks per game). The only cleaning that was needed for the CFB spreadsheets was deleting all the rows above the header and changing the names of the headers to something that was more SQL friendly. All of the header names were changed to lower case and a few of them were changed. Below I have a table showing the different column names that were used. The CFB spreadsheets were added to a dataset titled "CFB" in BigQuery in separate tables, each titled by whatever year they represent.
### NFL Sheets
The NFL spreadsheet was not nearly as simple as the CFB sheets, which were pretty much completely cleaned in Excel. The NFL sheet was almost entirely cleaned in BiqQuery. This sheet was added to a dataset in BigQuery titled, "NFL_Players" (I should have named it "NFL", but I named this dataset when I was first starting and was not sure what all I was going to be adding at the time).
