---
title: "Implantar LAMP em uma máquina virtual do Linux no Azure | Microsoft Docs"
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
ms.openlocfilehash: 174490bec1aea20abf2c2bd465c7aa2c8590e3e2
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-lamp-stack-on-azure"></a>Implantar pilha LAMP no Azure
Este artigo explica como implantar um servidor Web Apache, MySQL e PHP (a pilha LAMP) no Azure. Você precisa de uma conta do Azure ([Obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e da [CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2). Você também pode executar essas etapas com a [CLI do Azure 1.0](create-lamp-stack-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-command-summary"></a>Resumo rápido do comando

1. Salve e edite o [arquivo azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) de acordo com sua preferência em seu computador local.
2. Execute os dois comandos a seguir para criar um grupo de recursos e, em seguida, implantar seu modelo:

```azurecli
az group create -l westus -n myResourceGroup
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

### <a name="deploy-lamp-on-existing-vm"></a>Implantar LAMP em VM existente
Os comandos a seguir atualizam os pacotes e instalam o Apache, o MySQL e o PHP:

```bash
sudo apt-get update
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Passo a passo da implantação da LAMP em uma nova VM

1. Crie um grupo de recursos com [az group create](/cli/azure/group#create) para conter a nova VM:

```azurecli
az group create -l westus -n myResourceGroup
```
Para criar a própria VM, você pode usar um modelo do Azure Resource Manager já escrito encontrado [aqui no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

2. Salve o [arquivo azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) em seu computador local.
3. Edite o **arquivo azuredeploy.parameters.json** em suas entradas preferenciais.
4. Implante o modelo com [az group deployment create] fazendo referência ao arquivo JSON baixado:

```azurecli
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

A saída deverá ser semelhante ao seguinte exemplo:

```json
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Resources/deployments/azuredeploy",
"name": "azuredeploy",
"properties": {
    "correlationId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "debugSetting": null,
}
...
"provisioningState": "Succeeded",
"template": null,
"templateLink": {
    "contentVersion": "1.0.0.0",
    "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json"
    },
    "timestamp": "2017-02-22T00:05:51.860411+00:00"
},
"resourceGroup": "myResourceGroup"
}
```

Agora você criou uma VM Linux com LAMP já instalada. Se quiser, você poderá verificar a instalação indo até [Verificar se a LAMP foi instalada com êxito](#verify-lamp-successfully-installed).

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Passo a passo da implantação da LAMP em uma VM existente
Se você precisar de ajuda para criar uma VM Linux, clique [aqui para saber como criá-la](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-cli). Em seguida, você precisará usar o SSH na VM Linux. Se você precisar de ajuda para criar uma chave SSH, clique [aqui para saber como criá-la no Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Se você já tiver uma chave SSH, prossiga e use o SSH na linha de comando da sua VM Linux com `ssh azureuser@mypublicdns.westus.cloudapp.azure.com`.

Agora que você está trabalhando na VM Linux, podemos detalhar a instalação da pilha LAMP em distribuições baseadas em Debian. Os comandos exatos podem ser diferentes para outras distribuições de Linux.

#### <a name="installing-on-debianubuntu"></a>Instalação no Debian/Ubuntu
Você precisa dos seguintes pacotes instalados: `apache2`, `mysql-server`, `php5` e `php5-mysql`. Você pode instalar esses pacotes ao obtê-los diretamente ou usando o Tasksel.
Antes de instalar, você precisará baixar e atualizar as listas de pacotes.

```bash
sudo apt-get update
```

##### <a name="individual-packages"></a>Pacotes individuais
Como usar apt-get:

```bash
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

##### <a name="using-tasksel"></a>Usando o Tasksel
Como alternativa, você pode baixar o Tasksel, uma ferramenta do Debian/Ubuntu que instala vários pacotes relacionados como uma “tarefa” coordenada em seu sistema.

```bash
sudo apt-get install tasksel
sudo tasksel install lamp-server
```

Após executar uma das opções anteriores, você receberá uma solicitação para instalar esses pacotes e várias outras dependências. Para definir uma senha administrativa para o MySQL, pressione 'y' e depois 'Enter' para continuar e siga todas as solicitações. Esse processo instala as extensões PHP mínimas obrigatórias e necessárias para usar o PHP com o MySQL. 

![][1]

Execute o comando a seguir para ver outras extensões PHP que estão disponíveis em pacotes:

```bash
apt-cache search php5
```

#### <a name="create-infophp-document"></a>Criar documento info.php
Agora você deve ser capaz de verificar qual versão do Apache, MySQL e PHP você tem por meio da linha de comando digitando `apache2 -v`, `mysql -v` ou `php -v`.

Se você quiser realizar mais testes, crie uma página de informações rápida de PHP para exibição em um navegador. Crie um arquivo com o editor de texto Nano usando este comando:

```bash
sudo nano /var/www/html/info.php
```

No editor de texto Nano GNU, adicione as seguintes linhas:

```php
<?php
phpinfo();
?>
```

Salve e saia do editor de texto.

Reinicie o Apache com este comando para que todas as novas instalações entrem em vigor.

```bash
sudo service apache2 restart
```

## <a name="verify-lamp-successfully-installed"></a>Verifique se a LAMP foi instalada com êxito
Agora é possível verificar a página de informações do PHP que você criou abrindo um navegador e acessando http://youruniqueDNS/info.php. A página deve ser semelhante a esta imagem.

![][2]

Você pode verificar a instalação do Apache exibindo a Página Padrão do Apache2 Ubuntu acessando http://youruniqueDNS/. A saída deverá ser semelhante ao seguinte exemplo:

![][3]

Parabéns, você instalou uma pilha LAMP em sua VM do Azure!

## <a name="next-steps"></a>Próximas etapas
Consulte a documentação do Ubuntu sobre a pilha LAMP:

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/deploy-lamp-stack/apachesuccesspage.png

