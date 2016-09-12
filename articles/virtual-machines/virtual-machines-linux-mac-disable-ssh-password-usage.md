<properties
	pageTitle="Desabilitar senhas SSH na sua VM Linux configurando o SSHD | Microsoft Azure"
	description="Proteja sua VM Linux no Azure desabilitando logons de senha para SSH."
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
	ms.topic="article"
	ms.date="08/26/2016"
	ms.author="v-livech"/>

# Desabilitar senhas SSH na sua VM Linux configurando o SSHD

Este artigo se concentra em como bloquear a segurança de logon da sua VM Linux. Assim que a porta SSH 22 é aberta para o mundo, os bots começam a tentar fazer logon adivinhando senhas. O que vamos fazer neste artigo é desabilitar os logons de senha via SSH. Ao remover completamente a capacidade de usar senhas, protegemos a VM Linux contra esse tipo de ataque de força bruta. A vantagem disso é que vamos configurar o SSHD do Linux para permitir apenas logons via chaves públicas e privadas do SSH, definitivamente a forma mais segura de fazer logon no Linux. As combinações possíveis exigiriam adivinhar a chave privada, que é imensa e, portanto, desencoraja os bots a tentar um ataque de força bruta às chaves SSH.


## Metas

- Configurar o SSHD para não permitir:
  - Logons de senha
  - Logon de usuário raiz
  - Autenticação de desafio/resposta
- Configurar o SSHD para permitir:
  - Somente logons de chave SSH
- Reiniciar o SSHD enquanto ainda estiver conectado
- Testar a nova configuração do SSHD

## Introdução

[Definido por SSH](https://en.wikipedia.org/wiki/Secure_Shell)

O SSHD é o Servidor SSH executado na VM Linux. O SSH é um cliente executado em um shell no seu MacBook ou estação de trabalho Linux. O SSH também é o protocolo usado para proteger e criptografar a comunicação entre sua estação de trabalho e a VM Linux.

Para este artigo, é muito importante manter um logon aberto na sua VM Linux durante todo o passo a passo. Por esse motivo, abriremos dois terminais e o SSH para a VM Linux em ambos. Usaremos o primeiro terminal para fazer as mudanças no arquivo de configuração do SSHD e reiniciar o serviço SSHD. Usaremos o segundo terminal para testar essas mudanças assim que o serviço for reiniciado. Como estamos desabilitando as senhas SSH e dependendo estritamente das chaves SSH, se suas chaves SSH não estiverem corretas e você fechar a conexão com a VM, a VM será bloqueada permanentemente e ninguém poderá fazer logon nela, exigindo que ela seja excluída e recriada.

## Pré-requisitos

- [Criar chaves SSH em VMs Linux e em VMs Mac para Linux no Azure](virtual-machines-linux-mac-create-ssh-keys.md)
- Conta do Azure
  - [assinatura de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)
  - [Portal do Azure](http://portal.azure.com)
- VM Linux em execução no Azure
- Par de chaves pública e privada SSH em `~/.ssh/`
- Chave pública SSH em `~/.ssh/authorized_keys` na VM Linux
- Direitos do sudo na VM
- Porta 22 aberta

## Comandos rápidos

_Administradores Linux experientes que querem apenas a versão TLDR, comecem aqui. Qualquer outra pessoa que queira a explicação detalhada e o passo a passo deve ignorar esta seção._

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## Passo a passo detalhado

Faça logon na VM Linux no terminal 1 (T1). Faça logon na VM Linux no terminal 2 (T2).

No T2, vamos editar o arquivo de configuração do SSHD.

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

Nele, editaremos apenas as configurações para desabilitar senhas e habilitar os logons de chave SSH. Há muitas configurações nesse arquivo que você deve pesquisar e alterar para tornar o Linux e o SSH tão seguros quanto necessários.

#### Desabilitar logons de senha

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### Habilitar a autenticação de chave pública

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### Desabilitar logon de raiz

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### Desabilitar autenticação de desafio/resposta

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### Reiniciar o SSHD

No shell de T1, verifique se você ainda está conectado. Isso é essencial para que você não seja bloqueado na VM se suas chaves SSH não estiverem corretas, uma vez que as senhas agora estão desabilitadas. Se alguma configuração estiver incorreta na VM Linux, você poderá usar T1 para corrigir sshd\_config, pois ainda estará conectado e o SSH manterá a conexão ativa durante a reinicialização do serviço SSHD.

No T2, execute:

##### Na família Debian

```
username@macbook$ sudo service ssh restart
```

##### Na família RedHat

```
username@macbook$ sudo service sshd restart
```

As senhas agora estão desabilitadas na sua VM, protegendo-a contra tentativas de logon de senha por força bruta. Apenas com as chaves SSH permitidas, você poderá fazer logon mais rapidamente e com muito mais segurança.

<!---HONumber=AcomDC_0831_2016-->