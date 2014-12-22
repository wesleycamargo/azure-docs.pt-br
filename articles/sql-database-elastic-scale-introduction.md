<properties title="Azure SQL Database Elastic Scale" pageTitle="Escala Elástica do Banco de Dados SQL do Azure" description="Easily scale database resources in the cloud using Elastic Scale feature of Azure SQL Database." metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Escala Elástica do Banco de Dados SQL do Azure 
Bem-vindo à demonstração pública ﻿de Escala Elástica de Banco de Dados SQL do Azure! 

###Promessas e desafios
A Escala Elástica do Banco de Dados SQL do Azure cumpre a promessa da computação em nuvem e permite que praticamente ilimitado de capacidade, bem como elasticidade na plataforma de Banco de Dados SQL do Azure. Até agora, os provedores do serviço de nuvem foram capazes de fornecer a maioria dos aspectos em torno de limites capacidade de computação e armazenamento de blob. Elasticidade, no entanto, ainda continua sendo um desafio quando se trata de monitoração de estado de processamento de dados na nuvem, principalmente com o gerenciamento de banco de dados relacional. Vimos esses desafios surgem com mais destaque nos dois cenários abaixo: 

* Aumentando e diminuindo a capacidade para a parte do banco de dados relacional da carga de trabalho.
* Gerenciamento de pontos de acesso de utilização para cargas de trabalho com monitoração de estado do banco de dados e seus dados.

Tradicionalmente, estes cenários são endereçados comprando mais hardware que hospeda a camada de dados do aplicativo. No entanto, essa opção é limitada a nuvem em que todo o processamento ocorre em hardware de mercadoria predefinidos. Fragmentação ou a distribuição de dados e processamento em várias unidades de escala por motivos de capacidade, fornece uma alternativa atraente para os métodos tradicionais de ampliação tanto em termos de custo e a elasticidade. Ao longo dos anos, descobrimos que os clientes devem implementar seus próprios abordagens de expansão no Banco de Dados SQL do Azure a obterem sucesso com a fragmentação. Para alguns, isso significa gerenciar centenas ou milhares de bancos de dados SQL do Azure. Isso significava que muito do código na sua camada de dados que lidar com a complexidade de fragmentação em vez da lógica de negócios do aplicativo. 

Trabalhar diretamente com os clientes ao longo dos anos, vimos vários padrões de fragmentação emerge esses projetos. A Escala Elástica do Banco de Dados SQL do Azure fornece bibliotecas de cliente e ofertas de serviço relacionadas a esses padrões. A Escala Elástica torna mais fácil desenvolver, dimensionar e gerenciar as camadas de dados com monitoração de estado dos aplicativos do Azure.

Em seguida, você pode se concentrar na lógica de negócios do seu aplicativo em vez de infra-estrutura de construção de fragmentação.  


##Recursos 

Desenvolver, escalar gerenciar aplicativos de expansão usando fragmentação apresenta desafios para os dois o desenvolvedor como para o administrador. A Escala Elástico do Banco de Dados SQL do Azure facilita a vida de ambas essas funções. Os números no gráfico descrevem os principais recursos fornecidos com esta versão de demonstração pública. 
A parte inferior mostra a camada de dados do aplicativo e a distribuição de seus dados em vários bancos de dados, chamados fragmentos. Suponha que vários bancos de dados estão armazenando os dados de vários fragmentos. 

Para obter definições dos termos usados aqui, consulte o [Glossário de escala elástica](./sql-database-elastic-scale-glossary.md).

###Escala Elástico com fragmentação 

![][1]

A figura mostra o desenvolvedor e o administrador à esquerda e à direita. Os clientes podem esperar obter a funcionalidade completa do T-SQL durante o envio de operações de fragmento local em vez de operações entre fragmentos que têm suas próprias semânticas. 
A versão de visualização pública para a Escala Elástica do Banco de Dados SQL do Azure facilita a desenvolver aplicativos para Banco de Dados SQL do Azure fragmentados por meio dos seguintes recursos específicos: 

* **Gerenciamento de mapa de fragmentos**: Gerenciamento de mapa do fragmento (1) é a capacidade de um aplicativo para gerenciar vários metadados sobre seus fragmentos. Gerenciamento de mapa do fragmento é um recurso da biblioteca de cliente a ﻿Escala Elástica. Os desenvolvedores podem usar essa funcionalidade para registrar fragmentos, descrevem os mapeamentos de chaves de fragmentação individuais ou intervalos de chaves para fragmentos e manter esses metadados conforme se desenvolve o layout dos fragmentos na camada de dados para refletir as alterações de capacidade. Gerenciamento de mapa do fragmento constitui uma grande parte do código clichê que os clientes precisavam escrever em seus aplicativos quando eles implementavam fragmentação próprios. Para obter detalhes, consulte [Gerenciamento de mapa do fragmento](./sql-database-elastic-scale-shard-map-management.md)
 
