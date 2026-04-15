

If you want better results in your system design interviews, spend your time on these 10 fundamentals.  
  
After spending 19+ years across Google, Amazon & various startups, these are the topics, in my opinion, that one 100% should focus on:  
  
1. Content Delivery Network (CDN)  
→ Makes global content delivery fast by caching copies closer to users.  
  
2. Caching  
→ Reduces load and latency by storing frequently accessed data in memory.  
  
3. Distributed Caching  
→ Scales caching across multiple nodes to handle high traffic and avoid single points of failure.  
  
4. Latency vs Throughput  
→ Helps you trade off between quick responses (latency) and handling more requests (throughput).  
  
5. CAP Theorem  
→ Forces you to balance consistency, availability, and partition tolerance in system design.  
  
6. Load Balancing  
→ Distributes incoming traffic evenly to prevent server overload and downtime.  
  
7. ACID Transactions  
→ Ensures reliable, predictable database operations,critical for data integrity.  
  
8. SQL vs NoSQL  
→ Guides your choice of database based on structure, scalability, and data needs.  
  
9. Consistent Hashing  
→ Distributes data across servers efficiently, enabling easy scaling and rebalancing.  
  
10. Database Index  
→ Speeds up queries by allowing fast lookups on large datasets.  
  
11. Rate Limiting  
→ Prevents abuse and protects system resources by controlling request frequency.  
  
12. Microservices Architecture  
→ Breaks systems into modular services, enabling independent scaling and deployment.  
  
13. Strong vs Eventual Consistency  
→ Defines how quickly all nodes reflect data changes,impacts user experience and reliability.  
  
14. REST vs RPC  
→ Affects how services communicate,REST is simple and standardized, RPC is fast and direct.  
  
15. Batch Processing vs Stream Processing  
→ Decides if you handle data in chunks (batch) or as it arrives (stream).  
  
16. Heartbeat  
→ Monitors node health to quickly detect failures in distributed systems.  
  
17. Circuit Breaker  
→ Prevents system overload by blocking requests to failing services and enabling recovery.  
  
18. Idempotency  
→ Ensures repeating an operation won’t produce unwanted side effects, vital for reliability.  
  
19. Database Scaling  
→ Lets you grow database capacity to handle more data or users as your system expands.  
  
20. Data Replication  
→ Copies data across nodes to boost reliability and availability.  
  
21. Data Redundancy  
→ Provides backup copies so that failures don’t result in data loss.  
  
22. Database Sharding  
→ Splits large databases into smaller parts to improve performance and scalability.  
  
23. Proxy Server  
→ Sits between clients and servers, enabling traffic control, security, and caching.  
  
24. Domain Name System (DNS)  
→ Translates human-readable names to IPs, routing traffic efficiently on the internet.  
  
25. Message Queues  
→ Decouple services by allowing asynchronous communication, improving reliability and scalability.  
  
26. WebSockets  
→ Enable real-time, two-way communication between client and server.  
  
Continued ↓

Top 5 Kafka Use Cases  
  
Apache Kafka was originally designed for large-scale log processing, but today it powers many real-time data systems.  
Because Kafka stores events for a configurable retention period and allows consumers to read data at their own pace, it has become a key building block for event-driven architectures and streaming platforms.  
Here are some of the most common ways organizations use Kafka.  
  
🔹 Log Processing and Analysis  
Kafka is widely used to collect logs from multiple services such as order systems, payment services, or shopping platforms.  
These logs can then be streamed to tools like Elasticsearch and Kibana for centralized analysis and monitoring.  
  
🔹 Real-Time Data Streaming for Recommendations  
User activity such as clicks, views, or purchases can be streamed through Kafka.  
Streaming frameworks like Apache Flink can process these events in real time to generate personalized recommendations and insights.  
  
🔹 System Monitoring and Alerting  
Metrics and events from different services can be pushed into Kafka topics.  
Streaming processors analyze these events and trigger alerts when anomalies or system failures are detected.  
  
