# 1. Introduction
Throughout the course of this project, I gained valuable insights on database management system and retrieval languages. In this opportunity, I embarks on journey to implement a project to design database based on AirBnB dataset provided.

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

![Figure1_2](Figure/Figure1_2.png)

- **adjusted_price**: The adjusted price ranges from $165.0 to $1,500,000.0, with a median of $1,200.0. There are 333,341 rows with adjusted price different from the listed price. 329,672 rows have higher price than adjusted price, which is 98.9% of the time the host will set a lower adjusted price; 3,669 rows have lower price than adjusted price, which is 1.1% of the time the host will set a higher adjusted price. The maximum price increase is $20,000.0, and the maximum price decrease is $2,370.0. The distribution of the adjusted price is very similar to that of the price as shown in Figure 3 and Figure 4 through five-number summary and histogram.

![Figure3_4](Figure/Figure3_4.png)

- **minimum_nights**: The minimum nights a guest can book on a particular date ranges from 1 day to 1,125 days, with a median of 1 day. Out of the 6,231,465 rows, there are 3,468,431 rows with minimum nights of 1 day, which is 55.7% of the rows. Most of the listings have small minimum nights requirement, but there are some with around 200 days and 400 days and few even higher, which leads to a highly skewed to the right distribution. The five-number summary and histogram of minimum nights is shown in Figure 5 and Figure 6.

![Figure5_6](Figure/Figure5_6.png)

- **maximum_nights**: The maximum nights a guest can book on a particular date ranges from 1 day to 2,147,483,647 days. Examining the quantiles through Figure 7 reveals a highly skewed to the right distribution, and the same result is shown in the histogram in Figure 8.

![Figure_7_8](Figure/Figure7_8.png)

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

![Figure9](Figure/Figure9.png)

- **reviewer_id**: There are 210,367 unique reviewer IDs. Comparing with the 246,644 rows in reviews.csv, there are reviews left by the same reviewer, either for the same listing or different listing (240,229 reviews with different listing and reviewer), on the same date or not (246,613 reviews with different listing, reviewer and date).
- **reviewer_name**: The maximum number of characters for reviewer’s name is 42 characters. There are 43 different languages in the reviewer’s name, where the language detection is done using python package langdetect. The detection returns a list of languages in ISO 639-1 codes (“List of ISO 639-1 codes,” 2022), where their corresponding languages are shown in Figure 10.

![Figure10](Figure/Figure10.png)

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

![Figure11](Figure/Figure11.png)

- **description**: There are duplicate description for the listings as there are only 13,298 unique listing description. The maximum number of characters for description is 1,000 characters. There are 29 different languages in the description of the listing as shown in Figure 12.

![Figure12](Figure/Figure12.png)

- **neighborhood_overview**: There are duplicate neighbourhood description for the listings as there are only 6002 unique neighbourhood description. The maximum number of characters for neighbourhood description is 1000 characters. There are 31 different languages in the neighbourhood description of the listing as shown in Figure 13.

![Figure13](Figure/Figure13.png)

- **picture_url**: There are 15,799 unique picture URLs. The maximum number of characters for the URL that hosts the first image of the listing is 122 characters.
- **host_id**: There are 7,639 unique host IDs, meaning there are 7,639 different hosts in total. The maximum number of digits for the host's ID is 9 digits.
- **host_url**: There are 7,639 unique host URLs, which is of the same number of the unique host IDs. The maximum number of characters for the host’s URL is 42 characters.
- **host_name**: There are 5,991 unique host names. Comparing the number with the number of hosts in the area, there are hosts with the same name. The maximum number of characters for the host’s name is 35 characters. There are 37 different languages in the host’s name as shown in Figure 14.

![Figure14](Figure/Figure14.png)

- **host_since**: The date that the host was created spans from December 02, 2009 to June 17, 2022. The distribution of the host creation date is shown in Figure 15, and it follows somewhat bimodal distribution with the highest number of host creation around the year of 2015 and 2016 and the year of 2018 and 2019.

![Figure15](Figure/Figure15.png)

- **host_location**: For the host location, there are 632 unique host locations. Some of the locations more detailed with district and city information while others just have country. The maximum number of characters for the host’s self- reported location is 125 characters. There are 27 different languages in the host’s location as shown in Figure 16.

