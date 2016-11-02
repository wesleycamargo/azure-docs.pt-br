<properties
    pageTitle="Comandos da CLI do Azure no modo de Gerenciamento de Serviços | Microsoft Azure"
    description="Comandos da CLI (interface de linha de comando) do Azure no modo de Gerenciamento de Serviços para gerenciar implantações no modelo de implantação clássica"
    services="virtual-machines-linux,virtual-machines-windows,mobile-services, cloud-services"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="danlep"/>


# <a name="azure-cli-commands-in-azure-service-management-(asm)-mode"></a>Comandos da CLI do Azure no modo ASM (Gerenciamento de Serviços do Azure)

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-classic-include.md)] Você também pode [ler sobre todos os comandos de modelo do Resource Manager](virtual-machines/azure-cli-arm-commands.md) e usar a CLI para [migrar recursos](virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md) do clássico para o modelo do Resource Manager.

Este artigo fornece a sintaxe e as opções de comandos da CLI do Azure que, normalmente, seriam usadas para criar e gerenciar recursos do Azure no modelo de implantação clássica. É possível acessar esses comandos executando a CLI no modo ASM (Gerenciamento de Serviços). Essa não é uma referência completa, e sua versão da CLI poderá mostrar comandos ou parâmetros um pouco diferentes. 

Para começar, primeiro [instale a CLI do Azure](xplat-cli-install.md) e [conecte-se à sua assinatura do Azure](xplat-cli-connect.md).

Para ver a sintaxe do comando atual e as opções na linha de comando, digite `azure help` ou para exibir a ajuda de um comando específico, `azure help [command]`. Também é possível encontrar exemplos da CLI na documentação de criação e gerenciamento de serviços específicos do Azure.

Parâmetros opcionais são mostrados entre colchetes (por exemplo, `[parameter]`). Todos os outros parâmetros são obrigatórios.

Além dos parâmetros opcionais específicos aos comandos documentados aqui, há três parâmetros opcionais que podem ser usados para exibir saída detalhada, como opções de solicitação e códigos de status. O parâmetro `-v` fornece uma saída detalhada e o parâmetro `-vv` fornece uma saída mais detalhada ainda. A opção `--json` produzi o resultado no formato JSON bruto.

## <a name="setting-asm-mode"></a>Configurando o modo asm

Use o comando a seguir para habilitar os comandos de modo de Gerenciamento de Serviços de CLI do Azure.

    azure config mode asm

>[AZURE.NOTE] O modo do Azure Resource Manager da CLI e o modo do Gerenciamento de Serviços do Azure são mutuamente exclusivos. Ou seja, recursos criados em um modo não podem ser gerenciados no outro modo.

## <a name="manage-your-account-information-and-publish-settings"></a>Gerenciar suas informações de conta e configurações de publicação
Uma maneira que a CLI pode se conectar à sua conta é usando as informações da assinatura do Azure. (Veja [Conectar-se a uma assinatura do Azure por meio da CLI do Azure](xplat-cli-connect.md) para obter outras opções.) Essas informações podem ser obtidas no portal clássico do Azure em um arquivo de configurações de publicação conforme descrito aqui. É possível importar o arquivo de configurações de publicação como uma definição de configuração local persistente usada pela CLI para operações posteriores. Você precisa importar as configurações de publicação apenas uma vez.

**account download [opções]**

Esse comando inicia um navegador para baixar o arquivo .publishsettings do portal clássico do Azure.

    ~$ azure account download
    info:   Executing command account download
    info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
    help:   Save the downloaded file, then execute the command
    help:   account import <file>
    info:   account download command OK

**account import [opções] &lt;arquivo>**


Esse comando importa um arquivo publishsettings ou certificado para que possa ser usado pela ferramenta em sessões futuras.

    ~$ azure account import publishsettings.publishsettings
    info:   Importing publish settings file publishsettings.publishsettings
    info:   Found subscription: 3-Month Free Trial
    info:   Found subscription: Pay-As-You-Go
    info:   Setting default subscription to: 3-Month Free Trial
    warn:   The 'publishsettings.publishsettings' file contains sensitive information.
    warn:   Remember to delete it now that it has been imported.
    info:   Account publish settings imported successfully

> [AZURE.NOTE] O arquivo publishsettings pode conter detalhes (ou seja, o nome e a ID da assinatura) sobre mais de uma assinatura. Quando você importa o arquivo publishsettings, a primeira assinatura é usada como descrição padrão. Para usar uma assinatura diferente, execute o comando a seguir:
<code>~$ azure config set subscription &lt;other-subscription-id&gt;</code>

**account clear [opções]**

Esse comando remove as configurações de publicação armazenadas que foram importadas. Use esse comando se você terminar de usar a ferramenta neste computador e desejar garantir que a ferramenta não possa ser usada com sua conta em sessões futuras.

    ~$ azure account clear
    Clearing account info.
    info:   OK

**account list [opções]**

Lista as assinaturas importadas

    ~$ azure account list
    info:    Executing command account list
    data:    Name                                    Id
           Current
    data:    --------------------------------------  -------------------------------
    -----  -------
    data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
    data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
    data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account set [opções] &lt;assinatura&gt;**

Define a assinatura atual

###<a name="commands-to-manage-your-affinity-groups"></a>Comandos para gerenciar os grupos de afinidade

**account affinity-group list [opções]**

Esse comando lista os grupos de afinidade do Azure.

Os grupos de afinidade podem ser definidos quando um grupo de máquinas virtuais abrange várias máquinas físicas. O grupo de afinidade especifica que máquinas físicas devem estar o mais próximo possível umas das outras, para reduzir a latência de rede.

    ~$ azure account affinity-group list
    + Fetching affinity groups
    data:   Name                                  Label   Location
    data:   ------------------------------------  ------  --------
    data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
    info:   account affinity-group list command OK

**account affinity-group create [opções] &lt;nome&gt;**

Esse comando cria um grupo de afinidade

    ~$ azure account affinity-group create opentec -l "West US"
    info:    Executing command account affinity-group create
    + Creating affinity group
    info:    account affinity-group create command OK

**account affinity-group show [opções] &lt;nome&gt;**

Esse comando mostra os detalhes do grupo de afinidade

    ~$ azure account affinity-group show opentec
    info:    Executing command account affinity-group show
    + Getting affinity groups
    data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
    data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
    data:    Name "opentec"
    data:    Label "b3BlbnRlYw=="
    data:    Description $ i:nil "true"
    data:    Location "West US"
    data:    HostedServices ""
    data:    StorageServices ""
    data:    Capabilities Capability 0 "PersistentVMRole"
    data:    Capabilities Capability 1 "HighMemory"
    info:    account affinity-group show command OK

**account affinity-group delete [opções] &lt;name&gt;**

Esse comando exclui o grupo de afinidade especificado

    ~$ azure account affinity-group delete opentec
    info:    Executing command account affinity-group delete
    Delete affinity group opentec? [y/n] y
    + Deleting affinity group
    info:    account affinity-group delete command OK

###<a name="commands-to-manage-your-account-environment"></a>Comandos para gerenciar seu ambiente de conta

**account env list [opções]**

Lista dos ambientes de conta

    C:\windows\system32>azure account env list
    info:    Executing command account env list
    data:    Name
    data:    ---------------
    data:    AzureCloud
    data:    AzureChinaCloud
    info:    account env list command OK

**account env show [opções] [ambiente]**

Mostrar detalhes do ambiente da conta

    ~$ azure account env show
    info:    Executing command account env show
    Environment name: AzureCloud
    data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
    data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
    info:    account env show command OK

**account env add [opções] [ambiente]**

Esse comando adiciona um ambiente à conta

**account env set [opções] [ambiente]**

Esse comando define o ambiente da conta

**account env delete [opções] [ambiente]**

Esse comando exclui o ambiente especificado da conta

## <a name="commands-to-manage-your-classic-virtual-machines"></a>Comandos para gerenciar as máquinas virtuais clássicas
O diagrama a seguir mostra como as máquinas virtuais clássicas do Azure são hospedadas no ambiente de implantação de produção de um serviço de nuvem do Azure.

