Este artigo descreve um conjunto de práticas comprovadas para a execução de uma VM (máquina virtual) do Windows no Azure, considerando a escalabilidade, disponibilidade, capacidade de gerenciamento e segurança.

> [AZURE.NOTE] O Azure tem dois modelos de implantação diferentes: [Gerenciador de Recursos][resource-manager-overview] e clássico. Este artigo usa o Gerenciador de Recursos, recomendado pela Microsoft para novas implantações.

Não recomendamos usar uma única VM para cargas de trabalho de produção, pois não há nenhum SLA de tempo de atividade para VMs únicas no Azure. Para obter o SLA, é necessário implantar várias VMs em um conjunto de disponibilidade. Para obter mais informações, veja [Running multiple Windows VMs on Azure][multi-vm] (Executando várias VMs Windows no Azure).

## Diagrama da arquitetura

O provisionamento de uma VM no Azure envolve mais partes móveis do que apenas a própria VM. Há elementos de computação, de rede e de armazenamento.

![IaaS: VM única](./media/guidance-blueprints/compute-single-vm.png)

- **Grupo de recursos.** Um [_grupo de recursos_][resource-manager-overview] é um contêiner que armazena os recursos relacionados. Crie um grupo de recursos para armazenar os recursos desta VM.

- **VM**. Você pode provisionar uma VM de uma lista de imagens publicadas ou de um arquivo VHD que você carrega no Armazenamento de Blobs do Azure.

- **Disco do sistema operacional.** O disco do sistema operacional é um VHD armazenado no [armazenamento do Azure][azure-storage]. Isso significa que ele persistirá mesmo se a máquina host falhar.

- **Disco temporário.** A VM é criada com um disco temporário (a unidade `D:` no Windows). Esse disco é armazenado em uma unidade física no computador host. Ele _não_ é salvo no armazenamento do Azure e poderá desaparecer durante as reinicializações e outros eventos de ciclo de vida da VM. Use esse disco somente para dados temporários, como arquivos de paginação ou de permuta.

- **Discos de dados.** Um [disco de dados][data-disk] é um VHD persistente usado para dados de aplicativo. Os discos de dados são armazenados no Armazenamento do Azure, como o disco do sistema operacional.

- **Rede Virtual e sub-rede.** Cada VM no Azure é implantada em uma Rede Virtual, que é dividida em sub-redes.

- **Endereço IP público.** Um endereço IP público é necessário para se comunicar com a VM &mdash; por exemplo, via RDP (área de trabalho remota).

- **NIC (adaptador de rede)**. A NIC permite que a VM se comunique com a rede virtual.

- **NSG (Grupo de segurança de rede)**. O [NSG][nsg] é usado para permitir/negar o tráfego de rede para a sub-rede. É possível associar um NSG a uma NIC individual ou a uma sub-rede. Se você associá-lo a uma sub-rede, as regras do NSG se aplicarão a todas as VMs na sub-rede.
 
- **Diagnósticos.** O registro de diagnóstico é crucial para o gerenciamento e solução de problemas da VM.

## Recomendações

### Recomendações de VM

- Recomendamos as séries DS e GS, a menos que você tenha uma carga de trabalho especializada, como computação de alto desempenho. Para obter detalhes, veja [Tamanhos das máquinas virtuais][virtual-machine-sizes]. Ao mover uma carga de trabalho existente para o Azure, comece com o tamanho da VM que mais se aproxima de seus servidores locais. Em seguida, meça o desempenho da carga de trabalho real com relação à CPU, memória e IOPS de disco e ajuste o tamanho, se necessário. Além disso, se você precisar de várias NICs, esteja ciente do limite de NIC para cada tamanho.

- Quando você provisiona a VM e outros recursos, é necessário especificar um local. Em geral, escolha um local mais próximo de seus usuários internos ou de seus clientes. No entanto, nem todos os tamanhos de VM estão disponíveis em todas as localizações. Para obter detalhes, veja [Serviços por região][services-by-region]. Para listar os tamanhos de VM disponíveis em determinada localização, execute o seguinte comando da CLI do Azure:

    ```
    azure vm sizes --location <location>
    ```

