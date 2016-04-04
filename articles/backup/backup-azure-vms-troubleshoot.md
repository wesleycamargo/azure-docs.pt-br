<properties
	pageTitle="Solucionar problemas de backup de máquinas virtuais do Azure | Microsoft Azure"
	description="Solucionar problemas de backup e restauração de máquinas virtuais do Azure"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="trinadhk;jimpark;"/>


# Solucionar problemas de backup de máquinas virtuais do Azure
Você pode solucionar os erros encontrados enquanto usa o Backup do Azure com as informações listadas na tabela a seguir.

## Descoberta

| Operação de backup | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Descoberta | Falha ao detectar novos itens - Backup do Microsoft Azure encontrado e erro interno. Aguarde alguns minutos e repita a operação. | Repita o processo de descoberta após 15 minutos.
| Descoberta | Falha ao descobrir novos itens – outra operação de descoberta já está em andamento. Aguarde até que a operação de descoberta atual seja concluída. | Nenhum |

## Registrar
| Operação de backup | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Registrar | O número de discos de dados anexados à máquina virtual excedeu o limite com suporte. Retire alguns discos de dados dos anexos nesta máquina virtual e repita a operação. O backup do Azure dá suporte a até 16 discos de dados anexados a uma máquina virtual do Azure para backup | Nenhum |
| Registrar | O Backup do Microsoft Azure encontrou um erro interno. Aguarde alguns minutos e tente a operação novamente. Se o problema persistir, contate o Suporte da Microsoft. | É possível obter esse erro devido a uma das seguintes configuração sem suporte: <ul><li>LRS Premium </ul> |
| Registrar | Falha no registro com o tempo limite da operação Instalar agente | Verifique se a versão do sistema operacional da máquina virtual tem suporte. |
| Registrar | A execução do comando falhou - há outra operação em andamento neste item. Aguarde até que a operação anterior seja concluída | Nenhum |
| Registrar | Não há suporte para máquinas virtuais com discos rígidos virtuais no armazenamento Premium para backup | Nenhum |
| Registrar | O agente de máquina virtual não está presente na máquina virtual - instale o pré-requisito necessário, agente de VM e reinicie a operação. | [Leia mais](#vm-agent) sobre a instalação do agente de VM e como validar a instalação do agente de VM. |

## Backup

| Operação de backup | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Backup | A cópia de VHDs do Cofre de backup atingiu o tempo limite - tente a operação novamente dentro de alguns minutos. Se o problema persistir, contate o Suporte da Microsoft. | Isso ocorre quando há muitos dados a serem copiados. Verifique se você tem menos de 16 discos de dados. |
| Backup | Não pôde se comunicar com o agente VM para status do instantâneo. A subtarefa VM instantâneo VM atingiu o tempo limite. - Consulte o guia de solução de problemas sobre como resolver esse problema. | Esse erro é gerado se há um problema com o agente de VM ou se o acesso à rede para a infraestrutura do Azure está bloqueado de alguma forma. <ul> <li>Aprenda sobre [depuração de problemas no Agente de VM](#vm-agent) <li>Aprenda sobre [depuração de problemas de rede](#networking) <li>Se o agente de VM está funcionando bem, Aprenda sobre [solução de problemas de Instantâneo de VM](#Troubleshoot-VM-Snapshot-Issues)</ul><br>Se o agente de VM não está causando nenhum problema, reinicie a VM. Às vezes um estado incorreto de VM pode causar problemas e reiniciar a VM redefine esse "estado inválido" |
| Backup | Falha no backup com um erro interno - tente novamente a operação dentro de alguns minutos. Se o problema persistir, contate o Suporte da Microsoft | Você pode obter esse erro por duas razões: <ol><li> há muitos dados a serem copiados. <li>A máquina virtual original foi excluída e, portanto, o backup não pode ser feito. Para manter os dados de backup de uma VM excluída, mas parar os erros de backup, desproteja a VM e escolha a opção para manter os dados. Isso interromperá o agendamento de backup e também as mensagens de erro recorrentes. |
| Backup | Falha ao instalar a extensão dos Serviços de Recuperação do Azure no item selecionado - o agente de VM é um pré-requisito para a extensão de Serviços de Recuperação do Azure. Instale o agente de VM do Azure e reinicie a operação de registro | <ol> <li>Verifique se o agente de VM foi instalado corretamente. <li>Certifique-se de que o sinalizador de configuração da VM esteja definido corretamente.</ol> [Leia mais](#validating-vm-agent-installation) sobre a instalação do agente de VM e como validar a instalação do agente de VM. |
| Backup | A execução do comando falhou - outra operação está em andamento neste item. Aguarde até que a operação anterior seja concluída e tente novamente | Está em execução um backup existente ou um trabalho de restauração para a máquina virtual e não é possível iniciar um novo trabalho enquanto o trabalho existente estiver sendo executado. |
| Backup | Falha na instalação da extensão. Erro "COM+ não pôde se comunicar com o Coordenador de transações distribuídas da Microsoft | Isso geralmente significa que o serviço COM+ não está em execução. Entre em contato com o suporte da Microsoft para obter ajuda sobre como corrigir esse problema. |
| Backup | Falha na operação de instantâneo. Erro de operação do VSS "Esta unidade está bloqueada pela Criptografia de Unidade de Disco BitLocker. Você deve desbloquear esta unidade no Painel de Controle. | Desative o BitLocker para todas as unidades na VM e observe se o problema VSS é resolvido |
| Backup | Não há suporte para máquinas virtuais com discos rígidos virtuais no armazenamento Premium para backup | Nenhum |
| Backup | Máquina Virtual do Azure não encontrada. | Isso acontece quando a VM primária é excluída, mas a política de backup continua a procurar por uma VM para fazer backup. Para corrigir esse erro: <ol><li>Recrie a máquina virtual com o mesmo nome e o mesmo nome de grupo de recursos [nome do serviço de nuvem], <br>(OU) <li> Desabilite a proteção para esta VM para que os trabalhos de backup não sejam criados </ol> |
| Backup | O agente de máquina virtual não está presente na máquina virtual - instale o pré-requisito necessário, agente de VM e reinicie a operação. | [Leia mais](#vm-agent) sobre a instalação do agente de VM e como validar a instalação do agente de VM. |

## Trabalhos
| Operação | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Cancelar trabalho | Não há suporte para cancelamento deste tipo de trabalho - Aguarde até que o trabalho seja concluído. | Nenhum |
| Cancelar trabalho | O trabalho não está em um estado cancelável - Aguarde até que o trabalho seja concluído. <br>OU<br> o trabalho selecionado não está em um estado cancelável — Aguarde até que o trabalho seja concluído.| Muito provavelmente o trabalho está quase concluído; Aguarde até que o trabalho seja concluído |
| Cancelar trabalho | Não é possível cancelar o trabalho porque ele não está em andamento - há suporte para cancelamento apenas de trabalhos que estão em andamento. Tente cancelar um trabalho em andamento. | Isso ocorre devido a um estado transitório. Aguarde um minuto e repita a operação de cancelamento |
| Cancelar trabalho | Falha ao cancelar o trabalho - Aguarde até que o trabalho seja concluído. | Nenhum |


## Restaurar
| Operação | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Restaurar | A restauração falhou com erro interno de nuvem | <ol><li>O serviço de nuvem no qual você está tentando restaurar está definido com configurações de DNS. Você pode verificar <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Se houver um endereço configurado, significa que as configurações de DNS estão definidas.<br> <li>O serviço de nuvem para o qual você está tentando restaurar está configurado com ReservedIP e as VMs existentes no serviço de nuvem estão no estado parado.<br>Você pode verificar se um serviço de nuvem tem IP reservado usando os seguintes cmdlets do PowerShell:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Você está tentando restaurar uma máquina virtual com as configurações de rede especiais a seguir no mesmo serviço de nuvem. <br>- Máquinas virtuais em configuração do balanceador de carga (interna e externa)<br>- Máquinas virtuais com vários IPs Reservados<br>- Máquinas virtuais com várias NICs<br>Selecione um novo serviço de nuvem na interface do usuário ou veja as [considerações sobre restauração](backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) para VMs com configurações de rede especiais</ol> |
| Restaurar | O nome DNS selecionado já existe - especifique um nome DNS diferente e tente novamente. | O nome DNS aqui se refere ao nome do serviço de nuvem (geralmente terminando em .cloudapp.net). Isso precisa ser exclusivo. Se você encontrar esse erro, precisa escolher um nome de VM diferente durante a restauração. <br><br> Observe que esse erro é mostrado apenas para os usuários do portal do Azure. A operação de restauração por meio do PowerShell terá êxito porque apenas restaura os discos e não cria a VM. O erro será enfrentado quando a VM está explicitamente criada por você após a operação de restauração no disco. |
| Restaurar | A configuração de rede virtual especificada não está correta - especifique uma configuração de rede virtual diferente e tente novamente. | Nenhum |
| Restaurar | O serviço de nuvem especificado está usando um IP reservado, o que não corresponde à configuração da máquina virtual que está sendo restaurada - especifique um serviço de nuvem diferente que não está usando o IP reservado ou escolha outro ponto de recuperação para restaurar. | Nenhum |
| Restaurar | O serviço de nuvem atingiu o limite no número de pontos de extremidade de entrada - Repita a operação especificando um serviço de nuvem diferente ou usando um ponto de extremidade existente. | Nenhum |
| Restaurar | O cofre de backup e a conta de armazenamento de destino estão em duas regiões diferentes — Verifique se a conta de armazenamento especificada na operação de restauração está na mesma região do Azure que o cofre de backup. | Nenhum |
| Restaurar | Não há suporte para a conta de armazenamento especificada para a operação de restauração - As contas de armazenamento Básica/Padrão com configurações de replicação de redundância geográfica ou redundância local. Selecione uma conta de armazenamento com suporte | Nenhum |
| Restaurar | O tipo de conta de armazenamento especificado para a operação de restauração não está online - Certifique-se de que a conta de armazenamento especificada na operação de restauração está online | Isso pode acontecer devido a um erro transitório no armazenamento do Azure ou devido a uma interrupção. Escolha outra conta de armazenamento. |
| Restaurar | A Cota do Grupo de Recursos foi alcançada - Exclua alguns grupos de recursos do portal do Azure ou entre em contato com o suporte do Azure para aumentar os limites. | Nenhum |
| Restaurar | A subrede selecionada não existe - selecione uma subrede que exista | Nenhum |


## Política
| Operação | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Criar política | Falha ao criar a política - Reduza as escolhas de retenção para continuar com a configuração da política. | Nenhum |


## Agente de VM

### Configurando o agente de VM
Normalmente, o agente de VM já está presente em máquinas virtuais que são criadas na Galeria do Azure. No entanto, as máquinas virtuais que são migradas de datacenters locais não teriam o Agente de VM instalado. Para essas VMs, o Agente de VM precisa ser instalado explicitamente. Leia mais sobre [Instalando o Agente de VM em uma VM existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Para VMs do Windows:

- Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisará de privilégios de Administrador para concluir a instalação.
- [Atualizar a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.

Para VMs do Linux:

- Instale o [agente Linux](https://github.com/Azure/WALinuxAgent) mais recente do github.
- [Atualizar a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.


### Atualizar o Agente de VM
Para VMs do Windows:

- Atualizar o agente de VM é tão simples quanto reinstalar os [Binários do Agente de VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). No entanto, você precisa garantir que nenhuma operação de backup esteja em execução enquanto o Agente de VM estiver sendo atualizado.

Para VMs do Linux:

- Siga as instruções em [Atualizando o agente de VM do Linux](../virtual-machines/virtual-machines-linux-update-agent.md).


### Validando a instalação do Agente de VM
Como verificar a versão do Agente de VM em VMs do Windows:

1. Faça logon na máquina virtual do Azure e navegue até a pasta *C:\\WindowsAzure\\Packages*. Você deve encontrar o arquivo WaAppAgent.exe presente.
2. Clique com o botão direito do mouse no arquivo, vá para **Propriedades** e selecione a guia **Detalhes**. O campo Versão do produto deve ser 2.6.1198.718 ou mais recente

## Solucionar Problemas de Instantâneo de VM
O backup de VM depende da emissão do comando de instantâneo para o armazenamento subjacente. Não ter acesso a armazenamento ou atraso na execução da tarefa do instantâneo pode resultar em falha no backup. O descrito a seguir pode causar falha na tarefa do instantâneo.

1. O acesso à rede para o Armazenamento é bloqueado usando NSG<br> Aprenda mais sobre como [habilitar o acesso à rede](backup-azure-vms-prepare.md#2-network-connectivity) para Armazenamento usando WhiteListing de IPs ou por meio do servidor proxy.
2.  Máquinas virtuais com o backup do SQL Server configurado podem causar atraso na tarefa do instantâneo <br> Por padrão, o backup de VM emite backup completo de VSS em VMs do Windows. Em máquinas virtuais que estão executando SQL Servers e nas quais o SQL Server backup está configurado, isso pode causar atraso na execução do instantâneo. Defina a chave do Registro a seguir se houver falhas de backup devido a problemas de instantâneo.

	```
	[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
	"USEVSSCOPYBACKUP"="TRUE"
	```
3.  Status da VM informado incorretamente porque a VM está desligada em RDP. <br> Se você desligou a máquina virtual em RDP, verifique no portal que o status da VM esteja refletido corretamente. Se não estiver, desligue a máquina virtual no portal usando a opção 'Desligar' no painel da VM.
4.  Várias VMs do mesmo serviço de nuvem são configuradas para backup simultaneamente.<br> É a melhor prática distribuir as VMs do mesmo serviço de nuvem que tem diferentes agendamentos de backup.
5.  A VM está executando com alta utilização de CPU/memória.<br> Se a máquina virtual está em execução com alta utilização de CPU (>90%) ou memória, a tarefa de instantâneo é enfileirada, atrasada e eventualmente atingirá o tempo limite. Tente o backup sob demanda em tais situações.

<br>

## Rede
Como todas as extensões, a extensão de Backup precisa acessar a Internet pública para trabalhar. A falta de acesso à Internet pública pode se manifestar de inúmeras formas:

- A instalação da extensão pode falhar
- As operações de backup (como snapshot de disco) podem falhar
- A exibição do status da operação de backup pode falhar

A necessidade de resolução de endereços de Internet pública foi articulada [aqui](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Você precisará verificar as configurações de DNS para o VNET e certificar-se de que os URIs do Azure possam ser resolvidos.

Após a resolução de nomes ser feita corretamente, o acesso às IPs Azure também deve ser fornecido. Para desbloquear o acesso à infraestrutura do Azure, siga uma destas etapas:

1. Realizar a lista branca de intervalos de IP do datacenter do Azure.
    - Obter a lista de [IPs do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) a colocar na lista branca.
    - Desbloquear os IPs usando o cmdlet [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx). Execute este cmdlet na VM do Azure em uma janela do PowerShell com privilégios elevados (executar como Administrador).
    - Adicione regras ao NSG (se você tiver uma em vigor) para permitir o acesso aos IPs.
2. Criar um caminho para a transmissão do tráfego HTTP
    - Se você tiver alguma restrição de rede no local (um Grupo de Segurança de Rede, por exemplo), implante um servidor proxy HTTP para encaminhar o tráfego. As etapas para implantar um servidor proxy HTTP podem ser encontradas [aqui](backup-azure-vms-prepare.md#2-network-connectivity).
    - Adicione regras ao NSG (se você tiver uma em vigor) para permitir o acesso à INTERNET do Proxy HTTP.

>[AZURE.NOTE] O DHCP deve estar habilitado no convidado para que o Backup da VM IaaS funcione. Se você precisar de um endereço IP privado estático, deverá configurá-lo usando a plataforma. A opção DHCP na VM deve ser ativada. Exiba mais informações sobre como [Definir um IP privado interno estático](../virtual-network/virtual-networks-reserved-private-ip.md).

<!---HONumber=AcomDC_0323_2016-->