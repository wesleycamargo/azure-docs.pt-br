<properties title="Publish an app in RemoteApp" pageTitle="Publicar um aplicativo no RemoteApp" description="Saiba como publicar aplicativos e recursos no RemoteApp" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#Como publicar um aplicativo no RemoteApp

Depois de criar sua coleção na nuvem ou híbrida, você precisa publicar os aplicativos ou recursos que você deseja disponibilizar para os usuários. As imagens de modelo fornecidas com sua assinatura tem apenas alguns aplicativos publicados por padrão - para compartilhar os outros aplicativos, é necessário publicá-los.
 
Na guia **Publicação** no portal, clique em **Publicar**. Você pode adicionar um aplicativo da imagem do modelo do menu Iniciar ou fornecer o caminho onde o aplicativo está instalado na imagem do modelo. Se você optar por adicionar a partir do menu Iniciar, escolha o aplicativo para publicar. Se você optar por fornecer o caminho para o aplicativo, digite um nome para o aplicativo e o caminho para o aplicativo. Use variáveis no caminho - por exemplo, "% systemdrive %" em vez de "c:\".

**Observação:** se você deseja adicionar a seu aplicativo no menu Iniciar, você precisa ter *adicionado esse aplicativo ao menu Iniciar na sua imagem do modelo.* Caso contrário, o RemoteApp só verá o que *está* no menu Iniciar e você ficará confuso. Se você se esqueceu de adicionar o aplicativo ao menu Iniciar quando você criou o modelo, opte por adicionar o caminho para o aplicativo (ou recriar a imagem de modelo, mas isso dá muito mais trabalho.)

<!--HONumber=35.2-->
