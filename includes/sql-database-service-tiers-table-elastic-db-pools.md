
> [AZURE.IMPORTANT] * O banco de dados elástico compartilha o armazenamento de pool, de modo que o armazenamento do banco de dados é limitado ao menor entre o armazenamento de pool restante ou o armazenamento máximo por banco de dados

### Limites de pool elástico Básico

| | |
|---|:---:|
| Número máximo de eDTUs por pool | &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Armazenamento máximo por pool (GB)*| &nbsp;&nbsp;&nbsp;&nbsp;10 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;20 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;39 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;73 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;117 |
| Número máximo de bancos de dados por pool | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| Armazenamento máximo OLTP na memória (GB) por pool| N/D |
| Máximo de trabalhos simultâneos por pool | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| Máximo de logons simultâneos por pool | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| Máximo de sessões simultâneas por pool | 4800 &nbsp;9600 &nbsp; 19200 &nbsp; 28800 &nbsp; 28800 |
| Máximo de eDTUs por banco de dados | 5 |
| Mínimo de eDTUs por banco de dados | 0,5 |
| Armazenamento máximo por banco de dados (GB)* | 2 |
| Restauração pontual | Qualquer ponto nos sete últimos dias |
| Recuperação de desastre | Replicação geográfica ativa |
|||

### Limites de pool elástico Standard

| | |
|---|:---:|
| Número máximo de eDTUs por pool | &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Armazenamento máximo por pool (GB)*| &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Número máximo de bancos de dados por pool | &nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| Armazenamento máximo OLTP na memória (GB) por pool| N/D |
| Máximo de trabalhos simultâneos por pool | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp; 1300 &nbsp;&nbsp; 1850 &nbsp;&nbsp;&nbsp; 2400 |
| Máximo de logons simultâneos por pool | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp; 1300 &nbsp;&nbsp; 1850 &nbsp;&nbsp;&nbsp; 2400 |
| Máximo de sessões simultâneas por pool | 4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| Máximo de eDTUs por banco de dados | 10, 20, 50, 100 |
| Mínimo de eDTUs por banco de dados | 0, 10, 20, 50, 100 |
| Armazenamento máximo por banco de dados (GB)* | 250 |
| Restauração pontual | Qualquer ponto nos 35 últimos dias |
| Recuperação de desastre | Replicação geográfica ativa |


### Limites de pool elástico Premium

| | |
|---|:---:|
| Número máximo de eDTUs por pool | 125 &nbsp;&nbsp;&nbsp; 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 1000 &nbsp;&nbsp;&nbsp; &nbsp;1500 |
| Armazenamento máximo por pool (GB)*| 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 750 |
| Número máximo de bancos de dados por pool | 50 |
| Armazenamento máximo OLTP na memória (GB) por pool| N/D |
| Máximo de trabalhos simultâneos por pool | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp; 1300 &nbsp;&nbsp; 1850 &nbsp;&nbsp;&nbsp; 2400 |
| Máximo de logons simultâneos por pool | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp; 1300 &nbsp;&nbsp; 1850 &nbsp;&nbsp;&nbsp; 2400 |
| Máximo de sessões simultâneas por pool | 4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| Máximo de eDTUs por banco de dados | 125, 250, 500, 1000 |
| Mínimo de eDTUs por banco de dados | 0, 125, 250, 500, 1000 |
| Armazenamento máximo por banco de dados (GB)* | 500 |
| Restauração pontual | Qualquer ponto nos 35 últimos dias |
| Recuperação de desastre | Replicação geográfica ativa |

<!---HONumber=AcomDC_0817_2016-->