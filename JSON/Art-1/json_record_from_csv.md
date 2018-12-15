[:house:Home](https://github.com/debbiswal/Articles)  

## Create multiple JSON records from CSV using JSON template  

Today I came across a website , which creates JSON records from CSV files using JSON template.
http://www.convertcsv.com/csv-to-json.htm

**Example :**

* Go to STEP-1  on page:  
Provide CSV details

Sample csv data:  
```csv
productName,productId,baseProductId
Tesco Braeburn Apples Loose,253807413,50852666
Tesco Bramley Cooking Apples Loose,253558033,50501282
Tesco Gala Apples Loose,253559147,50623419
Tesco Cox Apples Loose,254304534,50925828
```  

* Go to STEP-2  on page:  
Provide the separator details here  

* Go to STEP-4 on page :  
Provide the JSON template and click ‘Convert CSV to JSON via template”  
You can see from below example  , how column names from CSV data is used .  

Provide 
``` 
TOP = “[“  , BOTTOM=”]”
```  

The TOP and BOTTOM values will be used to wrap the output. Here we have used the JSON array notation to keep all generated JSON records within.  

Sample JSON template  :  
```json
{
      "DeliveryGroupId": 1,
      "ProductId": "{productId}",
      "BaseProductId": "{baseProductId}",
      "Description": "{productName}",
      "InStorePrice": 4,
      "InStoreTotalPrice": 4      
    }
```  

Output :  
```json
[
{ "DeliveryGroupId": 1, "ProductId": "253807413", "BaseProductId": "50852666",  "Description": " Tesco Braeburn Apples Loose ", "InStorePrice": 4, "InStoreTotalPrice": 4  },
{ "DeliveryGroupId": 1, "ProductId": "253558033", "BaseProductId": "50501282",  "Description": " Tesco Bramley Cooking Apples Loose ", "InStorePrice": 4, "InStoreTotalPrice": 4  },
{ "DeliveryGroupId": 1, "ProductId": "253559147", "BaseProductId": "50623419",  "Description": " Tesco Gala Apples Loose ", "InStorePrice": 4, "InStoreTotalPrice": 4  },
{ "DeliveryGroupId": 1, "ProductId": "254304534", "BaseProductId": "50925828",  "Description": " Tesco Cox Apples Loose ", "InStorePrice": 4, "InStoreTotalPrice": 4  }
]
```  

Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles)
