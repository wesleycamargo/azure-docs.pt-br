---
title: Instalar o pacote de conteúdo do Power BI para Azure AD | Microsoft Docs
description: Saiba como instalar o pacote de conteúdo do Power BI para Azure AD
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ba2784663a585aebb82ee149be3b54e73920f6dd
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816643"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>Início Rápido: instalar o pacote de conteúdo do Power BI para Azure Active Directory

|  |
|--|
|Atualmente, o pacote de conteúdo do Azure AD Power BI usa as APIs do Graph do Azure AD para recuperar dados do locatário do Azure AD. Como resultado, você poderá ver alguma disparidade entre os dados disponíveis no pacote de conteúdo e os dados recuperados usando as [APIs do Microsoft Graph para relatórios](concept-reporting-api.md). |
|  |

Com o pacote de conteúdo do Power BI para Azure Active Directory, você pode ter insights profundos do que está acontece em seu Active Directory. Você pode baixar o pacote de conteúdo pré-criado e usá-lo para relatar todas as atividades em seu diretório usando a avançada experiência de visualização que o Power BI oferece. Você também pode criar seu próprio painel e compartilhá-lo facilmente com qualquer pessoa em sua organização. 

Neste início rápido, você aprende como instalar o pacote de conteúdo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

* Uma conta do Power BI. É a mesma conta que você usa para o O365 ou para o Azure AD. 
* Sua ID do locatário do Azure AD. É a **ID de Diretório** de seu diretório, encontrada na [página de propriedades](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) do portal do Azure.
* Uma licença do Azure AD Premium (P1/P2). 

## <a name="install-azure-ad-power-bi-content-pack"></a>Instalar o pacote de conteúdo do Power BI para Azure AD 

1. Faça logon no [Power BI](https://app.powerbi.com/groups/me/getdata/services) com sua conta do Power BI. É a mesma conta que você usa para o O365 ou para o Azure AD.

2. Pesquise **Logs de Atividade do Azure Active Directory** na página **Aplicativos** e selecione **Obter agora**. 

   ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. Na janela pop-up, digite sua ID de locatário do Azure AD, insira **7** para o número de dias a consultar e, em seguida, selecione **Avançar**.
    
   ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. Quando o painel de logs de atividade do Azure Active Directory for criado, selecione-o.

   ![Pacote de Conteúdo do Power BI do Azure Active Directory](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>Próximas etapas

* [Usar o pacote de conteúdo do Power BI](howto-power-bi-content-pack.md).
* [Solucionar problemas com erros do pacote de conteúdo](troubleshoot-content-pack.md).
