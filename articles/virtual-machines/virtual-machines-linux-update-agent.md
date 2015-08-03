<properties
	pageTitle="Como atualizar o Agente Linux do Azure para a última versão do Github"
	description="Saiba como atualizar o Agente Linux do Azure por meio do Github para sua VM do Linux no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="mingzhan"/>


# Como atualizar o Agente Linux do Azure para a última versão do Github

Para atualizar seu [Agente Linux do Azure](https://github.com/Azure/WALinuxAgent), você já deve ter:

1. uma vm do Linux em execução no Azure
2. Você está conectado a essa VM do Linux usando o SSH

> [AZURE.NOTE]Se vai executar essa tarefa em um computador Windows, você pode usar Putty para SSH em seu computador Linux. Para obter mais informações, consulte [Como fazer logon em uma Máquina Virtual executando Linux](virtual-machines-linux-how-to-log-on.md).

As distribuições do Linux endossadas pelo Azure colocaram o pacote do Agente Linux do Azure em seus repositórios. Portanto, verifique e instale a última versão por meio do repositório dessa distribuição primeiro, se possível.

Para o Ubuntu, basta digitar:
     
    #sudo apt-get install waagent

e CentOS, digite:

    #sudo yum install waagent

Para o Oracle Linux, verifique se o repositório de complementos está habilitado no arquivo `/etc/yum.repo.d/public-yum-ol6.repo` ou `/etc/yum.repo.d/public-yum-ol7.repo` e digite:

    #sudo yum install WALinuxAgent

Normalmente, isso é tudo de que você precisa. Porém, se por algum motivo for necessário instalá-lo no https://github.com diretamente, use as etapas a seguir.


## Instalar o wget

Faça logon em sua VM usando o SSH.

Instale o wget (existem algumas distribuições que não o instalam por padrão, como Redhat, CentOS e Oracle Linux versões 6.4 e 6.5) digitando `#sudo yum install wget` na linha de comando.


## Baixe a última versão

Abra [a versão do Agente Linux do Azure no Github](https://github.com/Azure/WALinuxAgent/releases) em uma página da Web e localize o número da última versão, como: 2.0.12. (Você pode localizar sua versão atual digitando `#waagent --version`.)

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

A linha a seguir usa a versão 2.0.12 como exemplo:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.12/waagent  

## Torne o waagent executável

    #chmod +x waagent

## Copie o novo executável para /usr/sbin/
    
    #sudo cp waagent /usr/sbin

Para o CoreOS, use:

    #sudo cp waagent /usr/share/oem/bin/
 
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



 

<!---HONumber=July15_HO4-->