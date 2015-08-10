<properties 
	pageTitle="Comece com ferramentas de banco de dados elástico" 
	description="Explicação básica do recurso das ferramentas de banco de dados elástico do Banco de Dados SQL do Azure, incluindo um aplicativo de amostra fácil de executar." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015" 
	ms.author="sidneyh@microsoft.com"/>

#Introdução às ferramentas do Banco de Dados Elástico

O aumento e a redução da capacidade sob demanda é uma das principais promessas da computação em nuvem. Para os aplicativos de banco de dados, uma técnica importante para a criação dessas tais soluções escalonáveis é o padrão conhecido como fragmentação – em que os dados são particionados fisicamente em um número de bancos de dados estruturados de forma idêntica. Mas, tradicionalmente, a criação e o gerenciamento de aplicativos que usam a fragmentação exigiu a codificação significativa fora da lógica comercial do aplicativo.

As ferramentas de banco de dados elásticas simplificam a criação e o gerenciamento dos aplicativos que utilizam a fragmentação do banco de dados no SQL Azure. As ferramentas incluem a biblioteca de cliente da ferramenta de banco de dados elástico e a ferramenta de divisão e mesclagem. Juntos, eles implementam os aspectos de infraestrutura de fragmentação e permitem que você se concentre em sua lógica de negócios de seu aplicativo.

Este documento apresenta a experiência do desenvolvedor usando a biblioteca de cliente de banco de dados elástico.

Para obter mais informações sobre como o banco de dados elástico funciona, consulte [Visão geral das ferramentas de banco de dados elástico](sql-database-elastic-scale-introduction.md).

Para obter uma lista de todos os tópicos sobre ferramentas de banco de dados elástico, consulte o [mapa de aprendizado](sql-database-elastic-scale-documentation-map.md)

## O aplicativo de exemplo do Banco de Dados Elástico

