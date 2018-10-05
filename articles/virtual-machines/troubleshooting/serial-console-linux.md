---
title: Console Serial da Máquina Virtual do Azure | Microsoft Docs
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
ms.date: 09/11/2018
ms.author: harijay
ms.openlocfilehash: 642bf03ecef7f6db25c51671635d96ef7baed91a
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410917"
---
# <a name="virtual-machine-serial-console"></a>Console Serial da Máquina Virtual


O Console Serial da Máquina Virtual no Azure fornece acesso a um console baseado em texto para máquinas virtuais do Linux. Essa conexão serial é para a porta serial COM1 da máquina virtual, fornecendo acesso à máquina virtual que é independente do estado da rede ou sistema operacional de uma máquina virtual. O acesso ao console serial de uma máquina virtual só pode ser feito no momento pelo portal do Azure e é permitido somente para usuários com acesso de VM ou superior à máquina virtual. 

Para documentação do console serial para VMs do Windows, [clique aqui](../windows/serial-console.md).

> [!Note] 
> O Console Serial para máquinas virtuais geralmente está disponível em regiões globais do Azure. Neste momento, o console serial ainda não está disponível em nuvens do Azure Governamental ou do Azure China.


## <a name="prerequisites"></a>Pré-requisitos 

* Você deve estar usando o modelo de implantação do Gerenciamento de Recursos. Implantações clássicas não possuem suporte. 
* Sua máquina virtual PRECISA ter os [diagnósticos de inicialização](boot-diagnostics.md) habilitados – veja a captura de tela abaixo.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* A conta do Azure que usa o console serial deve ter a [função de Colaborador](../../role-based-access-control/built-in-roles.md) para a VM e a conta de armazenamento [diagnósticos de inicialização](boot-diagnostics.md). 
* A máquina virtual para a qual você está acessando o console serial também deve ter uma conta baseada em senha. Você pode criar uma com a funcionalidade de [redefinição de senha](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso à VM. Para isso, veja a captura de tela abaixo.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

* Para obter configurações específicas de distribuições Linux, consulte [Acesso ao console serial para Linux](#Serial-Console-Linux-distro-availability)



## <a name="get-started-with-serial-console"></a>Introdução ao console serial
O console serial para máquinas virtuais é acessível apenas por meio do [portal do Azure](https://portal.azure.com). A seguir, as etapas para acessar o console serial para máquinas virtuais por meio do portal 

  1. Abrir o portal do Azure
  1. (Ignore isso caso sua VM tenha um usuário que usa autenticação de senha) Adicione um usuário com nome de usuário/autenticação de senha clicando na folha "Redefinir senha"
  1. No menu à esquerda, selecione as máquinas virtuais.
  1. Clique na VM na lista. A página de visão geral para a VM será aberta.
  1. Role para baixo até a seção Suporte + Solução de problemas e clique na opção "Console serial". Um novo painel com o console serial será aberto e iniciará a conexão.

![](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### 

> [!NOTE] 
> O console serial requer um usuário local com uma senha configurada. No momento, as VMs configuradas apenas com uma chave pública SSH não conseguirão entrar no console serial. Para criar um usuário local com senha, use a [Extensão de acesso à VM](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), disponível no portal, clicando em "Redefinir senha", e crie um usuário local com uma senha.
> Também é possível redefinir a senha do administrador na sua conta [usando GRUB para soltar no modo de usuário único](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distro-availability"></a>Disponibilidade de distribuição de Linux do Console Serial
Para que o console serial funcione corretamente, o sistema operacional convidado deve ser configurado para ler e gravar mensagens de console para a porta serial. A maioria das [Distribuições do Linux para Azure aprovadas](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) tem o console serial configurado por padrão. Basta clicar na seção Console Serial no portal do Azure para fornecer acesso ao console. 

Distribuição      | Acesso ao console serial
:-----------|:---------------------
Red Hat Enterprise Linux    | As imagens do Red Hat Enterprise Linux disponíveis no Azure têm acesso ao console habilitado por padrão. 
CentOS      | As imagens do CentOS disponíveis no Azure têm o acesso ao console habilitado por padrão. 
Ubuntu      | As imagens do Ubuntu disponíveis no Azure têm o acesso ao console habilitado por padrão.
CoreOS      | As imagens de CoreOS disponíveis no Azure têm o acesso ao console habilitado por padrão.
SUSE        | Imagens mais recentes do SLES disponíveis no Azure têm o acesso ao console habilitado por padrão. Se você estiver usando versões mais antigas (10 ou inferiores) do SLES no Azure, siga o [	artigo da base de dados](https://www.novell.com/support/kb/doc.php?id=3456486) para habilitar o console serial. 
Oracle Linux        | As imagens do Oracle Linux disponíveis no Azure têm o acesso ao console habilitado por padrão.
Imagens personalizadas do Linux     | Para habilitar o console serial para a imagem de VM do Linux personalizada, habilite o acesso ao console em `/etc/inittab` para executar um terminal em `ttyS0`. Aqui está um exemplo para adicionar isso no arquivo inittab: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Para obter mais informações sobre a criação adequada de imagens personalizadas, consulte [Criar e carregar um VHD do Linux no Azure](https://aka.ms/createuploadvhd). Se estiver criando um kernel personalizado, considere a possibilidade de habilitar os sinalizadores de kernel `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y`. O arquivo de configuração geralmente está localizado em /boot/ para obter uma exploração.

## <a name="common-scenarios-for-accessing-serial-console"></a>Cenários comuns para acessar o console serial 
Cenário          | Ações no console serial                
:------------------|:-----------------------------------------
Arquivo FSTAB danificado | Chave `Enter` para continuar e corrigir o arquivo fstab usando um editor de texto. Talvez seja necessário estar em modo de usuário único para fazer isso. Consulte [como corrigir problemas de fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [Usar o console serial para acessar GRUB e modo de usuário único](serial-console-grub-single-user-mode.md) para começar.
Regras de firewall incorretas | Acesse o console serial e corrija o iptables. 
Corrupção de sistema de arquivos/verificação | Acesse o console serial e recupere o sistema de arquivos. 
Problemas de configuração de RDP/SSH | Acesse o console serial e altere as configurações. 
Bloqueio de sistema de rede| Acesse o console serial através do portal para gerenciar o sistema. 
Interagir com o carregador de inicialização | Acesse o GRUB através do console serial.Access. Vá para [Usar o console serial para acessar GRUB e modo de usuário único](serial-console-grub-single-user-mode.md) para começar. 

## <a name="disable-serial-console"></a>Desabilitar o Console Serial
Por padrão, todas as assinaturas têm acesso de console serial habilitado para todas as VMs. É possível desabilitar o console serial no nível da assinatura ou no nível da VM.

> [!Note] 
> Para habilitar ou desabilitar o console serial para uma assinatura, você precisa ter permissões de gravação para a assinatura. Isso inclui, sem limitação, funções de administrador ou proprietário. Funções personalizadas também poderão ter permissões de gravação.

### <a name="subscription-level-disable"></a>Desabilitação no nível da assinatura
O Console serial pode ser desabilitado para uma assinatura inteira através da opção [Desabilitar chamada à API REST do console](https://aka.ms/disableserialconsoleapi). É possível usar a funcionalidade "Experimentá-lo" disponível na página Documentação da API para desabilitar e habilitar o Console Serial para uma assinatura. Insira `subscriptionId`, "padrão"no campo `default` e clique em Executar. Os comandos da CLI do Azure ainda não estão disponíveis e serão disponibilizados futuramente. [Tente a chamada à API REST aqui](https://aka.ms/disableserialconsoleapi).

![](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Como alternativa, é possível usar o conjunto de comandos abaixo no Cloud Shell (comandos de bash mostrados) para desabilitar, habilitar e exibir o status desabilitado do console serial para uma assinatura. 

* Para obter o status desabilitado do console serial para uma assinatura:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Para desabilitar o console serial para uma assinatura:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Para habilitar o console serial para uma assinatura:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Desabilitação no nível da VM
O Console serial pode ser desabilitado para VMs específicas, desabilitando a configuração de diagnóstico de inicialização dessa VM. Basta desabilitar o diagnóstico de inicialização a partir do portal do Azure e o console serial será desabilitado para a VM.

## <a name="serial-console-security"></a>Segurança do Console Serial 

### <a name="access-security"></a>Segurança de acesso 
O acesso ao console serial é limitado aos usuários que têm acesso de [Colaboradores de VM](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior para a máquina virtual. Se o locatário do AAD exigir a Autenticação Multifator, o acesso ao console serial também exigirá MFA, pois seu acesso é por meio do [portal do Azure](https://portal.azure.com).

### <a name="channel-security"></a>Segurança de canal
Todos os dados enviados são criptografados na rede.

### <a name="audit-logs"></a>Logs de auditoria
Todo o acesso ao console serial está conectado a logs de [diagnósticos de inicialização](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) da máquina virtual. O acesso a esses logs pertence e é controlado pelo administrador da máquina virtual do Azure.  

>[!CAUTION] 
Embora nenhuma senha de acesso para o console seja registrada, se comandos executados dentro do console contiverem ou gerarem senhas, segredos, nomes de usuário ou qualquer outra forma de PII (Informações de Identificação Pessoal), eles serão gravados nos logs de diagnóstico de inicialização da máquina virtual, juntamente com todo o outro texto visível, como parte da implementação da funcionalidade de scrollback do console serial. Esses logs são circulares, e somente pessoas com permissões de leitura para a conta de armazenamento de diagnóstico têm acesso a eles. No entanto, recomendamos que você siga a prática recomendada de usar o console SSH para qualquer item que envolva segredos e/ou PII. 

### <a name="concurrent-usage"></a>Uso simultâneo
Se um usuário estiver conectado ao console serial e outro usuário solicitar com êxito o acesso à mesma máquina virtual, o primeiro usuário será desconectado, e o segundo usuário será conectado de maneira semelhante ao primeiro usuário, aguardando e saindo do console físico, com um novo usuário aguardando.

>[!CAUTION] 
Isso significa que o usuário que é desconectado não será fará logoff! A capacidade de impor um logoff após a desconexão (via SIGHUP ou um mecanismo semelhante) ainda está em nossos planos. Para o Windows, há um tempo limite automático habilitado no SAC. Porém, para Linux, você pode definir a configuração de tempo limite de terminal. Para fazer isso, basta adicionar `export TMOUT=600` a .bash_profile ou .profile para o usuário com o qual você se conectou ao console, para que a sessão atinja o tempo limite após 10 minutos.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é o foco principal do console serial do Azure. Para esse fim, garantimos que o console serial seja acessível para pessoas com deficiências visuais e auditivas, bem como para pessoas que não possam usar o mouse.

### <a name="keyboard-navigation"></a>Navegação de teclado
Use a tecla `tab` no teclado para navegar pela interface do console serial no portal Aure. Sua localização será destacada na tela. Para deixar o foco do blade do console serial, pressione `Ctrl + F6` no teclado.

### <a name="use-serial-console-with-a-screen-reader"></a>Use o console serial com um leitor de tela
Console serial vem com suporte para leitor de tela embutido. Navegar ao redor com um leitor de tela ativado permitirá que o texto alternativo do botão atualmente selecionado seja lido em voz alta pelo leitor de tela.

## <a name="errors"></a>Errors
A maioria dos erros é transitória por natureza e, para tentar novamente, a conexão do console serial geralmente resolve-os. A tabela abaixo mostra uma lista de erros e mitigações

Erro                            |   Redução 
:---------------------------------|:--------------------------------------------|
Não é possível recuperar as configurações de diagnóstico de inicialização para '<VMNAME>'. Para usar o console serial, verifique se o diagnóstico de inicialização está habilitado para essa VM. | Verifique se a VM tem [diagnósticos de inicialização](boot-diagnostics.md) habilitados. 
A VM está em estado desalocado interrompido. Inicie a máquina virtual e tente estabelecer novamente a conexão de console serial. | A máquina virtual deve estar em um estado iniciado para acessar o console serial
Você não tem as permissões necessárias para usar essa VM por meio do console serial. Verifique se você tem pelo menos permissões de função de Colaborador de VM.| O acesso ao console serial requer determinada permissão de acesso. Confira [requisitos de acesso](#prerequisites) para obter detalhes
Não é possível determinar o grupo de recursos para a conta de armazenamento de diagnóstico de inicialização '<STORAGEACCOUNTNAME>'. Verifique se o diagnóstico de inicialização está habilitado para essa VM e se você tem acesso a essa conta de armazenamento. | O acesso ao console serial requer determinada permissão de acesso. Confira [requisitos de acesso](#prerequisites) para obter detalhes
O soquete da Web está fechado ou não pôde ser aberto. | Talvez seja necessário colocar `*.console.azure.com` na lista de permissões. Uma abordagem mais detalhada, porém mais longa, é colocar na lista de permissões os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), que são alterados regularmente.
Uma resposta "Proibida" foi encontrada ao acessar a conta de armazenamento de diagnóstico de inicialização dessa VM. | Assegure-se de que o diagnóstico de inicialização não tem um firewall de conta. Uma conta de armazenamento de diagnóstico de inicialização acessível é necessária para o console serial funcionar.

## <a name="known-issues"></a>Problemas conhecidos 
Estamos cientes de alguns problemas com o console serial. Aqui está uma lista desses problemas e das etapas de mitigação.

Problema                           |   Redução 
:---------------------------------|:--------------------------------------------|
Pressionar enter após a faixa de conexão não mostra um log no prompt | Veja esta página: [Pressionar enter não resulta em nenhuma ação](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Isso pode acontecer se você estiver executando uma VM personalizada, um dispositivo protegido ou uma configuração GRUB que impeça o Linux de se conectar corretamente à porta serial.
O texto do console serial ocupa apenas uma parte do tamanho da tela (geralmente depois de usar um editor de texto) | Os consoles seriais não dão suporte à negociação do tamanho da janela ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), o que significa que nenhum sinal SIGWINCH será enviado para atualizar o tamanho da tela e a VM não saberá o tamanho do seu terminal. É recomendável instalar o xterm ou algum outro utilitário similar que forneça o comando 'redimensionar'. Executar o comando 'redimensionar' corrigirá isso.
Colar cadeias de caracteres muito longas não funciona | O console serial limita o comprimento de cadeias de caracteres coladas no terminal a 2048 caracteres. Isso é para evitar sobrecarregar a largura de banda da porta serial.


## <a name="frequently-asked-questions"></a>Perguntas frequentes 
**P. Como posso enviar comentários?**

a. Forneça comentários sobre um problema acessando https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), envie comentários por meio de azserialhelp@microsoft.com ou na categoria da máquina virtual de http://feedback.azure.com

**P. O console serial dá suporte para copiar/colar?**

a. Sim, ele faz isso. Use Ctrl + Shift + C e Ctrl + Shift + V para copiar e colar no terminal.

**P. É possível usar o console serial, em vez de uma conexão SSH?**

a. Embora isso possa parecer tecnicamente possível, o console serial deve ser usado principalmente como uma ferramenta de solução de problemas em situações em que a conectividade via SSH não seja possível. É recomendável não usar o console serial como substituto de SSH por dois motivos:

1. O console serial não tem a mesma largura de banda que o SSH – é uma conexão somente de texto, portanto, mais interações de interface gráfica do usuário pesada serão difíceis no console serial.
1. O acesso ao console serial é atualmente apenas por nome de usuário e senha. Chaves SSH são muito mais seguras que as combinações de nome de usuário/senha, portanto, de uma perspectiva de segurança de logon, é recomendável SSH no console serial.

**P. Quem pode habilitar ou desabilitar o console serial da minha assinatura?**

a. Para habilitar ou desabilitar o console serial em uma assinatura, você precisa ter permissões de gravação para a assinatura. As funções que têm permissão de gravação incluem, sem limitação, funções de administrador ou proprietário. Funções personalizadas também poderão ter permissões de gravação.

**P. Quem pode acessar o console serial para minha VM?**

a. Você precisa ter acesso de nível de colaborador ou superior a uma VM para acessar o console serial da VM. 

**P. Meu console serial não mostra nada. O que devo fazer?**

a. Sua imagem provavelmente não está configurada corretamente para acesso ao console serial. Consulte [Acessar Console Serial para Linux](#Access-Serial-Console-for-Linux) para aprender a configurar sua imagem a fim de habilitar o console serial.

**P. O console serial está disponível para Conjuntos de Dimensionamento de Máquinas Virtuais?**

a. No momento, não há suporte para acesso ao console serial para instâncias de conjunto de dimensionamento de máquinas virtuais.

**P. Configurei minha VM utilizando apenas autenticação de chave SSH. Ainda posso usar o console serial para me conectar à VM?** R. Sim. Como o console serial não exige chaves SSH, basta configurar uma combinação de nome de usuário/senha. Para fazer isso, utilize a folha "Redefinir senha" no portal e use essas credenciais para entrar no console serial.

## <a name="next-steps"></a>Próximas etapas
* Use o Console Serial para [inicializar no GRUB e entrar no modo de usuário único ](serial-console-grub-single-user-mode.md)
* Use o Console Serial para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md)
* O Console Serial também está disponível para VMs do [Windows](../windows/serial-console.md)
* Saiba mais sobre [diagnóstico de inicialização](boot-diagnostics.md)