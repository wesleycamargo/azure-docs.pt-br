---
title: Instalar o Visual Studio e o SSDT para o SQL Data Warehouse | Microsoft Docs
description: Instalar o Visual Studio e o SSDT (Ferramentas de Desenvolvimento do SQL Server) para o SQL Data Warehouse do Azure
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 0ed9b406-9b42-4fe6-b963-fe0a5b48aac1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f7bafc793bf8abddd50b7976733b8fdf389a0366


---
# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>Instalar o Visual Studio 2015 e o SSDT para o SQL Data Warehouse
Para desenvolver aplicativos para o SQL Data Warehouse, recomendamos o uso do Visual Studio 2015 com a versão mais recente do SSDT (SQL Server Data Tools).  Também há suporte para o Visual Studio 2013 Atualização 5 com SSDT a fim de permitir a compatibilidade com versões anteriores.  

O uso do Visual Studio com SSDT permite o uso do Pesquisador de Objetos do SQL Server para explorar visualmente tabelas, exibições, procedimentos armazenados e muitos outros objetos em seu SQL Data Warehouse, além da execução de consultas.

> [!NOTE]
> O SQL Data Warehouse ainda não dá suporte aos Projetos do Banco de Dados do Visual Studio.  Este recurso será adicionado em uma futura versão.
> 
> 

## <a name="step-1-install-visual-studio-2015"></a>Etapa 1: Instalar o Visual Studio 2015
Siga estes links para baixar e instalar o Visual Studio 2015. Se você já tiver o Visual Studio 2013 ou 2015 instalado, pule para a Etapa 2 e instale o SSDT.

1. [Baixar o Visual Studio 2015][Baixar o Visual Studio 2015].
2. Siga o guia [Instalação do Visual Studio][Instalação do Visual Studio] no MSDN e escolha as configurações padrão.

## <a name="step-2-install-ssdt"></a>Etapa 2: Instalar o SSDT
Para instalar o SSDT para o Visual Studio, simplesmente procure uma atualização do SSDT dentro do Visual Studio seguindo estas etapas.

1. No Visual Studio, clique em **Ferramentas** / **Extensões e Atualizações…** / **Atualizações**
2. Selecione **Atualizações do Produto**, em seguida, procure **Atualização do Microsoft SQL Server para as ferramentas do banco de dados**

Se nenhuma atualização for encontrada, você já terá a versão mais recente instalada.  Para confirmar se o SSDT está instalado, clique em **Ajuda** / **Sobre o Microsoft Visual Studio** e procure o SQL Server Data Tools na lista.  A versão mais recente do SSDT é 14.0.60525.0.  Se a opção de instalação não estiver disponível no Visual Studio, uma alternativa é visitar a página de [Download do SSDT][Download do SSDT] para baixar e instalar o SSDT manualmente.

## <a name="next-steps"></a>Próximas etapas
Agora que você tem a versão mais recente do SSDT, está pronto para se [conectar][conectar] ao SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[conectar]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Baixar o Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Instalação do Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Download do SSDT]: https://msdn.microsoft.com/library/mt204009.aspx



<!--HONumber=Nov16_HO3-->


