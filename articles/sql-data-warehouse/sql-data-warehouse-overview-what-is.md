<properties
   pageTitle="O que é o SQL Data Warehouse do Azure? | Microsoft Azure"
   description="Banco de dados distribuído de nível corporativo com capacidade de processar volumes de petabytes de dados relacionais e não relacionais. Ele é o primeiro data warehouse de nuvem do setor que pode ser expandido, reduzido e pausado questão de segundos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/07/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# O que é o SQL Data Warehouse do Azure?

O SQL Data Warehouse do Azure é um banco de dados baseado em nuvem e expansível com capacidade de processar volumes imensos de dados, relacionais e não relacionais. Criado em nossa arquitetura MPP (processamento paralelo maciço), o SQL Data Warehouse pode lidar com sua carga de trabalho corporativa.

SQL Data Warehouse:

- Combina o banco de dados relacional do SQL Server com os recursos de expansão na nuvem do Azure. Você pode aumentar, diminuir, pausar ou retomar a computação em segundos. Você economiza custos expandindo a CPU quando for necessário e cortando o uso durante os horários de pico.
- Aproveita a plataforma do Azure. É fácil de implantar, apresenta uma manutenção tranquila e é totalmente tolerante a falhas graças ao backups automáticos.
- Complementa o ecossistema do SQL Server. Você pode desenvolver com o T-SQL (Transact-SQL) do SQL Server e ferramentas conhecidas.

Este artigo descreve os principais recursos do SQL Data Warehouse.

## Arquitetura MPP (processamento paralelo maciço)

O SQL Data Warehouse usa a arquitetura MPP (processamento paralelo maciço) da Microsoft, projetada para executar alguns dos maiores data warehouse corporativos locais do mundo.

Atualmente, a arquitetura MPP distribui os dados entre 60 unidades de armazenamento e processamento do tipo shared-nothing. Os dados são armazenados em um armazenamento com redundância local Premium e são vinculados aos nós de computação para execução da consulta. Com essa arquitetura, o SQL Data Warehouse usa uma abordagem de “dividir para conquistar” a fim de executar consultas complexas de T-SQL. Durante o processamento, o nó de controle simplifica cada consulta e, depois, passa o trabalho para os nós de computação, assim, cada um deles executa sua parte dos dados em paralelo.

Ao combinar a arquitetura MPP e os recursos de armazenamento do Azure, o SQL Data Warehouse pode:

- Aumentar ou reduzir o armazenamento de forma independente à computação.
- Aumentar ou reduzir a computação sem mover dados.
- Pausar a capacidade de computação enquanto mantém os dados intactos.
- Retomar a capacidade de computação quase imediatamente.

O diagrama a seguir mostra a arquitetura com mais detalhes.

![Arquitetura do SQL Data Warehouse][1]


- **Nó de controle:** o nó de controle gerencia e simplifica as consultas. É o front-end que interage com todos os aplicativos e conexões. No SQL Data Warehouse, o nó Controle é alimentado pelo Banco de Dados SQL, e a conexão com os dois é praticamente igual. Nos bastidores, o nó de controle coordena toda a movimentação e computação de dados necessárias para executar consultas em paralelo em seus dados distribuídos. Quando você envia uma consulta T-SQL ao SQL Data Warehouse, o nó de Controle a transforma em consultas separadas que são executadas paralelamente em cada nó de computação.

- **Nós de computação:** os nós de computação servem como a força por trás do SQL Data Warehouse. Eles são Bancos de Dados SQL que armazenam seus dados e processam sua consulta. Quando você adiciona dados, o SQL Data Warehouse distribui as linhas entre seus nós de computação. Os nós de computação são os trabalhadores que executam as consultas paralelas em seus dados. Após o processamento, eles passam os resultados novamente para o nó de controle. Para concluir a consulta, o nó de controle agrega os resultados e retorna o resultado final.

