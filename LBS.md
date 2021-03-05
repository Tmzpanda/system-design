```
Function       -------->            Service  ---------->  Database                 
Use Case                            + algorithm           + schema                        
API                                 + data structure      - cache (read)                                 
                                    - QPS                 - Queue (write)
                                    - LB or API Gateway   - Capacity                                                   
                                    - Rate Limit          - DB Partition        
                                    ! HA                  ! Replication         
                                                                                               
# Yelp
# Uber

```


- algorithm
  - geohashing
  - euclidean distance
  - 猜你喜欢 冷启动
  - uber dynamic

- data structure
  - QuadTree


- DB schema
  - grid
  - location

