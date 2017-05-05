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
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 22930bf1d37d7b6039bd02792cdd70ec321d6fa7
ms.lasthandoff: 04/22/2017


---

# <a name="managing-storage-accounts-using-the-azure-explorer-for-intellij"></a>Gerenciar contas de armazenamento usando o Azure Explorer para IntelliJ

O Azure Explorer, que faz parte do Kit de ferramentas do Azure para IntelliJ, fornece aos desenvolvedores de Java com uma solução fácil de usar para gerenciar contas de armazenamento em sua conta do Azure de dentro do IDE IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="creating-a-storage-account-in-intellij"></a>Criar uma conta de armazenamento no IntelliJ

As etapas a seguir guiarão você pelas etapas para criar uma conta de armazenamento usando o Azure Explorer.

1. Entre em sua conta do Azure usando as etapas no artigo [Instruções de entrada para o Kit de ferramentas do Azure para Eclipse].

1. Na janela de ferramentas do **Azure Explorer**, expanda o nó **Azure**, clique com o botão direito do mouse em **Contas de Armazenamento** e, em seguida, clique em **Criar Conta de Armazenamento**.
   ![Menu Criar Conta de Armazenamento][CS01]

1. Quando a caixa de diálogo **Criar Conta de Armazenamento** for exibida, especifique as seguintes opções: ![Caixa de Diálogo Criar Nova Conta de Armazenamento][CS02]

   a. **Nome**: especifica o nome que você deseja usar para a nova conta de armazenamento.

   b. **Tipo de conta**: Especifica o tipo de conta de armazenamento a criar, por exemplo, "Armazenamento de Blobs". (Para obter mais informações, consulte [Sobre as contas de armazenamento do Azure].)

   c. **Desempenho**: especifica que oferta da conta de armazenamento usar do editor selecionado; por exemplo "Premium". (Para obter mais informações, consulte [Metas de desempenho e escalabilidade do Armazenamento do Azure].)

   d. **Replicação**: especifica a replicação para a conta de armazenamento, por exemplo "Zona redundante". (Para obter mais informações, consulte [Replicação do Armazenamento do Azure].)

   e. **Assinatura**: especifica a assinatura do Azure que deseja usar para a nova conta de armazenamento.

   f. **Localização**: especifica a localização em que sua conta de armazenamento será criada, por exemplo, "Oeste dos EUA".

   g. **Grupo de Recursos**: especifica o grupo de recursos para sua máquina virtual; você precisa escolher uma das seguintes opções:
      * **Criar Novo**: especifica que você deseja criar um novo grupo de recursos.
      * **Usar Existente**: especifica que você escolherá entre uma lista de grupos de recursos associados à sua conta do Azure.

1. Quando você tiver especificado todas as opções acima, clique em **OK**.

## <a name="creating-a-storage-container-in-intellij"></a>Criar um contêiner de armazenamento no IntelliJ

As etapas a seguir guiarão você pelas etapas para criar um contêiner de armazenamento usando o Azure Explorer.

1. No Azure Explorer, clique com o botão direito do mouse na conta de armazenamento em que deseja criar um contêiner e, em seguida, clique em **Criar contêiner de blob**.
   ![Menu Criar Contêiner de Armazenamento][CC01]

1. Quando a caixa de diálogo **Criar Contêiner de Blob** aparecer, especifique o nome do seu contêiner e, em seguida, clique em **OK**. (Para obter mais informações sobre como nomear contêineres de armazenamento, consulte [Nomenclatura e referência de contêineres, blobs e metadados].) ![Caixa de Diálogo Criar Contêiner de Armazenamento][CC02]

## <a name="deleting-a-storage-container-in-intellij"></a>Excluir um Contêiner de Armazenamento no IntelliJ

Para excluir um contêiner de armazenamento usando o Azure Explorer, use as seguintes etapas:

1. No Azure Explorer, clique com o botão direito do mouse no contêiner de armazenamento e, em seguida, clique em **Excluir**.
   ![Menu Excluir Contêiner de Armazenamento][DC01]

1. Clique em **Sim** quando solicitado para excluir o contêiner de armazenamento.
   ![Caixa de Diálogo Excluir Contêiner de Armazenamento][DC02]

## <a name="deleting-a-storage-account-in-intellij"></a>Excluir uma conta de armazenamento no IntelliJ

Para excluir uma conta de armazenamento usando o Azure Explorer, use as seguintes etapas:

1. Na janela de ferramentas do **Azure Explorer**, clique com o botão direito do mouse na conta de armazenamento e escolha **Excluir**.
   ![Menu Excluir Conta de Armazenamento][DS01]

1. Clique em **Sim** quando solicitado para excluir a conta de armazenamento.
   ![Caixa de Diálogo Excluir Conta de Armazenamento][DS02]

## <a name="see-also"></a>Consulte também
Para obter mais informações sobre os tamanhos, preços e contas de armazenamento do Azure, consulte os links a seguir:

* [Introdução ao Armazenamento do Microsoft Azure]
* [Sobre as contas de armazenamento do Azure]
* Tamanhos de Conta de Armazenamento do Azure
   * [Tamanhos das contas de armazenamento do Windows no Azure]
   * [Tamanhos das contas de armazenamento do Linux no Azure]
* Preços da Conta de Armazenamento do Azure
   * [Preços da Conta de Armazenamento do Windows]
   * [Preços da Conta de Armazenamento do Linux]

Para obter mais informações sobre os kits de ferramentas do Azure para Java IDEs, confira os links a seguir:

* [Kit de ferramentas do Azure para Eclipse]
  * [Novidades no Kit de Ferramentas do Azure para o Eclipse]
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
[Novidades no Kit de Ferramentas do Azure para o Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
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
[Preços da Conta de Armazenamento do Windows]: /pricing/details/virtual-machines/windows/
[Preços da Conta de Armazenamento do Linux]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-intellij-managing-storage-accounts-using-azure-explorer/DC02.png

