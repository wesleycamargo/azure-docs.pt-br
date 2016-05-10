<properties
   pageTitle="Falha no backup de VM do Azure: não foi possível se comunicar com o agente de VM para o status do instantâneo - a subtarefa da VM atingiu o tempo limite | Microsoft Azure"
   description="Sintomas, causas e resoluções para falhas de backup de VM do Azure relacionadas à incapacidade de se comunicar com o agente de VM para status do instantâneo. Erro da subtarefa de VM de instantâneo que atingiu o tempo limite"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jwhit"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/27/2016"
    ms.author="jimpark; markgal"/>

# Falha no backup de VM do Azure: não foi possível se comunicar com o agente de VM para o status do instantâneo - a subtarefa da VM atingiu o tempo limite

## Resumo

Após registrar e agendar uma VM (Máquina Virtual) para o Backup do Azure, o serviço do Backup do Azure inicia o trabalho de backup no horário agendado se comunicando com a extensão de backup na VM para obter um instantâneo pontual. Existem condições que podem impedir que o instantâneo seja disparado que levam a uma falha de backup. Este artigo fornece etapas de solução de problemas relacionados a falhas de backup de VM do Azure referentes ao erro de tempo limite do instantâneo.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Sintoma

O Backup do Microsoft Azure para VM de IaaS (infraestrutura como serviço) falha e retorna a seguinte mensagem de erro nos detalhes do erro do trabalho no Portal do Azure.

**Não foi possível se comunicar com o agente de VM para o status do instantâneo - a subtarefa da VM atingiu o tempo limite.**

## Causa
Há quatro causas comuns para esse erro:

- A VM não tem acesso à Internet.
- O agente de VM do Microsoft Azure instalado na VM está desatualizado (para VMs do Linux).
- A extensão de backup não pode ser atualizada ou carregada.
- Não é possível recuperar o status de instantâneos ou não é possível obter os instantâneos.

## Causa 1: a VM não tem acesso à Internet
Pelo requisito de implantação, a VM não tem acesso à Internet ou tem restrições em vigor que impedem o acesso à infraestrutura do Azure.

A extensão de backup exige conectividade com os endereços IP públicos do Azure para funcionar corretamente. Isso ocorre porque ela envia comandos para um ponto de extremidade do Armazenamento do Azure (URL de HTTP) para gerenciar os instantâneos da VM. Se a extensão não tiver acesso à Internet pública, o backup, eventualmente, falhará.

### Solução
Nesse cenário, use um dos seguintes métodos para resolver o problema:

- Realizar a lista branca de intervalos de IP do datacenter do Azure
- Criar um caminho para a transmissão do tráfego HTTP

### Para realizar a lista branca de intervalos de IP do datacenter do Azure

1. Obtenha a [lista de IPs do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) a ser colocada na lista branca.
2. Desbloqueie os IPs usando o cmdlet New-NetRoute. Execute este cmdlet na VM do Azure em uma janela do PowerShell com privilégios elevados (execute como Administrador).
3. Adicione regras ao NSG (Grupo de Segurança de Rede) se você tiver um para permitir o acesso aos IPs.

### Para criar um caminho para a transmissão do tráfego HTTP

1. Se você tiver restrições de rede em vigor (por exemplo, um NSG), implante um servidor proxy HTTP para encaminhar o tráfego.
2. Se você tiver um NSG (grupo de segurança de rede), adicione regras para permitir o acesso à Internet por meio do proxy HTTP.

