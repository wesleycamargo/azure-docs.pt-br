<properties
	pageTitle="Criar chaves SSH no Linux e Mac | Microsoft Azure"
	description="Gerar e usar chaves SSH no Linux e no Mac para os modelos de implantação clássico e do Gerenciador de Recursos no Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/04/2016"
	ms.author="v-livech"/>

# Criar chaves SSH no Linux e Mac para VMs do Linux no Azure

Para criar uma chave SSH pública e privada protegida senha, você precisa da [CLI do Azure](../xplat-cli-install.md) no modo do gerenciador de recursos (`azure config mode arm`).

## Listagem rápida de comandos

Nos exemplos de comandos a seguir, substitua os valores entre &lt; e &gt; pelos valores de seu próprio ambiente.

```bash
[chrisL@fedora ~]$ ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.
azure_fedora_id_rsa

#Enter (twice) a [secure](https://www.xkcd.com/936/) password for the SSH key.

#Enter passphrase for github_id_rsa:
correct horse battery staple

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).
[chrisL@fedora ~]$ eval "$(ssh-agent -s)"
[chrisL@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.
[chrisL@fedora ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.
[chrisL@fedora ~]$ ssh -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue Dec 29 07:07:09 2015 from 66.215.21.201
[chrisL@fedora ~]$

```

## Introdução

Usar chaves SSH públicas e privadas é a maneira mais seguro **e** fácil de fazer logon em servidores Linux, mas, além disso, a [criptografia de chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) também fornece uma maneira muito mais segura de fazer logon em sua VM BSD ou Linux no Azure do que senhas, que podem ser violadas com muito mais facilidade. Sua chave pública pode ser compartilhada com qualquer pessoa; mas apenas você (ou sua infraestrutura de segurança local) possui sua chave privada.

