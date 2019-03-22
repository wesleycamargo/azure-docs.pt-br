---
title: Solucionar problemas de registro combinado para SSPR do Azure AD e MFA (visualização) – Azure Active Directory
description: Solucionar problemas de autenticação de multifator do Azure AD e o registro de combinada de redefinição de senha de autoatendimento (visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f799b671e6216b402aa988aa66da4c2cfc44693
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317520"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Solução de problemas combinado de registro de informações de segurança (visualização)

As informações fornecidas neste artigo podem orientar os administradores que estão Solucionando problemas com a experiência de registro combinado relatada por seus usuários finais.

|     |
| --- |
| Registro de informações de segurança combinada para redefinição de senha self-service de autenticação multifator do Azure e o Azure AD é um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="audit-logs"></a>Logs de auditoria

Os eventos registrados para o registro combinado estão sob a categoria de "Métodos de autenticação" no Azure AD logs de auditoria.

![Interface mostrando algumas segurança eventos de registro de informações para um novo usuário no diretório de logs de auditoria do AD do Azure](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

O exemplo a seguir lista todos os eventos de auditoria gerados por registro combinado:

| Atividade | Status | Motivo | Descrição |
| --- | --- | --- | --- |
| O usuário registrou todas as informações de segurança necessárias | Sucesso | O usuário registrou todas as informações de segurança necessárias. | Esse evento ocorre quando um usuário for concluída com êxito o registro.|
| O usuário registrou todas as informações de segurança necessárias | Falha | O usuário cancelou o registro de informações de segurança. | Esse evento ocorre quando um usuário cancela o registro do modo de interrupção.|
| Informações de segurança do usuário registrado | Sucesso | Usuário registrado "método". | Esse evento ocorre quando um usuário registra um método individual. "Método" pode ser um aplicativo de autenticador, telefone, Email, perguntas de segurança, senha de aplicativo, telefone alternativo, etc.| 
| Informações de segurança do usuário revisada | Sucesso | Usuário analisado com êxito as informações de segurança. | Esse evento ocorre quando um usuário clica em "Boa aparência" na página de revisão de informações de segurança.|
| Informações de segurança do usuário revisada | Falha | O usuário não pôde analisar as informações de segurança. | Esse evento ocorre quando um usuário clica em "Parece bem" sobre as informações de segurança Revise a página, mas algo falhar no back-end.|
| Informações de segurança do usuário excluído | Sucesso | Usuário excluído "método". | Esse evento ocorre quando um usuário exclui um método individual. "Método" pode ser um aplicativo de autenticador, telefone, Email, perguntas de segurança, senha de aplicativo, telefone alternativo, etc.|
| Informações de segurança do usuário excluído | Falha | Usuário não pôde excluir o "método". | Esse evento ocorre quando um usuário tenta excluir um método, mas ele falhar por algum motivo. "Método" pode ser um aplicativo de autenticador, telefone, Email, perguntas de segurança, senha de aplicativo, telefone alternativo, etc.|
| Informações de segurança padrão de usuário alterada | Sucesso | O usuário alterou as informações de segurança padrão para o "método". | Esse evento ocorre quando um usuário altera seu método de padrão. "Método" pode ser notificação de aplicativo do autenticador, código do meu aplicativo authenticator ou token, ligue para + X XXXXXXXXXX, texto de um código para + X XXXXXXXXX, etc.|
| Informações de segurança padrão de usuário alterada | Falha | Usuário não pôde alterar informações de segurança padrão para "método". | Esse evento ocorre quando um usuário tenta alterar o método padrão, mas ele falhar por algum motivo. "Método" pode ser a notificação de aplicativo do autenticador, um código do meu aplicativo authenticator ou token, chamada + X XXXXXXXXXX, texto de um código para + X XXXXXXXXX, etc.|

## <a name="troubleshooting-interrupt-mode"></a>Solução de problemas de modo de interrupção

| Sintoma | Etapas de solução de problemas |
| --- | --- |
| Os métodos que eu esperava ver não estou vendo. | 1. Verifique se o usuário tem uma função de administrador do AD do Azure. Em caso afirmativo, examine as diferenças de política do administrador SSPR. <br> 2. Determine se o usuário está sendo interrompido devido a imposição de registro MFA ou imposição de registro do SSPR. Examine o fluxograma em modos de registro combinado para determinar quais métodos devem ser mostrados. <br> 3. Determine como recentemente a política de MFA ou SSPR foi alterada. Se a alteração foi recente, ele pode levar algum tempo para que a política atualizada propagar.|

## <a name="troubleshooting-manage-mode"></a>Gerenciar o modo de solução de problemas

| Sintoma | Etapas de solução de problemas |
| --- | --- |
| Não tenho a opção de adicionar um método específico. | 1. Determine se o método está habilitado para MFA ou para SSPR. <br> 2. Se o método estiver habilitado, as políticas de salvá-la novamente e aguarde 1 a 2 horas antes de testar novamente. <br> 3. Se o método estiver habilitado, certifique-se de que o usuário já não tiver configurado o número máximo do método que eles têm permissão para configurar.|

## <a name="disable-combined-registration"></a>Desabilitar registro combinado

Quando um usuário registra seu número de telefone e/ou aplicativo móvel no novo combinados experiência, carimbos de nosso serviço em um conjunto de sinalizadores (StrongAuthenticationMethods) para esses métodos em que o usuário. Essa funcionalidade permite que o usuário execute Autenticação de Multifator do Azure (MFA) com esses métodos sempre que a MFA for necessária.

Os métodos que os usuários se registram por meio da nova experiência tem a propriedade StrongAuthenticationMethods definida. Se um administrador permite a visualização, os usuários se registram por meio da nova experiência e, em seguida, o administrador desativa a visualização, os usuários podem inadvertidamente ser registrados para MFA também.

Se um usuário que foi concluída combinado registro navega para a página de registro SSPR (redefinição) senha de autoatendimento atual, em [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), ele serão solicitados a executar a MFA antes que possam acessar essa página. Esta etapa é um comportamento esperado do ponto de vista técnico, mas para os usuários que foram registrados anteriormente para SSPR apenas, esta etapa é um novo comportamento. Embora essa etapa extra melhore a postura de segurança do usuário, fornecendo um nível adicional de segurança, os administradores querem reverter seus usuários para eles não sejam capazes de executar a MFA.  

### <a name="how-to-roll-back-users"></a>Como reverter os usuários

Se você, como um administrador deseja redefinir as configurações de MFA do usuário, criamos um script do PowerShell que limpará a propriedade StrongAuthenticationMethods para aplicativo móvel do usuário e/ou o número de telefone. Executar esse script para seus usuários significa que eles precisarão registrar novamente para MFA, se necessário. É recomendável testar a reversão com um ou dois usuários antes da reversão de todos os usuários afetados.

As etapas a seguir ajudarão você a reverter um usuário ou grupo de usuários:

#### <a name="prerequisites"></a>Pré-requisitos

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

#### <a name="rollback"></a>Reversão

Em uma janela do PowerShell, execute o comando a seguir depois de atualizar os locais realçados. Quando solicitado, forneça as credenciais de administrador global do seu locatário. O script produzirá o resultado da operação de atualização de cada usuário.

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>Desabilitar a experiência de visualização

Para desabilitar a experiência de visualização para seus usuários, conclua as seguintes etapas:

1. Entre no portal do Azure como administrador global ou administrador de usuários.
2. Navegue até **Active Directory do Azure**, **Configurações do usuário**, **Gerenciar configurações para os recursos de visualização do painel de acesso**.
3. Em **Os usuários podem usar recursos de visualização para registrar e gerenciar informações de segurança**, definir o seletor **Nenhum** e clicar em **Salvar**.

Os usuários não serão solicitados a registrar-se usando a experiência de visualização.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a visualização pública do registro combinado para redefinição de senha de autoatendimento e autenticação multifator do Azure](concept-registration-mfa-sspr-combined.md)
