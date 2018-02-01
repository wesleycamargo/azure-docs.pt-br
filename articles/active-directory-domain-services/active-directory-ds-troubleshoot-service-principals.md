---
title: "Azure Active Directory Domain Services: solução de problemas de configuração da entidade de serviço | Microsoft Docs"
description: "Solução de problemas de configuração da entidade de serviço para o Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: ergreenl
ms.openlocfilehash: e6144a4018f7fbe7dbf7b4693e3f41884e4a80a2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-service-principal-configuration"></a>Azure AD Domain Services: solução de problemas de configuração da entidade de serviço

Para realizar a manutenção, gerenciar e atualizar seu domínio, a Microsoft usa várias [Entidades de Serviço](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects) para se comunicar com o seu diretório. Se uma for configurada incorretamente ou excluída, poderá causar uma interrupção em seu serviço.

## <a name="aadds102-service-principal-not-found"></a>AADDS102: Entidade de serviço não encontrada

**Mensagem de alerta:**

*Uma Entidade de Serviço necessária para o funcionamento correto do Azure AD Domain Services foi excluída do seu diretório do Azure AD. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.*

As entidades de serviço são aplicativos usados pela Microsoft para gerenciar, atualizar e manter seu domínio gerenciado. Se forem excluídas, prejudicará a capacidade da Microsoft de realizar a manutenção em seu domínio. Use as etapas a seguir para determinar quais entidades de serviço precisam ser recriadas.

1. Navegue até a página [Aplicativos Empresariais – Todos os Aplicativos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) no portal do Azure.
2. Usando o menu suspenso Mostrar, selecione **Todos os Aplicativos** e clique em **Aplicar**.
3. Usando a tabela a seguir, pesquise cada ID de aplicativo colando a ID na caixa de pesquisa e pressionando Enter. Se os resultados da pesquisa estiverem vazios, recrie a entidade de serviço executando as etapas na coluna "resolução".

| ID do aplicativo | Resolução |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Recriar uma entidade de serviço ausente com o PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrar novamente no namespace Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Registrar novamente no namespace Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Entidades de Serviço com autocorreção](#service-principals-that-self-correct) |

### <a name="recreate-a-missing-service-principal-with-powershell"></a>Recriar uma Entidade de serviço ausente com o PowerShell

*Execute estas etapas para IDs ausentes: 2565bd9d-da50-47d4-8b85-4c97f669dc36*

**Correção:**

É necessária uma conta do Azure AD PowerShell para concluir estas etapas. Para saber mais sobre como instalar o Azure AD PowerShell, veja [este artigo](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Para resolver esse problema, digite os seguintes comandos em uma janela do PowerShell:
1. Install-Module AzureAD
2. Import-Module AzureAD
3. Verifique se a entidade de serviço necessária para o Azure AD Domain Services está ausente em seu diretório executando o seguinte comando do PowerShell:
      ```PowerShell
      Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
      ```
4. Crie a entidade de serviço digitando o seguinte comando do PowerShell:
     ```PowerShell
     New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
     ```
5. Após a criação da entidade de serviço ausente, aguarde duas horas e verifique a integridade do seu domínio.


### <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Registrar novamente no namespace Microsoft.AAD usando o portal do Azure

*Execute estas etapas para IDs ausentes: 443155a6-77f3-45e3-882b-22b3a8d431fb e abba844e-bc0e-44b0-947a-dc74e5d09022*

**Correção:**

Use as etapas a seguir para restaurar os Domain Services em seu diretório:

1. Navegue até a página [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.
2. Escolha a assinatura da tabela que está associada ao seu domínio gerenciado
3. Usando o painel de navegação esquerdo, escolha **Provedores de Recursos**
4. Pesquise "Microsoft.AAD" na tabela e clique em **Registrar novamente**
5. Para verificar se o alerta foi resolvido, exiba sua página de alertas de integridade em duas horas.


### <a name="service-principals-that-self-correct"></a>Entidades de Serviço com autocorreção

*Execute estas etapas para as IDs ausentes: d87dcbc6-a371-462e-88e3-28ad15ec4e64*

**Correção:**

A Microsoft pode identificar quando Entidades de Serviço específicas estão ausentes, configuradas incorretamente ou foram excluídas. Para corrigir o serviço rapidamente, a Microsoft recriará as próprias Entidades de Serviço. Verifique a integridade do seu domínio após duas horas para garantir que a entidade tenha sido recriada.

## <a name="contact-us"></a>Entre em contato
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](active-directory-ds-contact-us.md).
