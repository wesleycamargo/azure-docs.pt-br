---
title: Solução de erros do pacote de conteúdo dos Logs de atividades do Azure Active Directory | Microsoft Docs
description: Fornece uma lista de mensagens de erro do pacote de conteúdo de Atividades do Azure Active Directory e as etapas para corrigi-las.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bf50dbf942dc7a82afbb60455be45b6c4b287ccd
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782153"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Solução de erros do pacote de conteúdo dos Logs de atividades do Azure Active Directory 

|  |
|--|
|Atualmente, o pacote de conteúdo do Azure AD Power BI usa as APIs do Graph do Azure AD para recuperar dados do locatário do Azure AD. Como resultado, você poderá ver alguma disparidade entre os dados disponíveis no pacote de conteúdo e os dados recuperados usando as [APIs do Microsoft Graph para relatórios](concept-reporting-api.md). |
|  |

Ao trabalhar com o Pacote de Conteúdo do Power BI para a Versão Prévia do Azure Active Directory, é possível encontrar os seguintes erros: 

- [Falha na atualização](troubleshoot-content-pack.md#refresh-failed) 
- [Falha ao atualizar as credenciais de fonte de dados](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importação de dados muito demorada](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
Este artigo fornece informações sobre as possíveis causas e como corrigir esses erros.
 
## <a name="refresh-failed"></a>Falha na atualização 
 
**Como esse erro é exibido**: email do Power BI ou status de falha no histórico de atualizações. 


| Causa | Como corrigir |
| ---   | ---        |
| Erros de falha na atualização podem ser causados quando as credenciais dos usuários que se conectam ao pacote de conteúdo foram redefinidas, mas não atualizadas nas configurações de conexão do pacote de conteúdo. | No Power BI, localize o conjunto de dados correspondente ao painel dos Logs de atividades do Azure Active Directory (Logs de atividades do Azure Active Directory), escolha Agendar atualização e, em seguida, insira suas credenciais do Azure AD. |
| Uma atualização poderá falhar devido a problemas de dados no pacote de conteúdo subjacente. | Registre um tíquete de suporte. Para obter mais informações, consulte [Como obter suporte para o Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Falha ao atualizar as credenciais de fonte de dados 
 
**Como esse erro é exibido**: no Power BI, ao se conectar ao pacote de conteúdo dos Logs de atividades do Azure Active Directory (versão prévia). 

| Causa | Como corrigir |
| ---   | ---        |
| O usuário que está se conectando não é um administrador global nem um leitor de segurança ou administrador de segurança. | Use uma conta que é um administrador global ou um leitor de segurança ou administrador de segurança para acessar os pacotes de conteúdo. |
| Seu locatário não é um locatário Premium ou não tem, pelo menos, um usuário com um Arquivo de licença Premium. | Registre um tíquete de suporte. Para obter mais informações, consulte [Como obter suporte para o Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>Importação de dados muito demorada 
 
**Como esse erro é exibido**: no Power BI, depois de você se conectar ao pacote de conteúdo, o processo de importação de dados começa a preparar o painel para o Log de atividades do Azure Active Directory. Você vê a mensagem: “*Importando dados...*”  

| Causa | Como corrigir |
| ---   | ---        |
| Dependendo do tamanho do locatário, esta etapa poderá levar de alguns minutos a 30 minutos. | Seja paciente. Se a mensagem não for alterada para mostrar o painel em até uma hora, registre um tíquete de suporte. Para obter mais informações, consulte [Como obter suporte para o Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Próximas etapas

Para instalar o Pacote de Conteúdo do Power BI para a Versão Prévia do Azure Active Directory, clique [aqui](https://powerbi.microsoft.com/blog/azure-active-directory-meets-power-bi/).


