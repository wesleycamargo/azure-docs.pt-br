<properties 
	pageTitle="Adicionar referências de Escala Elástica do banco de dados SQL do Azure a um projeto do Visual Studio" 
	description="Como adicionar referências do .NET a APIs de escala elástica para projetos do Visual Studio usando o Nuget." 
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
	ms.date="04/16/2015" 
	ms.author="sidneyh"/>

# Como adicionar referências da biblioteca de cliente do banco de dados elástico a um projeto do Visual Studio 

### Pré-requisitos: 

- Instalar a [Galeria de extensões do NuGet Visual Studio](http://docs.nuget.org/docs/start-here/installing-nuget) para Visual Studio 

### Para adicionar uma referência de biblioteca de cliente do banco de dados elástico no Visual Studio 

1. Abra um projeto existente ou crie um novo projeto usando a caixa de diálogo Novo Projeto localizada em **Arquivo** --> **Novo** --> **Projeto** 
2. No Gerenciador de Soluções, clique com o botão direito em **Referências** e selecione **Gerenciar Pacotes NuGet**
3. No menu no lado esquerdo da janela Gerenciar Pacotes NuGet, selecione **Online** e depois **nuget.org** ou “Tudo” 
4. Na caixa de diálogo **Pesquisar Online**, digite **Banco de Dados Elástico**, selecione a **biblioteca de cliente do Banco de Dados Elástico** e clique em **Instalar**.

	![Pesquisa online][1]
4. Examine a licença e clique em **Eu Aceito**. 

A referência da biblioteca de cliente será adicionada ao seu projeto.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-add-references-visual-studio/search-online.png
<!--anchors-->

<!---HONumber=July15_HO2-->