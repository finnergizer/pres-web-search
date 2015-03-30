
#Tracking Techniques By Google
- Google Accounts
	- once logged into Google services, tracking cookie enables all of Google's services to uniquely identify each logged in user
- Large advertising networks
	- allow for cookie based tracking as seen in class

#Personalization
- 2011 Post on Google blog
	- language, geolocation, search history, Google+ connections being used to personalize web search
	- very little concrete information about how Google personalizes search results

#Terminology
- organic: pointing to a third party website
- primary link: main link in a result; most results contain >= 1 links
- Concern of Filter Bubble effect:  where users are only given results that the personalization algorithm thinks they want (while other, potentially important, results re- main hidden)

#Experiment Methodology - High Level
- create x google accounts that each vary by one specific feature
- execute q identicial queries once per day for d days
- save results from each query and compare to notice which google accounts provide different results and compare this to their features

#Sources of Noise
- updating the search index --> query results may change over time
- distributed infrastructure -->  different datacenters may house different indices with inconsistencies,
	- different results
- geolocation: IP address used to provide localized results
	- different subnets may receive different results
- A/B Testing
	- search engines experimenting with results and their click-through rates can cause different results to appear

#Noise - Carry-Over Effect
- A user searches for query A, followed by query B. Query A influences the results of Query B.
- Example: searching for hawaii, followed by urban outfitters
- Testing Carry-Over
	- In two different browsers, execute first query in one browser, followed by second query at later time
	- 1 indicates complete dissimilarity between sets of results from Query A and Query B
	- Figure 3 shows that After 10 minutes, sets are completely dissimilary
	- "test" followed by "touring"
	- Jacardian index shows dissimilarity between two result sets from queries
- **As a result, the remaining tests measuring personalization all ensured queries were executed 10 minutes apart**




#Noise - Eliminating
- Executed via normal Google search page vs Google Search API
- All machines (i.e. representing a different user) execute at the exact same time to avoid temporal changes in indices
- Using static DNS entries to direct all queries to a specific Google IP addresses
	- effectively removing the possibility of retrieving results from different datacenters
- Wait 11 minutes between queries to reduce carry-over effect
- All queries Sent from the same /24 subnet to avoid geolocation differences
- Control account
	- duplicate of one account in the set of x accounts
	- ensure that the duplicates retrieve the same results

#Implementation
- headless website testing with PhantomJS
- each machine is represented by a PhantomJS instance
- q=120, Table 1 shows distribution of categories for queries
	- must have breadth and impact (popular)
	- Google Zeitgeist provided trending search topics for all categories
- 27 year old female using Chrome 22 Windows 

# Real World Data
- AMT used, $2 per participant
- Each participant visited a web page that programmatically performed 80 queries
	- For each query performed, two PhantomJS instances were run to simulate a fresh account that acted as a control
- Results changed more significantly for participants who were Gmail, Google Maps, Youtube Users, than for control data
- Clearly, there is a personalization of web search contributing to the difference
- What features personalizes our results?
- Top 10 Queries vs. Least Personalized Queries
	- probability of AMT results equaling the control results, minus the probability of the control results equaling each other
	
#Personalization Features Metrics
- Jacard Index measuring similarity
	- size of the intersection of results over the size of the union
	- 1 indicates same results
	- 0 indicates no shared items between results
- Edit distance for measuring how ordering changed in results
	- the number of insertions, deletions, substitutions, swaps
	- Edit_Distance([a.com, b.com, c.com], [c.com, b.com]) = 2
	
	
	
#Measuring Personalization
- x+1 accounts where x is the number of options that a feature can take
	- x=3 for gender
	- "male", "female", "other"
	- additional "female" account for control
- each account searches on 120 queries

##Basic Cookie Tracking
- 4 accounts 
	- one logs into google and persists cookies
	- one doesn't log into google and persists cookle
	- two do not persist cookies (i.e. one is control)
- Jacard Index shows that results stay the same for all accounts
- Edit distance shows that logged in users receive on average results that are an edit distance of 2 from the control 
	- not persisting cookies
- Conclusion, cookies and being logged in can affect the order in which results are retrieved
	- first glimpse of how Google alters search results

##User Agent
- How the OS and browser choice can impact search results?
- No search personalization (i.e. reordering or difference in results) observed

##IP Address Geolocation
- How does Geolocation affect search personalization?
- Requests are first forwarded via SSH tunnels to different remote machines in 10 different states in the US
- Average Jacard Index for most non-control 
	- search results for most accounts differed by one result from the control 
- Edit distance for non-control is 2.12
	- result orders differ by at least 2 between machines in different locations
- Control was two accounts forwarding to Massachussets lab
- Conclusion, geolocation is used for personalizing search results
- Querying "pier one"
	- CA machine produced results including a local news story about a store grand opening in the area of CA
	- MA machine included a Google Maps link to a store in the area

## Google Account Attributes
- Created accounts differing by Gender
- Created accounts differeing only by age
- Jacardian Index of 1 indicates same results
- Edit distance ~0 indicates no reordering in these results
- Conclusion, no personalization based on Google Account information

##Historical Features
- Quantcast