🔹 Change Data Capture (CDC)  
Kafka can capture changes happening in databases and stream them to other systems.  
Using connectors, database updates can be propagated to systems like Redis, Elasticsearch, or replica databases in near real time.  
  
🔹 System Migration  
Kafka can also help during system upgrades or migrations.  
Events from older services can be streamed and processed by new services simultaneously, allowing gradual migration without disrupting production traffic.  
  
📌 Key takeaway  
Kafka is more than just a messaging system — it’s a powerful platform for building real-time data pipelines and event-driven systems.  


![No alternative text description for this image](https://media.licdn.com/dms/image/v2/D5622AQHmxeedhcOg4w/feedshare-shrink_800/B56ZzzlX_NHMAc-/0/1773613199682?e=1775692800&v=beta&t=jBkb6MyWsYVOCAgeYfgr9lg2FMMFwVYYj_Pj8DbLnpU)



𝗧𝗵𝗮𝘁 𝟲-𝗱𝗶𝗴𝗶𝘁 𝗢𝗧𝗣 𝘆𝗼𝘂 𝗲𝗻𝘁𝗲𝗿 𝗶𝗻 𝘀𝗲𝗰𝗼𝗻𝗱𝘀...𝗶𝘀 𝗽𝗼𝘄𝗲𝗿𝗲𝗱 𝗯𝘆 𝗮𝗻 𝗲𝗻𝘁𝗶𝗿𝗲 𝗮𝘂𝘁𝗵𝗲𝗻𝘁𝗶𝗰𝗮𝘁𝗶𝗼𝗻 𝘀𝘆𝘀𝘁𝗲𝗺 𝘄𝗼𝗿𝗸𝗶𝗻𝗴 𝗯𝗲𝗵𝗶𝗻𝗱 𝘁𝗵𝗲 𝘀𝗰𝗲𝗻𝗲𝘀....  
  
I built an OTP login system from scratch and realized how much goes into verifying those 6 digits.  
  
𝗛𝗲𝗿𝗲'𝘀 𝘄𝗵𝗮𝘁 𝗵𝗮𝗽𝗽𝗲𝗻𝘀 𝘄𝗵𝗲𝗻 𝘆𝗼𝘂 𝗿𝗲𝗾𝘂𝗲𝘀𝘁 𝗮𝗻 𝗢𝗧𝗣:  
  
𝟭) 𝗥𝗮𝗻𝗱𝗼𝗺 𝗡𝘂𝗺𝗯𝗲𝗿 𝗚𝗲𝗻𝗲𝗿𝗮𝘁𝗶𝗼𝗻  
The system generates a cryptographically secure 6-digit code. Not just random, but unpredictable enough that attackers can't guess patterns.  
  
𝟮) 𝗧𝗶𝗺𝗲-𝗕𝗮𝘀𝗲𝗱 𝗘𝘅𝗽𝗶𝗿𝗮𝘁𝗶𝗼𝗻  
Your OTP expires in 5-10 minutes. The backend stores the code with a timestamp and validates it's still fresh when you submit it.  
  
𝟯) 𝗥𝗮𝘁𝗲 𝗟𝗶𝗺𝗶𝘁𝗶𝗻𝗴  
Try requesting 20 OTPs in a minute? Blocked. This prevents brute force attacks and SMS spam that could cost thousands in API fees.  
  
𝟰) 𝗦𝗠𝗦/𝗘𝗺𝗮𝗶𝗹 𝗜𝗻𝘁𝗲𝗴𝗿𝗮𝘁𝗶𝗼𝗻  
The OTP gets sent via Twilio, AWS SNS, or email providers. This means handling API credentials, retry logic, and delivery failures gracefully.  
  
𝟱) 𝗩𝗲𝗿𝗶𝗳𝗶𝗰𝗮𝘁𝗶𝗼𝗻 𝗟𝗼𝗴𝗶𝗰  
When you submit the OTP, the system checks: Is it correct? Is it expired? Has it already been used? Only then does it create your session.  
  
