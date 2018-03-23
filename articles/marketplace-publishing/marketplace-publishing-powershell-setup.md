---
title: Configurar o PowerShell para criar uma VM para o Marketplace | Microsoft Docs
description: Instruções para configurar o Azure PowerShell e usar como um fluxo de processo opcional para criar imagens VM para implantar e vender no Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: mbaldwin
ms.openlocfilehash: 72ee1ac612fc4c7191718009a78f55272f0a44cf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurar o Azure PowerShell para criar a oferta para o Azure Marketplace
Para obter informações detalhadas sobre como configurar o PowerShell no Azure, consulte, [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Uma abordagem simples é usar o método de certificado que baixa e importa um certificado necessário para autenticar. Para obter o certificado necessário, use o cmdlet **Get-AzurePublishSettingsFile** . Quando for solicitado, salve o arquivo. Para importar o certificado em uma sessão do PowerShell, use o cmdlet **Import-AzurePublishSettingsFile** .

Para configurar e armazenar as configurações de assinatura do Microsoft Azure comuns para a sessão do PowerShell, use os cmdlets **Set-AzureSubscription** e **Select-AzureSubscription**:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

O primeiro comando associa uma conta de armazenamento padrão com a assinatura (necessária para algumas operações de provisionamento da VM).  O segundo faz a assinatura do ano atual (reconhecido por outros cmdlets).

## <a name="see-also"></a>Consulte também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)
* [Criar uma imagem de máquina virtual para o Marketplace](marketplace-publishing-vm-image-creation.md)

