---
title: "Provedores de dados necessários para conexões de cliente com o Azure Analysis Services | Microsoft Docs"
description: "Descreve os provedores de dados necessários para os clientes se conectarem ao Azure Analysis Services"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 13eb8ab1bf3c218f14b4c23ca1a46e9552d55b25
ms.openlocfilehash: d70b9f2c2a0cb1abe11dcfee9acb51dcb5552a60


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>Provedores de dados para conexão com o Azure Analysis Services

Os provedores de dados, também conhecidos como bibliotecas de cliente, são necessários para que os aplicativos cliente se conectem aos servidores Analysis Services. 

O Analysis Services utiliza três provedores de dados. O ADOMD.NET e o AMO (Objetos de Gerenciamento do Analysis Services) são provedores de dados gerenciados. O provedor OLE DB do Analysis Services (MSOLAP DLL) é um provedor de dados nativo. Normalmente, os três provedores são instalados ao mesmo tempo. O Azure Analysis Services exige as versões mais recentes dos provedores de dados. 

Os aplicativos cliente da Microsoft, como o Power BI Desktop e o Excel, instalam os três provedores de dados. No entanto, dependendo da versão do Excel, ou se as versões mais novas do Excel e do Power BI Desktop são ou não atualizadas mensalmente, os provedores de dados instalados podem não ser atualizados para as últimas versões exigidas pelo Azure Analysis Service. O mesmo se aplica a aplicativos personalizados ou outras interfaces, como AsCmd, TOM, ADOMD.NET. Esses aplicativos exigem a instalação manual dos provedores. Os provedores de dados de instalação manual estão incluídos nos pacotes de recursos do SQL Server como pacotes distribuíveis, no entanto, eles são associados à versão do SQL Server e podem não ser os mais recentes.  

Os provedores de dados para conexões de cliente são diferentes dos provedores de dados necessários para se conectar de um servidor Azure Analysis Services a uma fonte de dados. Para saber mais sobre conexões de fonte de dados, confira [Conexões de fonte de dados](analysis-services-datasource.md).
 
## <a name="download-the-latest-data-providers"></a>Baixar os provedores de dados mais recentes  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Próximas etapas
Com os provedores de dados mais recentes instalados, seu aplicativo cliente está pronto para se conectar a um servidor. Para saber mais sobre a conexão de um cliente, confira [Obter dados do Azure Analysis Services](analysis-services-connect.md).



<!--HONumber=Feb17_HO2-->


