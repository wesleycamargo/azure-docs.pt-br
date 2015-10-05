<properties
   pageTitle="Expectativas de visualização do SQL Data Warehouse | Microsoft Azure"
   description="Resumo dos recursos da visualização pública e nossas metas para disponibilidade geral do SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lvargas"
   manager="tonypet"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="lvargas;twounder;barbkess"/>

# Expectativas de visualização do SQL Data Warehouse

Este artigo descreve os recursos de visualização do SQL Data Warehouse e nossas metas para o serviço para disponibilidade geral (GA, general availability). Continuamente, atualizaremos essas informações à medida que aprimoramos os recursos da visualização pública.

Nossas metas para o SQL Data Warehouse:

- Desempenho previsível e escalabilidade linear até petabytes de dados.
- Alta disponibilidade para todas as operações de data warehouse com o apoio de um contrato de nível de serviço (SLA, service level agreement).

Nós trabalharemos continuamente visando essas metas antes de promover o SQL Data Warehouse para disponibilidade geral.

## Desempenho previsível e dimensionável

O SQL Data Warehouse do Azure apresenta Unidades de Data Warehouse (DWUs) como uma forma de medir os recursos de computação (CPUs, memória, E/S de armazenamento) disponíveis para o data warehouse. Aumentar o número de DWUs aumenta os recursos. À medida que aumenta o número de DWUs, o SQL Data Warehouse executa operações em paralelo (por exemplo, a consulta ou o carregamento de dados) em recursos mais distribuídos. Isso reduz a latência e melhora o desempenho.

Qualquer data warehouse tem duas métricas de desempenho fundamentais:

- Taxa de carga. O número de registros que podem ser carregadas no data warehouse por segundo. Nós medimos especificamente o número de registros que podem ser importados, via PolyBase, do Armazenamento de Blob do Azure para uma tabela com um Índice de Repositório de Colunas Clusterizado. 
- Taxa de varredura. O número de registros que podem ser recuperados em sequência do data warehouse por segundo. Nós medimos especificamente o número de registros retornados por uma consulta em um Índice de Repositório de Colunas Clusterizado.


Nós estamos medindo alguns importantes aprimoramentos de desempenho e em breve compartilharemos as taxas esperadas. Durante a visualização, faremos aprimoramentos contínuos (por exemplo, aumentar a compactação e o armazenamento em cache) para aumentar essas taxas e garantir que eles sejam dimensionados de maneira previsível.


## Alta confiabilidade apoiado por um SLA

### Proteção de dados 

O SQL Data Warehouse armazena todos os dados no Armazenamento do Azure usando blobs com redundância geográfica. Três cópias síncronas dos dados são mantidas na região local do Azure para garantir a proteção transparente de dados em caso de falhas localizadas (por exemplo, falhas da unidade de armazenamento). Além disso, mais três cópias assíncronas são mantidas em uma região remota do Azure para garantir a proteção de dados em caso de falhas regionais (recuperação de desastres). As regiões locais e remotas são combinadas para manter as latências aceitáveis de sincronização (por exemplo, leste e oeste dos EUA).


### Backups

O SQL Data Warehouse do Azure faz backup de todos os dados pelo menos a cada 8 horas usando Instantâneos de Armazenamento do Azure. Esses instantâneos são mantidos por 7 dias. Isso permite restaurar os dados para pelo menos 21 pontos no tempo nos últimos 7 dias até o momento em que o último instantâneo foi definido. Você pode restaurar dados de um instantâneo usando o PowerShell ou APIs REST.

Os instantâneos são copiados de modo assíncrono para uma região remota do Azure para adicionar a capacidade de recuperação em caso de falhas regionais (recuperação de desastres).


### Conclusão da consulta 

O SQL Data Warehouse armazena dados em um ou mais nós de computação, e cada um desses nós armazena alguns dados do usuário e controla a execução da consulta nesses dados. Como parte da arquitetura do processamento altamente paralelo (MPP, massively parallel processing), as consultas são executadas em paralelo em todos os nós de computação. O SQL Data Warehouse detecta e reduz as falhas de nó de computação automaticamente. No entanto, durante a visualização, uma operação (por exemplo, a consulta ou o carregamento de dados) pode falhar devido a falhas de nós individuais. Durante a visualização, estamos fazendo aprimoramentos contínuos para concluir com êxito as operações independentemente de falhas de nó.

Com base nos dados de telemetria, estimamos a confiabilidade atual do SQL Data Warehouse do Azure em 98%. Isso significa que, em média, 2 de 100 consultas podem falhar devido a erros do sistema. Isso não é um SLA. A probabilidade de falha na consulta aumenta com o tempo de execução. Por exemplo, uma consulta executada por mais de 2 horas tem uma probabilidade muito maior de falha que uma consulta que é executada por menos de 10 minutos. Durante a visualização, estamos aperfeiçoando a confiabilidade para garantirmos o mesmo nível de confiabilidade para operações independentemente de seu tempo de execução. Nós atualizaremos a confiabilidade esperada à medida que lançamos esses aprimoramentos. Para disponibilidade geral, a confiabilidade será apoiada por um SLA.

### Tempo de atividade do serviço

Durante a visualização, o SQL Data Warehouse pode ter até 4 eventos de manutenção por mês para instalar correções críticas. Cada evento pode causar falhas de consulta por até 2 horas. O tempo dependerá do número de DWUs alocadas para o serviço. Nós tentaremos notificar sobre esses eventos com 48 horas de antecedência.


## Próximas etapas

[Introdução][] com visualização pública.

<!--Image references-->

<!--Article references-->
[Introdução]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Sept15_HO4-->