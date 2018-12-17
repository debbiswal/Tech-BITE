[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#json)

## Extracting selected fields from JSON data  

Today , I came across a tool JQ , which helps in filtering selected fields from JSON data.  

Below are the details:  

Main site : https://stedolan.github.io/jq/  
Testing  site: https://jqplay.org/  


Example :  

JSON :  
```JSON
[{
  "name": "Braeburn Apples Loose",
  "productId": 253807413,
  "baseProductId": 50852666,
  "available": true,
  "selected": false,
  "units": 1
},{
  "name": "Bramley Cooking Apples Loose",
  "productId": 253558033,
  "baseProductId": 50501282,
  "available": true,
  "selected": false,
  "units": 1
},{
  "name": "Gala Apples Loose",
  "productId": 253559147,
  "baseProductId": 50623419,
  "available": true,
  "selected": false,
  "units": 1
}
]
```  

JQ query :  
```
.[] | {name:.name,productId:.productId,baseProductId:.baseProductId}
```  

Output  
```JSON
{
  "name": "Braeburn Apples Loose",
  "productId": 253807413,
  "baseProductId": 50852666
}
{
  "name": "Bramley Cooking Apples Loose",
  "productId": 253558033,
  "baseProductId": 50501282
}
{
  "name": "Gala Apples Loose",
  "productId": 253559147,
  "baseProductId": 50623419
}
```  

*NOTE : As of now I don’t know how to make array from this output with comma separated values. Need to check.*  

Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#json)
