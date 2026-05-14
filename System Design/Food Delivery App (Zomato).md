
Requirements
1. Functional Requirement
		a. user can browse restaurant
		b. Place order
		c. track order in real time
2. Non Functional Requirement
	1. High availability
	2. Low Latency
	3. Scalability (millions of users)
	4. Reliability (no duplicate  orders)

Non functional requirements must
1. Performance (how fast your system responds)
	1. Api response time < 200ms
	2. Fast db queries
	**How to achieve this**
	3. Caching(Redis)
	4. Efficient sql queries(indexes)
	5. Async programming (.Net async/await)
	6. Reduce network calls

2. Scalability
	1. Can your system handle increasing users load
	2. Types of scaling is vertical and horizontal
		1. Vertical -> increase server power
		2. Horizontal -> add more servers(preferred)
		
	3. How you achieve it
		1. Stateless API's
		2. Load balancer
		3. Microservices
	