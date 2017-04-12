---
title: Atualizar o Agente Linux do Azure por meio do GitHub | Microsoft Docs
description: "Saiba como atualizar o Agente Linux do Azure de sua VM Linux no Azure para a versão mais recente do GitHub"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2015
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c9cea69fc6462f69c8627219aca11272bbdc493f
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Como atualizar o Agente Linux do Azure em uma VM para a versão mais recente do GitHub
Para atualizar seu [agente Linux do Azure](https://github.com/Azure/WALinuxAgent) em uma VM do Linux no Azure, você já deve ter:

1. uma VM do Linux em execução no Azure.
2. uma conexão com essa VM do Linux usando o SSH.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [!NOTE]
> Se vai executar essa tarefa em um computador Windows, você pode usar PuTTY para SSH em seu computador Linux. Para obter mais informações, veja [Como fazer logon em uma Máquina Virtual executando Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

As distribuições do Linux endossadas pelo Azure colocaram o pacote do Agente Linux do Azure em seus repositórios. Portanto, verifique e instale a última versão por meio do repositório dessa distribuição primeiro, se possível.  

Para o Ubuntu, basta digitar:

```bash
sudo apt-get install walinuxagent
```

E no CentOS, digite:

```bash
sudo yum install waagent
```

Para Oracle Linux, verifique se o repositório `Addons` está habilitado. Escolha editar o arquivo `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) e altere a linha `enabled=0` para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** nesse arquivo.

Em seguida, para instalar a versão mais recente do agente Linux do Azure e digite:

```bash
sudo yum install WALinuxAgent
```

Caso você não encontre o repositório de complementos, basta adicionar essas linhas no final do arquivo .repo de acordo com sua versão do Oracle Linux:

Para máquinas virtuais do Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Para máquinas virtuais do Oracle Linux 7:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0
```

Em seguida, digite:

```bash
sudo yum update WALinuxAgent
```

Normalmente, isso é tudo de que você precisa. Porém, se por algum motivo for necessário instalá-lo no https://github.com diretamente, use as etapas a seguir.

## <a name="install-wget"></a>Instalar o wget
Faça logon em sua VM usando o SSH.

Instale o wget (existem algumas distribuições que não o instalam por padrão, como Redhat, CentOS e Oracle Linux versões 6.4 e 6.5) digitando `#sudo yum install wget` na linha de comando.

## <a name="download-the-latest-version"></a>Baixar a última versão
Abra [a versão do Agente Linux do Azure no GitHub](https://github.com/Azure/WALinuxAgent/releases) em uma página da Web e encontre o número de versão mais recente. (Você pode localizar sua versão atual digitando `#waagent --version`.)

### <a name="for-version-20x-type"></a>Para a versão 2.0. x, digite:

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent
```

A linha a seguir usa a versão 2.0.14 como exemplo:

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
```

### <a name="for-version-21x-or-later-type"></a>Para a versão 2.1. x ou posterior, digite:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
unzip WALinuxAgent-[version].zip
cd WALinuxAgent-[version]
```

A linha a seguir usa a versão 2.1.0 como exemplo:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
unzip WALinuxAgent-2.1.0.zip  
cd WALinuxAgent-2.1.0
```

## <a name="install-the-azure-linux-agent"></a>Instale o Agente Linux do Azure
### <a name="for-version-20x-use"></a>Para a versão 2.0. x, use:
Torne o waagent executável:

```bash
chmod +x waagent
```

Copie o novo executável para /usr/sbin/.

Para a maioria dos Linux, use:

```bash
sudo cp waagent /usr/sbin
```

Para o CoreOS, use:

```bash
sudo cp waagent /usr/share/oem/bin/
```

Se esta é a nova instalação do agente Linux do Azure, execute:

```bash
sudo /usr/sbin/waagent -install -verbose
```

### <a name="for-version-21x-use"></a>Para a versão 2.1. x, use:
Talvez seja necessário instalar o pacote `setuptools` primeiro, veja [aqui](https://pypi.python.org/pypi/setuptools). Em seguida, execute:

```bash
sudo python setup.py install
```

## <a name="restart-the-waagent-service"></a>Reinicie o serviço do waagent
Para a maioria das distribuições do Linux:

```bash
sudo service waagent restart
```

Para o Ubuntu, use:

```bash
sudo service walinuxagent restart
```

Para o CoreOS, use:

```bash
sudo systemctl restart waagent
```

## <a name="confirm-the-azure-linux-agent-version"></a>Confirme a versão do Agente Linux do Azure
    
```bash
waagent -version
```

Para o CoreOS, o comando acima pode não funcionar.

Você verá que a versão do Agente Linux do Azure foi atualizada para a nova versão.

Para obter mais informações sobre o Agente Linux do Azure, consulte [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent)(LEIAME do Agente Linux do Azure).


