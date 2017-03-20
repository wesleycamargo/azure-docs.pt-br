---
title: Configurar o ambiente de desenvolvimento no Mac OS X | Microsoft Docs
description: "Instale o tempo de execução, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir a instalação, você estará pronto para criar aplicativos no Mac OS X."
services: service-fabric
documentationcenter: java
author: saysa
manager: raunakp
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: fc73eedae7ec9664da714567f47a543e625cd023
ms.lasthandoff: 03/11/2017


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

>[!NOTE]
>  Você precisa usar versões com suporte mútuo do Vagrant e do VirtualBox. O Vagrant pode se comportar incorretamente em uma versão sem suporte do VirtualBox.
>

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

## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Instalar o plug-in do Service Fabric para o Eclipse Neon

O Service Fabric fornece um plug-in para o **Eclipse Neon para Java IDE** que pode simplificar o processo de criação e implantação dos serviços Java. Você pode seguir as etapas de instalação mencionadas nesta [documentação](service-fabric-get-started-eclipse.md#install-or-update-service-fabric-plugin-on-eclipse-neon) geral sobre como instalar ou atualizar o plug-in Eclipse do Service Fabric.

## <a name="using-service-fabric-eclipse-plugin-on-mac"></a>Uso do plug-in Eclipse do Service Fabric no Mac

Verifique se você passou por todas as etapas mencionadas na [documentação do plug-in Eclipse do Service Fabric](service-fabric-get-started-eclipse.md). As etapas para criar, desenvolver e implantar aplicativos Java do Service Fabric usando o contêiner vagrant-guest em um host do Mac geralmente são iguais às da documentação geral, exceto em alguns pontos que você deve considerar, como mencionado abaixo.-
* Como as bibliotecas do Service Fabric serão exigidas para que seu aplicativo Java do Service Fabric seja criado com êxito, o projeto do eclipse deverá ser criado em um caminho compartilhado. Por padrão, o conteúdo no caminho no seu host onde o ``Vagrantfile`` existe é compartilhado com o caminho do ``/vagrant`` no convidado.
* Para simplificar, se você tiver o ``Vagrantfile`` em um caminho, digamos, ``~/home/john/allprojects/``, precisará criar o seu projeto do Service Fabric ``MyActor`` no local ``~/home/john/allprojects/MyActor`` e o caminho para que seu espaço de trabalho do eclipse seja ``~/home/john/allprojects``.

## <a name="next-steps"></a>Próximas etapas
<!-- Links -->
* [Criar e implantar seu primeiro aplicativo Java de do Service Fabric no Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in do Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Criar um cluster do Service Fabric no portal do Azure](service-fabric-cluster-creation-via-portal.md)
* [Criar um cluster do Service Fabric no Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Entender o modelo de aplicativo do Service Fabric](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

