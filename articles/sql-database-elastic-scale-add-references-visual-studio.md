<properties title="Add Azure SQL DB Elastic Scale References to a Visual Studio Project" pageTitle="Adicionar referências de Elastic Scale do banco de dados SQL do Azure a um projeto do Visual Studio" description="How to add .NET references for Elastic Scale APIs to Visual Studio projects using Nuget." metaKeywords="Azure SQL Database, elastic scale, Nuget references" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Como: Adicionar referências de Elastic Scale do banco de dados SQL do Azure a um projeto do Visual Studio 

###Pré-requisitos: 

- Instalar a [Galeria de extensões do NuGet Visual Studio](http://docs.nuget.org/docs/start-here/installing-nuget) para Visual Studio 

###Para adicionar uma referência de Elastic Scale no Visual Studio 

1. Crie um novo projeto usando a caixa de diálogo Novo Projeto localizada em **File** --> **Novo** --> **Projeto** 
2. No Gerenciador de Soluções, clique com o botão direito em **Referências** e selecione **Gerenciar Pacotes NuGet**
3. No menu no lado esquerdo da janela Gerenciar Pacotes NuGet, selecione **Online** e depois **nuget.org** ou "Tudo" 
4. Na caixa de diálogo de **Pesquisa Online**, digite **Elastic Scale**, selecione as **bibliotecas do cliente de Elastic Scale** e clique em **Instalar**.
4. Examine a licença e clique em **Eu Aceito**. 

As referências de Elastic Scale do banco de dados SQL do Azure agora foram adicionadas ao seu projeto. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
