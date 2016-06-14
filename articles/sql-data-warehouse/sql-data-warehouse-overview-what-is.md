<properties
   pageTitle="O que é o SQL Data Warehouse do Azure | Microsoft Azure"
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

- Combina nosso banco de dados relacional comprovado do SQL Server com nossos recursos de expansão na nuvem do Azure. Você pode aumentar, diminuir, pausar ou retomar a computação em segundos. Isso permite que você economize custos expandindo a CPU quando for necessário e cortando o uso durante os horários de pico.
- Aproveita a nossa plataforma do Azure. É fácil de implantar, apresenta uma manutenção tranquila e é totalmente tolerante a falhas graças ao backups automáticos. 
- Complementa o ecossistema do SQL Server. Você pode desenvolver com o T-SQL do SQL Server e ferramentas conhecidas.

Continue lendo para saber mais sobre os principais recursos do SQL Data Warehouse.

## Arquitetura MPP (processamento paralelo maciço)

O SQL Data Warehouse usa uma arquitetura MPP (processamento paralelo maciço) da Microsoft, projetada para executar alguns dos maiores data warehouse corporativos locais do mundo.

Atualmente, nossa arquitetura MPP distribui os dados entre 60 unidades de armazenamento e processamento do tipo shared-nothing. Os dados são armazenados em um armazenamento com redundância local Premium e são vinculados aos nós de computação para execução da consulta. Com essa arquitetura, o SQL Data Warehouse usa uma abordagem de dividir para conquistar a fim de executar consultas complexas de T-SQL. Durante o processamento, o nó de controle simplifica cada consulta e, depois, passa o trabalho para os nós de computação, assim, cada um deles "conquista" sua parte dos dados em paralelo.

Ao combinar nossa arquitetura MPP e recursos de armazenamento do Azure, o SQL Data Warehouse pode:

- Aumentar ou reduzir o armazenamento de forma independente à computação.
- Aumentar ou reduzir a computação sem mover dados. 
- Pausar a capacidade de computação enquanto mantém os dados intactos.
- Retomar a capacidade de computação quase imediatamente.

Veja abaixo uma descrição detalhada da arquitetura.

![Arquitetura do SQL Data Warehouse][1]


- **Nó de controle:** o nó de controle gerencia e simplifica as consultas. É o front-end que interage com todos os aplicativos e conexões. No SQL Data Warehouse, o nó Controle é alimentado pelo Banco de Dados SQL, e a conexão com os dois é praticamente igual. Nos bastidores, o nó de controle coordena toda a movimentação e computação de dados necessárias para executar consultas em paralelo em seus dados distribuídos. Quando você envia uma consulta TSQL ao SQL Data Warehouse, o nó de controle a transforma em consultas separadas que serão executadas paralelamente em cada nó de computação.

- **Nós de computação:** os nós de computação servem como a força por trás do SQL Data Warehouse. Eles são Bancos de Dados SQL que armazenam seus dados e processam sua consulta. Quando você adiciona dados, o SQL Data Warehouse distribui as linhas entre seus nós de computação. Os nós de computação são os trabalhadores que executam as consultas paralelas em seus dados. Após o processamento, eles passam os resultados novamente para o nó de controle. Para concluir a consulta, o nó de controle agrega os resultados e retorna o resultado final.

- **Armazenamento:** os dados são armazenados em Blobs de Armazenamento do Azure. Quando os nós de computação interagem com seus dados, eles gravam e leem diretamente no e do armazenamento de blobs. Como o armazenamento do Azure expande de modo transparente e ilimitado, o SQL Data Warehouse pode fazer o mesmo. Como a computação e o armazenamento são independentes, o SQL Data Warehouse pode expandir automaticamente o armazenamento, separadamente da expansão da computação, e vice-versa. O Armazenamento do Azure também é totalmente tolerante a falhas, além de simplificar o processo de backup e restauração.

- **Serviço de Movimentação de Dados:** o DMS (Serviço de Movimentação de Dados) é nossa tecnologia de movimentação de dados entre os nós. O DMS oferece aos nós de computação acesso aos dados que eles precisam para junções e agregações. DMS não é um serviço do Azure. É um serviço do Windows executado junto com o Banco de Dados SQL em todos os nós. Como o DMS é executado em segundo plano, você não irá interagir com ele diretamente. No entanto, ao examinar os planos de consulta, você perceberá que eles incluem algumas operações DMS, uma vez que a movimentação dos dados é necessária de alguma forma para executar cada consulta em paralelo.


## Desempenho de consulta otimizado

A abordagem MPP também é auxiliada por diversas otimizações de desempenho específicas ao data warehouse, incluindo:

- Um otimizador de consulta distribuída e um conjunto de estatísticas complexas em todos os dados. Usando informações sobre distribuição e tamanho dos dados, o serviço é capaz de otimizar consultas avaliando o custo de operações de consulta distribuída específicas.

