---
title: Instalar o Visual Studio e o SSDT para o SQL Data Warehouse | Microsoft Docs
description: Instalar o Visual Studio e o SSDT (Ferramentas de Desenvolvimento do SQL Server) para o SQL Data Warehouse do Azure
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a2f01424dedb977000d0e4150f4a31c1a9a21cfb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Instalar o Visual Studio e o SSDT para o SQL Data Warehouse
Para desenvolver aplicativos para o SQL Data Warehouse, recomendamos o uso da versão mais recente do Visual Studio com a versão mais recente do SSDT (SQL Server Data Tools).  Também há suporte para o Visual Studio 2013 Atualização 5 com SSDT a fim de permitir a compatibilidade com versões anteriores.  

Usar o Visual Studio com SSDT permite usar o Pesquisador de Objetos do SQL Server para explorar visualmente tabelas, visualizações, procedimentos armazenados e muito mais objetos no seu SQL Data Warehouse bem como executar consultas.

> [!NOTE]
> O SQL Data Warehouse ainda não dá suporte aos Projetos do Banco de Dados do Visual Studio. Para receber atualizações periódicas sobre esse recurso, registre-se no [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Etapa 1: instalar o Visual Studio
Siga estes links para baixar e instalar o Visual Studio. Se você já tiver o Visual Studio 2013 ou posterior instalado, pule para a Etapa 2 e instale o SSDT.

1. [Baixar o Visual Studio][].
2. Siga o guia [Instalação do Visual Studio][Installing Visual Studio] no MSDN e escolha as configurações padrão.

## <a name="step-2-install-ssdt"></a>Etapa 2: Instalar o SSDT
Para instalar o SSDT para o Visual Studio, verifique primeiro se há atualização do SSDT a partir do Visual Studio seguindo as etapas a seguir.

1. No Visual Studio, clique em **Ferramentas** / **Extensões e Atualizações…** / **Atualizações**
2. Selecione **Atualizações do Produto**, em seguida, procure **Atualização do Microsoft SQL Server para as ferramentas do banco de dados**

Se nenhuma atualização for encontrada, você já terá a versão mais recente instalada.  Para confirmar se o SSDT está instalado, clique em **Ajuda** / **Sobre o Microsoft Visual Studio** e procure o SQL Server Data Tools na lista. A versão mais recente do SSDT é 14.0.60525.0. Se a opção de instalação não estiver disponível no Visual Studio, uma alternativa é visitar a página de [Download do SSDT][SSDT Download] para baixar e instalar o SSDT manualmente.

## <a name="next-steps"></a>Próximas etapas
Agora que você tem a versão mais recente do SSDT, está pronto para se [conectar][connect] ao seu SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Baixar o Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
