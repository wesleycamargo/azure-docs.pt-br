---
title: Depurar um aplicativo Web Java no Azure no IntelliJ | Microsoft Docs
description: Este tutorial mostra como usar o Kit de ferramentas do Azure para IntelliJ para depurar um Aplicativo Web Java no Azure.
services: app-service\web
documentationcenter: java
author: selvasingh
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/20/2016
ms.author: asirveda;robmcm

---
# Depurar um aplicativo Web Java no Azure no IntelliJ
Este tutorial mostra como depurar um aplicativo Web Java em execução no Azure usando o [Kit de ferramentas do Azure para IntelliJ]. Para simplificar, você usará um exemplo básico de JSP (Java Server Page) neste tutorial, mas as etapas seriam semelhantes para um servlet Java, ao depurar no Azure.

Após a conclusão deste tutorial, seu aplicativo será semelhante à ilustração a seguir ao depurá-lo no IntelliJ:

![][01]

## Pré-requisitos
* Um JDK (Java Developer Kit) versão 1.8 ou posterior.
* IntelliJ IDEA Ultimate Edition. Isso pode ser baixado em <https://www.jetbrains.com/idea/download/index.html>.
* Uma distribuição de um servidor Web ou de um servidor de aplicativo baseado em Java, como o Apache Tomcat ou o Jetty.
* Uma assinatura do Azure, que pode ser adquirida de <https://azure.microsoft.com/free/> ou de <http://azure.microsoft.com/pricing/purchase-options/>.
* O Kit de Ferramentas do Azure para IntelliJ. Para saber mais, confira [Instalação do Kit de Ferramentas do Azure para IntelliJ].
* Um Projeto Web Dinâmico criado e implantado no Serviço de Aplicativo do Azure; por exemplo, confira [Criar um aplicativo Web Hello World do Azure no IntelliJ].

## Para depurar um aplicativo Web Java no Azure
Para concluir as etapas desta seção, você pode usar um Projeto Web Dinâmico existente, que você já implantou como um Aplicativo Web Java no Azure. Baixe um [Projeto Web Dinâmico de Exemplo] e execute as etapas em [Criar um aplicativo Web Hello World para o Azure no IntelliJ] a fim de implantá-lo no Azure.

1. Abra o projeto de Aplicativo Web Java que você implantou no Azure no IntelliJ.
2. Clique no menu **Executar** e clique em **Editar Configurações...**.
   
    ![][02]
3. Quando a caixa de diálogo **Configurações de Execução/Depuração** for aberta:
   
   1. Selecione **Aplicativo Web do Azure**.
   2. Clique em **+** para adicionar uma nova configuração.
   3. Forneça um **Nome** para a configuração.
   4. Aceite os valores padrão restantes sugeridos pelo Kit de Ferramentas do Azure e clique em **OK**.
      
       ![][03]
4. Selecione a configuração de depuração do Aplicativo Web do Azure que você acabou de criar no canto superior direito do menu e clique em **Depurar**
   
    ![][04]
5. Quando receber a solicitação para **Habilitar a depuração remota no Aplicativo Web remoto agora?**, clique em **OK**.
6. Quando receber **Seu aplicativo Web agora está pronto para a depuração remota**, clique em **OK**.
   
    ![][05]
7. Selecione a configuração de depuração do Aplicativo Web do Azure que você acabou de criar no canto superior direito do menu e clique em **Depurar**.
8. Um prompt de comando do Windows ou um shell Unix será aberto e preparará a conexão necessária para depuração; você precisa esperar até que a conexão com seu aplicativo Web Java remoto seja bem-sucedida antes de continuar. Se você estiver usando o Windows, será semelhante a ilustração a seguir:
   
    ![][06]
9. Inserir um ponto de interrupção em sua página JSP, em seguida, abra a URL de seu aplicativo Web Java em um navegador:
   
   1. Abra o **Azure Explorer** no IntelliJ.
   2. Navegue até **Aplicativos Web** e até o aplicativo Web Java que você deseja depurar.
   3. Clique com o botão direito do mouse no aplicativo Web e clique em **Abrir no Navegador**.
   4. Agora, o IntelliJ entrará no modo de depuração.

## Próximas etapas
Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure].

Para obter mais informações sobre como criar aplicativos Web do Azure, confira a [Visão geral de Aplicativos Web].

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de ferramentas do Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Instalação do Kit de Ferramentas do Azure para IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Criar um aplicativo Web Hello World do Azure no IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Criar um aplicativo Web Hello World para o Azure no IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Projeto Web Dinâmico de Exemplo]: http://go.microsoft.com/fwlink/?LinkId=817337

[Central de desenvolvimento Java do Azure]: https://azure.microsoft.com/develop/java/
[Visão geral de Aplicativos Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png

<!---HONumber=AcomDC_0921_2016-->