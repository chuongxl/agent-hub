# System Design Interview Question Bank

A curated collection of system design interview questions organized by complexity and area for the Interview Simulator skill.

---

## 1. Requirements & Scoping

### Foundational Questions

**Q1.1**: "When designing a system, what's the first thing you should do before diving into architecture?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Requirements gathering importance
- **Follow-up**: "What types of requirements should you ask about?"

**Q1.2**: "What's the difference between functional and non-functional requirements? Give examples."
- **Expected Level**: Beginner to Intermediate
- **Concept**: Requirement categorization
- **Follow-up**: "Why does it matter to distinguish between them?"

### Intermediate Questions

**Q1.3**: "Design a URL shortening service. Before jumping to architecture, what questions would you ask?"
- **Expected Level**: Intermediate
- **Concept**: Clarification and scoping
- **Follow-up**: "How would your design change if it needed to support 1M vs. 1B shortened URLs?"

**Q1.4**: "How do you estimate capacity and scale for a system? Walk through an example."
- **Expected Level**: Intermediate
- **Concept**: Back-of-envelope math, capacity planning
- **Follow-up**: "What assumptions did you make?"

---

## 2. High-Level Architecture

### Foundational Questions

**Q2.1**: "At a high level, what are the main components of a typical web application architecture?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Typical layers (client, API, database, cache, etc.)
- **Follow-up**: "Why do you separate these layers?"

**Q2.2**: "What's the difference between monolithic and microservices architecture?"
- **Expected Level**: Intermediate
- **Concept**: Architectural patterns, tradeoffs
- **Follow-up**: "When would you choose each?"

### Intermediate Questions

**Q2.3**: "Draw a simple architecture for a social media feed system. Explain each component."
- **Expected Level**: Intermediate
- **Concept**: Component interaction, data flow
- **Follow-up**: "What are potential bottlenecks in this design?"

**Q2.4**: "Design the architecture for a video streaming platform like YouTube. What are the key components?"
- **Expected Level**: Intermediate to Advanced
- **Concept**: Complex system design, multiple concerns
- **Follow-up**: "How would you handle live streaming differently than on-demand?"

### Advanced Questions

**Q2.5**: "Design a real-time notification system that needs to deliver 1M+ notifications per minute."
- **Expected Level**: Advanced
- **Concept**: Scalable message queuing, real-time systems
- **Follow-up**: "How do you guarantee notification delivery?"

---

## 3. Database Design & Data Modeling

### Foundational Questions

**Q3.1**: "What are the key differences between SQL and NoSQL databases?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Database paradigms, tradeoffs
- **Follow-up**: "When would you use each?"

**Q3.2**: "Design a simple data schema for a user authentication system."
- **Expected Level**: Beginner to Intermediate
- **Concept**: Schema design, security considerations
- **Follow-up**: "What fields would you need?"

### Intermediate Questions

**Q3.3**: "You're designing a database for a massive e-commerce platform with millions of products. What considerations would you have?"
- **Expected Level**: Intermediate
- **Concept**: Indexing, denormalization, query optimization
- **Follow-up**: "How would you optimize search queries?"

**Q3.4**: "What's the N+1 query problem, and how would you solve it?"
- **Expected Level**: Intermediate
- **Concept**: Query optimization, data fetching patterns
- **Follow-up**: "Are there tools that help prevent this?"

### Advanced Questions

**Q3.5**: "Design a database schema for a time-series data system (e.g., stock prices, metrics). What would you store and how?"
- **Expected Level**: Advanced
- **Concept**: Time-series databases, aggregation, retention
- **Follow-up**: "How would you handle data at different time scales (1-min vs. daily)?"

**Q3.6**: "How would you shard a massive database across multiple servers? What are the challenges?"
- **Expected Level**: Advanced
- **Concept**: Horizontal scaling, shard key selection, consistency
- **Follow-up**: "How would you handle a shard that becomes a hotspot?"

---

## 4. Caching & Optimization

### Foundational Questions

**Q4.1**: "What is caching, and why is it important in system design?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Cache purpose, performance improvements
- **Follow-up**: "What are different cache layers?"

**Q4.2**: "What's the difference between client-side, server-side, and database caching?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Cache hierarchy, use cases
- **Follow-up**: "Give examples of each."

### Intermediate Questions

**Q4.3**: "Design a caching strategy for a news feed that gets 10M daily active users. What would you cache and why?"
- **Expected Level**: Intermediate
- **Concept**: Cache-aside pattern, TTLs, invalidation
- **Follow-up**: "How do you handle cache invalidation?"

**Q4.4**: "What's the Least Frequently Used (LFU) vs. Least Recently Used (LRU) cache eviction policy? When would you use each?"
- **Expected Level**: Intermediate
- **Concept**: Cache eviction strategies
- **Follow-up**: "Can you implement a simple LRU cache?"

