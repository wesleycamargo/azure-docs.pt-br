---
title: "Bibliotecas de cliente necessárias para conectar-se ao Azure Analysis Services | Microsoft Docs"
description: "Descreve as bibliotecas de cliente necessárias às ferramentas e ao aplicativos cliente para conectar o Azure Analysis Services"
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
ms.date: 12/14/2017
ms.author: owend
ms.openlocfilehash: 870d430d1926859894f452e0af812d794272a9e6
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliotecas de cliente para conectar-se ao Azure Analysis Services

Bibliotecas de cliente são necessárias para que ferramentas e aplicativos cliente se conectem aos servidores do Analysis Services. 

## <a name="download-the-latest-client-libraries"></a>Baixar as bibliotecas de cliente mais recentes  

|Baixar  |Versão  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    14.0.801.241      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    14.0.801.241      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   14.0.800.117      |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    14.0.801.241      |

## <a name="understanding-client-libraries"></a>Noções básicas sobre bibliotecas de clientes

O Analysis Services utiliza três bibliotecas de clientes, também conhecidas como provedores de dados. O ADOMD.NET e o AMO (Objetos de Gerenciamento do Analysis Services) são bibliotecas de clientes gerenciadas. O Provedor Analysis Services OLE DB (MSOLAP DLL) é uma biblioteca de clientes nativa. Normalmente, as três bibliotecas são instaladas ao mesmo tempo. **O Azure Analysis Services exige as versões mais recentes das três bibliotecas**. 

Os aplicativos clientes da Microsoft, como o Power BI Desktop e o Excel, instalam todas as três bibliotecas de clientes e as atualizam quando novas versões estão disponíveis. Dependendo da versão ou da frequência de atualizações, algumas bibliotecas de clientes podem não ser as versões mais recentes exigidas pelo Azure Analysis Services. O mesmo se aplica a aplicativos personalizados ou outras interfaces, como AsCmd, TOM, ADOMD.NET. Esses aplicativos exigem a instalação manual ou programática das bibliotecas. As bibliotecas de cliente para instalação manual estão incluídas nos pacotes de recursos do SQL Server como pacotes distribuíveis. No entanto, essas bibliotecas de cliente estão vinculadas à versão do SQL Server e talvez não sejam as mais recentes.  

As bibliotecas de cliente para conexões de cliente são diferentes dos provedores de dados necessários para se conectar de um servidor Azure Analysis Services a uma fonte de dados. Para saber mais sobre conexões de fonte de dados, confira [Conexões de fonte de dados](analysis-services-datasource.md).

## <a name="client-library-types"></a>Tipos de biblioteca de clientes

### <a name="analysis-services-ole-db-provider-msolap"></a>MSOLAP (Provedor Analysis Services OLE DB) 

 O MSOLAP (Provedor Analysis Services OLE DB) é a biblioteca de clientes nativa para conexões de banco de dados do Analysis Services. Ele é usado indiretamente pelo ADOMD.NET e pelo AMO, delegando solicitações de conexão ao provedor de dados. Você também pode chamar o provedor OLE DB diretamente do código do aplicativo.  
  
 O Provedor Analysis Services OLE DB é instalado automaticamente pela maioria das ferramentas e dos aplicativos clientes usados para acessar bancos de dados do Analysis Services. Ele deve ser instalado nos computadores usados para acessar dados do Analysis Services.  
  
 Os provedores OLE DB costumam ser especificados em cadeias de conexão. Uma cadeia de conexão do Analysis Services usa uma nomenclatura diferente para referenciar o provedor OLE DB: MSOLAP.\<version>.dll.

### <a name="amo"></a>AMO  

 O AMO é uma biblioteca de clientes gerenciada usada para administração de servidor e definição de dados. Ele é instalado e usado por aplicativos clientes e ferramentas. Por exemplo, o SSMS (SQL Server Management Studio) usa o AMO para se conectar ao Analysis Services.  
  
 Uma conexão usando o AMO geralmente é mínima, consistindo em `“data source=\<servername>”`. Depois que uma conexão é estabelecida, você usa a API para trabalhar com coleções de banco de dados e grandes objetos. Tanto o SSDT quanto o SSMS usam o AMO para se conectar a uma instância do Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 O ADOMD.NET é uma biblioteca de clientes de dados gerenciada usada para consultar dados do Analysis Services. Ele é instalado e usado por aplicativos clientes e ferramentas. 
  
 Ao se conectar a um banco de dados, as propriedades de cadeia de conexão das três bibliotecas são semelhantes. Quase todas as cadeias de conexão que você define para o ADOMD.NET usando [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) também funcionam para o AMO e o MSOLAP (Provedor Analysis Services OLE DB). Para saber mais, confira [Propriedades de cadeia de Conexão &#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>Como determinar a versão da biblioteca de clientes   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Vá para `C:\Program Files\Microsoft Analysis Services\AS OLEDB\140`. Se você tiver mais de uma pasta, escolha o número mais alto.
  
2.  Clique com botão direito do mouse em **msolap140.dll** > **Propriedades** > **Detalhes**.  
    
    ![Detalhes da biblioteca de clientes](media/analysis-services-data-providers/aas-msolap-details.png)
  
### <a name="amo"></a>AMO

1. Vá para `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\v4.0_14.0.0.0__89845dcd8080cc91`.
2. Clique com botão direito do mouse em **AnalysisServices** > **Propriedades** > **Detalhes**.  

### <a name="adomd"></a>ADOMD

1. Vá para `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\v4.0_14.0.0.0__89845dcd8080cc91`.
2. Clique com botão direito do mouse em **Microsoft.AnalysisServices.AdomdClient** > **Propriedades** > **Detalhes**.  


## <a name="next-steps"></a>Próximas etapas
[Conectar com Excel](analysis-services-connect-excel.md)    
[Conectar com o Power BI](analysis-services-connect-pbi.md)
