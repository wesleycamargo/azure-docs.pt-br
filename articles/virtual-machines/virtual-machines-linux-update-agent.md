<properties
	pageTitle="Atualizar o Agente Linux do Azure por meio do GitHub | Microsoft Azure"
	description="Saiba como atualizar o Agente Linux do Azure de sua VM do Linux no Azure para a versão mais recente do GitHub"
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="mingzhan"/>


# Como atualizar o Agente Linux do Azure em uma VM para a versão mais recente do GitHub

Para atualizar seu [agente Linux do Azure](https://github.com/Azure/WALinuxAgent) em uma VM do Linux no Azure, você já deve ter:

1. uma vm do Linux em execução no Azure
2. Você está conectado a essa VM do Linux usando o SSH

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


> [AZURE.NOTE]Se vai executar essa tarefa em um computador Windows, você pode usar Putty para SSH em seu computador Linux. Para obter mais informações, consulte [Como fazer logon em uma Máquina Virtual executando Linux](virtual-machines-linux-how-to-log-on.md).

As distribuições do Linux endossadas pelo Azure colocaram o pacote do Agente Linux do Azure em seus repositórios. Portanto, verifique e instale a última versão por meio do repositório dessa distribuição primeiro, se possível.

Para o Ubuntu, basta digitar:

    #sudo apt-get install walinuxagent

e CentOS, digite:

    #sudo yum install waagent

Para Oracle Linux, certifique-se de que o `Addons`repositório está habilitado. Edite o arquivo `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) e altere a linha `enabled=0` para `enabled=1` em **[ol6\_addons]** ou **[ol7\_addons]** nesse arquivo.

Em seguida, instale a versão mais recente do agente Linux do Azure e digite:

    #sudo yum install WALinuxAgent

Normalmente, isso é tudo de que você precisa. Porém, se por algum motivo for necessário instalá-lo no https://github.com diretamente, use as etapas a seguir.


## Instalar o wget

Faça logon em sua VM usando o SSH.

Instale o wget (existem algumas distribuições que não o instalam por padrão, como Redhat, CentOS e Oracle Linux versões 6.4 e 6.5) digitando `#sudo yum install wget` na linha de comando.


## Baixe a última versão

Abra [a versão do Agente Linux do Azure no GitHub](https://github.com/Azure/WALinuxAgent/releases) em uma página da Web e encontre o número de versão mais recente. (Você pode localizar sua versão atual digitando `#waagent --version`.)

###Para obter a versão 2.0. x, digite:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   A linha a seguir usa a versão 2.0.14 como exemplo:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

###Para obter a versão 2.1. x ou posterior, digite:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   A linha a seguir usa a versão 2.1.0 como exemplo:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

##Instalar o Agente do Linux

###Para obter a versão 2.0.x, use:

 Torne o waagent executável

    #chmod +x waagent

 Copie o novo executável para /usr/sbin/

  Para a maioria dos Linux, use

    #sudo cp waagent /usr/sbin

  Para o CoreOS, use:

    #sudo cp waagent /usr/share/oem/bin/

  Se esta for a nova instalação do agente Linux do Azure, execute abaixo:
 
    #sudo /usr/sbin/waagent -install -verbose

###Para obter a versão 2.1.x, use:

Você precisa instalar o pacote `setuptools` primeiro. Consulte [aqui](https://pypi.python.org/pypi/setuptools). Em seguida, execute abaixo:

    #sudo python setup.py install

## Reinicie o serviço de waagent

Para a maioria das distribuições do Linux:

    #sudo service waagent restart

Para o Ubuntu, use:

    #sudo service walinuxagent restart

Para o CoreOS, use:

    #sudo systemctl restart waagent

## Confirme a versão do Agente Linux do Azure

    #waagent -version

Para o CoreOS, o comando acima pode não funcionar.

Você verá que a versão do Agente Linux foi atualizada para a nova versão.

Para obter mais informações sobre o Agente Linux do Azure, consulte [LEIAME do Agente Linux do Azure](https://github.com/Azure/WALinuxAgent).

<!---HONumber=AcomDC_0121_2016-->