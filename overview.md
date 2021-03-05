```
Function       -------->            Service  ---------->  Database                 
Use Case                            + algorithm           + schema                        
API                                 + data structure      - cache (read)                                 
                                    - QPS                 - Queue (write)
                                    - LB or API Gateway   - Capacity                                                   
                                    - Rate Limit          - DB Partition        
                                    ! HA                  ! Replication                                        
                                                                                              
                                                                                             


```


DB Partition
- RDB sharding: consistent hashing
- NoSQL


