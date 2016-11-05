A tabela a seguir relaciona as cotas e limites específicos para os Hubs de Eventos do Azure. Para obter mais informações sobre os Hubs de Eventos, confira [Preços de Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/). Para obter informações sobre preços e outras cotas do Barramento de Serviço, consulte a visão geral [Preços do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

| Limite | Escopo | Tipo | Comportamento quando excedido | Valor |
| --- | --- | --- | --- | --- |
| Número de Hubs de Eventos por namespace |Namespace |Estático |As solicitações seguintes para a criação de um novo namespace serão rejeitadas. |10 |
| Número de partições por Hub de Eventos |Entidade |Estático |- |32 |
| Grupos de número de consumidores por Hub de Eventos |Entidade |Estático |- |20 |
| Número de conexões AMQP por namespace |Namespace |Estático |As solicitações seguintes de conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. |5.000 |
| Tamanho máximo de evento |Todo o sistema |Estático |- |256 KB |
| Número de destinatários fora da época por grupo de consumidores |Entidade |Estático |- |5 |
| Período de retenção máximo dos dados do evento |Entidade |Estático |- |1 a 7 dias |
| Unidades máximas da taxa de transferência |Namespace |Estático |A superação do limite de unidade da taxa de transferência fará com que os dados sejam limitados e gerará uma **ServerBusyException**. Você pode solicitar um número maior de unidades de produtividade para uma camada Standard ao preencher um tíquete de suporte. Unidades de produtividade adicionais estão disponíveis em blocos de vinte, em uma base de compra garantida. |20 |

<!---HONumber=AcomDC_0615_2016-->
