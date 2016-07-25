<properties
   pageTitle="Guias de Início Rápido da Solução de Banco de Dados SQL do Azure | Microsoft Azure"
   description="Saiba mais sobre as Soluções de Banco de Dados SQL do Azure"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="06/22/2016"
   ms.author="carlrab"/>

# Explorar os Guias de Início Rápido da solução de Banco de Dados SQL do Azure

Este artigo contém uma visão geral dos Guias de Início Rápido da solução de Banco de Dados SQL do Azure. Estes Guias de Início Rápido demonstram o uso do banco de dados SQL na solução completa com base em cenários do mundo real. Para obter tutoriais passo a passo simples que demonstram o uso de um recurso específico do Banco de Dados SQL do Azure, consulte [Explorar Tutoriais do Banco de Dados SQL do Azure](sql-database-explore-tutorials.md).

## Demonstração de WingTipTickets e laboratório prático

Os arquivos de demonstração e laboratório práticos de [WingTipTickets de Banco de Dados do SQL Azure](https://github.com/microsoft/wingtiptickets) incluem um laboratório prático que demonstra um Banco de Dados SQL e um aplicativo de exemplo baseado na Pesquisa do Azure, usado para vender ingressos para concertos

## Coletar e monitorar dados de uso de recursos em vários pools

Este Guia de Início Rápido da Solução fornece uma solução para coletar e monitorar o uso de recursos do Banco de Dados SQL do Azure em vários pools em uma assinatura. Quando você tiver um grande número de bancos de dados em uma assinatura, é complicado monitorar cada pool elástico separadamente. Para resolver disso, você pode combinar cmdlets do PowerShell do Banco de Dados SQL e consultas T-SQL para coletar dados de uso de recursos de vários pools e bancos de dados para monitoramento e análise de uso de recursos.

[Gerenciar Pools Elásticos Múltiplos no Banco de Dados SQL usando o PowerShell e o Power BI](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) no repositório de exemplos do SQL Server no GitHub fornece um conjunto de scripts do PowerShell e consultas T-SQL, em conjunto com a documentação sobre o que ele faz e como usá-lo.

## Introdução ao uso de Pools Elásticos em um cenário de SaaS

Este Guia de Início Rápido da Solução fornece uma solução para um cenário de SaaS (Software como solução) que utiliza Pools Elásticos para fornecer um back-end de banco de dados econômico e escalonável para um aplicativo SaaS. Nesta solução, você verá um passo a passo da implementação de um aplicativo Web que permite visualizar a carga criada em um Pool Elástico com um gerador de carga usando um painel personalizado que complementa o Portal do Azure.

[Painel personalizado do pool elástico para Saas](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) no repositório de exemplos do SQL Server no GitHub fornece um aplicativo Web de monitoramento e gerador de carga em conjunto com a documentação sobre o que ele faz e como usá-lo.

## Criando um Banco de Dados SQL usando o Entity Framework e desenvolvimento Code First

Este vídeo e exemplo fornece uma introdução ao desenvolvimento Code First visando a um novo banco de dados. O cenário inclui como meta um banco de dados que não existe e será criado pelo Code First, ou um banco de dados vazio ao qual o Code First adicionará novas tabelas. O Code First permite que você defina seu modelo usando classes C# ou VB.Net. Outras configurações podem ser opcionalmente definidas usando atributos em classes e propriedades ou usando uma API fluente. Confira [Code First para um novo banco de dados](https://msdn.microsoft.com/data/jj193542.aspx).

## Integrar ferramentas de Banco de Dados Elástico a um aplicativo Entity Framework

Este exemplo mostra as alterações em um aplicativo do Entity Framework necessárias para integrar com as [ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-get-started.md). O foco está na composição do [gerenciamento do mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md) e [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) com a abordagem do Entity Framework Code First. O exemplo [Code First – Novo banco de dados para o EF](http://msdn.microsoft.com/data/jj193542.aspx) serve como exemplo de execução para este exemplo. O código de exemplo que acompanha este documento faz parte do conjunto de ferramentas de banco de dados elástico de exemplos código do Visual Studio. Confira [Biblioteca cliente do Banco de Dados Elástico com Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md).

## Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha

Este exemplo mostra as alterações em um aplicativo do Entity Framework necessárias para integrar as [ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-get-started.md) com [segurança em nível de linha](https://msdn.microsoft.com/library/dn765131). Este exemplo ilustra como usar essas tecnologias em conjunto para criar um aplicativo com uma camada de dados altamente dimensionável e compatível com fragmentos multilocatários usando ADO.NET SqlClient e/ou Entity Framework. Este exemplo amplia a [biblioteca de cliente do Banco de Dados Elástico com o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) adicionando suporte para bancos de dados de fragmentos multilocatários. Ele cria um aplicativo de console simples para a criação de blogs e postagens, com quatro locatários e dois bancos de dados de fragmentos multilocatários. Confira [Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md).

## Aplicativo Tailspin Surveys

Este exemplo é um aplicativo Web multilocatário, chamado Surveys, que permite aos usuários criar pesquisas online. O exemplo demonstra algumas preocupações importantes com o gerenciamento de identidades de usuário em um aplicativo multilocatário, incluindo a inscrição, a autenticação, a autorização e as funções de aplicativo. Para executar este exemplo, confira [Como executar o aplicativo de exemplo Tailspin Surveys](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).

## Próximas etapas

[Explorar Tutoriais do Banco de Dados SQL do Azure](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0713_2016-->