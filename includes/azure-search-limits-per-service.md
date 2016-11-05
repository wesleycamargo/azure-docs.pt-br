O armazenamento é restrito pelo espaço em disco ou por um limite rígido do *número máximo* de índices ou documentos, o que ocorrer primeiro.

| Recurso | Grátis | Básico | S1 | S2 | S3 <br/>(Preview) | S3 HD <br/>(Preview) |
| --- | --- | --- | --- | --- | --- | --- |
| Contrato de nível de serviço (SLA) |Não <sup>1</sup> |Sim |Sim |Sim |Não <sup>1</sup> |Não <sup>1</sup> |
| Armazenamento por serviço |50 MB |2 GB |300 GB |1,2 TB |2,4 TB |200 GB |
| Partições por serviço |N/D |1 |12 |12 |12 |1 |
| Tamanho da partição |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |
| Réplicas |N/D |1 |12 |12 |12 |12 |
| Máximo de índices |3 |5 |50 |200 |200 |1000 |
| Máximo de documentos |10\.000 |1 milhão |180 milhões por serviço, 15 milhões por partição |720 milhões de documentos por serviço, 60 milhões por partição |1,4 bilhão de documentos por serviço, 120 milhões por partição |200 milhões por serviço, 1 milhão por índice |
| Estimativa de QPS (Consultas por segundo) |N/D |~3 por réplica |~15 por réplica |~60 por réplica |>60 por réplica |>60 por réplica |

<sup>1</sup> SKUs de preview e gratuitos não vêm com SLAs. Os SLAs são aplicados depois que um SKU fica disponível para o público geral.

<!---HONumber=AcomDC_0608_2016-->