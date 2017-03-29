---
title: "O que é o SQL Data Warehouse do Azure? | Microsoft Docs"
description: "Banco de dados distribuído de nível corporativo com capacidade de processar volumes de petabytes de dados relacionais e não relacionais. Ele é o primeiro data warehouse de nuvem do setor que pode ser expandido, reduzido e pausado questão de segundos."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;mausher;kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 6241eb0e7ea091dffcb0ae770f8d89f24a19eb67
ms.openlocfilehash: ff2f688d42924edb1596cb2db474a58748f2b44c
ms.lasthandoff: 01/18/2017


---
# <a name="what-is-azure-sql-data-warehouse"></a>O que é o SQL Data Warehouse do Azure?
O SQL Data Warehouse do Azure é um banco de dados baseado em nuvem e expansível com capacidade de processar volumes imensos de dados, relacionais e não relacionais. Criado em nossa arquitetura MPP (processamento paralelo maciço), o SQL Data Warehouse pode lidar com sua carga de trabalho corporativa.

SQL Data Warehouse:

* Combina o banco de dados relacional do SQL Server com os recursos de expansão na nuvem do Azure. Você pode aumentar, diminuir, pausar ou retomar a computação em minutos ou segundos. Você economiza custos expandindo a CPU quando for necessário e cortando o uso durante os horários de pico.
* Aproveita a plataforma do Azure. É fácil de implantar, apresenta uma manutenção tranquila e é totalmente tolerante a falhas graças ao backups automáticos.
* Complementa o ecossistema do SQL Server. Você pode desenvolver com o T-SQL (Transact-SQL) do SQL Server e ferramentas conhecidas.

Este artigo descreve os principais recursos do SQL Data Warehouse.

## <a name="massively-parallel-processing-architecture"></a>Arquitetura de processamento paralelo maciço
SQL Data Warehouse é um sistema de banco de dados distribuído de processamento extremamente paralelo (MPP). Ao dividir os dados e a funcionalidade de processamento em vários nós, o SQL Data Warehouse pode oferecer uma enorme escalabilidade, muito além de qualquer sistema individual.  Nos bastidores, o SQL Data Warehouse distribui seus dados por muitas unidades de armazenamento compartilhado e de processamento. Os dados são armazenados em um armazenamento com redundância local Premium e são vinculados aos nós de computação para execução da consulta. Com essa arquitetura, o SQL Data Warehouse usa uma abordagem de “dividir para conquistar” a fim de executar cargas e consultas complexas. As solicitações são recebidas pelo nó de controle, otimizadas e passadas aos nós de computação para realizarem seus trabalhos em paralelo.

Ao combinar a arquitetura MPP e os recursos de armazenamento do Azure, o SQL Data Warehouse pode:

* Aumentar ou reduzir o armazenamento de forma independente à computação.
* Aumentar ou reduzir a computação sem mover dados.
* Pausar a capacidade de computação enquanto mantém os dados intactos.
* Retomar a capacidade de computação quase imediatamente.

O diagrama a seguir mostra a arquitetura com mais detalhes.

![Arquitetura do SQL Data Warehouse][1]

**Nó de controle:** o nó de controle gerencia e simplifica as consultas. É o front-end que interage com todos os aplicativos e conexões. No SQL Data Warehouse, o nó Controle é alimentado pelo Banco de Dados SQL, e a conexão com os dois é praticamente igual. Nos bastidores, o nó de controle coordena toda a movimentação e computação de dados necessárias para executar consultas em paralelo em seus dados distribuídos. Quando você envia uma consulta T-SQL ao SQL Data Warehouse, o nó de Controle a transforma em consultas separadas que são executadas paralelamente em cada nó de computação.

**Nós de computação:** os nós de computação servem como a força por trás do SQL Data Warehouse. Eles são Bancos de Dados SQL que armazenam seus dados e processam sua consulta. Quando você adiciona dados, o SQL Data Warehouse distribui as linhas entre seus nós de computação. Os nós de computação são os trabalhadores que executam as consultas paralelas em seus dados. Após o processamento, eles passam os resultados novamente para o nó de controle. Para concluir a consulta, o nó de controle agrega os resultados e retorna o resultado final.

