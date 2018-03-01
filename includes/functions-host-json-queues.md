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

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------| 
|maxPollingInterval|60000|O intervalo máximo em milissegundos entre as votações da fila.| 
|visibilityTimeout|0|O intervalo de tempo entre as repetições quando o processamento de uma mensagem falha.| 
|batchSize|16|O número de mensagens em fila que o tempo de execução de Funções recupera simultaneamente e processa em paralelo. Quando o número que está sendo processado chega até `newBatchThreshold`, o tempo de execução obtém outro lote e começa a processar as mensagens. Portanto, o número máximo de mensagens simultâneas que estão sendo processadas por função é `batchSize` mais `newBatchThreshold`. Esse limite se aplica separadamente a cada função acionada por fila. <br><br>Se quiser evitar uma execução paralela para mensagens recebidas em uma fila, é possível definir `batchSize` como 1. No entanto, essa configuração elimina a simultaneidade desde que seu aplicativo de função seja executado em uma única máquina virtual (VM). Se o aplicativo de função se expande para várias VMs, cada VM pode executar uma instância de cada função acionada por fila.<br><br>O máximo `batchSize` é 32. | 
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila de mensagens suspeitas.| 
|newBatchThreshold|batchSize/2|Sempre que o número de mensagens processadas simultaneamente chega a esse número, o tempo de execução recupera outro lote.| 