- Técnicas e algoritmos avançados e integrados ao processo de movimentação de dados a fim de movimentar os dados de forma eficiente que movem dados eficientemente entre os recursos de computação, conforme a necessidade de execução da consulta. Essas operações de movimentação de dados são integradas e todas as otimizações no Serviço de Movimentação de Dados ocorrem automaticamente.

- Índice columnstore clusterizado por padrão. Ao usar o armazenamento baseado em coluna, o SQL Data Warehouse obtém até cinco vezes mais ganhos de compactação em relação ao armazenamento tradicional orientado por linha e até 10 vezes mais ganhos de desempenho em consulta. Consultas de análise que precisam examinar uma grande quantidade de linhas funcionam muito bem em índices columnstore.

## Escalonável

A arquitetura do SQL Data Warehouse apresenta armazenamento e computação separados, permitindo que cada um seja dimensionado independentemente. A estrutura de implantação simples e rápida do Banco de Dados SQL permite que computadores adicionais estejam disponíveis imediatamente. Complementar isso é o uso dos Blobs de Armazenamento do Azure. O uso dos blobs não apenas oferece armazenamento estável e replicado, mas também fornece a infraestrutura para expansão sem esforço e de baixo custo. Usando essa combinação de armazenamento de escala em nuvem e de computação do Azure, o SQL Data Warehouse permite pagar pelo armazenamento de desempenho da consulta à medida que você precisar dela e quando precisar dela. Alterar a quantidade de computação é tão simples quanto mover um controle deslizante no Portal do Azure para a esquerda ou direita, mas também pode ser agendado com o T-SQL e o PowerShell.

Junto com a capacidade de controlar totalmente o volume de computação, independentemente do armazenamento, o SQL Data Warehouse permite pausar totalmente seu data warehouse. Enquanto mantém o armazenamento em vigor, todas computação é liberada no pool principal do Azure, economizando dinheiro imediatamente. Quando necessário, basta retomar a computação e ter seu dados e computação disponíveis para sua carga de trabalho.

## Unidades de Data Warehouse

O uso de computação no SQL Data Warehouse é medido usando Unidades do SQL Data Warehouse (DWUs). DWUs são uma medida do poder subjacente que seu data warehouse tem, e são projetados para garantir que você tenha uma quantidade padrão de desempenho associada ao seu warehouse em qualquer momento. Especificamente, usamos DWUs para garantir que:

- Você seja capaz de dimensionar seu data warehouse com facilidade, sem se preocupar com o software ou hardware subjacente.

- Você possa prever o aprimoramento do desempenho para um nível de DWU antes de alterar o tamanho de seu data warehouse.

- O hardware e software subjacentes da sua instância podem ser alterados ou movidos sem afetar o desempenho da carga de trabalho

- Podemos fazer ajustes à arquitetura subjacente do serviço sem afetar o desempenho de sua carga de trabalho.

- À medida que aprimoramos rapidamente o desempenho do SQL Data Warehouse do SQL, podemos garantir que isso será feito de uma forma escalonável e afetando uniformemente o sistema.

Especificamente, examinaremos as Unidades de Data Warehouse como uma medida de três métricas precisas que consideramos altamente correlacionadas ao desempenho da carga de trabalho do data warehouse. Nosso objetivo é que, para nossa disponibilidade geral, essas métricas de carga de trabalho principais aumentem linearmente com os DWUs que você escolheu para o data warehouse.

**Verificação/Agregação:** essa métrica de carga de trabalho usa um padrão de data warehouse de consulta que verifica um grande número de linhas e, em seguida, executa uma agregação complexa. Essa operação exige bastante E/S e CPU.

**Carga:** essa métrica mede a capacidade de incluir dados no serviço. As cargas são concluídas com PolyBase carregando um conjunto de dados representativo de um Blob de Armazenamento do Azure. Essa métrica é projetada para enfatizar os aspectos de Rede e de CPU do serviço.

**CRIAR TABELA COMO SELECIONADA (CTAS):** CTAS mede a capacidade de criar a cópia de uma tabela. Isso envolve a leitura de dados do armazenamento, distribuição em todos os nós do dispositivo e nova gravação no armazenamento. É uma operação com uso intenso de CPU e rede.

## Pausar e escalar sob demanda

Quando você precisar de resultados mais rápidos, aumente suas DWUs e pague por mais desempenho. Quando precisar de menos potência de computação, diminua suas DWUs e pague somente pelo que precisa. Talvez você pense em trocar suas DWUs nestes cenários:

- Quando você não precisar executar consultas, talvez à noite ou nos finais de semana, desative suas consultas e pause seus recursos de computação a fim de evitar pagar pelas DWUs quando elas não forem necessárias.

- Quando o sistema tiver baixa demanda, considere a redução da DWU para um tamanho pequeno a fim de permitir que os usuários acessem os dados, mas como uma economia considerável.

- Ao executar uma operação de transformação ou carregamento de dados pesados, convém escalar verticalmente para que os dados fiquem disponíveis mais rapidamente.