- Para obter mais informações sobre como escolher uma imagem de VM publicada, confira [Navegar e selecionar imagens da máquina virtual do Azure][select-vm-image].

### Recomendações de disco e de armazenamento

- Para um melhor desempenho de E/S de disco, recomendamos o [Armazenamento Premium][premium-storage], que armazena dados em SSDs (unidades de estado sólido). O custo se baseia no tamanho do disco provisionado. O IOPS e a taxa de transferência (por exemplo, a taxa de transferência de dados) também dependem do tamanho do disco. Portanto, ao provisionar um disco, considere todos os três fatores (capacidade, IOPS e taxa de transferência).

- Adicione um ou mais discos de dados. Quando você cria um novo VHD, ele não está formatado. Faça logon na VM para formatar o disco.

- Se você tiver uma grande quantidade de discos de dados, esteja ciente dos limites totais de E/S da conta de armazenamento. Para obter mais informações, confira [Limites de disco da máquina virtual][vm-disk-limits].

- Para obter o melhor desempenho, crie uma conta de armazenamento separada para armazenar logs de diagnóstico. Uma conta LRS (armazenamento com redundância local) padrão é suficiente para os logs de diagnóstico.

- Quando possível, instale aplicativos em um disco de dados, não no disco do sistema operacional. No entanto, talvez alguns aplicativos herdados precisem instalar componentes na unidade C:. Nesse caso, você pode [redimensionar o disco do sistema operacional][resize-os-disk] usando o PowerShell.

### Recomendações de rede

- Esse endereço IP público pode ser dinâmico ou estático. O padrão é dinâmico.

    - Reserve um [endereço IP estático][static-ip] se precisar de um endereço IP fixo que não mudará – por exemplo, se você precisar criar um registro A no DNS ou se precisar do endereço IP para colocar na lista branca.

    - Você também pode criar um FQDN (nome de domínio totalmente qualificado) para o endereço IP. Em seguida, é possível registrar um [registro CNAME][cname-record] no DNS que aponta para o FQDN. Para obter mais informações, veja [Criar um nome de domínio totalmente qualificado no portal do Azure][fqdn].

- Todos os NSGs contêm um conjunto de [regras padrão][nsg-default-rules], incluindo uma regra que bloqueia todo o tráfego de Internet de entrada. As regras padrão não podem ser excluídas, mas outras regras podem substituí-las. Para habilitar o tráfego de Internet, crie regras que permitam o tráfego de entrada em portas específicas – por exemplo, a porta 80 para HTTP.

- Para habilitar o RDP, adicione uma regra NSG que permita o tráfego de entrada na porta TCP 3389.

## Considerações sobre escalabilidade

- Você pode escalar ou reduzir uma VM verticalmente [alterando o tamanho da VM][vm-resize].

- Para escalar horizontalmente, coloque duas ou mais VMs em um conjunto de disponibilidade atrás de um balanceador de carga. Para obter detalhes, veja [Running multiple Windows VMs on Azure][multi-vm] (Executando várias VMs Windows no Azure).

## Considerações sobre disponibilidade

- Como observamos acima, não há nenhum SLA para uma única VM. Para obter o SLA, é necessário implantar várias VMs em um conjunto de disponibilidade.

- Sua VM pode ser afetada por uma [manutenção planejada][planned-maintenance] ou [manutenção não planejada][manage-vm-availability]. Você pode usar os [logs de reinicialização da VM][reboot-logs] para determinar se uma reinicialização da VM foi causada por uma manutenção planejada.

- Os VHDs têm suporte no [Armazenamento do Azure][azure-storage], que é replicado para obter durabilidade e disponibilidade.

- Para proteger contra perda acidental de dados durante operações normais (por exemplo, devido ao erro do usuário), você também deve implementar backups pontuais usando [instantâneos de blob][blob-snapshot] ou outra ferramenta.

