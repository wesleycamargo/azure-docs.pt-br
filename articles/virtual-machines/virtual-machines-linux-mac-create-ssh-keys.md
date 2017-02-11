---
title: "Criar um par de chaves SSH pública e privada para VMs Linux | Microsoft Docs"
description: "Criar um par de chaves SSH pública e privada para VMs Linux."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/12/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 330637f5b69ad95aef149d9fbde16f2151cde837
ms.openlocfilehash: 5c515dbe8e3030abf079e5ff47884fb04b9048ba


---

# <a name="create-an-ssh-public-and-private-key-pair-for-linux-vms"></a>Criar um par de chaves SSH pública e privada para VMs Linux

Este artigo mostra como gerar um par de chaves pública e privada SSH para usar com VMs Linux.  Com um par de chaves SSH, você pode criar máquinas virtuais no Azure que tenham como padrão o uso de chaves SSH para autenticação, eliminando a necessidade de senhas para fazer logon.  As senhas podem ser adivinhadas e podem abrir suas VMs para tentativas de uso contínuo de força bruta para adivinhá-las. As VMs criadas com os Modelos do Azure ou com o `azure-cli` podem incluir sua chave pública SSH como parte da implantação, removendo uma etapa de configuração pós-implantação que consiste na desabilitação de logons com senha para SSH.

## <a name="quick-commands"></a>Comandos rápidos

Execute os comandos a seguir de um shell Bash, substituindo os exemplos por suas próprias escolhas.

As chaves SSH são mantidas por padrão no diretório `.ssh`.  

```bash
cd ~/.ssh/
```

Se você não tiver um diretório `~/.ssh`, o comando `ssh-keygen` o criará para você com as permissões corretas.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
```

Insira o nome do arquivo de chave privada que está salvo no diretório `~/.ssh/`:

```bash
~/.ssh/id_rsa
```

Insira a senha para id_rsa:

```bash
correct horse battery staple
```

Agora há um par de chaves SSH `id_rsa` e `id_rsa.pub` no diretório `~/.ssh`.

```bash
ls -al ~/.ssh
```

Adicione a chave recém-criada em `ssh-agent`:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Copie a chave pública SSH em sua VM Linux:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

Teste o logon usando chaves em vez de uma senha:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa ahmet@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

O SSH é configurado com êxito se você não for solicitado a inserir uma senha de chave privada SSH ou uma senha de logon para a VM.

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

Usar as chaves públicas e privadas do SSH é a maneira mais fácil de fazer logon em servidores Linux. [criptografia de chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) fornece uma maneira muito mais segura de fazer logon na VM BSD ou do Linux no Azure do que as senhas, que podem ser obtidas por força bruta muito mais facilmente.

Sua chave pública pode ser compartilhada com qualquer pessoa; mas apenas você (ou sua infraestrutura de segurança local) possui sua chave privada.  A chave privada SSH deve ter uma [senha bastante segura](https://www.xkcd.com/936/)(fonte:[xkcd.com](https://xkcd.com)) para protegê-la.  Esta senha é apenas para acessar a chave privada do SSH e **não é** a senha da conta de usuário.  Quando você adiciona uma senha à chave SSH, ela criptografa a chave privada para que a chave seja inútil sem a senha para desbloqueá-la.  Se um invasor roubasse sua chave privada e se ela não tivesse uma senha, ele poderia usar essa chave privada para fazer logon em qualquer servidor com a chave pública correspondente.  Se uma chave privada for protegida por senha, ela não poderá ser usada por esse invasor, o que é uma camada adicional de segurança para sua infraestrutura no Azure.

Este artigo cria arquivos de chave com o formato *ssh-rsa* , recomendados para as implantações no Resource Manager.  *ssh-rsa* e necessários no [portal](https://portal.azure.com) para as implantações clássicas e do Resource Manager.

## <a name="disable-ssh-passwords-by-using-ssh-keys"></a>Desabilitar senhas SSH usando chaves SSH

O Azure requer chaves públicas e privadas de pelo menos 2048 bits em formato ssh-rsa. Para criar as chaves, use `ssh-keygen`, que faz uma série de perguntas e, em seguida, grava uma chave privada e uma chave pública correspondente. Quando uma VM do Azure é criada, a chave pública é copiada para `~/.ssh/authorized_keys`.  As chaves SSH no `~/.ssh/authorized_keys` são usadas para desafiar o cliente para coincidir com a chave privada correspondente em uma conexão de logon SSH.  Quando uma VM Linux do Azure é criada usando chaves SSH para autenticação, o Azure configura o servidor SSHD para não permitir logons com senha, apenas com chaves SSH.  Portanto, a criação de VMs Linux do Azure com chaves SSH implanta a VM segura por padrão e evita a etapa típica de configuração pós-implantação de desabilitação de senhas no arquivo de configuração `sshd_config`.

## <a name="using-ssh-keygen"></a>Usando ssh-keygen

Esse comando cria um par de chaves SSH (criptografado) protegido por senha usando o RSA de 2048 bits e é comentado para identificá-lo facilmente.  

Comece alterando diretórios, para que todas as suas chaves ssh sejam criadas nesse diretório.

```bash
cd ~/.ssh
```

Se você não tiver um diretório `~/.ssh`, o comando `ssh-keygen` o criará para você com as permissões corretas.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

*Comando explicado*

`ssh-keygen` = programa usado para criar as chaves

`-t rsa` = tipo de chave para criação, que é o [formato RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = bits da chave

`-C "myusername@myserver"` = um comentário acrescentado ao fim do arquivo de chave pública para identificá-lo facilmente.  Normalmente, um email é usado como o comentário, mas você pode usar o que funcionar melhor para sua infraestrutura.

### <a name="using-pem-keys"></a>Usando chaves PEM

Se você estiver usando o modelo de implantação clássico (Portal Clássico do Azure ou a CLI do Serviço de Gerenciamento do Azure `asm`), talvez seja necessário usar chaves SSH formatadas por PEM para acessar suas VMs do Linux.  Veja como criar uma chave PEM de uma chave Pública SSH existente e de um certificado x509 existente.

Para criar uma chave formatada PEM de uma chave pública SSH existente:

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Exemplo de ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@myserver
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Arquivos de chave salvos:

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): id_rsa`