* **Roteamento dependente de dados**: Imagine que uma solicitação de entrada para o aplicativo. Com base no valor da chave de fragmentação da solicitação, o aplicativo precisa determinar o fragmento correto que mantém os dados para esse valor de chave de fragmentação e, em seguida, abrir uma conexão com esse fragmento (2) para processar a solicitação. Roteamento dependente de dados fornece a capacidade de abrir conexões com uma única chamada simples para o mapa do fragmento do aplicativo. Roteamento dependente de dados era outra área do código de infraestrutura que agora é coberto por funcionalidade na biblioteca de cliente da Escala Elástica. Para obter mais detalhes, consulte [Roteamento dependente de dados](./sql-database-elastic-scale-data-dependent-routing.md)

* **Consultas de vários fragmentos (MSQ)**: Consultar vários fragmentos funciona quando uma solicitação envolve fragmentos várias (ou todos). Uma consulta de vários fragmentos (3) executa o mesmo código T-SQL em todos os fragmentos ou um conjunto de fragmentos. Os resultados de fragmentos de participantes são mesclados em um resultado geral definido usando a semântica UNION ALL. A funcionalidade é exposta por meio do cliente biblioteca trata muitas tarefas, incluindo: gerenciamento de conexões, gerenciamento de threads, tratamento de falhas e processar os resultados intermediários. MSQ pode consultar até centenas de fragmentos. Para obter mais detalhes, consulte [Consulta de vários fragmentos](./sql-database-elastic-scale-multishard-querying.md).

* **Elasticidade de fragmento**: Essa funcionalidade permite aos administradores automatizar a escala vertical (discagem para cima e para a edição de um único fragmento) e a horizontal (fragmentos de adição ou remoção de um mapa de fragmento) do ambiente fragmentado por meio de scripts do PowerShell e por meio do serviço de automação do Azure. Para obter detalhes, consulte [Elasticidade de fragmento](./sql-database-elastic-scale-elasticity.md).

* **Serviço de Divisão-Mesclagem**: Quando as necessidades de capacidade flutuam em tandem com impulso nos negócios, os aplicativos precisam redistribuir dados de maneira flexível em vários bancos de dados (4). A Escala Elástica fornece uma experiência de serviço hospedado no cliente para crescimento e reduzindo a capacidade da camada de dados e gerenciar pontos de acesso para aplicativos fragmentados em situações que envolvem a movimentação de dados também. Ela desenvolve uma funcionalidade subjacente para mover shardlets sob demanda entre fragmentos diferentes e integra-se ao gerenciamento de mapa do fragmento para manter mapeamentos consistentes e conexões de roteamento dependente de dados precisos. Para obter detalhes, consulte [Dividindo e mesclando com a Escala Elástica](./sql-database-elastic-scale-overview-split-and-merge.md)

##Padrões comuns de fragmentação

A **Fragmentação** é uma técnica para distribuir grandes quantidades de dados estruturado de forma idêntica em vários bancos de dados independentes. É especialmente popular com desenvolvedores de nuvem que estiverem criando Software como as ofertas de serviço (SAAS) para clientes finais ou empresas. Esses clientes finais são referidos como "Locatários". Fragmentação pode ser necessária por vários motivos: 

* A quantidade total de dados é muito grande para caber dentro das restrições de um único banco de dados 
* A taxa de transferência de transação da carga de trabalho geral excede as capacidades de um único banco de dados 
* Inquilinos podem exigir isolamento físico entre si, para que bancos de dados separados são necessários para cada locatário 
* Seções diferentes de um banco de dados talvez precise residir em regiões diferentes para questões geopolíticas, de desempenho ou de conformidade. 

Em outros cenários, como inclusão de dados de dispositivos distribuídos, a fragmentação pode ser usada para preencher um conjunto de bancos de dados que são organizados temporariamente. Por exemplo, um banco de dados pode ser dedicado a cada dia ou semana. Nesse caso, a chave de fragmentação pode ser um número inteiro que representa a data (presente em todas as linhas das tabelas fragmentadas) e as consultas que recuperam informações para um intervalo de datas devem ser roteadas pelo aplicativo para o subconjunto de bancos de dados que abrangem o intervalo em questão.
 
A fragmentação funciona melhor quando todas as transações em um aplicativo podem ser restrita a um único valor de uma chave de fragmentação. Isso garante que todas as transações serão locais para um banco de dados específico. 

Alguns aplicativos usam a abordagem mais simples de criar um banco de dados separado para cada locatário. Este é o **padrão de fragmentação de locatário único** que fornece isolamento, capacidade de backup/restauração e escala de recurso na granularidade do inquilino. Com a fragmentação de locatário único, cada banco de dados está associado a um valor de ID específico do locatário (ou o valor de chave de cliente), mas essa chave não deve sempre estar presente nos próprios dados. É responsabilidade do aplicativo para rotear cada solicitação para o banco de dados apropriado. 

![][2]

Outros cenários de vários locatários pack juntos em bancos de dados, em vez de isolá-los em bancos de dados separados. Esse é um **padrão típico multilocatário fragmentação** - e pode ser orientada por considerações de custo, eficiência ou o fato de que um aplicativo gerencia grandes números de locatários muito pequenos. Na fragmentação multilocatária, as linhas nas tabelas de banco de dados foram projetadas para executar uma chave que identifica a ID do locatário ou a chave de fragmentação. Novamente, a camada de aplicativo é responsável pelo roteamento de solicitação de um locatário no banco de dados apropriado. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-intro/overview.png
[2]:./media/sql-database-elastic-scale-intro/tenancy.png
