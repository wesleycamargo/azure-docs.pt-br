Este artigo descreve um conjunto de práticas comprovadas para a execução de uma VM (máquina virtual) do Linux no Azure, considerando a escalabilidade, disponibilidade, capacidade de gerenciamento e segurança. O Azure dá suporte à execução de diversas distribuições Linux populares, incluindo CentOS, Debian, Red Hat Enterprise, Ubuntu e FreeBSD. Para obter mais informações, veja [Azure e Linux][azure-linux].

> [AZURE.NOTE] O Azure tem dois modelos de implantação diferentes: [Gerenciador de Recursos][resource-manager-overview] e clássico. Este artigo usa o Gerenciador de Recursos, recomendado pela Microsoft para novas implantações.

Não recomendamos usar uma única VM para cargas de trabalho de produção, pois não há nenhum SLA de tempo de atividade para VMs únicas no Azure. Para obter o SLA, é necessário implantar várias VMs em um conjunto de disponibilidade. Para obter mais informações, veja [Executando várias VMs no Azure][multi-vm].

## Diagrama da arquitetura

O provisionamento de uma VM no Azure envolve mais partes móveis do que apenas a própria VM. Há elementos de computação, de rede e de armazenamento.

![IaaS: VM única](./media/guidance-blueprints/compute-single-vm.png)

- **Grupo de recursos.** Um [_grupo de recursos_][resource-manager-overview] é um contêiner que armazena os recursos relacionados. Crie um grupo de recursos para armazenar os recursos desta VM.

- **VM**. Você pode provisionar uma VM de uma lista de imagens publicadas ou de um arquivo VHD que você carrega no Armazenamento de Blobs do Azure.

- **Disco do sistema operacional.** O disco do sistema operacional é um VHD armazenado no [armazenamento do Azure][azure-storage]. Isso significa que ele persistirá mesmo se a máquina host falhar. O disco do sistema operacional é `/dev/sda1`

- **Disco temporário.** A VM é criada com um disco temporário. Esse disco é armazenado em uma unidade física no computador host. Ele _não_ é salvo no armazenamento do Azure e poderá desaparecer durante as reinicializações e outros eventos de ciclo de vida da VM. Use esse disco somente para dados temporários, como arquivos de paginação ou de permuta. O disco temporário é `/dev/sdb1` e é montado em `/mnt/resource` ou `/mnt`.

- **Discos de dados.** Um [disco de dados][data-disk] é um VHD persistente usado para dados de aplicativo. Os discos de dados são armazenados no Armazenamento do Azure, como o disco do sistema operacional.

- **Rede Virtual e sub-rede.** Cada VM no Azure é implantada em uma Rede Virtual, que é dividida em sub-redes.

- **Endereço IP público.** Um endereço IP público é necessário para se comunicar com a VM, por exemplo, via SSH.

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

- Adicione um ou mais discos de dados. Quando você cria um novo VHD, ele não está formatado. Faça logon na VM para formatar o disco. Os discos de dados serão exibidos como `/dev/sdc`, `/dev/sdd` e assim por diante. Você pode executar `lsblk` para listar os dispositivos de bloco, incluindo os discos. Para usar um disco de dados, crie uma nova partição e o sistema de arquivos e, em seguida, monte o disco. Por exemplo:

    ```bat
    # Create a partition.
    sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     
    
    # Create a file system.
    sudo mkfs -t ext3 /dev/sdc1
    
    # Mount the drive.
    sudo mkdir /data1
    sudo mount /dev/sdc1 /data1
    ```

- Se você tiver uma grande quantidade de discos de dados, esteja ciente dos limites totais de E/S da conta de armazenamento. Para obter mais informações, confira [Limites de disco da máquina virtual][vm-disk-limits].

- Quando você adiciona um disco de dados, uma ID de LUN (número de unidade lógica) é atribuída ao disco. Opcionalmente, você poderá especificar a ID do LUN – por exemplo, se estiver substituindo um disco e quiser manter a mesma ID de LUN ou tiver um aplicativo que está procurando uma ID de LUN específica. No entanto, lembre-se de que as IDs de LUN devem ser exclusivas para cada disco.

- Talvez você queira alterar o agendador de E/S para otimizar o desempenho nas SSDs (usadas pelo Armazenamento Premium). Uma recomendação comum é usar o agendador NOOP para SSDs, mas é necessário usar uma ferramenta como [iostat] para monitorar o desempenho de E/S de disco para sua carga de trabalho específica.

