<!--
Used in:
sql-database-elastic-pool.md   
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->

### <a name="basic-elastic-pool-limits"></a>Limites de pool elástico Básico

| Tamanho do pool (eDTUs)  | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Armazenamento máx. por pool* | 5 GB | 10 GB | 20 GB | 29 GB | 39 GB | 78 GB | 117 GB | 156 GB |
| Armazenamento máximo OLTP na memória por pool* | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Número máximo de BDs por pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Máximo de trabalhos simultâneos por pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de logons simultâneos por pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por banco de dados | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} |
| Máximo de eDTUs por banco de dados | {5} | {5} | {5} | {5} | {5} | {5} | {5} | {5} | {5} |
||||||||

### <a name="standard-elastic-pool-limits"></a>Limites de pool elástico Standard

| Tamanho do pool (eDTUs)  | **50** | **100** | **200** | **300** | **400** | **800** | 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Armazenamento máx. por pool* | 50 GB| 100 GB| 200 GB | 300 GB| 400 GB | 800 GB | 
| Armazenamento máximo OLTP na memória por pool* | N/D | N/D | N/D | N/D | N/D | N/D | 
| Número máximo de BDs por pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Máximo de trabalhos simultâneos por pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Máximo de logons simultâneos por pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por banco de dados | {0,10,20,<br>50} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} |
| Máximo de eDTUs por banco de dados | {10,20,<br>50} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | 
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limites de pool elástico Standard (continuação) 

| Tamanho do pool (eDTUs)  |  **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento máx. por pool* | 1,2 TB | 1,6 TB | 2 TB | 2,4 TB | 2.9 TB | 
| Armazenamento máximo OLTP na memória por pool* | N/D | N/D | N/D | N/D | N/D | 
| Número máximo de BDs por pool | 500 | 500 | 500 | 500 | 500 | 500 |
| Máximo de trabalhos simultâneos por pool |  2400 | 3200 | 4000 | 5.000 | 6000 |
| Máximo de logons simultâneos por pool |  2400 | 3200 | 4000 | 5.000 | 6000 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por banco de dados | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} |
| Máximo de eDTUs por banco de dados | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Limites de pool elástico Premium

| Tamanho do pool (eDTUs)  | **125** | **250** | **500** | **1000** | **1500** | 
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento máx. por pool* | 250 GB| 500 GB| 750 GB| 750 GB| 750 GB| 
| Armazenamento máximo OLTP na memória por pool* | 1 GB| 2 GB| 4 GB| 10 GB| 12 GB| 
| Número máximo de BDs por pool | 50 | 100 | 100 | 100 | 100 |  
| Máximo de trabalhos simultâneos por pool | 200 | 400 | 800 | 1600 |  2400 | 
| Máximo de logons simultâneos por pool | 200 | 400 | 800 | 1600 |  2400 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por banco de dados | {0,25,50,75,<br>125} | {0,25,50,75,<br>125,250} | {0,25,50,75,<br>125,250,500} | {0,25,50,75,<br>125,250,500,<br>1000} | {0,25,50,75,<br>125,250,500,<br>1000,1500} | 
| Máximo de eDTUs por banco de dados | {25,50,75,<br>125} | {25,50,75,<br>125,250} | {25,50,75,<br>125,250,500} | {25,50,75,<br>125,250,500,<br>1000} | {25,50,75,<br>125,250,500,<br>1000,1500} |  
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limites de pool elástico Premium (continuação) 

| Tamanho do pool (eDTUs)  |  **2000** | **2500** | **3000** | **3500** | **4000** |
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento máx. por pool* | 750 GB | 750 GB | 750 GB | 750 GB | 750 GB |
| Armazenamento máximo OLTP na memória por pool* | 16 GB | 20 GB | 24 GB | 28 GB | 32 GB |
| Número máximo de BDs por pool | 100 | 100 | 100 | 100 | 100 | 
| Máximo de trabalhos simultâneos por pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de logons simultâneos por pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por banco de dados | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} |  {0,25,50,75,<br>125,250,500,<br>1000,1750,4000} | 
| Máximo de eDTUs por banco de dados | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750,4000} | 
||||||||

\* Os bancos de dados em pool compartilham o armazenamento de pool, de modo que o armazenamento do banco de dados é limitado ao menor entre o armazenamento de pool restante ou o armazenamento máximo por banco de dados. O armazenamento máximo por pool refere-se ao máximo de armazenamento dos arquivos de dados no pool e não inclui o espaço usado pelos arquivos de log.



<!--HONumber=Feb17_HO3-->


