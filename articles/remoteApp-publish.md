<properties 
    pageTitle="Publicar um aplicativo no RemoteApp" 
    description="Saiba como publicar aplicativos e recursos no RemoteApp." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="2/17/2015" 
    ms.author="elizapo" />


# Como publicar um aplicativo no RemoteApp

Depois de criar sua coleção na nuvem ou híbrida, você precisa publicar os aplicativos ou recursos que você deseja disponibilizar para os usuários. As imagens de modelo fornecidas com sua assinatura tem apenas alguns aplicativos publicados por padrão – para compartilhar os outros aplicativos, é necessário publicá-los.
 
Na guia **Publicação** no portal, clique em **Publicar**. Você pode adicionar um aplicativo da imagem do modelo do menu Iniciar ou fornecer o caminho onde o aplicativo está instalado na imagem do modelo. Se você optar por adicionar a partir do menu Iniciar, escolha o aplicativo para publicar. Se você optar por fornecer o caminho para o aplicativo, digite um nome para o aplicativo e o caminho para o aplicativo. Use variáveis no caminho - por exemplo, "%systemdrive%" em vez de "c:".

**Observação:** se você deseja adicionar o seu aplicativo ao menu Iniciar, você precisa ter *adicionado tal aplicativo ao menu Iniciar em sua imagem de modelo.* Caso contrário, o RemoteApp verá somente o que *está* no menu Iniciar e você ficará confuso. Se você se esqueceu de adicionar o aplicativo ao menu Iniciar quando criou o modelo, opte por adicionar o caminho ao aplicativo. (Ou recrie a imagem do seu modelo, mas é muito mais trabalhoso.)

<!--HONumber=54-->