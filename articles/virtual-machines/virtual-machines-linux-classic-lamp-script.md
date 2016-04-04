<properties
	pageTitle="Usar a extensão de Script Personalizado em uma VM do Linux | Microsoft Azure"
	description="Saiba como usar a extensão CustomScript para implantar aplicativos em Máquinas Virtuais do Linux no Azure criadas com o modelo de implantação clássico."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services="virtual-machines-linux"
	authors="gbowerman"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2015"
	ms.author="guybo"/>

#Implantar um aplicativo LAMP usando a extensão CustomScript do Azure para Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


A Extensão de Script Personalizado do Microsoft Azure para Linux é uma maneira de personalizar VMs (máquinas virtuais) executando código arbitrário escrito em qualquer linguagem de script com suporte da VM (por exemplo, Python e Bash). Isso fornece uma maneira muito flexível para automatizar a implantação de aplicativos em várias máquinas.

Você pode implantar a extensão CustomScript usando o portal clássico do Azure, Windows PowerShell ou a Interface de linha de comando do Azure (CLI do Azure).

Neste artigo, usaremos a CLI do Azure para implantar um aplicativo simples da LAMP em uma VM do Ubuntu criada com o modelo de implantação clássico.

## Pré-requisitos

Para este exemplo, primeiro crie duas VMs do Azure executando o Ubuntu 14.04 ou posterior. As VMs são chamadas *script-vm* e *lamp-vm*. Use nomes exclusivos ao criar as VMs. Uma é usada para executar os comandos da CLI e a outra para implantar o aplicativo LAMP.

Você também precisa de uma conta de armazenamento do Azure e uma chave para acessá-la (você pode obtê-la no portal clássico do Azure).

Se precisar de ajuda para criar VMs do Linux no Azure, confira [Criar uma máquina virtual executando Linux](virtual-machines-linux-cli-create.md).

Os comandos install reconhecem o Ubuntu, mas você pode adaptar a instalação para qualquer distribuição Linux com suporte.

A VM script-vm precisa ter a CLI do Azure instalada e uma conexão ativa com o Azure. Para obter ajuda sobre isso, confira [Instalar e configurar a Interface de linha de comando do Azure](../xplat-cli-install.md).

## Carregar um script

Usaremos a Extensão de Script Personalizado para executar um script em uma VM remota para instalar a pilha LAMP e criar uma página PHP. Para acessar o script em qualquer lugar, vou carregá-lo como um blob do Azure.

### Visão geral do script

O próximo script de exemplo instala uma pilha LAMP no Ubuntu (incluindo a configuração de uma instalação silenciosa do MySQL), grava um arquivo PHP simples e inicia o Apache.

	#!/bin/bash
	# set up a silent install of MySQL
	dbpass="mySQLPassw0rd"

	export DEBIAN_FRONTEND=noninteractive
	echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
	echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

	# install the LAMP stack
	apt-get -y install apache2 mysql-server php5 php5-mysql  

	# write some PHP
	echo <center><h1>My Demo App</h1><br/></center> > /var/www/html/phpinfo.php
	echo <\?php phpinfo()\; \?> >> /var/www/html/phpinfo.php

	# restart Apache
	apachectl restart

### Carregar o script

Salve o script como um arquivo de texto, por exemplo *lamp\_install.sh*, e carregue-o no Armazenamento do Azure. Você pode fazer isso facilmente com o Azure CLI. O exemplo a seguir carrega o arquivo em um contêiner de armazenamento chamado "scripts". Se o contêiner não existir, você precisará criá-lo primeiro.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Crie também um arquivo JSON que descreva como baixar o script do Armazenamento do Azure. Salve isso como *public\_config.json* (substituindo "mystorage" com o nome da sua conta de armazenamento):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## Implantar a extensão

Agora, você pode usar o próximo comando para implantar a Extensão de Script Personalizado do Linux na VM remota usando a CLI do Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

O comando anterior baixa e executa o script *lamp\_install.sh* na VM chamada *lamp-vm*.

Como o aplicativo inclui um servidor Web, lembre-se de abrir uma porta de escuta HTTP na VM remota com o próximo comando.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## Monitoramento e solução de problemas

Você pode verificar a execução do script personalizado analisando o arquivo de log na VM remota. SSH para *lamp-vm* e parte final do arquivo de log com o próximo comando.

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

Depois de executar a Extensão de Script Personalizado, você pode navegar até a página PHP que criou para obter informações. A página PHP para o exemplo deste artigo é **http://lamp-vm.cloudapp.net/phpinfo.php*.

## Recursos adicionais

Você pode usar as mesmas etapas básicas para implantar aplicativos mais complexos. Neste exemplo, o script de instalação foi salvo como um blob público no armazenamento do Azure. Uma opção mais segura seria armazenar o script de instalação como um blob seguro com uma [Assinatura de Acesso Seguro](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Recursos adicionais da CLI do Azure, do Linux e da Extensão de Script Personalizado são listados a seguir.

[Automatizar tarefas de personalização de VM do Linux usando a extensão CustomScript](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensões Linux do Azure (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Computação Linux e Software Livre no Azure](virtual-machines-linux-opensource-links.md)

<!---HONumber=AcomDC_0323_2016-->