**Armazenamento:** os dados são armazenados no Armazenamento de Blobs do Azure. Quando os nós de computação interagem com seus dados, eles gravam e leem diretamente no e do armazenamento de blobs. Como o armazenamento do Azure expande de modo transparente e amplo, o SQL Data Warehouse pode fazer o mesmo. Como a computação e o armazenamento são independentes, o SQL Data Warehouse pode expandir automaticamente o armazenamento, separadamente da expansão da computação, e vice-versa. O Armazenamento de Blobs do Azure também é totalmente tolerante a falhas, além de simplificar o processo de backup e restauração.

**Serviço de Movimentação de Dados:** o DMS (Serviço de Movimentação de Dados) move dados entre os nós. O DMS oferece aos nós de computação acesso aos dados que eles precisam para junções e agregações. DMS não é um serviço do Azure. É um serviço do Windows executado junto com o Banco de Dados SQL em todos os nós. Como o DMS é executado em segundo plano, você não irá interagir com ele diretamente. No entanto, ao examinar os planos de consulta, você perceberá que eles incluem algumas operações DMS, uma vez que a movimentação dos dados é necessária para executar cada consulta em paralelo.

## <a name="optimized-for-data-warehouse-workloads"></a>Otimizado para cargas de trabalho do data warehouse
A abordagem MPP também é auxiliada por diversas otimizações de desempenho específicas ao data warehouse, incluindo:

* Um otimizador de consulta distribuída e um conjunto de estatísticas complexas em todos os dados. Usando informações sobre distribuição e tamanho dos dados, o serviço é capaz de otimizar consultas avaliando o custo de operações de consulta distribuída específicas.
* Técnicas e algoritmos avançados e integrados ao processo de movimentação de dados a fim de movimentar os dados de forma eficiente que movem dados eficientemente entre os recursos de computação, conforme a necessidade de execução da consulta. Essas operações de movimentação de dados são integradas e todas as otimizações no Serviço de Movimentação de Dados ocorrem automaticamente.
* Índices **columnstore** clusterizados por padrão. Ao usar o armazenamento baseado em coluna, o SQL Data Warehouse obtém em média cinco vezes mais ganhos de compactação em relação ao armazenamento tradicional orientado por linha e 10 vezes ou mais ganhos de desempenho em consulta. Consultas de análise que precisam examinar uma grande quantidade de linhas funcionam muito bem em índices columnstore.

## <a name="predictable-and-scalable-performance"></a>Desempenho previsível e dimensionável
O SQL Data Warehouse separa o armazenamento e a computação, que permite que cada um dimensione de maneira independente. O SQL Data Warehouse pode dimensionar de forma rápida e simples para adicionar recursos de computação adicionais instantaneamente. Complementar isso é o uso do Armazenamento de Blobs do Azure. Os Blobs não apenas oferecem um armazenamento estável e replicado, mas também fornecem a infraestrutura para uma expansão sem esforço e de baixo custo. Usando essa combinação de armazenamento de escala em nuvem e de computação do Azure, o SQL Data Warehouse permite pagar pelo armazenamento e desempenho da consulta quando precisar dela. Alterar a quantidade de computação é tão simples quanto mover um controle deslizante no Portal do Azure para a esquerda ou direita, ou também pode ser agendado com o T-SQL e o PowerShell.

Junto com a capacidade de controlar totalmente o volume de computação, independentemente do armazenamento, o SQL Data Warehouse permite pausar totalmente seu data warehouse, o que significa que você não paga pela computação quando não precisa dela. Enquanto mantém o armazenamento em vigor, toda computação é liberada no pool principal do Azure, economizando seu dinheiro. Quando necessário, basta retomar a computação e ter seu dados e computação disponíveis para sua carga de trabalho.

## <a name="data-warehouse-units"></a>Unidades de Data Warehouse
A alocação de recursos para o SQL Data Warehouse é medida em DWUs (Unidades de Data Warehouse). As DWUs são uma medida de recursos subjacentes, como CPU, memória, IOPS, que são alocados para o SQL Data Warehouse. O aumento do número de DWUs aumenta o desempenho e os recursos. Especificamente, as DWUs ajudam a garantir que:

* Você seja capaz de dimensionar seu data warehouse com facilidade, sem se preocupar com o software ou hardware subjacente.
* Você possa prever o aprimoramento do desempenho para um nível de DWU antes de alterar o tamanho de seu data warehouse.
* O hardware e software subjacentes da sua instância podem ser alterados ou movidos sem afetar o desempenho da carga de trabalho.
* A Microsoft pode fazer ajustes à arquitetura subjacente do serviço sem afetar o desempenho de sua carga de trabalho.
* A Microsoft pode aprimorar rapidamente o desempenho do SQL Data Warehouse de uma forma escalonável e que afeta uniformemente o sistema.