![Figure16](Figure/Figure16.png)

- **host_about**: There are 3,305 unique host’s self-description. The maximum number of characters for the host’s self-description is 5,893 characters. There are 27 different languages in the host’s self-description as shown in Figure 17.

![Figure17](Figure/Figure17.png)

- **host_response_time**: The host’s response time has five levels: within an hour, within a few hours, within a day, a few days or more, nan(missing values). There are 7,033 listings with host’s response time within an hour, 1,420 listings of within a few hours, 1,252 listings of within a day, 1,740 listing of a few days or more, 5,629 of rows with missing value as shown in Figure 18. The maximum number of characters for the host’s response time is 18 characters.

![Figure18](Figure/Figure18.png)

- **host_response_rate**: There are 54 levels of host’s response rate. The attribute is of string data type in the csv file, but it is actually capturing numerical information. The host’s response time ranges from 0% to 100% with the majority of the listings have a host’s response rate of 100%. Figure 19 shows that the distribution of the host’s response rate is highly skewed to the right as a few listings have very low host’s response rate.

![Figure19](Figure/Figure19.png)

- **host_acceptance_rate**: There are 70 levels of host’s response rate. The attribute is of string data type in the csv file, but it is actually capturing numerical information. The host’s acceptance time ranges from 0% to 100% with the majority of the listings have a host’s acceptance rate of 100%. Figure 20 shows that the distribution of the host’s acceptance rate is highly skewed to the right as a few listings have very low host’s acceptance rate.

![Figure20](Figure/Figure20.png)

- **host_is_superhost**: There are three levels: f, t, nan(missing value). There are 15235 rows with f, 1837 rows with t and 2 rows with missing value.
- **host_thumbnail_url**: There are 7,557 unique host's profile pictures (displayed in small) on the listing page. The maximum number of characters for the host’s thumbnail URL is 106 characters.
- **host_picture_url**: There are 7,557 unique host's profile pictures (displayed in medium) on the listing page. The maximum number of characters for the host’s thumbnail URL is 109 characters.
- **host_neighbourhood**: There are 89 unique neighbourhood that the host lives in. The maximum number of characters for these neighbourhoods is 28 characters. There are 20 different languages in these neighbourhoods as shown in Figure 21.

![Figure21](Figure/Figure21.png)

- **host_listings_count**: The number of listings that the host has ranges from 0 listing to 422 listings with a median of 3 listings. With few very large host's listing count, its distribution is highly skewed to the right as shown in Figure 22 and Figure 23 through the five-number summary and the histogram.

![Figure22_23](Figure/Figure22_23.png)

- **host_verifications**: There are three ways that a host can confirm identity: phone, email and work email. Hence there are 8 levels in total: 6 combinations of the three ways, no verification and no information on the verification. There are 12,773 rows with verified email and phone, 2,168 rows with verified email, phone and work email, 1,916 rows with verified phone, 108 rows with verified phone and work email, 60 rows with no verification, 45 rows with verified email, 2 rows with verified work_email and 2 rows with no information on verification. The distribution can be seen in Figure 24. The maximum number of characters for the host’s verification is 32 characters.

![Figure24](Figure/Figure24.png)

- **host_has_profile_pic**: The attribute has three levels: f, t, nan(missing value). There are 16,922 rows with t, 150 rows with f and 2 rows with missing value.
- **host_identity_verified**: The attribute has three levels: f, t, nan(missing value). 16,922 rows with t, 150 rows with f and 2 rows with missing value. The host with host ID 21,348,778 has two listings in the area, and there is no information on this host except his/her host ID and URL.
- **neighbourhood**: There are 729 unique neighbourhoods of the listings. The maximum number of characters for the neighbourhood is 84 characters. There are 9 different languages for this attribute: English, Finnish, Indonesian, Dutch, Norwegian, Swahili, Thai, Tagalog, Vietnamese.
- **neighbourhood_cleansed**: After cleaning the neighbourhood data with geological information, there are now only 50 different neighbourhoods. The row counts for each neighbourhood is shown below in Figure 25, and the distribution is shown in Figure 26. The maximum number of characters for the neighbourhood is 20 characters.

