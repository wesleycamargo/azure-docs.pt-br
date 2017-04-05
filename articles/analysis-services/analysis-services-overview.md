---
title: "O que é o Azure Analysis Services | Microsoft Docs"
description: "Obtenha uma visão geral do Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 3539da8afe5781d74cbf723090050b767373f268
ms.lasthandoff: 03/25/2017


---
# <a name="what-is-azure-analysis-services"></a>O que é o Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Desenvolvido com base no mecanismo analítico comprovado do Microsoft SQL Server Analysis Services, o Azure Analysis Services fornece modelagem de dados de nível corporativo na nuvem. 

Assista a este vídeo para saber mais sobre como o Azure Analysis Services se adapta aos recursos gerais de BI da Microsoft e como você também pode se beneficiar com os modelos semânticos na nuvem.

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesGettingStarted/player]
>
>

> [!IMPORTANT]
> O Azure Analysis Services está em **visualização**. Existem algumas coisas que ainda não funcionam. Não se esqueça de conferir as [expectativas de visualização](#preview-expectations) mais adiante neste artigo. E fique de olho em nosso [blog do Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920) para obter as informações mais recentes.
> 
> 

## <a name="built-on-sql-server-analysis-services"></a>Criado no SQL Server Analysis Services
O Azure Analysis Services é compatível com o mesmo SQL Server 2016 Analysis Services Enterprise Edition que você já conhece. O Azure Analysis Services dá suporte a modelos tabulares no nível de compatibilidade 1200. DirectQuery, partições, segurança em nível de linha, relações bidirecionais e traduções: todos têm suporte.

## <a name="use-the-tools-you-already-know"></a>Use as ferramentas que você já conhece
![Ferramentas de desenvolvedor BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

Ao criar modelos de dados para o Azure Analysis Services, você poderá usar as mesmas ferramentas do SQL Server Analysis Services. Crie e implante modelos tabulares usando as versões mais recentes das [SSDT (SQL Server Data Tools)](https://msdn.microsoft.com/library/mt204009.aspx) ou usando o [Azure Powershell](/powershell/azureps-cmdlets-docs) e os modelos do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) no [SSMS (SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connect-to-data-sources"></a>Conectar às fontes de dados
Modelos de dados implantados em servidores no suporte do Azure, conectando a fontes de dados locais na sua organização ou na nuvem. Combine dados de fontes de dados locais e na nuvem para uma solução de BI híbrida.

![Fontes de dados](./media/analysis-services-overview/aas-overview-data-sources.png)

Como o servidor está na nuvem, a conexão com as fontes de dados nela é perfeita. Ao se conectar a fontes de dados locais, o [gateway de dados local](analysis-services-gateway.md) garante conexões rápidas e seguras com o servidor do Analysis Services na nuvem.  

 \* Algumas fontes de dados ainda não têm suporte no modo de visualização. Para saber mais, confira as [expectativas de visualização](#preview-expectations) mais adiante neste artigo.

## <a name="explore-your-data-from-anywhere"></a>Explorar seus dados de qualquer lugar
Conecte-se e [receba dados](analysis-services-connect.md) de seus servidores em praticamente qualquer lugar. O Azure Analysis Services dá suporte à conexão com o Power BI Desktop, com o Excel, com aplicativos personalizados e com ferramentas baseadas em navegador.

![Visualizações de dados](./media/analysis-services-overview/aas-overview-visualization.png)

 \* O Power BI Embedded ainda não tem suporte no modo de visualização.

## <a name="secure"></a>Segurança
#### <a name="user-authentication"></a>Autenticação de usuário
A autenticação de usuário para o Azure Analysis Services é feita pelo [AAD (Azure Active Directory)](../active-directory/active-directory-whatis.md). Quando tentam entrar em um banco de dados do Azure Analysis Services, os usuários usam uma identidade de conta da organização com acesso ao banco de dados que estão tentando acessar. Essas identidades de usuário devem ser membros do Azure Active Directory padrão da assinatura em que reside o servidor do Azure Analysis Services. A [Integração de diretórios](https://technet.microsoft.com/library/jj573653.aspx) entre o AAD e um Active Directory local é uma ótima maneira de dar acesso aos usuários a um banco de dados do Azure Analysis Services local, mas não é obrigatório em todos os cenários.

Os usuários entram com o nome UPN da conta e a senha. Quando sincronizado com um Active Directory local, o nome UPN do usuário geralmente é seu endereço de email empresarial.

As permissões para gerenciar o recurso do servidor do Azure Analysis Services são tratadas com a atribuição de usuários a funções em sua assinatura do Azure. Por padrão, os administradores de assinatura têm permissões de proprietário para o recurso de servidor no Azure. Outros usuários podem ser adicionados usando o Azure Resource Manager.

#### <a name="data-security"></a>Segurança de dados
O Azure Analysis Services utiliza o Armazenamento de Blobs do Azure para persistir o armazenamento e os metadados em bancos de dados do Analysis Services. Os arquivos de dados no blob são criptografados usando Azure SSE (criptografia do servidor de blobs). Ao usar o modo Consulta Direta, apenas os metadados são armazenados; os dados reais são acessados da fonte de dados no momento da consulta.

#### <a name="on-premises-data-sources"></a>Fontes de dados locais
O acesso protegido aos dados que residem localmente na organização pode ser feito com a instalação e a configuração de um [gateway de dados local](analysis-services-gateway.md). Os gateways fornecem acesso a dados nos modos Consulta Direta e de memória interna. Quando um modelo do Azure Analysis Services se conecta a uma fonte de dados local, uma consulta é criada junto com as credenciais criptografadas da fonte de dados local. O serviço de nuvem do gateway analisa a consulta e envia a solicitação para um Barramento de Serviço do Azure. O gateway local sonda o Barramento de Serviço do Azure para verificar solicitações pendentes. O gateway obtém a consulta, descriptografa as credenciais e conecta-se às fontes de dados para a execução. Os resultados são enviados da fonte de dados para o gateway e, em seguida, para o banco de dados do Azure Analysis Services.

O Azure Analysis Services é regido pelo [Termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e pela [política de privacidade do Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Para saber mais sobre a Segurança do Azure, confira a [Central de Confiabilidade da Microsoft](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="get-help"></a>Obter ajuda
O Azure Analysis Services é simples de configurar e gerenciar. Você pode encontrar todas as informações de que precisa para criar e gerenciar um servidor aqui. A criação de um modelo de dados para implantar no seu servidor é muito semelhante à criação de um modelo de dados para implantar em um servidor local. Existe uma ampla biblioteca de artigos conceituais, procedimentais, tutoriais e de referência em [Analysis Services no MSDN](https://msdn.microsoft.com/library/bb522607.aspx).

Também temos vários vídeos úteis em [Azure Analysis Services no Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

As coisas estão mudando rapidamente. Você sempre pode obter as informações mais recentes no [blog do Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920).

## <a name="community"></a>Comunidade
O Analysis Services tem uma comunidade de usuários vibrante. Participe das conversas no [Fórum do Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Comentários
Quer fazer sugestões ou solicitações de recursos? Deixe seus comentários nos [Comentários sobre o Azure Analysis Services](https://aka.ms/azureanalysisservicesfeedback).

Quer fazer sugestões sobre a documentação? Você pode adicionar comentários usando o Disqus na parte inferior de cada artigo.

## <a name="preview-expectations"></a>Expectativas da visualização
O Azure Analysis Services está atualmente em visualização. Existem alguns aspectos aos quais você deve estar atento.

##### <a name="server-modes"></a>Modos de servidor
O Azure Analysis Services atualmente dá suporte ao modo Tabular para modelos tabulares no nível de compatibilidade 1200. Não há suporte para os modos Multidimensional e Mineração de Dados e para o Power Pivot para SharePoint.

##### <a name="data-sources"></a>Fontes de dados
Na visualização, as fontes de dados a seguir têm suporte em modelos tabulares 1200 implantados em um servidor do Azure Analysis Services.

| **Nuvem** | **Local (exige o gateway de dados)** |
| --- | --- |
| Banco de dados SQL |SQL Server |
| SQL Data Warehouse |APS |
| Oracle | Oracle |
| Teradata | |

### <a name="data-source-providers"></a>Provedores de fonte de dados
Os modelos de dados no Azure Analysis Services podem exigir provedores de dados diferentes dos exigidos pelos modelos de dados no SQL Server Analysis Services para se conectarem às fontes de dados. Os requisitos do provedor de dados dependem da localização da fonte de dados ser na nuvem ou local e do tipo de modelo de dados, Consulta Direta ou na memória interna. Para saber mais, confira [Conexões com fonte de dados](analysis-services-datasource.md).

### <a name="client-connections"></a>Conexões de cliente

Os aplicativos cliente exigem os [provedores de dados](analysis-services-data-providers.md) de cliente mais recentes para se conectar ao Analysis Services do Azure.

Não há suporte para pastas de trabalho do Excel com conexões ativas em um servidor do Azure Analysis Services e salvas no OneDrive ou no SharePoint Online.

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe mais sobre o Azure Analysis Services, é hora de começar. Aprenda a [criar um servidor](analysis-services-create-server.md) no Azure e a [implantar um modelo tabular](analysis-services-deploy.md) nele.


