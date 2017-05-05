---
title: "Bibliotecas de cliente necessárias para conectar-se ao Analysis Services do Azure | Microsoft Docs"
description: "Descreve as bibliotecas de cliente necessárias às ferramentas e ao aplicativos cliente para conectar o Azure Analysis Services"
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
ms.date: 04/14/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: c29a6627f712b9d89ac65e845f3ccb4fb87bf8fb
ms.lasthandoff: 04/20/2017


---

# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliotecas de cliente para conectar-se ao Azure Analysis Services

Bibliotecas de cliente são necessárias para que ferramentas e aplicativos cliente se conectem aos servidores do Analysis Services. 

O Analysis Services utiliza três bibliotecas de cliente. O ADOMD.NET e o AMO (Objetos de Gerenciamento do Analysis Services) são bibliotecas de cliente gerenciadas. O provedor OLE DB do Analysis Services (MSOLAP DLL) é uma biblioteca de cliente nativa. Normalmente, as três bibliotecas são instaladas ao mesmo tempo. O Azure Analysis Services exige as versões mais recentes. 

Os aplicativos cliente da Microsoft, como o Power BI Desktop e o Excel, instalam as três bibliotecas de cliente. No entanto, dependendo da versão do Excel, ou se as versões mais novas do Excel e do Power BI Desktop são ou não atualizadas mensalmente, as bibliotecas de cliente instaladas podem não ser atualizadas para as últimas versões exigidas pelo Azure Analysis Service. O mesmo se aplica a aplicativos personalizados ou outras interfaces, como AsCmd, TOM, ADOMD.NET. Esses aplicativos exigem a instalação manual das bibliotecas. As bibliotecas de instalação manual estão incluídas nos pacotes de recursos do SQL Server como pacotes distribuíveis, no entanto, estes são associados à versão do SQL Server e podem não ser os mais recentes.  

As bibliotecas de cliente para conexões de cliente são diferentes dos provedores de dados necessários para se conectar de um servidor Azure Analysis Services a uma fonte de dados. Para saber mais sobre conexões de fonte de dados, confira [Conexões de fonte de dados](analysis-services-datasource.md).

## <a name="download-the-latest-preview-client-libraries"></a>Baixar as bibliotecas de cliente **visualização** mais recentes  
Use as bibliotecas de cliente a seguir para obter as correções de bugs e atualizações mais recentes. Essas bibliotecas de cliente são recomendadas ao se conectar à Visualização do Azure Analysis Services ou ao Analysis Services do SQL Server vNext.

[Visualização do MSOLAP (amd64)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[Visualização do MSOLAP (x86)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[Visualização do AMO](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[Visualização do ADOMD](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-client-libraries"></a>Baixar as bibliotecas de cliente **RTM** mais recentes  
Use as bibliotecas de cliente a seguir se estiver em um ambiente de produção e precisar de versões totalmente liberadas e com suporte completo.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Próximas etapas
[Conectar-se a um servidor do Azure Analysis Services](analysis-services-connect.md).