Este artigo cria arquivos de chave de formato *ssh-rsa*, pois são recomendados para implantações no Gerenciador de Recursos e necessários no [portal](https://portal.azure.com) para implantações clássicas e do Gerenciador de Recursos.


## Criar as chaves SSH

O Azure requer chaves públicas e privadas de pelo menos 2048 bits em formato ssh-rsa. Para criar o par, use `ssh-keygen`, que faz uma série de perguntas e, em seguida, grava uma chave privada e uma chave pública correspondente. Ao criar a VM do Azure, você passa o conteúdo de chave pública, que é copiado para a VM do Linux e é usado com sua chave privada local e armazenado com segurança para autenticá-lo quando você faz logon.

### Usando `ssh-keygen`

Esse comando cria um par de chaves SSH usando RSA de 2048 bits, e será comentado para identificá-lo facilmente.

```
chrisL@fedora$ ssh-keygen -t rsa -b 2048 -C "username@fedoraVMAzure"
```

##### Comando explicado

`ssh-keygen` = o programa usado para criar as chaves

`-t rsa` = tipo de chave a ser criada, que é o [formato RSA] (https://en.wikipedia.org/wiki/RSA_(cryptosystem))

`-b 2048` = bits da chave

`-C "username@fedoraVMAzure"` = um comentário para a chave, para identificá-la facilmente. O comentário é acrescentado ao fim do arquivo de chave pública. Um comentário frequentemente utilizado é um endereço de email, mas, para este artigo, vamos habilitar o uso de várias chaves SSH, assim, um comentário genérico é sugerido.

#### Guia passo a passo de `ssh-keygen`

```bash
chrisL@fedora$ ssh-keygen -t rsa -b 2048 -C "username@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:79:ad:25:cc:65:e9:0c:07:e5:d1:a9:08 username@fedoraVMAzure
The key's randomart image is:
+--[ RSA 4096]----+
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

chrisL@fedora$ ls -al ~/.ssh
-rw------- 1 username staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 username staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa` O nome do par de chaves para este artigo. Ter um par de chaves denominado **id\_rsa** é o padrão, e algumas ferramentas podem esperar o nome de arquivo de chave privada **id\_rsa**; portanto, ter um é uma boa ideia. (`~/.ssh/` é o local padrão típico para todos os pares de chaves SSH e o arquivo de configuração do SSH.)

`Enter passphrase (empty for no passphrase):` É altamente recomendável adicionar uma senha (o `ssh-keygen` chama isso de "senha") para seus pares de chaves. Sem uma senha para proteger o par de chaves, qualquer pessoa com uma cópia do arquivo da chave privada pode usá-lo para fazer logon em qualquer servidor (ou em seus servidores) com a chave pública correspondente. Portanto, adicionar uma senha oferece muito mais proteção no caso de alguém obter acesso a seu arquivo de chave privada, dando-lhe tempo para alterar as chaves usadas para autenticá-lo.

`chrisL@fedora$ ls -al ~/.ssh` Isso mostra os novos pares de chaves e suas permissões. O `ssh-keygen` criará o diretório `~/.ssh`, se ele não estiver presente, e também definirá os modos de propriedade e arquivo corretos.

## Criar e configurar um arquivo de configuração do SSH

Embora isso não seja absolutamente necessário para trabalhar com uma VM do Linux, é uma prática recomendada criar e configurar um arquivo `~/.ssh/config` para impedir o uso acidental de senhas para fazer logon com suas VMs, automatizar o uso de diferentes pares de chaves para diferentes VMs do Azure e configurar outros programas, como **git**, para direcionar vários servidores também.

O exemplo a seguir mostra uma configuração padrão.

### Criar o arquivo

```bash
chrisL@fedora$ touch ~/.ssh/config
```

### Editar o arquivo para adicionar a nova configuração de SSH

```bash
chrisL@fedora$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User username
  PubkeyAuthentication yes
  IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/github_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/private_repo_github_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /Users/steve/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /Users/steve/.ssh/id_rsa
  UseRoaming=no
```

Essa configuração de SSH fornece seções para cada serviço para habilitar cada um deles com seu próprio par de chaves dedicado. As configurações padrão são para os hosts nos quais você estiver conectado e que não corresponderem a nenhum dos grupos acima. A configuração de SSH também o habilita a ter dois logons separados do [GitHub](https://github.com), um para o trabalho público e um segundo para repositórios particulares que possam ser comuns em seu trabalho.


##### Arquivo de configuração explicado

`Host` = o nome do host que está sendo chamado no terminal. `ssh fedora22` instrui `SSH` a usar os valores no bloco de configurações rotulado como `Host fedora22` Observação: esse pode ser qualquer rótulo lógico para o uso e não representa o nome de host real de qualquer servidor.

`Hostname 102.160.203.241` = o endereço IP ou o nome DNS do servidor ao qual você está se conectando. Isso é usado para o roteamento para o servidor e pode ser um IP externo que seja mapeado para um endereço IP interno.

`User git` = a conta de usuário remoto a ser usada ao fazer logon na VM do Azure.

`PubKeyAuthentication yes` = instrui o SSH a usar uma chave SSH para fazer logon.

`IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa` = informa ao SSH qual par de chaves deve ser apresentado ao servidor para autenticar o logon.


## SSH em uma VM do Linux sem uma senha

Agora que tem um par de chaves SSH e um arquivo de configuração do SSH configurado, você pode fazer logon na VM do Linux de forma rápida e segura. Na primeira vez que você fizer logon em um servidor usando uma chave SSH, o comando solicitará a senha para esse arquivo de chave.

`chrisL@fedora$ ssh fedora22`

##### Comando explicado

Quando `chrisL@fedora$ ssh fedora22` é executado, primeiro o SSH localiza e carrega todas as configurações do bloco `Host fedora22` e, em seguida, carrega todas as configurações restantes do último bloco, `Host *`.

## Próximas etapas

Agora você pode usar os arquivos de chave para [criar uma VM do Linux segura no Azure usando um modelo](virtual-machines-linux-create-ssh-secured-vm-from-template.md).

<!---HONumber=AcomDC_0406_2016-->