𝟲) 𝗦𝗲𝘀𝘀𝗶𝗼𝗻 𝗠𝗮𝗻𝗮𝗴𝗲𝗺𝗲𝗻𝘁  
After successful verification, the backend generates a JWT or session token that keeps you logged in securely.  
  
𝗖𝗵𝗮𝗹𝗹𝗲𝗻𝗴𝗲𝘀 & 𝗘𝗱𝗴𝗲 𝗖𝗮𝘀𝗲𝘀 𝗬𝗼𝘂 𝗠𝘂𝘀𝘁 𝗛𝗮𝗻𝗱𝗹𝗲:  
• 𝗢𝗧𝗣 𝗯𝗿𝘂𝘁𝗲 𝗳𝗼𝗿𝗰𝗲 → limit retries and temporarily block repeated attempts.  
• 𝗠𝘂𝗹𝘁𝗶𝗽𝗹𝗲 𝗢𝗧𝗣 𝗿𝗲𝗾𝘂𝗲𝘀𝘁𝘀 → invalidate previous codes when a new OTP is generated.  
• 𝗦𝗠𝗦 𝗱𝗲𝗹𝗶𝘃𝗲𝗿𝘆 𝗱𝗲𝗹𝗮𝘆𝘀 → balance OTP expiration time with real-world delivery delays.  
• 𝗢𝗧𝗣 𝗿𝗲𝘂𝘀𝗲 𝗮𝘁𝘁𝗲𝗺𝗽𝘁𝘀 → invalidate the OTP immediately after successful verification.  
• 𝗖𝗼𝗻𝗰𝘂𝗿𝗿𝗲𝗻𝘁 𝗹𝗼𝗴𝗶𝗻𝘀 → handle race conditions when users request or verify OTPs from multiple devices.  
  
The difference between a secure OTP login and a system vulnerable to abuse is how y𝗼𝘂 𝗵𝗮𝗻𝗱𝗹𝗲 𝗿𝗮𝘁𝗲 𝗹𝗶𝗺𝗶𝘁𝗶𝗻𝗴, 𝗲𝘅𝗽𝗶𝗿𝗮𝘁𝗶𝗼𝗻, 𝗮𝗻𝗱 𝗲𝗱𝗴𝗲 𝗰𝗮𝘀𝗲𝘀.  
  
I built the entire system from scratch covering the 𝗢𝗧𝗣 𝗴𝗲𝗻𝗲𝗿𝗮𝘁𝗶𝗼𝗻, 𝘃𝗲𝗿𝗶𝗳𝗶𝗰𝗮𝘁𝗶𝗼𝗻 𝗹𝗼𝗴𝗶𝗰, 𝗦𝗠𝗦 𝗶𝗻𝘁𝗲𝗴𝗿𝗮𝘁𝗶𝗼𝗻, and 𝘀𝗲𝘀𝘀𝗶𝗼𝗻 𝗵𝗮𝗻𝗱𝗹𝗶𝗻𝗴.  
  
If this made you pause and think, “𝘁𝗵𝗲𝗿𝗲’𝘀 𝘁𝗵𝗶𝘀 𝗺𝘂𝗰𝗵 𝗵𝗮𝗽𝗽𝗲𝗻𝗶𝗻𝗴 𝗯𝗲𝗵𝗶𝗻𝗱 𝗮 𝘀𝗶𝗺𝗽𝗹𝗲 𝗢𝗧𝗣?”, I break the entire flow down step-by-step in my video.  
  
It’s also a vital authentication system design concept many developers struggle to explain in interviews.  
  


![No alternative text description for this image](https://media.licdn.com/dms/image/v2/D5622AQGbY0EObJkFdw/feedshare-shrink_800/B56Zzs2EVFKsAc-/0/1773500122726?e=1775692800&v=beta&t=VPCP5HVqEz_mbSRuseE6WU7oBw-kyl6X8yhpzmYNf1k)