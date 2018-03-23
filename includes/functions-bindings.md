A tabela a seguir mostra as associações que são suportadas nas duas versões principais do tempo de execução do Azure Functions.

| type | 1.x | 2. x | Gatilho | Entrada | Saída |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Armazenamento de Blobs](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔<sup>1</sup>|✔|✔|✔|  
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|  
| [Grade de Eventos](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [Hubs de Evento](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [Arquivo externo](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [Tabela Externa](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔<sup>1</sup>|✔| |✔|
| [Microsoft Graph<br/>Tabelas do Excel](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>Arquivos do OneDrive](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Email do Outlook](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft Graph<br/>Eventos](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>Tokens de autenticação](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Aplicativos Móveis](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔|✔| |✔|✔|  
| [Hubs de Notificação](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Armazenamento de filas](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔<sup>1</sup>|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Barramento de Serviço](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|  
| [Armazenamento de tabelas](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔<sup>1</sup>| |✔|✔|  
| [Timer](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|
| [Webhooks](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> Em 2.x, todas as associações, exceto HTTP, Timer e Azure Storage, devem ser registradas. Confira [Registrar as extensões de associação](../articles/azure-functions/functions-triggers-bindings.md#register-binding-extensions).

<sup>2</sup> Experimental &mdash; não tem suporte e pode ser abandonado no futuro.