Saiba como [configurar um proxy HTTP para backups de VM](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## Causa 2: o agente de VM do Microsoft Azure instalado na VM está desatualizado (para VMs do Linux)

### Solução
A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux é causada por problemas que afetam um agente de VM antigo. Como uma diretriz geral, as primeiras etapas para solucionar esse problema são as seguintes:

1. [Instale o agente de VM do Azure mais recente](https://acom-swtest-2.azurewebsites.net/documentation/articles/virtual-machines-linux-update-agent/).
2. Certifique-se de que o agente do Azure esteja em execução na VM. Para fazer isso, execute o seguinte comando: ```ps -e```

    Se esse processo não estiver em execução, use os seguintes comandos para reiniciá-lo.

    Para o Ubuntu: ```service walinuxagent start```

    Para outras distribuições: ```service waagent start
```

3. [Configure o agente de reinicialização automática](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).

4. Execute um novo backup de teste. Se as falhas persistirem, reúna logs das seguintes pastas para uma depuração adicional.

    Precisamos dos seguintes logs de VM do cliente:

    - /var/lib/waagent/*.xml
    - /var/log/waagent.log
    - /var/log/azure/*

Se precisarmos do registro detalhado para waagent, siga estas etapas para habilitar essa opção:

1. No arquivo /etc/waagent.conf, localize a seguinte linha:

    Habilitar o log detalhado (s|n)

2. Altere o valor **Logs.Verbose** de n para s.
3. Salve a alteração e reinicie o waagent seguindo as etapas anteriores nesta seção.

## Causa 3: A extensão de backup não pode ser atualizada ou carregada
Se as extensões não podem ser carregadas, o Backup falha porque não é possível obter um instantâneo.

### Solução
Para convidados do Windows:

1. Verifique se o serviço iaasvmprovider está habilitado e tem um tipo de inicialização automática.
2. Se essa não for a configuração, habilite o serviço para determinar se o próximo backup é bem-sucedido.

Se a extensão de backup ainda não conseguir ser atualizada ou carregada, você poderá forçar a extensão VMSnapshot ser recarregada instalando a extensão. A próxima tentativa de backup recarregará a extensão.

### Para desinstalar a extensão

1. Vá para o [Portal do Azure](https://ms.portal.azure.com/).
2. Localize a VM específica que tem problemas de backup.
3. Clique em **Configurações**.
4. Clique em **Extensões**.
5. Clique em **Extensão Vmsnapshot**.
6. Clique em **desinstalar**.

Isso fará com que a extensão seja reinstalada durante o próximo backup.

## Causa 4: não é possível recuperar o status de instantâneos ou não é possível obter os instantâneos
O backup de VM depende da emissão do comando de instantâneo para o armazenamento subjacente. O backup pode falhar porque ele não tem acesso ao armazenamento ou devido a um atraso na execução da tarefa do instantâneo.

### Solução
As condições a seguir podem causar a falha da tarefa do instantâneo:

| Causa | Solução |
| ----- | ----- |
| VMs que têm o Backup do Microsoft SQL Server configurado. Por padrão, o Backup de VM executa um Backup completo VSS em VMs do Windows. Em VMs que executam servidores baseados no SQL Server e em que o Backup do SQL Server está configurado, podem ocorrer atrasos na execução do instantâneo. | Defina a seguinte chave do Registro se estiver enfrentando falhas de backup devido a problemas de instantâneo.<br><br>[HKEY\_LOCAL\_MACHINE\\SOFTWARE\\MICROSOFT\\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| O status da VM é informado incorretamente porque a VM está desligada em RDP. Se você desligou a máquina virtual em RDP, verifique o portal para determinar se aquele status de VM está refletido corretamente. | Se não estiver, desligue a VM no portal usando a opção de "Desligar" no painel de VM. |
| Várias VMs do mesmo serviço de nuvem são configuradas para backup simultaneamente. | É uma melhor prática distribuir as VMs do mesmo serviço de nuvem para terem diferentes agendamentos de backup. |
| A VM está em execução com alto uso de CPU ou memória. | Se a VM estiver em execução com alta utilização de CPU (mais de 90%) ou alto uso de memória, a tarefa do instantâneo será enfieirada e postergada e, eventualmente, atingirá o tempo limite. Nessa situação, tente o backup sob demanda. |
|A VM não pode obter o endereço do host/malha do DHCP.|O DHCP deve estar habilitado no convidado para que o Backup da VM IaaS funcione. Se a VM não puder obter o endereço do host/malha da resposta DHCP 245, ela não poderá baixar ou executar qualquer extensão. Se você precisar de um endereço IP privado estático, deverá configurá-lo usando a plataforma. A opção DHCP na VM deve ser ativada. Exiba mais informações sobre [Como definir um IP interno estático privado](../virtual-network/virtual-networks-reserved-private-ip.md).|

<!---HONumber=AcomDC_0427_2016-->