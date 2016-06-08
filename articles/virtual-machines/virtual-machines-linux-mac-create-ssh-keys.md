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
	ms.date="05/16/2016"
	ms.author="v-livech"/>

# Criar chaves SSH no Linux e Mac para VMs do Linux no Azure

Para criar uma chave SSH pública e privada protegida por senha, você precisa de um terminal aberto em sua estação de trabalho. Assim que você tiver as chaves SSH, poderá criar novas VMs com essa chave por padrão ou adicionar a chave pública às VMs existentes usando a CLI do Azure e os modelos do Azure. Isso permitirá logons sem senha via SSH, usando o método de autenticação muito mais seguro de chaves em vez das senhas.

## Listagem rápida de comandos

Nos exemplos de comandos a seguir, substitua os valores entre &lt; e &gt; pelos valores de seu próprio ambiente.

```bash
ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"
```

Insira o nome do arquivo que será salvo no diretório `~/.ssh/`:

```bash
<azure_fedora_id_rsa>
```

Insira a senha para azure\_fedora\_id\_rsa:

```bash
<correct horse battery staple>
```

Adicione a chave recém-criada para `ssh-agent` no Linux e Mac (também é adicionada ao Conjunto de chaves OSX):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azure_fedora_id_rsa
```

Copie a chave pública SSH em seu Servidor Linux:

```bash
ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>
```

Teste o logon usando chaves em vez de uma senha:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## Introdução

Usar chaves SSH públicas e privadas é a maneira mais fácil de fazer logon em seus servidores Linux, mas, além disso, a [criptografia da chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) também fornece uma maneira muito mais segura de fazer logon em sua VM BSD ou Linux no Azure do que as senhas, que podem ser violadas por força bruta com muito mais facilidade. Sua chave pública pode ser compartilhada com qualquer pessoa; mas apenas você (ou sua infraestrutura de segurança local) possui sua chave privada. A chave privada SSH criada terá uma [senha segura](https://www.xkcd.com/936/) para protegê-la e essa senha é apenas para acessar a chave e **não é** a senha da conta de usuário. Quando você adiciona uma senha para a chave SSH, ela criptografa a chave privada para que esta não possa ser usada sem a senha para desbloqueá-la. Se um invasor conseguisse roubar sua chave privada e ela não tivesse senha, ele seria capaz de usar essa chave privada para fazer logon nos servidores que possuem a chave pública correspondente instalada. Se a chave privada é protegida por senha, ela não pode ser usada por esse invasor, o que é uma camada adicional de segurança para sua infraestrutura no Azure.


Este artigo cria arquivos de chave com o formato *ssh-rsa*, pois são recomendados para as implantações no Gerenciador de Recursos e necessários no [portal](https://portal.azure.com) para as implantações clássicas e do Gerenciador de Recursos.


## Criar as chaves SSH

O Azure requer chaves públicas e privadas de pelo menos 2048 bits em formato ssh-rsa. Para criar o par, usaremos `ssh-keygen`, que faz uma série de perguntas, em seguida, grava uma chave privada e uma chave pública correspondente. Ao criar a VM do Azure, você passa o conteúdo de chave pública, que é copiado para a VM do Linux e é usado com sua chave privada local e armazenado com segurança para autenticá-lo quando você faz logon.

## Usando ssh-keygen

Esse comando cria um par de chaves SSH (criptografado) protegido por senha usando o RSA de 2048 bits e será comentado para identificá-lo facilmente.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

_Comando explicado_

`ssh-keygen` = programa usado para criar as chaves

`-t rsa` = tipo de chave a ser criada, que é o [formato RSA] (https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = bits da chave

`-C "ahmet@fedoraVMAzure"` = um comentário acrescentado ao fim do arquivo de chave pública para identificá-lo facilmente. Normalmente, um email é usado como o comentário, mas você pode usar o que funcionar melhor para sua infraestrutura.

## Instruções passo a passo para o ssh-keygen

Cada etapa é explicada com detalhes. Comece executando `ssh-keygen`.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@fedoraVMAzure
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

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa`

O nome do par de chaves para este artigo. Ter um par de chaves denominado **id\_rsa** é o padrão e algumas ferramentas podem esperar o nome de arquivo da chave privada **id\_rsa**, portanto, ter um é uma boa ideia. (`~/.ssh/` é o local padrão típico para todos os pares de chaves SSH e o arquivo de configuração do SSH.)

```bash
ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```
Isso mostra os novos pares de chave e suas permissões. O `ssh-keygen` criará o diretório `~/.ssh`, se ele não existir e também definirá os modos de propriedade e arquivo corretos.