![Figure25_26](Figure/Figure25_26.png)

- **neighbourhood_group_cleansed**: This attribute does not contain any information as it only has empty values.
- **latitude**: The latitude of the listing ranges from 13.5273 to 13.97355. The distribution has a bell-shape, which is similar to normal distribution. The five- number summary and the histogram are shown below in Figure 27 and Figure 28.

![Figure27_28](Figure/Figure27_28.png)

- **longitude**: The longitude of the listing ranges from 100.31062 to 100.92344. The distribution has a bell-shape, which is similar to normal distribution. The five-number summary and the histogram are shown below in Figure 29 and Figure 30.

![Figure29_30](Figure/Figure29_30.png)

- **property_type**: There are 86 types of property. The row counts for each property type is shown in Figure 31, and the distribution plot is in Figure 32. The most popular property type is entire rental unit, followed by entire condo. The maximum number of characters for property type is 34 characters.

![Figure31_32](Figure/Figure31_32.png)

- **room_type**: There are 4 room types: entire home/apt, private room, hotel room and shared room. There are 9,224 rows with entire home/apt, 6,445 rows with private room, 794 rows with hotel room and 611 rows with shared room. The distribution plot is in Figure 33. The maximum number of characters for room type is 15 characters.

![Figure33](Figure/Figure33.png)

- **accommodates**: The maximum capacity of the listing ranges from 0 to 16 people. Most of the listings can accommodate less or equal to 5 people, while few listings can host up to 16 guests. This leads to a right skewed distribution as shown in Figure 34 and Figure 35 by the five-number summary and histogram.

![Figure34_35](Figure/Figure34_35.png)

- **bathrooms**: This attribute hosts no information as it is an empty column.
- **bathrooms_text**: There are 60 different descriptions of the bathrooms. The row count for each bathroom description is shown below in Figure 36, and the distribution plot is in Figure 37. The most popular bathroom description is 1 bath, followed by 1 private bath. The maximum number of characters for bathroom’s description is 17 characters.

![Figure36_37](Figure/Figure36_37.png)

- **bedrooms**: The number of bedrooms that listings have ranges from 1 to 50. This leads to a very right skewed distribution as shown by the five-number summary and the histogram in Figure 38 and Figure 39. Most of the listings have 1 bedroom, while few listings have up to 50 bedrooms.

![Figure38_39](Figure/Figure38_39.png)

- **beds**: The number of beds that listings have ranges from 1 to 50. Most of the listings have 1 to 2 beds, while few listings have up to 50 beds. This leads to a very right skewed distribution as shown by the five-number summary and the histogram in Figure 40 and Figure 41.

![Figure40_41](Figure/Figure40_41.png)

- **amenities**: There are 13,880 unique description of amenities included in the listings. The maximum number of characters for amenities’ description is 1,606 characters.
- **price**: The listing’s price ranges from $0 to $1,500,000, and it does not match with the price in calendar.csv. The distribution of the price is highly skewed to the right as shown in five-number summary and the histogram in Figure 42 and Figure 43.

![Figure42_43](Figure/Figure42_43.png)

- **minimum_nights**: The minimum number of nights for the listing ranges from 1 to 1,125 days. Most of the listings have a minimum less or equal to 7 days. Its distribution is highly skewed to the right as shown through the five-number summary and the histogram in Figure 44 and Figure 45.

![Figure44_45](Figure/Figure44_45.png)

- **maximum_nights**: The maximum number of nights for the listing ranges from 1 to 100,000 days. Most of the listings have a maximum less or equal to 1,125 days. Its distribution is highly skewed to the right as shown through the five- number summary and the histogram in Figure 46 and Figure 47.

![Figure46_47](Figure/Figure46_47.png)

- **minimum_minimum_nights**: The smallest minimum nights of the listing (looking 365 nights in the future in calendar) ranges from 1 to 1,125 days. Most of the listings have a minimum less or equal 7 days. Its distribution is highly skewed to the right as shown in the five-number summary and the histogram in Figure 48 and Figure 49. There are 16,793 listings that have the same value of smallest minimum nights value from the calendar (looking 365 nights in the future) with their current minimum nights value.

![Figure48_49](Figure/Figure48_49.png)

