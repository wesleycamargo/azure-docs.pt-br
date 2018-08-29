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
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: 66354db65d5e615780ec49683fbc72f1156ac5e1
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "42140816"
---
# <a name="virtual-machine-serial-console-preview"></a>Console Serial da Máquina Virtual (versão prévia) 


O Console Serial da Máquina Virtual no Azure fornece acesso a um console baseado em texto para máquinas virtuais do Windows e Linux. Essa conexão serial é para a porta serial COM1 da máquina virtual, fornece acesso à máquina virtual e não está relacionada à rede da máquina virtual/estado do sistema operacional. No momento, o acesso ao console serial para uma máquina virtual pode ser obtido somente por meio do portal do Azure e é permitido apenas para os usuários que têm acesso de Colaborador de VM ou superior à máquina virtual. 

Para obter a documentação do console serial para VMs do Linux, [clique aqui](../linux/serial-console.md).

> [!Note] 
> As visualizações são disponibilizadas a você se concordar com os termos de uso. Para saber mais, confira [Termos de Uso do Microsoft Azure para Visualizações do Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Atualmente, este serviço está em **visualização pública**, e o acesso ao console serial para máquinas virtuais está disponível para regiões globais do Azure. Neste ponto, o console serial não está disponível por meio de Azure Governamental, Azure Alemanha e Azure China cloud.

 

## <a name="prerequisites"></a>Pré-requisitos 

* Você deve estar usando o modelo de implantação do Gerenciamento de Recursos. Implantações clássicas não possuem suporte. 
* A máquina virtual deve ter os [diagnósticos de inicialização](boot-diagnostics.md) habilitados 
* A conta que usa o console serial deve ter a [função de Colaborador](../../role-based-access-control/built-in-roles.md) para a VM e a conta de armazenamento [diagnósticos de inicialização](boot-diagnostics.md). 

## <a name="open-the-serial-console"></a>Abrir o Console Serial
O console serial para máquinas virtuais é acessível apenas por meio do [portal do Azure](https://portal.azure.com). A seguir, as etapas para acessar o console serial para máquinas virtuais por meio do portal 

  1. Abrir o portal do Azure
  2. No menu à esquerda, selecione as máquinas virtuais.
  3. Clique na VM na lista. A página de visão geral para a VM será aberta.
  4. Role para baixo até a seção Suporte + Solução de Problemas e clique na opção de console serial (visualização). Um novo painel com o console serial será aberto e iniciará a conexão.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

## <a name="disable-serial-console"></a>Desabilitar o Console Serial
Por padrão, todas as assinaturas têm acesso de console serial habilitado para todas as VMs. É possível desabilitar o console serial no nível da assinatura ou no nível da VM.

### <a name="subscription-level-disable"></a>Desabilitação no nível da assinatura
O Console Serial pode ser desabilitado para uma assinatura inteira através da opção [Desabilitar chamada à API REST do console](https://aka.ms/disableserialconsoleapi). É possível usar a funcionalidade "Experimente" disponível na página Documentação da API para desabilitar e habilitar o Console Serial para uma assinatura. Insira `subscriptionId`, "padrão"no campo `default` e clique em Executar. Os comandos da CLI do Azure ainda não estão disponíveis e serão disponibilizados futuramente. [Tente a chamada à API REST aqui](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Como alternativa, é possível usar o conjunto de comandos abaixo no Cloud Shell (comandos de bash mostrados) para desabilitar, habilitar e exibir o status desabilitado do console serial para uma assinatura. 

* Para obter o status desabilitado do console serial para uma assinatura:
    ```
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Para desabilitar o console serial para uma assinatura:
    ```
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Para habilitar o console serial para uma assinatura:
    ```
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


## <a name="access-serial-console-for-windows"></a>Acessar o Console Serial para Windows 
As imagens mais recentes do Windows Server no Azure terão o [Console de Administração Especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) habilitado por padrão. O SAC tem suporte a versões de servidor do Windows, mas não está disponível em versões de cliente (por exemplo, Windows 10, Windows 8 ou Windows 7). Para habilitar o console Serial para máquinas virtuais do Windows criado usando Feb2018 ou imagens inferiores, use as etapas a seguir: 

1. Conecte-se à sua máquina virtual do Windows por meio da Área de Trabalho Remota
2. A partir de um prompt de comando Administrativo, execute os comando a seguir 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Reinicie o sistema para que o console do SAC esteja habilitado

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se necessário SAC pode ser habilitado offline também, 

1. Anexe o disco do Windows que você deseja o SAC configurado para um disco de dados em VM existente. 
2. A partir de um prompt de comando Administrativo, execute os comando a seguir 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Como saber se o SAC está habilitado ou não 

Se o [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) não estiver habilitado, o console serial não mostrará o prompt do SAC. Ele pode mostrar uma informações de integridade da VM em alguns casos ou estará em branco.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Habilitar menu de inicialização para exibir no console serial 

Se você precisar habilitar os prompts do carregador de inicialização do Windows para exibição no console serial, poderá adicionar as seguintes opções adicionais para o carregador de inicialização do Windows.

1. Conecte-se à sua máquina virtual do Windows por meio da Área de Trabalho Remota
2. A partir de um prompt de comando Administrativo, execute os comando a seguir 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Reinicie o sistema para que o menu de inicialização seja habilitado

> [!NOTE] 
> Nesse suporte de ponto para a funções de chave não está habilitado, se você precisar de opções de inicialização avançadas use bcdedit /set {current} onetimeadvancedoptions, consulte [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) para obter mais detalhes

## <a name="using-serial-console-for-nmi-calls-in-windows-vms"></a>Usar o Console Serial para chamadas NMI em VMs do Windows
Uma interrupção não mascarável (NMI) foi projetada para criar um sinal que o software em uma máquina virtual não irá ignorar. Historicamente, as NMIs são usadas para monitorar os problemas de hardware em sistemas que necessitam de tempos de resposta específicos.  Hoje em dia, os programadores e os administradores do sistema geralmente usam a NMI como um mecanismo para depurar ou solucionar problemas de sistemas que estão parados.

O Console Serial pode ser usado para enviar uma NMI a uma máquina virtual do Azure usando o ícone de teclado na barra de comandos mostrada abaixo. Depois que a NMI for entregue, a configuração de máquina virtual controlará como o sistema responde. O Windows pode ser configurado para falhar e criar um despejo de memória ao receber uma NMI.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Para obter informações sobre como configurar o Windows para criar um despejo de memória quando receber uma NMI, consulte: [Como gerar um arquivo de despejo completo ou um arquivo de despejo de falha de kernel usando uma NMI em um sistema baseado em Windows](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)


## <a name="errors"></a>Errors
A maioria dos erros é temporária, e é resolvida quando você tenta estabelecer novamente a conexão. A tabela abaixo mostra uma lista de erros e mitigação 

Erro                            |   Redução 
:---------------------------------|:--------------------------------------------|
Não é possível recuperar as configurações de diagnóstico de inicialização para '<VMNAME>'. Para usar o console serial, verifique se o diagnóstico de inicialização está habilitado para essa VM. | Verifique se a VM tem [diagnósticos de inicialização](boot-diagnostics.md) habilitados. 
A VM está em estado desalocado interrompido. Inicie a máquina virtual e tente estabelecer novamente a conexão de console serial. | A máquina virtual deve estar em um estado iniciado para acessar o console serial
Você não tem as permissões necessárias para usar essa VM por meio do console serial. Verifique se você tem pelo menos permissões de função de Colaborador de VM.| O acesso ao console serial requer determinada permissão de acesso. Confira [requisitos de acesso](#prerequisites) para obter detalhes
Não é possível determinar o grupo de recursos para a conta de armazenamento de diagnóstico de inicialização '<STORAGEACCOUNTNAME>'. Verifique se o diagnóstico de inicialização está habilitado para essa VM e se você tem acesso a essa conta de armazenamento. | O acesso ao console serial requer determinada permissão de acesso. Confira [requisitos de acesso](#prerequisites) para obter detalhes
Uma resposta "Proibida" foi encontrada ao acessar a conta de armazenamento de diagnóstico de inicialização dessa VM. | Assegure-se de que o diagnóstico de inicialização não tem um firewall de conta. Uma conta de armazenamento de diagnóstico de inicialização acessível é necessária para o console serial funcionar.
O soquete da Web está fechado ou não pôde ser aberto. | Talvez seja necessário colocar `*.console.azure.com` na lista de permissões. Uma abordagem mais detalhada, porém mais longa, é colocar na lista de permissões os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), que são alterados regularmente.

## <a name="known-issues"></a>Problemas conhecidos 
Como ainda estamos na fase de visualização para acesso ao console serial, estamos lidando com alguns problemas conhecidos. Abaixo está a lista deles, com possíveis soluções alternativas 

Problema                             |   Redução 
:---------------------------------|:--------------------------------------------|
Não há uma opção com o console serial de instância de conjunto de escala de máquina virtual | No momento da visualização, não há suporte para acesso ao console serial para instâncias de conjunto de escala de máquina virtual.
Pressionar enter após a faixa de conexão não mostra um log no prompt | [O pressionamento de enter não executa nenhuma ação](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Apenas as informações de integridade são mostradas ao se conectar a uma VM do Windows| [Sinais de integridade do Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
Não será possível digitar no prompt do SAC se a depuração de kernel estiver habilitada | RDP para VM e execute `bcdedit /debug {current} off` a partir de um prompt de comandos com privilégios elevados. Se não for possível o RDP, você poderá anexar o disco de SO a outra VM do Azure e modificá-lo enquanto estiver conectado como um disco de dados usando `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` e, em seguida, trocar o disco de volta.
Colar no PowerShell nos resultados do SAC em um terceiro caractere se o conteúdo original tinha um caractere de repetição | Uma solução alternativa é remover o módulo PSReadLine. `Remove-Module PSReadLine` removerá o módulo PSReadLine da sessão atual.
Algumas entradas de teclado produzem saída de SAC estranha (por exemplo, `[A`, `[3~`) | Sequência de escape [VT100](https://aka.ms/vtsequences) não são compatíveis com o prompt do SAC.
Uma resposta "Proibida" foi encontrada ao acessar a conta de armazenamento de diagnóstico de inicialização dessa VM. | Assegure-se de que o diagnóstico de inicialização não tem um firewall de conta. Uma conta de armazenamento de diagnóstico de inicialização acessível é necessária para o console serial funcionar.

## <a name="frequently-asked-questions"></a>Perguntas frequentes 
**P. Como posso enviar comentários?**

a. Forneça comentários sobre um problema acessando https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), envie comentários por meio de azserialhelp@microsoft.com ou na categoria da máquina virtual de http://feedback.azure.com

**P. Não consigo acessar o console serial. Onde posso registrar um caso de suporte?**

a. Esse recurso de visualização é abrangido por meio de Termos de Visualização do Azure. O suporte para isso é manipulado melhor por meio dos canais mencionados acima. 

## <a name="next-steps"></a>Próximas etapas
* Para obter um guia detalhado para comandos CMD e do PowerShell que você pode usar no SAC Windows, clique [aqui](serial-console-cmd-ps-commands.md).
* O Console Serial também está disponível para VMs do [Linux](../linux/serial-console.md).
* Saiba mais sobre [diagnóstico de inicialização](boot-diagnostics.md).
