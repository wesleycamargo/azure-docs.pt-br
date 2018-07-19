---
title: Console serial da máquina virtual do Azure | Microsoft Docs
description: Console serial bidirecional para máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 00a776131321500386b507f45ea84817b08147f7
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867854"
---
# <a name="virtual-machine-serial-console-preview"></a>Console serial da máquina virtual (visualização) 


O console serial da máquina virtual no Azure fornece acesso a um console baseado em texto para máquinas virtuais Linux e Windows. Essa conexão serial é para a porta serial COM1 da máquina virtual, fornece acesso à máquina virtual e não está relacionada à rede da máquina virtual/estado do sistema operacional. No momento, o acesso ao console serial para uma máquina virtual pode ser obtido somente por meio do portal do Azure e é permitido apenas para os usuários que têm acesso de Colaborador de VM ou superior à máquina virtual. 

> [!Note] 
> As visualizações são disponibilizadas a você se concordar com os termos de uso. Para saber mais, confira [Termos de Uso do Microsoft Azure para Visualizações do Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Atualmente, este serviço está em **visualização pública**, e o acesso ao console serial para máquinas virtuais está disponível para regiões globais do Azure. Neste ponto, o console serial não está disponível por meio de Azure Governamental, Azure Alemanha e Azure China cloud.


## <a name="prerequisites"></a>pré-requisitos 

* Você deve estar usando o modelo de implantação do Gerenciamento de Recursos. Implantações clássicas não possuem suporte. 
* A máquina virtual deve ter os [diagnósticos de inicialização](boot-diagnostics.md) habilitados 
* A conta que usa o console serial deve ter a [função de Colaborador](../../role-based-access-control/built-in-roles.md) para a VM e a conta de armazenamento [diagnósticos de inicialização](boot-diagnostics.md). 
* Para obter configurações específicas de distribuição do Linux, confira [Acesso ao console serial para Linux](#accessing-serial-console-for-linux)


## <a name="open-the-serial-console"></a>Abra o console serial
o console serial de máquinas virtuais só pode ser acessado por meio do [portal do Azure](https://portal.azure.com). A seguir, as etapas para acessar o console serial para máquinas virtuais por meio do portal 

  1. Abrir o portal do Azure
  2. No menu à esquerda, selecione as máquinas virtuais.
  3. Clique na VM na lista. A página de visão geral para a VM será aberta.
  4. Role para baixo até a seção Suporte + Solução de Problemas e clique na opção de console serial (visualização). Um novo painel com o console serial será aberto e iniciará a conexão.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> O console serial requer um usuário local com uma senha configurada. Neste momento, VMs configuradas somente com a chave pública SSH não terão acesso ao console serial. Para criar um usuário local com senha, siga [Extensão de Acesso a VM](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) e crie um usuário local com senha.

### <a name="disable-feature"></a>Desabilitar recurso
A funcionalidade do console serial pode ser desativada para VMs específicas desabilitando a configuração de diagnóstico de inicialização da VM.

## <a name="serial-console-security"></a>Segurança de console serial 

### <a name="access-security"></a>Segurança de acesso 
O acesso ao console serial é limitado aos usuários que têm acesso de [Colaboradores de VM](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior para a máquina virtual. Se o locatário do AAD exigir a Autenticação Multifator, o acesso ao console serial também exigirá MFA, pois seu acesso é por meio do [portal do Azure](https://portal.azure.com).

### <a name="channel-security"></a>Segurança de canal
Todos os dados são enviados criptografados durante a transmissão.

### <a name="audit-logs"></a>Logs de auditoria
Todo o acesso ao console serial está conectado a logs de [diagnósticos de inicialização](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) da máquina virtual. O acesso a esses logs pertence e é controlado pelo administrador da máquina virtual do Azure.  

>[!CAUTION] 
Embora nenhuma senha de acesso para o console seja registrada, se comandos executados dentro do console contiverem ou gerarem senhas, segredos, nomes de usuário ou qualquer outra forma de PII (Informações de Identificação Pessoal), eles serão gravados nos logs de diagnóstico de inicialização da máquina virtual, juntamente com todo o outro texto visível, como parte da implementação da funcionalidade de scrollback do console serial. Esses logs são circulares, e somente pessoas com permissões de leitura para a conta de armazenamento de diagnóstico têm acesso a eles. No entanto, recomendamos que você siga a prática recomendada de usar o console SSH para qualquer item que envolva segredos e/ou PII. 

### <a name="concurrent-usage"></a>Uso simultâneo
Se um usuário estiver conectado ao console serial e outro usuário solicitar com êxito o acesso à mesma máquina virtual, o primeiro usuário será desconectado, e o segundo usuário será conectado de maneira semelhante ao primeiro usuário, aguardando e saindo do console físico, com um novo usuário aguardando.

>[!CAUTION] 
Isso significa que o usuário que é desconectado não será fará logoff! A capacidade de impor um logoff após a desconexão (via SIGHUP ou um mecanismo semelhante) ainda está em nossos planos. Para o Windows, há um tempo limite automático habilitado no SAC. Porém, para Linux, você pode definir a configuração de tempo limite de terminal. Para fazer isso, basta adicionar `export TMOUT=600` a .bash_profile ou .profile para o usuário com o qual você se conectou ao console, para que a sessão atinja o tempo limite após 10 minutos.

### <a name="disable-feature"></a>Desabilitar recurso
A funcionalidade do console serial pode ser desativada para VMs específicas desabilitando a configuração de diagnóstico de inicialização da VM.

## <a name="common-scenarios-for-accessing-serial-console"></a>Cenários comuns para acessar o console serial 
Cenário          | Ações no console serial                |  Aplicabilidade de sistema operacional 
:------------------|:-----------------------------------------|:------------------
Arquivo FSTAB danificado | Insira a chave para continuar e corrija o arquivo fstab usando um editor de texto. Confira [como corrigir problemas de fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
Regras de firewall incorretas | Acessar o console serial e corrigir iptables ou regras de firewall do Windows | Linux/Windows 
Corrupção de sistema de arquivos/verificação | Acessar o console serial e recuperar o sistema de arquivos | Linux/Windows 
Problemas de configuração de RDP/SSH | Acessar o console serial e alterar as configurações | Linux/Windows 
Bloqueio de sistema de rede| Acesse o console serial pelo portal para gerenciar o sistema | Linux/Windows 
Interagir com o carregador de inicialização | Acesse GRUB/BCD usando o console serial | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Acessar o console serial para Linux
Para que o console serial funcione corretamente, o sistema operacional convidado deve ser configurado para ler e gravar mensagens de console para a porta serial. A maioria das [Distribuições do Linux para Azure aprovadas](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) tem o console serial configurado por padrão. Basta clicar no portal na seção de Console serial para obter acesso ao console. 

### <a name="access-for-red-hat"></a>Acesso para Red Hat 
Red Hat Images disponível no Azure tem acesso de console habilitado por padrão. O modo de usuário único no Red Hat requer que o usuário raiz seja habilitado. Ele é desabilitado por padrão. Se você precisar habilitar o modo de usuário único, siga estas instruções:

1. Faça logon no sistema Red Hat via SSH
2. Habilitar a senha para o usuário raiz 
 * `passwd root` (definir uma senha forte raiz)
3. Verifique se o usuário raiz só pode fazer logon por meio de ttyS0
 * `edit /etc/ssh/sshd_config` e verifique se PermitRootLog está definido como não
 * `edit /etc/securetty file` para permitir somente o logon por meio de ttyS0 

Agora, se o sistema for inicializado no modo de usuário único, você poderá se conectar por meio da senha raiz.

Como alternativa para RHEL 7.4 + ou 6.9 +, você pode habilitar o modo de usuário único nos prompts do GRUB. Confira as instruções [aqui](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="access-for-ubuntu"></a>Acesso para Ubuntu 
As imagens do Ubuntu disponíveis no Azure têm o acesso ao console habilitado por padrão. Se o sistema for inicializado no Modo de Usuário Único, você terá acesso sem credenciais adicionais. 

### <a name="access-for-coreos"></a>Acesso para CoreOS
As imagens de CoreOS disponíveis no Azure têm o acesso ao console habilitado por padrão. Se o sistema necessário puder ser inicializado no Modo de Usuário Único com a alteração de parâmetros GRUB e a adição de `coreos.autologin=ttyS0`, isso permitirá ao usuário principal fazer logon e acessar o console serial. 

### <a name="access-for-suse"></a>Acesso para SUSE
As imagens de SLES disponíveis no Azure têm o acesso ao console habilitado por padrão. Se você estiver usando versões mais antigas de SLES no Azure, siga o [artigo KB](https://www.novell.com/support/kb/doc.php?id=3456486) para habilitar o console serial. As imagens mais recentes de SLES 12 SP3+ também permitem o acesso por meio do console serial, caso o sistema seja inicializado no modo de emergência.

### <a name="access-for-centos"></a>Acesso para o CentOS
As imagens do CentOS disponíveis no Azure têm o acesso ao console habilitado por padrão. Para o Modo de Usuário Único, siga instruções semelhantes a Red Hat Images acima. 

### <a name="access-for-oracle-linux"></a>Acesso para o Oracle Linux
As imagens do Oracle Linux disponíveis no Azure têm o acesso ao console habilitado por padrão. Para o Modo de Usuário Único, siga instruções semelhantes a Red Hat Images acima.

### <a name="access-for-custom-linux-image"></a>Acesso para a imagem personalizada do Linux
Para habilitar o console serial para a imagem de VM do Linux personalizada, habilite o acesso ao console em /etc/inittab para executar um terminal em ttyS0. A seguir, um exemplo para adicionar isso no arquivo inittab 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Errors
A maioria dos erros é temporária, e é resolvida quando você tenta estabelecer novamente a conexão. A tabela abaixo mostra uma lista de erros e mitigação 

Erro                            |   Redução 
:---------------------------------|:--------------------------------------------|
Não é possível recuperar as configurações de diagnóstico de inicialização para '<VMNAME>'. Para usar o console serial, verifique se o diagnóstico de inicialização está habilitado para essa VM. | Verifique se a VM tem [diagnósticos de inicialização](boot-diagnostics.md) habilitados. 
A VM está em estado desalocado interrompido. Inicie a máquina virtual e tente estabelecer novamente a conexão de console serial. | A máquina virtual deve estar em um estado iniciado para acessar o console serial
Você não tem as permissões necessárias para usar essa VM por meio do console serial. Verifique se você tem pelo menos permissões de função de Colaborador de VM.| O acesso ao console serial requer determinada permissão de acesso. Confira [requisitos de acesso](#prerequisites) para obter detalhes
Não é possível determinar o grupo de recursos para a conta de armazenamento de diagnóstico de inicialização '<STORAGEACCOUNTNAME>'. Verifique se o diagnóstico de inicialização está habilitado para essa VM e se você tem acesso a essa conta de armazenamento. | O acesso ao console serial requer determinada permissão de acesso. Confira [requisitos de acesso](#prerequisites) para obter detalhes

## <a name="known-issues"></a>Problemas conhecidos 
Como ainda estamos na fase de visualização para acesso ao console serial, estamos lidando com alguns problemas conhecidos. Abaixo está a lista deles, com possíveis soluções alternativas 

Problema                           |   Redução 
:---------------------------------|:--------------------------------------------|
Não há uma opção com o console serial de instância de conjunto de escala de máquina virtual |  No momento da visualização, não há suporte para acesso ao console serial para instâncias de conjunto de escala de máquina virtual.
Pressionar enter após a faixa de conexão não mostra um log no prompt | [O pressionamento de enter não executa nenhuma ação](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Perguntas frequentes 
**P. Como posso enviar comentários?**

a. Forneça comentários sobre um problema acessando https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), envie comentários por meio de azserialhelp@microsoft.com ou na categoria da máquina virtual de http://feedback.azure.com

**P. Recebo um erro "Console existente tem um tipo de sistema operacional conflitante"Windows"com o tipo solicitado do sistema operacional do Linux?"**

a. Esse é um problema conhecido. Para corrigi-lo, basta abrir o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) no modo de bash e tentar novamente.

**P. Não consigo acessar o console serial. Onde posso registrar um caso de suporte?**

a. Esse recurso de visualização é abrangido por meio de Termos de Visualização do Azure. O suporte para isso é manipulado melhor por meio dos canais mencionados acima. 

## <a name="next-steps"></a>Próximas etapas
* O console serial também está disponível para VMs do [Windows](../windows/serial-console.md)
* Saiba mais sobre [bootdiagnostics](boot-diagnostics.md)