- Para entender qual é o valor ideal de DWU é, tente escalar verticalmente, para cima e para baixo, e executar algumas consultas após carregar os dados. Como o dimensionamento é rápido, você pode experimentar vários níveis diferentes de desempenho sem confirmar por mais de uma hora.

> [AZURE.NOTE] Observe que, devido à arquitetura do SQL Data Warehouse, talvez você não veja o desempenho esperado com volumes de dados mais baixos. Recomendamos começar com volumes de dados de 1 TB ou acima para obter uma verdadeira indicação dos benefícios no desempenho.

## Desenvolvido a partir do SQL Server

O SQL Data Warehouse tem base no mecanismo de banco de dados relacional comprovada do SQL Server e inclui muitos dos recursos que você espera de um data warehouse corporativo. Se você já conhece o Transact-SQL, é fácil transferir seu conhecimento para o SQL Data Warehouse. Se você estiver avançado ou apenas iniciando, os exemplos na documentação ajudarão a começar. Em geral, você pode pensar na forma como construímos os elementos de linguagem do SQL Data Warehouse da seguinte maneira:

- O SQL Data Warehouse usa a sintaxe TSQL (Transact-SQL do SQL Server) para várias operações e oferece suporte a um amplo conjunto de construtores SQL tradicionais, como procedimentos armazenados, funções definidas pelo usuário, particionamento de tabelas, índices e agrupamentos.

- O SQL Data Warehouse também contém vários recursos de ponta do SQL Server, incluindo índices columnstore clusterizados, integração de PolyBase e Auditoria de dados (completo com Avaliação de Ameaças).

- Como o SQL Data Warehouse ainda está em desenvolvimento, determinados elementos da linguagem TSQL que são menos comuns para cargas de trabalho de data warehouse, ou que são novidades para o SQL Server, talvez não estejam disponíveis no momento. Consulte nossa [documentação de migração][] para saber mais sobre isso.

Com a semelhança do Transact-SQL e recursos entre o SQL Server, SQL Data Warehouse, Banco de Dados SQL e Analytics Platform System, você pode desenvolver uma solução adequada às suas necessidades de dados. Você pode decidir onde manter os dados, com base no desempenho, na segurança e nos requisitos de escala e, em seguida, transferir dados, conforme a necessidade, entre sistemas diferentes.

## Integrado com as ferramentas da Microsoft

Além de adotar a área de superfície do TSQL do SQL Server, o SQL Data Warehouse também se integra com muitas ferramentas conhecidas dos usuários do SQL Server. Especificamente, nos concentramos em algumas, integrando algumas categorias de ferramentas com o SQL Data Warehouse, incluindo:

**Ferramentas tradicionais do SQL Server:** o SQL Data Warehouse é totalmente integrado ao SQL Server Analysis Services, aos serviços de integração e de relatório.

**Ferramentas baseadas em nuvem:** o SQL Data Warehouse pode ser usado junto com várias ferramentas novas do Azure, e apresenta integração total com o Data Factory, o Stream Analytics, o Aprendizado de Máquina e o Power BI do Azure. Confira [Visão geral das ferramentas integradas][] para obter uma lista de ferramentas do Azure com as quais temos integração.

**Ferramentas de terceiros:** vários provedores de ferramenta de terceiros têm integração certificada de suas ferramentas com o SQL Data Warehouse. Confira [Parceiros de solução do SQL Data Warehouse][] para obter uma lista completa dos parceiros.

## Cenários híbridos

O uso do SQL Data Warehouse com PolyBase fornece a capacidade sem precedentes de mover dados entre seus ecossistemas, desbloqueando a capacidade de configuração avançada de cenários híbridos com fontes de dados não relacionais e locais.

O Polybase é fácil de usar e permite aproveitar dados de fontes diferentes usando os mesmos comandos T-SQL que você já conhece. O Polybase permite consultar dados não relacionais mantidos no armazenamento de blob do Azure, como se fosse uma tabela normal. Use o Polybase para consultar dados não relacionais ou importar dados não relacionais no SQL Data Warehouse.

- O PolyBase usa tabelas externas para acessar dados não relacionais. As definições de tabela são armazenadas no SQL Data Warehouse e podem ser acessadas por SQL e ferramentas de acesso a dados relacionais normais.

- O Polybase não depende de sua integração. Ele expõe os mesmos recursos e funcionalidade a todas as fontes as quais oferece suporte. Os dados lidos pelo Polybase podem estar em vários formatos, incluindo arquivos delimitados ou ORC.

- O PolyBase pode ser usado para acessar o armazenamento de blob, que também está sendo usado como armazenamento para um cluster HD Insight, fornecendo acesso de ponta aos mesmos dados com ferramentas relacionais e não relacionais.

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
[documentação de migração]: ./sql-data-warehouse-overview-migrate.md
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

<!---HONumber=AcomDC_0608_2016-->