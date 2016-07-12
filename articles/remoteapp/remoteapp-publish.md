<properties
    pageTitle="Publicar um aplicativo no RemoteApp do Azure | Microsoft Azure"
    description="Saiba como publicar aplicativos e recursos no RemoteApp do Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="elizapo" />


# Como publicar um aplicativo no RemoteApp

Depois de criar sua coleção RemoteApp, você precisa publicar os aplicativos ou recursos que você deseja disponibilizar para os usuários. As imagens de modelo fornecidas com sua assinatura tem apenas alguns aplicativos publicados por padrão – para compartilhar os outros aplicativos, é necessário publicá-los.

> [AZURE.NOTE] Você precisa atualizar um aplicativo? Você precisará [atualizar a imagem](remoteapp-update.md) primeiro.

Na guia **Publicação** no portal, clique em **Publicar**. Você pode adicionar um aplicativo da imagem do modelo do menu **Iniciar** ou fornecer o caminho onde o aplicativo está instalado na imagem do modelo. Se você optar por adicionar do menu **Iniciar**, escolha na lista o aplicativo para publicação. Se você optar por fornecer o caminho para o aplicativo, digite um nome para o aplicativo e o caminho para o aplicativo. Use variáveis no caminho - por exemplo, "%systemdrive%" em vez de "c:".

> [AZURE.NOTE] Se quiser adicionar seu aplicativo do menu **Iniciar**, precisará ter *adicionado esse aplicativo ao menu **Iniciar** em sua imagem de modelo.* Caso contrário, o RemoteApp verá somente o que *está* no menu **Iniciar** e você ficará confuso.

>Para ter certeza de que seu aplicativo está no menu **Iniciar**, coloque um arquivo de atalho - **.lnk** - dentro da pasta %systemdrive%\\ProgramData\\Microsoft\\Windows\\Menu Iniciar\\Programas.

> Se você se esqueceu de adicionar o aplicativo ao menu **Iniciar** quando criou o modelo, opte por adicionar o caminho ao aplicativo. (Ou recrie a imagem do seu modelo, mas é muito mais trabalhoso.)


 

<!---HONumber=AcomDC_0629_2016-->