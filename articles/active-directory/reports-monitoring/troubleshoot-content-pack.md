---
title: Solução de erros do pacote de conteúdo dos Logs de atividades do Azure Active Directory | Microsoft Docs
description: Fornece uma lista de mensagens de erro do pacote de conteúdo de Atividades do Azure Active Directory e as etapas para corrigi-las.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78676ac2f2dcff74a27e0260a5d83e924f7c246f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60284952"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Solução de erros do pacote de conteúdo dos Logs de atividades do Azure Active Directory 

|  |
|--|
|Atualmente, o pacote de conteúdo do Azure AD Power BI usa as APIs do Graph do Azure AD para recuperar dados do locatário do Azure AD. Como resultado, você poderá ver alguma disparidade entre os dados disponíveis no pacote de conteúdo e os dados recuperados usando as [APIs do Microsoft Graph para relatórios](concept-reporting-api.md). |
|  |

Ao trabalhar com o Pacote de Conteúdo do Power BI para Azure AD (Azure Active Directory), é possível que você encontre os seguintes erros: 

- [Falha na atualização](troubleshoot-content-pack.md#refresh-failed) 
- [Falha ao atualizar as credenciais de fonte de dados](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importação de dados muito demorada](#data-import-is-too-slow) 

Este artigo fornece informações sobre as possíveis causas e como corrigir esses erros.
 
## <a name="refresh-failed"></a>Falha na atualização 
 
**Como esse erro ocorre**: Email do Power BI ou status com falha no histórico de atualizações. 


| Causa | Como corrigir |
| ---   | ---        |
| Erros de falha na atualização podem ser causados quando as credenciais dos usuários que se conectam ao pacote de conteúdo foram redefinidas, mas não atualizadas nas configurações de conexão do pacote de conteúdo. | No Power BI, localize o conjunto de dados correspondente ao painel de logs de atividades do Azure AD (**Logs de atividades do Azure Active Directory**), escolha programar atualização e insira as credenciais do Azure AD. |
| Uma atualização poderá falhar devido a problemas de dados no pacote de conteúdo subjacente. | [Registre um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Falha ao atualizar as credenciais de fonte de dados 
 
**Como esse erro ocorre**: No Power BI ao conectar o pacote de conteúdo dos logs de atividades do Azure AD. 

| Causa | Como corrigir |
| ---   | ---        |
| O usuário de conexão não é um administrador global, um leitor de segurança ou um administrador de segurança. | Use uma conta que seja um administrador global, um leitor de segurança ou um administrador de segurança para acessar os pacotes de conteúdo. |
| Seu locatário não é um locatário Premium ou não tem, pelo menos, um usuário com um Arquivo de licença Premium. | [Registre um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>A importação de dados é muito lenta 
 
**Como esse erro ocorre**: No Power BI, após conectar o pacote de conteúdo, o processo de importação de dados começará a preparar o painel para os logs de atividades do Azure AD. Você verá a mensagem: **Importando dados...** sem qualquer outro andamento.  

| Causa | Como corrigir |
| ---   | ---        |
| Dependendo do tamanho do locatário, essa etapa poderá demorar de alguns minutos a 30 minutos. | Se a mensagem não for alterada para mostrar o painel dentro de uma hora, [registre um ticket de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Próximos passos

* [Instalar o pacote de conteúdo do Power BI para relatórios do Azure AD](quickstart-install-power-bi-content-pack.md).
* [Usar o pacote de conteúdo do Power BI para relatórios do Azure AD para visualizar os dados](howto-power-bi-content-pack.md)
* [Como obter suporte para o Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
