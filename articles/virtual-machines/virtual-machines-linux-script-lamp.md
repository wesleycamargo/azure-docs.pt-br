<properties
	pageTitle="Implantar um aplicativo Linux usando a extensão de CustomScript do Azure"
	description="Aprenda a usar a extensão CustomScript do Azure para implantar aplicativos em máquinas virtuais Linux."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services="virtual-machines"
	authors="gbowerman"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2015"
	ms.author="guybo"/>

#Implantar um aplicativo LAMP usando a extensão CustomScript do Azure para Linux#

A extensão CustomScript do Azure para Linux fornece uma maneira de personalizar suas máquinas virtuais (VMs) executando código arbitrário escrito em qualquer linguagem de script com suporte a VM (por exemplo, Python, Bash, etc.). Isso fornece uma maneira muito flexível para automatizar a implantação de aplicativos em várias máquinas.

Você pode implantar a extensão CustomScript usando o Portal do Azure, o PowerShell ou a interface de linha de comando do Azure (CLI do Azure).

Neste exemplo, acompanharemos uma implantação de um aplicativo LAMP simples para Ubuntu usando o Azure CLI.

## Pré-requisitos

Para este passo a passo, crie duas VMs do Azure executando o Ubuntu 14.04. Chamaremos eles de *script-vm* e *lamp-vm* aqui. Quando você tentar fazer isso, use nomes exclusivos. Uma será para executar os comandos da CLI e uma para implantar o aplicativo LAMP.

Você também precisa de uma conta de armazenamento do Azure e a chave para acessá-la (você pode obter no portal do Azure).

Se precisar de ajuda para criar VMs do Linux no Azure, confira [Criar uma máquina virtual executando Linux](virtual-machines-linux-tutorial.md).

Embora os comandos de instalação específico assumam o Ubuntu, você poderá adaptar as etapas gerais para qualquer distribuição com suporte.

A VM *script-vm* precisa ter o Azure CLI instalado, com uma conexão ativa com o Azure. Para obter ajuda sobre isso, confira [Instalar e configurar a Interface de linha de comando do Azure](../xplat-cli.md).

## Carregar um script

Neste exemplo, a extensão CustomScript executará um script em uma VM remota para instalar a pilha LAMP e criar uma página PHP. Para acessar o script em qualquer lugar, vou carregá-lo como um blob do Azure.

**O script**

Esse script instala uma pilha LAMP no Ubuntu (incluindo a configuração de uma instalação silenciosa do MySQL), grava um arquivo PHP simples e inicia o Apache:

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
	echo <\?php phpinfo()\; \?> > /var/www/html/phpinfo.php

	# restart Apache
	apachectl restart

**Upload**

Salve o script como um arquivo de texto, por exemplo *lamp_install.sh*, e, em seguida, carregue-o para o armazenamento do Azure. Você pode fazer isso facilmente com o Azure CLI. O exemplo a seguir carrega o arquivo em um contêiner de armazenamento chamado "scripts". Observação: Se o contêiner não existir, você precisará criá-lo primeiro.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Crie também um arquivo JSON que descreve como baixar o script do armazenamento do Azure. Salve isso como *public_config.json* (substituindo "mystorage" com o nome da sua conta de armazenamento):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## Implantação da extensão

Agora estamos prontos para implantar a extensão CustomScript do Linux para a VM remota usando Azure CLI:

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

Isso baixará e executará o script *lamp_install.sh* na VM chamada *lamp-vm*.

Como o aplicativo inclui um servidor da Web, lembre-se de abrir uma porta de escuta HTTP na VM remota:

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## Monitoramento e solução de problemas

Você pode verificar o andamento da execução do script personalizado, observando o arquivo de log na VM remota. SSH para *lamp-vm* e final do arquivo de log:

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

Quando a extensão CustomScript tiver terminado a execução, você poderá navegar até a página PHP criada, que, neste exemplo, seria: *http://lamp-vm.cloudapp.net/phpinfo.php*.

## Recursos adicionais

Você pode usar as mesmas etapas básicas para implantar aplicativos mais complexos. Neste exemplo, o script de instalação foi salvo como um blob público no armazenamento do Azure. Uma opção mais segura seria armazenar o script de instalação como um blob seguro com uma [Assinatura de Acesso Seguro](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Estes são alguns recursos adicionais para o Azure CLI, Linux e a extensão CustomScript:

[Automatizar tarefas de personalização de VM do Linux usando a extensão CustomScript](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensões Linux do Azure (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Computação Linux e Software Livre no Azure](virtual-machines-linux-opensource.md)
 

<!---HONumber=58_postMigration-->