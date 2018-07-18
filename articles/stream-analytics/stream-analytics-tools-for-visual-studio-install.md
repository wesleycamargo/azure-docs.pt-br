---
title: Configurar ferramentas do Azure Stream Analytics para Visual Studio
description: Este artigo descreve os requisitos de instalação e como instalar as ferramentas do Azyre Stream Analytics para Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/19/2017
ms.openlocfilehash: 511658fc0e2b480987455007dac5f55cd7850feb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instalar o Azure Stream Analytics Tools para Visual Studio
As ferramentas do Azure Stream Analytics agora dão suporte ao Visual Studio 2017, 2015 e 2013. Este documento descreve como instalar e desinstalar as ferramentas.

Para obter mais informações sobre como usar as ferramentas, veja [Ferramentas do Stream Analytics para Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

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



## <a name="update"></a>Atualizar

### <a name="visual-studio-2017"></a>Visual Studio 2017
O novo lembrete de versão aparece na notificação do Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
As ferramentas do Stream Analytics instaladas para o Visual Studio verificam automaticamente em busca de novas versões. Siga as instruções na janela pop-up para instalar a versão mais recente. 


## <a name="uninstall"></a>Desinstalar

### <a name="visual-studio-2017"></a>Visual Studio 2017
Clique duas vezes no instalador do Visual Studio e selecione **Modificar**. Desmarque a caixa de seleção **Azure Data Lake e Stream Analytics Tools** da carga de trabalho de **Armazenamento de dados e processamento** ou da carga de trabalho do **Desenvolvimento do Azure**.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Vá para o Painel de Controle e desinstale o **Microsoft Azure Data Lake e Ferramentas do Stream Analytics para Visual Studio**.





