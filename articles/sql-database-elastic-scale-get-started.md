<properties title="Introdu&ccedil;&atilde;o &agrave; Escala El&aacute;stica do Banco de Dados SQL do Azure" pageTitle="Introdu&ccedil;&atilde;o &agrave; Escala El&aacute;stica do Banco de Dados SQL do Azure" description="Explica&ccedil;&atilde;o b&aacute;sica do recurso de Escala El&aacute;stica do Banco de Dados SQL do Azure, incluindo um aplicativo de amostra f&aacute;cil de executar." metaKeywords="sharding scaling, Azure SQL DB sharding, elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Introdução à visualização da Escala Elástica do Banco de Dados SQL do Azure

O aumento e a redução da capacidade sob demanda é uma das principais promessas da computação em nuvem. Historicamente, o cumprimento dessa promessa tem sido tedioso e complexo para a camada de banco de dados dos aplicativos em nuvem. Ao longo dos últimos anos, o setor convergiu em padrões de design bem estabelecidos conhecidos como fragmentação. Embora o padrão de fragmentação geral lide com o desafio, a criação e o gerenciamento de aplicativos usando a fragmentação exigem investimentos em infraestrutura significativos independentes da lógica de negócios do aplicativo.

A Escala Elástica do Banco de Dados SQL do Azure (em visualização) permite que a camada de dados de um aplicativo aumente e diminua por meio de práticas de fragmentação padrão do setor, além de simplificar significativamente o desenvolvimento e o gerenciamento de seus aplicativos fragmentados em nuvem. A Escala Elástica oferece funcionalidades de gerenciamento e de desenvolvedor que são fornecidas por meio de um conjunto de bibliotecas .Net e por modelos de serviço do Azure que você pode hospedar em sua assinatura do Azure para gerenciar seus aplicativos altamente escalonáveis. A Escala Elástica do BD do Azure implementa os aspectos de infraestrutura da fragmentação e, com isso, permite que você se concentre na lógica de negócios de seu aplicativo.

Este documento apresenta a experiência do desenvolvedor da Escala Elástica do BD SQL do Azure.

Para mais informações sobre como a Escala Elástica funciona, consulte a [Visão geral da Escala Elástica][Visão geral da Escala Elástica].

Para obter uma lista de todos os tópicos sobre a Escala Elástica, consulte [Mapa da documentação da Escala Elástica][Mapa da documentação da Escala Elástica]

## O aplicativo de amostra da Escala Elástica

A amostra cria um aplicativo fragmentado simples e explora os principais recursos da Escala Elástica. Para baixar e executar o aplicativo, siga as etapas mostradas abaixo ou no vídeo [Escala Elástica - Introdução][Escala Elástica - Introdução].

### Pré-requisitos

Para executar o aplicativo de amostra, você deve usar o Visual Studio e deve ter acesso a um Banco de Dados SQL do Azure em execução no Azure. Se você ainda não tiver uma assinatura do Azure, inscreva-se para uma [assinatura de avaliação][assinatura de avaliação].

#### Visual Studio e Nuget

1.  O Visual Studio 2012 ou posterior com C# é necessário. Baixe a versão gratuita em [Downloads do Visual Studio][Downloads do Visual Studio].
2.  Nuget 2.7 ou superior. Para obter a versão mais recente, consulte [Instalando o NuGet][Instalando o NuGet]

    #### Criar um banco de dados SQL do Azure

-   Consulte [Introdução ao Banco de Dados SQL do Microsoft Azure][Introdução ao Banco de Dados SQL do Microsoft Azure].

## Baixar e executar o aplicativo de amostra

O aplicativo de amostra **Escala Elástica com Banco de Dados SQL do Azure - Introdução** ilustra os aspectos mais importantes da experiência de desenvolvimento de aplicativos fragmentados usando a Escala Elástica do BD SQL do Azure. Ele se concentra nos principais casos de uso para [Gerenciamento de mapa de fragmentos][Gerenciamento de mapa de fragmentos], [Roteamento dependente de dados][Roteamento dependente de dados] e [Consulta de vários fragmentos][Consulta de vários fragmentos]. Para baixar e executar a amostra, siga estas etapas:

