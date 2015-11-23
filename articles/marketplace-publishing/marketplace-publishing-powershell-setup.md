<properties
   pageTitle="Configurar o PowerShell para criar uma VM para o Marketplace | Microsoft Azure"
   description="Instruções para configurar o Azure PowerShell e usar como um fluxo de processo opcional para criar imagens VM para implantar e vender no Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="hascipio"/>

# Configurar o Azure PowerShell para criar uma oferta para o Azure Marketplace
Para obter informações detalhadas sobre como configurar o PowerShell no Azure, visite [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Uma abordagem simples é usar o método de certificado, que baixa e importa um certificado necessário para autenticar. Para obter o certificado necessário, use o cmdlet *Get-AzurePublishSettingsFile*. Ao terminar, salve o arquivo. Para importar o certificado em uma sessão do PowerShell, use o *Import-AzurePublishSettingsFile*.

Para configurar e armazenar as configurações de assinatura do Microsoft Azure comuns para a sessão do PowerShell, use os cmdlets *Set-AzureSubscription* e *Select-AzureSubscription*:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

O primeiro comando associa uma conta de armazenamento padrão com a assinatura (necessária para algumas operações de provisionamento da VM). O segundo faz a assinatura do ano atual (reconhecido por outros cmdlets).

## Consulte também
- [Introdução: como publicar uma oferta para o Azure Marketplace](marketplace-publishing-getting-started.md)
- [Criar uma imagem de máquina virtual para o Marketplace](marketplace-publishing-vm-image-creation.md)

<!---HONumber=Nov15_HO3-->