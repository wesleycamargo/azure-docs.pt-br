```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|routePrefix|api|O prefixo da rota que se aplica a todas as rotas. Use uma cadeia de caracteres vazia para remover o prefixo padrão. |
|maxOutstandingRequests|-1|O número máximo de solicitações pendentes que serão mantidas em um dado momento (-1 significa ilimitado). O limite inclui solicitações que estão na fila, mas não iniciaram a execução, bem como qualquer execução em andamento. Quaisquer solicitações recebidas acima desse limite são rejeitadas com uma resposta "Muito ocupado" 429. Os chamadores podem usar essa resposta empregar estratégias de repetição baseadas em tempo. Essa configuração controla somente o enfileiramento que ocorre no caminho de execução do host de trabalho. Outras filas, como a fila de solicitação ASP.NET, não são afetadas por essa configuração. |
|maxConcurrentRequests|-1|O número máximo de funções HTTP que serão executadas em paralelo (-1 significa ilimitado). Por exemplo, você pode definir um limite caso suas funções HTTP usem muitos recursos do sistema quando a simultaneidade é alta. Ou se suas funções fazem solicitações de saída a um serviço de terceiros, talvez seja necessário limitar a taxa dessas chamadas.|
|dynamicThrottlesEnabled|false|Faz com que o pipeline de processamento de solicitação verifique periodicamente os contadores de desempenho do sistema. Os contadores incluem conexões, threads, processos, memória e cpu. Se qualquer um dos contadores passar do limite interno (80%), as solicitações serão rejeitadas com uma resposta "Muito ocupado" 429 até ele retornar aos níveis normais.|
