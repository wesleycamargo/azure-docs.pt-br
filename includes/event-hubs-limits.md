A tabela a seguir relaciona as cotas e limites específicos para os Hubs de Eventos do Azure. Para obter informações sobre os preços dos Hubs de Eventos, consulte os [detalhes de preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Escopo | Tipo | Comportamento quando excedido | Valor |
| --- | --- | --- | --- | --- |
| Número de Hubs de Eventos por namespace |Namespace |Estático |As solicitações seguintes para a criação de um novo namespace serão rejeitadas. |10 |
| O número de partições por Hub de Eventos |Entidade |Estático |- |32 |
| Número de grupos de consumidores por Hub de Eventos |Entidade |Estático |- |20 |
| Número de conexões AMQP por namespace |Namespace |Estático |Solicitações subsequentes de conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. |5.000 |
| Tamanho máximo de eventos de Hubs de Eventos|Todo o sistema |Estático |- |256 KB |
| Tamanho máximo do nome de um Hub de Eventos |Entidade |Estático |- |50 caracteres |
| Número de destinatários sem época por grupo de consumidores |Entidade |Estático |- |5 |
| Período de retenção máximo dos dados do evento |Entidade |Estático |- |Um a sete dias |
| Unidades de produtividade máxima |Namespace |Estático |Exceder o limite de unidades de produtividade causará a limitação dos seus dados e gerará um **ServerBusyException**. Você pode solicitar um número maior de unidades de produtividade para uma camada Standard ao preencher um tíquete de suporte. Unidades de produtividade adicionais estão disponíveis em blocos de vinte, em uma base de compra garantida. |20 |



<!--HONumber=Dec16_HO1-->


