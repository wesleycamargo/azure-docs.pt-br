<properties
   pageTitle="Instalar o Visual Studio 2015 e/ou o SSDT para SQL Data Warehouse | Microsoft Azure"
   description="Instalar o Visual Studio e/ou as ferramentas de desenvolvimento do SSDT para o SQL Data Warehouse do Azure"
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
   ms.date="04/20/2016"
   ms.author="sonyama;barbkess"/>

# Instalar o Visual Studio 2015 e/ou o SSDT para SQL Data Warehouse

Para desenvolver aplicativos para o SQL Data Warehouse, recomendamos o uso do Visual Studio 2015 com a versão mais recente do SSDT (SQL Server Data Tools). Também há suporte para o Visual Studio 2013 com SSDT.

Para executar consultas do Visual Studio Integrated Development Environment (IDE), você só precisa instalar o SSDT. Isso instalará o IDE do Visual Studio juntamente com o SSDT para que você pode usar o Pesquisador de Objetos do SQL Server para se conectar ao seu servidor do SQL Azure. Você poderá exibir e executar consultas em seus bancos de dados do SQL Data Warehouse.


## Etapa 1: Baixar e instalar o Visual Studio

Se você optar por instalar o Visual Studio, poderá usar o Visual Studio 2013 ou o Visual Studio 2015 com o SQL Data Warehouse. Se você já tiver o Visual Studio 2013 ou 2015 instalado, pule para a Etapa 2 para instalar o SSDT.

Para instalar o Visual Studio 2015:

1. [Baixar o Visual Studio 2015][] do Visual Studio Team Services.
2. Instale seguindo o guia [Instalando o Visual Studio][] do MSDN e escolha as configurações padrão.

## Etapa 2: Baixar e instalar o SSDT (SQL Server Data Tools) mais recente

Tendo o Visual Studio instalado ou não, ainda será necessário ter a versão mais recente do SSDT (SQL Server Data Tools) que dê suporte ao SQL Data Warehouse.

Para instalar a versão mais recente do SSDT:

1. [Baixe a Visualização do SQL Server Data Tools][] para Visual Studio 2013 ou 2015.
2. Instale seguindo as instruções de instalação no site de download.

## Próximas etapas

Agora que você tem a versão mais recente do SSDT, está pronto para [conectar-se][] ao banco de dados.

<!--Anchors-->

<!--Image references-->

<!--Arcticles-->
[conectar-se]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[Baixar o Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Instalando o Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Baixe a Visualização do SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!---HONumber=AcomDC_0511_2016-->