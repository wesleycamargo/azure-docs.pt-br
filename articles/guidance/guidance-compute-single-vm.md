<properties
   pageTitle="Execução de uma máquina virtual (Windows) | Plano gráfico | Microsoft Azure"
   description="Como executar uma única VM no Azure, prestando atenção na escalabilidade, na resiliência, na capacidade de gerenciamento e na segurança."
   services=""
   documentationCenter="na"
   authors="mikewasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="mikewasson"/>

# Execução de uma única VM do Windows no Azure

Este artigo descreve um conjunto de práticas comprovadas para a execução de uma única VM do Windows no Azure, prestando atenção na escalabilidade, na resiliência, na capacidade de gerenciamento e na segurança.

> [AZURE.WARNING] Não há SLA de tempo de atividade para VMs únicas no Azure. Use essa configuração para desenvolvimento e teste, mas não como uma implantação de produção.

O Azure tem dois modelos de implantação diferentes: [Gerenciador de Recursos][resource-manager-overview] e clássico. Este artigo usa o Gerenciador de Recursos, recomendado pela Microsoft para novas implantações. Há várias maneiras de usar o Gerenciador de Recursos, incluindo os comandos do [Portal do Azure][azure-portal], do [Azure PowerShell][azure-powershell], da [CLI do Azure][azure-cli] ou os [modelos do Gerenciador de Recursos][arm-templates]. Este artigo inclui um exemplo usando a CLI do Azure.

![IaaS: VM única](media/guidance-compute-single-vm.png)

O provisionamento de uma única VM no Azure envolve mais partes móveis do que a própria VM principal. Há elementos de computação, de rede e de armazenamento.

- **Grupo de recursos.** Crie um [grupo de recursos][resource-manager-overview] para armazenar os recursos para esta VM. Um _grupo de recursos_ é um contêiner que armazena os recursos relacionados.

- **VM**. Você pode provisionar uma VM de uma lista de imagens publicadas ou de um arquivo VHD que você carrega no Armazenamento de Blobs do Azure.

- **Disco do sistema operacional.** O disco do sistema operacional é um VHD armazenado no [armazenamento do Azure][azure-storage]. Isso significa que ele persistirá mesmo se a máquina host falhar.

- **Disco temporário.** A VM é criada com um disco temporário (a unidade `D:` no Windows). Esse disco é armazenado em uma unidade física no computador host. Ele _não_ é salvo no armazenamento do Azure e pode desaparecer durante as reinicializações e outros eventos de ciclo de vida da VM. Use esse disco somente para dados temporários, como arquivos de paginação ou de permuta.

- **Discos de dados.** Um [disco de dados][data-disk] é um VHD persistente usado para dados de aplicativo. Os discos de dados são armazenados no Armazenamento do Azure, como o disco do SO.

- **Rede Virtual e sub-rede.** Cada VM no Azure é implantada em uma Rede Virtual, que é dividida em sub-redes.

- **Endereço IP público.** Um endereço IP público é necessário para se comunicar com a VM &mdash; por exemplo, via RDP (área de trabalho remota).

- **NSG (grupo de segurança de rede)**. O [NSG][nsg] é usado para permitir/negar o tráfego de rede para a VM. As regras padrão do NSG não permitem nenhum tráfego de entrada da Internet.

- **NIC (Placa de interface de rede)**. A NIC permite que a VM se comunique com a rede virtual.

- **Diagnósticos.** O registro de diagnóstico é crucial para o gerenciamento e solução de problemas da VM.

## Recomendações de VM

- Ao mover uma carga de trabalho existente para o Azure, escolha o [Tamanho de VM][virtual-machine-sizes] que mais se aproxime de seus servidores locais. Recomendamos as séries GS e DS, que podem usar o Armazenamento Premium para cargas de trabalho com muita E/S.

    - Se sua carga de trabalho não exigir acesso ao disco de alto desempenho e baixa latência, considere os outros tamanhos de VM de camada Standard, como as séries A ou D.

- Quando você provisiona a VM e outros recursos, é necessário especificar um local. Em geral, escolha um local mais próximo de seus usuários internos ou de seus clientes. No entanto, nem todos os SKUs de VM estão disponíveis em todos os locais. Para obter detalhes, confira [Serviços por região][services-by-region].

