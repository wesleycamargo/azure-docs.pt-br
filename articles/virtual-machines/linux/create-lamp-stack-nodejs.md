---
title: "Implantar a LAMP em uma máquina virtual Linux com a CLI do Azure 1.0 | Microsoft Docs"
description: Saiba como instalar a pilha LAMP em uma VM Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jluk
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: NA
ms.topic: article
ms.date: 2/21/2017
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: feba2fb20d1831e92358ff5d1b4c9589d63d28dc
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-lamp-stack-with-the-azure-cli-10"></a>Implantar a pilha LAMP com a CLI do Azure 1.0
Este artigo explica como implantar um servidor Web Apache, MySQL e PHP (a pilha LAMP) no Azure. Você precisa de uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e da [CLI do Azure](../../cli-install-nodejs.md) que esteja [conectada à sua conta do Azure](../../xplat-cli-connect.md).

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure 1.0] – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

* Implantar LAMP em VM existente

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Passo a passo da implantação da LAMP em uma nova VM
Você pode começar criando um [grupo de recursos](../../azure-resource-manager/resource-group-overview.md) que conterá a nova VM:

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Para criar a própria VM, você pode usar um modelo do Azure Resource Manager já escrito encontrado [aqui no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Você deverá ver uma resposta solicitando mais algumas entradas:

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

Agora você criou uma VM Linux com LAMP já instalada. Se quiser, você poderá verificar a instalação indo até [Verificar se a LAMP foi instalada com êxito](#verify-lamp-successfully-installed).

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Passo a passo da implantação da LAMP em uma VM existente
Se você precisar de ajuda para criar uma VM Linux, clique [aqui para saber como criá-la](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Em seguida, você precisará usar o SSH na VM Linux. Se você precisar de ajuda para criar uma chave SSH, clique [aqui para saber como criá-la no Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Se você já tiver uma chave SSH, prossiga e use o SSH na linha de comando da sua VM Linux com `ssh exampleUsername@exampleDNS`.

Agora que você está trabalhando na VM Linux, podemos detalhar a instalação da pilha LAMP em distribuições baseadas em Debian. Os comandos exatos podem ser diferentes para outras distribuições de Linux.

#### <a name="installing-on-debianubuntu"></a>Instalação no Debian/Ubuntu
Você precisa dos seguintes pacotes instalados: `apache2`, `mysql-server`, `php5` e `php5-mysql`. Você pode instalá-los pegando esses pacotes diretamente ou usando Tasksel. As instruções para as duas opções estão listadas abaixo.
Antes de instalar, você precisa baixar e atualizar as listas de pacotes.

    user@ubuntu$ sudo apt-get update

##### <a name="individual-packages"></a>Pacotes individuais
Como usar apt-get:

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Usando o Tasksel
Como alternativa, você pode baixar o Tasksel, uma ferramenta do Debian/Ubuntu que instala vários pacotes relacionados como uma “tarefa” coordenada em seu sistema.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Após executar uma das opções anteriores, você receberá uma solicitação para instalar esses pacotes e várias outras dependências. Pressione 'y' e depois 'ENTER' para continuar e siga todas as solicitações para definir uma senha administrativa para MySQL. Essa ação instala as extensões PHP mínimas obrigatórias necessárias para usar o PHP com o MySQL. 

![][1]

Execute o comando a seguir para ver outras extensões PHP que estão disponíveis em pacotes:

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Criar documento info.php
Agora você deve ser capaz de verificar qual versão do Apache, MySQL e PHP você tem por meio da linha de comando digitando `apache2 -v`, `mysql -v` ou `php -v`.

Se você quiser realizar mais testes, crie uma página de informações rápida de PHP para exibição em um navegador. Crie um arquivo com o editor de texto Nano usando este comando:

    user@ubuntu$ sudo nano /var/www/html/info.php

No editor de texto Nano GNU, adicione as seguintes linhas:

    <?php
    phpinfo();
    ?>

Salve e saia do editor de texto.

Reinicie o Apache com este comando para que todas as novas instalações entrem em vigor.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Verifique se a LAMP foi instalada com êxito
Agora é possível verificar a página de informações do PHP que você criou abrindo um navegador e acessando http://youruniqueDNS/info.php. A página deve ser semelhante a esta imagem.

![][2]

Você pode verificar a instalação do Apache exibindo a Página Padrão do Apache2 Ubuntu acessando http://youruniqueDNS/. Você deverá ver algo parecido com esta imagem.

![][3]

Parabéns, você instalou uma pilha LAMP em sua VM do Azure!

## <a name="next-steps"></a>Próximas etapas
Consulte a documentação do Ubuntu sobre a pilha LAMP:

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/deploy-lamp-stack/apachesuccesspage.png
