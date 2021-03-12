```
Function       -------->            Service  ---------->  Database                 
Use Case                            + algorithm           + schema                        
API                                 + data structure      - cache (read)                                 
                                    - QPS                 - Queue (write)
                                    - API Gateway or LB   - Capacity                                                   
                                    - Rate Limit          - DB Partition        
                                    ! HA                  ! Replication                                        
                                                                                              
                                                                                             

```

- API Gateway
  - routing (microservices)
  - authentication, rate limiting, monitoring, alerting, etc.



- LB
  - funtion
    - traffic load
    - failover
    
  - component
    - app servers
    - cache servers
    - DB partitions
    - replication servers



- DB Partitioning
  - RDB sharding: consistent hashing
  - NoSQL



- Replication: writes will first go to primary server then will be replicated to secondary server
  - read traffic
  - fault-tolerance



- open connectiion
  - server
    - maintain open connection
      - long polling
      - websocket
    - track and redirect
      - k-v pairs (UserID, connectionObject)

    
  - scenario
    - NewsFeed push
    - Chat push
   
 
  


- DB choices
  - Cassandra - userPhoto
  - chat -> HBase: quick append, range-based search


