O Data factory é um serviço multilocatário que tem os seguintes limites padrão em vigor para certificar-se de que as assinaturas de clientes são protegidas das cargas de trabalho umas das outras. Entrando em contato com o suporte, é possível aumentar muitos dos limites facilmente em sua assinatura até o limite máximo.

| **Recurso** | **Limite padrão** | **Limite máximo** |
| --- | --- | --- |
| data factories em uma assinatura do Azure |50 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| pipelines em um data factory |2500 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| conjuntos de dados em um data factory |5.000 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| fatias simultâneas por conjunto de dados |10 |10 |
| bytes por objeto para objetos do pipeline <sup>1</sup> |200 KB |200 KB |
| bytes por objeto para objetos de conjunto de dados e serviço vinculado <sup>1</sup> |100 KB |2000 KB |
| núcleos de cluster sob demanda HDInsight em uma assinatura <sup>2</sup> |60 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Unidade de movimentação de dados de nuvem <sup>3</sup> |32 |[Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Contagem novas tentativas de execução de atividade do pipeline |1000 |MaxInt (32 bits) |

<sup>1</sup> Objetos de pipeline, conjunto de dados e serviço vinculado representam um agrupamento lógico de sua carga de trabalho. Os limites desses objetos não estão relacionados à quantidade de dados que você pode mover e processar com o serviço Azure Data Factory. O data factory foi desenvolvido para ser dimensionado de modo a lidar com petabytes de dados.

<sup>2</sup> Os núcleos sob demanda HDInsight são alocados da assinatura que contém o data factory. Como resultado, o limite acima é o limite de núcleo imposto pelo Data Factory para núcleos HDInsight sob demanda e é diferente do limite de núcleo associado à assinatura do Azure.

<sup>3</sup> A DMU (unidade de movimentação de dados de nuvem) está sendo usada em uma operação de cópia de nuvem para nuvem. É uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos da rede) de uma unidade única no Data Factory. Você pode obter uma taxa de transferência de cópia mais alta aproveitando mais DMUs em alguns cenários. Veja a seção [Unidades de movimentação de dados em nuvem](../articles/data-factory/data-factory-copy-activity-performance.md#cloud-data-movement-units) para exibir os detalhes.

| **Recurso** | **Limite inferior padrão** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre novas tentativas |1 segundo |1 segundo |
| Valor de tempo limite de nova tentativa |1 segundo |1 segundo |

### <a name="web-service-call-limits"></a>Limites de chamada de serviço Web
O Azure Resource Manager tem limites para chamadas à API. Você pode fazer chamadas de API em um intervalo dentro dos [limites de API do Gerenciador de Recursos do Azure](../articles/azure-subscription-service-limits.md#resource-group-limits).
