---
title: "Gerenciar Máquinas Virtuais usando o Azure Explorer para IntelliJ | Microsoft Docs"
description: "Saiba como gerenciar suas máquinas virtuais do Azure usando o Azure Explorer para IntelliJ."
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
ms.openlocfilehash: 0452267fa11bb09ccebd0bc9032ac269d79451d5
ms.lasthandoff: 04/22/2017


---

# <a name="managing-virtual-machines-using-the-azure-explorer-for-intellij"></a>Gerenciar Máquinas Virtuais usando o Azure Explorer para IntelliJ

O Azure Explorer, que faz parte do Kit de ferramentas do Azure para IntelliJ, fornece aos desenvolvedores de Java com uma solução fácil de usar para gerenciar máquinas virtuais em sua conta do Azure de dentro do IDE IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-intellij-show-azure-explorer](../includes/azure-toolkit-for-intellij-show-azure-explorer.md)]

## <a name="creating-a-virtual-machine-in-intellij"></a>Criar uma máquina virtual no IntelliJ

As etapas a seguir guiarão você pelas etapas para criar uma máquina virtual usando o Azure Explorer.

1. Entre em sua conta do Azure usando as etapas no artigo [Instruções de entrada para o Kit de ferramentas do Azure para IntelliJ].

1. Na janela de ferramentas do **Azure Explorer**, expanda o nó **Azure**, clique com o botão direito do mouse em **Máquinas Virtuais** e, em seguida, clique em **Criar VM**.
   ![Menu Criar VM][CR01]

1. Quando o assistente **Criar uma Nova Máquina Virtual** é exibido, escolha a sua assinatura e, em seguida, clique em **Avançar**.
   ![Assistente Criar uma Nova Máquina Virtual][CR02]

1. Na próxima tela do assistente, especifique as opções a seguir e então clique em **Avançar**: ![Assistente Criar Nova Máquina Virtual][CR03]

   a. **Localização**: especifica a localização em que sua máquina virtual será criada, por exemplo, "westus".

   b. **Imagem Recomendada**: especifica que você escolherá uma imagem de uma lista abreviada de imagens usadas com frequência.

   c. **Imagem Personalizada**: especifica que você escolherá uma imagem personalizada, para a qual será necessário especificar as seguintes opções:

      * **Editor**: especifica o editor que criou a imagem que você usará para criar sua máquina virtual; por exemplo, "Microsoft".

      * **Oferta**: especifica qual oferta da máquina virtual usar do editor selecionado; por exemplo "JDK".

      * **Sku**: especifica a *SKU (unidade de manutenção de estoque)* a usar da oferta selecionada; por exemplo, "JDK_8".

      * **No. de Versão**: especifica qual versão do SKU selecionado usar.

1. Na próxima tela do assistente, especifique as opções a seguir e então clique em **Avançar**: ![Assistente Criar Nova Máquina Virtual][CR04]

   a. **Nome da Máquina Virtual**: especifica o nome para sua nova máquina virtual, que deve começar com uma letra e conter somente letras, números e hifens.

   b. **Tamanho**: especifica o número de núcleos e a memória para alocar para sua máquina virtual.

   c. **Nome de usuário**: especifica a conta de administrador a criar para gerenciar sua máquina virtual.

   d. **Senha** e **Confirmar**: especifica a senha para sua conta de administrador.

