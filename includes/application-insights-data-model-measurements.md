Coleção de medidas personalizadas. Use esta coleção para relatar a medida nomeada associada ao item de telemetria. Os casos de uso típicos são:
- o tamanho da carga de Telemetria de Dependência
- o número de itens da fila processados por Telemetria de Solicitação
- tempo que o cliente demorou para concluir a etapa no assistente de Telemetria de Evento.

Você pode consultar [medidas personalizadas](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) na Análise do Aplicativo:

```
customEvents 
| where customMeasurements != "" 
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Medidas personalizadas associadas ao item de telemetria ao qual estas pertencem. E elas são sujeitas à amostragem com o item de telemetria que contém essas medidas. Use a [Telemetria de Métrica](#metric-telemetry) para controlar a medida que possui valor independente de outro tipo de telemetria. 

Comprimento de chave máximo: 150
