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
   ms.date="02/25/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;"/>


# O que é o SQL Data Warehouse do Azure?

O SQL Data Warehouse do Azure é um banco de dados distribuído de nível corporativo com capacidade de processar volumes imensos de dados relacionais e não relacionais. É o primeiro data warehouse de nuvem do setor que combina recursos SQL comprovados com a capacidade de expansão, redução e pausa em questão de segundos. O SQL Data Warehouse é também profundamente arraigado no Azure, e pode ser facilmente implantado em questão de segundos. Além disso, o serviço é totalmente gerenciado e remove o incômodo de gastar tempo com correção, manutenção e backups de software. Os backups internos e automáticos do SQL Data Warehouse oferecem suporte a tolerância a falhas e restauração de autoatendimento.

Na criação do SQL Data Warehouse, nos concentramos em alguns atributos principais para garantir o aproveitamento total do Azure e criar um data warehouse que pudesse atender à carga de trabalho da qualquer empresa.

## Otimizado

### Arquitetura do Data Warehouse

Em seu núcleo, o SQL Data Warehouse usa uma arquitetura MPP (processamento paralelo maciço) da Microsoft, projetada originalmente para executar alguns dos maiores data warehouse corporativos locais. Essa arquitetura se beneficia dos aprimoramentos de desempenho de data warehouse internos e também permite que o SQL Data Warehouse expanda facilmente e paralelize a computação de consultas SQL complexas. Além disso, a arquitetura do SQL Data Warehouse é projetada para aproveitar sua presença no Azure. Combinando esses dois aspectos, a arquitetura se divide em quatro componentes principais:

![Arquitetura do SQL Data Warehouse][1]

- **Nó de controle:** você se conecta ao nó de controle usando o SQL Data Warehouse com quaisquer ferramentas de desenvolvimento, carregamento ou business intelligence. No SQL Data Warehouse, o nó de controle é um Banco de Dados SQL e ao se conectar ele se parece com um Banco de Dados SQL padrão. No entanto, em segundo plano, ele coordena toda a movimentação e computação de dados que ocorrem no sistema. Quando um comando é emitido para o nó de controle, ele o divide em um conjunto de consultas que serão passados para os nós de computação do serviço.

- **Nós de computação:** como o nó de controle, os nós de computação do SQL Data Warehouse são ativados usando Bancos de Dados SQL. O trabalho deles é servir como a potência de computação do serviço. Nos bastidores, sempre que os dados são carregados no SQL Data Warehouse, eles são distribuídos entre os nós do serviço. Em seguida, sempre que o nó de controle recebe um comando ,ele o divide em partes para cada nó de computação, e os nós de computação operam em seus dados correspondentes. Depois de concluir a computação, os nós de computação passam os resultados parciais para o nó de controle que agrega os resultados antes de retornar uma resposta.

- **Armazenamento:** todo o armazenamento para o SQL Data Warehouse é um Blobs de armazenamento do Azure padrão. Isso significa que, ao interagir com dados, os nós de computação são gravados e lidos diretamente nos/dos Blobs. A capacidade do Armazenamento do Azure de expandir de forma transparente e permitir quase sem limite o dimensionamento automático do armazenamento, e fazer isso separadamente da computação. O Armazenamento do Azure também permite a manutenção do armazenamento durante a expansão ou pausa, simplificar nosso processo de backup e restauração, e ter um armazenamento mais seguro e mais tolerante a falhas.

- **Serviços de movimentação de dados:** a parte final que mantém tudo junto no SQL Data Warehouse são os nossos Serviços de movimentação de dados. Os serviços de movimentação de dados permitem que o nó de controle se comunique e passe os dados a todos os nós de computação. Eles também permitem que os nós de computação passem dados entre si, o que oferece a eles acesso a dados em outros nós de computação e permite que eles obtenham os dados necessários para completarem junções e agregações.

### Otimizações de mecanismo

Essa abordagem MPP permite que o SQL Data Warehouse use uma abordagem do tipo dividir e conquistar, conforme descrito acima, ao solucionar problemas de dados grandes. Como os dados no SQL Data Warehouse são divididos e distribuídos entre os nós de computação do serviço, cada nó de computação é capaz de operar em sua parte dos dados em paralelo. Por fim, os resultados são passados ao nó de controle e agregados antes de serem passado aos usuários. Essa abordagem também é auxiliada por diversas otimizações de desempenho específicas de data warehouse:

- O SQL Data Warehouse usa um otimizador de consulta avançado e um conjunto de estatísticas complexas em todos os dados no serviço para criar seus planos de consulta. Usando informações sobre distribuição e tamanho dos dados, o serviço é capaz de otimizar consultas distribuídas com base na avaliação do custo de operações de consulta específicas.

- Além de criar planos de consulta ideais, o SQL Data Warehouse incorpora técnicas e algoritmos avançados que movem dados eficientemente entre os recursos de computação, conforme a necessidade, para executar a consulta. Essas operações estão integradas aos Serviços de Movimentação de Dados do data warehouse, e as otimizações ocorrem automaticamente.

