---
title: "Como definir um endereço IP estático e privado no modo clássico usando o CLI| Microsoft Docs"
description: "Noções básicas sobre IPs privados e estáticos (DIPs) e como gerenciá-los no modo clássico usando o CLI"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 17386acf-c708-4103-9b22-ff9bf04b778d
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 70da85679c6e594278803fb073665a6eeab7df34


---
# <a name="how-to-set-a-static-private-ip-address-classic-in-azure-cli"></a>Como definir um endereço IP privado estático (clássico) no CLI do Azure
[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação clássico. Você também pode [gerenciar um endereço IP privado estático no modelo de implantação do Gerenciador de Recursos](virtual-networks-static-private-ip-arm-cli.md).

Os exemplos de comando abaixo do CLI do Azure esperam um ambiente simples já criado. Se você quiser executar os comandos da forma como eles aparecem neste documento, primeiro crie o ambiente de teste descrito em [criar uma vnet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar uma VM
Para criar uma nova VM denominada *DNS01* em um novo serviço de nuvem chamado *TestService* com base no cenário acima, execute estas etapas:

1. Se você nunca usou a CLI do Azure, consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.
2. Execute o comando **azure service create** para criar o serviço de nuvem.
   
        azure service create TestService --location uscentral
   
    Saída esperada:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Execute o comando **azure create vm** para criar a VM. Observe o valor para um endereço IP privado estático. A lista exibida após a saída explicar os parâmetros usados.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Saída esperada:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (ou --location)**. Região do Azure na qual a VM será criada. Para o nosso cenário, *centralus*.
   * **-n (ou --vm-name)**. Nome da VM a ser criada.
   * **-w (ou --virtual-network-name)**. Nome da Rede Virtual na qual a sub-rede será criada. 
   * **-S (ou --static-ip)**. Endereço IP privado estático da VM.
   * **TestService**. Nome do serviço de nuvem no qual a VM será criada.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Imagem usada para criar a VM.
   * **adminuser**. Administrador local da VM do Windows.
   * **AdminP@ssw0rd**. Senha do administrador local da VM do Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como recuperar informações do endereço IP privado estático de uma VM
Para exibir as informações do endereço IP privado estático da VM criada com o script acima, execute o seguinte comando do CLI do Azure e observe o valor de *Network StaticIP*:

    azure vm static-ip show DNS01

Saída esperada:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover o endereço IP privado estático de uma VM
Para remover o endereço IP estático e privado adicionado à VM no script acima, execute o seguinte comando do CLI do Azure:

    azure vm static-ip remove DNS01

Saída esperada:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Como adicionar um IP privado estático a uma VM existente
Para adicionar um IP privado estático à VM criada com o script acima, execute o comando a seguir:

    azure vm static-ip set DNS01 192.168.1.101

Saída esperada:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre endereços [IP públicos reservados](virtual-networks-reserved-public-ip.md) .
* Saiba mais sobre endereços [ILPIP (IP público em nível de instância)](virtual-networks-instance-level-public-ip.md) .
* Consulte as [APIs REST de IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).




<!--HONumber=Nov16_HO3-->


