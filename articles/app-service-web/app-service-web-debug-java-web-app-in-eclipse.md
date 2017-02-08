---
title: Depurar um aplicativo Web Java no Azure no Eclipse | Microsoft Docs
description: Este tutorial mostra como usar o Kit de ferramentas do Azure para Eclipse para depurar um Aplicativo Web Java no Azure.
services: app-service\web
documentationcenter: java
author: selvasingh
manager: wpickett
editor: 
ms.assetid: 321d2d19-9ce0-4165-8555-b6b15e671393
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: asirveda;robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 8a4f22442d030dae602171555bafb3733d61ebd7


---
# <a name="debug-a-java-web-app-on-azure-in-eclipse"></a>Depurar um aplicativo Web Java no Azure no Eclipse
Este tutorial mostra como depurar um aplicativo Web Java em execução no Azure usando o [Kit de ferramentas do Azure para Eclipse]. Para simplificar, você usará um exemplo básico de JSP (Java Server Page) neste tutorial, mas as etapas seriam semelhantes para um servlet Java, ao depurar no Azure.

Após a conclusão deste tutorial, seu aplicativo será semelhante à ilustração a seguir ao depurá-lo no Eclipse:

![][01]

## <a name="prerequisites"></a>Pré-requisitos
* Um JDK (Java Developer Kit) versão 1.8 ou posterior.
* Um IDE do Eclipse para desenvolvedores do Java EE, Indigo ou posterior. Isso pode ser baixado em <http://www.eclipse.org/downloads/>.
* Uma distribuição de um servidor Web ou de um servidor de aplicativo baseado em Java, como o Apache Tomcat ou o Jetty.
* Uma assinatura do Azure, que pode ser adquirida em <https://azure.microsoft.com/en-us/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* O Kit de Ferramentas do Azure para Eclipse. Para saber mais, confira [Instalação do Kit de Ferramentas do Azure para Eclipse].
* Um Projeto Web Dinâmico criado e implantado no Serviço de Aplicativo do Azure; por exemplo, confira [Criar um aplicativo Web Hello World do Azure no Eclipse].

## <a name="to-debug-a-java-web-app-on-azure"></a>Para depurar um aplicativo Web Java no Azure
Para concluir as etapas desta seção, você pode usar um Projeto Web Dinâmico existente, que você já implantou como um Aplicativo Web Java no Azure. Baixe um [Projeto Web Dinâmico de Exemplo] e execute as etapas em [Criar um aplicativo Web Hello World do Azure no Eclipse] a fim de implantá-lo no Azure. 

1. Abra o Eclipse.
2. Configure tempos limites para a depuração remota:
   
   1. Clique no menu **Windows** no Eclipse e clique em **Preferências**.
   2. Expanda o nó **Java** e selecione **Depurar**.
   3. Defina as configurações de **Tempo limite do depurador (ms)** e o **Tempo limite de inicialização (ms)** como `120000`.
      
       ![][02]
   4. Clique em **OK** para fechar a caixa de diálogo **Preferências**.
3. No modo de exibição do Explorador de Projetos do Eclipse, clique com botão direito do mouse no Projeto Web Dinâmico que você implantou no Azure. Quando o menu de contexto for exibido, escolha **Depurar como** e clique em **Aplicativo Web do Azure**.
   
    ![][03]
4. Se esta for a primeira vez que você está depurando seu Projeto Web Dinâmico, a caixa de diálogo **Configurações de Depuração** será aberta; você pode aceitar os valores padrão que são especificados pelo Kit de ferramentas na guia **Conectar**. Na guia **Origem**, clique em **Adicionar**, escolha **Projeto Java**, escolha **Projeto Web Dinâmico** e, em seguida, clique em **OK**. Depois de concluir essas etapas, clique em **Depurar**.
   
    ![][04]
5. Quando receber a solicitação para **Habilitar a depuração remota no Aplicativo Web remoto agora?**, clique em **OK**.
6. Quando receber **Seu aplicativo Web agora está pronto para a depuração remota**, clique em **OK**.
   
    ![][05]
7. Quando a caixa de diálogo **Configurações de Depuração** aparecer novamente, clique em **Depurar**.
8. Um prompt de comando do Windows ou um shell Unix será aberto e preparará a conexão necessária para depuração; você precisa esperar até que a conexão com seu aplicativo Web Java remoto seja bem-sucedida antes de continuar. Se você estiver usando o Windows, será semelhante a ilustração a seguir.
   
    ![][06]
9. Inserir um ponto de interrupção em sua página JSP, em seguida, abra a URL de seu aplicativo Web Java em um navegador:
   
   1. Abra o **Azure Explorer** no Eclipse.
   2. Navegue até **Aplicativos Web** e até o aplicativo Web Java que você deseja depurar.
   3. Clique com o botão direito do mouse no aplicativo Web e clique em **Abrir no Navegador**.
   4. Agora, o Eclipse entrará no modo de depuração.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure].

Para obter mais informações sobre como criar aplicativos Web do Azure, confira a [Visão geral de Aplicativos Web].

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de ferramentas do Azure para Eclipse]: ../azure-toolkit-for-eclipse.md
[Instalação do Kit de Ferramentas do Azure para Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Criar um aplicativo Web Hello World do Azure no Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Projeto Web Dinâmico de Exemplo]: http://go.microsoft.com/fwlink/?LinkId=817337

[Central de desenvolvimento Java do Azure]: https://azure.microsoft.com/develop/java/
[Visão geral de Aplicativos Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png



<!--HONumber=Jan17_HO1-->