1.  Abra o Visual Studio e selecione **Arquivo -\> Novo -\> Projeto**.
2.  Na caixa de diálogo, clique em **Online**.

    ![Novo Projeto\>Online][Novo Projeto\>Online]

3.  Clique em **Visual C#** em **Amostras**.

    ![Clique em Visual C#][Clique em Visual C#]

4.  Na caixa de pesquisa, digite **Escala Elástica** para procurar a amostra. O título **Escala Elástica com Banco de Dados SQL do Azure - Introdução** aparece.

    ![Caixa de pesquisa][Caixa de pesquisa]

5.  Selecione a amostra, escolha um nome e um local para o novo projeto e pressione **OK** para criar o projeto.
6.  Abra o arquivo **app.config** na solução para o projeto de amostra e siga as instruções no arquivo para adicionar o nome do seu servidor de banco de dados SQL do Azure e suas informações de logon (nome de usuário e senha).
7.  Compile e execute o aplicativo. Quando solicitado, permita que o Visual Studio restaure os pacotes NuGet da solução. Com isso, ocorrerá o download da versão mais recente das bibliotecas de cliente da Escala Elástica do NuGet.
8.  Trabalhe com as diferentes opções para saber mais sobre os recursos da Escala Elástica. Observe as etapas que o aplicativo realiza na saída do console e fique à vontade para explorar o código nos bastidores.

    ![progresso][progresso]

Parabéns, você criou e executou com êxito seu primeiro aplicativo de Escala Elástica no BD SQL do Azure. Analise rapidamente os fragmentos que a amostra criou ao se conectar com o SQL Server Management Studio ao seu Servidor de BD do Azure. Você observará novos bancos de dados de fragmentos de amostra e um banco de dados de gerenciador de mapa de fragmentos que a amostra criou.

**Observação** Se você não tiver o SQL Server Management Studio, consulte [Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio][Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio], que inclui instruções para obter a ferramenta.

### Principais partes da amostra do código

1.  **Gerenciamento de fragmentos e mapas de fragmentos**: O código ilustra como trabalhar com fragmentos, intervalos e mapeamentos no arquivo **ShardMapManagerSample.cs**. Você pode encontrar mais informações sobre esse tópico aqui: [Gerenciamento de mapa de fragmentos][Gerenciamento de mapa de fragmentos].
2.  **Roteamento dependente de dados**: O roteamento de transações para o fragmento correto é mostrado em **DataDependentRoutingSample.cs**. Para obter mais detalhes, consulte [Roteamento dependente de dados][Roteamento dependente de dados].
3.  **Consulta em vários fragmentos**: A consulta entre os fragmentos é ilustrada no arquivo **MultiShardQuerySample.cs**. Para obter mais detalhes, consulte [Consulta de vários fragmentos][Consulta de vários fragmentos].
4.  **Adição de fragmentos vazios**: A adição iterativa de novos fragmentos vazios é realizada pelo código no
    arquivo **AddNewShardsSample.cs**. Os detalhes sobre esse tópico são abordados aqui: [Gerenciamento de mapa de fragmentos][Gerenciamento de mapa de fragmentos].

### Outras operações da Escala Elástica

1.  **Divisão de um fragmento existente**: A capacidade de dividir fragmentos é fornecida pelo **serviço de Divisão/Mesclagem**. Você podem encontrar mais informações sobre esse serviço aqui: [Serviço de Divisão/Mesclagem][Serviço de Divisão/Mesclagem].
2.  **Mesclagem de fragmentos existentes**: As mesclagens de fragmentos também são realizadas usando o **serviço de Divisão/Mesclagem**. Para obter mais informações, consulte: [Serviço de Divisão/Mesclagem][Serviço de Divisão/Mesclagem].

## Custo

Os modelos de serviço e as bibliotecas da Escala Elástica são gratuitos. A Escala Elástica não impõe encargos adicionais sobre os custos para o uso do Azure.

Por exemplo, o aplicativo de amostra cria novos bancos de dados. O custo depende da edição de banco de dados do BD SQL do Azure escolhida e do uso do Azure do seu aplicativo.

Para obter informações sobre os preços, consulte [Detalhes de preços do Banco de Dados SQL][Detalhes de preços do Banco de Dados SQL].

## Próximas etapas

Para obter mais informações sobre o recurso de Escala Elástica, consulte:

-   [Página de aprendizado da Escala Elástica][Mapa da documentação da Escala Elástica]
-   Amostras de código:

    -   [Escala Elástica com Banco de Dados SQL do Azure - Introdução][Escala Elástica com Banco de Dados SQL do Azure - Introdução]
    -   [Escala Elástica com Banco de Dados SQL do Azure - Integração com o Entity Framework][Escala Elástica com Banco de Dados SQL do Azure - Integração com o Entity Framework]
    -   [Elasticidade do fragmento no Script Center][Elasticidade do fragmento no Script Center]
-   Blog: [Anúncio da Escala Elástica][Anúncio da Escala Elástica]
-   Channel 9: [Visão geral da Escala Elástica][1]
-   Fórum de discussão: [Fórum do Banco de Dados SQL do Azure][Fórum do Banco de Dados SQL do Azure]

<!--Anchors-->
<!--Image references-->

  [Visão geral da Escala Elástica]: http://go.microsoft.com/?linkid=9862592
  [Mapa da documentação da Escala Elástica]: ./sql-database-elastic-scale-documentation-map.md
  [Escala Elástica - Introdução]: http://go.microsoft.com/?linkid=9862983
  [assinatura de avaliação]: http://azure.microsoft.com/pt-br/pricing/free-trial/
  [Downloads do Visual Studio]: http://www.visualstudio.com/pt-br/downloads/download-visual-studio-vs.aspx
  [Instalando o NuGet]: http://docs.nuget.org/docs/start-here/installing-nuget
  [Introdução ao Banco de Dados SQL do Microsoft Azure]: http://azure.microsoft.com/pt-br/documentation/articles/sql-database-get-started/
  [Gerenciamento de mapa de fragmentos]: http://go.microsoft.com/?linkid=9862595
  [Roteamento dependente de dados]: http://go.microsoft.com/?linkid=9862596
  [Consulta de vários fragmentos]: http://go.microsoft.com/?linkid=9862597
  [Novo Projeto\>Online]: ./media/sql-database-elastic-scale-get-started/click-online.png
  [Clique em Visual C#]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
  [Caixa de pesquisa]: ./media/sql-database-elastic-scale-get-started/newProject.png
  [progresso]: ./media/sql-database-elastic-scale-get-started/output2.png
  [Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio]: http://azure.microsoft.com/pt-br/documentation/articles/sql-database-manage-azure-ssms/
  [Serviço de Divisão/Mesclagem]: http://go.microsoft.com/?linkid=9862795
  [Detalhes de preços do Banco de Dados SQL]: http://azure.microsoft.com/pt-br/pricing/details/sql-database/
  [Escala Elástica com Banco de Dados SQL do Azure - Introdução]: http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE
  [Escala Elástica com Banco de Dados SQL do Azure - Integração com o Entity Framework]: http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE
  [Elasticidade do fragmento no Script Center]: http://go.microsoft.com/?linkid=9862617
  [Anúncio da Escala Elástica]: http://go.microsoft.com/?linkid=9862608
  [1]: http://go.microsoft.com/?linkid=9862609
  [Fórum do Banco de Dados SQL do Azure]: http://social.msdn.microsoft.com/forums/azure/pt-br/home?forum=ssdsgetstarted
