<properties
   pageTitle="Guias de Início Rápido da Solução de Banco de Dados SQL do Azure | Microsoft Azure"
   description="Saiba mais sobre as Soluções de Banco de Dados SQL do Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# Explorar os Guias de Início Rápido da solução de Banco de Dados SQL do Azure

Este artigo contém uma visão geral dos Guias de Início Rápido da solução de Banco de Dados SQL do Azure. Estes Inícios Rápidos estão localizados no repositório de exemplos do SQL Server do GitHub e demonstram o uso do Banco de Dados SQL em uma solução completa com base em cenários do mundo real. Para obter tutoriais passo a passo simples, que demonstram o uso de um recurso específico do Banco de Dados SQL, confira [Explorar Tutoriais do Banco de Dados SQL do Azure](sql-database-explore-tutorials.md).

## Experimente a Demonstração de WingTipTickets e o laboratório prático

Os arquivos de demonstração e o laboratório prático de [WingTipTickets de Banco de Dados do SQL Azure](https://github.com/microsoft/wingtiptickets) demonstram um Banco de Dados SQL do Azure e um aplicativo de exemplo baseado na Pesquisa do Azure usado para vender ingressos para concertos.


## Coletar e monitorar dados de uso de recursos em vários pools

[Início Rápido da Solução: a telemetria de pool elástico usando o PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) fornece uma solução para coletar e monitorar o uso de recursos do Banco de Dados SQL em vários pools em uma assinatura. Quando você tem um grande número de bancos de dados em uma assinatura, é complicado monitorar cada pool elástico separadamente.

Para resolver este problema, você pode combinar cmdlets do PowerShell do Banco de Dados SQL e consultas T-SQL para coletar dados de uso de recursos de vários pools e bancos de dados. Isso ajuda a monitorar e analisar o uso de recursos com mais eficiência.

Este Início Rápido fornece um conjunto de scripts do PowerShell e consultas do T-SQL juntamente com a documentação sobre a solução e como implementá-la.

## Introdução ao Banco de Dados Elástico em um cenário de SaaS

 [Início Rápido da Solução: o painel personalizado do pool elástico para SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) fornece uma solução para um cenário de SaaS (software como serviço) que utiliza o recurso Banco de Dados Elástico do Banco de Dados SQL para fornecer um back-end do banco de dados econômico e escalonável para um aplicativo de SaaS.

Nesta solução, você verá a implementação de um aplicativo Web. Este aplicativo Web permite que você visualize a carga que é criada em um Banco de Dados Elástico por um gerador de carga que usa um painel personalizado que complementa o Portal do Azure.

Este Início Rápido fornece um gerador de carga e um aplicativo Web de monitoramento junto com a documentação sobre o que o aplicativo faz e como usá-lo.

## Crie um Banco de Dados SQL do Azure usando o desenvolvimento Code First e o Entity Framework

O vídeo e o exemplo em [Code First para um novo banco de dados](https://msdn.microsoft.com/data/jj193542.aspx) fornece uma introdução ao desenvolvimento Code First que tem como destino um novo banco de dados. Este cenário tem como objetivo um banco de dados que não existe, mas que será criado pelo Code First. Como alternativa, o cenário cria um banco de dados vazio para o qual o Code First adiciona novas tabelas.

O Code First permite que você defina seu modelo usando classes de C# ou do Visual Basic .NET. Você pode executar configurações extras opcionais por meio de atributos em classes e propriedades ou usando uma API fluente.

## Integre ferramentas do Banco de Dados Elástico a um aplicativo do Entity Framework

O exemplo da [biblioteca de cliente do Banco de Dados Elástico com o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) mostra as alterações que você precisa fazer em um aplicativo do Entity Framework para integrá-lo às [Ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-get-started.md). O foco está na composição do [gerenciamento do mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md) e no [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) com a abordagem do Entity Framework Code First.

O [Code First para um novo banco de dados para o EF](http://msdn.microsoft.com/data/jj193542.aspx) serve como exemplo de execução para este exemplo. O código de exemplo que acompanha este documento faz parte do conjunto de ferramentas do Banco de Dados Elástico de exemplos nos exemplos de código do Visual Studio.

## Integre as ferramentas do Banco de Dados Elástico à segurança em nível de linha

Os [aplicativos multilocatários com as ferramentas do Banco de Dados Elástico e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md) mostram as alterações que você precisa fazer em um aplicativo do Entity Framework para integrar as [ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-get-started.md) com a [segurança em nível de linha](https://msdn.microsoft.com/library/dn765131). Este exemplo ilustra como usar essas tecnologias em conjunto para compilar um aplicativo com uma camada de dados altamente escalonável e compatível com fragmentos multilocatários.

Você pode fazer isso usando o ADO.NET SqlClient ou o Entity Framework. Este exemplo amplia a [biblioteca de cliente do Banco de Dados Elástico com o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) ao adicionar suporte para bancos de dados de fragmentos multilocatários. Ele compila um aplicativo de console simples para a criação de blogs e postagens, com quatro locatários e dois bancos de dados de fragmentos multilocatários.

## Crie pesquisas online com o aplicativo da Tailspin Surveys

Este [aplicativo de exemplo da Tailspin Surveys](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) é um aplicativo Web multilocatário, chamado Surveys, que permite aos usuários criar pesquisas online. O exemplo aborda algumas preocupações importantes sobre como gerenciar identidades de usuário em um aplicativo multilocatário, incluindo a inscrição, a autenticação, a autorização e as funções de aplicativo.

## Saiba mais sobre os novos recursos de segurança do Banco de Dados SQL com o aplicativo de demonstração da Contoso Clinic

Este [aplicativo de demonstração da Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) demonstra os novos recursos de segurança do Banco de Dados SQL.

## Próximas etapas

[Explorar Tutoriais do Banco de Dados SQL do Azure](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0907_2016-->