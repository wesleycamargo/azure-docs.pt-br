---
title: "Solucionar problemas de backup de máquinas virtuais do Azure | Microsoft Docs"
description: "Solucionar problemas de backup e restauração de máquinas virtuais do Azure"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 73214212-57a4-4b57-a2e2-eaf9d7fde67f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: trinadhk;jimpark;
translationtype: Human Translation
ms.sourcegitcommit: b5b18d063a5926ad4acb7d0aa3935978d0fedb8c
ms.openlocfilehash: ab7855f88e2c9791327d6d7fa37213364c6c1d46


---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Solucionar problemas de backup de máquinas virtuais do Azure
> [!div class="op_single_selector"]
> * [Cofre dos serviços de recuperação](backup-azure-vms-troubleshoot.md)
> * [Cofre de backup](backup-azure-vms-troubleshoot-classic.md)
>
>

Você pode solucionar os erros encontrados enquanto usa o Backup do Azure com as informações listadas na tabela a seguir.

## <a name="backup"></a>Backup
| Operação de backup | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup |Não foi possível executar a operação, pois a VM não existe mais. - Pare a proteção da máquina virtual sem excluir os dados de backup. Mais detalhes em http://go.microsoft.com/fwlink/?LinkId=808124 |Isso acontece quando a VM primária é excluída, mas a política de backup continua a procurar por uma VM para fazer backup. Para corrigir esse erro:  <ol><li> Recrie a máquina virtual com o mesmo nome e com o mesmo nome do grupo de recursos [nome do serviço de nuvem],<br>(OU)</li><li> Pare a proteção da máquina virtual excluindo ou não os dados de backup. [Mais detalhes:](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Backup  |Não pôde se comunicar com o agente VM para status do instantâneo. - Certifique-se de que a VM tenha acesso à internet. Além disso, atualize o agente da VM conforme mencionado no guia de solução de problemas em http://go.microsoft.com/fwlink/?LinkId=800034 |Esse erro é gerado se há um problema com o agente de VM ou se o acesso à rede para a infraestrutura do Azure está bloqueado de alguma forma. Saiba mais sobre depuração de problemas de instantâneo de VM.<br>  Se o agente de VM não está causando problemas, reinicie a máquina virtual. Às vezes um estado incorreto de VM pode causar problemas e reiniciar a VM redefine esse "estado inválido" |
| Backup |Falha na operação de extensão dos serviços de recuperação. -Certifique-se de que o agente de máquina virtual mais recente esteja presente na máquina virtual e o serviço do agente esteja em execução. Tente novamente a operação de backup e, se ele falhar, entre em contato com o suporte da Microsoft. |Esse erro é disparado quando o agente da VM está desatualizado. Consulte a seção "Atualização do agente da VM" logo abaixo para atualizar o agente da VM. |
| Backup |A máquina virtual não existe. - Certifique-se de que a máquina virtual exista ou selecione uma máquina virtual diferente. |Isso acontece quando a VM primária é excluída, mas a política de backup continua a procurar por uma VM para fazer backup. Para corrigir esse erro:  <ol><li> Recrie a máquina virtual com o mesmo nome e com o mesmo nome do grupo de recursos [nome do serviço de nuvem],<br>(OU)<br></li><li>Pare a proteção da máquina virtual sem excluir os dados de backup. [Mais detalhes:](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Backup  |Falha na execução do comando. - Outra operação está em andamento neste item. Aguarde até que a operação anterior seja concluída e tente novamente |Está em execução um backup existente ou um trabalho de restauração para a máquina virtual e não é possível iniciar um novo trabalho enquanto o trabalho existente estiver sendo executado. |
| Backup  |A cópia de VHDs do Cofre de backup atingiu o tempo limite - tente a operação novamente dentro de alguns minutos. Se o problema persistir, contate o Suporte da Microsoft. |Isso ocorre quando há muitos dados a serem copiados. Verifique se você tem menos de 16 discos de dados. |
| Backup |Falha no backup com um erro interno - tente novamente a operação dentro de alguns minutos. Se o problema persistir, contate o Suporte da Microsoft |Você pode obter esse erro por 2 motivos:  <ol><li> Há um problema temporário ao acessar o armazenamento de VM. Verifique o [Status do Azure](https://azure.microsoft.com/en-us/status/) para ver se há qualquer problema ativo relacionado a computação, armazenamento ou rede na região. Repita o trabalho de backup depois que o problema for resolvido. <li>A VM original foi excluída e, portanto, o ponto de recuperação não pode ser usado. Para manter os dados de backup de uma VM excluída, mas remover os erros de backup: desproteja a VM e escolha a opção para manter os dados. Essa ação interrompe o trabalho de backup agendado e as mensagens de erro recorrentes. |
| Backup  |Falha ao instalar a extensão dos Serviços de Recuperação do Azure no item selecionado - o agente da VM é um pré-requisito para a Extensão dos Serviços de Recuperação do Azure. Instale o agente de VM do Azure e reinicie a operação de registro |<ol> <li>Verifique se o agente de VM foi instalado corretamente. <li>Certifique-se de que o sinalizador de configuração da VM tenha sido definido corretamente.</ol> [Leia mais](#validating-vm-agent-installation) sobre como instalar o agente da VM e como validar a instalação do agente da VM. |
| Backup  |Falha na instalação da extensão. Erro "COM+ não pôde se comunicar com o Coordenador de transações distribuídas da Microsoft |Isso geralmente significa que o serviço COM+ não está em execução. Entre em contato com o suporte da Microsoft para obter ajuda sobre como corrigir esse problema. |
| Backup |Falha na operação de instantâneo. Erro de operação do VSS "Esta unidade está bloqueada pela Criptografia de Unidade de Disco BitLocker. Você deve desbloquear esta unidade no Painel de Controle. |Desative o BitLocker para todas as unidades na VM e observe se o problema VSS é resolvido |
| Backup |Não há suporte para máquinas virtuais com discos rígidos virtuais no armazenamento Premium para backup |Nenhuma. |
| Backup  |Máquina Virtual do Azure não encontrada. |Isso acontece quando a VM primária é excluída, mas a política de backup continua a procurar por uma VM para fazer backup. Para corrigir esse erro:  <ol><li>Recrie a máquina virtual com o mesmo nome e com o mesmo nome do grupo de recursos [nome do serviço de nuvem], <br>(OU) <li> Desative a proteção para esta VM para que os trabalhos de backup não sejam criados. </ol> |
| Backup  |O agente de máquina virtual não está presente na máquina virtual - instale qualquer pré-requisito necessário e o agente de VM e, depois, reinicie a operação. |[Leia mais](#vm-agent) sobre a instalação do agente de VM e como validar a instalação do agente de VM. |

## <a name="jobs"></a>Trabalhos
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Cancelar trabalho |Não há suporte para cancelamento deste tipo de trabalho - Aguarde até que o trabalho seja concluído. |Nenhum |
| Cancelar trabalho |O trabalho não está em um estado cancelável - aguarde até que o trabalho seja concluído. <br>OU<br>  o trabalho selecionado não está em um estado cancelável - aguarde até que o trabalho seja concluído. |Muito provavelmente o trabalho está quase concluído; Aguarde até que o trabalho seja concluído |
| Cancelar trabalho |Não é possível cancelar o trabalho porque ele não está em andamento - há suporte para cancelamento apenas de trabalhos que estão em andamento. Tente cancelar um trabalho em andamento. |Isso ocorre devido a um estado transitório. Aguarde um minuto e repita a operação de cancelamento |
| Cancelar trabalho |Falha ao cancelar o trabalho - Aguarde até que o trabalho seja concluído. |Nenhum |

## <a name="restore"></a>Restaurar
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Restaurar |A restauração falhou com erro interno de nuvem |<ol><li>O serviço de nuvem no qual você está tentando restaurar está definido com configurações de DNS. Você pode verificar  <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Se houver um endereço configurado, isso significa que as configurações de DNS estão definidas.<br> <li>O serviço de nuvem para o qual você está tentando restaurar está configurado com ReservedIP e as VMs existentes no serviço de nuvem estão no estado parado.<br>Você pode verificar se um serviço de nuvem tem IP reservado usando os seguintes cmdlets do Powershell:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Você está tentando restaurar uma máquina virtual com as configurações de rede especiais a seguir no mesmo serviço de nuvem. <br>– Máquinas virtuais sob configuração do balanceador de carga (interno e externo)<br>– Máquinas virtuais com vários IPs reservados<br>– Máquinas virtuais com várias NICs<br>Selecione um novo serviço de nuvem na interface do usuário ou confira as [considerações sobre a restauração](backup-azure-arm-restore-vms.md#restoring-vms-with-special-network-configurations) para VMs com configurações de rede especiais</ol> |
| Restaurar |O nome DNS selecionado já existe - especifique um nome DNS diferente e tente novamente. |O nome DNS aqui se refere ao nome do serviço de nuvem (geralmente terminando em .cloudapp.net). Isso precisa ser exclusivo. Se você encontrar esse erro, precisa escolher um nome de VM diferente durante a restauração. <br><br>  Observe que esse erro é mostrado apenas para os usuários do portal do Azure. A operação de restauração por meio do PowerShell terá êxito porque apenas restaura os discos e não cria a VM. O erro será enfrentado quando a VM está explicitamente criada por você após a operação de restauração no disco. |
| Restaurar |A configuração de rede virtual especificada não está correta - especifique uma configuração de rede virtual diferente e tente novamente. |Nenhum |
| Restaurar |O serviço de nuvem especificado está usando um IP reservado, o que não corresponde à configuração da máquina virtual que está sendo restaurada - especifique um serviço de nuvem diferente que não está usando o IP reservado ou escolha outro ponto de recuperação para restaurar. |Nenhum |
| Restaurar |O serviço de nuvem atingiu o limite no número de pontos de extremidade de entrada - Repita a operação especificando um serviço de nuvem diferente ou usando um ponto de extremidade existente. |Nenhum |
| Restaurar |O cofre de backup e a conta de armazenamento de destino estão em duas regiões diferentes — Verifique se a conta de armazenamento especificada na operação de restauração está na mesma região do Azure que o cofre de backup. |Nenhum |
| Restaurar |Não há suporte para a conta de armazenamento especificada para a operação de restauração - As contas de armazenamento Básica/Padrão com configurações de replicação de redundância geográfica ou redundância local. Selecione uma conta de armazenamento com suporte |Nenhum |
| Restaurar |O tipo de conta de armazenamento especificado para a operação de restauração não está online - Certifique-se de que a conta de armazenamento especificada na operação de restauração está online |Isso pode acontecer devido a um erro transitório no armazenamento do Azure ou devido a uma interrupção. Escolha outra conta de armazenamento. |
| Restaurar |A Cota do Grupo de Recursos foi alcançada - Exclua alguns grupos de recursos do portal do Azure ou entre em contato com o suporte do Azure para aumentar os limites. |Nenhum |
| Restaurar |A subrede selecionada não existe - selecione uma subrede que exista |Nenhum |

## <a name="policy"></a>Política
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Criar política |Falha ao criar a política - Reduza as escolhas de retenção para continuar com a configuração da política. |Nenhum |

## <a name="vm-agent"></a>Agente de VM
### <a name="setting-up-the-vm-agent"></a>Configurando o agente de VM
Normalmente, o agente de VM já está presente em máquinas virtuais que são criadas na Galeria do Azure. No entanto, as máquinas virtuais que são migradas de datacenters locais não teriam o Agente de VM instalado. Para essas VMs, o Agente de VM precisa ser instalado explicitamente. Leia mais sobre [Instalando o Agente de VM em uma VM existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Para VMs do Windows:

* Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisará de privilégios de Administrador para concluir a instalação.
* [Atualizar a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.

Para VMs do Linux:

* Instale o [agente Linux](https://github.com/Azure/WALinuxAgent) mais recente do github.
* [Atualizar a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.

### <a name="updating-the-vm-agent"></a>Atualizar o Agente de VM
Para VMs do Windows:

* Atualizar o agente de VM é tão simples quanto reinstalar os [Binários do Agente de VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). No entanto, você precisa garantir que nenhuma operação de backup esteja em execução enquanto o Agente de VM estiver sendo atualizado.

Para VMs do Linux:

* Siga as instruções em [Atualizando o agente de VM do Linux](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="validating-vm-agent-installation"></a>Validando a instalação do Agente de VM
Como verificar a versão do Agente de VM em VMs do Windows:

1. Faça logon na máquina virtual do Azure e navegue até a pasta *C:\WindowsAzure\Packages*. Você deve encontrar o arquivo WaAppAgent.exe presente.
2. Clique com o botão direito do mouse no arquivo, vá para **Propriedades** e selecione a guia **Detalhes**. O campo Versão do produto deve ser 2.6.1198.718 ou mais recente

## <a name="troubleshoot-vm-snapshot-issues"></a>Solucionar Problemas de Instantâneo de VM
O backup de VM depende da emissão de comandos de instantâneo para o armazenamento subjacente. Não ter acesso ao armazenamento, ou atrasos na execução da tarefa do instantâneo, pode resultar na falha do trabalho de backup. O descrito a seguir pode causar falha na tarefa do instantâneo.

1. O acesso à rede para o Armazenamento é bloqueado usando NSG<br>
    Aprenda mais sobre como [habilitar o acesso à rede](backup-azure-vms-prepare.md#network-connectivity) para Armazenamento usando WhiteListing de IPs ou por meio do servidor proxy.
2. Máquinas virtuais com o backup do SQL Server configurado podem causar atraso na tarefa do instantâneo  <br>
    Por padrão, o backup de VM emite backup completo de VSS em VMs do Windows. Em máquinas virtuais que estão executando SQL Servers e nas quais o SQL Server backup está configurado, isso pode causar atraso na execução do instantâneo. Defina a chave do Registro a seguir se houver falhas de backup devido a problemas de instantâneo.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```
3. Status da VM informado incorretamente porque a VM está desligada em RDP.  <br>
    Se você desligou a máquina virtual em RDP, verifique no portal que o status da VM esteja refletido corretamente. Se não estiver, desligue a máquina virtual no portal usando a opção 'Desligar' no painel da VM.
4. Se mais de quatro VMs compartilharem o mesmo serviço de nuvem, configure várias políticas de backup para preparar os tempos de backup para que não haja mais de quatro backups de VM iniciados ao mesmo tempo. Tente distribuir os tempos de início de backup em intervalos de uma hora entre políticas.
5. A VM está executando com alta utilização de CPU/memória.<br>
    Se a máquina virtual está em execução com alta utilização de CPU (>90%) ou memória, a tarefa de instantâneo é enfileirada, atrasada e eventualmente atingirá o tempo limite. Tente o backup sob demanda em tais situações.

<br>

## <a name="networking"></a>Rede
Como todas as extensões, a extensão de Backup precisa acessar a Internet pública para trabalhar. A falta de acesso à Internet pública pode se manifestar de inúmeras formas:

* A instalação da extensão pode falhar
* As operações de backup (como snapshot de disco) podem falhar
* A exibição do status da operação de backup pode falhar

A necessidade de resolução de endereços de Internet pública foi articulada [aqui](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Você precisará verificar as configurações de DNS para o VNET e certificar-se de que os URIs do Azure possam ser resolvidos.

Após a resolução de nomes ser feita corretamente, o acesso às IPs Azure também deve ser fornecido. Para desbloquear o acesso à infraestrutura do Azure, siga uma destas etapas:

1. Realizar a lista branca de intervalos de IP do datacenter do Azure.
   * Obter a lista de [IPs do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) a colocar na lista branca.
   * Desbloquear os IPs usando o cmdlet [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) . Execute este cmdlet na VM do Azure em uma janela do PowerShell com privilégios elevados (executar como Administrador).
   * Adicione regras ao NSG (se você tiver uma em vigor) para permitir o acesso aos IPs.
2. Criar um caminho para a transmissão do tráfego HTTP
   * Se você tiver alguma restrição de rede no local (um Grupo de Segurança de Rede, por exemplo), implante um servidor proxy HTTP para encaminhar o tráfego. As etapas para implantar um servidor proxy HTTP podem ser encontradas [aqui](backup-azure-vms-prepare.md#network-connectivity).
   * Adicione regras ao NSG (se você tiver uma em vigor) para permitir o acesso à INTERNET do Proxy HTTP.

> [!NOTE]
> O DHCP deve estar habilitado no convidado para que o Backup da VM IaaS funcione.  Se você precisar de um endereço IP privado estático, deverá configurá-lo usando a plataforma. A opção DHCP na VM deve ser ativada.
> Exiba mais informações sobre [Como definir um IP interno estático privado](../virtual-network/virtual-networks-reserved-private-ip.md).
>
>



<!--HONumber=Nov16_HO5-->


