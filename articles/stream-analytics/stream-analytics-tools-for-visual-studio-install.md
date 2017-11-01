---
title: "Instruções de instalação para o Azure Stream Analytics Tools para Visual Studio | Microsoft Docs"
description: "Instruções de instalação para o Azure Stream Analytics Tools para Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: 80ce672ae91231e432f7ac9da49df29bb03efeca
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2017
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Instruções de instalação para o Stream Analytics Tools para Visual Studio
As ferramentas do Azure Stream Analytics agora dão suporte ao Visual Studio 2017, 2015 e 2013. Neste documento, mostramos como instalar e desinstalar as ferramentas.

Saiba como usar as [ferramentas do Stream Analytics para Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Instalar
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Baixe o [Visual Studio 2017 (15.3 ou acima)](https://www.visualstudio.com/). As edições Enterprise (Ultimate/Premium), Professional, Community têm suporte. Não há suporte para a Edição Express. 
* As ferramentas do Stream Analytics são parte das cargas de trabalho de **Desenvolvimento do Azure** e de **Armazenamento e processamento de dados** no Visual Studio 2017. Habilite qualquer uma dessas duas cargas de trabalho como parte da instalação do Visual Studio.

Habilite a carga de trabalho de **Armazenamento e processamento de dados** conforme mostrado:

![Carga de trabalho de armazenamento e processamento de dados](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

Habilite a carga de trabalho de **Desenvolvimento do Azure** conforme mostrado:

![Carga de trabalho de desenvolvimento do Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Instale o Visual Studio 2015 ou o Visual Studio 2013 Atualização 4. As edições Enterprise (Ultimate/Premium), Professional, Community têm suporte. Não há suporte para a Edição Express. 
* Instale o SDK do Microsoft Azure para .NET versão 2.7.1 ou posterior usando o [Web platform installer](http://www.microsoft.com/web/downloads/platform.aspx).
* Instalar o [Azure Stream Analytics Tools para Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Atualização

### <a name="visual-studio-2017"></a>Visual Studio 2017
O novo lembrete de versão aparece na notificação do Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
As ferramentas do Stream Analytics instaladas para o Visual Studio verificam automaticamente em busca de novas versões. Siga as instruções na janela pop-up para instalar a versão mais recente. 


## <a name="uninstall"></a>Desinstalar

### <a name="visual-studio-2017"></a>Visual Studio 2017
Clique duas vezes no instalador do Visual Studio e selecione **Modificar**. Desmarque a caixa de seleção **Azure Data Lake e Stream Analytics Tools** da carga de trabalho de **Armazenamento de dados e processamento** ou da carga de trabalho do **Desenvolvimento do Azure**.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Vá para o Painel de Controle e desinstale o **Microsoft Azure Data Lake e Ferramentas do Stream Analytics para Visual Studio**.