Senha da chave:

`Enter passphrase (empty for no passphrase):`

Recomendamos adicionar uma senha (o `ssh-keygen` chama isso de "passphrase") a seus pares de chave. Sem uma senha para proteger o par de chaves, qualquer pessoa com uma cópia do arquivo da chave privada pode usá-lo para fazer logon em qualquer servidor (ou em seus servidores) com a chave pública correspondente. Portanto, adicionar uma senha oferece muito mais proteção no caso de alguém obter acesso a seu arquivo de chave privada, dando-lhe tempo para alterar as chaves usadas para autenticá-lo.

## Usando ssh-agent para armazenar sua senha de chave privada

Para evitar a digitação da senha do arquivo de chave privada em cada logon do SSH, você poderá usar `ssh-agent` para armazenar em cache a senha do arquivo de chave privada permitindo logons rápidos e seguros em sua VM do Linux. Se você estiver usando OSX, o conjunto de chaves com segurança armazenará suas senhas de chave privadas ao chamar `ssh-agent`.

Primeiro, verifique se `ssh-agent` está em execução

```bash
eval "$(ssh-agent -s)"
```

Agora, adicione a chave privada a `ssh-agent` usando o comando `ssh-add`, novamente no OSX isso iniciará o conjunto de chaves que armazenará as credenciais.

```bash
ssh-add ~/.ssh/azure_fedora_id_rsa
```

A senha da chave privada agora é armazenada para que você não precise digitar a senha da chave a cada logon do SSH.

## Criar e configurar um arquivo de configuração do SSH

Embora isso não seja absolutamente necessário para trabalhar com uma VM do Linux, é uma prática recomendada criar e configurar um arquivo `~/.ssh/config` para impedir o uso acidental de senhas para fazer logon em suas VMs, automatizar o uso de diferentes pares de chave para diferentes VMs do Azure e configurar outros programas, como **git**, para direcionar vários servidores também.

O exemplo a seguir mostra uma configuração padrão.

### Criar o arquivo

```bash
touch ~/.ssh/config
```

### Edite o arquivo a fim de adicionar a nova configuração de SSH:

```bash
vim ~/.ssh/config
```

### Exemplo de arquivo `~/.ssh/config`:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /home/ahmet/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /home/ahmet/.ssh/id_rsa
  UseRoaming=no
```

Essa configuração de SSH fornece seções para cada serviço para habilitar cada um deles com seu próprio par de chaves dedicado. As configurações padrão são para os hosts nos quais você estiver conectado e que não corresponderem a nenhum dos grupos acima.


### Arquivo de configuração explicado

`Host` = o nome do host sendo chamado no terminal. `ssh fedora22` instrui `SSH` para usar os valores no bloco de configurações rotulado como `Host fedora22` OBSERVAÇÃO: pode ser qualquer rótulo lógico para o uso e não representa o nome de host real de qualquer servidor.

`Hostname 102.160.203.241` = o endereço IP ou o nome DNS do servidor sendo conectado. Isso é usado para o roteamento para o servidor e pode ser um IP externo que seja mapeado para um endereço IP interno.

`User git` = a conta de usuário remoto a ser usada ao fazer logon na VM do Azure.

`PubKeyAuthentication yes` = instrui o SSH desejado a usar uma chave SSH para fazer logon.

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa` = informa ao SSH qual par de chaves deve ser apresentado ao servidor para autenticar o logon.


## SSH no Linux sem uma senha

Agora que tem um par de chaves SSH e um arquivo de configuração do SSH configurado, você pode fazer logon na VM do Linux de forma rápida e segura. Na primeira vez que você fizer logon em um servidor usando uma chave SSH, o comando solicitará a senha para esse arquivo de chave.

```bash
ssh fedora22
```

### Comando explicado

Quando `ssh fedora22` é executado, primeiro o SSH localiza e carrega todas as configurações do bloco `Host fedora22`, em seguida, carrega todas as configurações restantes a partir do último bloco, `Host *`.

## Próximas etapas

A próxima etapa é criar VMs do Linux do Azure usando a nova chave pública SSH. As VMs do Azure criadas com uma chave pública SSH como o logon estão mais protegidas do que aqueles criadas com as senhas do método de logon padrão. As VMs do Azure que usam chaves SSH para logon são, por padrão, configuradas para desabilitar os logons com senha, evitando a quebra de força bruta nas tentativas.

- [Criar uma VM Linux segura usando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Criar uma VM Linux segura usando o Portal do Azure](virtual-machines-linux-quick-create-portal.md)
- [Criar uma VM Linux segura usando a CLI do Azure](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0525_2016-->