- A inclusão de índices columnstore clusterizados para SQL Data Warehouse também é fundamental para alcançar o desempenho de consulta rápida. Ao usar o armazenamento baseado em coluna, o SQL Data Warehouse pode obter até 5 vezes mais ganhos de compactação em relação ao armazenamento tradicional orientado por linha e até 10 vezes mais ganhos de desempenho em consulta. As consultas de data warehouse funcionam bem em índices columnstore porque frequentemente examinam a tabela inteira ou toda a partição de uma tabela e minimizam o impacto da movimentação de dados para etapas de consulta.

## Escalonável

A arquitetura do SQL Data Warehouse apresenta armazenamento e computação separados, permitindo que cada um seja dimensionado independentemente. A estrutura de implantação simples e rápida do Banco de Dados SQL permite que computadores adicionais estejam disponíveis imediatamente. Complementar isso é o uso dos Blobs de Armazenamento do Azure. Os blobs não apenas oferecem armazenamento estável e replicado, mas também fornecem a infraestrutura para expansão sem esforço e de baixo custo. Usando essa combinação de armazenamento de escala em nuvem e de computação do Azure, o SQL Data Warehouse permite pagar pelo armazenamento de desempenho da consulta à medida que você precisar dela e quando precisar dela. Alterar a quantidade de computação é tão simples quanto mover um controle deslizante no Portal Clássico do Azure para a esquerda ou direita, mas pode também ser agendado ou adicionado a uma carga de trabalho com o T-SQL e PowerShell.

Junto com a capacidade de controlar totalmente o volume de computação, independentemente do armazenamento, o SQL Data Warehouse permite pausar totalmente seu data warehouse. Enquanto mantém o armazenamento em vigor, todas computação é liberada no pool principal do Azure, economizando dinheiro imediatamente. Quando necessário, basta retomar a computação e ter seu dados e computação disponíveis para sua carga de trabalho.

O uso de computação no SQL Data Warehouse é medido usando Unidades do SQL Data Warehouse (DWUs). DWUs são uma medida do poder subjacente que seu data warehouse tem, e são projetados para garantir que você tenha uma quantidade padrão de desempenho associada ao seu warehouse em qualquer momento. Especificamente, usamos DWUs para garantir que:

- Você seja capaz de dimensionar seu data warehouse com eficiência, sem se preocupar com o software ou hardware subjacente.

- Você possa entender o desempenho visto em um nível de DWU antes de alterar o tamanho do seu data warehouse.

- O hardware e software subjacentes da sua instância podem ser alterados ou movidos sem afetar o desempenho da carga de trabalho

- Podemos fazer ajustes à arquitetura subjacente do serviço sem afetar o desempenho de sua carga de trabalho.

- À medida que aprimoramos rapidamente o desempenho do SQL Data Warehouse do SQL, podemos garantir que isso será feito de uma forma escalonável e afetando uniformemente o sistema.

### Unidades de Data Warehouse

Especificamente, examinaremos as Unidades de Data Warehouse como uma medida de três métricas precisas que consideramos altamente correlacionadas ao desempenho da carga de trabalho do data warehouse. Nosso objetivo é que, para nossa disponibilidade geral, essas métricas de carga de trabalho principais aumentem linearmente com os DWUs que você escolheu para o data warehouse.

**Verificação/Agregação:** essa métrica de carga de trabalho usa um padrão de data warehouse de consulta que verifica um grande número de linhas e, em seguida, executa uma agregação complexa. Essa operação exige bastante E/S e CPU.

**Carga:** essa métrica mede a capacidade de incluir dados no serviço. As cargas são concluídas com PolyBase carregando um conjunto de dados representativo de um Blob de Armazenamento do Azure. Essa métrica é projetada para enfatizar os aspectos de Rede e de CPU do serviço.

**CRIAR TABELA COMO SELECIONADA (CTAS):** CTAS mede a capacidade de criar a cópia de uma tabela. Isso envolve a leitura de dados do armazenamento, distribuição em todos os nós do dispositivo e nova gravação no armazenamento. É uma operação com uso intenso de CPU e rede.

### Quando dimensionar

Em geral, queremos que as DWUs sejam simples. Quando você precisar de resultados mais rápidos, aumente suas DWUs e pague por mais desempenho. Quando precisar de menos potência de computação, diminua suas DWUs e pague somente pelo que precisa. Algumas vezes que você pode pensar em alterar o número de DWUs é:

- Quando não precisar executar consultas, talvez ao anoitecer ou nos fins de semana, pause os recursos de computação para cancelar todas as consultas em execução e remova todas as DWUs alocadas para seu data warehouse.

- Ao executar uma operação de transformação ou carregamento de dados pesados, convém escalar verticalmente para que os dados fiquem disponíveis mais rapidamente.