### Advanced Questions

**Q4.5**: "How would you design a caching layer that needs to serve 100M requests per day with 99.9% cache hit rate?"
- **Expected Level**: Advanced
- **Concept**: Cache strategies, consistency, performance
- **Follow-up**: "What metrics would you monitor?"

---

## 5. Scaling & Distributed Systems

### Foundational Questions

**Q5.1**: "What's the difference between vertical and horizontal scaling?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Scaling approaches, tradeoffs
- **Follow-up**: "When would you use each?"

**Q5.2**: "What is load balancing, and why do you need it?"
- **Expected Level**: Beginner to Intermediate
- **Concept**: Load distribution, availability
- **Follow-up**: "What are different load balancing strategies?"

### Intermediate Questions

**Q5.3**: "Design a system that can scale from 1K to 1B users. What are the key milestones?"
- **Expected Level**: Intermediate
- **Concept**: Progressive scaling, bottleneck identification
- **Follow-up**: "At what point would you need different components?"

**Q5.4**: "How would you implement graceful degradation in a system experiencing high load?"
- **Expected Level**: Intermediate
- **Concept**: Resilience, rate limiting, circuit breakers
- **Follow-up**: "What features would you degrade first?"

### Advanced Questions

**Q5.5**: "Design a globally distributed system with data centers across multiple continents. How do you handle consistency and latency?"
- **Expected Level**: Advanced
- **Concept**: CAP theorem, eventual consistency, geo-distribution
- **Follow-up**: "How do you handle failures across regions?"

**Q5.6**: "What strategies would you use to achieve 99.99% uptime (four 9s) for a critical service?"
- **Expected Level**: Advanced
- **Concept**: Reliability, redundancy, failure handling
- **Follow-up**: "What's the cost tradeoff?"

---

## 6. Tradeoffs & Alternative Approaches

### Intermediate Questions

**Q6.1**: "You have a choice between consistency and availability. In what scenarios would you prioritize each?"
- **Expected Level**: Intermediate
- **Concept**: CAP theorem, system requirements alignment
- **Follow-up**: "Can you give real-world examples?"

**Q6.2**: "When designing a feature, how do you decide between pushing more work to the client vs. the server?"
- **Expected Level**: Intermediate
- **Concept**: Complexity distribution, performance, maintainability
- **Follow-up**: "What are the pros and cons of each?"

### Advanced Questions

**Q6.3**: "Compare a pull-based vs. push-based approach for a real-time notification system. Which would you choose and why?"
- **Expected Level**: Advanced
- **Concept**: System design tradeoffs, resource usage
- **Follow-up**: "Are there cases where you'd use both?"

**Q6.4**: "Explain the CQRS (Command Query Responsibility Segregation) pattern and when you'd use it."
- **Expected Level**: Advanced
- **Concept**: Advanced architectural patterns
- **Follow-up**: "What problems does it solve?"

---

## 7. Security & Compliance

### Intermediate Questions

**Q7.1**: "What security measures would you implement for a payment processing system?"
- **Expected Level**: Intermediate
- **Concept**: Data protection, compliance, authentication
- **Follow-up**: "How do you handle PCI-DSS compliance?"

**Q7.2**: "How would you design authentication and authorization for a multi-tenant SaaS application?"
- **Expected Level**: Intermediate
- **Concept**: Identity management, isolation
- **Follow-up**: "What's the difference between authentication and authorization?"

### Advanced Questions

**Q7.3**: "Design a system that needs to be SOC 2 compliant. What architectural decisions would change?"
- **Expected Level**: Advanced
- **Concept**: Security requirements, compliance frameworks
- **Follow-up**: "How do you prove compliance?"

---

## Example Scenario Progressions

### Short Interview (30 minutes, 6-8 questions)
1. Requirements clarification (Q1.1, Q1.2)
2. High-level architecture (Q2.1)
3. Database design (Q3.1)
4. Caching (Q4.1)
5. Scaling (Q5.1)
6. Tradeoffs (Q6.1)

### Comprehensive Interview (60+ minutes, 12-14 questions)
1. Detailed requirements (Q1.3, Q1.4)
2. Architecture design (Q2.3 or Q2.4)
3. Data modeling (Q3.3, Q3.4)
4. Caching strategy (Q4.3)
5. Scaling approach (Q5.3, Q5.4)
6. Distributed systems concerns (Q5.5)
7. Tradeoffs & alternatives (Q6.3, Q6.4)
8. Security (Q7.1 or Q7.2)

---

## Related Resources

- System Design Primer: https://github.com/donnemartin/system-design-primer
- Designing Data-Intensive Applications (Martin Kleppmann)
- Building Microservices (Sam Newman)
- Release It! (Michael Nygard)