- Para saber mais sobre como escolher uma imagem da VM publicada, confira [Navegar e selecionar imagens da máquina virtual do Azure][select-vm-image].

## Recomendações de disco e de armazenamento

Recomendamos o [Armazenamento Premium][premium-storage] para obter o melhor desempenho de E/S de disco. No entanto, observe que o Armazenamento Premium exige VMs das séries DS ou GS.

- Para o Armazenamento Premium, o custo tem base no tamanho do disco provisionado. O IOPS e a taxa de transferência (por exemplo, a taxa de transferência de dados) também dependem do tamanho do disco. Portanto, ao provisionar um disco, considere todos os três fatores (capacidade, IOPS e taxa de transferência). .

- Para o armazenamento Standard, o custo se baseia na quantidade de dados gravados no disco. Portanto, é uma prática recomendada provisionar o tamanho máximo (1023 GB). No entanto, use a formatação rápida ao formatar os discos. Um formato de disco cheio grava zeros no disco, o que resulta no uso real do armazenamento. Confira [Preços do Armazenamento do Azure][storage-price].

- Se você selecionar um armazenamento Standard, recomendamos o GRS (Armazenamento com Redundância Geográfica), pois ele é durável mesmo no caso de uma interrupção regional completa ou um desastre no qual a região primária não possa ser recuperada.

- Adicione um ou mais discos de dados. Quando você cria um novo VHD, ele não está formatado. Faça logon na VM para formatar o disco.

- Se você tiver uma grande quantidade de discos de dados, esteja ciente dos limites totais de E/S da conta de armazenamento. Para saber mais, confira [Limites de disco da máquina virtual][vm-disk-limits].

- Para obter o melhor desempenho, crie uma conta de armazenamento separada para armazenar logs de diagnóstico. Uma conta LRS (armazenamento com redundância local) padrão é suficiente para os logs de diagnóstico.

## Recomendações de rede

- Para uma única VM, crie uma Rede Virtual com uma sub-rede. Crie também um NSG e um endereço IP público.

- Esse endereço IP público pode ser dinâmico ou estático. O padrão é dinâmico.

    - Reserve um [endereço IP estático][static-ip] se precisar de um endereço IP fixo que não mudará &mdash; por exemplo, se você precisar criar um registro A no DNS ou precisar do endereço IP para colocar na lista válida.

    - Por padrão, o endereço IP não tem um FQDN (nome de domínio totalmente qualificado). Para saber mais, confira [Criar um nome de domínio totalmente qualificado no Portal do Azure][fqdn].

- Aloque uma NIC e associe-a ao endereço IP, à sub-rede e ao NSG.

- As regras NSG padrão não permitem RDP. Para habilitar o RDP, adicione uma regra ao NSG que permita o tráfego de entrada na porta TCP 3389.

## Escalabilidade

Você pode escalar uma VM vertical ou horizontalmente, alterando o tamanho da VM. O seguinte comando da CLI do Azure redimensiona uma VM:

```text
azure vm set -g <<resource-group>> --vm-size <<new-vm-size>
    --boot-diagnostics-storage-uri <<storage-account-uri>> <<vm-name>>
```

Redimensionar a VM disparará uma reinicialização do sistema e remapeará o SO e discos de dados existentes após a reinicialização. Qualquer coisa no disco temporário será perdida. A opção `--boot-diagnostics-storage-uri` habilita os [diagnósticos de inicialização][boot-diagnostics] para registrar erros relacionados à inicialização.

Talvez você não consiga dimensionar de uma família de SKUs para outra (por exemplo, de uma série A para a série G). Use o seguinte comando da CLI para obter uma lista de tamanhos disponíveis para uma VM existente:

```text
azure vm sizes -g <<resource-group>> --vm-name <<vm-name>>
```

Para escalar um tamanho que não esteja listado, você deve excluir a instância da VM e criar uma nova. A exclusão de uma VM não exclui os VHDs.

## Disponibilidade

- Não há SLA de disponibilidade para VMs únicas. Para obter um SLA com relação à disponibilidade, você deverá implantar várias VMs em um conjunto de disponibilidade.

