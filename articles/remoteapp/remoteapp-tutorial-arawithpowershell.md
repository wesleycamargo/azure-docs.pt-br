---
title: Usar os cmdlets do PowerShell com o Azure RemoteApp | Microsoft Docs
description: Saiba como usar os cmdlets do Windows PowerShell com o Azure RemoteApp.
services: remoteapp
documentationcenter: ''
author: guscatalano
manager: mbaldwin
editor: ''

ms.service: remoteapp
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 08/15/2016
ms.author: elizapo

---
# Usar os cmdlets do Windows PowerShell com o Azure RemoteApp
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

 Você pode usar os cmdlets do Azure RemoteApp PowerShell para administrar e manter suas coleções. Use as informações a seguir para começar.

## Obtenha os cmdlets
- - -
Baixe primeiro os cmdlets do Azure PowerShell [aqui](http://go.microsoft.com/?linkid=9811175), os do RemoteApp estão incluídos nele.

Confira a [ajuda do cmdlet do Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx).

## Configurar os cmdlets do Azure para usar sua assinatura
- - -
Siga [este guia](../powershell-install-configure.md) para que possa usar os cmdlets em relação à sua assinatura do Azure.

Você pode usar estas etapas para começar rapidamente:

1. Baixe e instale os [cmdlets do Azure PowerShell](http://go.microsoft.com/?linkid=9811175).
2. Inicie o Microsoft Azure PowerShell.
3. Execute **Add-AzureAccount** para autenticar sua assinatura do Azure. Quando solicitado, insira o mesmo nome de usuário e a senha que você usa para entrar no Portal do Azure.
4. Execute **Get-AzureSubscription** para listar as assinaturas associadas à sua conta de usuário.
5. Execute **Select-AzureSubscription** e especifique o nome da assinatura ou a ID a ser usada no console do PowerShell.

Parabéns, seu console do Azure PowerShell está configurado e pronto para usar. Lembre-se de que você precisará repetir as etapas 2 a 5 sempre que iniciar o console do Azure PowerShell.

## Criar uma coleção na nuvem
- - -
É simples, execute o seguinte comando:

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

O comando acima publica automaticamente os aplicativos do Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio e Word).

A criação de uma coleção pode levar 30 minutos ou mais para ser concluída. Portanto, este comando retorna uma ID de acompanhamento que pode ser usada da seguinte maneira:

    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Após a coleta ser feita, você pode adicionar usuários à coleção com o seguinte comando:

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

E pronto! Esse usuário deve ser capaz de se conectar ao aplicativo usando o cliente do Azure RemoteApp encontrado [aqui](https://www.remoteapp.windowsazure.com/).

## Cmdlets disponíveis
Há muitos outros comandos que temos, a documentação para eles será lançada em breve:

Cmdlets de coleção de RemoteApp básicos:

* New-AzureRemoteAppCollection
* Get-AzureRemoteAppCollection
* Set-AzureRemoteAppCollection
* Update-AzureRemoteAppCollection
* Remove-AzureRemoteAppCollection
* Add-AzureRemoteAppUser
* Get-AzureRemoteAppUser
* Remove-AzureRemoteAppUser
* Get-AzureRemoteAppSession
* Disconnect-AzureRemoteAppSession
* Invoke-AzureRemoteAppSessionLogoff
* Send-AzureRemoteAppSessionMessage
* Get-AzureRemoteAppProgram
* Get-AzureRemoteAppStartMenuProgram
* Publish-AzureRemoteAppProgram
* Unpublish-AzureRemoteAppProgram
* Get-AzureRemoteAppCollectionUsageDetails
* Get-AzureRemoteAppCollectionUsageSummary
* Get-AzureRemoteAppPlan

Cmdlets da rede virtual do RemoteApp:

* New-AzureRemoteAppVNet
* Get-AzureRemoteAppVNet
* Set-AzureRemoteAppVNet
* Remove-AzureRemoteAppVNet
* Get-AzureRemoteAppVpnDevice
* Get-- AzureRemoteAppVpnDeviceConfigScript
* Reset-AzureRemoteAppVpnSharedKey

Cmdlets de imagem do modelo do RemoteApp:

* New-AzureRemoteAppTemplateImage
* Get-AzureRemoteAppTemplateImage
* Rename-AzureRemoteAppTemplateImage
* Remove-AzureRemoteAppTemplateImage

Outros cmdlets do RemoteApp:

* Get-AzureRemoteAppLocation
* Get-AzureRemoteAppWorkspace
* Set-AzureRemoteAppWorkspace
* Get-AzureRemoteAppOperationResult

<!---HONumber=AcomDC_0817_2016-->