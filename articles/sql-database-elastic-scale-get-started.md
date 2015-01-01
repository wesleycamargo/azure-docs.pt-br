<properties title="Get Started with  Azure SQL Database Elastic Scale" pageTitle="Introdução à Escala Elástica do Banco de dados SQL do Azure" description="Explicação básica do recurso de Escala Elástica do Banco de dados SQL do Azure, incluindo um exemplo de aplicativo fácil de executar." metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Introdução à visualização de Escala Elástica do Banco de Dados SQL do Azure

O aumento e a redução da capacidade sob demanda é uma das principais promessas da computação em nuvem. Historicamente, o cumprimento dessa promessa tem sido tedioso e complexo para a camada de banco de dados dos aplicativos em nuvem. Ao longo dos últimos anos, o setor convergiu em padrões de design bem estabelecidos conhecidos como fragmentação. Embora o padrão de fragmentação geral lide com o desafio, a criação e o gerenciamento de aplicativos usando a fragmentação exigem investimentos em infraestrutura significativos independentes da lógica de negócios do aplicativo. 

A Escala Elástica do Banco de Dados SQL do Azure (em visualização) permite que a camada de dados de um aplicativo escale e reduza horizontalmente por meio de práticas de fragmentação padrão do setor, além de simplificar significativamente o desenvolvimento e o gerenciamento de seus aplicativos fragmentados em nuvem. A Escala Elástica oferece funcionalidades de gerenciamento e de desenvolvedor que são fornecidas por meio de um conjunto de bibliotecas .Net e por modelos de serviço do Azure que você pode hospedar em sua assinatura do Azure para gerenciar seus aplicativos altamente escalonáveis. A Escala Elástica do BD do Azure implementa os aspectos de infraestrutura da fragmentação e, com isso, permite que você se concentre na lógica de negócios de seu aplicativo. 

Este documento apresenta a experiência do desenvolvedor da Escala Elástica do BD SQL do Azure. 