- **maximum_minimum_nights**: The largest minimum nights of the listing (looking 365 nights in the future in calendar) ranges from 1 to 1,125 days. Most of the listings have a minimum less or equal to 7 days. Its distribution is highly skewed to the right as shown in the five-number summary and the histogram in Figure 50 and Figure 51. There are 16,773 listings that have the same value of largest minimum_nights value from the calender (looking 365 nights in the future) with their current minimum_nights.

![Figure50_51](Figure/Figure50_51.png)

- **minimum_maximum_nights**: The smallest maximum nights of the listing (looking 365 nights in the future in calendar) ranges from 1 to 2,147,483,647 days. Most of the listings have a maximum less or equal to 1,125 days. Its distribution is highly skewed to the right as shown in the five-number summary and the histogram in Figure 52 and Figure 53. There are 14801 listings that have the same value of smallest maximum nights value from the calendar (looking 365 nights in the future) with their current maximum nights value.

![Figure52_53](Figure/Figure52_53.png)

- **maximum_maximum_nights**: The largest maximum nights of the listing (looking 365 nights in the future in calendar) ranges from 1 to 2,147,483,647 days. Most of the listings have a maximum less or equal to 1,125 days. Its distribution is highly skewed to the right as shown in the five-number summary and the histogram in Figure 54 and Figure 55. There are 14961 listings that have the same value of largest maximum nights value from the calendar (looking 365 nights in the future) with their current maximum nights value.

![Figure54_55](Figure/Figure54_55.png)

- **minimum_nights_avg_ntm**: The average minimum nights of the listing (looking 365 nights in the future in calendar) ranges from 1 to 1,125 days. Most of the listings have a minimum less or equal to 7 days. Its distribution is highly skewed to the right as shown in the five-number summary and the histogram in Figure 56 and Figure 57.

![Figure56_57](Figure/Figure56_57.png)

- **maximum_nights_avg_ntm**: The average maximum nights of the listing (looking 365 nights in the future in calendar) ranges from 1 to 2,147,483,647 days. Most of the listings have a maximum less or equal to 1,125 days. Its distribution is highly skewed to the right as shown in the five-number summary and the histogram in Figure 58 and Figure 59.

![Figure58_59](Figure/Figure58_59.png)

- **calendar_updated**: This attribute does not contain any information since this is an empty column.
- **has_availability**: This attribute has two levels: t and f, corresponding to whether the listing has availability or not in the next 365 days. There are 17,004 listings with availability in the next 365 days based on calendar, and 70 listings not.
- **availability_30**: The availability of the listing in the next 30 days ranges from 0 to 30 days. With a median of 29 days and a third quartile of 30 days, most of the listings have around 30 days of availability in the next 30 days. The five- number summary and the histogram of the attribute are shown below in Figure 60 and Figure 61.

![Figure60_61](Figure/Figure60_61.png)

- **availability_60**: The availability of the listing in the next 60 days ranges from 0 to 60 days. With a median of 59 days and a third quartile of 60 days, most of the listings have around 60 days of availability in the next 60 days. The five- number summary and the histogram of the attribute are shown below in Figure 62 and Figure 63.

![Figure62_63](Figure/Figure62_63.png)

- **availability_90**: The availability of the listing in the next 90 days ranges from 0 to 90 days. With a median of 89 days and a third quartile of 90 days, most of the listings have around 90 days of availability in the next 90 days. The five- number summary and the histogram of the attribute are shown below in Figure 64 and Figure 65.

![Figure64_65](Figure/Figure64_65.png)

- **availability_365**: The availability of the listing in the next 365 days ranges from 0 to 365 days. With a median of 328 days and a third quartile of 365 days, most of the listings have around 365 days of availability in the next 365 days, with few around 0 days, 100 days and 150 days. The five-number summary and the histogram of the attribute are shown below in Figure 66 and Figure 67.

![Figure66_67](Figure/Figure66_67.png)

- **calendar_last_scraped**: The calendar was last scraped on June 20 and June 21 of the year 2022. There are 14,753 listings that were scraped on June 20, 2022, and 2321 listings were scraped on June 21, 2022.
- **number_of_reviews**: The number of reviews the listing has ranges from 0 to 1,119 reviews. Most of the listings have less than 10 reviews, but few can up have to 1,119 reviews based on the result from the five-number summary and the histogram of attribute shown in Figure 68 and Figure 69. The distribution is highly skewed to the right.

