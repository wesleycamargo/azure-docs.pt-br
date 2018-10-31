---
title: Console Serial da Máquina Virtual do Azure | Microsoft Docs
description: Console Serial Bidirecional para máquinas virtuais do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: harijay
ms.openlocfilehash: facd9be037894932e516e8294e36b6b0e55374c8
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024400"
---
# <a name="virtual-machine-serial-console"></a>Console Serial da Máquina Virtual


O console serial de máquina virtual no Azure fornece acesso a um console baseado em texto para máquinas virtuais do Windows. Essa conexão serial é para a porta serial COM1 da máquina virtual, fornecendo acesso à máquina virtual que é independente do estado da rede ou sistema operacional de uma máquina virtual. O acesso ao console serial de uma máquina virtual só pode ser feito no momento pelo portal do Azure e é permitido somente para usuários com acesso de VM ou superior à máquina virtual. 

Para obter a documentação do console serial para VMs do Linux, [clique aqui](serial-console-linux.md).

> [!NOTE] 
> O Console Serial para máquinas virtuais geralmente está disponível em regiões globais do Azure. Neste ponto, o Console Serial ainda não está disponível nas nuvens do Microsoft Azure Governamental ou Azure China.

 

## <a name="prerequisites"></a>Pré-requisitos 

* Você deve estar usando o modelo de implantação do Gerenciamento de Recursos. Implantações clássicas não possuem suporte. 
* A máquina virtual deve ter os [diagnósticos de inicialização](boot-diagnostics.md) habilitados 

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* A conta que usa o console serial deve ter a [função de Colaborador](../../role-based-access-control/built-in-roles.md) para a VM e a conta de armazenamento [diagnósticos de inicialização](boot-diagnostics.md). 
* A máquina virtual para a qual você está acessando o console serial também deve ter uma conta baseada em senha. Você pode criar uma com a funcionalidade de [redefinição de senha](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso à VM. Para isso, veja a captura de tela abaixo.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

## <a name="get-started-with-serial-console"></a>Introdução ao console serial
O console serial para máquinas virtuais é acessível apenas por meio do [portal do Azure](https://portal.azure.com). Abaixo estão as etapas para acessar o console serial para máquinas virtuais por meio do portal.

  1. Abrir o portal do Azure
  2. No menu à esquerda, selecione as máquinas virtuais.
  3. Clique na VM na lista. A página de visão geral para a VM será aberta.
  4. Role para baixo até a seção Suporte + Solução de problemas e clique na opção "Console serial". Um novo painel com o console serial será aberto e iniciará a conexão.

## <a name="enable-serial-console-in-custom-or-older-images"></a>Habilitar o Console Serial em imagens personalizadas ou anteriores
As imagens mais recentes do Windows Server no Azure terão o [Console de Administração Especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) habilitado por padrão. O SAC tem suporte a versões de servidor do Windows, mas não está disponível em versões de cliente (por exemplo, Windows 10, Windows 8 ou Windows 7). Para habilitar o console serial para máquinas virtuais do Windows criadas antes de fevereiro de 2018, use as seguintes etapas: 

1. Conecte-se à sua máquina virtual do Windows por meio da Área de Trabalho Remota
2. A partir de um prompt de comando Administrativo, execute os comando a seguir 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Reinicie o sistema para que o console do SAC esteja habilitado

![](/media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se necessário, o SAC pode ser habilitado offline também:

1. Anexe o disco do Windows que você deseja o SAC configurado para um disco de dados em VM existente. 
2. A partir de um prompt de comando Administrativo, execute os comando a seguir 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled"></a>Como sei se o SAC está habilitado?

Se o [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) não estiver habilitado, o console serial não mostrará o prompt do SAC. Em alguns casos, as informações da VM Health serão exibidas e, em outros casos, ficarão em branco. Se você estiver usando uma imagem do Windows Server criada antes de fevereiro de 2018, o SAC provavelmente não estará habilitado.

## <a name="enable-the-windows-boot-menu-in-serial-console"></a>Ativar o menu de inicialização do Windows no console serial 

Se você precisar ativar os prompts do carregador de inicialização do Windows para exibição no console serial, poderá adicionar as seguintes opções adicionais aos dados de configuração da inicialização. Ver [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) para obter mais detalhes

1. Conecte-se à sua máquina virtual do Windows por meio da Área de Trabalho Remota
2. A partir de um prompt de comando Administrativo, execute os comando a seguir 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 10`
* `bcdedit /set {bootmgr} bootems yes`
3. Reinicie o sistema para que o menu de inicialização seja habilitado

> [!NOTE] 
> O tempo limite que você definiu para o menu do gerenciador de inicialização para exibir terá impacto sobre o tempo de inicialização do sistema operacional no futuro. Embora seja aceitável para algumas adicionar um tempo de limite 10 segundos para garantir que o gerenciador de inicialização esteja visível por meio do console serial, outras pessoas talvez queiram um tempo limite maior ou menor. Defina o valor de tempo limite para um valor que você estiver familiarizado.

## <a name="use-serial-console-for-nmi-calls-in-windows-vms"></a>Usar o Console Serial para chamadas de NMI em VMs do Windows
Uma interrupção não mascarável (NMI) foi projetada para criar um sinal que o software em uma máquina virtual não irá ignorar. Historicamente, as NMIs são usadas para monitorar os problemas de hardware em sistemas que necessitam de tempos de resposta específicos.  Hoje em dia, os programadores e os administradores do sistema geralmente usam a NMI como um mecanismo para depurar ou solucionar problemas de sistemas que estão parados.

O Console Serial pode ser usado para enviar uma NMI a uma máquina virtual do Azure usando o ícone de teclado na barra de comandos mostrada abaixo. Depois que a NMI for entregue, a configuração de máquina virtual irá controlar como o sistema responde. O Windows pode ser configurado para falhar e criar um despejo de memória ao receber uma NMI.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Para obter informações sobre como configurar o Windows para criar um despejo de memória quando receber uma NMI, consulte: [Como gerar um arquivo de despejo completo ou um arquivo de despejo de falha de kernel usando uma NMI em um sistema baseado em Windows](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)

## <a name="open-cmd-or-powershell-in-serial-console"></a>Abra o CMD ou o Powershell no Console Serial

1. Conecte-se ao Console Serial. Se você se conectar com êxito ao Console Serial, você verá **SAC >** como a captura de tela a seguir mostra:

    ![Conectar ao SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

3.  Digite `cmd` para criar um canal que tem uma instância CMD. 
4.  Digite `ch -si 1` para alternar para o canal que está executando a instância CMD. 
5.  Pressione Enter e, em seguida, insira suas credenciais de logon que tem permissão administrativa.
6.  Depois de inserir credenciais válidas, a instância CMD é aberta.
7.  Para iniciar uma instância do PowerShell, digite `PowerShell` na CMD da instância e, em seguida, pressione Enter. 

    ![Abra o PowerShell e execute](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)


## <a name="disable-serial-console"></a>Desabilitar o Console Serial
Por padrão, todas as assinaturas têm acesso de console serial habilitado para todas as VMs. É possível desabilitar o console serial no nível da assinatura ou no nível da VM.

> [!NOTE]       
> Para habilitar ou desabilitar o console serial para uma assinatura, você precisa ter permissões de gravação para a assinatura. Isso inclui, sem limitação, funções de administrador ou proprietário. Funções personalizadas também poderão ter permissões de gravação.

### <a name="subscription-level-disable"></a>Desabilitação no nível da assinatura
O Console Serial pode ser desabilitado para uma assinatura inteira através da opção [Desabilitar chamada à API REST do console](https://aka.ms/disableserialconsoleapi). É possível usar a funcionalidade "Experimente" disponível na página Documentação da API para desabilitar e habilitar o Console Serial para uma assinatura. Insira `subscriptionId`, "padrão"no campo `default` e clique em Executar. Os comandos da CLI do Azure ainda não estão disponíveis e serão disponibilizados futuramente. [Tente a chamada à API REST aqui](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

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
Embora nenhuma senha de acesso para o console seja registrada, se comandos executados dentro do console contiverem ou gerarem senhas, segredos, nomes de usuários ou qualquer outra forma de PII (Informações de Identificação Pessoal), eles serão gravados nos logs de diagnóstico de inicialização da máquina virtual, juntamente com todo o outro texto visível, como parte da implementação da funcionalidade de scrollback do console serial. Esses logs são circulares, e somente pessoas com permissões de leitura para a conta de armazenamento de diagnóstico têm acesso a eles. No entanto, recomendamos que você siga a prática recomendada de usar o console do Desktop Remoto para qualquer item que envolva segredos e/ou PII. 

### <a name="concurrent-usage"></a>Uso simultâneo
Se um usuário estiver conectado ao console serial e outro usuário solicitar com êxito o acesso à mesma máquina virtual, o primeiro usuário será desconectado, e o segundo usuário será conectado de maneira semelhante ao primeiro usuário, aguardando e saindo do console físico, com um novo usuário aguardando.

>[!CAUTION] 
Isso significa que o usuário que é desconectado não será fará logoff! A capacidade de impor um logoff após a desconexão (via SIGHUP ou um mecanismo semelhante) ainda está em nossos planos. Para o Windows, há um tempo limite automático habilitado no SAC. Porém, para Linux, você pode definir a configuração de tempo limite de terminal. 

## <a name="common-scenarios-for-accessing-serial-console"></a>Cenários comuns para acessar o console serial 
Cenário          | Ações no console serial                
:------------------|:-----------------------------------------
Regras de firewall incorretas | Acesse regras de firewall de Windows seriais de console e correção. 
Corrupção de sistema de arquivos/verificação | Acesse o console serial e recupere o sistema de arquivos. 
Problemas de configuração de RDP | Acesse o console serial e altere as configurações. Vá para o [documentação do RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access) para começar a usar.
Bloqueio de sistema de rede| Acesse o console serial através do portal para gerenciar o sistema. Alguns comandos de rede são listados na documentação do [CMD do console serial e do PowerShell](serial-console-cmd-ps-commands.md). 
Interagir com o carregador de inicialização | Acesse o BCD através do console serial. Vá para [Ativando o menu de inicialização para mostrar no console serial](#enabling-boot-menu-to-show-in-the-serial-console) para começar. 

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é o foco principal do console serial do Azure. Para esse fim, garantimos que o console serial seja acessível para pessoas com deficiências visuais e auditivas, bem como para pessoas que não possam usar o mouse.

### <a name="keyboard-navigation"></a>Navegação de teclado
Use a tecla `tab` no teclado para navegar pela interface do console serial no portal Aure. Sua localização será destacada na tela. Para deixar o foco do blade do console serial, pressione `Ctrl + F6` no teclado.

### <a name="use-serial-console-with-a-screen-reader"></a>Use o console serial com um leitor de tela
Console serial vem com suporte para leitor de tela embutido. Navegar ao redor com um leitor de tela ativado permitirá que o texto alternativo do botão atualmente selecionado seja lido em voz alta pelo leitor de tela.

## <a name="errors"></a>Errors
A maioria dos erros é temporária, e é resolvida quando você tenta estabelecer novamente a conexão. A tabela abaixo mostra uma lista de erros e mitigações

Erro                            |   Redução 
:---------------------------------|:--------------------------------------------|
Não é possível recuperar as configurações de diagnóstico de inicialização para '<VMNAME>'. Para usar o console serial, verifique se o diagnóstico de inicialização está habilitado para essa VM. | Verifique se a VM tem [diagnósticos de inicialização](boot-diagnostics.md) habilitados. 
A VM está em estado desalocado interrompido. Inicie a máquina virtual e tente estabelecer novamente a conexão de console serial. | A máquina virtual deve estar em um estado iniciado para acessar o console serial
Você não tem as permissões necessárias para usar essa VM por meio do console serial. Verifique se você tem pelo menos permissões de função de Colaborador de VM.| O acesso ao console serial requer determinada permissão de acesso. Confira [requisitos de acesso](#prerequisites) para obter detalhes
Não é possível determinar o grupo de recursos para a conta de armazenamento de diagnóstico de inicialização '<STORAGEACCOUNTNAME>'. Verifique se o diagnóstico de inicialização está habilitado para essa VM e se você tem acesso a essa conta de armazenamento. | O acesso ao console serial requer determinada permissão de acesso. Confira [requisitos de acesso](#prerequisites) para obter detalhes
Uma resposta "Proibida" foi encontrada ao acessar a conta de armazenamento de diagnóstico de inicialização dessa VM. | Assegure-se de que o diagnóstico de inicialização não tem um firewall de conta. Uma conta de armazenamento de diagnóstico de inicialização acessível é necessária para o console serial funcionar.
O soquete da Web está fechado ou não pôde ser aberto. | Talvez seja necessário colocar `*.console.azure.com` na lista de permissões. Uma abordagem mais detalhada, porém mais longa, é colocar na lista de permissões os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), que são alterados regularmente.
Apenas as informações de integridade são mostradas ao se conectar a uma VM do Windows| Isso mostrará se o Console de Administração Especial não foi habilitado para sua imagem do Windows. Ver [Acessar o Console Serial para Windows](#access-serial-console-for-windows) para obter instruções sobre como habilitar manualmente o SAC em sua VM do Windows. Mais detalhes podem ser encontrados em [Sinais de integridade do Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Problemas conhecidos 
Estamos cientes de alguns problemas com o console serial. Aqui está uma lista desses problemas e das etapas de mitigação.

Problema                             |   Redução 
:---------------------------------|:--------------------------------------------|
Pressionar enter após a faixa de conexão não mostra um log no prompt | Veja esta página: [Pressionar enter não resulta em nenhuma ação](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Isso pode acontecer se você estiver executando uma VM personalizada, um dispositivo protegido ou uma configuração que impeça o Windows de se conectar corretamente à porta serial. Isso também ocorrerá se você estiver executando uma VM, de cliente do Windows 10 como somente as VMs do Windows Server são configuradas para ter EMS habilitada.
Não será possível digitar no prompt do SAC se a depuração de kernel estiver habilitada | RDP para VM e execute `bcdedit /debug {current} off` a partir de um prompt de comandos com privilégios elevados. Se não for possível o RDP, você poderá anexar o disco de SO a outra VM do Azure e modificá-lo enquanto estiver conectado como um disco de dados usando `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` e, em seguida, trocar o disco de volta.
Colar no PowerShell nos resultados do SAC em um terceiro caractere se o conteúdo original tinha um caractere de repetição | Uma solução alternativa é descarregar o passado do módulo PSReadLine da sessão atual. Execute `Remove-Module PSReadLine` para descarregar o módulo PSReadLine da sessão atual. Isso não excluirá ou desinstalará o módulo.
Algumas entradas de teclado produzem saída de SAC estranha (por exemplo, `[A`, `[3~`) | Sequência de escape [VT100](https://aka.ms/vtsequences) não são compatíveis com o prompt do SAC.
Colar cadeias de caracteres muito longas não funciona | O Console serial limita o comprimento de cadeias de caracteres coladas no terminal a 2048 caracteres. Isso é para evitar sobrecarregar a largura de banda da porta serial.

## <a name="frequently-asked-questions"></a>Perguntas frequentes 

**P. Como posso enviar comentários?**

a. Forneça comentários sobre um problema acessando https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), envie comentários por meio de azserialhelp@microsoft.com ou na categoria da máquina virtual de http://feedback.azure.com

**P. O console serial dá suporte para copiar/colar?**

a. Sim, ele faz isso. Use Ctrl + Shift + C e Ctrl + Shift + V para copiar e colar no terminal.

**P. Quem pode habilitar ou desabilitar o console serial da minha assinatura?**

a. Para habilitar ou desabilitar o console serial em uma assinatura, você precisa ter permissões de gravação para a assinatura. As funções que têm permissão de gravação incluem, sem limitação, funções de administrador ou proprietário. Funções personalizadas também poderão ter permissões de gravação.

**P. Quem pode acessar o console serial para minha VM?**

a. Você precisa ter acesso de nível de colaborador ou superior a uma VM para acessar o console serial da VM. 

**P. Meu console serial não mostra nada. O que devo fazer?**

a. Sua imagem provavelmente não está configurada corretamente para acesso ao console serial. Consulte [Habilitar o Console Serial em imagens antigas ou personalizadas](#enable-serial-console-in-custom-or-older-images) para encontrar detalhes sobre como configurar sua imagem para habilitar o console serial.

**P. O console serial está disponível para Conjuntos de Dimensionamento de Máquinas Virtuais?**

a. No momento, não há suporte para acesso ao console serial para instâncias de conjunto de dimensionamento de máquinas virtuais.

## <a name="next-steps"></a>Próximas etapas
* Para obter um guia detalhado para comandos CMD e do PowerShell que você pode usar no SAC Windows, clique [aqui](serial-console-cmd-ps-commands.md).
* O Console Serial também está disponível para VMs do [Linux](serial-console-linux.md).
* Saiba mais sobre [diagnóstico de inicialização](boot-diagnostics.md).