- Para entender qual é o valor ideal de DWU é, tente escalar verticalmente, para cima e para baixo, e executar algumas consultas após carregar os dados. Como o dimensionamento é rápido, você pode experimentar vários níveis diferentes de desempenho sem confirmar por mais de uma hora.

> [AZURE.NOTE] Observe que, devido à arquitetura ou ao Data Warehouse SQL talvez você não veja o desempenho esperado ao escalar volumes de dados mais baixos. Recomendamos começar com volumes de dados de 1 TB ou acima para obter resultados precisos do teste de desempenho.

## Integrado

O SQL Data Warehouse tem base no mecanismo de banco de dados relacional comprovada do SQL Server e inclui muitos dos recursos que você espera de um data warehouse corporativo. Se você já conhece o Transact-SQL, é fácil transferir seu conhecimento para o SQL Data Warehouse. Se você estiver avançado ou apenas iniciando, os exemplos na documentação ajudarão a começar. Em geral, você pode pensar na forma como construímos os elementos de linguagem do SQL Data Warehouse da seguinte maneira:

- O SQL Data Warehouse usa a sintaxe TSQL (Transact-SQL do SQL Server) para várias operações e oferece suporte a um amplo conjunto de construtores SQL tradicionais, como procedimentos armazenados, funções definidas pelo usuário, particionamento de tabelas, índices e agrupamentos.

- O SQL Data Warehouse também contém vários recursos de ponta do SQL Server, incluindo índices columnstore clusterizados, integração de PolyBase e Auditoria de dados (completo com Avaliação de Ameaças).

- Como o SQL Data Warehouse ainda está em desenvolvimento, determinados elementos da linguagem TSQL que são menos comuns para cargas de trabalho de data warehouse, ou que são novidades para o SQL Server, talvez não estejam disponíveis no momento. Consulte nossa documentação de migração para saber mais sobre isso.

Com a semelhança do Transact-SQL e recursos entre o SQL Server, SQL Data Warehouse, Banco de Dados SQL e Analytics Platform System, você pode desenvolver uma solução adequada às suas necessidades de dados. Você pode decidir onde manter os dados, com base no desempenho, na segurança e nos requisitos de escala e, em seguida, transferir dados, conforme a necessidade, entre sistemas diferentes.

Além de adotar a área de superfície do TSQL do SQL Server, o SQL Data Warehouse também se integra com muitas ferramentas conhecidas dos usuários do SQL Server. Especificamente, nos concentramos em algumas, integrando algumas categorias de ferramentas com o SQL Data Warehouse, incluindo:

**Ferramentas tradicionais do SQL Server:** integração total com o SQL Server Analysis Services, Integration Services e Reporting services está disponível com o SQL Data Warehouse.

**Ferramentas baseadas em nuvem:** o SQL Data Warehouse pode ser usado junto com várias ferramentas novas do Azure, e apresenta integração total com o Data Factory, o Stream Analytics, o Aprendizado de Máquina e o Power BI do Azure.

**Ferramentas de terceiros:** vários provedores de ferramenta de terceiros têm integração certificada de suas ferramentas com o SQL Data Warehouse. Consulte a lista completa.

## Híbrido

O uso do SQL Data Warehouse com PolyBase fornece a capacidade sem precedentes de mover dados entre seus ecossistemas, desbloqueando a capacidade de configuração avançada de cenários híbridos com fontes de dados não relacionais e locais.

O Polybase é fácil de usar e permite aproveitar dados de fontes diferentes usando os mesmos comandos T-SQL que você já conhece. O Polybase permite consultar dados não relacionais mantidos no armazenamento de blob do Azure, como se fosse uma tabela normal. Use o Polybase para consultar dados não relacionais ou importar dados não relacionais no SQL Data Warehouse.

- O PolyBase usa tabelas externas para acessar dados não relacionais. As definições de tabela são armazenadas no SQL Data Warehouse e podem ser acessadas por SQL e ferramentas de acesso a dados relacionais normais.

- O Polybase não depende de sua integração. Ele expõe os mesmos recursos e funcionalidade a todas as fontes as quais oferece suporte. Os dados lidos pelo Polybase podem estar em vários formatos, incluindo arquivos delimitados ou ORC.

- O PolyBase pode ser usado para acessar o armazenamento de blob, que também está sendo usado como armazenamento para um cluster HD Insight, fornecendo acesso de ponta aos mesmos dados com ferramentas relacionais e não relacionais.


## Próximas etapas

Agora que você sabe um pouco sobre o SQL Data Warehouse, saiba mais sobre a [carga de trabalho do data warehouse], [provisionar] e carregar [dados de exemplo] para começar.

>[AZURE.NOTE] Queremos melhorar este artigo. Se você optar por responder "não" à pergunta "Este artigo foi útil?", inclua uma breve sugestão sobre como melhorar o artigo ou o que está faltando. Obrigado!

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[carga de trabalho do data warehouse]: ./sql-data-warehouse-overview-workload.md
[dados de exemplo]: ./sql-data-warehouse-get-started-load-samples.md
[provisionar]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0302_2016-->