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
   ms.date="07/23/2016"
   ms.author="sonyama;barbkess"/>

# Instalar o Visual Studio 2015 e o SSDT para o SQL Data Warehouse

Para desenvolver aplicativos para o SQL Data Warehouse, recomendamos o uso do Visual Studio 2015 com a versão mais recente do SSDT (SQL Server Data Tools). Também há suporte para o Visual Studio 2013 Atualização 5 com SSDT a fim de permitir a compatibilidade com versões anteriores.

O uso do Visual Studio com SSDT permite o uso do Pesquisador de Objetos do SQL Server para explorar visualmente tabelas, exibições, procedimentos armazenados e muitos outros objetos em seu SQL Data Warehouse, além da execução de consultas.

> [AZURE.NOTE] O SQL Data Warehouse ainda não dá suporte aos Projetos do Banco de Dados do Visual Studio. Este recurso será adicionado em uma futura versão.

## Etapa 1: Instalar o Visual Studio 2015

Siga estes links para baixar e instalar o Visual Studio 2015. Se você já tiver o Visual Studio 2013 ou 2015 instalado, pule para a Etapa 2 e instale o SSDT.

1. [Baixar o Visual Studio 2015][].
2. Siga o guia [Instalação do Visual Studio][] no MSDN e escolha as configurações padrão.

## Etapa 2: Instalar o SSDT

Para instalar o SSDT para o Visual Studio, simplesmente procure uma atualização do SSDT dentro do Visual Studio seguindo estas etapas.

1. No Visual Studio, clique em **Ferramentas** / **Extensões e Atualizações...** / **Atualizações**
2. Selecione **Atualizações do Produto**, em seguida, procure **Atualização do Microsoft SQL Server para as ferramentas do banco de dados**

Se nenhuma atualização for encontrada, você já terá a versão mais recente instalada. Para confirmar se o SSDT está instalado, clique em **Ajuda** / **Sobre o Microsoft Visual Studio** e procure as Ferramentas de Dados do SQL Server na lista. A versão mais recente do SSDT é 14.0.60525.0. Se a opção de instalação não estiver disponível no Visual Studio, uma alternativa é visitar a página de [Download do SSDT][] para baixar e instalar o SSDT manualmente.

## Próximas etapas

Agora que você tem a versão mais recente do SSDT, está pronto para se [conectar][] ao seu SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[conectar]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[Baixar o Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Instalação do Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Download do SSDT]: https://msdn.microsoft.com/library/mt204009.aspx

<!---HONumber=AcomDC_0727_2016-->