## Considerações sobre capacidade de gerenciamento

- **Grupos de recursos.** Coloque recursos acoplados rigidamente que compartilham o mesmo ciclo de vida em um mesmo [grupo de recursos][resource-manager-overview]. Os grupos de recursos permitem implantar e monitorar recursos como um grupo, além de acumular custos de cobrança por grupo de recursos. Também é possível excluir recursos como um conjunto, o que é muito útil para implantações de teste. Dê nomes significativos aos recursos. Isso facilita a localização de um recurso específico e o entendimento de sua função. Veja [Recommended Naming Conventions for Azure Resources][naming conventions] (Convenções de nomenclatura recomendadas para recursos do Azure).

- **Diagnóstico da VM.** Habilite o monitoramento e diagnóstico, incluindo métricas de integridade básicas, logs de infraestrutura de diagnóstico e [diagnóstico de inicialização][boot-diagnostics]. O diagnóstico de inicialização poderá ajudar a diagnosticar uma falha de inicialização se sua VM entrar em um estado não inicializável. Para obter mais informações, veja [Habilitar monitoramento e diagnóstico][enable-monitoring]. Use a extensão [Coleção de Logs do Azure][log-collector] para coletar logs da plataforma Azure e carregá-los no Armazenamento do Azure.

    O seguinte comando da CLI habilita o diagnóstico:

    ```text
    azure vm enable-diag <resource-group> <vm-name>
     ```

- **Interrompendo uma VM.** O Azure faz uma distinção entre os estados "Parado" e "Desalocado". Você receberá uma cobrança quando o status da VM for "Parado". Você não receberá uma cobrança quando a VM for desalocada.

    Use o seguinte comando da CLI para desalocar uma VM:

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    O botão **Parar** no portal do Azure também desaloca a VM. No entanto, se você desligar por meio do sistema operacional enquanto estiver conectado, a VM será interrompida, mas _não_ desalocada e, portanto, você ainda será cobrado.

- **Excluindo uma VM.** Se você excluir uma VM, os VHDs não serão excluídos. Isso significa que você poderá excluir com segurança a VM sem perda de dados. No entanto, você ainda será cobrado pelo armazenamento. Para excluir o VHD, exclua o arquivo do [armazenamento de blobs][blob-storage].

  Para evitar a exclusão acidental, use um [bloqueio de recurso][resource-lock] para bloquear o grupo de recursos inteiro ou bloquear recursos individuais, como a VM.



## Considerações de segurança

- Use a [Central de Segurança do Azure][security-center] para ter uma exibição central do estado da segurança de seus recursos do Azure. A Central de Segurança monitora problemas de segurança potenciais, como atualizações do sistema e antimalware, e fornece uma visão abrangente da integridade de segurança de sua implantação.

    - A Central de Segurança é configurada por assinatura do Azure. Habilite a coleta de dados de segurança, conforme descrito em [Usar a Central de Segurança].
    - Depois que a coleta de dados for habilitada, a Central de Segurança examinará automaticamente todas as VMs criadas nessa assinatura.

- **Gerenciamento de patch.** Se for habilitada, a Central de Segurança verificará se atualizações críticas e de segurança estão ausentes. Use as [configurações da Política de Grupo][group-policy] na VM para habilitar as atualizações automáticas do sistema.

- **Antimalware.** Se for habilitada, a Central de Segurança verificará se o software antimalware está instalado. Você também pode usar a Central de Segurança para instalar o software antimalware por meio do Portal do Azure.