O nome do par de chaves para este artigo.  Ter um par de chaves denominado **id_rsa** é o padrão e algumas ferramentas podem esperar o nome de arquivo da chave privada **id_rsa**, portanto, ter um é uma boa ideia. O diretório `~/.ssh/` é o local padrão para os pares de chave SSH e o arquivo de configuração SSH.

```bash
ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 rsa.pub
```
Uma listagem do diretório `~/.ssh` . `ssh-keygen`cria o diretório `~/.ssh`, se ele não existir e também definirá os modos de propriedade e arquivo corretos.

Senha da chave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` se refere a uma senha como "senha".  É *altamente* recomendável adicionar uma senha a seus pares de chave. Sem uma senha para proteger o par de chaves, qualquer pessoa com o arquivo da chave privada pode usá-lo para fazer logon em qualquer servidor com a chave pública correspondente. Portanto, adicionar uma senha oferece mais proteção no caso de alguém obter acesso a seu arquivo de chave privada, dando-lhe tempo para alterar as chaves usadas para autenticá-lo.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Usando ssh-agent para armazenar sua senha de chave privada

Para evitar a digitação da senha do arquivo de chave privada em cada logon do SSH, você poderá usar `ssh-agent` para armazenar em cache a senha do arquivo de chave privada. Se você estiver usando um Mac, a cadeia de chaves do OSX armazena com segurança suas senhas de chave privadas quando `ssh-agent`é chamado.

Primeiro, verifique se `ssh-agent` está em execução

```bash
eval "$(ssh-agent -s)"
```

Agora, adicione a chave privada ao `ssh-agent` usando o comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Agora, a senha da chave privada é armazenada no `ssh-agent`.

## <a name="create-and-configure-an-ssh-config-file"></a>Criar e configurar um arquivo de configuração do SSH

É uma prática recomendada criar e configurar um arquivo `~/.ssh/config` para acelerar os logons e otimizar o comportamento do seu cliente SSH.

O exemplo a seguir mostra uma configuração padrão.

### <a name="create-the-file"></a>Criar o arquivo

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edite o arquivo a fim de adicionar a nova configuração de SSH:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Exemplo de arquivo `~/.ssh/config`:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Essa configuração de SSH fornece seções para cada serviço para habilitar cada um deles com seu próprio par de chaves dedicado. As configurações padrão (`Host *`) são para quaisquer hosts que não correspondam a qualquer um dos hosts específicos acima no arquivo de configuração.

### <a name="config-file-explained"></a>Arquivo de configuração explicado

`Host` = o nome do host sendo chamado no terminal.  `ssh fedora22` instrui `SSH` a usar os valores no bloco de configurações rotulado como `Host fedora22` OBSERVAÇÃO: o host pode ser qualquer rótulo lógico para o uso e não representa o nome de host real de qualquer servidor.

`Hostname 102.160.203.241` = o endereço IP ou o nome DNS do servidor acessado.

`User ahmet` = a conta de usuário remoto a ser usada ao fazer logon no servidor.

`PubKeyAuthentication yes` = instrui o SSH desejado a usar uma chave SSH para fazer logon.

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = a chave privada SSH e a chave pública correspondente a serem usadas para autenticação.

## <a name="ssh-into-linux-without-a-password"></a>SSH no Linux sem uma senha

Agora que tem um par de chaves SSH e um arquivo de configuração do SSH configurado, você pode fazer logon na VM do Linux de forma rápida e segura. Na primeira vez que você fizer logon em um servidor usando uma chave SSH, o comando solicitará a senha para esse arquivo de chave.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Comando explicado

Quando `ssh fedora22` é executado, primeiro o SSH localiza e carrega todas as configurações do bloco `Host fedora22`, em seguida, carrega todas as configurações restantes a partir do último bloco, `Host *`.

## <a name="next-steps"></a>Próximas etapas

A próxima etapa é criar VMs do Linux do Azure usando a nova chave pública SSH.  As VMs do Azure criadas com uma chave pública SSH como o logon estão mais protegidas do que as VMs criadas com as senhas do método de logon padrão.  As VMs do Azure criadas com chaves SSH são por padrão configuradas com senhas desabilitadas, evitando tentativas de adivinhação por força bruta.

* [Criar uma VM do Linux segura usando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM Linux segura usando o Portal do Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM Linux segura usando a CLI do Azure](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO2-->


