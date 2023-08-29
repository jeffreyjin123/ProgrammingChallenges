# SQL Challenge

The database contains three tables: marketing_performance, website_revenue, and campaign_info. Refer to the CSV
files to understand how these tables have been created.

`marketing_performance` contains daily ad spend and performance metrics -- impression, clicks, and conversions -- by campaign_id and location:
```sql
create table marketing_data (
 date datetime,
 campaign_id varchar(50),
 geo varchar(50),
 cost float,
 impressions float,
 clicks float,
 conversions float
);
```

`website_revenue` contains daily website revenue data by campaign_id and state:
```sql
create table website_revenue (
 date datetime,
 campaign_id varchar(50),
 state varchar(2),
 revenue float
);
```

`campaign_info` contains attributes for each campaign:
```sql
create table campaign_info (
 id int not null primary key auto_increment,
 name varchar(50),
 status varchar(50),
 last_updated_date datetime,
 revenue float
);
```

### Please provide a SQL statement for each question

1. Write a query to get the sum of impressions by day.
   
   SELECT date, SUM(impressions) AS total_impressions
   FROM marketing_data
   GROUP BY date;
   
2. Write a query to get the top three revenue-generating states in order of best to worst. How much revenue did the third best state generate?
   
   SELECT state, SUM(revenue) AS total_revenue
   FROM website_revenue
   GROUP BY state
   ORDER BY total_revenue DESC
   LIMIT 3;

   The third best state is Ohio, and the total revenue that the state generated is 37577.
   
3. Write a query that shows total cost, impressions, clicks, and revenue of each campaign. Make sure to include the campaign name in the output.

   SELECT
       c.name AS campaign_name,
       SUM(m.cost) AS total_cost,
       SUM(m.impressions) AS total_impressions,
       SUM(m.clicks) AS total_clicks,
       SUM(w.revenue) AS total_revenue
   FROM campaign_info c
   LEFT JOIN marketing_data m ON c.id = m.campaign_id
   LEFT JOIN website_revenue w ON c.id = w.campaign_id
   GROUP by c.name;
   
4. Write a query to get the number of conversions of Campaign5 by state. Which state generated the most conversions for this campaign?
   
   SELECT
       SUBSTRING_INDEX(m.geo, '-', -1) AS state,
       SUM(m.conversions) AS total_conversions
   FROM marketing_data m
   WHERE m.campaign_id = '99058240'
   GROUP BY state
   ORDER BY total_conversions DESC;

   GA or Georgia generated the most conversions for Campaign 5. At a total of 672 conversions.
   
5. In your opinion, which campaign was the most efficient, and why?

   SELECT 
       c.name AS campaign_name,
       SUM(m.cost) / NULLIF(SUM(m.conversions), 0) AS cost_per_conversion
   FROM campaign_info c
   LEFT JOIN marketing_data m ON c.id = m.campaign_id
   GROUP BY c.name
   ORDER BY cost_per_conversion ASC;

   We say a campaign is effecient if the cost per conversion is lower than the other campaigns. Meaning you spend less money to achieve each conversion.
   Thus campaign 4 is the most efficient campaign, as it has the lowest cost to conversion ration at 0.42662153.
   

**Bonus Question**

6. Write a query that showcases the best day of the week (e.g., Sunday, Monday, Tuesday, etc.) to run ads.
   
   SELECT 
    CASE DAYOFWEEK(date)
        WHEN 1 THEN 'Sunday'
        WHEN 2 THEN 'Monday'
        WHEN 3 THEN 'Tuesday'
        WHEN 4 THEN 'Wednesday'
        WHEN 5 THEN 'Thursday'
        WHEN 6 THEN 'Friday'
        ELSE 'Saturday' END AS day_of_week,
    SUM(cost) AS total_cost,
    SUM(impressions) AS total_impressions
  FROM marketing_data
  GROUP BY day_of_week
  ORDER BY total_impressions DESC
  LIMIT 1;