As Unidades de Data Warehouse fornecem uma medida de três métricas precisas altamente correlacionadas ao desempenho da carga de trabalho do data warehouse. O objetivo é que essas métricas de carga de trabalho principais aumentem linearmente com as DWUs que você escolheu para o data warehouse.

**Verificação/Agregação:** essa métrica de carga de trabalho usa um padrão de data warehouse de consulta que verifica um grande número de linhas e, em seguida, executa uma agregação complexa. Essa é uma operação que exige bastante E/S e CPU.

**Carga:** essa métrica mede a capacidade de incluir dados no serviço. As cargas são concluídas com PolyBase carregando um conjunto de dados representativo do Armazenamento de Blobs do Azure. Essa métrica é projetada para enfatizar os aspectos de Rede e de CPU do serviço.

**CTAS (Criar Tabela Como Selecionada):** CTAS mede a capacidade de copiar uma tabela. Isso envolve a leitura de dados do armazenamento, distribuição em todos os nós do dispositivo e nova gravação no armazenamento. É uma operação com uso intenso de CPU, rede e E/S.

## <a name="pause-and-scale-on-demand"></a>Pausar e escalar sob demanda
Quando você precisar de resultados mais rápidos, aumente suas DWUs e pague por mais desempenho. Quando precisar de menos potência de computação, diminua suas DWUs e pague somente pelo que precisa. Talvez você pense em trocar suas DWUs nestes cenários:

* Quando você não precisar executar consultas, talvez durante a noite ou nos finais de semana, desative suas consultas. Depois, pause seus recursos de computação para evitar pagar por DWUs quando não precisar mais delas.
* Quando o sistema tiver uma demanda baixa, considere a redução da DWU para um tamanho pequeno. Você ainda pode acessar os dados, mas com uma economia significativa.
* Ao executar uma operação de transformação ou carregamento de dados pesados, convém escalar verticalmente para que os dados fiquem disponíveis mais rapidamente.

Para entender qual é o valor ideal de DWU é, tente escalar verticalmente, para cima e para baixo, e executar algumas consultas após carregar os dados. Como o dimensionamento é rápido, você pode experimentar vários níveis diferentes de desempenho durante uma hora ou menos.  Lembre que o SQL Data Warehouse foi projetado para processar grandes quantidades de dados e para ver suas verdadeiras capacidades para dimensionar, especialmente nas escalas maiores que oferecemos, você desejará usar um grande conjunto de dados que se aproxima ou ultrapassa 1 TB.

## <a name="built-on-sql-server"></a>Desenvolvido a partir do SQL Server
O SQL Data Warehouse tem base no mecanismo de banco de dados relacional do SQL Server e inclui muitos dos recursos que você espera de um data warehouse corporativo. Se você já conhece o T-SQL, é fácil transferir seu conhecimento para o SQL Data Warehouse. Se você estiver avançado ou apenas iniciando, os exemplos na documentação ajudarão a começar. Em geral, você pode pensar na forma como construímos os elementos de linguagem do SQL Data Warehouse da seguinte maneira:

* O SQL Data Warehouse usa a sintaxe do T-SQL para várias operações. Ele também oferece suporte a um amplo conjunto de construtores SQL tradicionais, como procedimentos armazenados, funções definidas pelo usuário, particionamento de tabelas, índices e agrupamentos.
* O SQL Data Warehouse também contém vários recursos recentes do SQL Server, incluindo índices **columnstore** clusterizados, integração de PolyBase e auditoria de dados (completo com avaliação de ameaças).
* Talvez determinados elementos de linguagem T-SQL que são menos comuns para cargas de trabalho de armazenamento de dados, ou que são mais recentes para o SQL Server, não estejam disponíveis no momento. Para saber mais, veja a [Documentação de migração][Migration documentation].

Com a semelhança do Transact-SQL e recursos entre o SQL Server, SQL Data Warehouse, Banco de Dados SQL e Analytics Platform System, você pode desenvolver uma solução adequada às suas necessidades de dados. Você pode decidir onde manter os dados, com base no desempenho, na segurança e nos requisitos de escala e, em seguida, transferir dados, conforme a necessidade, entre sistemas diferentes.

