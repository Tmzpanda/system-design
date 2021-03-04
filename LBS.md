```
Function       -------->            Service  ---------->  Database                 ------->    Scalable
Use Case                            + algorithm           + schema                             QPS
API                                 + data structure                                           + LB or API Gateway
                                                                                               + Rate Limit
                                                                                               + DB partition
                                                                                               + cache
                                                                                              
                                                                                               
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

