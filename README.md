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
