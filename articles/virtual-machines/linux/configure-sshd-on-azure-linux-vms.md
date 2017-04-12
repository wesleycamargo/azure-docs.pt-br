---
title: "Configurar o SSHD em máquinas virtuais do Linux do Azure | Microsoft Docs"
description: "Configure o SSHD de acordo as práticas recomendadas de segurança e para bloquear o SSH em máquinas virtuais do Linux do Azure."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0195c385b00ab92b2b92ce8ff00983a0d91bf3a1
ms.lasthandoff: 04/03/2017


---

# <a name="configure-sshd-on-azure-linux-vms"></a>Configurar o SSHD em VMs do Linux do Azure

Este artigo mostra como bloquear o Servidor SSH no Linux, para fornecer a segurança das práticas recomendadas e também para acelerar o processo de logon no SSH usando chaves SSH em vez de senhas.  Para bloquear o SSHD por completo, não vamos permitir que o usuário raiz faça logon, vamos limitar os usuários que têm permissão para fazer logon por meio de uma lista de grupos aprovados desabilitando o protocolo SSH versão 1, bem como configurar um bit de chave mínimo e o logoff automático de usuários ociosos.  Os requisitos para este artigo são: uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e [arquivos de chave SSH pública e privada](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Comandos rápidos

Defina o `/etc/ssh/sshd_config` com as seguintes configurações:

### <a name="disable-password-logins"></a>Desabilitar logons de senha

```bash
PasswordAuthentication no
```

### <a name="disable-login-by-the-root-user"></a>Desabilitar o logon do usuário raiz

```bash
PermitRootLogin no
```

### <a name="allowed-groups-list"></a>Lista de grupos permitidos

```bash
AllowGroups wheel
```

### <a name="allowed-users-list"></a>Lista de usuários permitidos

```bash
AllowUsers ahmet ralph
```

### <a name="disable-ssh-protocol-version-1"></a>Desabilitar o protocolo SSH versão 1

```bash
Protocol 2
```

### <a name="minimum-key-bits"></a>Bits de chave mínimos

```bash
ServerKeyBits 2048
```

### <a name="disconnect-idle-users"></a>Desconectar usuários ociosos

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

O SSHD é o Servidor SSH executado na VM Linux.  O SSH é um cliente executado em um shell no MacBook, uma estação de trabalho do Linux ou um Bash no Windows.  O SSH também é o protocolo usado para proteger e criptografar a comunicação entre a estação de trabalho e a VM do Linux, tornando-o também uma VPN (Rede Virtual Privada).

Para este artigo, é muito importante manter um logon aberto na VM do Linux durante todo o passo a passo.  Depois de estabelecida a conexão SSH, ela permanecerá como uma sessão aberta, contanto que a janela não seja fechada.  Ter um terminal conectado permite que sejam feitas alterações ao serviço SSHD, sem o bloqueio, caso seja feita uma alteração significativa.  Se você for bloqueado da VM do Linux com uma configuração SSHD desfeita, o Azure oferecerá a capacidade de redefinir uma configuração SSHD desfeita com a [Extensão de Acesso à VM do Azure](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Por esse motivo, abrimos dois terminais e o SSH para a VM do Linux em ambos.  Usamos o primeiro terminal para fazer as alterações ao arquivo de configuração do SSHD e reiniciar o serviço SSHD.  Usamos o segundo terminal para testar essas alterações assim que o serviço é reiniciado.  Como estamos desabilitando as senhas SSH e dependendo estritamente das chaves SSH, se suas chaves SSH não estiverem corretas e você fechar a conexão com a VM, a VM será bloqueada permanentemente e ninguém poderá fazer logon nela, exigindo que ela seja excluída e recriada.

## <a name="disable-password-logins"></a>Desabilitar logons de senha

A maneira mais rápida para proteger a VM do Linux é desabilitar os logons de senha.  Quando os logons de senha estiverem habilitados, os bots que rastreiam a Internet iniciarão imediatamente uma tentativa de força bruta para detectar a senha da VM do Linux usando SSH.  Desabilitar os logons de senha por completo permite que o servidor SSH ignore todas as tentativas de logon de senha.

```bash
PasswordAuthentication no
```

## <a name="disable-login-by-the-root-user"></a>Desabilitar o logon do usuário raiz

Ao seguir as práticas recomendadas do Linux, o usuário `root` nunca deverá ser conectado via SSH ou usando o `sudo su`.  Todos os comandos que precisam de permissões em nível de raiz sempre devem ser executados por meio do comando `sudo`, que registra em log todas as ações para auditoria futura.  Desabilitar o usuário `root` do logon via SSH é uma das etapas das práticas recomendas de segurança que garante que somente os usuários autorizados têm permissão para o SSH.

```bash
PermitRootLogin no
```

## <a name="allowed-groups-list"></a>Lista de grupos permitidos

O SSH oferece um método de restringir usuários e grupos que têm ou não permissão de logon via SSH.  Esse recurso usa listas para aprovar ou negar o logon a usuários e grupos específicos.  Configurar o grupo de roda para a lista `AllowGroups` restringe os logons aprovados via SSH a apenas as contas de usuário que estão no grupo de roda.

```bash
AllowGroups wheel
```

## <a name="allowed-users-list"></a>Lista de usuários permitidos

A restrição dos logons SSH somente a usuários é uma maneira mais específica de realizar a mesma tarefa comparado ao `AllowGroups`.  

```bash
AllowUsers ahmet ralph
```

## <a name="disable-ssh-protocol-version-1"></a>Desabilitar o protocolo SSH versão 1

O protocolo SSH versão 1 não é seguro e deve ser desabilitado.  O protocolo SSH versão 2 é a versão atual que oferece uma maneira segura da conexão via SSH com o servidor.  Desabilitar o SSH versão 1 nega qualquer cliente SSH que tenta estabelecer uma conexão com o servidor SSH usando o SSH versão 1.  Somente as conexões do SSH versão 2 são permitidas para negociar uma conexão com o servidor SSH.

```bash
Protocol 2
```

## <a name="minimum-key-bits"></a>Bits de chave mínimos

Seguindo as práticas recomendadas de segurança, os logons SSH de senha são desabilitados e somente as chaves SSH podem ser usadas para autenticar no servidor SSH.  Essas chaves SSH podem ser criadas usando chaves de comprimento diferente, medidas em bits.  As práticas recomendadas afirmam que chaves de 2048 bits de comprimento são a força de chave mínima aceitável.  Em teoria, chaves com menos de 2048 bits poderiam ser violadas.  A configuração do `ServerKeyBits` para `2048` permite todas as conexões que usam chaves de 2048 bits ou superior e nega as conexões com menos de 2048 bits.

```bash
ServerKeyBits 2048
```

## <a name="disconnect-idle-users"></a>Desconectar usuários ociosos

O SSH tem a capacidade de desconectar os usuários que têm conexões abertas que permaneceram ociosas por mais de um período definido de segundos.  Manter sessões abertas somente aos usuários que estão ativos limita a exposição da VM do Linux.

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="restart-sshd"></a>Reiniciar o SSHD

Para habilitar as configurações no `/etc/ssh/sshd_config`, reinicie o processo do SSHD, que reinicia o servidor SSH.  A janela do terminal usada para reiniciar o servidor SSH permanece aberta sem perder a sessão SSH aberta.  Para testar as novas configurações do servidor SSH, use uma segunda janela de terminal ou guia.  Usar um terminal separado para testar a conexão SSH permite voltar e fazer outras alterações ao `/etc/ssh/sshd_config` no primeiro terminal, sem ser bloqueado por uma alteração significativa do SSHD.  

### <a name="on-redhat-centos-and-fedora"></a>No Redhat, Centos e Fedora

```bash
service sshd restart
```

### <a name="on-debian--ubuntu"></a>No Debian e Ubuntu

```bash
service ssh restart
```

## <a name="reset-sshd-using-azure-reset-access"></a>Redefinir o SSHD usando o acesso de redefinição do Azure

Se você for impedido de fazer uma alteração significativa na configuração do SSHD, poderá usar a extensão de acesso de VM do Azure para redefinir a configuração do SSHD de volta à configuração original.

Substitua os nomes de exemplo pelos seus próprios.

```azurecli
azure vm reset-access \
--resource-group myResourceGroup \
--name myVM \
--reset-ssh
```

## <a name="install-fail2ban"></a>Instalar o Fail2ban

É altamente recomendável instalar e configurar o aplicativo de software livre Fail2ban, que bloqueia repetidas tentativas de logon na VM do Linux via SSH usando força bruta.  O Fail2ban registra em log as repetidas tentativas de logon com falha via SSH e cria regras de firewall para bloquear o endereço IP de origem das tentativas.

* [Home page do Fail2ban](http://www.fail2ban.org/wiki/index.php/Main_Page)

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou e bloqueou o servidor SSH na VM do Linux, há outras práticas recomendadas de segurança que podem ser seguidas.  

* [Gerenciar usuários, SSH e verificar ou reparar discos em VMs do Linux do Azure usando a extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Criptografar discos em uma VM do Linux usando a CLI do Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Acesso e segurança em modelos do Azure Resource Manager](dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

