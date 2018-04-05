---
title: Console serial da máquina virtual do Azure | Microsoft Docs
description: Console serial bidirecional para máquinas virtuais do Azure.
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
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 9c16114b4f8d335dc750b1beef1fb6204ae20e0f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Console serial da máquina virtual (visualização) 


O console serial da máquina virtual no Azure fornece acesso a um console baseado em texto para máquinas virtuais Linux e Windows. Essa conexão serial é para a porta serial COM1 da máquina virtual, fornece acesso à máquina virtual e não está relacionada à rede da máquina virtual/estado do sistema operacional. No momento, o acesso ao console serial para uma máquina virtual pode ser obtido somente por meio do portal do Azure e é permitido apenas para os usuários que têm acesso de Colaborador de VM ou superior à máquina virtual. 

> [!Note] 
> As visualizações são disponibilizadas a você se concordar com os termos de uso. Para saber mais, confira [Termos de Uso do Microsoft Azure para Visualizações do Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Atualmente, este serviço está em **visualização pública**, e o acesso ao console serial para máquinas virtuais está disponível para regiões globais do Azure. Neste ponto, o console serial não está disponível por meio de Azure Government, Azure Germany e Azure China cloud.

 

## <a name="prerequisites"></a>pré-requisitos 

* A máquina virtual deve ter os [diagnósticos de inicialização](boot-diagnostics.md) habilitados 
* A conta que usa o console serial deve ter a [função de Colaborador](../../active-directory/role-based-access-built-in-roles.md) para a VM e a conta de armazenamento [diagnósticos de inicialização](boot-diagnostics.md). 

## <a name="open-the-serial-console"></a>Abra o console serial
o console serial de máquinas virtuais só pode ser acessado por meio do [portal do Azure](https://portal.azure.com). A seguir, as etapas para acessar o console serial para máquinas virtuais por meio do portal 

  1. Abrir o portal do Azure
  2. No menu à esquerda, selecione as máquinas virtuais.
  3. Clique na VM na lista. A página de visão geral para a VM será aberta.
  4. Role para baixo até a seção Suporte + Solução de Problemas e clique na opção de console serial (visualização). Um novo painel com o console serial será aberto e iniciará a conexão.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>Desabilitar recurso
A funcionalidade do console serial pode ser desativada para VMs específicas desabilitando a configuração de diagnóstico de inicialização da VM.

## <a name="serial-console-security"></a>Segurança de console serial 

### <a name="access-security"></a>Segurança de acesso 
O acesso ao console serial é limitado aos usuários que têm acesso de [Colaboradores de VM](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) ou superior para a máquina virtual. Se o locatário do AAD exigir a Autenticação Multifator, o acesso ao console serial também exigirá MFA, pois seu acesso é por meio do [portal do Azure](https://portal.azure.com).

### <a name="channel-security"></a>Segurança de canal
Todos os dados são enviados criptografados durante a transmissão.

### <a name="audit-logs"></a>Logs de auditoria
Todo o acesso ao console serial está conectado a logs de [diagnósticos de inicialização](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) da máquina virtual. O acesso a esses logs pertence e é controlado pelo administrador da máquina virtual do Azure.  

>[!CAUTION] 
Embora nenhuma senha de acesso para o console seja registrada, se comandos executados dentro do console contiverem ou gerarem senhas, segredos, nomes de usuário ou qualquer outra forma de PII (Informações de Identificação Pessoal), eles serão gravados nos logs de diagnóstico de inicialização da máquina virtual, juntamente com todo o outro texto visível, como parte da implementação da funcionalidade de scrollback do console serial. Esses logs são circulares, e somente pessoas com permissões de leitura para a conta de armazenamento de diagnóstico têm acesso a eles. No entanto, recomendamos que você siga a prática recomendada de usar o console do Desktop Remoto para qualquer item que envolva segredos e/ou PII. 

### <a name="concurrent-usage"></a>Uso simultâneo
Se um usuário estiver conectado ao console serial e outro usuário solicitar com êxito o acesso à mesma máquina virtual, o primeiro usuário será desconectado, e o segundo usuário será conectado de maneira semelhante ao primeiro usuário, aguardando e saindo do console físico, com um novo usuário aguardando.

>[!CAUTION] 
Isso significa que o usuário que é desconectado não será fará logoff! A capacidade de impor um logoff após a desconexão (via SIGHUP ou um mecanismo semelhante) ainda está em nossos planos. Para o Windows, há um tempo limite automático habilitado no SAC. Porém, para Linux, você pode definir a configuração de tempo limite de terminal. 


## <a name="accessing-serial-console-for-windows"></a>Acessar o console serial para Windows 
As imagens mais recentes do Windows Server no Azure terão o [Console de Administração Especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) habilitado por padrão. O SAC tem suporte a versões de servidor do Windows, mas não está disponível em versões de cliente (por exemplo, Windows 10, Windows 8 ou Windows 7). Para habilitar o console Serial para máquinas virtuais do Windows criado usando Feb2018 ou imagens inferiores faça as seguintes etapas: 

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

Se você precisar habilitar os prompts do carregador de inicialização do Windows para mostrar no console serial, você pode adicionar as seguintes opções adicionais para o carregador de inicialização do Windows.

1. Conecte-se à sua máquina virtual do Windows por meio da Área de Trabalho Remota
2. A partir de um prompt de comando Administrativo, execute os comando a seguir 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Reinicie o sistema para que o menu de inicialização seja habilitado

> [!NOTE] 
> Nesse suporte de ponto para a funções de chave não está habilitado, se você precisar de opções de inicialização avançadas use bcdedit /set {current} onetimeadvancedoptions, consulte [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) para obter mais detalhes

## <a name="common-scenarios-for-accessing-windows-serial-console"></a>Cenários comuns para acessar o console serial do Windows 
Cenário          | Ações no console serial                
:------------------|:----------------------------------------
Regras de firewall incorretas | Acessar o console serial e corrigir iptables ou regras de firewall do Windows 
Corrupção de sistema de arquivos/verificação | Acessar o console serial e recuperar o sistema de arquivos depois de fazer logon no SAC CMD
Problemas de configuração de RDP | Acessar o console serial e fazer logon no canal de cmd. Verifique a integridade dos serviços de Terminal e reinicie se necessário.
Bloqueio de sistema de rede| Acessar o console serial e fazer logon no canal de cmd. Verifique o status do firewall pela linha de comando [netsh](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts). 

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
Não há uma opção com o console serial de instância de conjunto de escala de máquina virtual | No momento da visualização, não há suporte para acesso ao console serial para instâncias de conjunto de escala de máquina virtual.
Pressionar enter após a faixa de conexão não mostra um log no prompt | [O pressionamento de enter não executa nenhuma ação](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Apenas as informações de integridade são mostradas ao se conectar a uma VM do Windows| [Sinais de integridade do Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)

## <a name="frequently-asked-questions"></a>Perguntas frequentes 
**P. Como posso enviar comentários?**

a. Forneça comentários sobre um problema acessando https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), envie comentários por meio de azserialhelp@microsoft.com ou na categoria da máquina virtual de http://feedback.azure.com 

**P. Recebo um erro "Console existente tem um tipo de sistema operacional conflitante"Windows"com o tipo solicitado do sistema operacional do Linux?**

a. Esse é um problema conhecido. Para corrigi-lo, basta abrir o [Shell de Nuvem do Azure](https://docs.microsoft.com/azure/cloud-shell/overview) no modo de bash e tentar novamente.

**P. Não consigo acessar o console serial. Onde posso registrar um caso de suporte?**

a. Esse recurso de visualização é abrangido por meio de Termos de Visualização do Azure. O suporte para isso é manipulado melhor por meio dos canais mencionados acima. 

## <a name="next-steps"></a>Próximas etapas
* O console serial também está disponível para VMs do [Linux](../linux/serial-console.md)
* Saiba mais sobre [bootdiagnostics](boot-diagnostics.md)