- Sua VM pode ser afetada por uma [manutenção planejada][planned-maintenance] ou uma [manutenção não planejada][manage-vm-availability]. Você pode usar os [logs de reinicialização da VM][reboot-logs] para determinar se uma reinicialização da VM foi causada por manutenção planejada.

- Não coloque uma única VM em um conjunto de disponibilidade. Colocar a VM em um conjunto de disponibilidade informa ao Azure para tratar a VM como parte de um conjunto com várias instâncias, e você não receberá qualquer alerta ou notificação antecipada sobre a reinicialização da manutenção planejada.

- Os VHDs têm suporte do [Armazenamento do Azure][azure-storage], que é replicado para obter durabilidade e disponibilidade.

- Para proteger contra perda acidental de dados durante operações normais (por exemplo, devido ao erro do usuário), você também deve implementar backups pontuais usando [instantâneos de blob][blob-snapshot] ou outra ferramenta.

## Capacidade de gerenciamento

- Execute o seguinte comando da CLI para habilitar diagnósticos da VM:
    
    ```text
    azure vm enable-diag <<resource-group>> <<vm-name>>
    ```
    
    Esse comando habilita métricas de integridade básicas, logs de infraestrutura de diagnóstico e diagnóstico de inicialização. Para saber mais, confira [Habilitar o monitoramento e o diagnóstico][enable-monitoring].

- Use a extensão [Coleção de Log do Azure][log-collector] para coletar logs e carregá-los no Armazenamento do Azure.

- O Azure faz uma distinção entre os estados "Parado" e "Desalocado". Você receberá uma cobrança quando o status da VM for "Parado". Você não receberá uma cobrança quando a VM for desalocada. (Confira as [Perguntas frequentes sobre a VM do Azure][vm-faq]).

    Use o seguinte comando da CLI para desalocar uma VM:
    
    ```text
    azure vm deallocate <<resource-group>> <<vm-name>>
    ```
    
    Observação: o botão **Parar** no portal do Azure também desaloca a VM. No entanto, se você desligar de dentro do Windows (via RDP), a VM será interrompida, mas _não_ desalocada, portanto você ainda será cobrado.

- Se você excluir uma VM, os VHDs não serão excluídos. Isso significa que você poderá excluir com segurança a VM sem perda de dados. No entanto, você ainda será cobrado pelo armazenamento. Para excluir o VHD, exclua o arquivo do [armazenamento de blobs][blog-storage].

- Para redimensionar o disco do sistema operacional, baixe o arquivo .vhd e use uma ferramenta como [Resize-VHD][Resize-VHD] para redimensionar o VHD. Carregue o VHD redimensionado no Armazenamento de Blobs depois exclua a instância da VM e provisione uma nova instância que usa o VHD redimensionado.


## Segurança

- Use a [Central de Segurança do Azure][security-center] para ter uma exibição central do estado da segurança de seus recursos do Azure. A Central de Segurança monitora possíveis problemas de segurança, como atualizações do sistema, antimalware, ACLs de ponto de extremidade e fornece uma visão abrangente da integridade de segurança de sua implantação. **Observação:** no momento da produção deste artigo, a Central de Segurança ainda estava em versão de visualização.

- Use o RBAC ([Controle de acesso baseado em função][rbac]) para definir quais os membros de sua equipe de DevOps podem gerenciar os recursos do Azure (VM, rede etc) que você implanta.

- Considere a instalação das [extensões de segurança][security-extensions].

- Use a [Azure Disk Encryption][disk-encryption] para criptografar o SO e os discos de dados. **Observação:** no momento da produção deste artigo, a Azure Disk Encryption ainda estava em versão de visualização.

## Solucionar problemas

- Para redefinir a senha de administrador local, execute o comando `vm reset-access` da CLI do Azure.
    
    ```text
    azure vm reset-access -u <<user>> -p <<new-password>> <<resource-group>> <<vm-name>>
    ```
    
- Se sua VM entrar em um estado não inicializável, use o [Diagnóstico de inicialização][boot-diagnostics] para diagnosticar falhas de inicialização.

