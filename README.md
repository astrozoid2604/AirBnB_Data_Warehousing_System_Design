# 1. Introduction
Throughout the course of this project, I gained valuable insights on database management system and retrieval languages. In this opportunity, our group embarks on journey to implement a project to design database based on AirBnB dataset provided.

---

## 1.1. Project Objective
The objective of this project is to understand the basic process of data management and retrieval in real applications and organizations, which generally covers the following tasks and procedures: collecting, processing, storing and accessing data.

---

## 1.2. Project Background
For the purpose of this project, an industrial dataset was gathered from AirBnB platform which is one of famous lodging online platform. The dataset is obtained from website [http://insideairbnb.com/get-the-data/](http://insideairbnb.com/get-the-data/) for Bangkok, Central Thailand, Thailand. 

From this dataset, few processes are involved such as knowing the inherent properties of the data, cleaning the dataset, generating derivative relations, storing data in both relational and non-relational database systems, and retrieving data so as to answer few samples of user queries.

--- 

# 2. Data Analysis
Having introduced about the project background, next step is to explore dataset semantics and structures which will be elaborated in details in this section.

---

## 2.1. Dataset Background
This group is given an AirBnB dataset in the region of Bangkok, Central Thailand, Thailand from [http://insideairbnb.com/get-the-data/](http://insideairbnb.com/get-the-data/). The dataset contains information on the listings, calendar prices, and reviews with summary information. There are five CSV files in total: calendar.csv, listings.csv, reviews.csv, listings_summary.csv and reviews_summary.csv. Since the last two files contain summary information on listings and reviews, which are already captured by listings.csv and reviews.csv, pertaining to the needs of this project, these two files are not analyzed in this and following sections. 

The calendar.csv gives the daily price of listings, and it has 6,231,645 rows and 7 attributes; the reviews.csv shows the review comments of listings, and it has 246,644 rows and 6 attributes; the listings.csv provides detailed information on listings including the host information, and it has 17,074 rows and 74 attributes.

---

## 2.2. Dataset Semantic and Structure
In this section, the three CSV files, calendar.csv reviews.csv and listings.csv, are going to be analyzed. First, their structure and semantics would be examined through meaning of each attribute, relationship between multiple tables and suggestion on potential primary keys and foreign keys.

Also, each data attribute are going to be explored through data type, domain, size or dictionary size of text data, volume, range, distribution and languages in text. In particular, the data types are determined first through Python codes, and with further examination of the attributes, advices are given on which data types to store the attributes in MySQL database. With the analysis on the attributes, especially with their ranges, constraints can be pointed out, and potential primary keys and foreign keys can be identified.

---

### 2.2.1. calendar.csv
The attributes and their meanings are as follows:
- **listing_id**: Listing ID of the AirBnB housing
- **date**: The date in the listing's calendar
- **available**: It determines whether the listing is available for booking at that date
- **price**: The price listed of the housing at that date
- **adjusted_price**: Adjusted price of the housing at that date, dynamic pricing
- **minimum_nights**: The minimum nights for a booking made on this day
- **maximum_nights**: The maximum nights for a booking made on this day

A summary of data types is as follows:
- **listing_id**: Integer data type. Might convert to varchar(18) in SQL
- **date**: String data type. Might convert to date type in SQL
- **available**: String data type. Might convert to char(1) in SQL
- **price**: String data type. Might convert to float(1) in SQL
- **adjusted_price**: String data type. Might convert to float(1) in SQL
- **minimum_nights**: Float data type. Can float(1) in SQL
- **maximum_nights**: Float data type. Can float(1) in SQL

The data attributes are then analyzed as follows:
- **listing_id**: There are 17,073 unique listing IDs. The maximum number of digits of the listing ID is 18 digits.
- **date**: The date ranges from June 20, 2022 to June 20, 2023. While examining individual listing’s association with different dates, it shows that some listings have daily price for all days during the year span while others are not.
- **available**: There are two levels for available: t and f, corresponding to the availability of the listing on a particular date. There are 4,329,257 rows with t and 1902388 rows with f.
- **price**: The price ranges from $252.0 to $1500000.0 with a median of $1,215.0. Together with five-number summary and histogram of price in Figure 1 and Figure 2, it indicates that most of the prices are low, with a few very high prices that makes the distribution of price highly skewed to the right.
![Figure 1 Five-Number Summary of price](Figure/Figure1.PNG)
![Figure 2 Histogram of price](Figure/Figure2.PNG)
- **adjusted_price**: The adjusted price ranges from $165.0 to $1,500,000.0, with a median of $1,200.0. There are 333,341 rows with adjusted price different from the listed price. 329,672 rows have higher price than adjusted price, which is 98.9% of the time the host will set a lower adjusted price; 3,669 rows have lower price than adjusted price, which is 1.1% of the time the host will set a higher adjusted price. The maximum price increase is $20,000.0, and the maximum price decrease is $2,370.0. The distribution of the adjusted price is very similar to that of the price as shown in Figure 3 and Figure 4 through five-number summary and histogram.
![Figure 3 Five-Number Summary of adjusted_price](Figure/Figure3.PNG)
![Figure 4 Histogram of adjusted_price](Figure/Figure4.PNG)
- **minimum_nights**: The minimum nights a guest can book on a particular date ranges from 1 day to 1,125 days, with a median of 1 day. Out of the 6,231,465 rows, there are 3,468,431 rows with minimum nights of 1 day, which is 55.7% of the rows. Most of the listings have small minimum nights requirement, but there are some with around 200 days and 400 days and few even higher, which leads to a highly skewed to the right distribution. The five-number summary and histogram of minimum nights is shown in Figure 5 and Figure 6.
![Figure 5 Five-Number Summary of minimum_nights](Figure/Figure5.PNG)
![Figure 6 Histogram of minimum_nights](Figure/Figure6.PNG)
- **maximum_nights**: The maximum nights a guest can book on a particular date ranges from 1 day to 2,147,483,647 days. Examining the quantiles through Figure 7 reveals a highly skewed to the right distribution, and the same result is shown in the histogram in Figure 8.
![Figure 7 Five-Number Summary of maximum_night](Figure/Figure7.PNG)
![Figure 8 Histogram of maximum_nights](Figure/Figure8.PNG)

The attribute constraints, potential primary keys and foreign keys are as follows:
- primary key (listing_id, date)
- check (available in ('t', 'f')): to ensure available only has two levels
- check (price > 0)
- check (adjusted_price > 0)
- check (minimum_nights >= 1)
- check (maximum_nights >= 1)
- foreign key(listing_id) references listings(id) (will be touched on later)

---

### 2.2.2. reviews.csv
The attributes and their meanings are as follows:
- **listing_id**: Listing ID of the AirBnB housing, same as listing_id in calendar.csv
- **id**: The review's ID
- **date**: The date of the review
- **reviewer_id**: The reviewer's ID
- **reviewer_name**: The reviewer's name
- **comments**: The review's comment

A summary of data types is as follows:
- **listing_id**: Integer data type. Might convert to varchar(18) in SQL
- **id**: Integer data type. Might convert to varchar(18) in SQL
- **date**: String data type. Might convert to date type in SQL
- **reviewer_id**: Integer data type. Might convert to varchar(9) in SQL
- **reviewer_name**: String data type. Might convert to varchar(42) in SQL,length may need to increase
- **comments**: String data type. Might convert to varchar(6577) in SQL, length may need to increase

The data attributes are then analyzed as follows:
- **listing_id**: There are 10,012 unique listing IDs. The maximum number of digits of the listing ID is 18 digits. All listing IDs in reviews.csv are in calendar.csv.
- **id**: All reviews’ IDs are unique. The maximum number of digits of the ID is
18** digits.
- **date**: The reviews spans from February 09, 2011 to June 21, 2022. Comparing
with the dates from calendar.csv, there is not much overlapping: only June 20 and June 21 of the year 2022 are in calendar.csv. In total, there are only 41 reviews on left on these two dates. In addition, the review date is not necessarily a leave date or occupying date of the reviewer’s stay at the listing. Figure 9 below shows a distribution of the number of review by year. It can be seen that the distribution follows a bell shape: most of the reviews were left in the year of 2017 to 2020. This is somewhat aligned with the growth of AirBnB during those years.
![Figure 9 Number of Reviews by Year](Figure/Figure9.PNG)
- **reviewer_id**: There are 210,367 unique reviewer IDs. Comparing with the 246,644 rows in reviews.csv, there are reviews left by the same reviewer, either for the same listing or different listing (240,229 reviews with different listing and reviewer), on the same date or not (246,613 reviews with different listing, reviewer and date).
- **reviewer_name**: The maximum number of characters for reviewer’s name is 42 characters. There are 43 different languages in the reviewer’s name, where the language detection is done using python package langdetect. The detection returns a list of languages in ISO 639-1 codes (“List of ISO 639-1 codes,” 2022), where their corresponding languages are shown in Figure 10.
![Figure 10 Languages in reviewer_name](Figure/Figure10.PNG)
- **comments**: The maximum number of characters for reviewer’s name is 6,577 characters. There are 44 languages used in comments, including all of the 43 languages used in reviewer’s name plus Urdu.

The attribute constraints, potential primary keys and foreign keys are as follows: • primary key(id)
- foreign key(listing_id) references listings(id) (will be touched on later)

---

### 2.2.3. listings.csv
The attributes and their meanings are as follows, mostly referring to dataset dictionary from Inside AirBnB (Inside AirBnB Data Dictionary, 2022):
- **id**: Listing ID of the AirBnB housing, same as listing_id in calendar.csv and reviews.csv
- **listing_url**: The URL of the AirBnB housing
- **scrape_id**: The web scraping ID
- **last_scraped**: The date and time in UTC format at which a particular listing was scraped
- **name**: The name of the listing
- **description**: The detailed description of the listing
- **neighborhood_overview**: The host's description of the neighborhood
- **picture_url**: The URL to the AirBnB hosted regular sized image for the listing (i.e. the first image of the listing)
- **host_id**: AirBnB's unique identifier for the host/user
- **host_url**: The host's AirBnB page
- **host_name**: The name of the host, usually just the first name
- **host_since**: The date the host/user was created. For hosts that are AirBnB guests this could be the date they registered as a guest. i.e. the date the host joined AirBnB as a host or a guest
- **host_location**: The host's self-reported location
- **host_about**: The host's self-description
- **host_response_time**: The average amount of time that the host took to respond to all new messages in the past 30 days (past 30 days with respect to the listing's scarped date)
- **host_response_rate**: The percentage of new enquiries and reservation requests the host responded to (by either accepting/pre-approving or declining) within 24 hours in the past 30 days (What are response rate and response time and how are they calculated?)
- **host_acceptance_rate**: The rate at which the host accepts booking requests
- **host_is_superhost**: This describes whether the host is a superhost
- **host_thumbnail_url**: The host's profile picture on the listing page, displayed small
- **host_picture_url**: The host's profile picture on the airbnb page, displayed medium
- **host_neighbourhood**: The neighbourhood the host lives in. This information is not on host's page and listing page
- **host_listings_count**: The number of listings the host has (per AirBnB calculations)
- **host_total_listings_count*8: The number of listings the host has (per AirBnB calculations).
- **host_verifications**: This describes the means of host identity confirmation.
- **host_has_profile_pic**: This describes whether the host has a profile picture
- **host_identity_verified**: This describes whether the host's identity has been verified by AirBnB (Verifying your identity)
- **neighbourhood**: The neighbourhood of the listing
- **neighbourhood_cleansed**: The neighbourhood as geocoded using the latitude and longitude against neighbourhoods as defined by open or public digital shapefiles
- **neighbourhood_group_cleansed**: The neighbourhood group as geocoded using the latitude and longitude against neighbourhoods as defined by open or public digital shapefiles
- **latitude**: The latitude of the listing, uses the World Geodetic System (WGS84) projection for latitude and longitude
- **longitude**: The longitude of the listing, uses the World Geodetic System (WGS84) projection for latitude and longitude
- **property_type**: The host’s self-selected property type. Hotels and Bed and Breakfasts are described as such by their hosts in this field
- **room_type**: The room type of the listing (Types of places to stay)
- **accommodates**: The maximum capacity of the listing
- **bathrooms**: The number of bathrooms in the listing
- **bathrooms_text**: The textual description of the number of bathrooms in the listing
- **bedrooms**: The number of bedrooms
- **beds**: The number of bed(s)
- **amenities**: The list of amenities the place offers
- **price**: The daily price in local currency
- **minimum_nights**: The minimum number of night stay for the listing
- **maximum_nights**: The maximum number of night stay for the listing
- **minimum_minimum_nights**: The smallest minimum_nights value from the calendar (looking 365 nights in the future)
- **maximum_minimum_nights**: The largest minimum_nights value from the calendar (looking 365 nights in the future)
- **minimum_maximum_nights**: The smallest maximum_nights value from the calendar (looking 365 nights in the future)
- **maximum_maximum_nights**: The largest maximum_nights value from the calendar (looking 365 nights in the future)
- **minimum_nights_avg_ntm**: The average minimum_nights value from the calendar (looking 365 nights in the future)
- **maximum_nights_avg_ntm**: The average maximum_nights value from the calendar (looking 365 nights in the future)
- **calendar_updated**: Empty column
- **has_availability**: This describes whether the listing is available based on the following four columns (i.e. in the next 365 days).
- **availability_30**: The availability of the listing 30 days in the future as determined by the calendar. Note a listing may not be available because it has been booked by a guest or blocked by the host
- **availability_60**: The availability of the listing 60 days in the future as determined by the calendar. Note a listing may not be available because it has been booked by a guest or blocked by the host
- **availability_90**: The availability of the listing 90 days in the future as determined by the calendar. Note a listing may not be available because it has been booked by a guest or blocked by the host
- **availability_365**: The availability of the listing 365 days in the future as determined by the calendar. Note a listing may not be available because it has been booked by a guest or blocked by the host
- **calendar_last_scraped**: The date the listing's calendar was last scraped
- **number_of_reviews**: The number of reviews the listing has
- **number_of_reviews_ltm**: The number of reviews the listing has (in the last 12 months)
- **number_of_reviews_l30d**: The number of reviews the listing has (in the last 30 days)
- **first_review**: The date of the first/oldest review 
- **last_review**: The date of the last/newest review
- **review_scores_rating**: The overall score rating of the listing by all reviews, out of 5
- **review_scores_accuracy**: The accuracy score for the listing by all reviews, out of 5
- **review_scores_cleanliness**: The cleanliness score for the listing by all reviews, out of 5
- **review_scores_checkin**: The check-in score for the listing by all reviews, out of 5
- **review_scores_communication**: The communication score for the listing by all reviews, out of 5
- **review_scores_location**: The location score for the listing by all reviews, out of 5
- **review_scores_value**: The value score for the listing by all reviews, out of 5
- **license**: The licence/permit/registration number
- **instant_bookable**: This describes whether the guest can automatically book the listing without the host requiring to accept their booking request. An indicator of a commercial listing
- **calculated_host_listings_count**: The number of listings the host has in the current scrape, in the city/region geography
- **calculated_host_listings_count_entire_homes**: The number of Entire home/apt listings the host has in the current scrape, in the city/region geography
- **calculated_host_listings_count_private_rooms**: The number of Private room listings the host has in the current scrape, in the city/region geography
- **calculated_host_listings_count_shared_rooms**: The number of Shared room listings the host has in the current scrape, in the city/region geography
- **reviews_per_month**: The number of reviews the listing has over the lifetime of the listing (in months)

A summary of data types is as follows:
- **id**: Integer data type. Might convert to varchar(18) in SQL
- **listing_url**: String data type. Might convert to varchar(2,048) in SQL. 2,048 characters is the conventional max length for URL
- **scrape_id**: Integer data type. Might delete this row in SQL
- **last_scraped**: String data type. Might convert to date type in SQL
- **name**: String data type. Might convert to varchar(255) in SQL, length may need to increase
- **description**: String data type. Might convert to varchar(1,000) in SQL, length may need to increase
- **neighborhood_overview**: String data type. Might convert to varchar(1,000) in SQL, length may need to increase
- **picture_url**: String data type. Might convert to varchar(2,048) in SQL. 2,048 characters is the conventional max length for URL
- **host_id**: Integer data type. Might convert to varchar(9) in SQL
- **host_url**: String data type. Might convert to varchar(2,048) in SQL. 2,048 characters is the conventional max length for URL
- **host_name**: String data type. Might convert to varchar(35) in SQL, length may need to increase
- **host_since**: String data type. Might convert to date type in SQL 
- **host_location**: String data type. Might convert to varchar(125) in SQL, length may need to increase
- **host_about**: String data type. Might convert to varchar(5893) in SQL, length may need to increase
- **host_response_time**: String data type. Might convert to varchar(18) or int in SQL. int based on James' note to handle missing values
- **host_response_rate**: String data type. Might convert to int in SQL. % to whitespace according to James' note
- **host_acceptance_rate**: String data type. Might convert to int in SQL. % to whitespace according to James' note
- **host_is_superhost**: String data type. Might convert to char(1) in SQL
- **host_thumbnail_url**: String datat type. Might convert to varchar(2,048) in SQL. 2,048 characters is the conventional max length for URL
- **host_picture_url**: String data type. Might convert to varchar(2,048) in SQL. 2,048 characters is the conventional max length for URL
- **host_neighbourhood**: String data type. Might convert to varchar(28) in SQL, length may need to increase
- **host_listings_count**: Float data type. Can set float(1) in SQL
- **host_total_listings_count**: Float data type. Might delete this row in SQL
- **host_verifications**: String data type. Might convert to varchar(32) in SQL
- **host_has_profile_pic**: String data type. After imputing the missing values, might convert to char(1) in SQL
- **host_identity_verified**: String data type. After imputing the missing values, might convert to char(1) in SQL
- **neighbourhood**: String data type. Might convert to varchar(80) in SQL, length may need to increase
- **neighbourhood_cleansed**: String data type. Might convert to char(20) in SQL
- **neighbourhood_group_cleansed**: Float data type. All empty values, might delete this row in SQL
- **latitude**: Float data type. Might convert to float(5) in SQL
- **longitude**: Float data type. Might convert to float(5) in SQL
- **property_type**: String data type. Might convert to varchar(34) in SQL
- **room_type**: String data type. Might convert to varchar(15) in SQL
- **accommodates**: Integer data type
- **bathrooms**: Float data type. All empty values, might delete this row in SQL
- **bathrooms_text**: String data type. Might convert to varchar(17) in SQL
- **bedrooms**: Float data type. Might convert to float(1) in SQL
- **beds**: Float data type. Might convert to float(1) in SQL
- **amenities**: String data type. Might convert to varchar(1606) in SQL
- **price**: String data type. Might convert to float(1) in SQL
- **minimum_nights**: Integer data type.
- **maximum_nights**: Integer data type.
- **minimum_minimum_nights**: Float data type. Might convert to float(1) in SQL
- **maximum_minimum_nights**: Float data type. Might convert to float(1) in SQL
- **minimum_maximum_nights**: Float data type. Might convert to float(1) in SQL
- **maximum_maximum_nights**: Float data type. Might convert to float(1) in SQL
- **minimum_nights_avg_ntm**: Float data type. Might convert to float(1) in SQL
- **maximum_nights_avg_ntm**: Float data type. Might convert to float(1) in SQL
- **calendar_updated**: Float data type. All empty values, might delete this row in SQL
- **has_availability**: String data type. Might convert to char(1) in SQL
- **availability_30**: Integer data type.
- **availability_60**: Integer data type.
- **availability_90**: Integer data type.
- **availability_365**: Integer data type.
- **calendar_last_scraped**: String data type. Might convert to date type in SQL
- **number_of_reviews**: Integer data type.
- **number_of_reviews_ltm**: Integer data type
- **number_of_reviews_l30d**: Integer data type.
- **first_review**: Float data type. Might convert to date type in SQL
- **last_review**: Float data type. Might convert to date type in SQL
- **review_scores_rating**: Float data type. Can set float(1) in SQL
- **review_scores_accuracy**: Float data type. Can set float(1) in SQL
- **review_scores_cleanliness**: Float data type. Can set float(1) in SQL
- **review_scores_checkin**: Float data type. Can set float(1) in SQL
- **review_scores_communication**: Float data type. Can set float(1) in SQL
- **review_scores_location**: Float data type. Can set float(1) in SQL
- **review_scores_value**: Float data type. Can set float(1) in SQL
- **license**: Float data type. All empty values, might delete this row in SQL
- **instant_bookable**: String data type. Can set char(1) in SQL
- **calculated_host_listings_count**: Integer data type.
- **calculated_host_listings_count_entire_homes**: Integer data type.
- **calculated_host_listings_count_private_rooms**: Integer data type.
- **calculated_host_listings_count_shared_rooms**: Integer data type.
- **reviews_per_month**: Float data type. Can set float(2) in SQL.

The data attributes are then analyzed as follows:
- **id**: There are 17,074 unique listing IDs. The maximum length of digits is 18 digits. Since id uniquely identifies each listing, it serves the same purpose as listing_id in calendar.csv and reviews.csv. Recall that there are 17,073 unique listing IDs in calendar.csv, this suggests that there is one listing that is present in listings.csv but not in calendar.csv, which is the listing with id 13,729.
- **listing_url**: There are 17,074 unique listing URLs, which is of the same number of unique IDs of the listings. The maximum number of characters for listing’s URL is 36 characters.
- **scrape_id**: All listing has the same web scraping ID 20,220,620,202,126.
- **last_scraped**: There are two days that the AirBnB data was last scraped: June 20 and June 21 of the year 2022.
- **name**: There are duplicate names for the listings as there are only 16,089 unique names. The maximum number of characters for name is 255 characters. There are 39 different languages in the name of the listing as shown in Figure 11.
![Figure 11 Languages in name](Figure/Figure11.PNG)
- **description**: There are duplicate description for the listings as there are only 13,298 unique listing description. The maximum number of characters for description is 1,000 characters. There are 29 different languages in the description of the listing as shown in Figure 12.
![Figure 12 Languages in description](Figure/Figure12.PNG)
- **neighborhood_overview**: There are duplicate neighbourhood description for the listings as there are only 6002 unique neighbourhood description. The maximum number of characters for neighbourhood description is 1000 characters. There are 31 different languages in the neighbourhood description of the listing as shown in Figure 13.
![Figure 13 Languages in neighborhood_overview](Figure/Figure13.PNG)
- **picture_url**: There are 15,799 unique picture URLs. The maximum number of characters for the URL that hosts the first image of the listing is 122 characters.
- **host_id**: There are 7,639 unique host IDs, meaning there are 7,639 different hosts in total. The maximum number of digits for the host's ID is 9 digits.
- **host_url**: There are 7,639 unique host URLs, which is of the same number of the unique host IDs. The maximum number of characters for the host’s URL is 42 characters.
- **host_name**: There are 5,991 unique host names. Comparing the number with the number of hosts in the area, there are hosts with the same name. The maximum number of characters for the host’s name is 35 characters. There are 37 different languages in the host’s name as shown in Figure 14.
![Figure 14 Languages in host_name](Figure/Figure14.PNG)
- **host_since**: The date that the host was created spans from December 02, 2009 to June 17, 2022. The distribution of the host creation date is shown in Figure 15, and it follows somewhat bimodal distribution with the highest number of host creation around the year of 2015 and 2016 and the year of 2018 and 2019.
![Figure 15 Distribution of host_since by Year](Figure/Figure15.PNG)
- **host_location**: For the host location, there are 632 unique host locations. Some of the locations more detailed with district and city information while others just have country. The maximum number of characters for the host’s self- reported location is 125 characters. There are 27 different languages in the host’s location as shown in Figure 16.
![Figure 16 Languages in host_location](Figure/Figure16.PNG)
- **host_about**: There are 3,305 unique host’s self-description. The maximum number of characters for the host’s self-description is 5,893 characters. There are 27 different languages in the host’s self-description as shown in Figure 17.
![Figure 17 Languages in host_about](Figure/Figure17.PNG)
- **host_response_time**: The host’s response time has five levels: within an hour, within a few hours, within a day, a few days or more, nan(missing values). There are 7,033 listings with host’s response time within an hour, 1,420 listings of within a few hours, 1,252 listings of within a day, 1,740 listing of a few days or more, 5,629 of rows with missing value as shown in Figure 18. The maximum number of characters for the host’s response time is 18 characters.
![Figure 18 Distribution of host_response_time](Figure/Figure18.PNG)
- **host_response_rate**: There are 54 levels of host’s response rate. The attribute is of string data type in the csv file, but it is actually capturing numerical information. The host’s response time ranges from 0% to 100% with the majority of the listings have a host’s response rate of 100%. Figure 19 shows that the distribution of the host’s response rate is highly skewed to the right as a few listings have very low host’s response rate.
![Figure 19 Distribution of host_reponse_rate](Figure/Figure19.PNG)
- **host_acceptance_rate**: There are 70 levels of host’s response rate. The attribute is of string data type in the csv file, but it is actually capturing numerical information. The host’s acceptance time ranges from 0% to 100% with the majority of the listings have a host’s acceptance rate of 100%. Figure 20 shows that the distribution of the host’s acceptance rate is highly skewed to the right as a few listings have very low host’s acceptance rate.
![Figure 20 Distribution of host_ acceptance_rate](Figure/Figure20.PNG)
- **host_is_superhost**: There are three levels: f, t, nan(missing value). There are 15235 rows with f, 1837 rows with t and 2 rows with missing value.
- **host_thumbnail_url**: There are 7,557 unique host's profile pictures (displayed in small) on the listing page. The maximum number of characters for the host’s thumbnail URL is 106 characters.
- **host_picture_url**: There are 7,557 unique host's profile pictures (displayed in medium) on the listing page. The maximum number of characters for the host’s thumbnail URL is 109 characters.
- **host_neighbourhood**: There are 89 unique neighbourhood that the host lives in. The maximum number of characters for these neighbourhoods is 28 characters. There are 20 different languages in these neighbourhoods as shown in Figure 21.
![Figure 21 Languages in host_neighborhood](Figure/Figure21.PNG)
- **host_listings_count**: The number of listings that the host has ranges from 0 listing to 422 listings with a median of 3 listings. With few very large host's listing count, its distribution is highly skewed to the right as shown in Figure 22 and Figure 23 through the five-number summary and the histogram.
![Figure 22 Five-Number Summary of host_listings_count](Figure/Figure22.PNG)
![Figure 23 Histogram of host_listings_count](Figure/Figure23.PNG)
- **host_verifications**: There are three ways that a host can confirm identity: phone, email and work email. Hence there are 8 levels in total: 6 combinations of the three ways, no verification and no information on the verification. There are 12,773 rows with verified email and phone, 2,168 rows with verified email, phone and work email, 1,916 rows with verified phone, 108 rows with verified phone and work email, 60 rows with no verification, 45 rows with verified email, 2 rows with verified work_email and 2 rows with no information on verification. The distribution can be seen in Figure 24. The maximum number of characters for the host’s verification is 32 characters.
![Figure 24 Distribution of host_verifications](Figure/Figure24.PNG)
- **host_has_profile_pic**: The attribute has three levels: f, t, nan(missing value). There are 16,922 rows with t, 150 rows with f and 2 rows with missing value.
- **host_identity_verified**: The attribute has three levels: f, t, nan(missing value). 16,922 rows with t, 150 rows with f and 2 rows with missing value. The host with host ID 21,348,778 has two listings in the area, and there is no information on this host except his/her host ID and URL.
- **neighbourhood**: There are 729 unique neighbourhoods of the listings. The maximum number of characters for the neighbourhood is 84 characters. There are 9 different languages for this attribute: English, Finnish, Indonesian, Dutch, Norwegian, Swahili, Thai, Tagalog, Vietnamese.
- **neighbourhood_cleansed**: After cleaning the neighbourhood data with geological information, there are now only 50 different neighbourhoods. The row counts for each neighbourhood is shown below in Figure 25, and the distribution is shown in Figure 26. The maximum number of characters for the neighbourhood is 20 characters.
![Figure 25 Level Count for neighbourhood_cleansed](Figure/Figure25.PNG)
![Figure 26 Distribution of neighbourhood_cleansed](Figure/Figure26.PNG)
- **neighbourhood_group_cleansed**: This attribute does not contain any information as it only has empty values.
- **latitude**: The latitude of the listing ranges from 13.5273 to 13.97355. The distribution has a bell-shape, which is similar to normal distribution. The five- number summary and the histogram are shown below in Figure 27 and Figure 28.
![Figure 27 Five-Number Summary of latitude](Figure/Figure27.PNG)
![Figure 28 Histogram of latitude](Figure/Figure28.PNG)
- **longitude**: The longitude of the listing ranges from 100.31062 to 100.92344. The distribution has a bell-shape, which is similar to normal distribution. The five-number summary and the histogram are shown below in Figure 29 and Figure 30.
![Figure 29 Five-Number Summary of longitude](Figure/Figure29.PNG)
![Figure 30 Histogram of longitude](Figure/Figure30.PNG)
- **property_type**: There are 86 types of property. The row counts for each property type is shown in Figure 31, and the distribution plot is in Figure 32. The most popular property type is entire rental unit, followed by entire condo. The maximum number of characters for property type is 34 characters.
![Figure 31 Level Counts for property_type](Figure/Figure31.PNG)
![Figure 32 Distribution of property_type](Figure/Figure32.PNG)
- **room_type**: There are 4 room types: entire home/apt, private room, hotel room and shared room. There are 9,224 rows with entire home/apt, 6,445 rows with private room, 794 rows with hotel room and 611 rows with shared room. The distribution plot is in Figure 33. The maximum number of characters for room type is 15 characters.
![Figure 33 Distribution of room_type](Figure/Figure33.PNG)
- **accommodates**: The maximum capacity of the listing ranges from 0 to 16 people. Most of the listings can accommodate less or equal to 5 people, while few listings can host up to 16 guests. This leads to a right skewed distribution as shown in Figure 34 and Figure 35 by the five-number summary and histogram.
![Figure 34 Five-Number Summary of accommodates](Figure/Figure34.PNG)
![Figure 35 Histogram of accommodates](Figure/Figure35.PNG)
- **bathrooms**: This attribute hosts no information as it is an empty column.
- **bathrooms_text**: There are 60 different descriptions of the bathrooms. The row count for each bathroom description is shown below in Figure 36, and the distribution plot is in Figure 37. The most popular bathroom description is 1 bath, followed by 1 private bath. The maximum number of characters for bathroom’s description is 17 characters.
![Figure 36 Level Counts for bathrooms_text](Figure/Figure36.PNG)
![Figure 37 Distribution of bathrooms_text](Figure/Figure37.PNG)
- **bedrooms**: The number of bedrooms that listings have ranges from 1 to 50. This leads to a very right skewed distribution as shown by the five-number summary and the histogram in Figure 38 and Figure 39. Most of the listings have 1 bedroom, while few listings have up to 50 bedrooms.
![Figure 38 Five-Number Summary of bedrooms](Figure/Figure38.PNG)
![Figure 39 Histogram of bedrooms](Figure/Figure39.PNG)
- **beds**: The number of beds that listings have ranges from 1 to 50. Most of the listings have 1 to 2 beds, while few listings have up to 50 beds. This leads to a very right skewed distribution as shown by the five-number summary and the histogram in Figure 40 and Figure 41.
![Figure 40 Five-Number Summary of beds](Figure/Figure40.PNG)
![Figure 41 Histogram of beds](Figure/Figure41.PNG)
- **amenities**: There are 13,880 unique description of amenities included in the listings. The maximum number of characters for amenities’ description is 1,606 characters.
- **price**: The listing’s price ranges from $0 to $1,500,000, and it does not match with the price in calendar.csv. The distribution of the price is highly skewed to the right as shown in five-number summary and the histogram in Figure 42 and Figure 43.
![Figure 42 Five-Number Summary of price](Figure/Figure42.PNG)
![Figure 43 Histogram of price](Figure/Figure43.PNG)
- **minimum_nights**: The minimum number of nights for the listing ranges from 1 to 1,125 days. Most of the listings have a minimum less or equal to 7 days. Its distribution is highly skewed to the right as shown through the five-number summary and the histogram in Figure 44 and Figure 45.
![Figure 44 Five-Number Summary of minimum_nights](Figure/Figure44.PNG)
![Figure 45 Histogram of minimum_nights](Figure/Figure45.PNG)
- **maximum_nights**: The maximum number of nights for the listing ranges from 1 to 100,000 days. Most of the listings have a maximum less or equal to 1,125 days. Its distribution is highly skewed to the right as shown through the five- number summary and the histogram in Figure 46 and Figure 47.
![Figure 46 Five-Number Summary of maximum_nights](Figure/Figure46.PNG)
![Figure 47 Histogram of maximum_nights](Figure/Figure47.PNG)





---

# 3. Data Processing


---

## 3.1. Processing Strategy


---

## 3.2. Calendar Relation


---

## 3.3. Transaction Relation


---

## 3.4. Reviews and Reviewer Relations


---

## 3.5. Listings, Scrape, Host, Host_pic, and Location Relations


---

## 3.6. Final Unicode Characters Processing


---

# 4. Data Storing


---

## 4.1. Requirement Analysis


---

## 4.2. Conceptual Design


---

## 4.3. Logical Design


---

## 4.4. BCNF Schema Reduction


---

## 4.5. Data Deployment at Relational Database


---

## 4.6. Data Deployment at Non-Relational Database


---

# 5. Data Retrieval


---

## 5.1. MySQL Queries


---

## 5.2. MongoDB Queries


---

## 5.3. Database System Comparison


---

# 6. Project Summary


---

## 6.1. Conclusion


---

## 6.2. Future Work



