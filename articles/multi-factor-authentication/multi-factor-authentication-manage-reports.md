---
title: "Relatórios de acesso e uso para o MFA do Azure | Microsoft Docs"
description: "Descreve como usar o recurso de relatórios do Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 42a87adef740cc2c1d77c9f02eef8aaa5f207258
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---
# <a name="reports-in-azure-multi-factor-authentication"></a>Relatórios no Azure Multi-Factor Authentication
O Azure Multi-Factor Authentication fornece vários relatórios que podem ser usados por você e sua organização. Esses relatórios podem ser acessados por meio do Portal de Gerenciamento da Autenticação Multifator. Veja a seguir uma lista dos relatórios disponíveis:

| Relatório | Descrição |
|:--- |:--- |
| Uso |Os relatórios de uso exibem informações sobre o uso geral, resumo do usuário e detalhes do usuário. |
| Status do servidor |Este relatório exibe o status dos servidores da autenticação multifator associada à sua conta. |
| Histórico de usuário bloqueado |Esses relatórios mostram o histórico de solicitações para bloquear ou desbloquear usuários. |
| Histórico de usuário desviado |Mostra o histórico de solicitações para desviar da autenticação multifator para o número de telefone de um usuário. |
| Alerta de fraude |Mostra um histórico dos alertas de fraude apresentados durante o intervalo de datas especificado. |
| Em fila |Lista os relatórios em fila para processamento e seu status. Um link para baixar ou exibir o relatório é fornecido quando o relatório é concluído. |

## <a name="view-reports"></a>Exibir relatórios
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. Selecione Active Directory à esquerda.
3. Siga uma destas duas opções, dependendo de se você usa Provedores de Autenticação:
   * **Opção 1**: clique na guia Provedores de Multi-Factor Authentication. Selecione seu provedor de MFA e clique no botão **Gerenciar** na parte inferior.
   * **Opção 2**: selecione seu diretório e acesse a guia **Configurar**. Na seção da autenticação multifator, escolha **Gerenciar configurações de serviço**. Na parte inferior da página Configurações do Serviço de MFA, clique em Ir para o link para portal.
4. No Portal de Gerenciamento da Autenticação Multifator do Azure, selecione o tipo de relatório desejado na seção **Exibir um Relatório** no painel de navegação esquerdo.

<center>![Nuvem](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Recursos adicionais**

* [Para usuários](end-user/multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication no MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)

