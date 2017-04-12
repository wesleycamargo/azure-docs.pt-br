---
title: Carregar uma imagem personalizada para o Azure RemoteApp | Microsoft Docs
description: Saiba como criar uma imagem personalizada para o RemoteApp do Azure.
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: 299e0510-1a6b-4fdf-914a-3631b061a360
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: ericor
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: dcf897cfb03316312613a641f1758cd4636d06b7
ms.lasthandoff: 03/31/2017


---
# <a name="upload-a-custom-image-for-azure-remoteapp"></a>Carregar uma imagem personalizada para o RemoteApp do Azure
> [!IMPORTANT]
> O Azure RemoteApp será descontinuado até 31 de agosto de 2017. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Agora que você criou sua imagem de modelo personalizada ou atualizou-a com as alterações, é preciso carregar a imagem para a biblioteca de imagens do Azure RemoteApp. Siga estas etapas.

## <a name="before-you-start"></a>Antes de começar
1. Verifique se a imagem personalizada cumpre os [requisitos de imagem](remoteapp-imagereqs.md) e os [requisitos do aplicativo](remoteapp-appreqs.md).
2. Instale o [módulo PowerShell do Azure](/powershell/azureps-cmdlets-docs).

## <a name="step-by-step-on-how-to-upload-custom-image"></a>Passo a passo sobre como carregar a imagem personalizada
1. Abra o Portal de Gerenciamento do Azure e navegue até a página do RemoteApp.
2. Na guia **Imagens do modelo**, clique em **Carregar** na parte inferior da página.
3. Insira um nome amigável para a imagem e especifique o local da conta de armazenamento. Certifique-se de que o local seja o mesmo que o da sua coleção do RemoteApp ou um local em que você deseja criar uma.
4. Quando solicitado, baixe o script para seu computador local.
5. Copie os parâmetros de comando na caixa de texto para a área de transferência.
6. Abra uma janela elevada do Windows PowerShell.
7. Na janela do Windows PowerShell com privilégios elevados, navegue para o mesmo diretório para o qual você baixou o script.
8. Cole o comando copiado e pressione **Enter**.
   
   O processo de carregamento será iniciado e a duração pode depender de vários fatores, incluindo a velocidade da rede e o tamanho da imagem
9. Se o carregamento falhar devido a uma interrupção da rede ou problemas semelhantes, você sempre poderá retomar o processo de carregamento. Para retomar um carregamento, execute o script novamente usando a mesma linha de comando.

> [!WARNING]
> Nunca modifique o script de upload. Verificações específicas foram implementadas para garantir que a imagem cumpra os requisitos de imagem e os requisitos do aplicativo.
> 
> 

## <a name="common-problems"></a>Problemas comuns
* Certifique-se de usar o Windows PowerShell, não o PowerShell do Azure. Você precisa instalar o módulo PowerShell do Azure, pois determinados módulos são necessários durante o processo de carregamento.
* Nunca altere o script, as validações existem para sua conveniência.
* Se o arquivo vhd for bloqueado durante o carregamento, copie o arquivo ou mova-o para um novo local e tente novamente. Pode haver algum processo do Windows impedindo o carregamento.  


