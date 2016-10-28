A tabela a seguir lista os limites associados às diferentes camadas de serviço (S1, S2, S3, F1). Para obter informações sobre o custo de cada *unidade* em cada camada, confira [Preços do Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 Standard | S2 Standard | Padrão S3 | F1 Gratuito |
| -------- | ----------- | ----------- | ----------- | ------- |
| Mensagens/dia | 400\.000 | 6\.000.000 | 300\.000.000 | 8\.000 |
| Máximo de unidades | 200 | 200 | 200 | 1 |

> [AZURE.NOTE] Se você antecipar o uso de mais de 200 unidades com um hub da camada S1, S2 ou S3, entre em contato com o suporte da Microsoft.

A seguinte tabela lista os limites que se aplicam aos recursos do Hub IoT:

| Recurso | Limite |
| -------- | ----- |
| Máximo de hubs IoT por assinatura do Azure | 10 |
| Máximo de hubs IoT por assinatura do Azure | 1 |
| Número máximo de identidades de dispositivo<br/> retornadas em uma única chamada | 1000 |
| Retenção máxima de mensagem do Hub IoT para mensagens do dispositivo para nuvem | 7 dias |
| Tamanho máximo da mensagem do dispositivo para a nuvem | 256 KB |
| Tamanho máximo do lote do dispositivo para a nuvem | 256 KB |
| Máximo de mensagens no lote do dispositivo para a nuvem | 500 |
| Tamanho máximo da mensagem da nuvem para o dispositivo | 64 KB |
| TTL máximo para mensagens da nuvem para o dispositivo | 2 dias |
| Contagem máxima de entrega para mensagens <br/> da nuvem para o dispositivo | 100 |
| Contagem máxima de entrega de mensagens de comentários <br/> em resposta a uma mensagem da nuvem para o dispositivo | 100 |
| TTL máximo de mensagens de comentários <br/> em resposta a uma mensagem da nuvem para o dispositivo | 2 dias |

> [AZURE.NOTE] Se precisar de mais de 10 hubs IoT em uma assinatura do Azure, entre em contato com o suporte da Microsoft.

O serviço do Hub IoT restringe as solicitações quando as seguintes cotas são excedidas:

| Restrição | Valor por hub |
| -------- | ------------- |
| Operações de registro de identidade <br/> (criar, recuperar, listar, atualizar e excluir), <br/> importação/exportação em massa ou individual | 5000/min/unidade (para S3) <br/> 100/min/unidade (para S1 e S2). |
| Conexões do dispositivo | 6000/s/unidade (para S3), 120/s/unidade (S2), 12/s/unidade (para S1). <br/>Mínimo de 100/s. |
| Envios do dispositivo para a nuvem | 6000/s/unidade (para S3), 120/s/unidade (S2), 12/s/unidade (para S1). <br/>Mínimo de 100/s. |
| Envios da nuvem para o dispositivo | 5000/min/unidade (para S3), 100/min/unidade (para S1 e S2). |
| Recebimentos da nuvem para o dispositivo | 50000/min/unidade (para S3) 1000/min/unidade (para S1 e S2). |
| Operações de upload de arquivo | 5000 notificações de upload de arquivos/min/unidade (para S3), 100 notificações de upload de arquivos/min/unidade (para S1 e S2). <br/> 10000 URIs de SAS podem estar fora de uma conta de armazenamento ao mesmo tempo.<br/> 10 URIs de SAS/dispositivo podem estar fora ao mesmo tempo. |

<!---HONumber=AcomDC_0824_2016-->