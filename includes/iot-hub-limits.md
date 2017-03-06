A tabela a seguir lista os limites associados às diferentes camadas de serviço (S1, S2, S3, F1). Para obter informações sobre o custo de cada *unidade* em cada camada, confira [Preços do Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 Standard | S2 Standard | Padrão S3 | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Mensagens/dia |400.000 |6.000.000 |300.000.000 |8.000 |
| Máximo de unidades |200 |200 |10 |1 |

> [!NOTE]
> Se você prevê o uso de mais de 200 unidades com um hub da camada S1 ou S2 ou 10 unidades com um hub da camada S3, entre em contato com o suporte da Microsoft.
> 
> 

A seguinte tabela lista os limites que se aplicam aos recursos do Hub IoT:

| Recurso | Limite |
| --- | --- |
| Máximo de hubs IoT por assinatura do Azure |10 |
| Máximo de hubs IoT por assinatura do Azure |1 |
| Número máximo de identidades de dispositivo<br/> retornadas em uma única chamada |1000 |
| Retenção máxima de mensagem do Hub IoT para mensagens do dispositivo para nuvem |7 dias |
| Tamanho máximo da mensagem do dispositivo para a nuvem |256 KB |
| Tamanho máximo do lote do dispositivo para a nuvem |256 KB |
| Máximo de mensagens no lote do dispositivo para a nuvem |500 |
| Tamanho máximo da mensagem da nuvem para o dispositivo |64 KB |
| TTL máximo para mensagens da nuvem para o dispositivo |2 dias |
| Contagem máxima de entrega para mensagens  <br/> da nuvem para o dispositivo |100 |
| Contagem máxima de entrega de mensagens de comentários  <br/> em resposta a uma mensagem da nuvem para o dispositivo |100 |
| TTL máximo de mensagens de comentários  <br/> em resposta a uma mensagem da nuvem para o dispositivo |2 dias |
| Tamanho máximo de dispositivo gêmeo <br/> (marcas, propriedades relatadas e propriedades desejadas) | 8 KB |
| Tamanho máximo do valor de cadeia de caracteres do dispositivo gêmeo | 512 bytes |
| Profundidade máxima de objeto em dispositivo gêmeo | 5 |
| Tamanho máximo da carga do método direto | 8 KB |
| Máximo de retenção de histórico do trabalho | 30 dias |
| Máximo de trabalhos simultâneos | 10 (para S3), 5 para (S2), 1 (para S1) |
| Pontos de extremidade adicionais máximo | 10 (para S1, S2, S3) |
| Máxima de regras de roteamento de mensagens | 100 (para S1, S2, S3) |


> [!NOTE]
> Se precisar de mais de 10 Hubs IoT pagos em uma assinatura do Azure, entre em contato com o suporte da Microsoft.
> 
> 

O serviço do Hub IoT restringe as solicitações quando as seguintes cotas são excedidas:

| Restrição | Valor por hub |
| --- | --- |
| Operações de registro de identidade <br/> (criar, recuperar, listar, atualizar e excluir), <br/> importação/exportação em massa ou individual |5000/min/unidade (para S3)  <br/> &100;/min/unidade (para S1 e S2). |
| Conexões do dispositivo |6000/s/unidade (para S3), 120/s/unidade (S2), 12/s/unidade (para S1). <br/>Mínimo de 100/s. |
| Envios do dispositivo para a nuvem |6000/s/unidade (para S3), 120/s/unidade (S2), 12/s/unidade (para S1). <br/>Mínimo de 100/s. |
| Envios da nuvem para o dispositivo |5000/min/unidade (para S3), 100/min/unidade (para S1 e S2). |
| Recebimentos da nuvem para o dispositivo |50000/min/unidade (para S3) 1000/min/unidade (para S1 e S2). |
| Operações de upload de arquivo |5000 notificações de upload de arquivos/min/unidade (para S3), 100 notificações de upload de arquivos/min/unidade (para S1 e S2). <br/> 10000 URIs de SAS podem estar fora de uma conta de Armazenamento do Azure ao mesmo tempo.<br/> &10; URIs de SAS/dispositivo podem estar fora ao mesmo tempo. |
| Métodos diretos | 1500/s/unidade (para S3), 30/s/unidade (para S2), 10/s/unidade (para S1) |
| Leituras de dispositivo gêmeo | 50/s/unidade (para S3), máximo de 10/s ou 1/s/unidade (para S2), 10/s (para S1) |
| Atualizações de dispositivos gêmeos | 50/s/unidade (para S3), máximo de 10/s ou 1/s/unidade (para S2), 10/s (para S1) |
| Operações de trabalhos <br/> (criar, atualizar, listar, excluir) | 5000/min/unidade (S3), 100/min/unidade (para S2), 100/min/unidade (S1) |
| Taxa de transferência de operação de trabalhos por dispositivo | 50/s/unidade (para S3), máximo de 10/s ou 1/s/unidade (para S2), 10/s (para S1) |