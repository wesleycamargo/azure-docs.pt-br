<properties
   pageTitle="Expectativas de visualização do SQL Data Warehouse | Microsoft Azure"
   description="Resumo dos recursos da visualização pública e nossas metas para disponibilidade geral do SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/05/2016"
   ms.author="nicw;barbkess;sonyama"/>


# Expectativas de visualização do SQL Data Warehouse

Este artigo descreve os recursos de visualização do SQL Data Warehouse e nossas metas de GA (disponibilidade geral) para o serviço. Continuamente, atualizaremos essas informações à medida que aprimoramos os recursos da visualização pública.

Nossas metas para o SQL Data Warehouse:

- Desempenho previsível e escalabilidade linear até petabytes de dados
- Alta confiabilidade para todas as operações de data warehouse
- Curto tempo de carregamento de dados para insights de dados em dados relacionais e não relacionais

Trabalharemos continuamente para atingir essas metas durante a visualização do SQL Data Warehouse.

## Desempenho previsível e dimensionável

O SQL Data Warehouse do Azure apresenta Unidades de Data Warehouse (DWUs) como uma forma de medir os recursos de computação (CPUs, memória, E/S de armazenamento) disponíveis para o data warehouse. Aumentar o número de DWUs aumenta os recursos. À medida que aumenta o número de DWUs, o SQL Data Warehouse executa operações em paralelo (por exemplo, a consulta ou o carregamento de dados) em recursos mais distribuídos. Isso reduz a latência e melhora o desempenho.

Qualquer data warehouse tem duas métricas de desempenho fundamentais:

- Taxa de carga. O número de registros que podem ser carregadas no data warehouse por segundo. Nós medimos especificamente o número de registros que podem ser importados, via PolyBase, do Armazenamento de Blob do Azure para uma tabela com um Índice de Repositório de Colunas Clusterizado.
- Taxa de varredura. O número de registros que podem ser recuperados em sequência do data warehouse por segundo. Nós medimos especificamente o número de registros retornados por uma consulta em um Índice de Repositório de Colunas Clusterizado.

Nós estamos medindo alguns importantes aprimoramentos de desempenho e em breve compartilharemos as taxas esperadas. Durante a visualização, faremos aprimoramentos contínuos (por exemplo, aumentar a compactação e o armazenamento em cache) para aumentar essas taxas e garantir que eles sejam dimensionados de maneira previsível.

## Proteção de dados

O SQL Data Warehouse armazena todos os dados no Armazenamento do Azure usando o armazenamento com redundância local. Várias cópias síncronas dos dados são mantidas no datacenter local para garantir a proteção transparente de dados em caso de falhas localizadas.

## Backups

O SQL Data Warehouse do Azure faz backup de todos os dados pelo menos a cada 8 horas usando Instantâneos de Armazenamento do Azure. Esses instantâneos são mantidos por 7 dias. Isso permite restaurar os dados para pelo menos 21 pontos no tempo nos últimos 7 dias até o momento em que o último instantâneo foi definido. Você pode restaurar dados de um instantâneo usando o PowerShell ou APIs REST.

## Confiabilidade da consulta

O SQL Data Warehouse foi criado sobre uma arquitetura MPP (processamento paralelo massivo). O SQL Data Warehouse detecta e migra automaticamente as falhas de nó de controle e de computação. No entanto, uma operação (por exemplo, carregamento de dados ou consulta) pode falhar como resultado de uma falha de nó ou de uma migração. Durante a visualização, estamos fazendo aprimoramentos contínuos para concluir com êxito as operações independentemente de falhas de nó.


## Atualizações e tempo de inatividade

O SQL Data Warehouse será atualizado periodicamente para adicionar novos recursos e instalar correções críticas. Essas atualizações podem causar interrupção e, neste momento, as atualizações não são executadas em uma programação previsível. Se você achar que esse processo causa muita interrupção, incentivamos você a [criar um tíquete de suporte][] para que possamos ajudar a contornar esse processo.


## Próximas etapas

[Introdução][] com visualização pública.

<!--Image references-->

<!--Article references-->
[criar um tíquete de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Introdução]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->