![Diagrama técnico do Azure](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**create-new** cria a unidade no armazenamento de blobs (isto é, e:\ no diagrama); **attach** anexa um disco já criado, mas desanexado, à máquina virtual.

**vm create [opções] &lt;nome_dns> &lt;imagem> &lt;nome_de_usuário> [password]**

Esse comando cria uma máquina virtual do Azure. Por padrão, cada máquina virtual (vm) é criada em seu próprio serviço de nuvem. Você pode especificar se uma máquina virtual deve ser adicionada a um serviço de nuvem existente por meio do uso da opção -c, conforme documentado aqui.

O comando create vm, como o portal clássico do Azure, só cria máquinas virtuais no ambiente de implantação de produção. Não há nenhuma opção para criar uma máquina virtual no ambiente de implantação de preparo de um serviço de nuvem. Se sua assinatura não tem uma conta de armazenamento existente do Azure, o comando cria uma.

Você pode especificar um local por meio do parâmetro --location ou especificar um grupo de afinidade por meio do parâmetro --affinity-group. Se nenhum dos parâmetros for fornecido, você será solicitado a fornecer um de uma lista de locais válidos.

A senha fornecida deve ter de 8 a 123 caracteres e atender aos requisitos de complexidade de senha do sistema operacional que você está usando para esta máquina virtual.

Se você planeja usar SSH para gerenciar uma máquina virtual Linux implantada (como normalmente é o caso), deverá ativar SSH por meio da opção -e ao criar a máquina virtual. Não é possível ativar SSH após a máquina virtual ter sido criada.

As máquinas virtuais do Windows podem habilitar RDP posteriormente adicionando a porta 3389 como um ponto de extremidade.

Os seguintes parâmetros opcionais são aceitos para esse comando:

**-c, --connect** criar a máquina virtual dentro de uma implantação já criada em um serviço de hospedagem. Se -vmname não for usado com essa opção, o nome da nova máquina virtual será gerado automaticamente.<br />
**-n, --vm-name** especifica o nome da máquina virtual. Por padrão, esse parâmetro usa o nome do serviço de hospedagem. Se -vmname não for especificado, o nome para a nova máquina virtual será gerado como &lt;service-name>&lt;id>, em que &lt;id> é o número de máquinas virtuais existentes no serviço mais 1. Por exemplo, se você usar esse comando para adicionar uma máquina virtual a um serviço de hospedagem MyService que tem uma máquina virtual existente, a nova máquina virtual será chamada de MyService2.<br />
**-u, --blob-url** Especifique a URL de armazenamento de blobs de destino no qual criar o disco de sistema da máquina virtual. <br />
**-z, --vm-size** Especifique o tamanho da máquina virtual. Os valores válidos são: "ExtraSmall", "Small", "Medium", "Large", "ExtraLarge", "A5", "A6", "A7", "A8", "A9", "A10", "A11", "Basic_A0", "Basic_A1", "Basic_A2", "Basic_A3", "Basic_A4", "Standard_D1", "Standard_D2", "Standard_D3", "Standard_D4", "Standard_D11", "Standard_D12", "Standard_D13", "Standard_D14", "Standard_DS1", "Standard_DS2", "Standard_DS3", "Standard_DS4", "Standard_DS11", "Standard_DS12", "Standard_DS13", "Standard_DS14", "Standard_G1", "Standard_G2", "Standard_G3", "Standard_G4", "Standard_G55". O valor padrão é "Small". <br />
**-r** Adiciona conectividade RDP a uma máquina virtual do Windows. <br />
**-e, --ssh** Adiciona conectividade SSH a uma máquina virtual do Windows. <br />
**-t, --ssh-cert** Especifica o certificado SSH. <br />
**-s** A assinatura <br />
**-o, --community** A imagem especificada é uma imagem da comunidade <br />
**-w** O nome da rede virtual <br/>
**-l, --location** especifica a localização (por exemplo, "Centro-Norte dos EUA"). <br />
**-a, --affinity-group** especifica o grupo de afinidades.<br />
**-w, --virtual-network-name** Especifique a rede virtual à qual adicionar a nova máquina virtual. As redes virtuais podem ser configuradas e gerenciadas no Portal Clássico do Azure.<br />
**-b, --subnet-names** Especifica os nomes de sub-redes a atribuir à máquina virtual.

Neste exemplo, MSFT__Win2K8R2SP1-120514-1520-141205-01-en-us-30GB é uma imagem fornecida pela plataforma. Para obter mais informações sobre imagens do sistema operacional, consulte vm image list.

    ~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
    info:   Executing command vm create
    Enter VM 'my-vm-name' password: ************
    info:   vm create command OK

**vm create-from &lt;nome_dns> &lt;arquivo_de_função>**

Esse comando cria uma máquina virtual do Azure em um arquivo de função JSON.

    ~$ azure vm create-from my-vm example.json
    info:   OK

**vm list [opções]**

Esse comando lista as máquinas virtuais do Azure. A opção -json especifica que os resultados serão retornados em formato JSON bruto.

    ~$ azure vm list
    info:   Executing command vm list
    data:   DNS Name                          VM Name      Status
    data:   --------------------------------  -----------  ---------
    data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
    info:   vm list command OK

**vm location list [opções]**

Esse comando lista todos os locais disponíveis da conta do Azure.

    ~$ azure vm location list
    info:   Executing command vm location list
    data:   Name                   Display Name
    data:   ---------------------  ------------
    data:   Azure Preview  West US
    info:   account location list command OK

**vm show [opções] &lt;nome>**

Esse comando mostra detalhes sobre uma máquina virtual do Azure. A opção -json especifica que os resultados serão retornados em formato JSON bruto.

    ~$ azure vm show my-vm
    info:   Executing command vm show
    data:   {
    data:       InstanceSize: 'Small',
    data:       InstanceStatus: 'ReadyRole',
    data:       DataDisks: [],
    data:       IPAddress: '10.26.192.206',
    data:       DNSName: 'my-vm.cloudapp.net',
    data:       InstanceStateDetails: {},
    data:       VMName: 'my-vm',
    data:       Network: {
    data:           Endpoints: [
    data:               {
    data:                   Protocol: 'tcp',
    data:                   Vip: '65.52.250.250',
    data:                   Port: '63238' ,
    data:                   LocalPort: '3389',
    data:                   Name: 'RemoteDesktop'
    data:               }
    data:           ]
    data:       },
    data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
    data:   }
    info:   vm show command OK

**vm delete [opções] &lt;nome>**

Esse comando exclui uma máquina virtual do Azure. Por padrão, esse comando não exclui o blob do Azure no qual o disco do sistema operacional e o disco de dados são criados. Para excluir o blob e a máquina virtual na qual ele se baseia, especifique a opção -b.

    ~$ azure vm delete my-vm
    info:   Executing command vm delete
    info:   vm delete command OK

**vm start [opções] &lt;nome>**

Esse comando inicia uma máquina virtual do Azure.

    ~$ azure vm start my-vm
    info:   Executing command vm start
    info:   vm start command OK

**vm restart [opções] &lt;nome>**

Esse comando reinicia uma máquina virtual do Azure.

    ~$ azure vm restart my-vm
    info:   Executing command vm restart
    info:   vm restart command OK

**vm shutdown [opções] &lt;nome>**

Esse comando desliga uma máquina virtual do Azure. É possível utilizar a opção -p para especificar que o recurso de computação não seja liberado durante o desligamento.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**vm capture &lt;nome-da-vm> &lt;nome-da-imagem-de-destino>**

Esse comando captura uma imagem da máquina virtual do Azure.

Uma imagem de máquina virtual poderá ser capturada somente se o estado da máquina virtual for **Parado**. Desligue a máquina virtual antes de continuar.

    ~$ azure.cmd vm capture my-vm mycaptureimagename --delete
    info:   Executing command vm capture
    + Fetching VMs
    + Capturing VM
    info:   vm capture command OK

**vm export [opções] &lt;nome-da-vm> &lt;caminho-do-arquivo>**

Esse comando exporta uma imagem da máquina virtual do Azure para um arquivo.

    ~$ azure vm export "myvm" "C:\"
    info:    Executing command vm export
    + Getting virtual machines
    + Exporting the VM
    info:   vm export command OK

##  <a name="commands-to-manage-your-azure-virtual-machine-endpoints"></a>Comandos para gerenciar seus pontos de extremidade de máquinas virtuais do Azure
O diagrama a seguir mostra a arquitetura de uma implantação típica de várias instâncias de uma máquina virtual clássica. Neste exemplo, a porta 3389 é aberta em cada máquina virtual (para acesso ao RDP). Também há um endereço IP interno (por exemplo, 168.55.11.1) em cada máquina virtual que é usada pelo balanceador de carga para rotear o tráfego para a máquina virtual. Esse endereço IP interno também pode ser usado para comunicação entre máquinas virtuais.

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

As solicitações externas para máquinas virtuais passam por um balanceador de carga. Por esse motivo, as solicitações não podem ser especificadas em relação a uma máquina virtual específica em implantações com várias máquinas virtuais. Para implantações com várias máquinas virtuais, o mapeamento da porta deve ser configurado entre as máquinas virtuais (porta_da_vm) e o balanceador de carga (porta_do_bc).

**vm endpoint create &lt;nome-da-vm> &lt;porta-do-bc> [porta-da-vm]**

Esse comando cria um ponto de extremidade da máquina virtual. Você também pode usar -u ou --enable-direct-server-return para especificar se o retorno direto do servidor deve ser habilitado nesse ponto de extremidade, desabilitado por padrão.

    ~$ azure vm endpoint create my-vm 8888 8888
    azure vm endpoint create my-vm 8888 8888
    info:   Executing command vm endpoint create
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint create command OK

**vm endpoint create-multiple [opções] &lt;nome-da-vm> &lt;porta-do-bc>[:&lt;porta-da-vm>[:&lt;protocolo>[:&lt;habilitar-retorno-de-servidor-direto>[:&lt;nome-do-bc-definido>[:&lt;protocolo-de-investigação>[:&lt;porta-de-investigação>[:&lt;caminho-de-investigação>[:&lt;nome-do-bc-interno>]]]]]]]] {1-*}**

Cria vários pontos de extremidade de vm (máquina virtual).

**vm endpoint delete [opções] &lt;nome-da-vm> &lt;nome-do-ponto-de-extremidade>**

Esse comando exclui um ponto de extremidade da máquina virtual.

    ~$ azure vm endpoint delete my-vm http
    azure vm endpoint delete my-vm http
    info:   Executing command vm endpoint delete
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint delete command OK

**vm endpoint list &lt;nome-da-vm>**

Esse comando lista todos os pontos de extremidade da máquina virtual. A opção -json especifica que os resultados serão retornados em formato JSON bruto.

    ~$ azure vm endpoint list my-linux-vm
    data:   Name  External Port  Local Port
    data:   ----  -------------  ----------
    data:   ssh   22             22

**vm endpoint update [opções] &lt;nome-da-vm> &lt;nome-do-ponto-de-extremidade>**

Este comando atualiza um ponto de extremidade de vm para novos valores usando estas opções.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**vm endpoint show [opções] &lt;nome-da-vm>**

Esse comando mostra os detalhes dos pontos de extremidade em uma vm

    ~$ azure vm endpoint show "mycouchvm"
    info:    Executing command vm endpoint show
    + Getting virtual machines
    data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
    data:    Network Endpoints 0 LocalPort "5984"
    data:    Network Endpoints 0 Name "CouchDB_EP"
    data:    Network Endpoints 0 Port "5984"
    data:    Network Endpoints 0 Protocol "tcp"
    data:    Network Endpoints 0 Vip "168.61.9.97"
    data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
    data:    Network Endpoints 1 LocalPort "2020"
    data:    Network Endpoints 1 Name "CouchEP_2"
    data:    Network Endpoints 1 Port "2020"
    data:    Network Endpoints 1 Protocol "tcp"
    data:    Network Endpoints 1 Vip "168.61.9.97"
    data:    Network Endpoints 2 LocalPort "3389"
    data:    Network Endpoints 2 Name "RemoteDesktop"
    data:    Network Endpoints 2 Port "3389"
    data:    Network Endpoints 2 Protocol "tcp"
    data:    Network Endpoints 2 Vip "168.61.9.97"
    info:    vm endpoint show command OK

## <a name="commands-to-manage-your-azure-virtual-machine-images"></a>Comandos para gerenciar suas imagens de máquinas virtuais do Azure

Imagens de máquinas virtuais são capturas de máquinas virtuais já configuradas que podem ser replicadas conforme necessário.

**vm image list [opções]**

Esse comando obtém uma lista de imagens de máquinas virtuais. Existem três tipos de imagens: imagens criadas pela Microsoft, que são prefixadas com "MSFT", imagens criadas por terceiros, que são prefixadas com o nome do fornecedor e imagens criadas por você. Para criar imagens, você pode capturar uma máquina virtual existente ou criar uma imagem de um .vhd personalizado carregado no armazenamento de blob. Para obter mais informações sobre como usar um .vhd personalizado, consulte o comando vm image create.
A opção -json especifica que os resultados serão retornados em formato JSON bruto.

    ~$ azure vm image list
    data:   Name                                                                   Category   OS
    data:   ---------------------------------------------------------------------  ---------  -------
    data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
    data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
    data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
    data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
    data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
    data:   WIN2K8-R2-WINRM                                                        User       Windows
    info:   vm image list command OK

**vm image show [opções] &lt;nome>**

Esse comando mostra os detalhes da imagem de uma máquina virtual.

    ~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
    + Fetching VM image
    info:   Executing command vm image show
    data:   {
    data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
    data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
    data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
    data:       Category: 'Microsoft',
    data:       OS: 'Windows',
    data:       Eula: 'http://www.microsoft.com',
    data:       LogicalSizeInGB: '30'
    data:   }
    info:   vm image show command OK

**vm image delete [opções] &lt;nome>**

Esse comando exclui uma imagem de máquina virtual.

    ~$ azure vm image delete my-vm-image
    info:   Executing command vm image delete
    info:   VM image deleted: my-vm-image
    info:   vm image delete command OK

**vm image create &lt;nome> [caminho de origem]**

Esse comando cria uma imagem de máquina virtual. Os arquivos .vhd personalizados são carregados no armazenamento de blob e, em seguida, a imagem de máquina virtual é criada nela. Em seguida, você pode usar essa imagem de máquina virtual para criar uma máquina virtual. Os parâmetros Location e OS são obrigatórios.

>[AZURE.NOTE]No momento, este comando dá suporte apenas para o carregamento de arquivos .vhd fixos. Para carregar um arquivo .vhd dinâmico, use os [utilitários do VHD do Azure para prosseguir](https://github.com/Microsoft/azure-vhd-utils-for-go).

Alguns sistemas impõem limites de descritor de arquivo por processo. Se esse limite for excedido, a ferramenta exibirá um erro de limite de descritor de arquivo. Você pode executar o comando novamente usando o parâmetro -p &lt;número> para reduzir o número máximo de carregamentos paralelos. O número máximo padrão de carregamentos paralelos é 96.

    ~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
    info:   Executing command vm image create
    + Retrieving storage accounts
    info:   VHD size : 13 MB
    info:   Uploading 13312.5 KB
    Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
    info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
    info:   vm image create command OK

## <a name="commands-to-manage-your-azure-virtual-machine-data-disks"></a>Comandos para gerenciar seus discos de dados de máquinas virtuais do Azure

Os discos de dados são arquivos .vhd no armazenamento de blob que podem ser utilizados por uma máquina virtual. Para obter mais informações sobre como os discos de dados são implantados no armazenamento de blob, consulte o diagrama técnico do Azure mostrado anteriormente.

Os comandos para anexar discos de dados (azure vm disk attach e azure vm disk attach-new) atribuem um LUN (número de unidade lógica) ao disco de dados anexado, conforme exigido pelo protocolo SCSI. Ao primeiro disco de dados anexado a uma máquina virtual é atribuído o LUN 0, ao próximo é atribuído o LUN 1 e assim por diante.

Ao desanexar um disco de dados com o comando azure vm detach, use o parâmetro &lt;lun&gt; para indicar qual disco desanexar.

>[AZURE.NOTE] Você deve sempre desanexar discos de dados na ordem inversa, começando com o LUN de número mais alto que foi atribuído. A camada SCSI do Linux não oferece suporte à desanexação de um LUN de número inferior enquanto um LUN de número superior ainda estiver anexado. Por exemplo, você não deve desanexar o LUN 0, se o LUN 1 ainda estiver anexado.

**vm disk show [opções] &lt;nome>**

Esse comando mostra detalhes sobre um disco do Azure.

    ~$ azure vm disk show anucentos-anucentos-0-20120524070008
    info:   Executing command vm disk show
    data:   AttachedTo DeploymentName "mycentos"
    data:   AttachedTo HostedServiceName "myanucentos"
    data:   AttachedTo RoleName "myanucentos"
    data:   OS "Linux"
    data:   Location "Azure Preview"
    data:   LogicalDiskSizeInGB "30"
    data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
    data:   Name "mycentos-mycentos-0-20120524070008"
    data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
    info:   vm disk show command OK

**vm disk list [opções] [nome-da-vm]**

Esse comando lista discos do Azure ou discos anexados a uma máquina virtual específica. Se for executado com um parâmetro de nome de máquina virtual, retornará todos os discos anexados à máquina virtual. O LUN 1 é criado com a máquina virtual, e todos os outros discos listados são anexados separadamente.

    ~$ azure vm disk list mycentos
    info:   Executing command vm disk list
    data:   Lun  Size(GB)  Blob-Name
    data:   ---  --------  --------------------------------
    data:   1    30        mycentos-cb39b8223b01f95c.vhd
    data:   2    10        mycentos-e3f0d717950bb78d.vhd
    info:   vm disk list command OK

Executar esse comando sem um parâmetro de nome de máquina virtual retorna todos os discos.

    ~$ azure vm disk list
    data:   Name                                        OS
    data:   ------------------------------------------  -------
    data:   mycentos-mycentos-0-20120524070008          Linux
    data:   mycentos-mycentos-2-20120525055052
    data:   mywindows-winvm-20120522223119              Windows
    info:   vm disk list command OK

**vm disk delete [opções] &lt;nome>**

Esse comando exclui um disco do Azure de um repositório pessoal. O disco deve ser desanexado da máquina virtual antes de ser excluído.

    ~$ azure vm disk delete mycentos-mycentos-2-20120525055052
    info:   Executing command vm disk delete
    info:   Disk deleted: mycentos-mycentos-2-20120525055052
    info:   vm disk delete command OK

**vm disk create &lt;nome> [caminho-de-origem]**

Esse comando carrega e registra um disco do Azure. --blob-url, --location, ou --affinity-group deve ser especificado. Se você usar esse comando com [caminho_de_origem], o arquivo .vhd especificado será carregado e uma imagem será criada. Em seguida, você pode anexar essa imagem a uma máquina virtual usando o comando vm disk attach.

Alguns sistemas impõem limites de descritor de arquivo por processo. Se esse limite for excedido, a ferramenta exibirá um erro de limite de descritor de arquivo. Você pode executar o comando novamente usando o parâmetro -p &lt;número> para reduzir o número máximo de carregamentos paralelos. O número máximo padrão de carregamentos paralelos é 96.

    ~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
    info:   Executing command vm disk create
    info:   VHD size : 10 MB
    info:   Uploading 10240.5 KB
    Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
    info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
    info:   vm disk create command OK

**vm disk upload [opções] &lt;caminho-de-origem> &lt;url-do-blob> &lt;chave-de-conta-de-armazenamento>**

Esse comando permite carregar um disco de vm

    ~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
    info:   Executing command vm disk upload
    info:   Uploading 12351.5 KB
    info:   vm disk upload command OK

**vm disk attach &lt;nome-da-vm> &lt;nome-da-imagem-de-disco>**

Esse comando anexa um disco existente no armazenamento de blob a uma máquina virtual existente implantada em um Serviço de Nuvem.

    ~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
    info:   Executing command vm disk attach
    info:   vm disk attach command OK

**vm disk attach-new &lt;nome-da-vm> &lt;tamanho-em-gb> [url-do-blob]**

Esse comando exporta um disco de dados para uma máquina virtual do Azure. Neste exemplo, 20 é o tamanho do novo disco, em gigabytes, a ser anexado. Opcionalmente, você pode usar uma URL de blob como o último argumento para especificar explicitamente o blob de destino a ser criado. Se você não especificar uma URL de blob, um objeto blob será gerado automaticamente.

    ~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
    info:   Executing command vm disk attach-new
    info:   vm disk attach-new command OK  

**vm disk detach &lt;nome-da-vm> &lt;lun>**

Esse comando desanexa um disco de dados conectado a uma máquina virtual do Azure. &lt;lun> identifica o disco a ser desanexado. Para obter uma lista dos discos associados a um disco antes de desanexá-lo, utilize o comando vm disk-list &lt;nome-da-vm>.

    ~$ azure vm disk detach my-vm 2
    info:   Executing command vm disk detach
    info:   vm disk detach command OK

## <a name="commands-to-manage-your-azure-cloud-services"></a>Comandos para gerenciar seus serviços de nuvem do Azure

Os serviços de nuvem do Azure são aplicativos e serviços hospedados em funções web e de trabalho. Os comandos a seguir podem ser usados para gerenciar os serviços de nuvem do Azure.

**service create [opções] &lt;serviceName>**

Esse comando cria um serviço de nuvem

    ~$ azure service create newservicemsopentech
    info:    Executing command service create
    + Getting locations
    help:    Location:
      1) East Asia
      2) Southeast Asia
      3) North Europe
      4) West Europe
      5) East US
      6) West US
      : 6
    + Creating cloud service
    data:    Cloud service name newservicemsopentech
    info:    service create command OK