1. Na última tela do assistente, especifique as seguintes opções e, em seguida, clique em **Concluir**: ![Assistente Criar Nova Máquina Virtual][CR07]

   a. **Grupo de Recursos**: especifica o grupo de recursos para sua máquina virtual; você precisa escolher uma das seguintes opções:
      * **Criar Novo**: especifica que você deseja criar um novo grupo de recursos.
      * **Usar Existente**: especifica que você escolherá entre uma lista de grupos de recursos associados à sua conta do Azure.

   b. **Conta de armazenamento**: especifica a conta de armazenamento a usar para armazenar a máquina virtual; você pode escolher uma conta de armazenamento existente ou criar uma nova conta. Se você escolher **&lt;&lt;Criar Nova&gt;&gt;**, a caixa de diálogo a seguir será exibida:

      ![Caixa de diálogo Criar Nova Conta de Armazenamento][CR05]

   c. **Rede Virtual** e **Sub-rede**: especifica a rede virtual e a sub-rede às quais sua máquina virtual será conectada; você pode escolher uma rede e sub-rede existentes para usar na máquina virtual ou você pode criar uma nova rede e sub-rede. Se você escolher **&lt;&lt;Criar Nova&gt;&gt;**, a caixa de diálogo a seguir será exibida:

      ![Caixa de diálogo Criar Nova Rede Virtual][CR06]

   d. **Endereço IP público**: especifica um endereço IP voltado para o exterior para sua máquina virtual; você poderá optar por criar um novo endereço IP ou escolher **(Nenhum)** se sua máquina virtual não tiver um endereço IP público.

   e. **Grupo de Segurança de Rede**: especifica um firewall de rede opcional que sua máquina virtual usará; você poderá escolher um firewall existente ou escolher **(Nenhum)** se sua máquina virtual não usar um firewall de rede.

   f. **Conjunto de disponibilidade**: especifica um conjunto de disponibilidade opcional ao qual sua máquina virtual pode pertencer; você pode escolher um conjunto de disponibilidade existente, criar um novo conjunto de disponibilidade ou escolher **(Nenhum)** se sua máquina virtual não pertence a um conjunto de disponibilidade.

1. Ao concluir as etapas acima, sua nova máquina virtual será exibida na janela de ferramentas do Azure Explorer.
   ![Nova Máquina Virtual][CR08]

## <a name="restarting-a-virtual-machine-in-intellij"></a>Reiniciar uma Máquina Virtual no IntelliJ

Para reiniciar uma máquina virtual usando o Azure Explorer no IntelliJ, use as seguintes etapas:

1. Na janela de ferramentas do **Azure Explorer**, clique com o botão direito do mouse na máquina virtual e escolha **Reiniciar**.
   ![Reiniciar uma Máquina Virtual][RE01]

1. Quando solicitado, clique em **Sim** para reiniciar a máquina virtual.
   ![Reiniciar uma Máquina Virtual][RE02]

## <a name="shutting-down-a-virtual-machine-in-intellij"></a>Desligar uma Máquina Virtual no IntelliJ

Para desligar uma máquina virtual em execução usando o Azure Explorer no IntelliJ, use as seguintes etapas:

1. Na janela de ferramentas do **Azure Explorer**, clique com o botão direito do mouse na máquina virtual e escolha **Desligar**.
   ![Desligar uma Máquina Virtual][SH01]

1. Quando solicitado, clique em **Sim** para desligar a máquina virtual.
   ![Desligar uma Máquina Virtual][SH02]

## <a name="deleting-a-virtual-machine-in-intellij"></a>Excluir uma Máquina Virtual no IntelliJ

Para excluir uma máquina virtual usando o Azure Explorer no IntelliJ, use as seguintes etapas:

1. Na janela de ferramentas do **Azure Explorer**, clique com o botão direito do mouse na máquina virtual e escolha **Excluir**.
   ![Excluir uma Máquina Virtual][DE01]

1. Quando solicitado, clique em **Sim** para excluir a máquina virtual.
   ![Excluir uma Máquina Virtual][DE02]

## <a name="see-also"></a>Consulte também
Para obter mais informações sobre os tamanhos e os preços das máquinas virtuais do Azure, consulte os links a seguir:

* Tamanhos das Máquinas Virtuais do Azure
   * [Tamanhos das máquinas virtuais do Windows no Azure]
   * [Tamanhos das máquinas virtuais do Linux no Azure]
* Preços das Máquinas Virtuais do Azure
   * [Preços de máquinas virtuais do Windows]
   * [Preços de Máquinas Virtuais do Linux]

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

[Tamanhos das máquinas virtuais do Windows no Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Tamanhos das máquinas virtuais do Linux no Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Preços de máquinas virtuais do Windows]: /pricing/details/virtual-machines/windows/
[Preços de Máquinas Virtuais do Linux]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[RE01]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/RE01.png
[RE02]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/RE02.png

[SH01]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/SH01.png
[SH02]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/SH02.png

[DE01]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/DE01.png
[DE02]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/DE02.png

[CR01]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR01.png
[CR02]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR02.png
[CR03]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR03.png
[CR04]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR04.png
[CR05]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR05.png
[CR06]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR06.png
[CR07]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR07.png
[CR08]: ./media/azure-toolkit-for-intellij-managing-virtual-machines-using-azure-explorer/CR08.png