- **Armazenamento:** os dados são armazenados no Armazenamento de Blobs do Azure. Quando os nós de computação interagem com seus dados, eles gravam e leem diretamente no e do armazenamento de blobs. Como o armazenamento do Azure expande de modo transparente e ilimitado, o SQL Data Warehouse pode fazer o mesmo. Como a computação e o armazenamento são independentes, o SQL Data Warehouse pode expandir automaticamente o armazenamento, separadamente da expansão da computação, e vice-versa. O Armazenamento de Blobs do Azure também é totalmente tolerante a falhas, além de simplificar o processo de backup e restauração.

- **Serviço de Movimentação de Dados:** o DMS (Serviço de Movimentação de Dados) move dados entre os nós. O DMS oferece aos nós de computação acesso aos dados que eles precisam para junções e agregações. DMS não é um serviço do Azure. É um serviço do Windows executado junto com o Banco de Dados SQL em todos os nós. Como o DMS é executado em segundo plano, você não irá interagir com ele diretamente. No entanto, ao examinar os planos de consulta, você perceberá que eles incluem algumas operações DMS, uma vez que a movimentação dos dados é necessária para executar cada consulta em paralelo.


## Desempenho de consulta otimizado

A abordagem MPP também é auxiliada por diversas otimizações de desempenho específicas ao data warehouse, incluindo:

- Um otimizador de consulta distribuída e um conjunto de estatísticas complexas em todos os dados. Usando informações sobre distribuição e tamanho dos dados, o serviço é capaz de otimizar consultas avaliando o custo de operações de consulta distribuída específicas.

- Técnicas e algoritmos avançados e integrados ao processo de movimentação de dados a fim de movimentar os dados de forma eficiente que movem dados eficientemente entre os recursos de computação, conforme a necessidade de execução da consulta. Essas operações de movimentação de dados são integradas e todas as otimizações no Serviço de Movimentação de Dados ocorrem automaticamente.

- Índices **columnstore** clusterizados por padrão. Ao usar o armazenamento baseado em coluna, o SQL Data Warehouse obtém até cinco vezes mais ganhos de compactação em relação ao armazenamento tradicional orientado por linha e até 10 vezes mais ganhos de desempenho em consulta. Consultas de análise que precisam examinar uma grande quantidade de linhas funcionam muito bem em índices columnstore.

## Armazenamento e computação escalonáveis

A arquitetura do SQL Data Warehouse apresenta armazenamento e computação separados, permitindo que cada um seja dimensionado independentemente. A estrutura de implantação simples e rápida do Banco de Dados SQL permite que computadores adicionais estejam disponíveis imediatamente. Complementar isso é o uso do Armazenamento de Blobs do Azure. Os Blobs não apenas oferecem um armazenamento estável e replicado, mas também fornecem a infraestrutura para uma expansão sem esforço e de baixo custo. Usando essa combinação de armazenamento de escala em nuvem e de computação do Azure, o SQL Data Warehouse permite pagar pelo armazenamento de desempenho da consulta à medida que você precisar dela e quando precisar dela. Alterar a quantidade de computação é tão simples quanto mover um controle deslizante no Portal do Azure para a esquerda ou direita, mas também pode ser agendado com o T-SQL e o PowerShell.

Junto com a capacidade de controlar totalmente o volume de computação, independentemente do armazenamento, o SQL Data Warehouse permite pausar totalmente seu data warehouse. Enquanto mantém o armazenamento em vigor, toda computação é liberada no pool principal do Azure, economizando seu dinheiro imediatamente. Quando necessário, basta retomar a computação e ter seu dados e computação disponíveis para sua carga de trabalho.

## Unidades de Data Warehouse

O uso de computação no SQL Data Warehouse é medido usando Unidades do SQL Data Warehouse (DWUs). DWUs são uma medida do poder subjacente que seu data warehouse tem, e são projetados para garantir que você tenha uma quantidade padrão de desempenho associada ao seu warehouse em qualquer momento. Especificamente, as DWUs ajudam a garantir que:

- Você seja capaz de dimensionar seu data warehouse com facilidade, sem se preocupar com o software ou hardware subjacente.

