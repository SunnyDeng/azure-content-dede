
### redisCacheName

Der Name des zu erstellenden Azure Redis Cache.

    "redisCacheName": {
      "type": "string"
    }

### redisCacheSKU

Der Tarif des neuen Azure Redis Cache.

    "redisCacheSKU": {
      "type": "string",
      "allowedValues": [ "Basic", "Standard" ],
      "defaultValue": "Basic"
    }

Die Vorlage definiert die Werte, die für diesen Parameter zulässig sind (Basic oder Standard), und weist einen Standardwert (Basic) zu, wenn kein Wert angegeben wird. "Basic" bietet einen einzelnen Knoten mit mehreren Größen bis zu 53 GB. "Standard" bietet zwei Knoten, Primär/Replikat, mit mehreren Größen bis zu 53 GB und 99,9% SLA.

### redisCacheFamily

Die SKU-Familie.

    "redisCacheFamily": {
      "type": "string",
      "defaultValue": "C"
    }

### redisCacheCapacity

Die Größe der neuen Azure Redis Cache-Instanz.

    "redisCacheCapacity": {
      "type": "int",
      "allowedValues": [ 0, 1, 2, 3, 4, 5, 6 ],
      "defaultValue": 0
    }

Die Vorlage definiert die Werte, die für diesen Parameter zulässig sind (0, 1, 2, 3, 4, 5 oder 6), und weist einen Standardwert (0) zu, wenn kein Wert angegeben wird. Diese Zahlen entsprechen den folgenden Cachegrößen: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

### redisCacheVersion

Die Redis-Serverversion des neuen Caches.

    "redisCacheVersion": {
      "type": "string",
      "defaultValue": "2.8"
    }

<!---HONumber=August15_HO6-->