<!--
Used in:
sql-database-elastic-pool.md   
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->
 
<a id="basic-elastic-pool-limits" class="xliff"></a>

### Limites de pool elástico Básico

| Tamanho do pool (eDTUs)  | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Armazenamento máximo de dados por pool* | 5 GB | 10 GB | 20 GB | 29 GB | 39 GB | 78 GB | 117 GB | 156 GB |
| Armazenamento máximo OLTP na memória por pool | N/D  | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Número máximo de BDs por pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Máximo de trabalhos simultâneos (solicitações) por pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de logons simultâneos por pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por banco de dados | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Máximo de eDTUs por banco de dados | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Armazenamento máximo de dados por banco de dados | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 
||||||||

<a id="standard-elastic-pool-limits" class="xliff"></a>

### Limites de pool elástico Standard

| Tamanho do pool (eDTUs)  | **50** | **100** | **200**** | **300**** | **400**** | **800****| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Armazenamento máximo de dados por pool* | 50 GB| 100 GB| 200 GB | 300 GB| 400 GB | 800 GB | 
| Armazenamento máximo OLTP na memória por pool | N/D  | N/D | N/D | N/D | N/D | N/D | 
| Número máximo de BDs por pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Máximo de trabalhos simultâneos (solicitações) por pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Máximo de logons simultâneos por pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por banco de dados** | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Máximo de eDTUs por banco de dados** | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Armazenamento máximo de dados por banco de dados | 250 GB | 250 GB | 250 GB | 250 GB | 250 GB | 250 GB |
||||||||

<a id="standard-elastic-pool-limits-continued" class="xliff"></a>

### Limites de pool elástico Standard (continuação) 

| Tamanho do pool (eDTUs)  |  **1200**** | **1600**** | **2000**** | **2500**** | **3000**** |
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento máximo de dados por pool* | 1,2 TB | 1,6 TB | 2 TB | 2,4 TB | 2.9 TB | 
| Armazenamento máximo OLTP na memória por pool | N/D  | N/D | N/D | N/D | N/D | 
| Número máximo de BDs por pool | 500 | 500 | 500 | 500 | 500 | 
| Máximo de trabalhos simultâneos (solicitações) por pool |  2400 | 3200 | 4000 | 5.000 | 6000 |
| Máximo de logons simultâneos por pool |  2400 | 3200 | 4000 | 5.000 | 6000 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por banco de dados** | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Máximo de eDTUs por banco de dados** | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Armazenamento máximo de dados por banco de dados | 250 GB | 250 GB | 250 GB | 250 GB | 250 GB | 
||||||||

<a id="premium-elastic-pool-limits" class="xliff"></a>

### Limites de pool elástico Premium

| Tamanho do pool (eDTUs)  | **125** | **250** | **500** | **1000** | **1500*****| 
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento máximo de dados por pool* | 250 GB | 500 GB | 750 GB | 1 TB | 1,5 TB | 
| Armazenamento máximo OLTP na memória por pool | 1 GB| 2 GB| 4 GB| 10 GB| 12 GB| 
| Número máximo de BDs por pool | 50 | 100 | 100 | 100 | 100 |  
| Máximo de trabalhos simultâneos por pool (solicitações) | 200 | 400 | 800 | 1600 |  2400 | 
| Máximo de logons simultâneos por pool | 200 | 400 | 800 | 1600 |  2400 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por banco de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000 | 
| Máximo de eDTUs por banco de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000 |
| Armazenamento máximo de dados por banco de dados | 500 GB | 500 GB | 500 GB | 500 GB | 500 GB | 
||||||||

<a id="premium-elastic-pool-limits-continued" class="xliff"></a>

### Limites de pool elástico Premium (continuação) 

| Tamanho do pool (eDTUs) | **2000***** | **2500***** | **3000***** | **3500***** | **4000*****|
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento máximo de dados por pool* | 2 TB | 2.5 TB | 3 TB | 3.5 TB | 4 TB |
| Armazenamento máximo OLTP na memória por pool | 16 GB | 20 GB | 24 GB | 28 GB | 32 GB |
| Número máximo de BDs por pool | 100 | 100 | 100 | 100 | 100 | 
| Máximo de trabalhos simultâneos (solicitações) por pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de logons simultâneos por pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por banco de dados | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 |  0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Máximo de eDTUs por banco de dados | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Armazenamento máximo de dados por banco de dados | 500 GB | 500 GB | 500 GB | 500 GB | 500 GB | 
||||||||

<a id="premium-rs-elastic-pool-limits" class="xliff"></a>

### Limites de pool elástico Premium RS

| Tamanho do pool (eDTUs)  | **125** | **250** | **500** | **1000** |
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento máximo de dados por pool* | 250 GB| 500 GB | 750 GB | 750 GB |
| Armazenamento máximo OLTP na memória por pool | 1 GB | 2 GB | 4 GB | 10 GB |
| Número máximo de BDs por pool | 50 | 100 | 100 | 100 |
| Máximo de trabalhos simultâneos (solicitações) por pool | 200 | 400 | 800 | 1600 |
| Máximo de logons simultâneos por pool | 200 | 400 | 800 | 1600 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por banco de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 |
| Máximo de eDTUs por banco de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 
| Armazenamento máximo de dados por banco de dados | 500 GB | 500 GB | 500 GB | 500 GB | 
||||||||

> [!IMPORTANT]
>\* Os bancos de dados em pool compartilham o armazenamento de pool, de modo que o armazenamento de dados em um pool elástico é limitado ao menor entre o armazenamento de pool restante ou o armazenamento máximo por banco de dados. 
>
>
>\*\*O mín/máx de eDTUs por banco de dados começando em 200 eDTUs e mais está na versão prévia pública.
>
>\*\*\* Armazenamento de dados máximo padrão por pool para os pools Premium com 500 eDTUs ou mais é de 750 GB. Para obter o maior tamanho de armazenamento de dados máximo por pool Premium para 1000 ou mais eDTUs, esse tamanho deve ser explicitamente selecionado usando o portal do Azure ou o [PowerShell](../articles/sql-database/sql-database-elastic-pool-manage-powershell.md#change-the-storage-limit-for-an-elastic-pool). Os pools Premium com mais de 1000 GB de armazenamento estão atualmente em versão prévia nas seguintes regiões: Leste dos EUA 2, Oeste dos EUA, Gov. EUA - Virgínia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. O armazenamento máximo por pool para todas as outras regiões é atualmente limitado a 750 GB.
>