## <a name="data-protection"></a>Proteção de dados
O SQL Data Warehouse armazena todos os dados no armazenamento com redundância local do Azure Premium. Várias cópias síncronas dos dados são mantidas no datacenter local para garantir a proteção transparente de dados em caso de falhas localizadas. Além disso, o SQL Data Warehouse faz o backup automaticamente dos bancos de dados ativos (sem pausa) em intervalos regulares usando Instantâneos de Armazenamento do Azure. Para saber mais sobre como o backup e a restauração funcionam, confira a [Visão geral de backup e restauração][Backup and restore overview].

## <a name="integrated-with-microsoft-tools"></a>Integrado com as ferramentas da Microsoft
O SQL Data Warehouse também integra muitas das ferramentas com as quais os usuários do SQL Server podem estar familiarizados. Estão incluídos:

**Ferramentas tradicionais do SQL Server:** o SQL Data Warehouse é totalmente integrado ao SQL Server Analysis Services, aos serviços de integração e de relatório.

**Ferramentas baseadas em nuvem:** o SQL Data Warehouse pode ser usado junto com várias ferramentas novas do Azure, incluindo o Data Factory, o Stream Analytics, o Aprendizado de Máquina e o Power BI. Para obter uma lista mais completa, confira [Visão geral das ferramentas integradas][Integrated tools overview].

**Ferramentas de terceiros:** vários provedores de ferramenta de terceiros têm integração certificada de suas ferramentas com o SQL Data Warehouse. Para obter uma lista completa, veja [Parceiros de solução do SQL Data Warehouse][SQL Data Warehouse solution partners].

## <a name="hybrid-data-sources-scenarios"></a>Cenários de fontes de dados híbridos
O uso do SQL Data Warehouse com o PolyBase fornece a capacidade sem precedentes de mover os dados entre seus ecossistemas, desbloqueando a capacidade de configuração avançada de cenários híbridos com fontes de dados não relacionais e locais.

O Polybase permite que você aproveite dados de fontes diferentes usando comandos conhecidos de T-SQL. O Polybase permite consultar dados não relacionais mantidos no Armazenamento de Blobs do Azure, como se fosse uma tabela normal. Use o Polybase para consultar dados não relacionais ou importar dados não relacionais no SQL Data Warehouse.

* O PolyBase usa tabelas externas para acessar dados não relacionais. As definições de tabela são armazenadas no SQL Data Warehouse, e podem ser acessadas usando o SQL e ferramentas usadas normalmente para acessar dados relacionais.
* O Polybase não depende de sua integração. Ele expõe os mesmos recursos e funcionalidade a todas as fontes as quais oferece  suporte. Os dados lidos pelo Polybase podem estar em vários formatos, incluindo arquivos delimitados ou ORC.
* O PolyBase pode ser usado para acessar o armazenamento de blobs que também está sendo usado como armazenamento para um cluster do HDInsight. Isso fornece a você acesso aos mesmos dados com ferramentas relacionais e não relacionais.

## <a name="sla"></a>Contrato de Nível de Serviço
O SQL Data Warehouse oferece um SLA (Contrato de Nível de Serviço) no nível do produto como parte do SLA do Microsoft Online Services. Para saber mais, visite [SLA para o SQL Data Warehouse][SLA for SQL Data Warehouse]. Para obter informações de SLA sobre todos os outros produtos, visite a página do Azure [Contratos de Nível de Serviço] ou baixe-os na página [Licenciamento por Volume][Volume Licensing]. 

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe um pouco sobre o SQL Data Warehouse, saiba como [criar um SQL Data Warehouse][create a SQL Data Warehouse] e [carregar dados de amostra][load sample data] rapidamente. Se você ainda não conhece o Azure, poderá achar o [Glossário do Azure][Azure glossary] útil à medida que encontrar nova terminologia. Ou, dê uma olhada em alguns desses outros recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da Equipe Consultoria para Clientes]
* [Criar um tíquete de suporte]
* [Fórum do MSDN]
* [Fórum Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar um tíquete de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Histórias de sucesso de clientes]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da Equipe Consultoria para Clientes]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Contratos de Nível de Serviço]: https://azure.microsoft.com/en-us/support/legal/sla/

