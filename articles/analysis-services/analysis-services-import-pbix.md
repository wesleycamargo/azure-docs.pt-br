---
title: Importar um arquivo do Power BI Desktop no Azure Analysis Services | Microsoft Docs
description: Descreve como importar um arquivo do Power BI Desktop (pbix) usando o portal do Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3dd90fc862e64812c0ba17bef74818d18788f4b5
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440963"
---
# <a name="import-a-power-bi-desktop-file"></a>Importar um arquivo do Power BI Desktop

É possível importar um modelo de dados em um arquivo do Power BI Desktop (pbix) para o Azure Analysis Services. Metadados de modelo, dados armazenados em cache e conexões de fonte de dados são importados. Relatórios e visualizações não serão importadas. Modelos de dados importados do Power BI Desktop estão no nível de compatibilidade 1400.

**Restrições**   
- O modelo pbix pode se conectar apenas a fontes de dados do **Banco de Dados SQL do Azure** e do **SQL Data Warehouse do Azure**. 
- O modelo pbix não pode ter conexões dinâmica ou DirectQuery. 
- A importação pode falhar se o modelo de dados pbix contiver metadados que incompatíveis com o Analysis Services.

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