![Figure68_69](Figure/Figure68_69.png)

- **number_of_reviews_ltm**: The number of reviews the listing has in the last 12 months ranges from 0 to 112 reviews. Most of the listings have 0 reviews in the last year, but few can up have to 112 reviews based on the result from the five-number summary and the histogram of attribute shown in Figure 70 and Figure 71. The distribution is highly skewed to the right.

![Figure70_71](Figure/Figure70_71.png)

- **number_of_reviews_l30d**: The number of reviews the listing has in the last 30 days ranges from 0 to 23 reviews. Most of the listings have 0 reviews in the last 30 days, but few can up have to 23 reviews based on the result from the five-number summary and the histogram of attribute shown in Figure 72 and Figure 73. The distribution is highly skewed to the right.

![Figure72_73](Figure/Figure72_73.png)

- **first_review**: The date of the first review spans from February 09, 2011 to June 21, 2022. There are 10,012 listings with information on the first review’s date.
- **last_review**: The date of the last review spans from December 15, 2012 to June 21, 2022. There are 10,012 listings with information on the last review’s date, and there are 1,873 listings with same first review and last review date.
- **review_scores_rating**: The overall score rating of the listing by all reviews ranges from 0 to 5. From the five-number summary and the histogram of the attribute in Figure 74 and Figure 75, most of the listings have score of 4 and above, and the distribution is highly skewed to the left.

![Figure74_75](Figure/Figure74_75.png)

- **review_scores_accuracy**: The accuracy score rating of the listing by all reviews ranges from 0 to 5. Only 9,786 listings out of the 10,012 listings that have reviews have review score for accuracy. From the five-number summary and the histogram of the attribute in Figure 76 and Figure 77, most of the listings have score of 4 and above, and the distribution is highly skewed to the left.

![Figure76_77](Figure/Figure76_77.png)

- **review_scores_cleanliness**: The cleanliness score rating of the listing by all reviews ranges from 0 to 5. Only 9,786 listings out of the 10,012 listings that have reviews have review score for cleanliness. From the five-number summary and the histogram of the attribute in Figure 78 and Figure 79, most of the listings have score of 4 and above, and the distribution is highly skewed to the left.

![Figure78_79](Figure/Figure78_79.png)

- **review_scores_checkin**: The check-in score rating of the listing by all reviews ranges from 0 to 5. Only 9,780 listings out of the 10,012 listings that have reviews have review score for check-in. From the five-number summary and the histogram of the attribute in Figure 80 and Figure 81, most of the listings have score of 4 and above, and the distribution is highly skewed to the left.

![Figure80_81](Figure/Figure80_81.png)

- **review_scores_communication**: The communication score rating of the listing by all reviews ranges from 0 to 5. Only 9,785 listings out of the 10,012 listings that have reviews have review score for communication. From the five- number summary and the histogram of the attribute in Figure 82 and Figure 83, most of the listings have score of 4 and above, and the distribution is highly skewed to the left.

![Figure82_83](Figure/Figure82_83.png)

- **review_scores_location**: The location score rating of the listing by all reviews ranges from 0 to 5. Only 9,779 listings out of the 10,012 listings that have reviews have review score for location. From the five-number summary and the histogram of the attribute in Figure 84 and Figure 85, most of the listings have score of 4 and above, and the distribution is highly skewed to the left.

![Figure84_85](Figure/Figure84_85.png)

- **review_scores_value**: The value score rating of the listing by all reviews ranges from 0 to 5. Only 9,778 listings out of the 10,012 listings that have reviews have review score for value. From the five-number summary and the histogram of the attribute in Figure 86 and Figure 87, most of the listings have score of 4 and above, and the distribution is highly skewed to the left.

![Figure86_87](Figure/Figure86_87.png)

