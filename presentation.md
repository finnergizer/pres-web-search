# Demonstrating Real-World Personalization

- Begin by measuring the extent of personalization that typical users see today as a baseline

	- 200 Amazon Mechanical Turk Workers that use various Google Services visited a site that automatically searched 80 queries from randomly selected, but diverse categories
	- Tech, News, Lifestyle, Quirky, Humanities, Science

- Searches executed via proxy to provide:
	1. A PhantomJS script to run the same query as a "control" user

	2. User queries and control queries are executed in parallel

	3. Google Search requests are made to a hard-coded Google IP address


???


- Demonstrating the difference in search results between a control and Google users with accounts
- Mechanical Turk is a marketplace for work, where you can request work from many workers
- Proxy: A proxy server is a computer that functions as an intermediary between a web browser (such as Internet Explorer) and the Internet
- PhantomJS is a WebKit browser which runs headlessly (i.e. doesn't draw out the the screen). The benefits that brings is speed — if you're controlling an actual program on your computer, you've a certain overhead in booting up the browser, configuring a profile etc.

Proxy Benefits:

1. Control results
	- executed from a "fresh" user with relatively no Google History
	- "Control" result set for comparison
2. Paralell Execution:
	- Elimination of temporal changes in result sets for user and query
3. Hard-coded IP:
	- Both queries should access the same datacenter
	
---

# Real-World Personalization: Results

.center[<img align="middle" src="images/figure-5-amt-personalization-original.png" width="500" alt="Figure 5 Amt Personalization Original">]

- Compared to control results, user results observed a significant difference.

???

- Compared user results to control results, and control results to each other.
	- Intuitively, the change between control results is simply baseline noise from updating search index, distributed data centers, geolocation, A/B testing, carry-over effect
	- On average, users showed and 11.7% higher likelihood of differing from the control than the control results differing from each other

---

#What Causes Personalization?

- Create `\(x+1\)` accounts where `\(x\)` is the number of options that a feature can have

	- Additional account is a control account with the same features as one other account
	- If no noise is present, these two accounts should provide same results

- Each account searches 120 queries over a period of 7 days
	- Each query is executed at the same time for all accounts

- Each accounts traffic is routed to a unique IP endpoint via SSH tunnel
	- 192.168.1.1 vs. 192.168.1.2


???

After observing that it web searches are clearly personalized from the AMT worker results, authors decided to test what features of a Google Profile caused this personalization?

- x+1 accounts where x is the number of options that a feature can take
	- x=3 for gender
	- "male", "female", "other"
	- additional "female" account for control

- 120 queries are categorically diverse

- Unique IP addresses within \24 subnet
	- IP addresses are unique, but will not be affected by geolocation noise
---

class: center, middle
.center[![Table 3 Features Evaluated](images/table-3-features-evaluated.png)]

---

#Personalization - Cookies

- 3 Options: Logged In w/ Cookies Enabled, Logged Out w/ Cookies Enabled, Cookies Disabled
	- Control: Cookies disabled

- Results from each query are compared to the results from the control 

![Browser Cookies Results](images/browser-cookies-results.png)

- **Results provided do not change, but order does**
	- Logged in users with cookies experience an average reordering of 2 in their search results

---

#Personalization - IP Address Geolocation

- 10 different accounts each forwarding their Google Search requests to **remote machines in 10 different states**
	- Control: Account forwarding to Massachusetts

![Ip Geolocation Results](images/ip-geolocation-results.png)

- Average Jacard Index ~ 0.9
	- 1 **difference** in search results vs. control
	
- Average Edit Distance ~ 2.12
	- 2 **reorderings** needed to match ranking (1-10) of control's search results
	
???

Example:

- Querying "pier one"
	- CA machine produced results including a local news story about a store grand opening in the area of CA
	- MA machine included a Google Maps link to a store in the area

- Conclusion, geolocation is used for personalizing search results

---

#Personalization - Features Not Contributing

- Google Account Attributes (Gender & Age)	

![Gender Results](images/gender-results.png)

- Search History
	- 20 accounts specified a demographic attribute (E.g. income level)
	- Before executing the 120 test queries, each account searches 100 queries that individuals in that demographic usually search for


![Seach History Income Level Demographics](images/seach-history-income-level-demographics.png)

- Search-Result-Click History, Browsing History

???

- Gender:
	- Jacard Index of 1 indicates same results
	- Edit distance ~0 indicates no reordering in these results
	
- Demographic queries obtained from Quantcast, a site that published top websites and the demographic score for each demographic
	- The query strings are constructed by taking domains from the Quantcast top-2000 that have scores >100 for a particular demographic and removing subdomains and top level domains (e.g., www.amazon.com becomes “amazon”).

- Search-Result-Click History
	- Same procedure as search history, expect the user clicks on the results that are retrieved

- Browsing history
	- Browsing random pages before searching 
	- Pages browsed are ones that would have Google cookies
		- Allows Google to track

---

#Which Queries are More Personalized?

- Plot the cumulative distribution function of the Jacard Index and Edit Distance for all categories across all experiments

![Cdf Query Categories](images/cdf-query-categories.png)

- For example, _Queries relating to **politics** are more likely to produce personalized results than **"what is..."** queries_

	 - It is more probable that political queries have a lower Jacard Index (less similarity between results)
	 - It is is more probable that "what is..." query have a smaller edit distance (less reordering, additions, removals and swapping) is required to match search results

???

Additional insight into personalization.

- Took the Jacard Index and Edit distance for all queries belong to a specific category across all experiments

Jacard Index:

- Probability that a query relating to politics will have a Jacard Index `\(<=\)` 0.9 `\(~=\)` 35%
	- Probability that 1 result will differ between result sets of size 10
	
- Probability that a "what is..." query will have a Jacard Index `\(<=\)` 0.9 `\(~=\)` 12%
	- It is less likely that search result sets will be different

Edit Distance:

- Probability that a query relating to politics will have a Edit Distance `\(<=\)` 2 `\(~=\)` 60%
	- Probability that at most 2 reordings are required
	
- Probability that a "what is..." query will have a Jacard Index `\(<=\)` 2 `\(~=\)` 78%
	- It is more likely that "what is..." query results will need less reording

---

#Which Rankings are More Volatile?

- For each result at rank `\(r\in[1,10]\)` for all test accounts, compare this result to the same result at rank `\(r\)` in the control account's result set.
	- If different, consider this a changed result at rank `\(r\)`

- To determine the results changed for a rank `\(r\)` as a percentage:
	- Divide the number of changed results at `\(r\)` for a query, by the total number of results at rank `\(r\)` for that query

- **Idea**: *Ranks that produced more changed results from control will have a higher percentage of results changed in the plot*

---


.center[<img src="images/rank-volatility.png" width="400" height="" alt="Rank Volatility">]

- Personalized results: cookie experiment, and the geolocation experiment

##Results:

- Volatility of rank is not uniform

- Personalized search results are more susceptible to ranking volatility 

???

Result changes on non-personalize pages represent noise

Results ranked 1 are not likely to move in rank
Results ranked 6 are the most likely to move in rank

---

#Conclusion

The authors provide three contributions to studying personalization of web search by:

1. Providing a detailed methodology for measuring web search personalization 

2. Showing that ~12% of results are personalized for 200 real-world users using this approach

3. Identifying features that contribute and don't contribute to web search personalization using this methodology in automated accounts

???

It identifies and compensates for sources of noise in search engines

Noise:
- temporal changes in search indexes
- distributed infrastructure and datacenters
- geolocation
- A/B testing

The authors provide a stepping stone to investigating more areas of web search personalization:
- Investigating beyond accounts in the US
- Investigating more features that could contribute to personalization 