- Use o [RBAC][rbac] (controle de acesso baseado em função) para controlar o acesso aos recursos do Azure implantados. O RBAC permite atribuir funções de autorização aos membros de sua equipe de DevOps. Por exemplo, a função Leitor pode exibir os recursos do Azure, mas não criar, gerenciar nem excluí-los. Algumas funções são específicas a determinados tipos de recursos do Azure. Por exemplo, a função Colaborador da Máquina Virtual pode reiniciar ou desalocar uma VM, redefinir a senha de administrador, criar uma nova VM e assim por diante. Outras [funções RBAC internas][rbac-roles] que podem ser úteis para esta arquitetura de referência incluem [Usuário de DevTest Lab][rbac-devtest] e [Colaborador de Rede][rbac-network]. Um usuário pode ser atribuído a várias funções, e você pode criar funções personalizadas para permissões ainda mais refinadas.

    > [AZURE.NOTE] O RBAC não limita as ações que podem ser executadas por um usuário conectado a uma VM. Essas permissões são determinadas pelo tipo de conta no SO convidado.

- Para redefinir a senha de administrador local, execute o comando `vm reset-access` da CLI do Azure.

    ```text
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
    ```

- Use os [logs de auditoria][audit-logs] para ver as ações de provisionamento e outros eventos da VM.

- Considere o uso do [Azure Disk Encryption][disk-encryption] se você precisar criptografar os discos do sistema operacional e de dados.

## Script de implantação de exemplo

O seguinte script em lotes do Windows executa os comandos da [CLI do Azure][azure-cli] para implantar uma única instância VM e os recursos de rede e armazenamento relacionados, como mostra o diagrama anterior.

O script usa as convenções de nomenclatura descritas em [Recommended Naming Conventions for Azure Resources][naming conventions] (Convenções de nomenclatura recomendadas para recursos do Azure).

```bat
ECHO OFF
SETLOCAL

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Set up variables for deploying resources to Azure.
:: Change these variables for your own deployment.

:: The APP_NAME variable must not exceed 4 characters in size.
:: If it does the 15 character size limitation of the VM name may be exceeded.

SET APP_NAME=app1
SET LOCATION=eastus2
SET ENVIRONMENT=dev
SET USERNAME=testuser


:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see:
::   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
:: To see the VM sizes available in a region:
:: 	azure vm sizes --location <location>
SET VM_SIZE=Standard_DS1

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

IF "%2"=="" (
    ECHO Usage: %0 subscription-id admin-password
    EXIT /B
    )

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1
SET PASSWORD=%2

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

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% --location %LOCATION% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% --location %LOCATION% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --network-security-group-name %NSG_NAME% ^
  %POSTFIX%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% --location %LOCATION% %POSTFIX%

:: Create the NIC
CALL azure network nic create --public-ip-name %IP_NAME% --subnet-name ^
  %SUBNET_NAME% --subnet-vnet-name %VNET_NAME%  --name %NIC_NAME% --location ^
  %LOCATION% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS --location %LOCATION% %POSTFIX% ^
  %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS --location %LOCATION% %POSTFIX% ^
  %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --vnet-name %VNET_NAME% --nic-name %NIC_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" --location %LOCATION% ^
  %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new --vm-name %VM_NAME% --size-in-gb 128 --vhd-name ^
  data1.vhd --storage-account-name %VHD_STORAGE% %POSTFIX%

:: Allow RDP
CALL azure network nsg rule create --nsg-name %NSG_NAME% --direction Inbound ^
  --protocol Tcp --destination-port-range 3389 --source-port-range * ^
  --priority 100 --access Allow RDPAllow %POSTFIX%
```

## Próximas etapas

Para que o [SLA para Máquinas Virtuais][vm-sla] seja aplicado, é necessário implantar duas ou mais instâncias em um Conjunto de Disponibilidade. Para obter mais informações, veja [Running multiple Windows VMs on Azure][multi-vm] (Executando várias VMs Windows no Azure).

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-windows-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/pt-BR/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/pt-BR/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[group-policy]: https://technet.microsoft.com/pt-BR/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/pt-BR/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/pt-BR/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/pt-BR/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/pt-BR/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[security-center]: https://azure.microsoft.com/pt-BR/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/pt-BR/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[Usar a Central de Segurança]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/pt-BR/support/legal/sla/virtual-machines/v1_0/

<!---HONumber=AcomDC_0713_2016-->