<properties
   pageTitle="Atualizar sua coleção do Azure RemoteApp | Microsoft Azure"
   description="Saiba como atualizar a coleção do RemoteApp do Azure"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="03/28/2016"
   ms.author="elizapo"/>

# Criar uma coleção de RemoteApp do Azure

Pelo menos uma vez, inevitavelmente, você precisará atualizar os aplicativos ou a imagem em sua coleção de RemoteApp do Azure. Se você estiver usando uma das imagens incluídas com sua assinatura do RemoteApp do Azure, na coleção de uma nuvem ou híbrida, quaisquer atualizações são manipuladas pelo RemoteApp do Azure, portanto você pode descansar em paz.

No entanto, se você estiver usando uma imagem personalizada (que é criada do zero ou que você criou, modificando uma das nossas imagens), você é responsável pela manutenção da imagem e dos aplicativos.

Então, como você para atualizar sua coleção? É bem simples:

1. Atualize a imagem que você usou em sua coleção. Aplique os patches ou atualizações necessárias e salve-a com um novo nome.
2. [Carregue](remoteapp-uploadimage.md) ou [importe](remoteapp-image-on-azurevm.md) essa imagem no RemoteApp.
3. Agora, na página de coleção, clique em **Atualizar**.
4. Escolha a nova imagem a partir da lista **Imagem de modelo**.
4. Aqui está a parte complicada - você precisa decidir como lidar com quaisquer usuários que estão usando um aplicativo na coleção. Você tem as seguintes opções:
	- **Dar aos usuários 60 minutos após a atualização**. Assim que a atualização estiver concluída, o RemoteApp do Azure exibirá uma mensagem para qualquer usuário ativo informando-os para salvar seu trabalho e fazer logoff e logon novamente. Após 60 minutos, quaisquer usuários ativos que não tiverem feito logoff serão automaticamente desconectados. Os usuários podem fazer logon de novo imediatamente.
	- **Desconectar os usuários imediatamente**. Assim que a atualização estiver concluída, faça logoff de todos os usuários automaticamente sem qualquer aviso. Se você escolher essa opção, os usuários poderão perder dados. No entanto, eles podem se reconectar ao aplicativo imediatamente.

1. Clique na marca de seleção para iniciar a atualização.

<!---HONumber=AcomDC_0330_2016-->