**service show [opções] &lt;serviceName>**

Esse comando mostra os detalhes de um Serviço de Nuvem do Azure.

    ~$ azure service show newservicemsopentech
    info:    Executing command service show
    + Getting cloud service
    data:    Name newservicemsopentech
    data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
    data:    Properties location West US
    data:    Properties label newservicemsopentech
    data:    Properties status Created
    data:    Properties dateCreated
    data:    Properties dateLastModified
    info:    service show command OK

**service list [opções]**

Esse comando lista os serviços de nuvem do Azure.

    ~$ azure service list
    info:   Executing command service list
    data:   Name         Status
    data:   -----------  -------
    data:   service1     Created
    data:   service2     Created
    info:   service list command OK

**service delete [opções] &lt;nome>**

Esse comando exclui um Serviço de Nuvem do Azure.

    ~$ azure service delete myservice
    info:   Executing command service delete myservice
    info:   cloud-service delete command OK

Para forçar a exclusão, use o parâmetro `-q`.


## <a name="commands-to-manage-your-azure-certificates"></a>Comandos para gerenciar seus certificados do Azure

Os certificados de serviço do Azure são certificados SSL conectados à sua conta do Azure. Para obter mais informações sobre certificados do Azure, consulte [Gerenciando Certificados](http://msdn.microsoft.com/library/azure/gg981929.aspx).

**service cert list [opções]**

Esse comando lista os certificados do Azure.

    ~$ azure service cert list
    info:   Executing command service cert list
    + Fetching cloud services
    + Fetching certificates
    data:   Service   Thumbprint                                Algorithm
    data:   --------  ----------------------------------------  ---------
    data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
    info:   service cert list command OK

**service cert create &lt;prefixo_dns> &lt;arquivo> [senha]**

Este comando carrega um certificado. Deixe o prompt de senha em branco para certificados que não são protegidos por senha.

    ~$ azure service cert create nghinazz ~/publishSet.pfx
    info:   Executing command service cert create
    Cert password:
    + Creating certificate
    info:   service cert create command OK

**service cert delete [opções] &lt;impressão digital>**

Esse comando exclui um certificado.

    ~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
    info:   Executing command service cert delete
    + Deleting certificate
    info:   nghinazz : cert deleted
    info:   service cert delete command OK

## <a name="commands-to-manage-your-web-apps"></a>Comandos para gerenciar seus aplicativos da Web

Um aplicativo Web do Azure é uma configuração da Web acessível pelo URI. Os aplicativos Web são hospedados em máquinas virtuais, mas você não precisa se preocupar com os detalhes de criação e implantação da máquina virtual. Esses detalhes são tratados para você pelo Azure.

**site list [opções]**

Esse comando lista seus aplicativos Web.

    ~$ azure site list
    info:   Executing command site list
    data:   Name            State    Host names
    data:   --------------  -------  --------------------------------------------------
    data:   mongosite       Running  mongosite.antdf0.antares.windows.net
    data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
    data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
    info:   site list command OK

**site set [opções] [nome]**

Esse comando define as opções de configuração para o aplicativo Web [nome]

    ~$ azure site set
    info:    Executing command site set
    Web site name: mydemosite
    + Getting sites
    + Updating site config information
    info:    site set command OK

**site deploymentscript [opções]**

Esse comando gera um script de implantação personalizado

    ~$ azure site deploymentscript --node
    info:    Executing command site deploymentscript
    info:    Generating deployment script for node.js Web Site
    info:    Generated deployment script files
    info:    site deploymentscript command OK

**site create [opções] [nome]**

Esse comando cria um aplicativo Web e o diretório local.

    ~$ azure site create mysite
    info:   Executing command site create
    info:   Using location northeuropewebspace
    info:   Creating a new web site
    info:   Created web site at  mysite.antdf0.antares.windows.net
    info:   Initializing repository
    info:   Repository initialized
    info:   site create command OK

> [AZURE.NOTE] O nome do site deve ser exclusivo. Você não pode criar um site com o mesmo nome DNS de um site existente.

**site browse [opções] [nome]**

Esse comando abre o seu aplicativo Web em um navegador.

    ~$ azure site browse mysite
    info:   Executing command site browse
    info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
    info:   site browse command OK

**site show [opções] [nome]**

Esse comando mostra os detalhes de um aplicativo Web.

    ~$ azure site show mysite
    info:   Executing command site show
    info:   Showing details for site
    data:   Site AdminEnabled true
    data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
    data:   Site Name mysite
    data:   Site Owner 00060000814EDDEE
    data:   Site RepositorySiteName mysite
    data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
    data:   Site State Running
    data:   Site UsageState Normal
    data:   Site WebSpace northeuropewebspace
    data:   Config AppSettings
    data:   Config ConnectionStrings
    data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
    data:   Config DetailedErrorLoggingEnabled false
    data:   Config HttpLoggingEnabled false
    data:   Config Metadata
    data:   Config NetFrameworkVersion v4.0
    data:   Config NumberOfWorkers 1
    data:   Config PhpVersion 5.3
    data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
    data:   Config RequestTracingEnabled false
    data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
    info:   site show command OK

**site delete [opções] [nome]**

Esse comando exclui um aplicativo Web.

    ~$ azure site delete mysite
    info:   Executing command site delete
    info:   Deleting site mysite
    info:   Site mysite has been deleted
    info:   site delete command OK

 **site swap [opções] [nome]**

Este comando troca dois slots do aplicativo Web.

Esse comando oferece suporte à seguinte opção adicional:

**-q ou **--quiet**: não solicitar confirmação. Use esta opção em scripts automatizados.


**site start [opções] [nome]**

Esse comando inicia um aplicativo Web.

    ~$ azure site start mysite
    info:   Executing command site start
    info:   Starting site mysite
    info:   Site mysite has been started
    info:   site start command OK

**site stop [opções] [nome]**

Esse comando para um aplicativo Web.

    ~$ azure site stop mysite
    info:   Executing command site stop
    info:   Stopping site mysite
    info:   Site mysite has been stopped
    info:   site stop command OK

**site restart [opções] [nome]**

Este comando para e, em seguida, inicia um aplicativo Web específico.

Esse comando oferece suporte à seguinte opção adicional:

**--slot** &lt;slot>: o nome do slot a reiniciar.


**site location list [opções]**

Esse comando lista os locais de seus aplicativos Web.

    ~$ azure site location list
    info:    Executing command site location list
    + Getting locations
    data:    Name
    data:    ----------------
    data:    West Europe
    data:    West US
    data:    North Central US
    data:    North Europe
    data:    East Asia
    data:    East US
    info:    site location list command OK

###<a name="commands-to-manage-your-web-app-application-settings"></a>Comandos para gerenciar as configurações do aplicativo Web

**site appsetting list[opções] [nome]**

Esse comando lista a configuração do aplicativo adicionada ao aplicativo Web.

    ~$ azure site appsetting list
    info:    Executing command site appsetting list
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Name  Value
    data:    ----  -----
    data:    test  value
    info:    site appsetting list command OK

**site appsetting add [opções] &lt;par-chave-valor> [nome]**

Esse comando adiciona uma configuração de aplicativo a seu aplicativo Web como um par de chave e valor.

    ~$ azure site appsetting add test=value
    info:    Executing command site appsetting add
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    + Updating site config information
    info:    site appsetting add command OK

**site appsetting delete [opções] &lt;chave> [nome]**

Esse comando exclui a configuração do aplicativo especificado do aplicativo Web.

    ~$ azure site appsetting delete test
    info:    Executing command site appsetting delete
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    Delete application setting test? [y/n] y
    + Updating site config information
    info:    site appsetting delete command OK

**site appsetting show [opções] &lt;chave> [nome]**

Esse comando exibe os detalhes da configuração do aplicativo especificado

    ~$ azure site appsetting show test
    info:    Executing command site appsetting show
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Value:  value
    info:    site appsetting show command OK

###<a name="commands-to-manage-your-web-app-certificates"></a>Comandos para gerenciar os certificados de aplicativo Web

**site cert list [opções] [nome]**

Esse comando exibe uma lista dos certificados do aplicativo Web.

    ~$ azure site cert list
    info:    Executing command site cert list
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Subject                       Expiration Date                    Thumbprint
    data:    ----------------------------  -----------------------------------------
    ----------------  ----------------------------------------
    data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
    data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
    info:    site cert list command OK

**site cert add [opções] &lt;caminho-do-certificado> [nome]**

**site cert delete [opções] &lt;impressão digital> [nome]**

**site cert show [opções] &lt;impressão digital> [nome]**

Esse comando mostra os detalhes do certificado

    ~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    Executing command site cert show
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Certificate hostNames 0=msopentech.azurewebsites.net
    data:    Certificate expirationDate
    data:    Certificate friendlyName msopentech.azurewebsites.net
    data:    Certificate issueDate
    data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
    data:    Certificate subjectName msopentech.azurewebsites.net
    data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    site cert show command OK

###<a name="commands-to-manage-your-web-app-connection-strings"></a>Comandos para gerenciar as cadeias de conexão do aplicativo Web

**site connectionstring list [opções] [nome]**

**site connectionstring add [opções] &lt;nome-da-conexão> &lt;valor> &lt;tipo> [nome]**

**site connectionstring delete [opções] &lt;nome-da-conexão> [nome]**

**site connectionstring show [opções] &lt;nome-da-conexão> [nome]**

###<a name="commands-to-manage-your-web-app-default-documents"></a>Comandos para gerenciar os documentos padrão do aplicativo Web

**site defaultdocument list [opções] [nome]**

**site defaultdocument add [opções] &lt;documento> [nome]**

**site defaultdocument delete [opções] &lt;documento> [nome]**

###<a name="commands-to-manage-your-web-app-deployments"></a>Comandos para gerenciar as implantações de seu aplicativo Web

**site deployment list [opções] [nome]**

**site deployment show [opções] &lt;commitId> [nome]**

**site deployment redeploy [opções] &lt;commitId> [nome]**

**site deployment github [opções] [nome]**

**site deployment user set [opções] [nome-de-usuário] [senha]**

###<a name="commands-to-manage-your-web-app-domains"></a>Comandos para gerenciar os domínios de seu aplicativo Web

**site domain list [opções] [nome]**

**site domain add [opções] &lt;dn> [nome]**

**site domain delete [opções] &lt;dn> [nome]**

###<a name="commands-to-manage-your-web-app-handler-mappings"></a>Comandos para gerenciar os mapeamentos de manipulador do aplicativo Web

**site handler list [opções] [nome]**

**site handler add [opções] &lt;extensão> &lt;processador> [nome]**

**site handler delete [opções] &lt;extensão> [nome]**

###<a name="commands-to-manage-your-web-jobs"></a>Comandos para gerenciar seus trabalhos Web

**site job list [opções] [nome]**

Este comando lista todos os trabalhos web em um aplicativo Web.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--job-type** &lt;tipo-de-trabalho>: opcional. O tipo de trabalho web. O valor válido é “acionado” ou “contínuo”. Por padrão, retorna trabalhos Web de todos os tipos.
+ **--slot** &lt;slot>: o nome do slot a reiniciar.

**site job show [opções] &lt;nome-do-trabalho> &lt;tipo-do-trabalho> [nome]**

Esse comando mostra os detalhes de um trabalho web específico.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--job-name** &lt;nome-do-trabalho>: necessário. O nome do trabalho web.
+ **--job-type** &lt;tipo-do-trabalho>: necessário. O tipo de trabalho web. O valor válido é “acionado” ou “contínuo”.
+ **--slot** &lt;slot>: o nome do slot a reiniciar.

**site job delete [opções] &lt;jobName> &lt;jobType> [nome]**

Esse comando exclui o trabalho web específico.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--job-name** &lt;nome-do-trabalho>    necessário. O nome do trabalho web.
+ **--job-type** &lt;tipo-do-trabalho>    necessário. O tipo de trabalho web. O valor válido é “acionado” ou “contínuo”.
+ **-q** ou **--quiet**: não solicitar confirmação. Use esta opção em scripts automatizados.
+ **--slot** &lt;slot>: o nome do slot a reiniciar.

**site job upload [opções] &lt;nome-do-trabalho> &lt;tipo-do-trabalho> <jobFile> [nome]**

Esse comando exclui o trabalho web específico.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--job-name** &lt;nome-do-trabalho>: necessário. O nome do trabalho web.
+ **--job-type** &lt;tipo-do-trabalho>: necessário. O tipo de trabalho web. O valor válido é “acionado” ou “contínuo”.
+ **--job-file** &lt;arquivo-do-trabalho>: necessário. O arquivo do trabalho.
+ **--slot** &lt;slot>: o nome do slot a reiniciar.

**site job start [opções] &lt;jnome-do-trabalho> &lt;tipo-do-trabalho> [nome]**

Esse comando inicia o trabalho web específico.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--job-name** &lt;nome-do-trabalho>: necessário. O nome do trabalho web.
+ **--job-type** &lt;tipo-do-trabalho>: necessário. O tipo de trabalho web. O valor válido é “acionado” ou “contínuo”.
+ **--slot** &lt;slot>: o nome do slot a reiniciar.

**site job stop [opções] &lt;jobName> &lt;jobType> [nome]**

Esse comando para o trabalho web especificado. Apenas trabalhos contínuos podem ser parados.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--job-name** &lt;nome-do-trabalho>: necessário. O nome do trabalho web.
+ **--slot** &lt;slot>: o nome do slot a reiniciar.

###<a name="commands-to-manage-your-web-jobs-history"></a>Comandos para gerenciar o Histórico dos Trabalhos Web

**site job history list [opções] [nome do trabalho] [nome]**

Esse comando exibe um histórico das execuções do trabalho web específico.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--job-name** &lt;nome-do-trabalho>: necessário. O nome do trabalho web.
+ **--slot** &lt;slot>: o nome do slot a reiniciar.

**site job history show [opções] [nome do trabalho] [ID de execução] [nome]**

Esse comando mostra os detalhes de uma execução do trabalho para o trabalho web específico.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--job-name** &lt;nome-do-trabalho>: necessário. O nome do trabalho web.
+ **--run-id** &lt;ID-de-execução>: opcional. O ID do histórico de execução. Se não especificado, mostrar a última execução.
+ **--slot** &lt;slot>: o nome do slot a reiniciar.

###<a name="commands-to-manage-your-web-app-diagnostics"></a>Comandos para gerenciar o diagnóstico de seu aplicativo Web

**site log download [opções] [nome]**

Baixar um arquivo .zip que contém os diagnósticos do aplicativo Web.

    ~$ azure site log download
    info:    Executing command site log download
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    + Downloading diagnostic log to diagnostics.zip
    info:    site log download command OK

**site log tail [opções] [nome]**

Esse comando conecta seu terminal ao serviço de log de streaming.

    ~$ azure site log tail
    info:    Executing command site log tail
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [opções] [nome]**

Esse comando configura as opções de diagnóstico para seu aplicativo Web.

    ~$ azure site log set -a
    info:    Executing command site log set
    + Getting output options
    help:    Output:
      1) file
      2) storage
      : 1
    Web site name: mydemosite
    + Getting locations
    + Getting sites
    + Getting site information
    + Getting diagnostic settings
    + Updating diagnostic settings
    info:    site log set command OK

