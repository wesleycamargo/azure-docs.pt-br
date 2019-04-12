---
title: Solucionar problemas de registro combinado para a autenticação multifator (visualização) – Azure Active Directory e o Azure AD SSPR
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
ms.openlocfilehash: 40918493071fe0dd694c43e2b087a2bf7eb197d8
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489186"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Solução de problemas combinado de registro de informações de segurança (visualização)

As informações neste artigo destina-se para orientar os administradores que estão Solucionando problemas relatados por usuários da experiência de registro combinado.

|     |
| --- |
| O registro de informações de segurança combinada para autenticação multifator do Azure e redefinição de senha de autoatendimento do Azure Active Directory (Azure AD) é um recurso de visualização pública do Azure AD. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Logs de auditoria

Os eventos registrados para o registro combinado estão na categoria de métodos de autenticação no Azure AD logs de auditoria.

![Eventos de registro mostrando interface de logs de auditoria do AD do Azure](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

A tabela a seguir lista todos os eventos de auditoria gerados por registro combinado:

| Atividade | Status | Motivo | DESCRIÇÃO |
| --- | --- | --- | --- |
| O usuário registrou todas as informações de segurança necessárias | Sucesso | O usuário registrou todas as informações de segurança necessárias. | Esse evento ocorre quando um usuário for concluída com êxito o registro.|
| O usuário registrou todas as informações de segurança necessárias | Failure | O usuário cancelou o registro de informações de segurança. | Esse evento ocorre quando um usuário cancela o registro do modo de interrupção.|
| Informações de segurança do usuário registrado | Sucesso | Usuário registrado *método*. | Esse evento ocorre quando um usuário registra um método individual. *Método* podem ser autenticador app, telefone, Email, segurança perguntas, aplicativo senha, telefone alternativo e assim por diante.| 
| Informações de segurança do usuário revisada | Sucesso | Usuário analisado com êxito as informações de segurança. | Esse evento ocorre quando um usuário seleciona **parece bom** na página de revisão de informações de segurança.|
| Informações de segurança do usuário revisada | Failure | O usuário não pôde analisar as informações de segurança. | Esse evento ocorre quando um usuário seleciona **parece bom** sobre as informações de segurança Revise a página, mas algo falhar no back-end.|
| Informações de segurança do usuário excluído | Sucesso | Usuário excluído *método*. | Esse evento ocorre quando um usuário exclui um método individual. *Método* podem ser autenticador app, telefone, Email, segurança perguntas, aplicativo senha, telefone alternativo e assim por diante.|
| Informações de segurança do usuário excluído | Failure | O usuário não pôde excluir *método*. | Esse evento ocorre quando um usuário tenta excluir um método, mas a tentativa falhar por algum motivo. *Método* podem ser autenticador app, telefone, Email, segurança perguntas, aplicativo senha, telefone alternativo e assim por diante.|
| Informações de segurança padrão de usuário alterada | Sucesso | O usuário alterou as informações de segurança padrão para *método*. | Esse evento ocorre quando um usuário altera o método padrão. *Método* podem ser notificação de aplicativo do autenticador, um código do meu aplicativo authenticator ou token, ligue para + X XXXXXXXXXX, texto, um código para + X XXXXXXXXX e assim por diante.|
| Informações de segurança padrão de usuário alterada | Failure | Usuário não pôde alterar as informações de segurança padrão para *método*. | Esse evento ocorre quando um usuário tenta alterar o método padrão, mas a tentativa falhar por algum motivo. *Método* podem ser notificação de aplicativo do autenticador, um código do meu aplicativo authenticator ou token, ligue para + X XXXXXXXXXX, texto, um código para + X XXXXXXXXX e assim por diante.|

## <a name="troubleshooting-interrupt-mode"></a>Solução de problemas de modo de interrupção

| Sintoma | Etapas para solucionar problemas |
| --- | --- |
| Os métodos que eu esperava ver não estou vendo. | 1. Verifique se o usuário tem uma função de administrador do AD do Azure. Em caso afirmativo, exiba as diferenças de política do administrador SSPR. <br> 2. Determine se o usuário está sendo interrompido devido a imposição de registro de autenticação multifator ou imposição de registro do SSPR. Consulte a [fluxograma](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) em "Modos de registro combinado" para determinar quais métodos devem ser mostrados. <br> 3. Determine como recentemente a política de autenticação multifator ou SSPR foi alterada. Se a alteração foi recente, ele pode levar algum tempo para que a política atualizada propagar.|

## <a name="troubleshooting-manage-mode"></a>Gerenciar o modo de solução de problemas

| Sintoma | Etapas para solucionar problemas |
| --- | --- |
| Não tenho a opção de adicionar um método específico. | 1. Determine se o método está habilitado para a autenticação multifator ou para SSPR. <br> 2. Se o método estiver habilitado, salvar as políticas novamente e aguarde 1 a 2 horas antes de testar novamente. <br> 3. Se o método estiver habilitado, certifique-se de que o usuário já não tiver configurado o número máximo do método que eles têm permissão para configurar.|

## <a name="disable-combined-registration"></a>Desabilitar registro combinado

Quando um usuário registra um número de telefone e/ou aplicativo móvel no novo combinados experiência, carimbos de nosso serviço em um conjunto de sinalizadores (StrongAuthenticationMethods) para esses métodos em que o usuário. Essa funcionalidade permite que o usuário execute a autenticação multifator com esses métodos sempre que a autenticação multifator é necessária.

Se um administrador permite a visualização, os usuários se registrar por meio da nova experiência e, em seguida, o administrador desativa a visualização, os usuários podem inadvertidamente ser registrados para a autenticação multifator também.

Se um usuário que tiver concluído o registro combinado vai para a página de registro SSPR (redefinição) de senha de autoatendimento atual no [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), o usuário será solicitado a realizar a autenticação multifator antes de poderem acessar nessa página. Esta etapa é esperada do ponto de vista técnico, mas é novo para os usuários que foram registrados anteriormente para SSPR apenas. Embora essa etapa extra melhorar a postura de segurança do usuário, fornecendo outro nível de segurança, os administradores talvez queira reverter seus usuários para que eles não são capazes de realizar a autenticação multifator.  

### <a name="how-to-roll-back-users"></a>Como reverter os usuários

Se você, como um administrador, deseja redefinir as configurações de autenticação multifator do usuário, você pode usar o script do PowerShell fornecido na próxima seção. O script limpará a propriedade StrongAuthenticationMethods para aplicativo móvel do usuário e/ou o número de telefone. Se você executar esse script para seus usuários, precisará registrar novamente para a autenticação multifator, se necessário. É recomendável a reversão de teste com um ou dois usuários antes da reversão de todos os usuários afetados.

As etapas a seguir ajudará você a reverter um usuário ou grupo de usuários.

#### <a name="prerequisites"></a>Pré-requisitos

1. Instale os módulos do PowerShell do Azure AD apropriados. Em uma janela do PowerShell, execute estes comandos para instalar os módulos:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Salve a lista de IDs de objeto de usuário afetado em seu computador como um arquivo de texto com uma ID para cada linha. Anote o local do arquivo.
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

Em uma janela do PowerShell, execute o seguinte comando, fornecendo os locais de arquivo de script e o usuário. Quando solicitado, forneça as credenciais de administrador global do seu locatário. O script produzirá o resultado da operação de atualização de cada usuário.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Desabilitar a experiência de visualização

Para desabilitar a experiência de visualização para seus usuários, conclua estas etapas:

1. Entre no portal do Azure como um administrador do usuário.
2. Vá para **Azure Active Directory** > **configurações do usuário** > **gerenciar as configurações de recursos de visualização do painel de acesso**.
3. Sob **os usuários podem usar recursos de visualização para registrar e gerenciar informações de segurança**, defina o seletor **None**e, em seguida, selecione **salvar**.

Os usuários não serão solicitados a registrar usando a experiência de visualização.

## <a name="next-steps"></a>Próximos passos

* [Saiba mais sobre a visualização pública do registro combinado para redefinição de senha de autoatendimento e autenticação multifator do Azure](concept-registration-mfa-sspr-combined.md)
