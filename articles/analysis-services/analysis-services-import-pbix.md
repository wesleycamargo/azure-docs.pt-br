---
title: Importar um arquivo do Power BI Desktop no Azure Analysis Services | Microsoft Docs
description: Descreve como importar um arquivo do Power BI Desktop (pbix) usando o portal do Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/26/2018
ms.author: owend
ms.openlocfilehash: 43eab587a1e5209069a248f1e2e1f57af158a2b8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importar um arquivo do Power BI Desktop

Você pode criar um novo modelo no Azure AS importando um arquivo do Power BI Desktop (pbix). Metadados de modelo, dados armazenados em cache e conexões de fonte de dados são importados. Relatórios e visualizações não serão importadas. Uma vez em seu servidor, alterações de modelo podem ser feitas atualizando e reimportando o pbix, usando o recurso de web designer (versão prévia) no portal, ou usando o SQL Server Management Studio (SSMS). Modelos importados não podem ser abertos ou exportados para o Visual Studio.

> [!NOTE]
> Se seu modelo pbix se conecta a fontes de dados local, um [gateway local](analysis-services-gateway.md) deve ser configurado para o servidor.

## <a name="to-import-from-pbix"></a>Para importar do pbix

1. Na **Visão Geral** > **Web Designer** do servidor, clique em **Abrir**.

    ![Criar um modelo no portal do Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. Em **Designer de Web** > **Modelos**, clique em **+ Adicionar**.

    ![Criar um modelo no portal do Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. Em **Novo modelo**, digite um nome de modelo e, em seguida, selecione arquivo do Power BI Desktop.

    ![Caixa de diálogo Novo modelo no portal do Azure](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. Em **Importar**, localize e selecione seu arquivo.

     ![Caixa de diálogo Conectar no portal do Azure](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>Consulte também

[Como criar um modelo no portal do Azure](analysis-services-create-model-portal.md)   
[Conectar-se ao Azure Analysis Services](analysis-services-connect.md)  
