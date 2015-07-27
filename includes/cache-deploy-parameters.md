
### redisCacheName

O nome do Cache Redis do Azure a ser criado.

    "redisCacheName": {
      "type": "string"
    }

### redisCacheSKU

A camada de preços do novo Cache Redis do Azure.

    "redisCacheSKU": {
      "type": "string",
      "allowedValues": [ "Basic", "Standard" ],
      "defaultValue": "Basic"
    }

O modelo define os valores que são permitidos para esse parâmetro (Basic ou Standard) e atribui um valor padrão (Basic) se nenhum valor for especificado. Basic fornece um único nó com vários tamanhos disponíveis, até 53 GB. Standard fornece Principal/Réplica de dois nós com vários tamanhos disponíveis, até 53 GB e SLA de 99,9%.

### redisCacheFamily

A família do SKU.

    "redisCacheFamily": {
      "type": "string",
      "defaultValue": "C"
    }

### redisCacheCapacity

O tamanho da nova instância do Cache Redis do Azure.

    "redisCacheCapacity": {
      "type": "int",
      "allowedValues": [ 0, 1, 2, 3, 4, 5, 6 ],
      "defaultValue": 0
    }

O modelo define os valores que são permitidos para esse parâmetro (0, 1, 2, 3, 4, 5 ou 6) e atribui um valor padrão (0) se nenhum valor for especificado. Esses números correspondem aos seguintes tamanhos de cache: 0 = 250 MB, 1 = 1 GB, 2 = 2,5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

### redisCacheVersion

A versão de servidor do Redis do novo cache.

    "redisCacheVersion": {
      "type": "string",
      "defaultValue": "2.8"
    }

<!---HONumber=July15_HO3-->