---
title: Início rápido de Adicionar política de nomenclatura para grupos do Office 365 – Azure Active Directory | Microsoft Docs
description: Explica como adicionar novos usuários no Azure Active Directory ou excluir usuários existentes dele
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f2219e038d3432807c81246256873a1ecb2cd9b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093466"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Início Rápido: política de nomenclatura para grupos no Azure Active Directory

Neste início rápido, você configurará a política de nomenclatura em seu locatário do Azure AD (Azure Active Directory) para grupos do Office 365 criados pelo usuário, para ajudar você a classificar e pesquisar os grupos do seu locatário. Por exemplo, você poderia usar a política de nomenclatura para:

* Comunicar a função de um grupo, associação, região geográfica ou quem criou o grupo.
* Ajude a categorizar grupos no catálogo de endereços.
* Bloqueie o uso de palavras específicas em nomes de grupo e alias.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="install-powershell-cmdlets"></a>Instalar cmdlets do PowerShell

Certifique-se de desinstalar qualquer versão anterior do Azure Active Directory PowerShell para o módulo de gráfico para o Windows PowerShell e instale [Azure Active Directory PowerShell para gráfico - versão de visualização pública 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) antes de executar os comandos do PowerShell. 

1. Abra o aplicativo Windows PowerShell como um administrador.
2. Desinstale qualquer versão anterior do AzureADPreview.
  
   ```
   Uninstall-Module AzureADPreview
   ```
3. Instale a versão mais recente do AzureADPreview.
  
   ```
   Install-Module AzureADPreview
   ```
   Se você for solicitado sobre como acessar um repositório não confiável, digite **Y**. Pode levar alguns minutos para que o novo módulo instalar.

## <a name="set-up-naming-policy"></a>Configurar política de nomenclatura

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>Etapa 1: entrar usando cmdlets do PowerShell

1. Abra o aplicativo do Windows PowerShell. Você não precisa de privilégios elevados.

2. Execute os comandos a seguir para preparar para executar os cmdlets.
  
   ```
   Import-Module AzureADPreview
   Connect-AzureAD
   ```
   Na tela **Entrar na sua conta** que abre, digite sua conta de administrador e senha para conectar-se ao serviço e selecione **Entrar**.

3. Siga as etapas em [cmdlets do Azure Active Directory para definir as configurações de grupo](groups-settings-cmdlets.md) para criar configurações de grupo para este locatário.

### <a name="step-2-view-the-current-settings"></a>Etapa 2: Exiba as configurações atuais

1. Exiba as configurações de política de nomenclatura atuais.
  
   ```
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
2. Exiba as configurações do grupo atual.
  
   ```
   $Setting.Values
   ```
  
### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>Etapa 3: definir a política de nomenclatura e quaisquer palavras bloqueadas personalizadas

1. Defina os prefixos e sufixos de nome de grupo no Azure AD PowerShell. Para o recurso funcionar corretamente, [GroupName] deve ser incluído na configuração.
  
   ```
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
2. Defina as palavras bloqueadas personalizadas que você deseja restringir. O exemplo a seguir ilustra como você pode adicionar suas próprias palavras personalizadas.
  
   ```
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
3. Salve as configurações para a nova política ser efetivada, como no exemplo a seguir.
  
   ```
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
É isso. Você definiu sua política de nomenclatura e adicionou suas palavras bloqueadas personalizadas.

## <a name="clean-up-resources"></a>Limpar recursos

1. Esvazie os prefixos e sufixos de nome de grupo no Azure AD PowerShell.
  
   ```
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
2. Esvazie as palavras bloqueadas personalizadas.
  
   ```
   $Setting["CustomBlockedWordsList"]=""
   ```
  
3. Salve as configurações.
  
   ```
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a usar cmdlets do PowerShell para definir a política de nomenclatura para seu locatário do Azure AD.

Para saber mais sobre restrições técnicas, adicionar uma lista de palavras bloqueadas personalizadas ou as experiências do usuário final entre aplicativos do Office 365, avance para o próximo artigo para saber mais sobre detalhes da política de nomenclatura.
> [!div class="nextstepaction"]
> [Política de nomenclatura e todos os seus detalhes](groups-naming-policy.md)
