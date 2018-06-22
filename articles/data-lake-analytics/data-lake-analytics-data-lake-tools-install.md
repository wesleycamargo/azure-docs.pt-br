---
title: Instale as Ferramentas do Azure Data Lake para Visual Studio
description: Este artigo descreve como instalar o Azure Data Lake Tools para Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 05/03/2018
ms.openlocfilehash: c520c437212c23cc9dc8327c95b9f2a77b08e1ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622863"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalar Ferramentas do Data Lake para Visual Studio

Saiba como usar o Visual Studio para criar contas do Azure Data Lake Analytics, definir trabalhos no [U-SQL](data-lake-analytics-u-sql-get-started.md) e enviar trabalhos ao serviço do Data Lake Analytics. Para saber mais sobre a Análise Data Lake, consulte a [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>pré-requisitos

* **Visual Studio**: há suporte para todas as edições, exceto Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **SDK do Microsoft Azure para .NET** versão 2.7.1 ou posterior.  Instale-o usando o [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
* Uma conta do **Data Lake Analytics**. Para criar uma conta, confira [Introdução ao Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Instale as Ferramentas do Azure Data Lake para Visual Studio 2017

Há suporte para Ferramentas do Azure Data Lake para Visual Studio no Visual Studio 2017 15.3 ou superior. A ferramenta é parte das cargas de trabalho **Armazenamento e processamento de dados** e **Desenvolvimento do Azure** no Instalador do Visual Studio. Habilite qualquer uma dessas duas cargas de trabalho como parte da instalação do Visual Studio.  

Habilite a carga de trabalho **Armazenamento e processamento de dados** conforme mostrado: ![Habilitar carga de trabalho Armazenamento e processamento de dados](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Habilite a carga de trabalho **Desenvolvimento do Azure** conforme mostrado: ![Habilitar carga de trabalho de desenvolvimento do Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instale as Ferramentas do Azure Data Lake para Visual Studio 2013 e 2015

Baixe e instale as Ferramentas do Azure Data Lake para Visual Studio [do Centro de Download](http://aka.ms/adltoolsvs). Após a instalação, observe que:
* O nó do **Gerenciador de Servidores** > do **Azure** contém um nó do **Data Lake Analytics**. 
* O menu de **ferramentas** tem um item do **Data Lake**.


## <a name="next-steps"></a>Próximas etapas
* Para registrar em log as informações de diagnóstico, veja [Acessando os logs de diagnóstico para o Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando a Análise Data Lake do Azure](data-lake-analytics-analyze-weblogs.md).
* Para usar o modo de exibição de execução de vértice, veja [Usar o Modo de Exibição de Execução de Vértice nas Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
