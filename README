# in·cog
#### /inˈkäɡ,iNGˈkäɡ/ 
##### _having one's true identity concealed_
#
Incogly is an URL concealing service for shortening and renaming long URLs into more shareable short links.

## Features:
-	URL Shortening: convert long URLs into short links ideal for sharing
-	Custom short URLs: create custom link
-	Link engagement metrics: aggregate metrics such as URL redirections per day for targeted advertisements

## Requirements:
- [ ] Service shall create a short link against a long URL
- [ ] Clicking on a short link shall redirect to the original URL
- [ ] Service shall allow users to create custom short links with a maximum character limit of 12
- [ ] Service shall collect metrics
- [ ] Short links shall have a default timespan of 3 months until expiration.
- [ ] Service shall allow registered users to specify expiration time for each short link
- [ ] Service shall be highly available
- [ ] URL redirection shall be executed in real-time

## Estimations:
The service will be read-heavy with more short link redirection requests than new short link creation requests.  A 100:1 read write ratio is assumed
Assuming the service receives 10 million new short link requests per month, then 1 billion short link redirections are expected.
-	100 * 10,000,000 = 1,000,000,000 redirections
-	10,000,000 short links/month / (30 days * 24 hours * 3600 seconds) = ~4 new short link every second
-	100 * 4 URLs/sec = 400 redirections/sec

Short links are stored for 3 months by default.  Assume short links can be stored up to 3 years.  The total number of short links to store is 600 million.
-	10,000,000 short links/month * 12 months/year * 3 years = 360,000,000 short links

Assume 277 Bytes for each short link record.  The recommended link size is 2000 characters or 2000 Bytes when encoded to ASCII.  255 Bytes is used as the average URL length is approximately 77 characters based on https://www.supermind.org/blog/740/average-length-of-a-url-part-2.  100 GB of storage is required to store short link records for 3 years.
-	Short link: 12 Bytes
-	Expiration date: 5 Bytes
-	Creation date: 5 Bytes
-	Original URL: 255 Bytes
-	360,000,000 records * 277 Bytes/record = ~100 GB

For write requests, the total incoming data will be 10 KB per second
-	4 * 277 = ~1.1 KB/sec

For read requests, the total outgoing data will be 1 MB per second
-	400 * 277 = 110.8KB/sec

To improve performance, frequently accessed short links are cached using the 80-20 rule.  20% of the short links generate 80% of traffic.  The 20% of the most accessed short links are cached.
-	400 * 3600 seconds * 24 hours = 34,560,000 requests/day
-	0.2 * 34,560,000 * 277 Bytes = ~2GB of memory

## Estimates:
**Short links created:** 4/s
**Short links created in 3 years:** 360 million
**Redirections:** 400/s
**Storage for 3 years:** 100GB
**Cache size:** 2GB

## Short link encoding:
##### Base-62 encoding
Using counter for rapid generation of unique short link
Limit to 7 characters. 12 Characters is for custom short links.

## Interface
REST APIs will expose the functionality of the service.
##### Create short link API
Endpoint: POST
-	##### Parameter:
	**original_url (string, required):** the original URL before shortening
	**custom_link (string, optional):** the custom link for the short link if the user wants to specify
	**expiration_date (timestamp, optional):** the date and time the short link should expire.
	**user_id (string, optional):** the ID of the user creating the short link if applicable.  No User ID defaults to generic guest ID
-	##### Response:
	**short_link (string):** the short link based on the original url
	**creation_date (timestamp):** the timestamp of when the short link is creased
	**expiration_date (timestamp):** the timestamp of when the short link expires
##### Redirect API
Endpoint: GET
-	##### Parameter:
	**short_link (string, required):** the short link that needs to be resolved to the original URL
-	##### Response:
	Redirects to the original_url
##### Get short links API
Endpoint: GET
-	##### Parameter:
	**user_id (string, required):** the User ID of short links requested
-	##### Response:
	**urls (list):** a list of all short links created by user_id
##### Delete short link API
Endpoint: DELETE
-	##### Parameter:
	**short_link (string, required):** ahe short link to delete
	**user_id (string, required):** ahe User ID requesting to delete
-	##### Response:
	**status (string):** the status of delete request. OK or error
##### Metrics API
Endpoint: GET
-	##### Parameter:
	**short_link (string, required):** the short link which metrics is requested
	**start_date (timestamp, optional):** the start date for metrics 
	**end_date (timestamp, optional):** the end date for metrics
-	##### Response:
    **click_count (int):** the number of times the short link has been clicked

## Database Schema:
Two tables are required.  One for storing user information and one for the original URL and short link mapping
-	##### User information
	**User ID:** a unique user ID
	**Email:** email of the user
	**Creation Date:** timestamp of when the user ID was registered
-	##### Short link mapping
	**Short Link:** 7 character long unique URL
	**Original URL:** the original long URL
	**User ID:** the user ID of the user who created the short link