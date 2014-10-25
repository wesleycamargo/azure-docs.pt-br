<properties linkid="manage-linux-howto-custom-create-vm" urlDisplayName="Create a custom VM" pageTitle=" Create a custom virtual machine running Linux in Azure" metaKeywords="Azure custom vm, creating custom vm" description="Learn how to create a custom virtual machine in Azure." metaCanonical="http://www.windowsazure.com/pt-br/manage/windows/how-to-guides/custom-create-a-vm/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Como criar uma máquina virtual personalizada

Uma máquina virtual *personalizada* refere-se a uma máquina virtual que você criou da opção **de Galeria** porque isso lhe dá mais opções de configuração do que a opção **Criação Rápida**. Estão incluídos:

-   Mais opções de imagens para criação da máquina virtual (VM)
-   Conectar a VM a uma rede virtual
-   Instalar o Agente de VM e as extensões, tais como para antimalware
-   Adicionar a VM a um serviço de nuvem existente
-   Adicionar a VM a um grupo de disponibilidade

**Importante**: Se você quiser que sua máquina virtual use uma rede virtual para que possa se conectar às suas máquinas virtuais diretamente pelo nome do host ou estabelecer conexões entre locais, use o método Da Galeria e especifique a rede virtual ao criar a máquina virtual. Uma máquina virtual pode ser configurada para ingressar em uma rede virtual somente quando você criar a máquina virtual. Para mais detalhes sobre redes virtuais, consulte [Visão geral da rede virtual do Azure][].

[WACOM.INCLUDE [virtual-machines-create-WindowsVM][]]

  [Visão geral da rede virtual do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [virtual-machines-create-WindowsVM]: ../includes/virtual-machines-create-WindowsVM.md
