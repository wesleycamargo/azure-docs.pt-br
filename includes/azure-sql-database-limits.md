| Recurso | Limite padrão |
| --- | --- |
| Tamanho do banco de dados |Depende do nível de desempenho<sup>1</sup> |
| Logons |Depende do nível de desempenho<sup>1</sup> |
| Uso de memória |Concessão de memória de 16 MB por mais de 20 segundos |
| Sessões |Depende do nível de desempenho<sup>1</sup> |
| Tamanho do tempdb |5 GB |
| Duração da transação |24 horas<sup>2</sup> |
| Bloqueios por transação |1 milhão |
| Tamanho por transação |2 GB |
| Porcentagem do espaço total de log usado por transação |20% |
| Máximo de solicitações simultâneas (threads de trabalho) |Depende do nível de desempenho<sup>1</sup> |

<sup>1</sup>O Banco de Dados SQL tem níveis de desempenho como por exemplo: Basic, Standard e Premium. Standard e Premium também são divididos em níveis de desempenho. Para obter limites detalhados por camada e nível de serviço, consulte[Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx).

<sup>2</sup>Se a transação bloquear um recurso exigido por uma tarefa de sistema subjacente, a duração máxima será de 20 segundos.

<!---HONumber=August15_HO7-->