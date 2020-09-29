---
title: azure elastic pool scale on storage bytes amazing error message
category: [azure]
tag: [azure]
---


This is just a complain.
Azure guys , your error messages suck.

Recently I tried to do some automation tasks via azure api *elastic_pools.create_or_update*.
My flow is like this: 

```
get existing elastic pool info via the elastic_pool.get api --> update the info with new max_size_bytes value --> use the create_or_update to scale the pool 
```

My core number and storage size pair are defintely in the available range by checking the [resource limit doc](https://docs.microsoft.com/en-us/azure/azure-sql/database/resource-limits-vcore-elastic-pools) 

For example:
the updating parameter:
core number : 8
max_size_bytes : 121319424

But even I checked this parameters for couple times and tried to called the api, the api always return the following error message : 
```
'statusMessage': '{\\'error\\':{\\'code\\':\\'ElasticPoolCapacityStorageNotAllowedMB\\',\\'message\\':\\'The elastic pool storage limit in megabytes (118476) does not belong to the allowed values for service tier 'GeneralPurpose' with capacity '8'.\\'}}',
```

First, It's nosense for the api need the parameter is bytes unit and return the error message with mega bytes.
Seconds, WHF the message told me my stoage byts is not compatiable with the core number even in the doc It should be.
I did not want to waste my time on this so I decided to contact the $MS support but they obviously not understand what problem I faced.
After "some" trial and error I finally figured it out......

The root cause is:

###the sizes bytes must be divisible by GB###

WWWWWWWWWWWWWWWWWWWWWWry!!!

In my opinion, this should be improved by the following two steps:
- improve the docsting in the elastic_pool parameter
- improve the error message. change it to "max_size_bytes must be divisible by GB, please change its value"


Hope some guy meet the same issue may not suffer from this by checking this article.
long live , developers ......








 
