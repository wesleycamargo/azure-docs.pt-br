```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|maxPollingInterval|60000|O intervalo máximo em milissegundos entre as votações da fila.| 
|visibilityTimeout|0|O intervalo de tempo entre as repetições quando o processamento de uma mensagem falha.| 
|batchSize|16|O número de mensagens da fila a recuperar e processar em paralelo. O máximo é 32.| 
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila de mensagens suspeitas.| 
|newBatchThreshold|batchSize/2|O limite em que a busca de um novo lote de mensagens é realizada.| 