- Para obter o melhor desempenho, crie uma conta de armazenamento separada para armazenar logs de diagnóstico. Uma conta LRS (armazenamento com redundância local) padrão é suficiente para os logs de diagnóstico.


### Recomendações de rede

- Esse endereço IP público pode ser dinâmico ou estático. O padrão é dinâmico.

    - Reserve um [endereço IP estático][static-ip] se precisar de um endereço IP fixo que não mudará – por exemplo, se você precisar criar um registro A no DNS ou se precisar do endereço IP para colocar na lista branca.

    - Você também pode criar um FQDN (nome de domínio totalmente qualificado) para o endereço IP. Em seguida, é possível registrar um [registro CNAME][cname-record] no DNS que aponta para o FQDN. Para obter mais informações, veja [Criar um nome de domínio totalmente qualificado no portal do Azure][fqdn].

- Todos os NSGs contêm um conjunto de [regras padrão][nsg-default-rules], incluindo uma regra que bloqueia todo o tráfego de Internet de entrada. As regras padrão não podem ser excluídas, mas outras regras podem substituí-las. Para habilitar o tráfego de Internet, crie regras que permitam o tráfego de entrada em portas específicas – por exemplo, a porta 80 para HTTP.

- Para habilitar o SSH, adicione uma regra ao NSG que permita o tráfego de entrada na porta TCP 22.

## Considerações sobre escalabilidade

- Você pode escalar ou reduzir uma VM verticalmente [alterando o tamanho da VM][vm-resize]. 

- Para escalar horizontalmente, coloque duas ou mais VMs em um conjunto de disponibilidade atrás de um balanceador de carga. Para obter mais informações, veja [Running multiple VMs on Azure][multi-vm] (Executando várias VMs no Azure).

## Considerações sobre disponibilidade

- Como observamos acima, não há nenhum SLA para uma única VM. Para obter o SLA, é necessário implantar várias VMs em um conjunto de disponibilidade.

- Sua VM pode ser afetada por uma [manutenção planejada][planned-maintenance] ou [manutenção não planejada][manage-vm-availability]. Você pode usar os [logs de reinicialização da VM][reboot-logs] para determinar se uma reinicialização da VM foi causada por uma manutenção planejada.

- Os VHDs têm suporte no [Armazenamento do Azure][azure-storage], que é replicado para obter durabilidade e disponibilidade.

- Para proteger contra perda acidental de dados durante operações normais (por exemplo, devido ao erro do usuário), você também deve implementar backups pontuais usando [instantâneos de blob][blob-snapshot] ou outra ferramenta.

## Considerações sobre capacidade de gerenciamento

- **Grupos de recursos.** Coloque recursos acoplados rigidamente que compartilham o mesmo ciclo de vida em um mesmo [grupo de recursos][resource-manager-overview]. Os grupos de recursos permitem implantar e monitorar recursos como um grupo, além de acumular custos de cobrança por grupo de recursos. Também é possível excluir recursos como um conjunto, o que é muito útil para implantações de teste. Dê nomes significativos aos recursos. Isso facilita a localização de um recurso específico e o entendimento de sua função. Veja [Recommended Naming Conventions for Azure Resources][naming conventions] (Convenções de nomenclatura recomendadas para recursos do Azure).

- **SSH**. Antes de criar uma VM do Linux, gere um par de chaves pública-privada do RSA de 2048 bits. Use o arquivo de chave pública ao criar a VM. Para obter mais informações, veja [Como usar SSH com Linux e Mac no Azure][ssh-linux].

- **Diagnóstico da VM.** Habilite o monitoramento e diagnóstico, incluindo métricas de integridade básicas, logs de infraestrutura de diagnóstico e [diagnóstico de inicialização][boot-diagnostics]. O diagnóstico de inicialização poderá ajudar a diagnosticar uma falha de inicialização se sua VM entrar em um estado não inicializável. Para obter mais informações, veja [Habilitar monitoramento e diagnóstico][enable-monitoring].

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

- Automatize as atualizações do sistema operacional usando a extensão de VM [OSPatching]. Instale essa extensão ao provisionar a VM. Você pode especificar a frequência de instalação dos patches e se haverá necessidade de reinicialização após a aplicação de patch.

