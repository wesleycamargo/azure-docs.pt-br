---
title: Gerenciar contas de armazenamento usando o Azure Explorer para IntelliJ | Microsoft Docs
description: Saiba como gerenciar suas contas de armazenamento do Azure usando o Azure Explorer para IntelliJ.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: a1b56cc2751fc43a1ad6917eca77eec460f26694
ms.contentlocale: pt-br
ms.lasthandoff: 05/15/2017


---

# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-intellij"></a>Gerenciar contas de armazenamento usando o Azure Explorer para IntelliJ

O Azure Explorer, que faz parte do Kit de ferramentas do Azure para IntelliJ, fornece aos desenvolvedores de Java com uma solução fácil de usar para gerenciar contas de armazenamento em sua conta do Azure de dentro do IDE (ambiente de desenvolvimento integrado) IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="create-a-storage-account-in-intellij"></a>Criar uma conta de armazenamento no IntelliJ

Para criar uma conta de armazenamento usando o Azure Explorer, faça o seguinte:

1. Entre em sua conta do Azure usando as [Instruções de entrada para o Kit de ferramentas do Azure para Eclipse]. 

2. Na exibição do **Azure Explorer**, expanda o nó **Azure**, clique com o botão direito do mouse em **Contas de Armazenamento** e, em seguida, clique em **Criar Conta de Armazenamento**.

   ![Comando para Criar Conta de Armazenamento][CS01]

3. Na caixa de diálogo **Criar Conta de Armazenamento**, especifique as opções a seguir:

   ![Caixa de diálogo Criar Nova Conta de Armazenamento][CS02]

   * **Nome**: especifica o nome que você deseja usar para a nova conta de armazenamento.

   * **Tipo de conta**: especifica o tipo de conta de armazenamento a criar (por exemplo, "Armazenamento de Blobs"). Para saber mais, confira [Sobre as contas de armazenamento do Azure]. 

   * **Desempenho**: especifica qual oferta de conta de armazenamento usar do editor selecionado (por exemplo "Premium"). Para saber mais, veja [Metas de desempenho e escalabilidade do Armazenamento do Azure]. 

   * **Replicação**: especifica a replicação para a conta de armazenamento (por exemplo "Zona redundante"). Para saber mais, veja [Replicação do Armazenamento do Azure]. 

   * **Assinatura**: especifica a assinatura do Azure que deseja usar para a nova conta de armazenamento.

   * **Localização**: especifica a localização em que sua conta de armazenamento será criada (por exemplo "Oeste dos EUA").

   * **Grupo de Recursos**: especifica o grupo de recursos para suas máquinas virtuais. Selecione uma das seguintes opções:
      * **Criar novo**: especifica que você deseja criar um novo grupo de recursos.
      * **Usar existente**: especifica que você selecionará em uma lista de grupos de recursos associados à sua conta do Azure.

4. Quando você tiver especificado todas as opções anteriores, clique em **OK**.

## <a name="create-a-storage-container-in-intellij"></a>Criar um contêiner de armazenamento no IntelliJ

Para criar um contêiner de armazenamento usando o Azure Explorer, faça o seguinte:

1. Na exibição do Azure Explorer, clique com o botão direito do mouse na conta de armazenamento em que deseja criar um contêiner e, em seguida, clique em **Criar contêiner de blob**.

   ![Comando Criar contêiner de blobs][CC01]

2. Na caixa de diálogo **Criar Contêiner de Blob**, especifique o nome do seu contêiner e, em seguida, clique em **OK**. Para saber mais sobre como nomear contêineres de armazenamento, veja [Nomenclatura e referência de contêineres, blobs e metadados].

   ![Caixa de Diálogo Criar Contêiner de Armazenamento][CC02]

## <a name="delete-a-storage-container-in-intellij"></a>Excluir um contêiner de armazenamento no IntelliJ

Para excluir um contêiner de armazenamento usando o Azure Explorer, faça o seguinte:

1. Na exibição Azure Explorer, clique com o botão direito do mouse no contêiner de armazenamento e, em seguida, clique em **Excluir**.

   ![Comando Excluir contêiner de armazenamento][DC01]

2. Na janela de confirmação, clique em **Sim**.

   ![Janela de confirmação de Excluir contêiner de armazenamento][DC02]

## <a name="delete-a-storage-account-in-intellij"></a>Excluir uma conta de armazenamento no IntelliJ

Para excluir uma conta de armazenamento usando o Azure Explorer, faça o seguinte:

1. Na exibição do **Azure Explorer**, clique com o botão direito do mouse na conta de armazenamento e selecione **Excluir**.

   ![Menu Excluir Conta de Armazenamento][DS01]

2. Na janela de confirmação, clique em **Sim**.

   ![Janela de confirmação de Excluir conta de armazenamento][DS02]

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os tamanhos, preços e contas de armazenamento do Azure, veja os recursos a seguir:

* [Introdução ao Armazenamento do Microsoft Azure]
* [Sobre as contas de armazenamento do Azure]
* Tamanhos de conta de armazenamento do Azure
  * [Tamanhos das contas de armazenamento do Windows no Azure]
  * [Tamanhos das contas de armazenamento do Linux no Azure]
* Preços da conta de armazenamento do Azure
  * [Preços da conta de armazenamento do Windows]
  * [Preços da conta de armazenamento do Linux]

Para saber mais sobre os kits de ferramentas do Azure para Java IDEs, confira os recursos a seguir:

* [Kit de ferramentas do Azure para Eclipse]
  * [Novidades no Kit de Ferramentas do Azure para Eclipse]
  * [Instalação do Kit de Ferramentas do Azure para o Eclipse]
  * [Instruções de entrada para o Kit de ferramentas do Azure para Eclipse]
  * [Criar um aplicativo Web Hello World para o Azure no Eclipse]
* [Kit de Ferramentas do Azure para IntelliJ]
  * [Novidades no Kit de Ferramentas do Azure para IntelliJ]
  * [Instalação do Kit de Ferramentas do Azure para IntelliJ]
  * [Instruções de entrada para o Kit de ferramentas do Azure para IntelliJ]
  * [Criar um aplicativo Web Hello World para o Azure no IntelliJ]

Para saber mais sobre como usar o Azure com Java, confira o [Centro de Desenvolvedores Java do Azure] e as [Ferramentas Java para Visual Studio Team Services].

<!-- URL List -->

[Kit de ferramentas do Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar um aplicativo Web Hello World para o Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar um aplicativo Web Hello World para o Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalação do Kit de Ferramentas do Azure para o Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalação do Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Instruções de entrada para o Kit de ferramentas do Azure para Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Instruções de entrada para o Kit de ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Novidades no Kit de Ferramentas do Azure para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novidades no Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro de Desenvolvedores Java do Azure]: https://azure.microsoft.com/develop/java/
[Ferramentas Java para Visual Studio Team Services]: https://java.visualstudio.com/

[Introdução ao Armazenamento do Microsoft Azure]: /azure/storage/storage-introduction
[Sobre as contas de armazenamento do Azure]: /azure/storage/storage-create-storage-account
[Replicação do Armazenamento do Azure]: /azure/storage/storage-redundancy
[Metas de desempenho e escalabilidade do Armazenamento do Azure]: /azure/storage/storage-scalability-targets
[Nomenclatura e referência de contêineres, blobs e metadados]: http://go.microsoft.com/fwlink/?LinkId=255555

[Tamanhos das contas de armazenamento do Windows no Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Tamanhos das contas de armazenamento do Linux no Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Preços da conta de armazenamento do Windows]: /pricing/details/virtual-machines/windows/
[Preços da conta de armazenamento do Linux]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC02.png

