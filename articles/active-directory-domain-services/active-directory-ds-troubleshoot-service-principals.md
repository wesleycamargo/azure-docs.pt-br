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
ms.date: 02/19/2018
ms.author: ergreenl
ms.openlocfilehash: 7388bb291f665f195355a01d19a82cba9ed453eb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Solucionar problemas de configuração de entidade de serviço inválida para o domínio gerenciado

Este artigo ajuda você a solucionar problemas e resolver erros de configuração relacionados à entidade de serviço que resultam na seguinte mensagem de alerta:

## <a name="alert-aadds102-service-principal-not-found"></a>Alerta AADDS102: Entidade de serviço não encontrada
**Mensagem de alerta:** *Uma Entidade de Serviço necessária para o funcionamento correto do Azure AD Domain Services foi excluída do seu diretório do Azure AD. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.*

As [entidades de serviço](../active-directory/develop/active-directory-application-objects.md) são aplicativos usados pela Microsoft para gerenciar, atualizar e manter seu domínio gerenciado. Se forem excluídas, prejudicará a capacidade da Microsoft de realizar a manutenção em seu domínio. 


## <a name="check-for-missing-service-principals"></a>Verificar entidades de serviço ausentes
Use as etapas a seguir para determinar quais entidades de serviço precisam ser recriadas:

1. Navegue até a página [Aplicativos Empresariais – Todos os Aplicativos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) no portal do Azure.
2. No menu suspenso **Mostrar**, selecione **Todos os Aplicativos** e clique em **Aplicar**.
3. Usando a tabela a seguir, pesquise cada ID de aplicativo colando a ID na caixa de pesquisa e pressionando Enter. Se os resultados da pesquisa estiverem vazios, recrie a entidade de serviço executando as etapas na coluna "resolução".

| ID do aplicativo | Resolução |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Recriar uma Entidade de serviço ausente com o PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrar novamente no namespace Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Registrar novamente no namespace Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Entidades de Serviço com autocorreção](#service-principals-that-self-correct) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Recriar uma Entidade de serviço ausente com o PowerShell
Siga estas etapas se uma entidade de serviço com a ID ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` está ausente do seu diretório do AD do Azure.

**Correção**: É necessária uma conta do Azure AD PowerShell para concluir estas etapas. Para saber mais sobre como instalar o Azure AD PowerShell, veja [este artigo](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Para resolver esse problema, digite os seguintes comandos em uma janela do PowerShell:
1. Instalar o módulo do  Azure AD PowerShell e importá-lo.
    
    ```powershell 
    Install-Module AzureAD
    Import-Module AzureAD
    ```
    
2. Verifique se a entidade de serviço necessária para o Azure AD Domain Services está ausente em seu diretório executando o seguinte comando do PowerShell:
    
    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```
    
3. Crie a entidade de serviço digitando o seguinte comando do PowerShell:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```
    
4. Após a criação da entidade de serviço ausente, aguarde duas horas e verifique a integridade do seu domínio gerenciado.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Registrar novamente no namespace Microsoft.AAD usando o portal do Azure
Siga estas etapas se uma entidade de serviço com a ID ```443155a6-77f3-45e3-882b-22b3a8d431fb``` ou ```abba844e-bc0e-44b0-947a-dc74e5d09022``` está ausente do seu diretório do Azure AD.

**Correção:** Use as etapas a seguir para restaurar os Domain Services em seu diretório:

1. Navegue até a página [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.
2. Escolha a assinatura da tabela que está associada ao seu domínio gerenciado
3. Usando o painel de navegação esquerdo, escolha **Provedores de Recursos**
4. Pesquise "Microsoft.AAD" na tabela e clique em **Registrar novamente**
5. Para garantir que o alerta seja resolvido, exiba a página de integridade para seu domínio gerenciado em duas horas.


## <a name="service-principals-that-self-correct"></a>Entidades de Serviço com autocorreção
Siga estas etapas se uma entidade de serviço com a ID ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` está ausente do seu diretório do Azure AD.

**Correção:** O Azure AD Domain Services pode detectar quando essa entidade de serviço específica está ausente, configurada incorretamente ou excluída. O serviço automaticamente recria essa entidade de serviço. Verifique a integridade do seu domínio gerenciado após duas horas para garantir que a entidade de serviço tenha sido recriada.


## <a name="contact-us"></a>Entre em contato
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](active-directory-ds-contact-us.md).
