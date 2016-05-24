<properties
   pageTitle="Instalar o Visual Studio e o SSDT para o SQL Data Warehouse | Microsoft Azure"
   description="Instalar o Visual Studio e o SSDT (Ferramentas de Desenvolvimento do SQL Server) para o SQL Data Warehouse do Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/16/2016"
   ms.author="sonyama;barbkess"/>

# Instalar o Visual Studio 2015 e o SSDT para o SQL Data Warehouse

Para desenvolver aplicativos para o SQL Data Warehouse, recomendamos o uso do Visual Studio 2015 com a versão mais recente do SSDT (SQL Server Data Tools). Também há suporte para o Visual Studio 2013 com SSDT.

Além disso, a **Atualização do Microsoft SQL Server para as ferramentas do banco de dados** é necessária para executar consultas a partir do IDE (Ambiente de Desenvolvimento Integrado) do Visual Studio. Depois de instalar essa extensão, em seguida, você poderá exibir os objetos do banco de dados na árvore do Gerenciador de objetos e executar consultas no SQL Data Warehouse.

> [AZURE.NOTE] O SQL Data Warehouse ainda não dá suporte aos Projetos do Banco de Dados do Visual Studio. Este recurso será adicionado em uma futura versão.

## Etapa 1: Instalar o Visual Studio 2015

Siga estes links para baixar e instalar o Visual Studio 2015. Se você já tiver o Visual Studio 2013 ou 2015 instalado, pule para a Etapa 2 e instale o SSDT.

1. [Baixar o Visual Studio 2015][] do Visual Studio Team Services.
2. Siga o guia [Instalação do Visual Studio][] no MSDN e escolha as configurações padrão.

## Etapa 2: Instalar o SSDT

Para instalar o SSDT para o Visual Studio, simplesmente procure uma atualização do SSDT dentro do Visual Studio seguindo estas etapas.

1. No Visual Studio, clique em **Ferramentas** / **Extensões e Atualizações...** / **Atualizações**
2. Selecione **Atualizações do Produto**, em seguida, procure **Atualização do Microsoft SQL Server para as ferramentas do banco de dados**

Se uma atualização não for encontrada, então, você terá a versão mais recente instalada. Para confirmar se o SSDT está instalado, clique em **Ajuda** / **Sobre o Microsoft Visual Studio** e procure as Ferramentas de Dados do SQL Server na lista.

## Próximas etapas

Agora que você tem a versão mais recente do SSDT, está pronto para [conectar-se][] ao banco de dados.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[conectar-se]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[Baixar o Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Instalação do Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx

<!---HONumber=AcomDC_0518_2016-->