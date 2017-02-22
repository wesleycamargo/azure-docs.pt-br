O armazenamento é restrito pelo espaço em disco ou por um limite rígido do *número máximo* de índices ou documentos, o que ocorrer primeiro.

| Recurso | Grátis | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Contrato de nível de serviço (SLA) |Não <sup>1</sup> |Sim |Sim |Sim |Sim |Sim |
| Armazenamento por partição |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partições por serviço |N/D |1 |12 |12 |12 |3 <sup>2</sup> |
| Tamanho da partição |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |
| Réplicas |N/D |3 |12 |12 |12 |12 |
| Índices máximos |3 |5 |50 |200 |200 |1000 por partição ou 3000 por serviço |
| Indexadores máximos |3 |5 |50 |200 |200 |Não há suporte do indexador |
| Máximo de fontes de dados |3 |5 |50 |200 |200 |Não há suporte do indexador |
| Máximo de documentos |10.000 |1 milhão |15 milhões por partição ou 180 milhões por serviço |60 milhões por partição ou 720 milhões por serviço |120 milhões por partição ou 1,4 bilhão por serviço |1 milhão por serviço, 200 milhões por partição |
| Estimativa de QPS (Consultas por segundo) |N/D |~3 por réplica |~15 por réplica |~60 por réplica |~60 por réplica |>60 por réplica |

<sup>1</sup> SKUs gratuitos e em fase de visualização não acompanham os SLAs (Contratos de Nível de Serviço). Os SLAs são aplicados depois que um SKU fica disponível para o público geral.

<sup>2</sup> S3 HD tem um limite rígido de três partições, que é menor do que o limite de partição do S3. O limite de partição inferior é aplicado porque a contagem de índice de S3 HD é consideravelmente maior. Uma vez que há limites de serviço para os recursos de computação (processamento e armazenamento) e de conteúdo (índices e documentos), o limite de conteúdo é atingido primeiro.


<!--HONumber=Feb17_HO2-->


