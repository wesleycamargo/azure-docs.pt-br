---
title: Desabilitar registro convergido para o Microsoft Azure Active Directory SSPR e o MFA (visualização pública)
description: Desabilitar a autenticação de vários fatores do Microsoft Azure Active Directory e registro de redefinição de senha de autoatendimento (visualização pública)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 6a51b1a3050b37fdcc822006f9e25d6662c65fb2
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426345"
---
# <a name="disable-azure-ad-converged-registration-public-preview"></a>Desabilitar o registro convergido do Microsoft Azure Active Directory (visualização pública)

Quando um usuário registra seu número de telefone e/ou aplicativo móvel na experiência convergida, nosso serviço marca um conjunto de sinalizadores (StrongAuthenticationMethods) para aqueles métodos naquele usuário. Essa funcionalidade permite que o usuário execute Autenticação de Multifator do Azure (MFA) com esses métodos sempre que a MFA for necessária.

Os métodos que os usuários se registram por meio da nova experiência tem a propriedade StrongAuthenticationMethods definida. Esse comportamento também ocorrerá depois que a visualização pública estiver disponível. Se um administrador permite a visualização, os usuários se registram por meio da nova experiência e, em seguida, o administrador desativa a visualização, os usuários podem inadvertidamente ser registrados para MFA também.

Se um usuário que tiver concluído o registro convergido navegar para a página de registro SSPR atual, em [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup), serão solicitados a executar a MFA antes que possam acessar essa página. Esta etapa é um comportamento esperado do ponto de vista técnico, mas para os usuários que foram registrados anteriormente para SSPR apenas, esta etapa é um novo comportamento. Embora essa etapa extra melhore a postura de segurança do usuário, fornecendo um nível adicional de segurança, os administradores querem reverter seus usuários para eles não sejam capazes de executar a MFA.  

## <a name="how-to-roll-back-users"></a>Como reverter os usuários

Se você, como um administrador deseja redefinir as configurações de MFA do usuário, criamos um script do PowerShell que limpará a propriedade StrongAuthenticationMethods para aplicativo móvel do usuário e/ou o número de telefone. Executar esse script para seus usuários significa que eles precisarão registrar novamente para MFA, se necessário. É recomendável testar a reversão com um ou dois usuários antes da reversão de todos os usuários afetados.

As etapas a seguir ajudarão você a reverter um usuário ou grupo de usuários:

### <a name="pre-requisites"></a>Pré-requisitos

1. Você precisará instalar os módulos adequados do PowerShell do Microsoft Azure Active Directory. Em uma janela do PowerShell, execute estes comandos para instalar os módulos:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Salve a lista de ID/IDs de objeto de usuário afetado em seu computador como um arquivo de texto com uma ID para cada linha. Anote o local do arquivo.
1. Salve o script a seguir em seu computador e anote o local do script:

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

### <a name="rollback"></a>Reversão

Em uma janela do PowerShell, execute o comando a seguir depois de atualizar os locais realçados. Quando solicitado, forneça as credenciais de administrador global do seu locatário. O script produzirá o resultado da operação de atualização de cada usuário.

`<script location> -path <user file location>`

## <a name="disable-preview-experience"></a>Desabilitar a experiência de visualização

Para desabilitar a experiência de visualização para seus usuários, conclua as seguintes etapas:

1. Entre no portal do Azure como administrador global ou administrador de usuários.
2. Navegue até **Active Directory do Azure**, **Configurações do usuário**, **Gerenciar configurações para os recursos de visualização do painel de acesso**.
3. Em **Os usuários podem usar recursos de visualização para registrar e gerenciar informações de segurança**, definir o seletor **Nenhum** e clicar em **Salvar**.

Os usuários não serão solicitados a registrar-se usando a experiência de visualização.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sob re a versão prévia pública do registro convergido para a redefinição de senha de autoatendimento e a Autenticação Multifator do Microsoft Azure](concept-registration-mfa-sspr-converged.md)