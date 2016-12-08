---
title: Desabilitar senhas SSH na sua VM Linux configurando o SSHD | Microsoft Docs
description: Proteja sua VM Linux no Azure desabilitando logons de senha para SSH.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 46137640-a7d2-40e5-a1e9-9effef7eb190
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 5e5eb2a4821918e88ea3f566c7f3203dab5a68b9


---
# <a name="disable-ssh-passwords-on-your-linux-vm-by-configuring-sshd"></a>Desabilitar senhas SSH na sua VM Linux configurando o SSHD
Este artigo se concentra em como bloquear a segurança de logon da sua VM Linux.  Assim que a porta SSH 22 é aberta para o mundo, os bots começam a tentar fazer logon adivinhando senhas.  O que vamos fazer neste artigo é desabilitar os logons de senha via SSH.  Ao remover completamente a capacidade de usar senhas, protegemos a VM Linux contra esse tipo de ataque de força bruta.  A vantagem disso é que vamos configurar o SSHD do Linux para permitir apenas logons via chaves públicas e privadas do SSH, definitivamente a forma mais segura de fazer logon no Linux.  As combinações possíveis exigiriam adivinhar a chave privada, que é imensa e, portanto, desencoraja os bots a tentar um ataque de força bruta às chaves SSH.

## <a name="goals"></a>Metas
* Configurar o SSHD para não permitir:
  * Logons de senha
  * Logon de usuário raiz
  * Autenticação de desafio/resposta
* Configurar o SSHD para permitir:
  * Somente logons de chave SSH
* Reiniciar o SSHD enquanto ainda estiver conectado
* Testar a nova configuração do SSHD

## <a name="introduction"></a>Introdução
[Definido por SSH](https://en.wikipedia.org/wiki/Secure_Shell)

O SSHD é o Servidor SSH executado na VM Linux.  O SSH é um cliente executado em um shell no seu MacBook ou estação de trabalho Linux.  O SSH também é o protocolo usado para proteger e criptografar a comunicação entre sua estação de trabalho e a VM Linux.

Para este artigo, é muito importante manter um logon aberto na sua VM Linux durante todo o passo a passo.  Por esse motivo, abriremos dois terminais e o SSH para a VM Linux em ambos.  Usaremos o primeiro terminal para fazer as mudanças no arquivo de configuração do SSHD e reiniciar o serviço SSHD.  Usaremos o segundo terminal para testar essas mudanças assim que o serviço for reiniciado.  Como estamos desabilitando as senhas SSH e dependendo estritamente das chaves SSH, se suas chaves SSH não estiverem corretas e você fechar a conexão com a VM, a VM será bloqueada permanentemente e ninguém poderá fazer logon nela, exigindo que ela seja excluída e recriada.

## <a name="prerequisites"></a>Pré-requisitos
* [Criar chaves SSH no Linux e Mac para VMs do Linux no Azure](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Conta do Azure
  * [assinatura de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)
  * [Portal do Azure](http://portal.azure.com)
* VM Linux em execução no Azure
* Par de chaves pública e privada SSH em `~/.ssh/`
* Chave pública SSH em `~/.ssh/authorized_keys` na VM Linux
* Direitos do sudo na VM
* Porta 22 aberta

## <a name="quick-commands"></a>Comandos rápidos
*Administradores Linux experientes que querem apenas a versão TLDR, comecem aqui.  Qualquer outra pessoa que queira a explicação detalhada e o passo a passo deve ignorar esta seção.*

```bash
sudo vim /etc/ssh/sshd_config
```

Edite o arquivo de configuração conforme descrito a seguir:

```sh
# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

Reinicie o serviço SSHD. Em distribuições com base em Debian:

```bash
sudo service ssh restart
```

Em distribuições com base em Red Hat:

```bash
sudo service sshd restart
```

## <a name="detailed-walk-through"></a>Passo a passo detalhado
Faça logon na VM Linux no terminal 1 (T1).  Faça logon na VM Linux no terminal 2 (T2).

No T2, vamos editar o arquivo de configuração do SSHD.  

```bash
sudo vim /etc/ssh/sshd_config
```

Nele, editaremos apenas as configurações para desabilitar senhas e habilitar os logons de chave SSH.  Há muitas configurações nesse arquivo que você deve pesquisar e alterar para tornar o Linux e o SSH tão seguros quanto necessários.

#### <a name="disable-password-logins"></a>Desabilitar logons de senha

```sh
# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### <a name="enable-public-key-authentication"></a>Habilitar a autenticação de chave pública

```sh
# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### <a name="disable-root-login"></a>Desabilitar logon de raiz

```sh
# Change PermitRootLogin to this:
PermitRootLogin no
```

#### <a name="disable-challenge-response-authentication"></a>Desabilitar autenticação de desafio/resposta
```sh
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### <a name="restart-sshd"></a>Reiniciar o SSHD
No shell de T1, verifique se você ainda está conectado.  Isso é essencial para que você não seja bloqueado na VM se suas chaves SSH não estiverem corretas, uma vez que as senhas agora estão desabilitadas.  Se alguma configuração estiver incorreta na VM Linux, você poderá usar T1 para corrigir sshd_config, pois ainda estará conectado e o SSH manterá a conexão ativa durante a reinicialização do serviço SSHD.

No T2, execute:

##### <a name="on-the-debian-family"></a>Na família Debian
```bash
sudo service ssh restart
```

##### <a name="on-the-redhat-family"></a>Na família RedHat
```bash
sudo service sshd restart
```

As senhas agora estão desabilitadas na sua VM, protegendo-a contra tentativas de logon de senha por força bruta.  Apenas com as chaves SSH permitidas, você poderá fazer logon mais rapidamente e com muito mais segurança.




<!--HONumber=Nov16_HO3-->


