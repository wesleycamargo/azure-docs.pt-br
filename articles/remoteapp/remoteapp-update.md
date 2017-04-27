---
title: "Atualizar sua coleção do Azure RemoteApp | Microsoft Docs"
description: "Saiba como atualizar a coleção do RemoteApp do Azure"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: e553d432-e581-48fe-b996-c432357eb64a
ms.service: remoteapp
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: compute
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 41c8a13ebd008ed4f9d8a5399bf8e272bf0fd7b2
ms.lasthandoff: 03/31/2017


---
# <a name="update-a-collection-in-azure-remoteapp"></a>Criar uma coleção de RemoteApp do Azure
> [!IMPORTANT]
> O Azure RemoteApp será descontinuado até 31 de agosto de 2017. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Pelo menos uma vez, inevitavelmente, você precisará atualizar os aplicativos ou a imagem em sua coleção de RemoteApp do Azure. Se você estiver usando uma das imagens incluídas com sua assinatura do Azure RemoteApp, na coleção de uma nuvem ou híbrida, quaisquer atualizações serão manipuladas pelo Azure RemoteApp, portanto você poderá descansar em paz.

No entanto, se você estiver usando uma imagem personalizada (que é criada do zero ou que você criou, modificando uma das nossas imagens), você é responsável pela manutenção da imagem e dos aplicativos. Se você precisar atualizar sua imagem ou de qualquer aplicativo dentro dele, será necessário criar uma nova versão atualizada da imagem e então substituir a imagem existente em sua coleção pela nova imagem atualizada.

Então, como você para atualizar sua coleção? É bem simples:

1. Atualize a imagem que você usou em sua coleção. Aplique os patches ou atualizações necessárias e salve-a com um novo nome.
2. [Carregue](remoteapp-uploadimage.md) ou [importe](remoteapp-image-on-azurevm.md) essa imagem no RemoteApp.
3. Agora, na página de coleção, clique em **Atualizar**.
4. Escolha a nova imagem a partir da lista **Imagem de modelo** .
5. Aqui está a parte complicada - você precisa decidir como lidar com quaisquer usuários que estão usando um aplicativo na coleção. Você tem as seguintes opções:
   
   * **Dar aos usuários 60 minutos após a atualização**. Assim que a atualização estiver concluída, o RemoteApp do Azure exibirá uma mensagem para qualquer usuário ativo informando-os para salvar seu trabalho e fazer logoff e logon novamente. Após 60 minutos, quaisquer usuários ativos que não tiverem feito logoff serão automaticamente desconectados. Os usuários podem fazer logon de novo imediatamente.
   * **Desconectar os usuários imediatamente**. Assim que a atualização estiver concluída, faça logoff de todos os usuários automaticamente sem qualquer aviso. Se você escolher essa opção, os usuários poderão perder dados. No entanto, eles podem se reconectar ao aplicativo imediatamente.
6. Clique na marca de seleção para iniciar a atualização.


