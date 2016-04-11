<properties
		pageTitle="Adicionar um usuário a uma VM Linux no Azure | Microsoft Azure"
		description="Adicione um usuário a uma VM Linux no Azure."
		services="virtual-machines-linux"
		documentationCenter=""
		authors="vlivech"
		manager="timlt"
		editor=""
		tags="azure-resource-manager"
/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="03/04/2016"
		ms.author="v-livech"
/>

# Adicionar um usuário a uma VM do Azure

Com qualquer VM Linux recentemente lançada, uma das primeiras tarefas é criar um novo usuário. Neste artigo, explicaremos como criar uma conta de usuário do sudo, configurar a senha, adicionar chaves públicas do SSH e, por fim, usar `visudo` para permitir o sudo sem uma senha.

Os pré-requisitos são: [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/), [chaves pública e privada SSH](virtual-machines-linux-mac-create-ssh-keys.md), um grupo de recursos do Azure e a CLI do Azure instalada e alternada para o modo Azure Resource Manager usando `azure config mode arm`.

## Comandos rápidos

```bash
# Add a new user on RedHat family distros
bill@slackware$ sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
bill@slackware$ sudo useradd -G sudo exampleUser

# Set a password
bill@slackware$ sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
bill@slackware$ ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
bill@slackware$ visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
exampleuser@exampleserver$ sudo top
```

## Passo a passo detalhado

### Introdução

Uma das primeiras tarefas, e uma das mais comuns, com um novo servidor é adicionar uma conta de usuário. Os logons de raiz sempre devem ser desabilitados e até mesmo a conta raiz em si não deve ser usada com o servidor Linux, apenas a do sudo. Criar um novo usuário e dar a ele privilégios de escalonamento raiz usando o sudo é a maneira adequada de administrar e usar o Linux.

O comando que usaremos é `useradd`, que modifica `/etc/passwd`, `/etc/shadow`, `/etc/group` e `/etc/gshadow` para criar o novo usuário do Linux. Adicionaremos um sinalizador de linha ao comando `useradd` para também adicionar o novo usuário ao grupo sudo adequado no Linux. Embora `useradd` crie uma entrada em `/etc/passwd`, ele não fornece ao novo usuário uma conta e uma senha. Criaremos uma senha inicial para o novo usuário usando `passwd`, um comando bastante simples. A última etapa será modificar as regras do sudo para permitir que o usuário execute comandos com privilégios sudo sem ter que inserir uma senha para cada comando. Depois que o usuário tiver feito logon usando o par de chaves pública e privada, vamos supor que essa conta de usuário esteja protegida contra atores nocivos e que permitirá acesso ao sudo sem uma senha.

### Adição de um único usuário do sudo a uma VM do Azure

Faça logon na VM do Azure usando chaves SSH. Se você não tiver configurado o acesso de chave pública SSH, leia este artigo primeiro [Using Public Key Authentication with Azure ](http://link.to/article) (Usando Autenticação de Chave Pública com o Azure).

O comando `useradd` concluirá as seguintes tarefas:

- Criar uma nova conta de usuário
- Criar um novo grupo de usuários com o mesmo nome
- Adicionar uma entrada em branco a `/etc/passwd`
- Adicionar uma entrada em branco a `/etc/gpasswd`

O sinalizador de linha de comando `-G` vai adicionar a nova conta de usuário ao grupo Linux adequado atribuindo à nova conta de usuário privilégios de escalonamento raiz.

#### Adicionar o usuário

```bash
# On RedHat family distros
bill@slackware$ sudo useradd -G wheel exampleUser

# On Debian family distros
bill@slackware$ sudo useradd -G sudo exampleUser
```

#### Definir uma senha

O comando `useradd` cria o novo usuário e adiciona uma entrada a `/etc/passwd` e `/etc/gpasswd`, mas não define a senha. Faremos isso agora usando o comando `passwd`.

```bash
bill@slackware$ sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

Agora temos um usuário com privilégios do sudo no servidor.

### Adicionar chave pública SSH à nova conta de usuário

No seu computador, use o comando `ssh-copy-id` com a nova senha que acabou de definir.

```bash
bill@slackware$ ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### Utilização do visudo para permitir o uso do sudo sem uma senha

Usar `visudo` para editar o arquivo `/etc/sudoers` adiciona algumas camadas de proteção contra modificação incorreta desse arquivo tão importante. Na execução de `visudo`, o arquivo `/etc/sudoers` é bloqueado para garantir que ninguém mais possa fazer alterações enquanto ele estiver sendo ativamente editado. O arquivo `/etc/sudoers` também é verificado em busca de erros pelo `visudo` quando você tenta salvar ou sair. Isso garante que você não deixe um arquivo sudoers com falha no sistema.

Já temos usuários no grupo padrão correto para acesso ao sudo. Agora, habilitaremos esses grupos para usar o sudo sem senha.

```bash
# Execute visudo as root to edit the /etc/sudoers file
bill@slackware$ visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### Verificar o usuário, as chaves ssh e o sudo

```bash
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
exampleuser@exampleserver$ sudo top
```

<!---HONumber=AcomDC_0330_2016-->