###<a name="commands-to-manage-your-web-app-repositories"></a>Comandos para gerenciar os repositórios de seu aplicativo Web

**site repository branch [opções] &lt;ramificação> [nome]**

**site repository delete [opções] [nome]**

**site repository sync [opções] [nome]**

###<a name="commands-to-manage-your-web-app-scaling"></a>Comandos para gerenciar o dimensionamento de seu aplicativo Web

**site scale mode [opções] &lt;modo> [nome]**

**site scale instances [opções] &lt;instâncias> [nome]**


## <a name="commands-to-manage-azure-mobile-services"></a>Comandos para gerenciar os Serviços Móveis do Azure

Os Serviços Móveis do Azure reúnem um conjunto de serviços do Azure que habilitam recursos de back-end para seus aplicativos. Os comandos de Serviços Móveis estão divididos nas seguintes categorias:

+ [Comandos para gerenciar instância dos Serviços Móveis](#Mobile_Services)
+ [Comandos para gerenciar a configuração do serviço móvel](#Mobile_Configuration)
+ [Comandos para gerenciar tabelas do serviço móvel](#Mobile_Tables)
+ [Comandos para gerenciar scripts do serviço móvel](#Mobile_Scripts)
+ [Comandos para gerenciar trabalhos agendados](#Mobile_Jobs)
+ [Comandos para dimensionar um serviço móvel](#Mobile_Scale)

As opções a seguir se aplicam à maioria dos comandos de Serviços Móveis:

+ **-h** ou **--help**: exibir informações sobre o uso da saída.
+ **-s `<id>`** ou **--subscription `<id>`**: usar uma assinatura específica, especificada como `<id>`.
+ **-v** ou **--verbose**: gravar saída detalhada.
+ **--json**: gravar uma saída JSON.

### <a name="<a-name="mobile_services"></a>commands-to-manage-mobile-service-instances"></a><a name="Mobile_Services"></a>Comandos para gerenciar instância dos Serviços Móveis

**mobile locations [opções]**

Esse comando lista os locais geográficos suportados pelos Serviços Móveis.

    ~$ azure mobile locations
    info:    Executing command mobile locations
    info:    East US (default)
    info:    West US
    info:    North Europe

**mobile create [opções] [nome do serviço] [nome do usuário administrador do sql] [senha do administrador do sql]**

Esse comando cria um serviço móvel juntamente com um Banco de Dados e servidor SQL.

    ~$ azure mobile create todolist your_login_name Secure$Password
    info:    Executing command mobile create
    + Creating mobile service
    info:    Overall application state: Healthy
    info:    Mobile service (todolist) state: ProvisionConfigured
    info:    SQL database (todolist_db) state: Provisioned
    info:    SQL server (e96ean1c6v) state: ProvisionConfigured
    info:    mobile create command OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-r `<sqlServer>`**  ou **--sqlServer `<sqlServer>`**: utilizar um servidor de banco de dados SQL existente, especificado como `<sqlServer>`.
+ **-d `<sqlDb>`** ou **--sqlDb `<sqlDb>`**: usar banco de dados SQL existente, especificado como `<sqlDb>`.
+ **-l `<location>`** ou **--location `<location>`**: criar o serviço em um local específico, especificado como `<location>`. Executar azure mobile locations para obter os locais disponíveis.
+ **--sqlLocation `<location>`**: criar o SQL Server em um `<location>` específico; o padrão é o local do serviço móvel.

**mobile delete [opções] [nome do serviço]**

Esse comando exclui um serviço móvel juntamente com seu Banco de Dados e servidor SQL.

    ~$ azure mobile delete todolist -a -q
    info:    Executing command mobile delete
    data:    Mobile service todolist
    data:    SQL database todolistAwrhcL60azo1C401
    data:    SQL server fh1kvbc7la
    + Deleting mobile service
    info:    Deleted mobile service
    + Deleting SQL server
    info:    Deleted SQL server
    + Deleting mobile application
    info:    Deleted mobile application
    info:    mobile delete command OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-d** ou **--deleteData**: excluir todos os dados desse serviço móvel do banco de dados.
+ **-a** ou **--deleteAll**: excluir o banco de dados SQL e o servidor.
+ **-q** ou **--quiet**: não solicitar confirmação. Use esta opção em scripts automatizados.

**mobile list [opções]**

Esse comando lista os serviços móveis.

    ~$ azure mobile list
    info:    Executing command mobile list
    data:    Name          State  URL
    data:    ------------  -----  --------------------------------------
    data:    todolist      Ready  https://todolist.azure-mobile.net/
    data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
    info:    mobile list command OK

**mobile show [opções] [nome-do-serviço]**

Esse comando exibe detalhes sobre um serviço móvel.

    ~$ azure mobile show todolist
    info:    Executing command mobile show
    + Getting information
    info:    Mobile application
    data:    status Healthy
    data:    Mobile service name todolist
    data:    Mobile service status ProvisionConfigured
    data:    SQL database name todolistAwrhcL60azo1C401
    data:    SQL database status Linked
    data:    SQL server name fh1kvbc7la
    data:    SQL server status Linked
    info:    Mobile service
    data:    name todolist
    data:    state Ready
    data:    applicationUrl https://todolist.azure-mobile.net/
    data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    webspace WESTUSWEBSPACE
    data:    region West US
    data:    tables TodoItem
    info:    mobile show command OK

**mobile restart [opções] [nome-do-serviço]**

Esse comando reinicia uma instância do serviço móvel.

    ~$ azure mobile restart todolist
    info:    Executing command mobile restart
    + Restarting mobile service
    info:    Service was restarted.
    info:    mobile restart command OK

**mobile log [opções] [nome-do-serviço]**

Esse comando retorna logs do serviço móvel, filtrando todos os tipos de log, menos `error`.

    ~$ azure mobile log todolist -t error
    info:    Executing command mobile log
    data:
    data:    timeCreated 2013-01-07T16:04:43.351Z
    data:    type error
    data:    source /scheduler/TestingLogs.js
    data:    message This is an error.
    data:
    info:    mobile log command OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-r `<query>`** ou **--query `<query>`**: executa a consulta de log especificada.
+ **-t `<type>`** ou **--type `<type>`**: filtrar os logs retornados pela entrada `<type>`, que pode ser `information`, `warning` ou `error`.
+ **-k `<skip>`** ou **--skip `<skip>`**: ignora o número de linhas especificadas por `<skip>`.
+ **-p `<top>`** ou **--top `<top>`**: retorna um número de linhas especificado por `<top>`.

> [AZURE.NOTE] O parâmetro **--query** tem precedência sobre **--type**, **--skip** e **--top**.

**mobile recover [opções] [nome do serviço não íntegro] [nome do serviço íntegro]**

Este comando recupera um serviço móvel não saudável ao movê-lo a um serviço móvel saudável em uma região diferente.

Esse comando oferece suporte à seguinte opção adicional:

**-q** ou **--quiet**: suprimir o prompt para confirmação de recuperação.

**mobile key regenerate [opções] [nome-do-serviço] [tipo]**

Esse comando regenera a chave do aplicativo do serviço móvel.

    ~$ azure mobile key regenerate todolist application
    info:    Executing command mobile key regenerate
    info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
    info:    mobile key regenerate command OK

Os tipos de chave são`master` e `application`.

> [AZURE.NOTE] Quando você regenera chaves, os clientes que usam a chave antiga talvez não possam acessar o serviço móvel. Quando regenera a chave do aplicativo, você deve atualizar seu aplicativo com o novo valor da chave.

**mobile key set [opções] [nome-do-serviço] [tipo] [valor]**

Este comando define a chave do serviço móvel para um valor específico.


### <a name="<a-name="mobile_configuration"></a>commands-to-manage-mobile-service-configuration"></a><a name="Mobile_Configuration"></a>Comandos para gerenciar a configuração do serviço móvel

**mobile config list [opções] [nome-do-serviço]**

Esse comando lista as opções de configuração de um serviço móvel.

    ~$ azure mobile config list todolist
    info:    Executing command mobile config list
    + Getting mobile service configuration
    data:    dynamicSchemaEnabled true
    data:    microsoftAccountClientSecret Not configured
    data:    microsoftAccountClientId Not configured
    data:    microsoftAccountPackageSID Not configured
    data:    facebookClientId Not configured
    data:    facebookClientSecret Not configured
    data:    twitterClientId Not configured
    data:    twitterClientSecret Not configured
    data:    googleClientId Not configured
    data:    googleClientSecret Not configured
    data:    apnsMode none
    data:    apnsPassword Not configured
    data:    apnsCertifcate Not configured
    info:    mobile config list command OK

**mobile config get [opções] [nome-do-serviço] [chave]**

Esse comando obtém uma opção de configuração específica de um serviço móvel, esquema dinâmico nesse caso.

    ~$ azure mobile config get todolist dynamicSchemaEnabled
    info:    Executing command mobile config get
    data:    dynamicSchemaEnabled true
    info:    mobile config get command OK

**mobile config set [opções] [nome-do-serviço] [chave] [valor]**

Esse comando define uma opção de configuração específica para um serviço móvel, esquema dinâmico nesse caso.

    ~$ azure mobile config set todolist dynamicSchemaEnabled false
    info:    Executing command mobile config set
    info:    mobile config set command OK


### <a name="<a-name="mobile_tables"></a>commands-to-manage-mobile-service-tables"></a><a name="Mobile_Tables"></a>Comandos para gerenciar tabelas do serviço móvel

**mobile table list [opções] [nome-do-serviço]**

Esse comando lista todas as tabelas em seu serviço móvel.

    ~$azure mobile table list todolist
    info:    Executing command mobile table list
    data:    Name      Indexes  Rows
    data:    --------  -------  ----
    data:    Channel   1        0
    data:    TodoItem  1        0
    info:    mobile table list command OK

**mobile table show [opções] [nome-do-serviço] [nome-da-tabela]**

Esse comando retorna detalhes sobre uma tabela específica.

    ~$azure mobile table show todolist
    info:    Executing command mobile table show
    + Getting table information
    info:    Table statistics:
    data:    Number of records 5
    info:    Table operations:
    data:    Operation  Script       Permissions
    data:    ---------  -----------  -----------
    data:    insert     1900 bytes   user
    data:    read       Not defined  user
    data:    update     Not defined  user
    data:    delete     Not defined  user
    info:    Table columns:
    data:    Name  Type           Indexed
    data:    ----  -------------  -------
    data:    id    bigint(MSSQL)  Yes
    data:    text      string
    data:    complete  boolean
    info:    mobile table show command OK

**mobile table create [opções] [nome-do-serviço] [nome-da-tabela]**

Esse comando cria uma tabela.

    ~$azure mobile table create todolist Channels
    info:    Executing command mobile table create
    + Creating table
    info:    mobile table create command OK

Esse comando oferece suporte à seguinte opção adicional:

+ **-p `&lt;permissions>`** ou **--permissions `&lt;permissions>`**: lista delimitada por vírgulas de pares de `<operation>`=`<permission>`, em que `<operation>` é `insert`, `read`, `update` ou `delete` e `&lt;permissions>` é `public`, `application` (padrão), `user` ou `admin`.

**mobile data read [opções] [nome-do-serviço] [nome-da-tabela] [consulta]**

Esse comando lê dados de uma tabela.

    ~$azure mobile data read todolist TodoItem
    info:    Executing command mobile data read
    data:    id  text     complete
    data:    --  -------  --------
    data:    1   item #1  false
    data:    2   item #2  true
    data:    3   item #3  false
    data:    4   item #4  true
    info:    mobile data read command OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-k `<skip>`** ou **--skip `<skip>`**: ignora o número de linhas especificadas por `<skip>`.
+ **-t `<top>`** ou **--top `<top>`**: retorna um número específico de linhas, especificado por `<top>`.
+ **-l** ou **--list**: retorna dados em um formato de lista.

**mobile table update [opções] [nome-do-serviço] [nome-da-tabela]**

Esse comando altera as permissões de exclusão em uma tabela somente para administradores.

    ~$azure mobile table update todolist Channels -p delete=admin
    info:    Executing command mobile table update
    + Updating permissions
    info:    Updated permissions
    info:    mobile table update command OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-p `&lt;permissions>`** ou **--permissions `&lt;permissions>`**: lista delimitada por vírgulas de pares de `<operation>`=`<permission>`, em que `<operation>` é `insert`, `read`, `update` ou `delete` e `&lt;permissions>` é `public`, `application` (padrão), `user` ou `admin`.
+ **--deleteColumn `<columns>`**: lista de colunas delimitada por vírgulas a serem excluídas, como `<columns>`.
+ **-q** ou **--quiet**: exclui as colunas sem solicitar confirmação.
+ **--addIndex `<columns>`**: lista de colunas delimitada por vírgulas a serem incluídas no índice.
+ **--deleteIndex `<columns>`**: lista de colunas delimitada por vírgulas a serem excluídas do índice.

**mobile table delete [opções] [nome do serviço] [nome da tabela]**

Esse comando exclui uma tabela.

    ~$azure mobile table delete todolist Channels
    info:    Executing command mobile table delete
    Do you really want to delete the table (yes/no): yes
    + Deleting table
    info:    mobile table delete command OK

Especifique o parâmetro -q para excluir a tabela sem confirmação. Faça isso para evitar o bloqueio de scripts de automação.

**mobile data truncate [opções] [nome-do-serviço] [nome-da-tabela]**

Esse comando remove todas as linhas de dados da tabela.

    ~$azure mobile data truncate todolist TodoItem
    info:    Executing command mobile data truncate
    info:    There are 7 data rows in the table.
    Do you really want to delete all data from the table? (y/n): y
    info:    Deleted 7 rows.
    info:    mobile data truncate command OK


### <a name="<a-name="mobile_scripts"></a>commands-to-manage-scripts"></a><a name="Mobile_Scripts"></a>Comandos para gerenciar scripts

Os comandos desta seção são utilizados para gerenciar os scripts de servidor que pertencem a um serviço móvel. Para obter mais informações, consulte [Trabalhar com scripts de servidor em Serviços Móveis](mobile-services/mobile-services-how-to-use-server-scripts.md).

**mobile script list [opções] [nome-do-serviço]**

Esse comando lista scripts registrados, incluindo scripts de tabela e script do agendador.

    ~$azure mobile script list todolist
    info:    Executing command mobile script list
    + Getting script information
    info:    Table scripts
    data:    Name                   Size
    data:    ---------------------  ----
    data:    table/TodoItem.delete  256
    data:    table/Devices.insert   1660
    error:   Unable to get shared scripts
    info:    Scheduler scripts
    data:    Name                 Status     Interval   Last run   Next run
    data:    -------------------  ---------  ---------  ---------  ---------
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    info:    mobile script list command OK

**mobile script download [opções] [nome-do-serviço] [nome-do-script]**

Esse comando baixa o script de inserção da tabela TodoItem em um arquivo chamado `todoitem.insert.js` na subpasta `table`.

    ~$azure mobile script download todolist table/todoitem.insert.js
    info:    Executing command mobile script download
    info:    Saved script to ./table/todoitem.insert.js
    info:    mobile script download command OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-p `<path>`** ou **--path `<path>`**: o local no arquivo no qual salvar o script, em que o diretório de trabalho atual é o padrão.
+ **-f `<file>`** ou **--file `<file>`**: o nome do arquivo no qual salvar o script.
+ **-o** ou **--override**: substituir um arquivo existente.
+ **-c** ou **--console**: gravar o script no console, em vez de em um arquivo.

**mobile script upload [opções] [nome-do-serviço] [nome-do-script]**

Esse comando carrega um novo script chamado `todoitem.insert.js` do subdiretório `table`.

    ~$azure mobile script upload todolist table/todoitem.insert.js
    info:    Executing command mobile script upload
    info:    mobile script upload command OK

O nome do arquivo deve ser composto dos nomes da tabela e da operação. Ele deve estar localizado na subpasta tabela relativa ao local onde o comando é executado. Você também pode usar o parâmetro **-f `<file>`** ou **--file `<file>`** para especificar um nome de arquivo e caminho diferente que contém o script a ser registrado.


**mobile script delete [opções] [nome do serviço] [nome do script]**

Esse comando remove o script de inserção existente da tabela TodoItem.

    ~$azure mobile script delete todolist table/todoitem.insert.js
    info:    Executing command mobile script delete
    info:    mobile script delete command OK

### <a name="<a-name="mobile_jobs"></a>commands-to-manage-scheduled-jobs"></a><a name="Mobile_Jobs"></a>Comandos para gerenciar trabalhos agendados

Os comandos desta seção são utilizados para gerenciar trabalhos agendados que pertencem a um serviço móvel. Para obter mais informações, veja [Agendar trabalhos](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx).

**mobile job list [opções] [nome-do-serviço]**

Esse comando lista trabalhos agendados.

    ~$azure mobile job list todolist
    info:    Executing command mobile job list
    info:    Scheduled jobs
    data:    Job name    Script name           Status    Interval     Last run              Next run
    data:    ----------  --------------------  --------  -----------  --------------------  --------------------
    data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
    info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
    info:    mobile job list command OK

**mobile job create [opções] [nome-do-serviço] [nome-do-trabalho]**

Esse comando cria um trabalho chamado `getUpdates` que está agendado para ser executado a cada hora.

    ~$azure mobile job create -i 1 -u hour todolist getUpdates
    info:    Executing command mobile job create
    info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
    info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
    info:    mobile job create command OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-i `<number>`** ou **--interval `<number>`**: o intervalo do trabalho como um número inteiro. O valor padrão é `15`.
+ **-u `<unit>`** ou **--intervalUnit `<unit>`**: a unidade de _interval_, que pode ser um dos seguintes valores:
    + **minuto** (padrão)
    + **hora**
    + **dia**
    + **mês**
    + **nenhum** (trabalhos sob demanda)
+ **-t `<time>`** **--startTime `<time>`** A hora de início da primeira execução do script, no formato ISO. O valor padrão é `now`.

> [AZURE.NOTE] Novos trabalhos são criados no estado desabilitado porque um script ainda deve ser carregado. Use o comando **mobile script upload** para carregar um script e o comando **mobile job update** para habilitar o trabalho.

**mobile job update [opções] [nome-do-serviço] [nome-do-trabalho]**

O comando a seguir habilita o trabalho desabilitado `getUpdates`.

    ~$azure mobile job update -a enabled todolist getUpdates
    info:    Executing command mobile job update
    info:    mobile job update command OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-i `<number>`** ou **--interval `<number>`**: o intervalo do trabalho como um número inteiro. O valor padrão é `15`.
+ **-u `<unit>`** ou **--intervalUnit `<unit>`**: a unidade de _interval_, que pode ser um dos seguintes valores:
    + **minuto** (padrão)
    + **hora**
    + **dia**
    + **mês**
    + **nenhum** (trabalhos sob demanda)
+ **-t `<time>`** **--startTime `<time>`** A hora de início da primeira execução do script, no formato ISO. O valor padrão é `now`.
+ **-a `<status>`** ou **--status `<status>`**: o status do trabalho, que pode ser `enabled` ou `disabled`.

**mobile job delete [opções] [nome do serviço] [nome do trabalho]**

Esse comando remove o trabalho agendado getUpdates do servidor TodoList.

    ~$azure mobile job delete todolist getUpdates
    info:    Executing command mobile job delete
    info:    mobile job delete command OK

> [AZURE.NOTE] A exclusão de um trabalho também exclui o script carregado.

### <a name="<a-name="mobile_scale"></a>commands-to-scale-a-mobile-service"></a><a name="Mobile_Scale"></a>Comandos para dimensionar um serviço móvel

Os comandos desta seção são usados para dimensionar um serviço móvel. Para obter mais informações, consulte [Dimensionando um serviço móvel](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx).

**mobile scale show [opções] [nome-do-serviço]**

Esse comando exibe informações de escala, incluindo o modo de computação atual e o número de instâncias.

    ~$azure mobile scale show todolist
    info:    Executing command mobile scale show
    data:    webspace WESTUSWEBSPACE
    data:    computeMode Free
    data:    numberOfInstances 1
    info:    mobile scale show command OK

**mobile scale change [opções] [nome-do-serviço]**

Esse comando altera a escala do serviço móvel de gratuito para modo premium.

    ~$azure mobile scale change -c Reserved -i 1 todolist
    info:    Executing command mobile scale change
    + Rescaling the mobile service
    info:    mobile scale change command OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-c `<mode>`** ou **--computeMode `<mode>`**: o modo de computação deve ser `Free` ou `Reserved`.
+ **-i `<count>`** ou **--numberOfInstances `<count>`**: o número de instâncias usadas durante a execução em modo reservado.

> [AZURE.NOTE] Ao definir o modo de computação como `Reserved`, todos os serviços móveis da mesma região são executados em modo premium.


###<a name="commands-to-enable-preview-features-for-your-mobile-service"></a>Comandos para habilitar recursos de visualização para seu Serviço Móvel

**mobile preview list [opções] [nome-do-serviço]**

Este comando exibe os recursos de visualização disponíveis no serviço específico se eles foram habilitados.

    ~$ azure mobile preview list mysite
    info:    Executing command mobile preview list
    + Getting preview features
    data:    Preview feature  Enabled
    data:    ---------------  -------
    data:    SourceControl    No
    data:    Users            No
    info:    You can enable preview features using the 'azure mobile preview enable' command.
    info:    mobile preview list command OK

**mobile preview enable [opções] [nome-do-serviço] [nome-do-recurso]**

Esse comando habilita o recurso de visualização específico para um serviço móvel. Uma vez habilitados, os recursos de visualização não podem ser desabilitados para um serviço móvel.

###<a name="commands-to-manage-your-mobile-service-apis"></a>Comandos para gerenciar seus APIs de serviço móvel

**mobile api list [opções] [nome-do-serviço]**

Este comando exibe uma lista de APIs personalizada do serviço móvel que você criou para seu serviço móvel.

    ~$ azure mobile api list mysite
    info:    Executing command mobile api list
    + Retrieving list of APIs
    info:    APIs
    data:    Name                  Get          Put          Post         Patch        Delete
    data:    --------------------  -----------  -----------  -----------  -----------  -----------
    data:    myCustomRetrieveAPI   application  application  application  application  application
    info:    You can manipulate API scripts using the 'azure mobile script' command.
    info:    mobile api list command OK

**mobile api create [opções] [nome-do-serviço] [nome-da-api]**

Criar uma API personalizada de serviço móvel

    ~$ azure mobile api create mysite myCustomRetrieveAPI
    info:    Executing command mobile api create
    + Creating custom API: 'myCustomRetrieveAPI'
    info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
    info:    mobile api create command OK

Esse comando oferece suporte à seguinte opção adicional:

**-p** ou **--permissions** &lt;permissões>: uma lista de pares &lt;método>=&lt;permissão> delimitada por vírgulas.

**mobile api update [opções] [nome-do-serviço] [nome-da-api]**

Este comando atualiza a API personalizada do serviço móvel específico.

Esse comando oferece suporte à seguinte opção adicional:

Esse comando oferece suporte às seguintes opções adicionais:

+ **-p** ou **--permissions** &lt;permissões>: uma lista de pares &lt;método>=&lt;permissão> delimitada por vírgulas.
+ **-f** ou **--force**: substitui quaisquer alterações personalizadas para o arquivo de metadados de permissões.

**mobile api delete [opções] [nome do serviço] [nome da api]**

    ~$ azure mobile api delete mysite myCustomRetrieveAPI
    info:    Executing command mobile api delete
    + Deleting API: 'myCustomRetrieveAPI'
    info:    mobile api delete command OK

Este comando exclui a API personalizada do serviço móvel específico.

###<a name="commands-to-manage-your-mobile-application-app-settings"></a>Comandos para gerenciar suas configurações do aplicativo móvel

**mobile appsetting list [opções] [nome-do-serviço]**

Este comando evibe as configurações do aplicativo móvel para o serviço específico.

    ~$ azure mobile appsetting list mysite
    info:    Executing command mobile appsetting list
    + Retrieving app settings
    data:    Name               Value
    data:    -----------------  -----
    data:    enablebetacontent  true
    info:    mobile appsetting list command OK

**mobile appsetting add [opções] [nome-do-serviço] [nome] [valor]**

Este comando adiciona uma configuração do apicativo personalizado para seu serviço móvel.

    ~$ azure mobile appsetting add mysite enablebetacontent true
    info:    Executing command mobile appsetting add
    + Retrieving app settings
    + Adding app setting
    info:    mobile appsetting add command OK

**mobile appsetting delete [opções] [nome do serviço] [nome]**

Este comando remove a configuração do aplicativo específico para seu serviço móvel.

    ~$ azure mobile appsetting delete mysite enablebetacontent
    info:    Executing command mobile appsetting delete
    + Retrieving app settings
    + Removing app setting 'enablebetacontent'
    info:    mobile appsetting delete command OK

**mobile appsetting show [opções] [nome-do-serviço] [nome]**

Este comando remove a configuração do aplicativo específico para seu serviço móvel.

    ~$ azure mobile appsetting show mysite enablebetacontent
    info:    Executing command mobile appsetting show
    + Retrieving app settings
    info:    enablebetacontent: true
    info:    mobile appsetting show command OK

## <a name="manage-tool-local-settings"></a>Gerenciar as configurações locais da ferramenta

As configurações locais são a ID de sua assinatura e o Nome da Conta de Armazenamento Padrão.

**config list [opções]**

Esse comando exibe as definições da configuração.

    ~$ azure config list
    info:   Displaying config settings
    data:   Setting                Value
    data:   ---------------------  ------------------------------------
    data:   subscription           32-digit-subscription-key
    data:   defaultStorageAccount  name

**config set [opções] &lt;nome&gt;,&lt;valor&gt;**

Esse comando altera uma definição da configuração.

    ~$ azure config set defaultStorageAccount myname
    info:   Setting 'defaultStorageAccount' to value 'myname'
    info:   Changes saved.

## <a name="commands-to-manage-service-bus"></a>Comandos para gerenciar o Service Bus

Utilize estes comandos para gerenciar sua conta do Service Bus

**sb namespace check [opções] &lt;nome>**

Verifique se o namespace do barramento de serviço é legal e está disponível.

**sb namespace create &lt;name> &lt;location>**

Cria um namespace do Barramento de Serviço.

    ~$ azure sb namespace create mysbnamespacea-test "West US"
    info:    Executing command sb namespace create
    + Creating namespace mysbnamespacea-test in region West US
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Activating
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    _: [object Object]
    info:    sb namespace create command OK


**sb namespace delete &lt;nome>**

Remover um namespace.

    ~$ azure sb namespace delete mysbnamespacea-test
    info:    Executing command sb namespace delete
    Delete namespace mysbnamespacea-test? [y/n] y
    + Deleting namespace mysbnamespacea-test
    info:    sb namespace delete command OK

**sb namespace list**

Listar todos os namespaces criados para sua conta.

    ~$ azure sb namespace list
    info:    Executing command sb namespace list
    + Getting namespaces
    data:    Name                 Region   Status
    data:    -------------------  -------  ------
    data:    mysbnamespacea-test  West US  Active
    info:    sb namespace list command OK


**sb namespace location list**

Exibir uma lista de todos os namespaces locais disponíveis.

    ~$ azure sb namespace location list
    info:    Executing command sb namespace location list
    + Getting locations
    data:    Name              Code
    data:    ----------------  ----------------
    data:    East Asia         East Asia
    data:    West Europe       West Europe
    data:    North Europe      North Europe
    data:    East US           East US
    data:    Southeast Asia    Southeast Asia
    data:    North Central US  North Central US
    data:    West US           West US
    data:    South Central US  South Central US
    info:    sb namespace location list command OK

**sb namespace show &lt;nome>**

Exibir detalhes sobre um namespace específico.

    ~$ azure sb namespace show mysbnamespacea-test
    info:    Executing command sb namespace show
    + Getting namespace
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Active
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    UpdatedAt: 2013-11-14T16:25:37.85Z
    info:    sb namespace show command OK

**sb namespace verify &lt;nome>**

Verificar se o namespace está disponível.

## <a name="commands-to-manage-your-storage-objects"></a>Comandos para gerenciar seus objetos de armazenamento

###<a name="commands-to-manage-your-storage-accounts"></a>Comandos para gerenciar suas contas de Armazenamento

**storage account list [opções]**

Este comando exibe as contas de armazenamento em sua assinatura.

    ~$ azure storage account list
    info:    Executing command storage account list
    + Getting storage accounts
    data:    Name             Label  Location
    data:    ---------------  -----  --------
    data:    mybasestorage           West US
    info:    storage account list command OK

**storage account show [opções] <name>**

Este comando exibe informações sobre a conta de armazenamento específica, incluindo o URI e propriedades da conta.

**storage account create [opções] <name>**

Este comando cria uma conta de armazenamento com base nas opções fornecidas.

    ~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-e** ou **--label** &lt;rótulo>: o rótulo para a conta de armazenamento.
+ **-d** ou **--description** &lt;descrição>: A descrição da conta de armazenamento.
+ **-l** ou **--location** &lt;name>: a região geográfica na qual criar a conta de armazenamento.
+ **-a** ou **--affinity-group** &lt;nome>: o grupo de afinidades ao qual associar a conta de armazenamento. 
+ **--type** indica o tipo de conta a criar: Armazenamento Standard com a opção de redundância (LRS/ZRS/GRS/RAGRS) ou Armazenamento Premium (PLRS).

**storage account set [opções] <name>**

Este comando atualiza a conta de armazenamento específica.

    ~$ azure storage account set mybasestorage --kind Storage --sku-name GRS
    info:    Executing command storage account set
    + Updating storage account
    info:    storage account set command OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-e** ou **--label** &lt;rótulo>: o rótulo para a conta de armazenamento.
+ **-d** ou **--description** &lt;descrição>: A descrição da conta de armazenamento.
+ **-l** ou **--location** &lt;name>: a região geográfica na qual criar a conta de armazenamento.
+ **--type** indica o novo tipo de conta: Armazenamento Standard com a opção de redundância (LRS/ZRS/GRS/RAGRS) ou Armazenamento Premium (PLRS).

**storage account delete [opções] <name>**

Este comando exclui a conta de armazenamento específica.

Esse comando oferece suporte à seguinte opção adicional:

**-q** ou **--quiet**: não solicitar confirmação. Use esta opção em scripts automatizados.

###<a name="commands-to-manage-your-storage-account-keys"></a>Comandos para gerenciar suas chaves de contas de Armazenamento

**storage account keys list [opções] <name>**

Este comando lista as chaves primárias e secundárias para a conta de armazenamento específica.

**storage account keys renew [opções] <name>**

###<a name="commands-to-manage-your-storage-container"></a>Comandos para gerenciar seu contêiner de armazenamento

**storage container list [opções] [prefixo]**

Este comando exibe a lista de contêiner de armazenamento para uma conta de armazenamento específico. A conta de armazenamento é específica pela cadeia de conexão ou o nome da conta de armazenamento e a chave da conta.

Esse comando oferece suporte às seguintes opções adicionais:

+ **-p** ou **-prefix** &lt;prefixo>: o prefixo de nome do contêiner de armazenamento.
+ **-a** ou **--account-name** &lt;nome-da-conta>: o nome da conta de armazenamento.
+ **-k** ou **--account-key** &lt;chave-de-conta>: a chave de conta de armazenamento.
+ **-c** ou **--connection-string** &lt;cadeia-de-conexão>: a cadeia de conexão do armazenamento.
+ **--debug**: executa o comando de armazenamento no modo de depuração.

**storage container show [opções] [contêiner]**
**storage container create [opções] [contêiner]**

Esse comando cria um contêiner de armazenamento para a conta de armazenamento específica. A conta de armazenamento é específica pela cadeia de conexão ou o nome da conta de armazenamento e a chave da conta.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--container** &lt;contêiner>: o nome do contêiner de armazenamento a ser criado.
+ **-p** ou **-prefix** &lt;prefixo>: o prefixo de nome do contêiner de armazenamento.
+ **-a** ou **--account-name** &lt;nome-da-conta>: o nome da conta de armazenamento
+ **-k** ou **--account-key** &lt;chave-de-conta>: a chave de conta de armazenamento
+ **-c** ou **--connection-string** &lt;cadeia-de-conexão>: a cadeia de conexão do armazenamento
+ **--debug**: executa o comando de armazenamento no modo de depuração.

**storage container delete [opções] [contêiner]**

Esse comando exclui o contêiner de armazenamento específico. A conta de armazenamento é específica pela cadeia de conexão ou o nome da conta de armazenamento e a chave da conta.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--container** &lt;contêiner>: o nome do contêiner de armazenamento a ser criado.
+ **-p** ou **-prefix** &lt;prefixo>: o prefixo de nome do contêiner de armazenamento.
+ **-a** ou **--account-name** &lt;nome-da-conta>: o nome da conta de armazenamento.
+ **-k** ou **--account-key** &lt;chave-de-conta>: a chave de conta de armazenamento.
+ **-c** ou **--connection-string** &lt;cadeia-de-conexão>: a cadeia de conexão do armazenamento.
+ **--debug**: executa o comando de armazenamento no modo de depuração.

**storage container set [opções] [contêiner]**

Esse comando define a lista de controle de acesso para o contêiner de armazenamento. A conta de armazenamento é específica pela cadeia de conexão ou o nome da conta de armazenamento e a chave da conta.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--container** &lt;contêiner>: o nome do contêiner de armazenamento a ser criado.
+ **-p** ou **-prefix** &lt;prefixo>: o prefixo de nome do contêiner de armazenamento.
+ **-a** ou **--account-name** &lt;nome-da-conta>: o nome da conta de armazenamento.
+ **-k** ou **--account-key** &lt;chave-de-conta>: a chave de conta de armazenamento.
+ **-c** ou **--connection-string** &lt;cadeia-de-conexão>: a cadeia de conexão do armazenamento.
+ **--debug**: executa o comando de armazenamento no modo de depuração.

###<a name="commands-to-manage-your-storage-blob"></a>Comandos para gerenciar seu blob de armazenamento

**storage blob list [opções] [contêiner] [prefixo]**

Este comando retorna uma lista de blobs de armazenamento no contêiner de armazenamento específico.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--container** &lt;contêiner>: o nome do contêiner de armazenamento a ser criado.
+ **-p** ou **-prefix** &lt;prefixo>: o prefixo de nome do contêiner de armazenamento.
+ **-a** ou **--account-name** &lt;nome-da-conta>: o nome da conta de armazenamento.
+ **-k** ou **--account-key** &lt;chave-de-conta>: a chave de conta de armazenamento.
+ **-c** ou **--connection-string** &lt;cadeia-de-conexão>: a cadeia de conexão do armazenamento.
+ **--debug**: executa o comando de armazenamento no modo de depuração.

**storage blob show [opções] [contêiner] [blob]**

Esse comando exibe os detalhes do blob de armazenamento especificado.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--container** &lt;contêiner>: o nome do contêiner de armazenamento a ser criado.
+ **-p** ou **-prefix** &lt;prefixo>: o prefixo de nome do contêiner de armazenamento.
+ **-a** ou **--account-name** &lt;nome-da-conta>: o nome da conta de armazenamento.
+ **-k** ou **--account-key** &lt;chave-de-conta>: a chave de conta de armazenamento.
+ **-c** ou **--connection-string** &lt;cadeia-de-conexão>: a cadeia de conexão do armazenamento.
+ **--debug**: executa o comando de armazenamento no modo de depuração.

**storage blob delete [opções] [contêiner] [blob]**

Esse comando oferece suporte às seguintes opções adicionais:

+ **--container** &lt;contêiner>: o nome do contêiner de armazenamento a ser criado.
+ **-b** ou **--blob** &lt;nome-do-blob>: o nome do blob de armazenamento a ser excluído.
+ **-q** ou **--quiet**: remova o blob de armazenamento específico sem confirmação.
+ **-a** ou **--account-name** &lt;nome-da-conta>: o nome da conta de armazenamento.
+ **-k** ou **--account-key** &lt;chave-de-conta>: a chave de conta de armazenamento.
+ **-c** ou **--connection-string** &lt;cadeia-de-conexão>: a cadeia de conexão do armazenamento.
+ **--debug**: executa o comando de armazenamento no modo de depuração.

**storage blob upload [opções] [arquivo] [contêiner] [blob]**

Este comando carrega o arquivo específico para o blob de armazenamento espeficicado.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--container** &lt;contêiner>: o nome do contêiner de armazenamento a ser criado.
+ **-b** ou **--blob** &lt;nome-do-blob>: o nome do blob de armazenamento a ser carregado.
+ **-t** ou **--blobtype** &lt;tipo-de-blob>: o tipo de blob de armazenamento: Página ou Bloco.
+ **-p** ou **--properties** &lt;propriedades>: as propriedades do blob de armazenamento para o arquivo carregado. As propriedades são pares chave=valor e separados com ponto e vírgula (;). As propriedades disponíveis são contentType, contentEncoding, contentLanguage e cacheControl.
+ **-m** ou **--metadata** &lt;metadados>: os metadados do blob de armazenamento para o arquivo carregado. Os metadados são pares chave=valor e separados com ponto e vírgula (;).
+ **--concurrenttaskcount** &lt;contagem-de-tarefas-simultâneas> O número máximo de solicitações de upload simultâneas.
+ **-q** ou **--quiet**: substituir o blob de armazenamento específico sem confirmação.
+ **-a** ou **--account-name** &lt;nome-da-conta>: o nome da conta de armazenamento.
+ **-k** ou **--account-key** &lt;chave-de-conta>: a chave de conta de armazenamento.
+ **-c** ou **--connection-string** &lt;cadeia-de-conexão>: a cadeia de conexão do armazenamento.
+ **--debug**: executa o comando de armazenamento no modo de depuração.

**storage blob download [opções] [contêiner] [blob] [destino]**

Este comando baixa o blob de armazenamento específico.

Esse comando oferece suporte às seguintes opções adicionais:

+ **--container** &lt;contêiner>: o nome do contêiner de armazenamento a ser criado.
+ **-b** ou **--blob** &lt;nome-do-blob>: o nome do blob de armazenamento.
+ **-d** ou **--destination** [destino]: o caminho do arquivo ou diretório de destino do download.
+ **-m** ou **--checkmd5**: a marca md5sum para o arquivo baixado.
+ **--concurrenttaskcount** &lt;contagem-de-tarefas-simultâneas> O número máximo de solicitações de upload simultâneas
+ **-q** ou **--quiet**: substitui o arquivo de destino sem confirmação.
+ **-a** ou **--account-name** &lt;nome-da-conta>: o nome da conta de armazenamento.
+ **-k** ou **--account-key** &lt;chave-de-conta>: a chave de conta de armazenamento.
+ **-c** ou **--connection-string** &lt;cadeia-de-conexão>: a cadeia de conexão do armazenamento.
+ **--debug**: executa o comando de armazenamento no modo de depuração.

## <a name="commands-to-manage-sql-databases"></a>Comandos para gerenciar bancos de dados SQL

Use estes comandos para gerenciar seus bancos de dados SQL do Azure

###<a name="commands-to-manage-sql-servers."></a>Comandos para gerenciar servidores SQL.

Use estes comandos para gerenciar seus servidores SQL

**sql server create &lt;login-do-administrador> &lt;senha-do-administrador> &lt;localização>**

Criar um servidor de banco de dados

    ~$ azure sql server create test T3stte$t "West US"
    info:    Executing command sql server create
    + Creating SQL Server
    data:    Server Name i1qwc540ts
    info:    sql server create command OK

**sql server show &lt;nome>**

Exibir detalhes do servidor.

    ~$ azure sql server show xclfgcndfg
    info:    Executing command sql server show
    + Getting SQL server
    data:    SQL Server Name xclfgcndfg
    data:    SQL Server AdministratorLogin msopentechforums
    data:    SQL Server Location West US
    data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
    info:    sql server show command OK

**lista do sql server**

Obter a lista de servidores.

    ~$ azure sql server list
    info:    Executing command sql server list
    + Getting SQL server
    data:    Name        Location
    data:    ----------  --------
    data:    xclfgcndfg  West US
    info:    sql server list command OK

**sql server delete &lt;nome>**

Exclui um servidor

    ~$ azure sql server delete i1qwc540ts
    info:    Executing command sql server delete
    Delete server i1qwc540ts? [y/n] y
    + Removing SQL Server
    info:    sql server delete command OK

###<a name="commands-to-manage-sql-databases"></a>Comandos para gerenciar bancos de dados SQL

Use estes comandos para gerenciar seus bancos de dados SQL.

**sql db create [opções] &lt;nome-do-servidor> &lt;nome-do-banco-de-dados> &lt;senha-do-administrador>**

Cria uma instância do banco de dados

    ~$ azure sql db create fr8aelne00 newdb test
    info:    Executing command sql db create
    Administrator password: ********
    + Creating SQL Server Database
    info:    sql db create command OK

**sql db show [opções] &lt;nome-do-servidor> &lt;nome-do-banco-de-dados> &lt;senha-do-administrador>**

Exibir detalhes do banco de dados.

    C:\windows\system32>azure sql db show fr8aelne00 newdb test
    info:    Executing command sql db show
    Administrator password: ********
    + Getting SQL server databases
    data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
    ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
    m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
    icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
    ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
    ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
    /Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
    ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
    Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
    services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
    tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
    om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
    pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
    d=2013-11-18T19:48:27Z, name=
    data:    Database Id 4
    data:    Database Name newdb
    data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database ServiceObjectiveAssignmentState 1
    data:    Database ServiceObjectiveAssignmentStateDescription Complete
    data:    Database ServiceObjectiveAssignmentErrorCode
    data:    Database ServiceObjectiveAssignmentErrorDescription
    data:    Database ServiceObjectiveAssignmentSuccessDate
    data:    Database Edition Web
    data:    Database MaxSizeGB 1
    data:    Database MaxSizeBytes 1073741824
    data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
    data:    Database CreationDate
    data:    Database RecoveryPeriodStartDate
    data:    Database IsSystemObject
    data:    Database Status 1
    data:    Database IsFederationRoot
    data:    Database SizeMB -1
    data:    Database IsRecursiveTriggersOn
    data:    Database IsReadOnly
    data:    Database IsFederationMember
    data:    Database IsQueryStoreOn
    data:    Database IsQueryStoreReadOnly
    data:    Database QueryStoreMaxSizeMB
    data:    Database QueryStoreFlushPeriodSeconds
    data:    Database QueryStoreIntervalLengthMinutes
    data:    Database QueryStoreClearAll
    data:    Database QueryStoreStaleQueryThresholdDays
    info:    sql db show command OK

**sql db list [opções] &lt;nome-do-servidor> &lt;senha-do-administrador>**

Listar os bancos de dados.

    ~$ azure sql db list fr8aelne00 test
    info:    Executing command sql db list
    Administrator password: ********
    + Getting SQL server databases
    data:    Name    Edition  Collation                     MaxSizeInGB
    data:    ------  -------  ----------------------------  -----------
    data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
    info:    sql db list command OK

**sql db delete [opções] &lt;nome-do-servidor> &lt;nome-do-banco-de-dados> &lt;senha-do-administrador>**

Exclui um banco de dados.

    ~$ azure sql db delete fr8aelne00 newdb test
    info:    Executing command sql db delete
    Administrator password: ********
    Delete database newdb? [y/n] y
    + Getting SQL server databases
    + Removing database
    info:    sql db delete command OK

###<a name="commands-to-manage-your-sql-server-firewall-rules"></a>Comandos para gerenciar suas regras de firewall do SQL Server

Use estes comandos para gerenciar suas regras de firewall do SQL Server

**sql firewallrule create [opções] &lt;nome-do-servidor> &lt;nome-da-regra> &lt;endereço-IP-inicial> &lt;endereço-IP-final>**

Criar uma regra de firewall para um SQL Server.

    ~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
    info:    Executing command sql firewallrule create
    + Creating Firewall Rule
    info:    sql firewallrule create command OK

**sql firewallrule show [opções] &lt;nome-do-servidor> &lt;nome-da-regra>**

Mostrar detalhes da regra de firewall.

    ~$ azure sql firewallrule show fr8aelne00 allowed
    info:    Executing command sql firewallrule show
    + Getting firewall rule
    data:    Firewall rule Name allowed
    data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
    data:    Firewall rule State Normal
    data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
    5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
    data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
    055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
    data:    Firewall rule StartIPAddress 131.107.0.0
    data:    Firewall rule EndIPAddress 131.107.255.255
    info:    sql firewallrule show command OK

**sql firewallrule list [opções] &lt;nome-do-servidor>**

Listar as regras de firewall.

    ~$ azure sql firewallrule list fr8aelne00
    info:    Executing command sql firewallrule list
    \data:    Name     Start IP address  End IP address
    data:    -------  ----------------  ---------------
    data:    allowed  131.107.0.0       131.107.255.255
    +
    info:    sql firewallrule list command OK

**sql firewallrule delete [opções] &lt;nome-do-servidor> &lt;nome-da-regra>**

Esse comando exclui uma regra de firewall.

    ~$ azure sql firewallrule delete fr8aelne00 allowed
    info:    Executing command sql firewallrule delete
    Delete rule allowed? [y/n] y
    + Removing firewall rule
    info:    sql firewallrule delete command OK

## <a name="commands-to-manage-your-virtual-networks"></a>Comandos para gerenciar suas Redes Virtuais

Use estes comandos para gerenciar suas Redes Virtuais

**network vnet create [opções] &lt;localização>**

Criar uma rede virtual.

    ~$ azure network vnet create vnet1 --location "West US" -v
    info:    Executing command network vnet create
    info:    Using default address space start IP: 10.0.0.0
    info:    Using default address space cidr: 8
    info:    Using default subnet start IP: 10.0.0.0
    info:    Using default subnet cidr: 11
    verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
    verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
    verbose: Fetching Network Configuration
    verbose: Fetching or creating affinity group
    verbose: Fetching Affinity Groups
    verbose: Fetching Locations
    verbose: Creating new affinity group AG1
    info:    Using affinity group AG1
    verbose: Updating Network Configuration
    info:    network vnet create command OK

**network vnet show &lt;nome>**

Mostrar detalhes de uma Rede Virtual.

    ~$ azure network vnet show vnet1
    info:    Executing command network vnet show
    + Fetching Virtual Networks
    data:    Name "vnet1"
    data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
    data:    AffinityGroup "AG1"
    data:    State "Created"
    data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
    data:    Subnets 0 Name "subnet-1"
    data:    Subnets 0 AddressPrefix "10.0.0.0/11"
    info:    network vnet show command OK

**network vnet list**

Listar todas as Redes Virtuais existentes.

    ~$ azure network vnet list
    info:    Executing command network vnet list
    + Fetching Virtual Networks
    data:    Name        Status   AffinityGroup
    data:    ----------  -------  -------------
    data:    vnet1      Created  AG1
    data:    vnet2      Created  AG1
    data:    vnet3      Created  AG1
    data:    vnet4      Created  AG1
    info:    network vnet list command OK


**network vnet delete &lt;nome>**

Exclui a Rede Virtual especificada.

    ~$ azure network vnet delete opentechvn1
    info:    Executing command network vnet delete
    + Fetching Network Configuration
    Delete the virtual network opentechvn1 ?  (y/n) y
    + Deleting the virtual network opentechvn1
    info:    network vnet delete command OK

**network export [caminho-do-arquivo]**

Para configuração de rede avançada, você pode exportar sua configuração de rede localmente. A configuração de rede exportada inclui as configurações do servidor DNS, as configurações de rede virtual, as configurações de site de rede local e outras configurações.

**network import [caminho-do-arquivo]**

Importar uma configuração de rede local.

**network dnsserver register [opções] &lt;IP-do-dns>**

Registrar um servidor DNS que você pretende usar para a resolução de nomes em sua configuração de rede.

    ~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
    info:    Executing command network dnsserver register
    + Fetching Network Configuration
    + Updating Network Configuration
    info:    network dnsserver register command OK

**network dnsserver list**

Listar todos os servidores DNS registrados em sua configuração de rede.

    ~$ azure network dnsserver list
    info:    Executing command network dnsserver list
    + Fetching Network Configuration
    data:    DNS Server ID         DNS Server IP
    data:    --------------------  --------------
    data:    DNS-bb39b4ac34d66a86  44.55.22.11
    data:    FrontEndDnsServer     98.138.253.109
    info:    network dnsserver list command OK

**network dnsserver unregister [opções] &lt;IP-do-dns>**

Remove uma entrada de servidor DNS da configuração de rede.

    ~$ azure network dnsserver unregister 77.88.99.11
    info:    Executing command network dnsserver unregister
    + Fetching Network Configuration
    Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
    + Deleting the DNS server entry dns-4 ( 77.88.99.11 )
    info:    network dnsserver unregister command OK




<!--HONumber=Oct16_HO2-->


