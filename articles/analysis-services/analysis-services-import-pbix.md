---
title: Importar um arquivo do Power BI Desktop no Azure Analysis Services | Microsoft Docs
description: Descreve como importar um arquivo do Power BI Desktop (pbix) usando o portal do Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aea6f3efcf3740527c43b75a30caadf6b2a8b623
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601064"
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