Para mais informações sobre como a Escala Elástica funciona, consulte a [Visão geral da Escala Elástica](http://go.microsoft.com/?linkid=9862592).

Para obter uma lista de todos os tópicos sobre a Escala Elástica, consulte [Mapa da documentação da Escala Elástica](./sql-database-elastic-scale-documentation-map.md)

## O aplicativo de exemplo da Escala Elástica

A amostra cria um aplicativo fragmentado simples e explora os principais recursos da Escala Elástica. Para baixar e executar o aplicativo, siga as etapas mostradas abaixo ou no vídeo [Escala Elástica - Introdução](http://go.microsoft.com/?linkid=9862983). 

###Pré-requisitos
Para executar o aplicativo de amostra, você deve usar o Visual Studio e deve ter acesso a um Banco de Dados SQL do Azure em execução no Azure. Se você ainda não tiver uma assinatura do Azure, inscreva-se para uma [assinatura de avaliação](http://azure.microsoft.com/pt-br/pricing/free-trial/).
####Visual Studio e Nuget

1. O Visual Studio 2012 ou posterior com C# é necessário. Baixe a versão gratuita em [Downloads do Visual Studio](http://www.visualstudio.com/pt-br/downloads/download-visual-studio-vs.aspx).
2. Nuget 2.7 ou superior. Para obter a versão mais recente, consulte [Instalando o NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)
####Criar um banco de dados SQL do Azure

* Consulte [Introdução ao Banco de Dados SQL do Microsoft Azure](http://azure.microsoft.com/pt-br/documentation/articles/sql-database-get-started/).

##Baixar e executar o aplicativo de amostra
O aplicativo de exemplo **Escala Elástica com Banco de Dados SQL do Azure - Introdução** ilustra os aspectos mais importantes da experiência de desenvolvimento de aplicativos fragmentados usando a Escala Elástica do BD SQL do Azure. Foca nos principais casos de uso para [Gerenciamento de mapa de fragmentos](http://go.microsoft.com/?linkid=9862595)[Roteamento dependente de dados](http://go.microsoft.com/?linkid=9862596) e [Consulta de vários fragmentos](http://go.microsoft.com/?linkid=9862597). Para baixar e executar a amostra, siga estas etapas: 

1. Abra o Visual Studio e selecione **Arquivo -> Novo -> Projeto**.
2. Na caixa de diálogo, clique em **Online**.

    ![New Project>Online][2]
3. Clique em **Visual C#** em **Amostras**.

    ![Click Visual C#][3]
4. Na caixa de pesquisa, digite **Escala Elástica** para procurar a amostra. O título **Escala Elástica com Banco de Dados SQL do Azure - Introdução** aparece.

    ![Search Box][1]
 
5. Selecione a amostra, escolha um nome e um local para o novo projeto e pressione **OK** para criar o projeto.
6. Abra o arquivo **app.config** na solução para o projeto de amostra e siga as instruções no arquivo para adicionar o nome do seu servidor de banco de dados SQL do Azure e suas informações de logon (nome de usuário e senha).
7. Compile e execute o aplicativo. Quando solicitado, permita que o Visual Studio restaure os pacotes NuGet da solução. Com isso, ocorrerá o download da versão mais recente das bibliotecas de cliente da Escala Elástica do NuGet.
8. Trabalhe com as diferentes opções para saber mais sobre os recursos da Escala Elástica. Observe as etapas que o aplicativo realiza na saída do console e fique à vontade para explorar o código nos bastidores.

    ![progress][4]

Parabéns, você criou e executou com êxito seu primeiro aplicativo de Escala Elástica no BD SQL do Azure. Analise rapidamente os fragmentos que a amostra criou ao se conectar com o SQL Server Management Studio ao seu Servidor de BD do Azure. Você observará novos bancos de dados de fragmentos de amostra e um banco de dados de gerenciador de mapa de fragmentos que a amostra criou.

**Observação** Se você não tiver o SQL Server Management Studio, consulte [Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio](http://azure.microsoft.com/pt-br/documentation/articles/sql-database-manage-azure-ssms/), que inclui instruções para obter a ferramenta.  

### Principais partes do exemplo de código

1. **Gerenciamento de fragmentos e mapas de fragmentos**:  O código ilustra como trabalhar com fragmentos, intervalos e mapeamentos no arquivo **ShardMapManagerSample.cs**.Você pode encontrar mais informações sobre esse tópico aqui:[Gerenciamento de mapa de fragmentos](http://go.microsoft.com/?linkid=9862595).  
2. **Roteamento dependente de dados**: O roteamento de transações para o fragmento correto é mostrado em **DataDependentRoutingSample.cs**. Para obter mais detalhes, consulte [Roteamento dependente de dados](http://go.microsoft.com/?linkid=9862596). 
3. **Consulta em vários fragmentos**: A consulta entre os fragmentos é ilustrada no arquivo **MultiShardQuerySample.cs**. Para obter mais detalhes, consulte [Consulta de vários fragmentos](http://go.microsoft.com/?linkid=9862597).
4. **Adição de fragmentos vazios**:A adição iterativa de novos fragmentos vazios é realizada pelo código no
arquivo **AddNewShardsSample.cs**. Os detalhes sobre esse tópico são abordados aqui:[Gerenciamento de mapa de fragmentos](http://go.microsoft.com/?linkid=9862595).

### Outras operações da Escala Elástica

1. **Divisão de um fragmento existente**:  A capacidade de dividir fragmentos é fornecida pelo **serviço de Divisão/Mesclagem**.Você podem encontrar mais informações sobre esse serviço aqui:[Serviço de Divisão/Mesclagem](http://go.microsoft.com/?linkid=9862795).
2. **Mesclagem de fragmentos existentes**:  As mesclagens de fragmentos também são realizadas usando o **serviço de Divisão/Mesclagem**.Para obter mais informações, consulte:[Serviço de Divisão/Mesclagem](http://go.microsoft.com/?linkid=9862795).   


## Custo

Os modelos de serviço e as bibliotecas da Escala Elástica são gratuitos. A Escala Elástica não impõe encargos adicionais sobre os custos para o uso do Azure. 

Por exemplo, o aplicativo de exemplo cria novos bancos de dados. O custo depende da edição de banco de dados do BD SQL do Azure escolhida e do uso do Azure do seu aplicativo.

Para obter informações sobre os preços, consulte [Detalhes de preços do Banco de Dados SQL](http://azure.microsoft.com/pt-br/pricing/details/sql-database/).

## Próximas etapas
Para obter mais informações sobre o recurso de Escala Elástica, consulte:

* [Página de aprendizado da Escala Elástica](./sql-database-elastic-scale-documentation-map.md) 
-    Amostras de código: 
    -    [Escala Elástica com Banco de Dados SQL do Azure - Introdução](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [Escala Elástica com Banco de Dados SQL do Azure - Integração com o Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Elasticidade do fragmento no Script Center](http://go.microsoft.com/?linkid=9862617)
-    Blog: [Anúncio da Escala Elástica](http://go.microsoft.com/?linkid=9862608)
-    Channel 9: [Visão geral da Escala Elástica](http://go.microsoft.com/?linkid=9862609)
-    Fórum de discussão: [Fórum do Banco de Dados SQL do Azure](http://social.msdn.microsoft.com/forums/azure/en-US/home?forum=ssdsgetstarted)


<!--Anchors-->
[O aplicativo de exemplo da Escala Elástica]: #The-Elastic-Scale-Sample-Application
[Baixar e executar o aplicativo de amostra]: #Download-and-Run-the-Sample-App
[Custo]: #Cost
[Próximas etapas]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png

<!--HONumber=35_1-->