- **license**: This attribute contains no information since it is an empty column.
- **instant_bookable**: There are two levels: t and f. 11,376 listings are instant bookable while 5,698 listings are not.
- **calculated_host_listings_count**: The number of listings the host has in the current scrape ranges from 1 to 195, which is different from host_listings_count. Most of the hosts have less or equal to 11 listings, but some have up to 195 listings. The distribution is highly skewed to the right as shown by the five-number summary and the histogram in Figure 88 and Figure 89.

![Figure88_89](Figure/Figure88_89.png)

- **calculated_host_listings_count_entire_homes**: The number of entire home/apt listings the host has in the current scrape ranges from 0 to 195. Most of the hosts have less or equal to 5 listings of entire home/apt, but some have up to 195 listings. The distribution is highly skewed to the right as shown by the five-number summary and the histogram in Figure 90 and Figure 91.

![Figure90_91](Figure/Figure90_91.png)

- **calculated_host_listings_count_private_rooms**: The number of private rooms listings the host has in the current scrape ranges from 0 to 195. Most of the hosts have less or equal to 2 listings of private rooms, but some have up to 195 listings. The distribution is highly skewed to the right as shown by the five- number summary and the histogram in Figure 92 and Figure 93.

![Figure92_93](Figure/Figure92_93.png)

- **calculated_host_listings_count_shared_rooms**: The number of shared rooms listings the host has in the current scrape ranges from 0 to 62. Most of the hosts have less or equal to 0 listing of shared rooms, but some have up to 62 listings. The distribution is highly skewed to the right as shown by the five- number summary and the histogram in Figure 94 and Figure 95.

![Figure94_95](Figure/Figure94_95.png)

- **reviews_per_month**: The attribute ranges from 0.01 to 19.42. Most of the hosts have less than 1 review per month. The distribution is highly skewed to the right as shown in the five-number summary and the histogram in Figure 96 and Figure 97.

![Figure96_97](Figure/Figure96_97.png)

The attribute constraints, potential primary keys and foreign keys are as follows:
- primary key(id)
- listing_url not null
- host_id not null (in future separation to host table, host_id is the primary key)
- host_url not null
- check (host_is_superhost in ('t', 'f'))
- check (host_listings_count >= 0)
- check (host_total_listings_count >= 0)
- check (host_has_profile_pic in ('t', 'f'))
- check (host_identity_verified in ('t', 'f'))
- check (latitude >= 0)
- check (longitude >= 0)
- check (room_type in ('Entire home/apt', 'Private room', 'Hotel room', 'Shared room'))
- check (accommodates >= 0)
- check (bedrooms >= 1)
- check (beds >= 1)
- check (price >= 0)
- check (minimum_nights >= 1)
- check (maximum_nights >= 1)
- check (minimum_minimum_nights >= 1)
- check (maximum_minimum_nights >= 1)
- check (minimum_maximum_nights >= 1)
- check (maximum_maximum_nights >= 1)
- check (minimum_nights_avg_ntm >= 1)
- check (maximum_nights_avg_ntm >= 1)
- check (has_availability in ('t', 'f'))
- check (availability_30 >= 0)
- check (availability_60 >= 0)
- check (availability_90 >= 0)
- check (availability_365 >= 0)
- check (number_of_reviews >= 0)
- check (number_of_reviews_ltm >= 0)
- check (number_of_reviews_l30d >= 0)
- check (review_scores_rating >= 0)
- check (review_scores_accuracy >= 0)
- check (review_scores_cleanliness >= 0)
- check (review_scores_checkin >= 0)
- check (review_scores_communication >= 0)
- check (review_scores_location >= 0)
- check (review_scores_value >= 0)
- check (instant_bookable in ('t', 'f'))
- check (calculated_host_listings_count >= 1)
- check (calculated_host_listings_count_entire_homes >= 0)
- check (calculated_host_listings_count_private_rooms >= 0)
- check (calculated_host_listings_count_shared_rooms >= 0)
- check (reviews_per_month >= 0)

---

# 3. Data Processing
Upon performing in-depth data analysis in previous section, AirBnB dataset undergoes processing stage to clean unexpected data like duplicated values, inconsistent number of attributes, and presence of multiple non-English languages.

---

