---
title: "Implantar LAMP em uma máquina virtual do Linux | Microsoft Docs"
description: Saiba como instalar a pilha LAMP em uma VM Linux
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
ms.date: 06/07/2016
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 29b295b7e061b16e187db59cdf9b777e12d63034


---
# <a name="deploy-lamp-stack-on-azure"></a>Implantar pilha LAMP no Azure
Este artigo explica como implantar um servidor Web Apache, MySQL e PHP (a pilha LAMP) no Azure. Você precisará de uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e a [CLI do Azure](../xplat-cli-install.md) que é [conectada à sua conta do Azure](../xplat-cli-connect.md).

Este artigo mostra dois métodos de instalação da LAMP:

## <a name="quick-command-summary"></a>Resumo rápido do comando
1) Implantar LAMP na nova VM

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Implantar LAMP em VM existente

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Passo a passo da implantação da LAMP em uma nova VM
Você pode começar criando um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) que conterá a VM:

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

Agora você criou uma VM Linux com LAMP já instalada. Se quiser, você pode verificar a instalação indo até [Verificar se a LAMP foi instalada com êxito].

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Passo a passo da implantação de LAMP em VM existente
Se você precisar de ajuda para criar uma VM Linux, clique [aqui para saber como criar uma VM Linux](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Em seguida, você precisará acessar via SSH a VM Linux. Se você precisar de ajuda com a criação de uma chave SSH clique [aqui para saber como criar uma chave SSH no Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Se você já tiver uma chave SSH, prossiga e acesse via SSH em sua VM Linux com `ssh username@uniqueDNS`.

Agora que você está trabalhando em sua VM Linux, vamos detalhar a instalação da pilha LAMP em distribuições baseadas em Debian. Os comandos exatos podem ser diferentes para outras distribuições de Linux.

#### <a name="installing-on-debianubuntu"></a>Instalação no Debian/Ubuntu
Você precisará dos seguintes pacotes instalados: `apache2`, `mysql-server`, `php5` e `php5-mysql`. Você pode instalá-los pegando esses pacotes diretamente ou usando Tasksel. As instruções para as duas opções estão listadas abaixo.
Antes de instalar, você precisará baixar e atualizar as listas de pacote.

    user@ubuntu$ sudo apt-get update

##### <a name="individual-packages"></a>Pacotes individuais
Como usar apt-get:

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Como usar Tasksel
Como alternativa, você pode baixar o Tasksel, uma ferramenta do Debian/Ubuntu que instala vários pacotes relacionados como uma “tarefa” coordenada em seu sistema.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Após executar uma das opções acima, você receberá uma solicitação para instalar esses pacotes e algumas outras dependências. Pressione 'y' e depois 'ENTER' para continuar e siga todas as solicitações para definir uma senha administrativa para MySQL. Serão instaladas as solicitações mínimas das extensões PHP necessárias para se usar o PHP com o MySQL. 

![][1]

Execute o comando a seguir para ver outras extensões PHP que estão disponíveis em pacotes:

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Criar documento info.php
Agora você deve ser capaz de verificar qual versão do Apache, MySQL e PHP você tem por meio da linha de comando digitando `apache2 -v`, `mysql -v` ou `php -v`.

Se você quiser realizar mais testes, crie uma página de informações rápida de PHP para exibição em um navegador. Crie um novo arquivo com o editor de texto Nano usando este comando:

    user@ubuntu$ sudo nano /var/www/html/info.php

No editor de texto Nano GNU, adicione as seguintes linhas:

    <?php
    phpinfo();
    ?>

Salve e saia do editor de texto.

Reinicie o Apache com este comando para que todas as novas instalações tenham efeito.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Verificar se a LAMP foi instalada com êxito
Agora você pode verificar a página de informações de PHP que você criou em seu navegador acessando http://youruniqueDNS/info.php, ela deve ser semelhante a esta.

![][2]

Você pode verificar a instalação do Apache exibindo a Página Padrão do Apache2 Ubuntu acessando http://youruniqueDNS/. Você deverá ver algo assim.

![][3]

Parabéns, você instalou uma pilha LAMP em sua VM do Azure!

## <a name="next-steps"></a>Próximas etapas
Consulte a documentação do Ubuntu sobre a pilha LAMP:

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png



<!--HONumber=Nov16_HO3-->