- Você possa prever o aprimoramento do desempenho para um nível de DWU antes de alterar o tamanho de seu data warehouse.

- O hardware e software subjacentes da sua instância podem ser alterados ou movidos sem afetar o desempenho da carga de trabalho.

- A Microsoft pode fazer ajustes à arquitetura subjacente do serviço sem afetar o desempenho de sua carga de trabalho.

- A Microsoft pode aprimorar rapidamente o desempenho do SQL Data Warehouse de uma forma escalonável e afetando uniformemente o sistema.

Especificamente, as Unidades de Data Warehouse fornecem uma medida de três métricas precisas altamente correlacionadas ao desempenho da carga de trabalho do data warehouse. O objetivo da Microsoft é que, para nossa disponibilidade geral, estas métricas de carga de trabalho principais aumentem linearmente com as DWUs que você escolheu para o data warehouse.

**Verificação/Agregação:** essa métrica de carga de trabalho usa um padrão de data warehouse de consulta que verifica um grande número de linhas e, em seguida, executa uma agregação complexa. Essa é uma operação que exige bastante E/S e CPU.

**Carga:** essa métrica mede a capacidade de incluir dados no serviço. As cargas são concluídas com PolyBase carregando um conjunto de dados representativo do Armazenamento de Blobs do Azure. Essa métrica é projetada para enfatizar os aspectos de Rede e de CPU do serviço.

**CTAS (Criar Tabela Como Selecionada):** CTAS mede a capacidade de criar a cópia de uma tabela. Isso envolve a leitura de dados do armazenamento, distribuição em todos os nós do dispositivo e nova gravação no armazenamento. É uma operação com uso intenso de CPU e rede.

## Pausar e escalar sob demanda

Quando você precisar de resultados mais rápidos, aumente suas DWUs e pague por mais desempenho. Quando precisar de menos potência de computação, diminua suas DWUs e pague somente pelo que precisa. Talvez você pense em trocar suas DWUs nestes cenários:

- Quando você não precisar executar consultas, talvez durante a noite ou nos finais de semana, desative suas consultas. Depois, pause seus recursos de computação para evitar pagar por DWUs quando não precisar mais delas.

- Quando o sistema tiver uma demanda baixa, considere a redução da DWU para um tamanho pequeno. Você ainda pode acessar os dados, mas com uma economia significativa.

- Ao executar uma operação de transformação ou carregamento de dados pesados, convém escalar verticalmente para que os dados fiquem disponíveis mais rapidamente.

- Para entender qual é o valor ideal de DWU é, tente escalar verticalmente, para cima e para baixo, e executar algumas consultas após carregar os dados. Como o dimensionamento é rápido, você pode experimentar vários níveis diferentes de desempenho durante uma hora ou menos.

> [AZURE.NOTE] Devido à arquitetura do SQL Data Warehouse, talvez você não veja o desempenho esperado com volumes de dados mais baixos. Comece com volumes de dados de 1 TB ou mais para obter uma verdadeira indicação dos benefícios no desempenho.

## Desenvolvido a partir do SQL Server

O SQL Data Warehouse tem base no mecanismo de banco de dados relacional do SQL Server e inclui muitos dos recursos que você espera de um data warehouse corporativo. Se você já conhece o T-SQL, é fácil transferir seu conhecimento para o SQL Data Warehouse. Se você estiver avançado ou apenas iniciando, os exemplos na documentação ajudarão a começar. Em geral, você pode pensar na forma como construímos os elementos de linguagem do SQL Data Warehouse da seguinte maneira:

- O SQL Data Warehouse usa a sintaxe do T-SQL para várias operações. Ele também oferece suporte a um amplo conjunto de construtores SQL tradicionais, como procedimentos armazenados, funções definidas pelo usuário, particionamento de tabelas, índices e agrupamentos.

- O SQL Data Warehouse também contém vários recursos recentes do SQL Server, incluindo índices **columnstore** clusterizados, integração de PolyBase e auditoria de dados (completo com avaliação de ameaças).

