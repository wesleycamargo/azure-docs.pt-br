---
title: "Instalação do Kit de Ferramentas do Azure para IntelliJ | Microsoft Docs"
description: Saiba como instalar o Kit de Ferramentas do Azure para o IDEA do IntelliJ.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 349252e6629c0eda4d603399345206903d530578


---
# <a name="installing-the-azure-toolkit-for-intellij"></a>Instalação do Kit de Ferramentas do Azure para IntelliJ
O Kit de Ferramentas do Azure para IntelliJ fornece modelos e funcionalidade que permitem criar, desenvolver, testar e implantar com facilidade aplicativos Azure usando o ambiente de desenvolvimento IDEA do IntelliJ. O Kit de Ferramentas do Azure para IntelliJ é um projeto de código-fonte aberto, cujo código-fonte está disponível de acordo com a Licença do MIT no site do projeto no GitHub na seguinte URL:

<https://github.com/microsoft/azure-tools-for-java>

Há dois métodos de instalação do Kit de Ferramentas do Azure para IntelliJ, na caixa de diálogo de Configurações e no menu Configurar na tela inicial; ambos os métodos de instalação serão demonstrados nas etapas a seguir.

[!INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-settings-dialog-box"></a>Para instalar o Kit de Ferramentas do Azure para IntelliJ na caixa de diálogo de configurações
1. Inicie o IDEA do IntelliJ.
2. Quando o IDEA do IntelliJ é aberto, clique em **Arquivo**, em seguida, clique em **Configurações**.
   
    ![Abra a caixa de diálogo Configurações do IDEA do IntelliJ][01a]
3. Na caixa de diálogo Configurações, clique em **Plug-ins** e, em seguida, clique em **Procurar repositórios**.
   
    ![Caixa de diálogo Configurações do IDEA do IntelliJ][02a]
4. Na caixa de diálogo **Procurar repositórios** , digite "Azure" na caixa de pesquisa. Realce **Kit de Ferramentas do Azure para IntelliJ** e, em seguida, clique em **Instalar**.
   
    ![Procure o Kit de Ferramentas do Azure para IntelliJ][03]
   
    O IDEA do IntelliJ exibirá o progresso da instalação em uma caixa de diálogo.
   
    ![Progresso da instalação][04]
5. Quando a instalação for concluída, clique em **Reiniciar IDEA do IntelliJ**.
   
    ![Reiniciar IDEA do IntelliJ][05]
6. Clique em **OK** para fechar a caixa de diálogo Configurações.
   
    ![Feche a caixa de diálogo Configurações do IDEA do IntelliJ][06]
7. Quando for solicitado a reiniciar o IDEA do IntelliJ ou adiar, clique em **Reiniciar**.
   
    ![Reiniciar IDEA do IntelliJ][07]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-start-screen"></a>Para instalar o Kit de Ferramentas do Azure para IntelliJ a partir da tela inicial
1. Inicie o IDEA do IntelliJ.
2. Quando for exibida a tela inicial do IDEA do IntelliJ, clique em **Configurar**, em seguida, clique em **Plug-ins**.
   
    ![Instale os plug-ins do IDEA do IntelliJ][01b]
3. Na caixa de diálogo **Plug-ins**, clique em **Procurar repositórios**.
   
    ![Procure os repositórios de plug-in do IDEA do IntelliJ][02b]
4. Na caixa de diálogo **Procurar repositórios** , digite "Azure" na caixa de pesquisa. Realce **Kit de Ferramentas do Azure para IntelliJ** e, em seguida, clique em **Instalar**.
   
    ![Procure o Kit de Ferramentas do Azure para IntelliJ][03]
   
    O IDEA do IntelliJ exibirá o progresso da instalação em uma caixa de diálogo.
   
    ![Progresso da instalação][04]
5. Quando a instalação for concluída, clique em **Reiniciar IDEA do IntelliJ**.
   
    ![Reiniciar IDEA do IntelliJ][05]
6. Quando for solicitado a reiniciar o IDEA do IntelliJ ou adiar, clique em **Reiniciar**.
   
    ![Reiniciar IDEA do IntelliJ][07]

## <a name="see-also"></a>Consulte também
Para obter mais informações sobre os kits de ferramentas do Azure para Java IDEs, confira os links a seguir:

* [Kit de ferramentas do Azure para Eclipse]
  * [Instalação do Kit de Ferramentas do Azure para o Eclipse]
  * [Criar um aplicativo Web Hello World para o Azure no Eclipse]
  * [Novidades no Kit de Ferramentas do Azure para o Eclipse]
* [Kit de Ferramentas do Azure para IntelliJ]
  * *Instalação do Kit de Ferramentas do Azure para IntelliJ (Este artigo)*
  * [Criar um aplicativo Web Hello World para o Azure no IntelliJ]
  * [Novidades no Kit de Ferramentas do Azure para IntelliJ]

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure].

<!-- URL List -->

[Kit de ferramentas do Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar um aplicativo Web Hello World para o Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar um aplicativo Web Hello World para o Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalação do Kit de Ferramentas do Azure para o Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novidades no Kit de Ferramentas do Azure para o Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novidades no Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Central de desenvolvimento Java do Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01a]: ./media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: ./media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: ./media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: ./media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: ./media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: ./media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: ./media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: ./media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: ./media/azure-toolkit-for-intellij-installation/07-restart-intellij.png



<!--HONumber=Jan17_HO1-->


