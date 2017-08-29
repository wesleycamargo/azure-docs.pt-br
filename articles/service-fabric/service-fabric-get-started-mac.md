---
title: Configurar o ambiente de desenvolvimento no Mac OS X para funcionar com o Azure Service Fabric | Microsoft Docs
description: "Instale o tempo de execução, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir a instalação, você estará pronto para criar aplicativos no Mac OS X."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2017
ms.author: saysa
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 8b4fc0ab9034263418cac42ced203035e0a8fcad
ms.contentlocale: pt-br
ms.lasthandoff: 08/23/2017

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
> Você precisa usar versões com suporte mútuo do Vagrant e do VirtualBox. O Vagrant pode se comportar incorretamente em uma versão sem suporte do VirtualBox.
>

## <a name="create-the-local-vm"></a>Criar a VM local
Para criar a VM local que contém um cluster de cinco nós do Service Fabric, faça o seguinte:

1. Clone o repositório `Vagrantfile`

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    Essas etapas trazem o arquivo `Vagrantfile` que contém a configuração da VM juntamente com o local de onde a VM é baixada.

2. Navegue até o clone local do repositório

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Opcional) Modifique as configurações padrão da VM

    Por padrão, a VM local é configurada da seguinte maneira:

   * 3 GB de memória alocada
   * Rede de host privada configurada no IP 192.168.50.50, permitindo a passagem do tráfego a partir do host do Mac

     Você pode alterar qualquer uma dessas configurações ou adicionar outras configurações à VM no `Vagrantfile`. Confira a [Documentação do Vagrant](http://www.vagrantup.com/docs) para obter a lista completa das opções de configuração.
4. Criar a VM

    ```bash
    vagrant up
    ```

   Esta etapa baixa a imagem pré-configurada da VM, inicializa-a localmente e, em seguida, configura um cluster local do Service Fabric nela. Você deve esperar que isso leve alguns minutos. Se a configuração for concluída com êxito, você verá uma mensagem na saída indicando que o cluster está sendo inicializado.

    ![A configuração do cluster começa após o provisionamento da VM][cluster-setup-script]

    >[!TIP]
    > Se o download da VM está demorando muito, você pode baixá-la usando wget ou curl, ou, em um navegador, acessando o link especificado por **config.vm.box_url** no arquivo `Vagrantfile`. Depois de baixá-lo localmente, edite `Vagrantfile` para apontar para o caminho local onde você baixou a imagem. Por exemplo se você baixou a imagem /home/users/test/azureservicefabric.tp8.box, em seguida, defina **config.vm.box_url** para esse caminho.
    >

5. Teste se o cluster foi configurado corretamente navegando até o Service Fabric Explorer em http://192.168.50.50:19080/Explorer (supondo que você manteve o IP da rede privada padrão).

    ![Service Fabric Explorer exibido a partir do Mac de host][sfx-mac]


## <a name="create-application-on-mac-using-yeoman"></a>Criar um aplicativo Mac usando Yeoman
O Service Fabric fornece ferramentas de scaffolding que ajudarão a criar um aplicativo do Service Fabric no terminal usando gerador de modelos Yeoman. Execute as etapas abaixo para garantir que você tenha o gerador de modelos yeoman do Service Fabric funcionando em seu computador.

1. Node.js e NPM precisam estar instalados em seu mac. Se não for possível instalar o Node.js e o NPM usando Homebrew, faça o seguinte. Para verificar as versões do Node.js e do NPM instaladas em seu Mac, use a opção ``-v``.

  ```bash
  brew install node
  node -v
  npm -v
  ```
2. Instalar o gerador de modelos [Yeoman](http://yeoman.io/) em seu computador a partir do NPM

  ```bash
  npm install -g yo
  ```
3. Instale o gerador do Yeoman que você deseja usar, executando as etapas na [documentação](service-fabric-get-started-linux.md) do guia de introdução. Para criar Aplicativos do Service Fabric usando Yeoman, execute as etapas -

  ```bash
  npm install -g generator-azuresfjava       # for Service Fabric Java Applications
  npm install -g generator-azuresfguest      # for Service Fabric Guest executables
  npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
  ```
4. Para compilar um aplicativo Java do Service Fabric no Mac, você precisaria do JDK 1.8 e Gradle instalado no computador.


## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Instalar o plug-in do Service Fabric para o Eclipse Neon

O Service Fabric fornece um plug-in para o **Eclipse Neon para Java IDE** que pode simplificar o processo de criação e implantação dos serviços Java. Você pode seguir as etapas de instalação mencionadas nesta [documentação](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) geral sobre como instalar ou atualizar o plug-in Eclipse do Service Fabric.

>[!TIP]
> Por padrão, oferecemos suporte a IP padrão, conforme mencionado no ``Vagrantfile`` no ``Local.json`` do aplicativo gerado. No caso de você alterá-lo e implantar o Vagrant com um IP diferente, atualize o IP correspondente no ``Local.json`` de seu aplicativo.

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