- Talvez determinados elementos de linguagem T-SQL que são menos comuns para cargas de trabalho de armazenamento de dados, ou que são mais recentes para o SQL Server, não estejam disponíveis no momento. Para obter mais informações, consulte a [Documentação de migração][].

Com a semelhança do Transact-SQL e recursos entre o SQL Server, SQL Data Warehouse, Banco de Dados SQL e Analytics Platform System, você pode desenvolver uma solução adequada às suas necessidades de dados. Você pode decidir onde manter os dados, com base no desempenho, na segurança e nos requisitos de escala e, em seguida, transferir dados, conforme a necessidade, entre sistemas diferentes.

## Integrado com as ferramentas da Microsoft

O SQL Data Warehouse também integra muitas das ferramentas com as quais os usuários do SQL Server podem estar familiarizados. Estão incluídos:

**Ferramentas tradicionais do SQL Server:** o SQL Data Warehouse é totalmente integrado ao SQL Server Analysis Services, aos serviços de integração e de relatório.

**Ferramentas baseadas em nuvem:** o SQL Data Warehouse pode ser usado junto com várias ferramentas novas do Azure, incluindo o Data Factory, o Stream Analytics, o Aprendizado de Máquina e o Power BI. Para obter uma lista mais completa, confira [Visão geral das ferramentas integradas][].

**Ferramentas de terceiros:** vários provedores de ferramenta de terceiros têm integração certificada de suas ferramentas com o SQL Data Warehouse. Para obter uma lista completa, confira [Parceiros de solução do SQL Data Warehouse][].

## Cenários de fontes de dados híbridos

O uso do SQL Data Warehouse com PolyBase fornece a capacidade sem precedentes de mover dados entre seus ecossistemas, desbloqueando a capacidade de configuração avançada de cenários híbridos com fontes de dados não relacionais e locais.

O Polybase permite que você aproveite dados de fontes diferentes usando comandos conhecidos de T-SQL. O Polybase permite consultar dados não relacionais mantidos no Armazenamento de Blobs do Azure, como se fosse uma tabela normal. Use o Polybase para consultar dados não relacionais ou importar dados não relacionais no SQL Data Warehouse.

- O PolyBase usa tabelas externas para acessar dados não relacionais. As definições de tabela são armazenadas no SQL Data Warehouse, e podem ser acessadas usando o SQL e ferramentas usadas normalmente para acessar dados relacionais.

- O Polybase não depende de sua integração. Ele expõe os mesmos recursos e funcionalidade a todas as fontes as quais oferece suporte. Os dados lidos pelo Polybase podem estar em vários formatos, incluindo arquivos delimitados ou ORC.

- O PolyBase pode ser usado para acessar o armazenamento de blobs que também está sendo usado como armazenamento para um cluster HD Insight. Isso fornece a você acesso aos mesmos dados com ferramentas relacionais e não relacionais.

## Próximas etapas

Agora que você sabe um pouco sobre o SQL Data Warehouse, saiba mais sobre a [carga de trabalho do data warehouse], [como provisionar] um SQL Data Warehouse e [como carregar dados de exemplo]. Ou, dê uma olhada em alguns desses outros recursos do SQL Data Warehouse.

- [Blogs]
- [Solicitações de recursos]
- [Vídeos]
- [Blogs da equipe CAT]
- [Criar um tíquete de suporte]
- [Fórum do MSDN]
- [Fórum Stack Overflow]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar um tíquete de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[carga de trabalho do data warehouse]: ./sql-data-warehouse-overview-workload.md
[como carregar dados de exemplo]: ./sql-data-warehouse-get-started-load-sample-databases.md
[como provisionar]: ./sql-data-warehouse-get-started-provision.md
[Documentação de migração]: ./sql-data-warehouse-overview-migrate.md
[Parceiros de solução do SQL Data Warehouse]: ./sql-data-warehouse-integrate-solution-partners.md
[Visão geral das ferramentas integradas]: ./sql-data-warehouse-overview-integrate.

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da equipe CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0622_2016-->