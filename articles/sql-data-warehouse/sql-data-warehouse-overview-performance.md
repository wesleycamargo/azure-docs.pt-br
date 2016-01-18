<properties
   pageTitle="Visão geral de desempenho e escala | Microsoft Azure"
   description="Introdução aos recursos de desempenho e escala do SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="barbkess;JRJ@BigBangData.co.uk;mausher;nicw"/>

# Visão geral de desempenho e escala
Ao colocar o Data Warehouse na nuvem, você não precisa mais lidar com problemas de nível baixo de hardware. Já se foram os dias em que você precisava pesquisar o tipo de processador, a quantidade de memória ou o tipo de armazenamento que são necessários para obter excelente desempenho no seu data warehouse. Em vez disso, o SQL Data Warehouse faz essa pergunta: com que rapidez você deseja analisar seus dados?

O SQL Data Warehouse é uma plataforma distribuída de banco de dados baseada na nuvem, desenvolvida para fornecer excelente desempenho em escala onde você é quem está no controle total dos recursos usados para resolver consultas. Ao ajustar o número de unidades de data warehouse alocadas ao seu data warehouse, você pode escalar de modo elástico o tamanho dos recursos do warehouse em questão de segundos. Como uma plataforma distribuída de escala horizontal, o SQL Data Warehouse permite que o data warehouse processe volumes de dados significativos de modo efetivo e eficaz, enquanto deixa você no controle total do custo da solução.

*As informações a seguir aplicam-se ao SQL Data Warehouse do Azure conforme lançadas no preview. Essas informações serão atualizadas continuamente durante o preview à medida que o serviço é aprimorado para disponibilidade geral.*

Nossas metas para o SQL Data Warehouse são: - Desempenho previsível e escalabilidade linear para petabytes de dados - Alta confiabilidade de todas as operações de data warehouse sustentada por um SLA (Contrato de Nível de Serviço) - Tempo curto do carregamento à percepção de dados de dados entre dados relacionais e não relacionais

Trabalharemos ininterruptamente para atingir essas metas durante o preview, isto é, antes da disponibilização geral.

>[AZURE.NOTE]As seções abaixo descrevem o serviço do SQL Data Warehouse do Azure no lançamento do Preview Público. Essas informações serão atualizadas continuamente durante o preview à medida que o serviço é aprimorado para disponibilidade geral.

## Proteção de dados
O SQL Data Warehouse armazena todos os dados no Armazenamento do Azure usando blobs com redundância geográfica. Três cópias síncronas dos dados são mantidas na região local do Azure para garantir a proteção transparente de dados em caso de falhas localizadas (por exemplo, falhas da unidade de armazenamento). Além disso, mais três cópias assíncronas são mantidas em uma região remota do Azure para garantir a proteção de dados em caso de falhas regionais (recuperação de desastres). As regiões locais e remotas são combinadas para manter as latências aceitáveis de sincronização (por exemplo, leste e oeste dos EUA).

## Restauração do banco de dados
O SQL Data Warehouse faz backup de todos os dados a cada 4 horas usando Instantâneos de Armazenamento do Azure. Esses instantâneos são mantidos por 7 dias gratuitamente. Isso permite restaurar os dados para até 42 pontos no tempo nos últimos 7 dias até o momento em que o último instantâneo foi definido. Durante o preview, será introduzida a capacidade de especificar valores diferentes de retenção. Os dados podem ser restaurados de um instantâneo usando o PowerShell ou as APIs REST. Os instantâneos são copiados de modo assíncrono em uma região remota do Azure para capacidade de recuperação adicionada em caso de falhas regionais (recuperação de desastres).

