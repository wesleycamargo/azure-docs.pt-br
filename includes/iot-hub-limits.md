A tabela a seguir lista os limites associados às diferentes camadas de serviço (S1, S2, F1). Para obter informações sobre o custo de cada *unidade* em cada camada, confira [Preços do Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 Standard | S2 Standard | F1 Gratuito |
| -------- | ----------- | ----------- | ------- |
| Mensagens/dia | 400\.000 | 6\.000.000 | 8\.000 |
| Máximo de unidades | 200 | 200 | 1 |

> [AZURE.NOTE] Se você antecipar o uso de mais de 200 unidades com um hub da camada S1 ou S2, entre em contato com o suporte da Microsoft.

A seguinte tabela lista os limites que se aplicam aos recursos do Hub IoT:

| Recurso | Limite |
| -------- | ----- |
| Máximo de hubs IoT por assinatura do Azure | 10 |
| Número máximo de identidades de dispositivo<br/> retornadas em uma única chamada | 1000 |
| Máximo de retenção de mensagens do Hub IoT | 7 dias |
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
| Operações de registro de identidade <br/> (criar, recuperar, listar, atualizar e excluir), <br/> importação/exportação em massa ou individual | 100/min/unidade, até 5000/min |
| Conexões do dispositivo | 120/s/unidade (para S2), 12/s/unidade (para S1). Mínimo de 100/s. |
| Envios do dispositivo para a nuvem | 120/s/unidade (para S2), 12/s/unidade (para S1) <br/> Mínimo de 100/s |
| Envios da nuvem para o dispositivo | 100/min/unidade |
| Recebimentos da nuvem para o dispositivo | 1000/min/unidade |

<!---HONumber=AcomDC_0629_2016-->