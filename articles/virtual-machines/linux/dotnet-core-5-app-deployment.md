---
title: "Automação da implantação de aplicativos com extensões de máquina virtual | Microsoft Docs"
description: "Tutorial principal de DotNet da máquina virtual do Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 9fc8b1ba-60f5-410b-8190-9f1ff885e50e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f55773b0e523d59bb76117821fa3dde9668f1eb0
ms.contentlocale: pt-br
ms.lasthandoff: 04/03/2017


---
# <a name="application-deployment-with-azure-resource-manager-templates-for-linux-vms"></a>Implantação de aplicativos com modelos do Azure Resource Manager para VMs Linux

Depois que todos os requisitos de infraestrutura do Azure foram identificados e convertidos em um modelo de implantação, a implantação real do aplicativo precisa ser resolvida. Implantação de aplicativo aqui se refere a instalar os binários do aplicativo real nos recursos do Azure. Para o exemplo de Loja de Música, .Net Core, NGINX e Supervisor precisam ser instalados e configurados em cada máquina virtual. Os binários da Loja de Música precisam ser instalados na máquina virtual e o banco de dados da Loja de Música criado previamente.

Este documento detalha como extensões de Máquina Virtual podem automatizar a implantação de aplicativos e configuração de máquinas virtuais do Azure. Todas as dependências e configurações exclusivas são realçadas. Para obter a melhor experiência, pré-implante uma instância da solução em sua assinatura do Azure e trabalhe com o modelo do Azure Resource Manager. O modelo completo pode ser encontrado aqui – [Implantação de Loja de Música no Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="configuration-script"></a>Script de configuração
Extensões de máquina virtual são programas especializados executadas em máquinas virtuais para fornecer automação da configuração. As extensões estão disponíveis para várias finalidades específicas, como antivírus, configuração de registro e configuração do Docker. Uma extensão de script personalizado pode ser usada para executar qualquer script em uma máquina virtual. Com o exemplo de Loja de Música, cabe à extensão de script personalizado configurar máquinas virtuais do Ubuntu e instalar o aplicativo de Loja de Música. 

Antes de detalhar como extensões de máquina virtual são declaradas em um modelo do Azure Resource Manager, examine o script que é executado. Esse script configura a máquina virtual do Ubuntu para hospedar o aplicativo de Loja de Música. Quando executado, o script instala todo software necessário, instala o aplicativo de Loja de Música do controle do código-fonte e prepara o banco de dados. 

Para saber mais sobre como hospedar um aplicativo .Net Core no Linux, consulte [Publicar em um ambiente de produção do Linux](https://docs.asp.net/en/latest/publishing/linuxproduction.html).

> Este exemplo é para fins de demonstração.
> 
> 

```bash
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>Extensão de script da VM
Extensões de VM podem ser executadas em uma máquina virtual no momento do build incluindo o recurso de extensão no modelo do Azure Resource Manager. A extensão pode ser adicionada usando o Assistente para Adicionar Recursos do Visual Studio ou inserindo JSON válido no modelo. O recurso de extensão do script é aninhado dentro do recurso de máquina virtual; isso pode ser visto no exemplo a seguir.

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Extensão de script da VM](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359). 

Observe que no JSON a seguir o script é armazenado no GitHub. Esse script também pode ser armazenado no Armazenamento de Blobs do Azure. Além disso, os modelos do Azure Resource Manager permitem a criação da cadeia de caracteres de execução de script de forma que os valores de parâmetros de modelo podem ser usados como parâmetros para execução de script. Nesse caso, os dados são fornecidos ao implantar os modelos e, em seguida, esses valores podem ser usados ao executar o script.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Para obter mais informações sobre como usar a extensão de script personalizado, consulte [Extensões de script personalizado com modelos do Resource Manager](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-step"></a>Próxima etapa
<hr>

[Explorar mais modelos do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)