A amostra cria um aplicativo fragmentado simples e explora os principais recursos das ferramentas de banco de dados elástico. Para baixar e executar o aplicativo, siga as etapas mostradas abaixo ou no vídeo [Escala Elástica – Introdução](http://channel9.msdn.com/Blogs/Windows-Azure/Elastic-Scale-with-Azure-SQL-Database-Getting-Started).

## Pré-requisitos
Para executar o aplicativo de amostra, você deve usar o Visual Studio e deve ter acesso a um Banco de Dados SQL do Azure em execução no Azure. Se você ainda não tiver uma assinatura do Azure, inscreva-se para uma [assinatura de avaliação](http://azure.microsoft.com/pricing/free-trial/).
### Visual Studio e Nuget

1. O Visual Studio 2012 ou posterior com C# é necessário. Baixe a versão gratuita em [Downloads do Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. Nuget 2.7 ou superior. Para obter a versão mais recente, consulte [Instalando o NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)
### Criar um banco de dados SQL do Azure

* Consulte [Introdução ao Banco de Dados SQL do Microsoft Azure](sql-database-get-started.md).

## Baixar e executar o aplicativo de amostra

O aplicativo de exemplo **Banco de Dados Elástico com SQL do Azure – Introdução** ilustra os aspectos mais importantes da experiência de desenvolvimento de aplicativos fragmentados usando as ferramentas de banco de dados elástico do SQL do Azure. Ele se concentra nos principais casos de uso para [gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md), [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) e [consulta de vários fragmentos](sql-database-elastic-scale-multishard-querying.md). Para baixar e executar a amostra, siga estas etapas:

1. Abra o Visual Studio e selecione **Arquivo -> Novo -> Projeto**.
2. Na caixa de diálogo, clique em **Online**.

    ![Novo Projeto>Online][2]
3. Clique em **Visual C#** em **Amostras**.

    ![Clique em Visual C#][3]
4. Na caixa de pesquisa, digite **banco de dados elástico** para procurar a amostra. O título **Banco de dados Elástico com Banco de Dados SQL do Azure - Introdução** aparece.

    ![Caixa de pesquisa][1]
 
5. Selecione a amostra, escolha um nome e um local para o novo projeto e pressione **OK** para criar o projeto.
6. Abra o arquivo **app.config** na solução para o projeto de amostra e siga as instruções no arquivo para adicionar o nome do seu servidor de banco de dados SQL do Azure e suas informações de logon (nome de usuário e senha).
7. Compile e execute o aplicativo. Quando solicitado, permita que o Visual Studio restaure os pacotes NuGet da solução. Com isso, ocorrerá o download da versão mais recente das bibliotecas de cliente do banco de dados elástico do NuGet.
8. Trabalhe com as diferentes opções para saber mais sobre os recursos da biblioteca de clientes. Observe as etapas que o aplicativo realiza na saída do console e fique à vontade para explorar o código nos bastidores.

    ![progresso][4]

Parabéns – você criou e executou com sucesso seu primeiro aplicativo fragmentado usando as ferramentas de banco de dados elástico no banco de dados do SQL Azure. Analise rapidamente os fragmentos que a amostra criados ao se conectar com o Visual Studio ou o SQL Server Management Studio ao seu Servidor de Banco de Dados do Azure. Você observará novos bancos de dados de fragmentos de amostra e um banco de dados de gerenciador de mapa de fragmentos que a amostra criou.

**Observação** Se você não tiver o SQL Server Management Studio, consulte [Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio](sql-database-manage-azure-ssms.md), que inclui instruções para obter a ferramenta.

### Principais partes do exemplo de código

1. **Gerenciamento de fragmentos e mapas de fragmentos**: o código ilustra como trabalhar com fragmentos, intervalos e mapeamentos no arquivo **ShardMapManagerSample.cs**. Você pode encontrar mais informações sobre esse tópico aqui: [Shard Map Management](http://go.microsoft.com/?linkid=9862595).  
2. **Roteamento dependente de dados**: o roteamento de transações para o fragmento correto é mostrado em **DataDependentRoutingSample.cs**. Para obter mais detalhes, consulte [Roteamento dependente de dados](http://go.microsoft.com/?linkid=9862596). 
3. **Consulta em vários fragmentos**: a consulta entre os fragmentos é ilustrada no arquivo **MultiShardQuerySample.cs**. Para obter mais detalhes, consulte [Consulta de vários fragmentos](http://go.microsoft.com/?linkid=9862597).
4. **Consulta em vários fragmentos**: a adição iterativa de novos fragmentos vazios é realizada pelo código no arquivo **AddNewShardsSample.cs**. Os detalhes deste tópico são abordados aqui: [Gerenciamento de mapa de fragmentos](http://go.microsoft.com/?linkid=9862595).

### Outras operações da escala elástica

1. **Divisão de um fragmento existente:**: A capacidade de dividir fragmentos é fornecida por meio da **Ferramenta da Divisão de Mesclagem**. Você pode encontrar mais informações sobre essa ferramenta aqui: [Visão geral da ferramenta da Divisão de Mesclagem](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Mesclagem de fragmentos existentes**: a mesclagem de fragmentos também é realizada usando-se a **ferramenta de mesclagem de divisão**. Para obter mais informações, consulte: [Visão geral da ferramenta de divisão de mesclagem](sql-database-elastic-scale-overview-split-and-merge).   


## Custo

As ferramentas de banco de dados elástico são gratuitas. As ferramentas de banco de dados elástico não impõem cobranças adicionais sobre os custos para o uso do Azure.

Por exemplo, o aplicativo de exemplo cria novos bancos de dados. O custo depende da edição de banco de dados do BD SQL do Azure escolhida e do uso do Azure do seu aplicativo.

Para obter informações sobre os preços, consulte [Detalhes de preços do Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).

## Próximas etapas
Para saber mais, confira o Mapa de tópicos sobre ferramentas de banco de dados elásticos.

* [Mapa de documentação de ferramentas elásticas de banco de dados](sql-database-elastic-scale-documentation-map.md) 
-    Amostras de código: 
    -    [Banco de dados elásticos com o SQL Azure - Introdução](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Banco de dados elásticos com o SQL Azure - integração com o Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Elasticidade do fragmento no Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    Blog: [Anúncio da Escala Elástica](http://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    Canal 9: [Visão geral da Escala Elástica](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    Fórum de discussão: [Fórum do banco de dados do SQL Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 

<!---HONumber=July15_HO5-->