- Examine os [logs de auditoria][audit-logs] para ver as ações de provisionamento e outros eventos da VM.

## Comandos da CLI do Azure (exemplo)

O seguinte script de lote do Windows executa os comandos da [CLI do Azure][azure-cli] para implantar uma única instância de VM e os recursos relacionados de rede e armazenamento, como mostra o diagrama anterior.

```bat
ECHO OFF
SETLOCAL

IF "%~1"=="" (
    ECHO Usage: %0 subscription-id
    EXIT /B
    )

:: Set up variables to build out the naming conventions for deploying
:: the cluster

SET LOCATION=eastus2
SET APP_NAME=app1
SET ENVIRONMENT=dev
SET USERNAME=testuser
SET PASSWORD=AweS0me@PW

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1

:: Set up the names of things using recommended conventions
SET RESOURCE_GROUP=%APP_NAME%-%ENVIRONMENT%-rg
SET VM_NAME=%APP_NAME%-vm0

SET IP_NAME=%APP_NAME%-pip
SET NIC_NAME=%VM_NAME%-0nic
SET NSG_NAME=%APP_NAME%-nsg
SET SUBNET_NAME=%APP_NAME%-subnet
SET VNET_NAME=%APP_NAME%-vnet
SET VHD_STORAGE=%VM_NAME:-=%st0
SET DIAGNOSTICS_STORAGE=%VM_NAME:-=%diag

:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see...
SET VM_SIZE=Standard_DS1

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --resource-group %RESOURCE_GROUP% ^
  --subscription %SUBSCRIPTION%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% %POSTFIX%

:: Create the NIC
CALL azure network nic create --network-security-group-name %NSG_NAME% ^
  --public-ip-name %IP_NAME% --subnet-name %SUBNET_NAME% --subnet-vnet-name ^
  %VNET_NAME%  --name %NIC_NAME% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS %POSTFIX% %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS %POSTFIX% %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --nic-name %NIC_NAME% --vnet-name %VNET_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new -g %RESOURCE_GROUP% --vm-name %VM_NAME% ^
  --size-in-gb 128 --vhd-name data1.vhd --storage-account-name %VHD_STORAGE%

:: Allow RDP
CALL azure network nsg rule create -g %RESOURCE_GROUP% --nsg-name %NSG_NAME% ^
  --direction Inbound --protocol Tcp --destination-port-range 3389 ^
  --source-port-range * --priority 100 --access Allow RDPAllow
```

<!-- links -->

[arm-templates]: ../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md
[audit-logs]: https://azure.microsoft.com/pt-BR/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../virtual-machines/virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-storage]: ../storage/storage-introduction.md
[blob-snapshot]: ../storage/storage-blob-snapshots.md
[blog-storage]: ../storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/pt-BR/blog/boot-diagnostics-for-virtual-machines-v2/
[data-disk]: ../virtual-machines/virtual-machines-disks-vhds.md
[disk-encryption]: ../azure-security-disk-encryption.md
[enable-monitoring]: ../azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../virtual-machines/virtual-machines-create-fqdn-on-portal.md
[log-collector]: https://azure.microsoft.com/pt-BR/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../virtual-machines/virtual-machines-manage-availability.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[password-reset]: ../virtual-machines/virtual-machines-windows-reset-password.md
[planned-maintenance]: ../virtual-machines/virtual-machines-planned-maintenance.md
[premium-storage]: ../storage/storage-premium-storage-preview-portal.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[reboot-logs]: https://azure.microsoft.com/pt-BR/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/pt-BR/library/hh848535.aspx
[resource-manager-overview]: ../resource-group-overview.md
[security-center]: https://azure.microsoft.com/pt-BR/services/security-center/
[security-extensions]: ../virtual-machines/virtual-machines-extensions-features.md#security-and-protection
[select-vm-image]: ../virtual-machines/resource-groups-vm-searching.md
[services-by-region]: https://azure.microsoft.com/pt-BR/regions/#services
[static-ip]: ../virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../virtual-machines/virtual-machines-size-specs.md
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-faq]: ../virtual-machines/virtual-machines-questions.md

<!---HONumber=AcomDC_0302_2016-->