- Use o [RBAC][rbac] (controle de acesso baseado em função) para controlar o acesso aos recursos do Azure implantados. O RBAC permite atribuir funções de autorização aos membros de sua equipe de DevOps. Por exemplo, a função Leitor pode exibir os recursos do Azure, mas não criar, gerenciar nem excluí-los. Algumas funções são específicas a determinados tipos de recursos do Azure. Por exemplo, a função Colaborador da Máquina Virtual pode reiniciar ou desalocar uma VM, redefinir a senha de administrador, criar uma nova VM e assim por diante. Outras [funções RBAC internas][rbac-roles] que podem ser úteis para esta arquitetura de referência incluem [Usuário de DevTest Lab][rbac-devtest] e [Colaborador de Rede][rbac-network]. Um usuário pode ser atribuído a várias funções, e você pode criar funções personalizadas para permissões ainda mais refinadas.

    > [AZURE.NOTE] O RBAC não limita as ações que podem ser executadas por um usuário conectado a uma VM. Essas permissões são determinadas pelo tipo de conta no SO convidado.

- Use os [logs de auditoria][audit-logs] para ver as ações de provisionamento e outros eventos da VM.

- Considere o uso do [Azure Disk Encryption][disk-encryption] se você precisar criptografar os discos do sistema operacional e de dados.

## Script de implantação de exemplo

O seguinte script Bash executa os comandos da [CLI do Azure][azure-cli] para implantar uma única instância VM e os recursos de rede e armazenamento relacionados, como mostra o diagrama anterior.

O script usa as convenções de nomenclatura descritas em [Recommended Naming Conventions for Azure Resources][naming conventions] (Convenções de nomenclatura recomendadas para recursos do Azure).

Para executar o script:

1. Gere uma chave de autenticação do RSA de 2048 bits.

        ssh-keygen -t rsa -b 2048

2. Passe a ID de assinatura do Azure e o nome do arquivo de chave pública como parâmetros para o script.

        ./azurecli-single-vm-sample.sh <subscription ID> ~/.ssh/id_rsa.pub

3. Quando o script for concluído, faça logon na VM usando SSH. Use a chave privada para autenticar.

        ssh testuser@<app>-vm1.<location>.cloudapp.azure.com -i ~/.ssh/id_rsa

    em que `<app>` é o valor da variável de script `APP_NAME`, e `<location>` é o valor da variável `LOCATION`.