## 3.1. Processing Strategy
There are few strategies involved for cleaning the dataset which will be discussed in this section:
- When a particular record is null (i.e. missing), it will be replaced with value which logically making sense and following attribute data type discussed in data analysis in previous section. This is done to ensure there is no null entries on all dataset.
- Records with duplicated values signify that the table is not in BCNF form and therefore schema reduction will be performed and discussed elaborately in next section for data storing.
- For attributes which are of “free-text” in nature (e.g. ‘comments’ attribute in reviews table, ‘description’ attribute in listings table, etc), all characters that does not conform to ASCII and UTF-8 encoding will be deprecated and replaced with whitespaces. This is done to ensure data importability to database management systems with strict encoding type like MySQL. For simplification, same approach is implemented for data deployment at MongoDB.
- The presence of multiple languages in the dataset imposes difficulty for data deployment at traditional relational database management system. Therefore, non-English characters, emoticons, and any other special characters belonging to extended Unicode character set would also be replaced with whitespaces.
- Summary files such as listings_summary.csv and reviews_summary.csv only capture table snapshot of key lingtings.csv and reviews.csv files with exact same number of rows but reduced number of columns. This is why these summary files will be ignored for this project.

Based on aforementioned strategies, I will discuss my exact implementation on all attributes which are altered during data processing for each individual tables in next subsequent subsection under current Section 3.

---

## 3.2. Calendar Relation
First, I need to check if there are rows containing null values in calendar.csv through execution of below code chunk. The result prints the attribute name, data type, and the number of null records respectively. From the result below, there are 2 records under ‘minimum_nights’ and ‘maximum_nights’ columns with null values. They will be backfilled with integer 1. The output of this processed calendar would be named as 1_calendar.csv.

![Figure98](Figure/Figure98.png)

Next processing step for calendar relation would be to convert price attribute under calendar relation from initially string data type to float data type. There are two generic cases under price attribute here such as $870.00 and “$100,000.00” which would be converted into 870.00 and 100000.00 by getting rid of dollar sign ($), double quotation sign (“ “), and comma sign for those prices greater than 999.99 dollars. If this comma sign is not removed, they will introduce a new column in calendar relation once dollar sign and double quotation sign are removed which is not desired. See below code snippet which generates 2_calendar.csv from input 1_calendar.csv file earlier.

![Figure99](Figure/Figure99.png)

---

## 3.3. Transaction Relation
Transaction relation is derived from processed calendar.csv discussed previously. If I carefully take a look the records in the calendar dataset, I can see that under each listing_id, there will be a range of dates and availability status with ‘f’ indicating that the place is occupied and ‘t’ indicating that the place is available for rent.
The idea to derive transaction relation is to leverage only on two attributes from calendar, namely, dates and available. For each listing_id, I would be keeping track of checkin date and checkout date. Checkin date is defined as the date when available column turns from consecutive status ‘t’ to ‘f’, while checkout date is defined as the date when available column turns from consecutive status ‘f’ to ‘t’. Occupancy period can then be calculated by taking difference between checkin date and checkout date. If checkin date and checkout date is the same, I will assume that occupancy period is 1 day.
From above model, I proceeded to do text processing using code snippet below to read 1_calendar.csv and to generate two interim output files called 1_checkin.csv and 1_checkout.csv. Both these interim files have same set of attributes as calendar relation. The only exception is that attribute ‘dates’ in checkin.csv reflects checkin date while ‘dates’ in checkout.csv reflects checkout date.

![Figura100](Figure/Figure100.png)


Next step would be to read said 1_checkin.csv and 1_checkout.csv into a pandas dataframe and calculate a new attribute occupancy_period which equals to ‘dates’ from 1_checkout.csv subtracted by ‘dates’ from 1_checkin.csv. Based on BCNF schema reduction discussed in section 4 for data storing, it is concluded that transaction relation can be reduced to only consists of attributes listing_id, checkin_date, and occupancy_period so that it is in BCNF form. The final transaction file is saved as 2_transaction.csv. Code snippet below illustrates steps taken to generate 2_transaction.csv based on 1_checkin.csv and 1_checkout.csv

![Figura101](Figure/Figure101.png)

---

## 3.4. Reviews and Reviewer Relations
On reviews relation, first step is the same which is to check if there is any null values using check_null() function described in Figure 98 earlier. I can see that there are 2 records under attribute reviewer_name which are missing and there are 24 missing entries under attribute comments. For these 26 missing entries, they will be backfilled with single whitespace since original data type is string.

