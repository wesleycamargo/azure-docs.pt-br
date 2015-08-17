<properties
   pageTitle="O que é o SQL Data Warehouse | Microsoft Azure"
   description="Banco de dados distribuído de nível corporativo na nuvem do Azure com capacidade de processar volumes de petabytes de dados relacionais e não relacionais. Ele é o primeiro data warehouse de nuvem do setor que pode ser expandido, reduzido e pausado questão de segundos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/23/2015"
   ms.author="barbkess;JRJ@BigBangData.co.uk;"/>

# O que é o SQL Data Warehouse?

O SQL Data Warehouse é um banco de dados distribuído de nível corporativo na nuvem do Azure com capacidade de processar volumes de petabytes de dados relacionais e não relacionais. Ele é o primeiro data warehouse de nuvem do setor que pode ser expandido, reduzido e pausado questão de segundos.

O SQL Data Warehouse combina o que há de melhor em confiabilidade e qualidade de banco de dados relacional de nível corporativo com computação em nuvem. Leia mais sobre os componentes principais de design do SQL Data Warehouse.

## Expandir, reduzir ou pausar recursos de computação
Com o SQL Data Warehouse, os dados são armazenados no armazenamento de escala em nuvem e a computação é escalonada de modo independente, permitindo que você pague pelo desempenho de consulta conforme a necessidade. Agora, você pode implantar, expandir, reduzir e, até mesmo, pausar a computação dinamicamente. Aproveite o armazenamento em escala de nuvem e aplique a computação de consulta com base nas necessidades sazonais. Quando em pausa, você paga apenas pelo armazenamento.

> [AZURE.NOTE]As DWUs (Unidades de Data Warehouse) são as unidades de escala para recursos de computação no SQL Data Warehouse.

- Quando você precisar de resultados mais rapidamente, aumente as DWUs e pague pelo aumento; quando não precisar de tanta capacidade de computação, diminua as DWUs e volte a pagar por uma quantidade menor. O poder da computação aumenta na mesma proporção do número de DWUs; dobrar as DWUs, dobra os recursos de computação. 
- Quando não precisar executar consultas, talvez ao anoitecer ou nos fins de semana, pause os recursos de computação para cancelar todas as consultas em execução e remova todas as DWUs alocadas para seu data warehouse. O armazenamento de dados permanece intacto, mas não há cobrança pelos recursos de computação. Quando precisar iniciar a execução de consultas, talvez na segunda-feira de manhã, você poderá retomar os recursos de computação. 

## Índices columnstore e processamento massivamente paralelo para desempenho excepcional
O SQL Data Warehouse usa arquitetura MPP (processamento paralelo massivo) da Microsoft e a tecnologia de índice columnstore do SQL Server para fornecer excelente desempenho.

> [AZURE.NOTE]O MPP é uma abordagem de divisão e domínio que resolve problemas de dados volumosos usando computação paralela. Os dados são divididos e distribuídos entre muitos recursos de computação, e cada recurso opera em sua parte dos dados paralelamente.

- Grande parte do segredo está na tecnologia de consulta distribuída da Microsoft. O SQL Data Warehouse usa um otimizador de consulta avançado que calcula como otimizar as consultas distribuídas com base na avaliação do custo das operações de consulta. Ele também possui técnicas e algoritmos avançados que movem dados eficientemente entre os recursos de computação, conforme a necessidade, para executar a consulta.
- Os índices columnstore são a chave para atingir o desempenho rápido de consulta em consultas de data warehouse. Ao usar o armazenamento baseado em coluna, os índices columnstore obtêm até 5 vezes mais ganhos de compactação em relação ao armazenamento tradicional orientado por linha e até 10 vezes mais ganhos de desempenho em consulta. As consultas de data warehouse funcionam bem em índices columnstore porque frequentemente examinam a tabela inteira ou toda a partição de uma tabela. Por outro lado, as consultas OLTP funcionam bem em índices de árvore binária porque buscam linhas específicas na tabela.


## Nuvem híbrida com experiência do SQL Server de nível corporativo
O SQL Data Warehouse se baseia no mecanismo de banco de dados relacional comprovado do SQL Server e inclui os recursos que você espera de um data warehouse corporativo, incluindo procedimentos armazenados, funções definidas pelo usuário, particionamento de tabela, índices e agrupamento.

> [AZURE.NOTE]Se você já conhece o Transact-SQL, é fácil transferir seu conhecimento para o SQL Data Warehouse. Não importa se você é experiente ou novato, os exemplos da documentação ajudarão a começar.

- O SQL Data Warehouse usa Transact-SQL do SQL Server, índice columnstore e tecnologias do PolyBase juntamente com a arquitetura MPP (processamento massivamente paralelo) do Analytic Platform System para criar essa experiência de data warehouse PaaS (Plataforma como Serviço) exclusiva e integrada.  

- Com a semelhança do Transact-SQL e recursos entre o SQL Server, SQL Data Warehouse, Banco de Dados SQL e Analytics Platform System, você pode desenvolver uma solução adequada às suas necessidades de dados. Você pode decidir onde manter os dados, com base no desempenho, na segurança e nos requisitos de escala e, em seguida, transferir dados, conforme a necessidade, entre o local e a nuvem.


## Consultar em dados relacionais e não relacionais
O Polybase permite consultar dados não relacionais mantidos no armazenamento de blob do Azure ou no HDFS (Sistema de Arquivos do Hadoop), como se fosse uma tabela regular. Use o Polybase para consultar dados não relacionais ou importar dados não relacionais no SQL Data Warehouse.

> [AZURE.NOTE]O Polybase é fácil de usar e permite aproveitar dados de fontes diferentes usando os mesmos comandos T-SQL que você já conhece. Não há necessidade de aprender sobre o HiveQL ou outras linguagens para aproveitar os benefícios do Hadoop.

- O Polybase não depende de sua integração. Ele expõe os mesmos recursos e funcionalidade a todas as distribuições do Hadoop que têm suporte. Os dados lidos pelo Polybase podem estar em vários formatos, incluindo arquivos delimitados ou ORC.
- O PolyBase usa tabelas externas para acessar dados não relacionais. As definições de tabela são armazenadas no SQL Data Warehouse e os dados são armazenados externamente no armazenamento de blob do Azure ou no Hadoop.


## Implantação segura de infraestrutura sem custos de manutenção
O SQL Data Warehouse é implantado facilmente em questão de segundos. Esse serviço é uma oferta totalmente gerenciada que elimina a incômoda tarefa de aplicar patch e fazer a manutenção do software. O SQL Data Warehouse tem backups internos que oferecem suporte à restauração de autoatendimento; o serviço faz backup automaticamente dos dados no Armazenamento do Azure, à medida que cria instantâneos dos pontos de restauração do banco de dados.


## Próximas etapas
Saiba mais sobre a [carga de trabalho do data warehouse]. [Provisione] e carregue os [dados de exemplo] para começar.

<!--Image references-->

<!--Article references-->
[carga de trabalho do data warehouse]: ./sql-data-warehouse-overview-workload.md
[dados de exemplo]: ./sql-data-warehouse-get-started-load-samples.md
[Provisione]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=August15_HO6-->