---
title: Configurar o ambiente de desenvolvimento no Mac OS X | Microsoft Docs
description: "Instale o tempo de execução, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir a instalação, você estará pronto para criar aplicativos no Mac OS X."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: d5a04e34a3f46097b3ad1b04ac6b2b845fc946af
ms.openlocfilehash: beb869fd5c1fcc19101b16149244187c37a6d6a3


---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurar seu ambiente de desenvolvimento no Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Você pode criar aplicativos do Service Fabric para serem executados nos clusters do Linux usando o Mac OS X. Este artigo aborda como configurar seu Mac para o desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos
O Service Fabric não é executado nativamente no OS X. Para executar um cluster do Service Fabric local, fornecemos uma máquina de virtual pré-configurada do Ubuntu usando o Vagrant e o VirtualBox. Antes de começar, você precisa do:

* [Vagrant (v1.8.4 ou posterior)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>Criar a VM local
Para criar a VM local que contém um cluster de cinco nós do Service Fabric, faça o seguinte:

1. Clone o repositório **Vagrantfile**
   
    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
2. Navegue até o clone local do repositório

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Opcional) Modifique as configurações padrão da VM

    Por padrão, a VM local é configurada da seguinte maneira:

   * 3 GB de memória alocada
   * Rede de host privada configurada no IP 192.168.50.50, permitindo a passagem do tráfego a partir do host do Mac

     Você pode alterar qualquer uma dessas configurações ou adicionar outras configurações à VM no Vagrantfile. Confira a [Documentação do Vagrant](http://www.vagrantup.com/docs) para obter a lista completa das opções de configuração.
4. Criar a VM

    ```bash
    vagrant up
    ```
  
   Esta etapa baixa a imagem pré-configurada da VM, inicializa-a localmente e, em seguida, configura um cluster local do Service Fabric nela. Você deve esperar que isso leve alguns minutos. Se a configuração for concluída com êxito, você verá uma mensagem na saída indicando que o cluster está sendo inicializado.
   
    ![A configuração do cluster começa após o provisionamento da VM][cluster-setup-script]
    
5. Teste se o cluster foi configurado corretamente navegando até o Service Fabric Explorer em http://192.168.50.50:19080/Explorer (supondo que você manteve o IP da rede privada padrão).

    ![Service Fabric Explorer exibido a partir do Mac de host][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Instalar o plug-in do Service Fabric para o Eclipse Neon (opcional)
O Service Fabric fornece um plug-in para o IDE do Eclipse Neon que pode simplificar o processo de criação e implantação dos serviços Java.

1. No Eclipse, verifique se você tem o Buildship versão 1.0.17 ou posterior instalado. Você pode verificar as versões dos componentes instalados escolhendo **Ajuda > Detalhes da Instalação**. Você pode atualizar o Buildship usando as instruções [aqui][buildship-update].
2. Para instalar o plug-in do Service Fabric, escolha **Ajuda > Instalar Novo Software...**
3. Na caixa de texto "Trabalhar com", digite: http://dl.windowsazure.com/eclipse/servicefabric.
4. Clique em Adicionar.

    ![Plug-in do Eclipse Neon para o Service Fabric][sf-eclipse-plugin-install]
5. Escolha o plug-in do Service Fabric e clique em Avançar.
6. Continue com a instalação e aceite o contrato de licença do usuário final.

## <a name="next-steps"></a>Próximas etapas
<!-- Links -->

* [Criar seu primeiro aplicativo do Service Fabric para Linux](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar um cluster do Service Fabric no portal do Azure](service-fabric-cluster-creation-via-portal.md)
* [Criar um cluster do Service Fabric no Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Entender o modelo de aplicativo do Service Fabric](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship



<!--HONumber=Jan17_HO1-->