## Confiabilidade
O SQL Data Warehouse é um sistema distribuído com vários componentes, em que o número de componentes aumenta à medida que aumenta o número de DWUs (Unidades de Data Warehouse). O SQL Data Warehouse detecta e reduz automaticamente falhas de computação. No entanto, uma operação (por exemplo, carregamento de dados ou consulta) pode falhar devido a falhas de computação individuais. Durante o preview, faremos aprimoramentos contínuos na confiabilidade da consulta para permitir que a maioria das operações seja concluída com êxito, apesar das falhas.

Com base nos dados de telemetria coletados, a confiabilidade do SQL Data Warehouse é estimada em 98% para cargas de trabalho típicas de data warehouse. Isso significa que, em média, 2 de 100 consultas podem falhar devido a erros do sistema. Esse não é um SLA para o preview, mas um indicador da confiabilidade esperada das consultas que serão executadas. Observe que a probabilidade de uma consulta falhar aumenta com seu tempo de execução (por ex., uma consulta que leva mais de 2 horas tem muito mais probabilidade de falhar do que uma consulta que leva menos de 10 minutos). Durante o preview, faremos aprimoramentos contínuos para garantir o mesmo nível de confiabilidade das operações, independentemente do tempo de execução. Atualizaremos a confiabilidade esperada à medida que liberarmos esses aprimoramentos com a meta de fornecer uma SLA completo na disponibilidade geral.

Durante o preview, o SQL Data Warehouse pode ter até 5 eventos de manutenção por mês para instalação de correções críticas. Cada evento pode causar falhas de consulta por até 2 horas. O tempo depende da quantidade de DWUs usadas pela instância do SQL Data Warehouse. Sempre tentaremos notificar os clientes sobre esses eventos com antecedência de 48 horas a fim de permitir que eles se preparem para isso.

## Desempenho e escala
O SQL Data Warehouse apresenta DWUs (Unidades de Data Warehouse) como uma abstração de recursos computacionais (CPUs, memória, E/S de armazenamento) agregada em vários nós. Aumentar o número de DWUs aumenta os recursos computacionais agregados de uma instância do SQL Data Warehouse. O SQL Data Warehouse distribui operações (por ex., carregamento de dados ou consulta) por toda a infraestrutura de computação na instância para aumentar ou diminuir o desempenho das cargas e consultas conforme o sistema é escalonado verticalmente para cima ou para baixo.

Qualquer data warehouse possui 2 métricas básicas de desempenho: - **Taxa de carga**: o número de registros que podem ser carregados no data warehouse por segundo. Medimos, especificamente, o número de registros que podem ser importados, via PolyBase, do Armazenamento de Blob do Azure para uma tabela com um Índice Column-Store Clusterizado. - **Taxa de exame**: o número de registros que podem ser recuperados sequencialmente do data warehouse por segundo. Essa métrica mede especificamente o número de registros retornados por uma consulta de uma tabela com um Índice Column-Store Clusterizado.

Durante o preview, faremos aprimoramentos contínuos para aumentar essas taxas e garantir que sejam escalonadas de modo previsível.

## Principais conceitos de desempenho

Consulte os artigos a seguir que ajudam a entender alguns dos principais conceitos adicionais de desempenho e escala:

- [desempenho e escala][]
- [modelo de simultaneidade][]
- [criando tabelas][]
- [escolher uma chave de distribuição de hash para sua tabela][]
- [estatísticas para melhorar o desempenho][]

## Próximas etapas
Consulte o artigo [visão geral de desenvolvimento][] para obter algumas diretrizes sobre a criação da sua solução no SQL Data Warehouse.

<!--Image references-->

<!--Article references-->

[desempenho e escala]: sql-data-warehouse-performance-scale.md
[modelo de simultaneidade]: sql-data-warehouse-develop-concurrency.md
[criando tabelas]: sql-data-warehouse-develop-table-design.md
[escolher uma chave de distribuição de hash para sua tabela]: sql-data-warehouse-develop-hash-distribution-key
[estatísticas para melhorar o desempenho]: sql-data-warehouse-develop-statistics.md
[visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_0107_2016-->