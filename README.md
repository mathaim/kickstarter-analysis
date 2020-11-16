# kickstarter-analysis
Performing analysis on kickstarter data to uncover trends in campaign success.

# 1. Overview of Project
Louise’s play *Fever* came close to its fundraising goal in a short amount of time. Now, she wants to know how different campaigns fared in relation to their launch dates and their funding goals. In order to do this, we will primarily consider two variables: timing of campaign launch and monetary goal of campaign. Because Louise is interested in play campaign outcomes, we will subset the data to only include the parent category of theater and the subcategory of play.

# 2. Analysis and Challenges
## Cleaning Data
The main challenge with exploring this dataset was formating the **lanched_at variable**. In it's raw format, it is a ten digit timestamp that is difficult to understand. As it is the timestamp shows total seconds that have passed since the epoch, which is January 1, 1970 in Unix. I had to convert the date to a more readible format using this code:

              date_created =(((launched_at/60)/60)/24)+DATE(1970,1,1)

This formula is dividing the total time first by 60 seconds to get the number of minutes, then 60 minutes to get the number of hours, then 24 hours to get how many days after the epoch this campaign was launched. The DATE function puts the data in a readible format. The result is data of the format: 6/22/15 0:10.


We want to categorize the data in order to draw out trends. In their own columns, I isolated both the Month and Year for each data point. To get the Year, I just created a new column and used the =YEAR() function. To get the Month, I used the =TEXT() function:

              year = YEAR(date_created)
              month = TEXT(date_created, "mmm")
              
Since Excel dates are serial numbers in their nature it can be difficult use in pivot tables, I also used the =TEXT() function convert them to text values. I created a new column for this string variable and used the formula:

              date_created_text=TEXT(date_created,"mm/dd/yy h:mm")
              
Now the data is ready for analysis!

## Theater Outcomes by Launch Date
To better understand the role of time in campaign sucess, I used a pivot table. I set the **outcomes** variable as the columns and the **date_created_text** variable as the rows. I set the count of **outcomes** as the values. I excluded the *blank* and *live* outcomes because we want to focus on campaign success. I also set **parent_category** and **years** as filters. I then added the **month** variable to the rows above the **date_created_text** so that we could group dates by month. Lousie in primarily interested in the data for other theater kickstarters, so I applied this to the **parent_category** filter. 

![Exhibit A](Theater_Outcomes_vs_Launch.png)

## Outcomes Based on Goals
Another thing to consider is if the goals the campaign sets could possibly affect its success. This is could be the case because if campaigns set goals that are too lofty, people may feel less inclined to donate. To examine this, I asigned the **goal** variable to categories in $5000 increments. I used the following =COUNTIFS() function:

          =COUNTIFS(Kickstarter!$F:$F,"successful",Kickstarter!$D:$D,">=1000",Kickstarter!$D:$D,"<=4999",Kickstarter!$R:$R,"plays")   
          
The first argument of the =COUNTIFS() function is a range and the second argument is a criteria. If the crtieria is true, the function adds one to the count. In the function above, I set three crtieria for the count. If the **outcomes** column (F) equals "successful," if the **goal** column (D) is less than $4,999 but greater than $1000 and if the **subcategory** column (R) equals "plays," it is added to the count. This process is applied to all the goal categories and all the outcomes. I then used the =SUM() function to create a "Total Projects" column, and divided the counts by this number to get the percentage breakdowns.


![Exhibit B](Outcomes_vs_Goals.png)

# 3. Results

## Conclusion
Looking at Exhibit A, it is clear that from January until April both successful and failed campaigns follow a similar pattern. There are about 20 more successful than failed campaigns throughout these months. There is a local maxima for both successful and failed campaigns in February, which means that more campaigns were launched that month. There is a large spike of successful campaigns in May. Additionally, there is no similar spike of failed campaigns in May which means that it isn't just the case that more campaigns were launched in May. Theater campaigns that  launch in May had a higher liklihood of being successful. However, as the year progresses, there is a general downward trend of successful campaigns. The gap between successful and failed campaigns narrows. Campaigns launched in December has almost an equal liklihood of failure and success. The number of canceled campaigns stays low and relatively constant throughout the year.

Exhibit B shows the percentage of failed and successful play campaigns by goal dollar amount. Plays that have a goal of less than $1000 have a high chance of being successful (~75%). However this liklihood diminishes as the goal amount grows. If the goal is between $15,000 and $35,000, the campaign is more likely to fail than succeed. Campaigns between $35,000 and $45,000 are more likely to succeed (~65%) than fail (~35%). Campaigns with goals greater than $50,000 are very unlikely to be sucessful (~15%).

Recommendation: If Louise's primary goal is to be successful in her campaign, she should launch it in May with a goal of less than $1000. However, if she needs more than that to finance her play, she should consider setting the goal around $40,000. 

## Opportunities for Further Analysis
There are some limitations within the dataset. It would be interesting to see if this is the organizer's first kickstarter campaign. I would also like to see if social media played a role in campaign success. I would also like to see if there are any geographical trends within each country (ubran/rural/suburban). All of these missing variables could potentially offer better insight regarding kickstarter campaign sucess.

There is opportunity for further analysis if we looked at the average amount pledged per donor, so we could see if successful campaigns tend to be backed by a large number of small donations or vice versa. 