![Figura102](Figure/Figure102.png)

Next step would be to replace every single non-ASCII characters in attributes reviewer_name and comments with whitespaces by defining a function called clean_nonasscii().

![Figura103](Figure/Figure103.png)

Based on BCNF schema reduction discussed in section 4, there are duplicated records on reviewer_name and therefore a new relation called reviewer will be created which consists of only two attributes, namely, reviewer_id and reviewer_name. Final list of attributes in reviews relation would be id, listing_id, reviewer_id, date, and comments. This last step of processing for reviews and reviewer relation can be seen in below code snippet. The output files are 2_reviews.csv and 2_reviewer.csv

![Figura104](Figure/Figure104.png)

---

## 3.5. Listings, Scrape, Host, Host_pic, and Location Relations
Let’s start the processing discussion in this subsection with listings relation first. Upon executing check_null() function from Figure 98 earlier, a list of attributes with null records are generated. Each column has different approach to backfill missing entries. Detailed list can be seen in Figure 105 below.
• For most attributes having string data type, missing entries are backfilled with single whitespace. The only exception would be URL-related attribute being backfilled with string “None”, host_response_time attribute being backfilled with 72 (to indicate more than 3-day response, 72 hours), host_has_profile_pic & host_identity_verify being backfilled with ‘f’, and bathrooms_text being backfilled with string “1 bathroom”.
• For attributes having date data type, missing entries would be backfilled with date 2008-08-11 which is the launch date of AirBnB platform application to public. Affected attributes are host_since, first_review, and last_review.
• For attributes having float or int data type, missing entries are handled in case- by-case basis. Attributes related to rate, review scores, licence, calendar_updated, and neighbourhood_group_cleansed are backfilled with 0. Attributes related to count parameters such as host_listings_count, host_total_listings_count, bedrooms, and beds are backfilled with 1 since each listing must logically have at least 1 on these attributes.

![Figura105](Figure/Figure105.png)

In Figure 106 below, it provides detailed implementation on null entries backfilling based on action description in Figure 105. On top of that, there are several value update to original non-null records on few columns.
• Attribute host_response_time applies update_responsetime() function to change the value in such a way that string “within an hour”, “within a day”, and “within a few hours” will be converted to integer 1, 24, and 3 respectively. This is done to ease query pertaining host responsiveness in the later part for query implementation.
• Attribute host_response_rate and host_acceptance_rate apply update_responserate() function to remove percentage sign (%) for ease of query execution.
• Attribute bathrooms applies derive_bathroom() function to fetch the number of bathrooms denoted in text-form in attribute bathrooms_text which is of string data type.
• Attribute price applies update_price() function to remove special characters in the value so that the price can be in float data type.

![Figura106](Figure/Figure106.png)

Last step of listings relation processing is basically to refer back to BCNF schema reduction discussed in section 4 where multiple intrinsic functional dependencies residing in listing relation are separated out as standalone and separate relations. In this case, there are 4 relations extracted out form listings relation which are scrape relation, location relation, host relation, and host_pic relation. Other than candidate keys of said 4 relations, attributes belonging to these 4 relations would be dropped from original listings relation. Further, based on respective candidate keys, these 4 relations will drop duplicated record entries so as to make the dataset unique. The output of below Figure 107 would be 2_listings.csv, 2_scrape.csv, 2_location.csv, 2_host.csv, and 2_host_pic.csv.

![Figura107](Figure/Figure107.png)

---

## 3.6. Final Unicode Characters Processing
At this point, I have in total of 9 relation files which are 2_calendar.csv, 2_transaction.csv, 2_reviews.csv, 2_reviewer.csv, 2_listings.csv, 2_host.csv, 2_host_pic.csv, 2_scrape.csv, and 2_location.csv. The prefix “2_” in the file names indicate that they are final version of processed files. Before deploying these CSV files into relational database and non-relational database, last processing step is to convert all remaining non-ASCII characters into whitespaces as can be seen in below code snippet. All the files would be overwritten to same file names respectively based on the output of said last processing step.

![Figura108](Figure/Figure108.png)

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



