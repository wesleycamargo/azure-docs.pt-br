```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o retorno de chamada que a bomba de mensagens deve iniciar. Por padrão, o tempo de execução do Functions processa várias mensagens simultaneamente. Para direcionar o tempo de execução para processar uma única fila ou mensagem de tópico de cada vez, defina `maxConcurrentCalls` como 1. | 
|prefetchCount|n/d|O PrefetchCount padrão que será usado pelo MessageReceiver subjacente.| 
|autoRenewTimeout|00:05:00|A duração máxima na qual o bloqueio de mensagem será renovado automaticamente.| 