```bat
#!/bin/bash

############################################################################
#script for generating infrastructure for single VM running linux          #
# of user choice. It creates azure resource group, storage account for VM  #
# vnet, subnets for VM, and NSG rule                                       #
# tags for main variables used                                             #
# ScriptCommandParameters                                                  #
# ScriptVars                                                               #
############################################################################

############################################################################
# User defined functions for single VM script                              #
# errhandle : handles errors via trap if any exception happens             #
# in the cli execution or if the user interrupts with CTRL+C               #
# allowing for fast interruption                                           #
############################################################################

# error handling or interruption via ctrl-c.
# line number and error code of executed command is passed to errhandle function

trap 'errhandle $LINENO $?' SIGINT ERR

errhandle()
{
  echo "Error or Interruption at line ${1} exit code ${2} "
  exit ${2}
}

###############################################################################
############################## End of user defined functions ##################
###############################################################################

# 2 paramaters are expected
# public key file needs to be generated using ssh-keygen

if [ $# -ne 2  ]
then
	echo  "Usage:  ${0}  subscription-id public-ssh-key-file"
	exit
fi

if [ ! -f $2  ]
then
	echo "Public Key file ${2} does not exist. please generate it"
	echo "ssh-keygen -t rsa -b 2048"
	exit
fi

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
# ScriptCommandParameters
SUBSCRIPTION=$1
PUBLICKEYFILE=$2

# ScriptVars
LOCATION=eastus2
APP_NAME=app1
ENVIRONMENT=dev
USERNAME=testuser
VM_NAME="${APP_NAME}-vm1"
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
IP_NAME="${APP_NAME}-pip"
NIC_NAME="${VM_NAME}-1nic"
NSG_NAME="${APP_NAME}-nsg"
SUBNET_NAME="${APP_NAME}-subnet"
VNET_NAME="${APP_NAME}-vnet"
VHD_STORAGE="${VM_NAME//-}st1"
DIAGNOSTICS_STORAGE="${VM_NAME//-}diag"

# Use the following command to get the list of URNs for RHEL, UBUNTU, and OPENSUSE:
# RHEL
# azure vm image list $LOCATION  redhat RHEL 7.2
# UBUNTU
# azure vm image list $LOCATION canonical ubuntuserver 14.04.3-LTS
# SUSE
# azure vm image $LOCATION  suse opensuse 13.2

LINUX_BASE_IMAGE=redhat:rhel:7.2:7.2.20160302

# For a list of VM sizes see: 
#   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
# To see the VM sizes available in a region:
# 	azure vm sizes --location <location>
VM_SIZE=Standard_DS1

# Set up the postfix variables attached to most CLI commands

POSTFIX="--resource-group ${RESOURCE_GROUP} --subscription ${SUBSCRIPTION}"

azure config mode arm

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#Create resources

#Create the enclosing resource group
azure group create --name $RESOURCE_GROUP --location $LOCATION --subscription $SUBSCRIPTION

# Create the VNet
azure network vnet create --address-prefixes 172.17.0.0/16 --name $VNET_NAME \
--location $LOCATION $POSTFIX

#Create the network security group
azure network nsg create --name $NSG_NAME --location $LOCATION $POSTFIX

#Create the subnet
azure network vnet subnet create --vnet-name $VNET_NAME --address-prefix  "172.17.0.0/24" \
--name $SUBNET_NAME --network-security-group-name $NSG_NAME $POSTFIX

#Create the public IP address (dynamic)
azure network public-ip create --name $IP_NAME --domain-name-label $VM_NAME \
--location $LOCATION $POSTFIX

#Create the NIC
azure network nic create --public-ip-name $IP_NAME --subnet-name $SUBNET_NAME \
--subnet-vnet-name $VNET_NAME --name $NIC_NAME --location $LOCATION $POSTFIX

#Create the storage account for the OS VHD
azure storage account create --type PLRS --location $LOCATION $POSTFIX $VHD_STORAGE

#Create the storage account for diagnostics logs
azure storage account create --type LRS --location $LOCATION $POSTFIX $DIAGNOSTICS_STORAGE

#Create the VM
azure vm create --name $VM_NAME --os-type Linux --image-urn  $LINUX_BASE_IMAGE \
--vm-size $VM_SIZE --vnet-subnet-name $SUBNET_NAME --vnet-name $VNET_NAME \
--nic-name $NIC_NAME --storage-account-name $VHD_STORAGE \
--os-disk-vhd "${VM_NAME}-osdisk.vhd" --admin-username $USERNAME \
--ssh-publickey-file $PUBLICKEYFILE --boot-diagnostics-storage-uri \
"https://${DIAGNOSTICS_STORAGE}.blob.core.windows.net/" --location $LOCATION $POSTFIX

#Attach a data disk
azure vm disk attach-new --vm-name $VM_NAME --size-in-gb 128 --vhd-name data1.vhd \
--storage-account-name $VHD_STORAGE $POSTFIX

#Allow SSH
azure network nsg rule create --nsg-name $NSG_NAME --direction Inbound --protocol Tcp \
--destination-port-range 22  --source-port-range "*"  --priority 100 --access Allow \
SSHAllow $POSTFIX

#Install patching extension
PATCH_CONFIG='{"rebootAfterPatch":"RebootIfNeed","startTime":"3:00","dayOfWeek":"Sunday","category":"ImportantAndRecommended"}'
azure vm extension set --name OSPatchingForLinux --publisher-name Microsoft.OSTCExtensions \
--public-config $PATCH_CONFIG --vm-name $VM_NAME --version 2.0 $POSTFIX
```

## Próximas etapas

Para que o [SLA para Máquinas Virtuais][vm-sla] seja aplicado, é necessário implantar duas ou mais instâncias em um Conjunto de Disponibilidade. Para obter mais informações, veja [Executando várias VMs no Azure][multi-vm].

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/pt-BR/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/pt-BR/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-linux-portal-create-fqdn.md
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/pt-BR/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/pt-BR/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/pt-BR/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/pt-BR/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-ssh-from-linux.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/pt-BR/support/legal/sla/virtual-machines/v1_0/

<!---HONumber=AcomDC_0622_2016-->