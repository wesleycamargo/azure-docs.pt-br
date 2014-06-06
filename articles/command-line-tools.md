<properties linkid="manage-linux-other-resources-command-line-tools" urlDisplayName="Ferramentas de linha de comando" pageTitle="Ferramentas de linha de comando do Azure para Mac e Linux" metaKeywords="linha de comando do Azure, ferramentas do Azure para Mac, ferramentas do Azure para Linux" description="Saiba como usar a ferramenta de linha de comando para Mac e Linux no Azure." metaCanonical="" services="web-sites,virtual-machines,mobile-services,cloud-services" documentationCenter="" title="" authors=""  solutions="" writer="larryfr" manager="" editor=""  />

#Ferramenta de linha de comando do Azure para Mac e Linux

Esta ferramenta fornece funcionalidade para criar, implantar e gerenciar máquinas virtuais, sites e serviços móveis do Azure em áreas de trabalho Mac e Linux. Essa funcionalidade é semelhante àquela fornecida pelos cmdlets do Windows PowerShell que são instalados com o SDK do Azure para .NET, Node.JS e PHP.

Para instalar a ferramenta em um Mac, baixe e execute o [Instalador do SDK do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=252249).

Para instalar a ferramenta no Linux, instale a versão mais recente do Node.JS e use NPM para instalar:

    npm install azure-cli -g

Parâmetros opcionais são mostrados entre colchetes (por exemplo, [parâmetro]). Todos os outros parâmetros são obrigatórios.

Além dos parâmetros opcionais específicos aos comandos documentados aqui, há três parâmetros opcionais que podem ser usados para exibir saída detalhada, como opções de solicitação e códigos de status. O parâmetro -v fornece saída detalhada e o parâmetro -vv fornece saída mais detalhada ainda. A opção --json produzirá o resultado no formato json bruto.

**Sumário:**

* [Gerenciar suas informações de conta e configurações de publicação](#Manage_your_account_information_and_publish_settings)
* [Comandos para gerenciar suas máquinas virtuais do Azure](#Commands_to_manage_your_Azure_virtual_machines)
* [Comandos para gerenciar seus pontos de extremidade de máquinas virtuais do Azure](#Commands_to_manage_your_Azure_virtual_machine_endpoints)
* [Comandos para gerenciar suas imagens de máquinas virtuais do Azure](#Commands_to_manage_your_Azure_virtual_machine_images)
* [Comandos para gerenciar seus discos de dados de máquinas virtuais do Azure](#Commands_to_manage_your_Azure_virtual_machine_data_disks)
* [Comandos para gerenciar seus serviços de nuvem do Azure](#Commands_to_manage_your_Azure_cloud_services)
* [Comandos para gerenciar seus certificados do Azure](#Commands_to_manage_your_Azure_certificates)
* [Comandos para gerenciar seus sites](#Commands_to_manage_your_web_sites)
* [Comandos para gerenciar os Serviços Móveis do Azure](#Commands_to_manage_mobile_services)
* [Gerenciar as configurações locais da ferramenta](#Manage_tool_local_settings)
* [Comandos para gerenciar o Service Bus](#Commands_to_manage_service_bus)
* [Comandos para gerenciar bancos de dados SQL](#Commands_to_manage_sql)
* [Comandos para gerenciar suas Redes Virtuais](#Commands_to_manage_vnet)

##<a name="Manage_your_account_information_and_publish_settings"></a>Gerenciar suas informações de conta e configurações de publicação
As informações de sua assinatura do Azure são usadas pela ferramenta para conectar-se à sua conta. Essas informações podem ser obtidas no portal do Azure em um arquivo de configurações de publicação conforme descrito aqui. O arquivo de configurações de publicação pode ser importado como uma definição de configuração local persistente que a ferramenta irá usar para operações subsequentes. Você precisa importar as configurações de publicação apenas uma vez.

**account download [opções]**

Esse comando inicia um navegador para baixar o arquivo .publishsettings do portal do Azure.

	~$ azure account download
	info:   Executando o comando account download
	info:   Iniciando o navegador para https://windows.azure.com/download/publishprofile.aspx
	help:   Salve o arquivo baixado e execute o comando
	help:   importação de conta <file>
	info:   comando account download OK

**account import [opções] &lt;arquivo>**

Esse comando importa um arquivo publishsettings ou certificado para que possa ser usado pela ferramenta no futuro.

	~$ azure account import publishsettings.publishsettings
	info:   Importando arquivo de configurações de publicação publishsettings.publishsettings
	info:   Assinatura encontrada: Versão de avaliação gratuita por três meses
	info:   Assinatura encontrada: Pago pelo uso
	info:   Definindo assinatura padrão como: Versão de Avaliação Gratuita por três meses
	warn:   O arquivo 'publishsettings.publishsettings' contém informações confidenciais.
	warn: Lembre-se de excluí-lo agora que foi importado.
	info:   Configurações de publicação da conta importadas com sucesso

<div class="dev-callout"><b>Observação</b>
   <p>O arquivo publishsettings pode conter detalhes (ou seja, o nome e a ID da assinatura) sobre mais de uma assinatura. Quando você importa o arquivo publishsettings, a primeira assinatura é usada como descrição padrão. Para usar outra assinatura, execute o seguinte comando.</p>
<code>~$ azure config set subscription &lt;other-subscription-id&gt;</code>
</div>

**account clear [opções]**

Esse comando remove as configurações de publicação armazenadas que foram importadas. Use esse comando se você terminar de usar a ferramenta neste computador e desejar garantir que a ferramenta não possa ser usada com sua conta no futuro.

	~$ azure account clear
	Limpando informações da conta.
	info:   OK

**account list [opções]**

Listar as assinaturas importadas

	~$ azure account list
	info:    Executando o comando account list
	data:    Nome                                    Id
	       Atual
	data:    --------------------------------------  -------------------------------
	-----  -------
	data:    Assinatura de fóruns                    8679c8be-3b05-49d9-b8fb  true
	data:    Assinatura na equipe de evangelistas    9e672699-1055-41ae-9c36  false
	data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account set [opções] &lt;assinatura&gt;**

Definir a assinatura atual

###Comandos para gerenciar os grupos de afinidade

**account affinity-group list [opções]**

Esse comando lista os grupos de afinidade do Azure.

Os grupos de afinidade podem ser definidos quando um grupo de máquinas virtuais abrange várias máquinas físicas. O grupo de afinidade especifica que máquinas físicas devem estar o mais próximo possível umas das outras, para reduzir a latência de rede.
 
	~$ azure account affinity-group list
	+ Buscando grupos de afinidade
	data:   Nome                                  Rótulo   Local
	data:   ------------------------------------  ------  --------
	data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  Oeste dos Estados Unidos
	info:   comando account affinity-group list OK

**account affinity-group create [opções] &lt;nome&gt;**

Esse comando cria um novo grupo de afinidade

	~$ azure account affinity-group create opentec -l "West US"
	info:    Executando o comando account affinity-group create
	+ Criando grupo de afinidade
	info:   comando account affinity-group create OK

**account affinity-group show [opções] &lt;nome&gt;**

Esse comando mostra os detalhes do grupo de afinidade

	~$ azure account affinity-group show opentec
	info:    Executando o comando account affinity-group show
	+ Obtendo grupos de afinidade
	data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
	data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
	data:    Nome "opentec"
	data:    Rótulo "b3BlbnRlYw=="
	data:    Descrição $ i:nil "true"
	data:    Local "Oeste dos EUA"
	data:    HostedServices ""
	data:    StorageServices ""
	data:    Funcionalidade de capacidades 0 "PersistentVMRole"
	data:    Funcionalidade de capacidades 1 "HighMemory"
	info:   comando account affinity-group show OK

**account affinity-group delete [opções] &lt;nome&gt;**

Esse comando exclui o grupo de afinidade especificado

	~$ azure account affinity-group delete opentec
	info:    Executando o comando account affinity-group delete
	Excluir grupo de afinidade opentec? [s/n] s
	+ Excluindo grupo de afinidade
	info:   comando account affinity-group delete OK

###Comandos para gerenciar seu ambiente de conta

**account env list [opções]**

Lista dos ambientes de conta

	C:\windows\system32>azure account env list
	info:    Executando o comando account env list
	data:    Nome
	data:    ---------------
	data:    AzureCloud
	data:    AzureChinaCloud
	info:    comando account env list OK

**account env show [opções] [ambiente]**

Mostrar detalhes do ambiente da conta

	~$ azure account env show
	info:    Executando o comando account env show
	Nome do ambiente: AzureCloud
	data:    publishingProfile do ambiente http://go.microsoft.com/fwlink/?LinkId=2544
	data:    Portal do ambiente http://go.microsoft.com/fwlink/?LinkId=2544
	info:    comando account env show OK

**account env add [opções] [ambiente]**

Esse comando adiciona um ambiente à conta

**account env set [opções] [ambiente]**

Esse comando define o ambiente da conta

**account env delete [opções] [ambiente]**

Esse comando exclui o ambiente especificado da conta

##<a name="Commands_to_manage_your_Azure_virtual_machines"></a>Comandos para gerenciar as máquinas virtuais do Azure
O diagrama a seguir mostra como as máquinas virtuais do Azure são hospedadas no ambiente de implantação da produção de um Serviço de Nuvem do Azure.
 
![Diagrama técnico do Azure](./media/command-line-tools/architecturediagram.jpg)	

**create-new** cria a unidade no armazenamento de blob (isto é, e:\ no diagrama); **attach** anexa um disco já criado, mas desanexado, à máquina virtual.

**vm create [opções] &lt;nome_dns> &lt;imagem> &lt;nome_do_usuário> [senha]**

Esse comando cria uma nova máquina virtual do Azure. Por padrão, cada máquina virtual é criada em seu próprio Serviço de Nuvem. No entanto, você pode especificar que uma máquina virtual deve ser adicionada a um Serviço de Nuvem existente por meio do uso da opção -c, conforme documentado aqui.

Observe que o comando create vm, como o portal do Azure, só cria máquinas virtuais no ambiente de implantação de produção. No momento, não há nenhuma opção para criar uma máquina virtual no ambiente de implantação de preparo de um Serviço de Nuvem. Observe que uma conta de Armazenamento do Azure será criada por esse comando se ainda não existir uma conta para sua assinatura.

Você pode especificar um local por meio do parâmetro --location ou especificar um grupo de afinidade por meio do parâmetro --affinity-group. Se nenhum dos parâmetros for fornecido, você será solicitado a fornecer um de uma lista de locais válidos.

A senha fornecida deve ter de 8 a 123 caracteres e atender aos requisitos de complexidade de senha do sistema operacional que você está usando para esta máquina virtual.

Se você antecipar a necessidade de utilizar SSH para gerenciar uma máquina virtual Linux implantada (como normalmente é o caso), deverá ativar SSH por meio da opção -e ao criar a máquina virtual. Não é possível ativar SSH após a máquina virtual ter sido criada.

As máquinas virtuais do Windows podem habilitar RDP posteriormente adicionando a porta 3389 como um ponto de extremidade.

Os seguintes parâmetros opcionais são aceitos para esse comando:

**-c, --connect** criar a máquina virtual dentro de uma implantação já criada em um serviço de hospedagem. Se -vmname não for usado com essa opção, o nome da nova máquina virtual será gerado automaticamente.<br />
**-n --vm-name** especifica o nome da máquina virtual. Por padrão, esse parâmetro usa o nome do serviço de hospedagem. Se -vmname não for especificado, o nome da nova máquina virtual será gerado como &lt;nome_do_serviço >&lt;id>, em que &lt;id> é o número de máquinas virtuais existentes no serviço mais 1. Por exemplo, se você usar esse comando para adicionar uma nova máquina virtual a um serviço de hospedagem Meu_serviço que tem máquina virtual existente, a nova máquina virtual será chamada de Meu_serviço2.<br /> 
**-u, --blob-url** especifica a URL de armazenamento de blob na qual criar o disco de sistema da máquina virtual. <br />
**-z, --vm-size** especifica o tamanho da máquina virtual. Os valores válidos são "extrasmall", "small", "medium", "large", "extralarge". O valor padrão é "small". <br />
**-r** adiciona conectividade RDP a uma máquina virtual do Windows. <br />
**-e, --ssh** adiciona conectividade SSH a uma máquina virtual do Windows. <br />
**-t, --ssh-cert** especifica o certificado SSh. <br />
**-s** a assinatura <br />
**-o,--community** a imagem especificada é uma imagem da comunidade <br />
**-w** o nome da rede virtual <br/>
**-l, --location** especifica o local (por exemplo, "Centro Norte dos EUA"). <br />
**-a, --affinity-group** especifica o grupo de afinidade.<br />
**-w, --virtual-network-name** especifica a rede virtual na qual você deseja adicionar a nova máquina virtual. As redes virtuais podem ser configuradas e gerenciadas no portal do Azure.<br />
**-b, --subnet-names** especifica os nomes de sub-redes a serem atribuídas à máquina virtual.

Neste exemplo, MSFT__Win2K8R2SP1-120514-1520-141205-01-en-us-30GB é uma imagem fornecida pela plataforma. Para obter mais informações sobre imagens do sistema operacional, consulte vm image list.

	~$ azure vm create nome_da_minha_vm MSFT__Windows-Server-2008-R2-SP1.11-29-2011 nome_do_usuário --location "Western US" -r
	info:   Executando o comando vm create
	Digite a senha da VM 'nome_da_minha_vm' VM: ************                                     
	info:   comando vm create OK

**vm create-from &lt;nome_dns> &lt;arquivo_de_função>**

Esse comando cria uma nova máquina virtual do Azure em um arquivo de função JSON.

	~$ azure vm create-from my-vm example.json
	info:   OK

**vm list [opções]**

Esse comando lista as máquinas virtuais do Azure. A opção -json especifica que os resultados são retornados em formato JSON bruto. 

	~$ azure vm list
	info:   Executando o comando vm list
	data:   Nome DNS                          Nome da VM      Status                  
	data:   --------------------------------  -----------  ---------
	data:   my-vm-name.cloudapp-preview.net        minha-vm        ReadyRole
	info:   comando vm list OK

**vm location list [opções]**

Esse comando lista todos os locais disponíveis da conta do Azure.

	~$ azure vm location list
	info:   Executando o comando vm location list
	data:   Nome                   Nome de Exibição                                    
	data:   ---------------------  ------------
	data:   Visualização do Azure Oeste dos EUA     
	info:   comando account location list OK

**vm show [opções] &lt;nome>**

Esse comando mostra detalhes sobre uma máquina virtual do Azure. A opção -json especifica que os resultados são retornados em formato JSON bruto. 

	~$ azure vm show minha_vm
	info:   Executando o comando vm show
	data:   {                                                                      
	data:       InstanceSize: 'Small',
	data:       InstanceStatus: 'ReadyRole',
	data:       DataDisks: [],
	data:       IPAddress: '10.26.192.206',
	data:       DNSName: 'my_vm.cloudapp.net',
	data:       InstanceStateDetails: {},
	data:       VMName: 'minha_vm',
	data:       Rede: {
	data:           Pontos de extremidade: [
	data:               {
	data:                   Protocolo: 'tcp',
	data:                   Vip: '65.52.250.250',
	data:                   Porta: '63238' ,
	data:                   LocalPort: '3389',
	data:                   Nome: 'RemoteDesktop'
	data:               }
	data:           ]
	data:       },
	data:       Imagem: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
	data:   } 
	info:   comando vm show OK

**vm delete [opções] &lt;nome>**

Esse comando exclui uma máquina virtual do Azure. Por padrão, esse comando não exclui o blob do Azure no qual o disco do sistema operacional e o disco de dados são criados. Para excluir o blob e a máquina virtual na qual ele se baseia, especifique a opção -b.

	~$ azure vm delete minha_vm 
	info:   Executando o comando vm delete
	info:   comando vm delete OK

**vm start [opções] &lt;nome>**

Esse comando inicia uma máquina virtual do Azure.

	~$ azure vm start minha_vm
	info:   Executando o comando vm start
	info:   comando vm start OK

**vm restart [opções] &lt;nome>**

Esse comando reinicia uma máquina virtual do Azure.

	~$ azure vm restart minha_vm
	info:   Executando o comando vm restart
	info:   comando vm restart OK

**vm shutdown [opções] &lt;nome>**

Esse comando desliga uma máquina virtual do Azure. Você pode usar a opção -p para especificar que o recurso de computação não seja liberado durante o desligamento.

```
~$ azure vm shutdown minha_vm
info:   Executando o comando vm shutdown
info:   comando vm shutdown OK  
```

**vm capture &lt;nome_da_vm> &lt;nome_da_imagem_de_destino>**

Esse comando captura uma imagem da máquina virtual do Azure.

Uma imagem da máquina virtual não pode ser capturada enquanto a máquina virtual tiver um estado, a menos que o estado da máquina virtual seja Parada.

	~$ azure.cmd vm capture minha_vm nome_da_minha_imagem_de_captura --delete
	info:   Executando o comando vm capture
	+ Buscando VMs
	+ Capturando VM
	info:   comando vm capture OK

**vm export [opções] &lt;nome_da_vm> &lt;caminho_do_arquivo>**

Esse comando exporta uma imagem da máquina virtual do Azure para um arquivo.

	~$ azure vm export "minha_vm" "C:\"
	info:   Executando o comando vm export
	+ Obtendo máquinas virtuais
	+ Exportar a VM
	info:   comando vm export OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_endpoints"></a>Comandos para gerenciar seus pontos de extremidade de máquina virtual do Azure
O diagrama a seguir mostra a arquitetura de uma implantação típica de várias instâncias de uma máquina virtual. Observe que neste exemplo, a porta 3389 é aberta em cada máquina virtual (para acesso RDP) e que também há um endereço IP interno (por exemplo, 168.55.11.1) em cada máquina virtual que é usada pelo balanceador de carga para rotear o tráfego para a máquina virtual. Esse endereço IP interno também pode ser usado para comunicação entre máquinas virtuais.

![azurenetworkdiagram](./media/command-line-tools/networkdiagram.jpg)
 
As solicitações externas para máquinas virtuais passam por um balanceador de carga. Por esse motivo, as solicitações não podem ser especificadas em relação a uma máquina virtual específica em implantações com várias máquinas virtuais. Para implantações com várias máquinas virtuais, o mapeamento da porta deve ser configurado entre as máquinas virtuais (porta_da_vm) e o balanceador de carga (porta_do_bc).

**vm endpoint create &lt;nome_da_vm> &lt;porta_do_bc> [porta_da_vm]**

Esse comando cria um ponto de extremidade da máquina virtual. Você também pode usar -u ou --enable-direct-server-return para especificar se o retorno direto do servidor deve ser habilitado nesse ponto de extremidade, desabilitado por padrão.

	~$ azure vm endpoint create minha_vm 8888 8888
	azure vm endpoint create minha_vm 8888 8888
	info:   Executando o comando vm endpoint create
	+ Buscando VM
	+ Lendo a configuração de rede
	+ Atualizando a configuração de rede
	info:   comando vm endpoint create OK

**vm endpoint create-multiple [opções] &lt;nome_da_vm> &lt;porta_do_bc>[:&lt;porta_da_vm>[:&lt;protocolo>[:&lt;nome_definido_do_bc>[:&lt;protocolo_de_prob>:&lt;protocolo_de_prob_do_bc>[:&lt;caminho_do_prob>]]]]] ]{1-*}**

Cria vários pontos de extremidade de vm. Você também pode usar -u ou --enable-direct-server-return para especificar se o retorno direto do servidor deve ser habilitado nesse ponto de extremidade, desabilitado por padrão.

**vm endpoint delete &lt;nome_da_vm> &lt;porta_do_bc>**

Esse comando exclui um ponto de extremidade da máquina virtual.

	~$ azure vm endpoint delete minha_vm 8888
	azure vm endpoint delete minha_vm 8888
	info:   Executando o comando vm endpoint delete
	+ Buscando VM
	+ Lendo a configuração de rede
	+ Atualizando a configuração de rede
	info:   comando vm endpoint delete OK

**vm endpoint list &lt;nome_da_vm>**

Esse comando lista todos os pontos de extremidade da máquina virtual. A opção -json especifica que os resultados são retornados em formato JSON bruto. 

	~$ azure vm endpoint list minha_vm_linux
	data: Nome Porta Externa Porta Local                                        
	data: ---- ------------- ----------
	data:   ssh   22             22

**vm endpoint update [opções] &lt;nome_da_vm> &lt;nome_do_ponto_de_extremidade>**

Este comando atualiza um ponto de extremidade de vm para novos valores usando estas opções.

    -n, --endpoint-name <name>          o novo nome do ponto de extremidade
    -t, --lb-port <port>                a nova porta do balanceador de carga
    -t, --vm-port <port>                a nova porta local
    -o, --endpoint-protocol <protocol>  o novo protocolo de camada de transporte da porta (tcp ou udp) 

**vm endpoint show [opções] &lt;nome_da_vm>**

Esse comando mostra os detalhes dos pontos de extremidade em uma vm

	~$ azure vm endpoint show "mycouchvm"
	info:    Executando o comando vm endpoint show
	+ Obtendo máquinas virtuais
	data:    Pontos de Extremidade de Rede 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
	data:    Pontos de Extremidade de Rede 0 Porta_Local "5984"
	data:    Pontos de Extremidade de Rede 0 Nome "CouchDB_EP-5984"
	data:    Pontos de Extremidade de Rede 0 Porta "5984"
	data:    Pontos de Extremidade de Rede 0 Protocolo "tcp"
	data:    Pontos de Extremidade de Rede 0 Vip "168.61.9.97"
	data:    Pontos de Extremidade de Rede 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
	data:    Pontos de Extremidade de Rede 1 Porta_Local "2020"
	data:    Pontos de Extremidade de Rede 1 Nome "CouchEP_2"
	data:    Pontos de Extremidade de Rede 1 Porta "2020"
	data:    Pontos de Extremidade de Rede 1 Protocolo "tcp"
	data:    Pontos de Extremidade de Rede 1 Vip "168.61.9.97"
	data:    Pontos de Extremidade de Rede 2 Porta_Local "3389"
	data:    Pontos de Extremidade de Rede 2 Nome "RemoteDesktop"
	data:    Pontos de Extremidade de Rede 2 Porta "3389"
	data:    Pontos de extremidade de Rede 2 Protocolo "tcp"
	data:    Pontos de Extremidade de Rede 2 Vip "168.61.9.97"
	info:    comando vm endpoint show OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_images"></a>Comandos para gerenciar suas imagens de máquinas virtuais do Azure

Imagens de máquinas virtuais são capturas de máquinas virtuais já configuradas que podem ser replicadas conforme necessário.

**vm image list [opções]**

Esse comando obtém uma lista de imagens de máquinas virtuais. Existem três tipos de imagens: imagens criadas pela Microsoft, que são prefixadas com "MSFT", imagens criadas por terceiros, que normalmente são prefixadas com o nome do fornecedor e imagens criadas por você. Para criar imagens, você pode capturar uma máquina virtual existente ou criar uma imagem de um .vhd personalizado carregado no armazenamento de blob. Para obter mais informações sobre como usar um .vhd personalizado, consulte o comando vm image create.
A opção -json especifica que os resultados são retornados em formato JSON bruto. 

	~$ azure vm image list
	data:   Nome                                                                   Categoria   SO
	data:  --------------------------------------------------------------------- --------- -------
	data: CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd Canonical Linux
	data: MSFT__Windows-Server-2008-R2-SP1.11-29-2011 Microsoft Windows
	data: MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011 Microsoft Windows
	data: MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22 Microsoft Windows
	data: MSFT__Windows-Server-8-Beta.2-17-2012 Microsoft Windows
	data: MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22 Microsoft Windows
	data: OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd OpenLogic Linux
	data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
	data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
	data:   WIN2K8-R2-WINRM                                                        Usuário       Windows
	info:   comando vm image list OK   

**vm image show [opções] &lt;nome>**

Esse comando mostra os detalhes da imagem de uma máquina virtual.

	~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
	+ Buscando imagem de VM
	info:   Executando o comando vm image show
	data:   {                                                                      
	data:       Rótulo: 'Windows Server 2008 R2 SP1, Nov 2011',
	data:       Nome: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       Descrição: 'Microsoft Windows Server 2008 R2 SP1',
	data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
	data:       Categoria: 'Microsoft',
	data:       SO: 'Windows',
	data:       Eula: 'http://www.microsoft.com',
	data:       LogicalSizeInGB: '30'
	data:   }
	info:   comando vm image show OK 

**vm image delete [opções] &lt;name>**

Esse comando exclui uma imagem de máquina virtual.

	~$ azure vm image delete imagem_da_minha_vm
	info:   Executando o comando vm image delete
	info:   Imagem de VM excluída: imagem_da_minha_vm                                         
	info:   comando vm image delete OK

**vm image create &lt;nome> [caminho_de_origem]**

Esse comando cria uma imagem de máquina virtual. Os arquivos .vhd personalizados são carregados no armazenamento de blob e, em seguida, a imagem de máquina virtual é criada nela. Em seguida, você pode usar essa imagem de máquina virtual para criar uma máquina virtual. Os parâmetros Location e OS são obrigatórios.

Alguns sistemas impõem limites de descritor de arquivo por processo. Se esse limite for excedido, a ferramenta exibirá um erro de limite de descritor de arquivo. Você pode executar o comando novamente usando o parâmetro -p &lt;número> para reduzir o número máximo de carregamentos paralelos. O número máximo padrão de carregamentos paralelos é 96.

	~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
	info:   Executando o comando vm image create
	+ Recuperando contas de armazenamento
	info:   Tamanho do VHD: 13 MB
	info:   Carregando 13312.5 KB
	Solicitado:100,0% Concluído:100,0% Executando: 105 Tempo: 8s Velocidade: 1721 KB/s
	info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd é carregado com êxito
	info:   comando vm image create OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_data_disks"></a>Comandos para gerenciar seus discos de dados de máquinas virtuais do Azure

Discos de dados são arquivos .vhd no armazenamento de blob que podem ser usados por uma máquina virtual. Para obter mais informações sobre como os discos de dados são implantados no armazenamento de blob, consulte o diagrama técnico do Azure mostrado anteriormente. 

Os comandos para anexar discos de dados (azure vm disk attach e azure vm disk attach-new) atribuem um LUN (número de unidade lógica) ao disco de dados anexado, conforme exigido pelo protocolo SCSI. Ao primeiro disco de dados anexado a uma máquina virtual é atribuído o LUN 0, ao próximo é atribuído o LUN 1 e assim por diante.

Ao desanexar um disco de dados com o comando azure vm detach, use o parâmetro &lt;lun&gt; para indicar qual disco desanexar. 

<div class="dev-callout"><b>Observação</b>
   <p>Observe que você deve sempre desanexar discos de dados na ordem inversa, começando com o LUN de número mais alto que foi atribuído. A camada SCSI do Linux não oferece suporte à desanexação de um LUN de número inferior enquanto um LUN de número superior ainda estiver anexado. Por exemplo, você não deve desanexar o LUN 0, se o LUN 1 ainda estiver anexado.</p>
</div>

**vm disk show [opções] &lt;name>**

Esse comando mostra detalhes sobre um disco do Azure.

	~$ azure vm disk show anucentos-anucentos-0-20120524070008
	info:   Executando o comando vm disk show
	data:   AttachedTo DeploymentName "mycentos"
	data:   AttachedTo HostedServiceName "myanucentos"
	data:   AttachedTo RoleName "myanucentos"
	data:   SO "Linux"
	data:   Local "Visualização do Azure"
	data:   LogicalDiskSizeInGB "30"
	data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
	data:   Nome "mycentos-mycentos-0-20120524070008"
	data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
	info:   comando vm disk show OK

**vm disk list [opções] [nome_da_vm]**

Esse comando lista discos do Azure ou discos anexados a uma máquina virtual especificada. Se for executado com um parâmetro de nome de máquina virtual, retornará todos os discos anexados à máquina virtual. O LUN 1 é criado com a máquina virtual, e todos os outros discos listados são anexados separadamente.

	~$ azure vm disk list mycentos
	info:   Executando o comando vm disk list
	data: Tamanho (GB) do LUN  Nome_do_Blob
	data: --- -------- --------------------------------
	data:   1    30        mycentos-cb39b8223b01f95c.vhd
	data:   2    10        mycentos-e3f0d717950bb78d.vhd
	info:   comando vm disk list OK                                               

Executar esse comando sem um parâmetro de nome de máquina virtual retorna todos os discos.

	~$ azure vm disk list 
	data:   Nome                                        SO
	data:   ------------------------------------------  -------
	data:   mycentos-mycentos-0-20120524070008          Linux
	data:   mycentos-mycentos-2-20120525055052
	data:   mywindows-winvm-20120522223119              Windows
	info:   comando vm disk list OK

**vm disk delete [opções] &lt;name>**

Esse comando exclui um disco do Azure de um repositório pessoal. O disco deve ser desanexado da máquina virtual antes de ser excluído.

	~$ azure vm disk delete mycentos-mycentos-2-20120525055052
	info:   Executando o comando vm disk delete
	info:   Disco excluído: mycentos-mycentos-2-20120525055052                  
	info:   comando vm disk delete OK

**vm disk create &lt;name> [caminho_de_origem]**

Esse comando carrega e registra um disco do Azure. --blob-url, --location, ou --affinity-group deve ser especificado. Se você usar esse comando com [caminho_de_origem], o arquivo .vhd especificado será carregado e uma nova imagem será criada. Em seguida, você pode anexar essa imagem a uma máquina virtual usando o comando vm disk attach.

Alguns sistemas impõem limites de descritor de arquivo por processo. Se esse limite for excedido, a ferramenta exibirá um erro de limite de descritor de arquivo. Você pode executar o comando novamente usando o parâmetro -p &lt;número> para reduzir o número máximo de carregamentos paralelos. O número máximo padrão de carregamentos paralelos é 96. 

	~$ azure vm disk create meu_disco_de_dados ~/test.vhd --location "Oeste dos EUA"
	info:   Executando o comando vm disk create
	info:   Tamanho do VHD: 10 MB                                                       
	info:   Carregando 10240.5 KB
	Solicitado:100,0% Concluído:100,0% Executando: 81 Tempo:    11 s Velocidade:  952 KB/s 
	info:   http://account.blob.core.azure.com/disks/test.vhd carregado com êxito
	info:   comando vm disk create OK

**vm disk upload [opções] &lt;caminho_de_origem> &lt;url_do_blob> &lt;chave_de_conta_de_armazenamento>**

Esse comando permite carregar um disco de vm

	~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
	info:   Executando o comando vm disk upload                                                      
	info:   Carregando 12351.5 KB
	info:   comando vm disk upload OK

**vm disk attach &lt;nome_da_vm> &lt;nome_do_disco_de_imagem>**

Esse comando anexa um disco existente no armazenamento de blob a uma máquina virtual existente implantada em um Serviço de Nuvem.

	~$ azure vm disk attach minha_vm minha_vm-minha_vm-2-201242418259
	info:   Executando o comando vm disk attach
	info:   comando vm disk attach OK

**vm disk attach-new &lt;nome_da_vm> &lt;tamanho_em_gb> [url_do_blob]**

Esse comando exporta um disco de dados para uma máquina virtual do Azure. Neste exemplo, 20 é o tamanho do novo disco, em gigabytes, a ser anexado. Opcionalmente, você pode usar uma URL de blob como o último argumento para especificar explicitamente o blob de destino a ser criado. Se você não especificar uma URL de blob, um objeto blob será gerado automaticamente.

	~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
	info:   Executando o comando vm disk attach-new
	info:   comando vm disk attach-new OK  

**vm disk detach &lt;nome_da_vm> &lt;lun>**

Esse comando desanexa um disco de dados conectado a uma máquina virtual do Azure. &lt;lun> identifica o disco a ser desanexado. Para obter uma lista dos discos associados a um disco antes de desanexá-lo, use o comando vm disk-list &lt;nome_da_vm>.

	~$ azure vm disk detach minha_vm 2
	info:   Executando o comando vm disk detach
	info:   comando vm disk detach OK

##<a name="Commands_to_manage_your_Azure_cloud_services"></a>Comandos para gerenciar seus serviços de nuvem do Azure

Os serviços de nuvem do Azure são aplicativos e serviços hospedados em funções web e de trabalho. Os comandos a seguir podem ser usados para gerenciar os serviços de nuvem do Azure.

**service create [opções] &lt;nome_do_serviço>**

Esse comando cria um novo Serviço de Nuvem

	~$ azure service create newservicemsopentech
	info:    Executando o comando service create
	+ Obtendo locais
	help:    Local:
	  1) Ásia Oriental
	  2) Sudeste da Ásia
	  3) Norte da Europa
	  4) Europa Ocidental:
	  5) Leste dos EUA
	  6) Oeste dos EUA
	  : 6
	+ Criar um Serviço de Nuvem
	data:    Cloud service name newservicemsopentech
	info:    comando service create OK

**service show [opções] &lt;nome_do_serviço>**

Esse comando mostra os detalhes de um Serviço de Nuvem do Azure.

	~$ azure service show newservicemsopentech
	info:    Executando o comando service show
	+ Obtendo Serviço de Nuvem
	data:    Nome newservicemsopentech
	data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
	data:    Local das propriedades Oeste dos EUA
	data:    Rótulo das propriedades newservicemsopentech
	data:    Status das propriedades criado
	data:    dateCreated das propriedades
	data:    dateLastModified das propriedades
	info:   comando service show OK

**service list [opções]**

Esse comando lista os serviços de nuvem do Azure.

	~$ azure service list
	info:   Executando o comando service list
	data:   Nome         Status                                                    
	data:   -----------  -------
	data:   service1     Criado
	data:   service2     Criado
	info:   comando service list OK

**service delete [opções] &lt;name>**

Esse comando exclui um Serviço de Nuvem do Azure.

	~$ azure cloud-service delete meu_serviço
	info:   Executando o comando cloud-service delete meu_serviço 
	info:   comando cloud-service delete OK


##<a name="Commands_to_manage_your_Azure_certificates"></a>Comandos para gerenciar seus certificados do Azure

Os certificados do Azure são certificados (ou seja, certificados SSL) conectados à sua conta do Azure.

**service cert list [opções]**

Esse comando lista os certificados do Azure.

	~$ azure service cert list
	info:   Executando o comando service cert list
	+ Buscando serviços de nuvem                                                      
	+ Buscando certificados                                                        
	data:   Serviço   Impressão digital                                Algoritmo
	data:   --------  ----------------------------------------  ---------
	data:   meu_serviço  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1     
	info:   comando service cert list OK

**service cert create &lt;prefixo_dns> &lt;arquivo> [senha]**

Este comando carrega um certificado. Deixe o prompt de senha em branco para certificados que não são protegidos por senha.

	~$ azure service cert create nghinazz ~/publishSet.pfx
	info:   Executando o comando service cert create
	Senha do certificado: 
	+ Criando certificado                                                         
	info:   comando service cert create OK

**service cert delete [opções] &lt;impressão_digital>**

Esse comando exclui um certificado.

	~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
	info:   Executando o comando service cert delete
	+ Excluindo um certificado                                                         
	info:   nghinazz : certificado excluído
	info:   comando service cert delete OK


##<a name="Commands_to_manage_your_web_sites"></a>Comandos para gerenciar seus sites

Um Site do Azure é uma configuração web acessível pelo URI. Os sites são hospedados em máquinas virtuais, mas você não precisa se preocupar com os detalhes de criação e implantação da máquina virtual. Esses detalhes são tratados para você pelo Azure.

**site list [opções]**

Esse comando lista seus sites.

	~$ azure site list
	info:   Executando o comando site list
	data:   Nome            Estado    Nomes de host                                        
	data:   --------------  -------  --------------------------------------------------
	data:   mongosite       Executando  mongosite.antdf0.antares.windows.net     
	data:   myphpsite       Executando  myphpsite.antdf0.antares.windows.net     
	data:   mydrupalsite36  Executando  mydrupalsite36.antdf0.antares.windows.net
	info:   comando site list OK

**site set [opções] [nome]**

Esse comando irá definir opções de configuração para seu site [nome]

	~$ azure site set
	info:    Executando o comando site set
	Nome do site: mydemosite
	+ Obtendo sites
	+ Atualizando as informações de configuração do site
	info:    comando site set OK

**site deploymentscript [opções]**

Esse comando gera um script de implantação personalizado

	~$ azure site deploymentscript --node
	info:    Executando o comando site deploymentscript
	info:    Gerando o script de implantação do site node.js
	info:    Arquivos de script de implantação gerados
	info:    comando site deploymentscript OK

**site create [opções] [nome]**

Esse comando cria um novo site e o diretório local. 

	~$ azure site create meu_site
	info:   Executando o comando site create
	info:   Usando o local northeuropewebspace
	info:   Criando um novo site
	info:   Site criado em mysite.antdf0.antares.windows.net
	info:   Inicializando o repositório
	info:   Repositório inicializado
	info:   comando site create OK

<div class="dev-callout"><b>Observação</b>
   <p>O nome do site deve ser exclusivo. Você não pode criar um site com o mesmo nome DNS de um site existente.</p>
</div>

**site portal [opções] [nome]**

Esse comando abre o portal em um navegador para que você possa gerenciar seus sites.

	~$ azure site portal meu_site
	info:   Executando o comando site portal
	info:   Iniciando o navegador em https://windows.azure.net/#Workspaces/WebsiteExtension/Website/mysite/dashboard
	info:   comando site portal OK

**site browse [opções] [nome]**

Esse comando abre o seu site da web em um navegador.

	~$ azure site browse meu_site
	info:   Executando o comando site browse
	info:   Iniciando o navegador em http://mysite.antdf0.antares-test.windows-int.net
	info:   comando site browse OK

**site show [opções] [nome]**

Esse comando mostra os detalhes de um site.

	~$ azure site show meu_site
	info:   Executando o comando site show
	info:   Mostrando detalhes do site
	data:   Site AdminEnabled true
	data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
	data:   Nome do site meu_site
	data:   Proprietário do site 00060000814EDDEE
	data:   Nome do meu-site RepositorySiteName
	data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
	data:   Estado do Site Executando
	data:   UsageState do Site Normal
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
	data:   Repositório https://mysite.scm.antdf0.antares-test.windows-int.net/
	info:   comando site show OK

**site delete [opções] [nome]**

Esse comando exclui um site.

	~$ azure site delete meu_site
	info:   Executando o comando site delete
	info:   Excluindo o site meu_site
	info:   Site meu_site excluído
	info:   comando site delete OK

**site start [opções] [nome]**

Esse comando inicia um site.

	~$ azure site start meu_site
	info:   Executando o comando site start
	info:   Iniciando o site meu_site
	info:   Site meu_site iniciado
	info:    comando site start OK

**site stop [opções] [nome]**

Esse comando pára um site.

	~$ azure site stop meu_site
	info:   Executando o comando site stop
	info:   Parando o site meu_site
	info:   Site meu_site parado
	info:   comando site stop OK

**site location list [opções]**

Esse comando lista os locais de seus sites.

	~$ azure site location list
	info:    Executando o comando site location list
	+ Obtendo locais
	data:    Nome
	data:    ----------------
	data:    Europa Ocidental
	data:    Oeste dos EUA
	data:    Centro-Norte dos EUA
	data:    Norte da Europa
	data;    Ásia Oriental
	data:    Leste dos EUA
	info:    comando site location list OK

###Comandos para gerenciar as configurações do aplicativo do Site

**site appsetting list [opções] [nome]**

Esse comando lista a configuração do aplicativo adicionado ao site

	~$ azure site appsetting list
	info:    Executando o comando site appsetting list
	Nome do site: meu_site_de_demonstração
	+ Obtendo sites
	+ Obtendo informações de configuração do site
	data:    Nome  Valor
	data:    ----  -----
	data:    teste  valor
	info:    comando site appsetting list OK

**site appsetting add [opções] &lt;par_chave_valor> [nome]**

Esse comando adiciona uma configuração de aplicativo a seu site como um par de chave e valor

	~$ azure site appsetting add test=value
	info:    Executando o comando site appsetting add
	Nome do site: mydemosite
	+ Obtendo sites
	+ Obtendo informações de configuração do site
	+ Atualizando as informações de configuração do site
	info:    comando site appsetting add OK

**site appsetting delete [opções] &lt;chave> [nome]**

Esse comando exclui a configuração do aplicativo especificado do site

	~$ azure site appsetting delete test
	info:    Executando o comando site appsetting delete
	Nome do site: mydemosite
	+ Obtendo sites
	+ Obtendo informações de configuração do site
	Excluir teste de configuração do aplicativo? [s/n] s
	+ Atualizando as informações de configuração do site
	info:    comando site appsetting delete OK

**site appsetting show [opções] &lt;chave> [nome]**

Esse comando exibe os detalhes da configuração do aplicativo especificado

	~$ azure site appsetting show test
	info:    Executando o comando site appsetting show
	Nome do site: mydemosite
	+ Obtendo sites
	+ Obtendo informações de configuração do site
	data:    Valor:  valor
	info:    comando site appsetting show OK

###Comandos para gerenciar os certificados de seu Site

**site cert list [opções] [nome]**

Esse comando exibe uma lista dos certificados do site

	~$ azure site cert list
	info:    Executando o comando site cert list
	Nome do site: mydemosite
	+ Obtendo sites
	+ Obtendo informações do site
	data:    Assunto                       Data de Expiração	                  Impressão digital
	data:    ----------------------------  -----------------------------------------
	----------------  ----------------------------------------
	data:    *.msopentech.com              Sex 28 de novembro de 2014 09h49m57 GMT-0800 (Hora Padrão do Pacífico)  A40E82D3DC0286D1F58650E570ECF8224F69A148
	data:    msopentech.azurewebsites.net  Sex 19 de junho de 2015 11h57m32 GMT-0700 (Hor. de Verão do Pacífico)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
	info:   comando site cert list OK

**site cert add [opções] &lt;caminho_do_certificado> [nome]**

**site cert delete [opções] &lt;impressão_digital> [nome]**

**site cert show [opções] &lt;impressão_digital> [nome]**

Esse comando mostra os detalhes do certificado

	~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    Executando o comando site cert show
	Nome do site: mydemosite
	+ Obtendo sites
	+ Obtendo informações do site
	data:    Certificado hostNames 0=msopentech.azurewebsites.net
	data:    expirationDate do certificado
	data:    Nome_amigável do certificado msopentech.azurewebsites.net
	data:    issueDate do certificado
	data:    Emissor do certificado CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
	data:    subjectName do certificado msopentech.azurewebsites.net
	data:    Impressão digital do certificado CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    comando site cert show OK

###Comandos para gerenciar as cadeias de conexão do Site

**site connectionstring list [opções] [nome]**

**site connectionstring add [opções] &lt;nome_da_conexão> &lt;valor> &lt;tipo> [nome]**

**site connectionstring delete [opções] &lt;nome_da_conexão> [valor]**

**site connectionstring show [opções] &lt;nome_da_conexão> [nome]**

###Comandos para gerenciar os documentos padrão do Site

**site defaultdocument list [opções] [nome]**

**site defaultdocument add [opções] &lt;documento> [nome]**

**site defaultdocument delete [opções] &lt;documento> [nome]**

###Comandos para gerenciar as implantações de seu Site

**site deployment list [opções] [nome]**

**site deployment show [opções] &lt;commitId> [nome]**

**site deployment redeploy [opções] &lt;commitId> [nome]**

**site deployment github [opções] [nome]**

**site deployment user set [opções] [nome_do_usuário] [senha]**

###Comandos para gerenciar os domínios de seu Site

**site domain list [opções] [nome]**

**site domain add [opções] &lt;dn> [nome]**

**site domain delete [opções] &lt;dn> [nome]**

###Comandos para gerenciar os mapeamentos de manipulador do Site

**site handler list [opções] [nome]**

**site handler add [opções] &lt;extensão> &lt;processador> [nome]**

**site handler delete [opções] &lt;extensão> [nome]**

###Comandos para gerenciar o diagnóstico de seu Site

**site log download [opções] [nome]**

Baixar um arquivo. zip do diagnóstico de seu site

	~$ azure site log download
	info:    Executando o comando site log download
	Nome do site: mydemosite
	+ Obtendo sites
	+ Obtendo informações do site
	+ Baixando o log de diagnóstico em diagnostics.zip
	info:    comando site log download OK

**site log tail [opções] [nome]**

Esse comando conecta seu terminal ao serviço de log de streaming

	~$ azure site log tail
	info:    Executando o comando site log tail
	Nome do site: mydemosite
	+ Obtendo sites
	+ Obtendo informações do site
	2013-11-19T17:24:17 Bem-vindo, agora você está conectado ao serviço de streaming de log.

**site log set [opções] [nome]**

Esse comando configura as opções de diagnóstico

	~$ azure site log set -a
	info:    Executando o comando site log set
	+ Obtendo opções de saída
	help:    Saída:
	  1) arquivo
	  2) armazenamento
	  : 1
	Nome do site: mydemosite
	+ Obtendo locais
	+ Obtendo sites
	+ Obtendo informações do site
	+ Obtendo configurações de diagnóstico
	+ Atualizando as configurações de diagnóstico
	info:    comando site log set OK

###Comandos para gerenciar os repositórios de seu Site

**site repository branch [opções] &lt;ramificação> [nome]**

**site repository delete [opções] [nome]**

**site repository sync [opções] [nome]**

###Comandos para gerenciar o dimensionamento de seu Site

**site scale mode [opções] &lt;modo> [nome]**

**site scale instances [opções] &lt;instâncias> [nome]**


##<a name="Commands_to_manage_mobile_services"></a>Comandos para gerenciar os Serviços Móveis do Azure

Os Serviços Móveis do Azure reúnem um conjunto de serviços do Azure que habilitam recursos de back-end para seus aplicativos. Os comandos de Serviços Móveis estão divididos nas seguintes categorias:

+ [Comandos para gerenciar instâncias do serviço móvel](#Mobile_Services)
+ [Comandos para gerenciar a configuração do serviço móvel](#Mobile_Configuration)
+ [Comandos para gerenciar tabelas do serviço móvel](#Mobile_Tables)
+ [Comandos para gerenciar scripts do serviço móvel](#Mobile_Scripts)
+ [Comandos para gerenciar trabalhos agendados](#Mobile_Jobs)
+ [Comandos para dimensionar um serviço móvel](#Mobile_Scale)

As opções a seguir se aplicam à maioria dos comandos de Serviços Móveis:

+ **-h** ou **--help**: exibe informações sobre o uso da saída.
+ **-s `<id>`** ou **--subscription `<id>`**: usa uma assinatura específica, especificada como `<id>`.
+ **- v** ou **--verbose**: grava saída detalhada.
+ **--json**: grava uma saída JSON.

###<a name="Mobile_Services"></a>Comandos para gerenciar instância dos Serviços Móveis

**mobile locations [opções]**

Esse comando lista os locais geográficos suportados pelos Serviços Móveis.

	~$ azure mobile locations
	info:    Executando o comando mobile locations
	info:    Leste dos EUA (padrão)
	info:    Oeste dos EUA		
	info:    Norte da Europa

**mobile create [opções] [nome_do_serviço] [nome_do_usuário_admin_SQL] [senha_do_admin_SQL]**

Esse comando cria um serviço móvel juntamente com um Banco de Dados e servidor SQL.

	~$ azure mobile create todolist seu_nome_de_logon Secure$Password
	info:    Executando o comando mobile create
	+ Criando serviço móvel
	info:    Estado geral do aplicativo: Íntegro
	info:    Estado do serviço móvel (todolist): ProvisionConfigured
	info:    Estados do banco de dados SQL (todolist_db): Provisionado
	info:    Estado do servidor SQL (e96ean1c6v): ProvisionConfigured
	info:    comando mobile create OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-r `<sqlServer>`** ou **--sqlServer `<sqlServer>`**: usar um servidor de banco de dados SQL existente, especificado como `<sqlServer>`.
+ **-d `<sqlDb>`** ou **– sqlDb `<sqlDb>`**: usar banco de dados SQL existente, especificado como `<sqlDb>`.
+ **-l `<location>`** ou **--location `<location>`**: criar o serviço em um local específico, especificado como `<location>`. Executar azure mobile locations para obter os locais disponíveis.	
+ **- sqlLocation `<location>`**: criar o servidor SQL em um `<location>` específico; o padrão é o local do serviço móvel.

**mobile delete [opções] [nome_do_serviço]**

Esse comando exclui um serviço móvel juntamente com seu Banco de Dados e servidor SQL.

	~$ azure mobile delete todolist -a -q
	info:    Executando o comando mobile delete
	data:    Serviço móvel todolist
	data:    Banco de dados SQL todolistAwrhcL60azo1C401
	data:    Servidor SQL fh1kvbc7la
	+ Excluindo serviço móvel
	info:    Serviço móvel excluído
	+ Excluindo o servidor SQL
	info:    Servidor SQL excluído
	+ Excluindo aplicativo móvel
	info:    Aplicativo móvel excluído
	info:    comando mobile delete OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-d** ou **--deleteData**: excluir todos os dados desse serviço móvel do banco de dados.
+ **-a** ou **--deleteAll**: excluir o banco de dados SQL e o servidor.
+ **-q ou **--quiet**: não solicita confirmação. Use esta opção em scripts automatizados.

**mobile list [opções]**

Esse comando lista os serviços móveis.

	~$ azure mobile list
	info:    Executando o comando mobile list
	data:    Nome         Estado  URL
	data:    ------------  -----  --------------------------------------
	data:    todolist      Pronta https://todolist.azure-mobile.net/
	data:    mymobileapp   pronto  https://mymobileapp.azure-mobile.net/
	info:   comando mobile list OK

**mobile show [opções] [nome_do_serviço]**

Esse comando exibe detalhes sobre um serviço móvel.

	~$ azure mobile show todolist
	info:    Executando o comando mobile show
	+ Obtendo informações
	info:    Aplicativo móvel
	data:   status Íntegro
	data:    Nome do serviço todolist
	data:    Status do serviço móvel ProvisionConfigured
	data:    Nome do banco de dados SQL todolistAwrhcL60azo1C401
	data:    Status do banco de dados SQL Vinculado
	data:    Nome do servidor SQL fh1kvbc7la
	data:    Status do servidor SQL Vinculado
	info:    Serviço móvel
	data:    nome todolist
	data:    estado Pronto
	data:    applicationUrl https://todolist.azure-mobile.net/
	data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    espaço na web WESTUSWEBSPACE
	data:    região Oeste dos EUA
	data:    tabelas TodoItem
	info:   comando mobile show OK	

**mobile restart [opções] [nome_do_serviço]**

Esse comando reinicia uma instância do serviço móvel.

	~$ azure mobile restart todolist
	info:    Executando o comando mobile restart
	+ Reiniciando o serviço móvel
	info:    Serviço reiniciado.
	info:    comando mobile restart OK

**mobile log [opções] [nome_do_serviço]**

Esse comando retorna logs do serviço móvel, filtrando todos os tipos de log, menos `erro`.

	~$ azure mobile log todolist -t error
	info:    Executando o comando mobile log
	data:
	data:    timeCreated 2013-01-07T16:04:43.351Z
	data:    tipo erro
	data:    origem /scheduler/TestingLogs.js
	data:    mensagem Isto é um erro.
	data:
	info:   comando mobile log OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-r `<query>`** ou **--query `<query>`**: executa a consulta de log especificada.
+ **-t `<type>`** ou **--type `<type>`**: filtrar os logs retornados por `<type>` de entrada, que pode ser `information`, `warning` ou `error`.
+ **-k `<skip>`** ou **--skip `<skip>`**: ignora o número de linhas especificadas por `<skip>`.
+ **-p `<top>`** ou **--top `<top>`**: retorna um número de linhas especificado por `<top>`.

<div class="dev-callout"><b>Observação</b>
   <p>O parâmetro **--query** tem precedência sobre **--type**, **--skip** e **--top**.</p>
</div>

**mobile key regenerate [opções] [nome_do_serviço] [tipo]**

Esse comando regenera a chave do aplicativo do serviço móvel.

	~$ azure mobile key regenerate todolist application
	info:    Executando o comando mobile key regenerate
	info:    A nova chave do aplicativo é SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
	info:    comando mobile key regenerate OK

Os tipos de chave são `master` e `application`.

<div class="dev-callout"><b>Observação</b>
   <p>Quando você regenera chaves, os clientes que usam a chave antiga talvez não possam acessar o serviço móvel. Quando regenera a chave do aplicativo, você deve atualizar seu aplicativo com o novo valor da chave. </p>
</div> 

###<a name="Mobile_Configuration"></a>Comandos para gerenciar a configuração do serviço móvel

**mobile config list [opções] [nome_do_serviço]**

Esse comando lista as opções de configuração de um serviço móvel.

	~$ azure mobile config list todolist
	info:    Executando o comando mobile config list
	+ Obtendo a configuração do serviço móvel
	data:    dynamicSchemaEnabled true
	data:    microsoftAccountClientSecret Não configurado
	data:    microsoftAccountClientId Não configurado
	data:    microsoftAccountPackageSID Não configurado
	data:    facebookClientId Não configurado
	data:    facebookClientSecret Não configurado
	data:    twitterClientId Não configurado
	data:    twitterClientSecret Não configurado
	data:    googleClientId Não configurado
	data:    googleClientSecret Não configurado
	data:    apnsMode none
	data:    apnsPassword Não configurado
	data:    apnsCertifcate Não configurado
	info:    comando mobile config list OK

**mobile config get [opções] [nome_do_serviço] [chave]**

Esse comando obtém uma opção de configuração específica de um serviço móvel, esquema dinâmico nesse caso.

	~$ azure mobile config get todolist dynamicSchemaEnabled
	info:    Executando o comando mobile config get
	data:    dynamicSchemaEnabled true
	info:    comando mobile config get OK

**mobile config set [opções] [nome_do_serviço] [chave] [valor]**

Esse comando define uma opção de configuração específica para um serviço móvel, esquema dinâmico nesse caso.

	~$ azure mobile config set todolist dynamicSchemaEnabled false
	info:    Executando o comando mobile config set
	info:    comando mobile config set OK


###<a name="Mobile_Tables"></a>Comandos para gerenciar tabelas do serviço móvel

**mobile table list [opções] [nome_do_serviço]**

Esse comando lista todas as tabelas em seu serviço móvel.

	~$azure mobile table list todolist
	info:    Executando o comando mobile table list
	data:    Nome      Índices  Linhas
	data:    --------  -------  ----
	data:    Canal     1        0
	data:    TodoItem  1        0
	info:    comando mobile table list OK

**mobile table show [opções] [nome_do_serviço] [nome_da_tabela]**

Esse comando retorna detalhes sobre uma tabela específica.

	~$azure mobile table show todolist
	info:    Executando o comando mobile table show
	+ Obtendo informações da tabela
	info:    Estatísticas da tabela:
	data:    Número de registros 5
	info:    Operações da tabela:
	data:    Operação      Script       Permissões
	data:    ---------  -----------  -----------
	data:    inserir     1900 bytes   usuário
	data:    leitura     Usuário não definido
	data:    atualização Usuário não definido
	data:    exclusão    Usuário não definido
	info:    Colunas da tabela:
	data:    Nome  Tipo           Indexado
	data:    ----  -------------  -------
	data:    id    bigint(MSSQL)  Sim
	data:    texto      cadeia de caracteres
	data:    concluído  booliano
	info:    comando mobile table show OK

**mobile table create [opções] [nome_do_serviço] [nome_da_tabela]**

Esse comando cria uma tabela.

	~$azure mobile table create todolist Channels
	info:    Executando o comando mobile table create
	+ Criando tabela
	info:    comando mobile table create OK

Esse comando oferece suporte à seguinte opção adicional:

+ **-p `<permissions>`** ou **--permissions `<permissions>`**: lista delimitada por vírgulas de pares `<operation>`=`<permission>`, em que `<operation>` é `insert`, `read`, `update` ou `delete` e `<permissions>` é `public`, `application` (padrão), `user` ou `admin`.

**mobile data read [opções] [nome_do_serviço] [nome_da_tabela] [consulta]**

Esse comando lê dados de uma tabela.

	~$azure mobile data read todolist TodoItem
	info:    Executando o comando mobile data read
	data:    id  texto     concluído
	data:    --  -------  --------
	data:    1   item #1  false
	data:    2   item #2  true
	data:    3   item #3  false
	data:    4   item #4  true
	info:    comando mobile data read OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-k `<skip>`** ou **--skip `<skip>`**: ignora o número de linhas especificadas por `<skip>`.
+ **-t `<top>`** ou **--top `<top>`**: retorna um número específico de linhas, especificado por `<top>`.
+ **-l** ou **--list**: retorna dados em um formato de lista.

**mobile table update [opções] [nome_do_serviço] [nome_da_tabela]**

Esse comando altera as permissões de exclusão em uma tabela somente para administradores.

	~$azure mobile table update todolist Channels -p delete=admin
	info:    Executando o comando mobile table update
	+ Atualizando permissões
	info:    Permissões atualizadas
	info:    comando mobile table update OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-p `<permissions>`** ou **--permissions `<permissions>`**: lista delimitada por vírgulas de pares `<operation>`=`<permission>`, em que `<operation>` é `insert`, `read`, `update` ou `delete` e `<permissions>` é `public`, `application` (padrão), `user` ou `admin`.
+ **--deleteColumn `<columns>`**: lista de colunas delimitada por vírgulas a serem excluídas, como `<columns>`.
+ **-q** ou **--quiet**: excluir as colunas sem solicitar confirmação.
+ **--addIndex `<columns>`**: lista de colunas delimitada por vírgulas a serem incluídas no índice.
+ **--deleteIndex `<columns>`**: lista de colunas delimitada por vírgulas a serem excluídas do índice.

**mobile table delete [opções] [nome_do_serviço] [nome_da_tabela]**

Esse comando exclui uma tabela.

	~$azure mobile table delete todolist Channels
	info:    Executando o comando mobile table delete
	Deseja realmente excluir a tabela (sim/não): sim
	+ Excluindo tabela
	info:    comando mobile table delete OK

Especifique o parâmetro -q para excluir a tabela sem confirmação. Faça isso para evitar o bloqueio de scripts de automação.

**mobile data truncate [opções] [nome_do_serviço] [nome_da_tabela]**

Esse comando remove todas as linhas de dados da tabela.

	~$azure mobile data truncate todolist TodoItem
	info:    Executando o comando mobile data truncate
	info:    Há sete linhas de dados na tabela.
	Deseja realmente excluir todos os dados da tabela? (s/n): s
	info:    Sete linhas excluídas.
	info:    comando mobile data truncate OK


###<a name="Mobile_Scripts"></a>Comandos para gerenciar scripts

Os comandos desta seção são usados para gerenciar os scripts de servidor que pertencem a um serviço móvel. Para obter mais informações, consulte [Trabalhar com scripts de servidor em Serviços Móveis (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/develop/mobile/how-to-guides/work-with-server-scripts/)

**mobile script list [opções] [nome_do_serviço]**

Esse comando lista scripts registrados, incluindo scripts de tabela e script do agendador.

	~$azure mobile script list todolist
	info:    Executando o comando mobile script list
	+ Obtendo informações do script
	info:    Scripts de tabela
	data:    Nome                   Tamanho
	data:    ---------------------  ----
	data:    table/TodoItem.delete  256
	data:    table/Devices.insert   1660
	error:   Não é possível obter scripts compartilhados
	info: scripts do agendador
	data:    Nome                 Status     Intervalo   Última execução   Próxima execução
	data:    -------------------  ---------  ---------  ---------  ---------
	data:    agendador/indefinido  indefinido  indefinido  indefinido  indefinido
	data:    agendador/indefinido  indefinido  indefinido  indefinido  indefinido
	info:    comando mobile script list OK

**mobile script upload [opções] [nome_do_serviço] [nome_do_script]**

Esse comando carrega um novo script chamado `todoitem.insert.js` do subdiretório `table`:

	~$azure mobile script upload todolist table/todoitem.insert.js
	info:    Executando o comando mobile script upload
	info:    comando mobile script upload OK

O nome do arquivo deve ser composto dos nomes da tabela e da operação e deve estar localizado na subpasta tabela relativa ao local onde o comando é executado. Você também pode usar o parâmetro **-f `<file>`** ou **--file `<file>`** para especificar outro nome de arquivo e caminho do arquivo que contém o script a ser registrado.

**mobile script download [opções] [nome_do_serviço] [nome_do_script]**

Esse comando baixa o script de inserção da tabela TodoItem em um arquivo chamado `todoitem.insert.js` na subpasta `table`.

	~$azure mobile script download todolist table/todoitem.insert.js
	info:    Executando o comando mobile script download
	info:    Script salvo em ./table/todoitem.insert.js
	info:    comando mobile script download OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-p `<path>`** ou **--path `<path>`**: O local no arquivo no qual salva o script, onde o diretório de trabalho atual é o padrão.
+ **-f `<file>`** ou **--file `<file>`**: O nome do arquivo no qual salvar o script.
+ **-o** ou **--override**: substituir um arquivo existente.
+ **-c** ou **--console**: gravar o script no console, em vez de em um arquivo.

**mobile script delete [opções] [nome_do_serviço] [nome_do_script]**

Esse comando remove o script de inserção existente da tabela TodoItem.

	~$azure mobile script delete todolist table/todoitem.insert.js
	info:    Executando o comando mobile script delete
	info:    comando mobile script delete OK

###<a name="Mobile_Jobs"></a>Comandos para gerenciar trabalhos agendados

Os comandos desta seção são usados para gerenciar trabalhos agendados que pertencem a um serviço móvel. Para obter mais informações, consulte [Trabalhos agendados (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/jj860528.aspx).

**mobile job list [opções] [nome_do_serviço]**

Esse comando lista trabalhos agendados.

	~$azure mobile job list todolist
	info:    Executando o comando mobile job list
	info:    Trabalhos agendados
	data:    Nome do trabalho Nome do script      Status      Intervalo     Última execução       Próxima execução
	data:    --------------   ---------------     --------    -----------   --------------------  --------------------
	data:    getUpdates     scheduler/getUpdates  habilitado  15 [minutos]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
	info:    você pode manipular scripts de trabalhos agendados usando o comando 'azure mobile script'.
	info:   comando mobile job list OK

**mobile job create [opções] [nome_do_serviço] [nome_do_trabalho]**

Esse comando cria um novo trabalho chamado `getUpdates` que está agendado para ser executado a cada hora.

	~$azure mobile job create -i 1 -u hour todolist getUpdates 
	info:    Executando o comando mobile job create
	info:    O trabalho foi criado em estado desabilitado. Você pode habilitar o trabalho usando o comando 'azure mobile job update'.
	info:    você pode manipular o script do trabalho agendado usando o comando 'azure mobile script'.
	info:    comando mobile job create OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-i `<number>`** ou **--interval `<number>`**: o intervalo do trabalho, como um inteiro, o valor padrão é `15`.
+ **-u `<unit>`** ou **--intervalUnit `<unit>`**: a unidade de _interval_, que pode ser um dos seguintes valores: 
	+ **minuto** (padrão)
	+ **hora**
	+ **dia**
	+ **mês** 
	+ **nenhum** (trabalhos sob demanda)
+ **-t `<time>`** **--startTime `<time>`** a hora de início da primeira execução do script, no formato ISO; o valor padrão é `agora`.

<div class="dev-callout"><b>Observação</b>
   <p>Novos trabalhos são criados no estado desabilitado porque um script ainda deve ser carregado. Use o comando <strong>mobile script upload</strong> para carregar um script e o comando <strong>mobile job update</strong> para habilitar o trabalho.</p>
</div> 

**mobile job update [opções] [nome_do_serviço] [nome_do_trabalho]**

O comando a seguir habilita o trabalho desabilitado `getUpdates`.

	~$azure mobile job update -a enabled todolist getUpdates 
	info:    Executando o comando mobile job update
	info:    comando mobile job update OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-i `<number>`** ou **--interval `<number>`**: o intervalo do trabalho, como um inteiro, o valor padrão é `15`.
+ **-u `<unit>`** ou **--intervalUnit `<unit>`**: a unidade de _interval_, que pode ser um dos seguintes valores: 
	+ **minuto** (padrão)
	+ **hora**
	+ **dia**
	+ **mês** 
	+ **nenhum** (trabalhos sob demanda)
+ **-t `<time>`** **--startTime `<time>`** a hora de início da primeira execução do script, no formato ISO; o valor padrão é `agora`.
+ **-a `<status>`** ou **--status`<status>`**: o status do trabalho, que pode ser `habilitado` ou `desabilitado`.

**mobile job delete [opções] [nome_do_serviço] [nome_do_trabalho]**

Esse comando remove o trabalho agendado getUpdates do servidor TodoList.

	~$azure mobile job delete todolist getUpdates
	info:    Executando o comando mobile job delete
	info:    comando mobile job delete OK

<div class="dev-callout"><b>Observação</b>
   <p>A exclusão de um trabalho também exclui o script carregado.</p>
</div> 

###<a name="Mobile_Scale"></a>Comandos para dimensionar um serviço móvel

Os comandos desta seção são usados para dimensionar um serviço móvel. Para obter mais informações, consulte [Dimensionando um serviço móvel (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/jj193178.aspx).

**mobile scale show [opções] [nome_do_serviço]**

Esse comando exibe informações de escala, incluindo o modo de computação atual e o número de instâncias.

	~$azure mobile scale show todolist
	info:    Executando o comando mobile scale show
	data:    espaço na web WESTUSWEBSPACE
	data:    computeMode Gratuito
	data:    numberOfInstances 1
	info:    comando mobile scale show OK

**mobile scale change [opções] [nome_do_serviço]**

Esse comando altera a escala do serviço móvel de gratuito para modo premium.

	~$azure mobile scale change -c Reserved -i 1 todolist
	info:    Executando o comando mobile scale change
	+ Redimensionando o serviço móvel
	info:    comando mobile scale change OK

Esse comando oferece suporte às seguintes opções adicionais:

+ **-c `<mode>`** ou **--computeMode `<mode>`**: o modo de computação deve ser `Gratuito` ou `Reservado`.
+ **-i `<count>` ou **--numberOfInstances`<count>`**: o número de instâncias usadas durante a execução em modo reservado.

<div class="dev-callout"><b>Observação</b>
   <p>Quando você define o modo de computação como `Reservado`, todos os serviços móveis da mesma região são executados em modo premium.</p>
</div>  

##<a name="Manage_tool_local_settings"></a>Gerenciar as configurações locais da ferramenta

As configurações locais são a ID de sua assinatura e o Nome da Conta de Armazenamento Padrão.

**config list [opções]**

Esse comando exibe as definições da configuração.

	~$ azure config list
	info:   Exibindo as definições da configuração
	data:   Configuração                Valor                               
	data:   ---------------------  ------------------------------------
	data:   assinatura             chave de 32 dígitos da assinatura
	data:   defaultStorageAccount  nome

**config set [opções] &lt;nome&gt;,&lt;valor&gt;**

Esse comando altera uma definição da configuração.

	~$ azure config set defaultStorageAccount nyname
	info:   Definindo 'defaultStorageAccount' para o valor 'meu_nome'
	info:   Alterações salvas.

##<a name ="Commands_to_manage_service_bus"></a>Comandos para gerenciar o Service Bus

Utilize estes comandos para gerenciar sua conta do Service Bus

**sb namespace create &lt;nome> &lt;local>**

Cria um namespace do Service Bus

	~$ azure sb namespace create mysbnamespacea-test "West US"
	info:    Executando o comando sb namespace create
	+ Criando o namespace mysbnamespacea-test na região Oeste dos EUA
	data:    Nome: mysbnamespacea-test
	data:    Região: Oeste dos EUA
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Ativando
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/
	
	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Habilitado: true
	data:    _: [objeto Objeto]
	info:    comando sb namespace create OK

**sb namespace show &lt;nome>**

Exibir detalhes sobre um namespace específico

	~$ azure sb namespace show mysbnamespacea-test
	info:    Executando o comando sb namespace show
	+ Obtendo o namespace
	data:    Nome: mysbnamespacea-test
	data:    Região: Oeste dos EUA
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Ativo
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/
	
	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Habilitado: true
	data:    UpdatedAt: 2013-11-14T16:25:37.85Z
	info:    comando sb namespace show OK

**sb namespace list**

Listar todos os namespaces criados para sua conta

	~$ azure sb namespace list
	info:    Executando o comando sb namespace list
	+ Obtendo os namespaces
	data:    Nome                 Região          Status
	data:    -------------------  --------------  ------
	data:    mysbnamespacea-test  Oeste dos EUA   Ativo
	info:    comando sb namespace list OK

**sb namespace delete &lt;nome>**

Remover um namespace

	~$ azure sb namespace delete mysbnamespacea-test
	info:    Executando o comando sb namespace delete
	Excluir o namespace mysbnamespacea-test? [s/n] s
	+ Excluindo o namespace mysbnamespacea-test
	info:    comando sb namespace delete OK

**sb namespace location list**

Exibir uma lista de todos os namespaces locais disponíveis

	~$ azure sb namespace location list
	info:    Executando o comando sb namespace location list
	+ Obtendo locais
	data:    Nome                    Código
	data:    ----------------        ----------------
	data:    Ásia Oriental           Ásia Oriental
	data:    Oeste da Europa         Oeste da Europa
	data:    Norte da Europa         Norte da Europa
	data:    Leste dos EUA           Leste dos EUA
	data:    Sudeste da Ásia         Sudeste da Ásia
	data:    Centro-Norte dos EUA    Centro-Norte dos EUA
	data:    Oeste dos EUA           Oeste dos EUA
	data:    Centro-Sul dos EUA      Centro-Sul dos EUA
	info:    comando sb namespace location list OK

**sb namespace verify &lt;nome>**

Verificar se o namespace está disponível

##<a name ="Commands_to_manage_sql"></a>Comandos para gerenciar bancos de dados SQL

Use estes comandos para gerenciar seus bancos de dados SQL do Azure

###Comandos para gerenciar servidores SQL

Use estes comandos para gerenciar seus servidores SQL

**sql server create &lt;logon_do_administrador> &lt;senha_do_administrador> &lt;local>**

Criar um novo servidor de banco de dados

	~$ azure sql server create test T3stte$t "West US"
	info:    Executando o comando sql server create
	+ Criando servidor SQL
	data:    Nome do Servidor i1qwc540ts
	info:    comando sql server create OK

**sql server show &lt;nome>**

Exibir detalhes do servidor

	~$ azure sql server show xclfgcndfg
	info:    Executando o comando sql server show
	+ Obtendo o servidor SQL
	data:    Nome do Servidor SQL xclfgcndfg
	data:    logon_do_administrador do Servidor SQL msopentechforums
	data:    Local do Servidor SQL Oeste dos EUA
	data:    Nome_do_domínio_totalmente_qualificado do SQL xclfgcndfg.database.windows.net
	info:    comando sql server show OK

**sql server list**

Obter a lista de servidores

	~$ azure sql server list
	info:    Executando o comando sql server list
	+ Obtendo o servidor SQL
	data:    Nome        Local
	data:    ----------  --------
	data:    xclfgcndfg  Oeste dos EUA
	info:    comando sql server list OK

**sql server delete &lt;nome>**

Exclui um servidor 

	~$ azure sql server delete i1qwc540ts
	info:    Executando o comando sql server delete
	Excluir servidor i1qwc540ts? [s/n] s
	+ Removendo o SQL Server
	info:    comando sql server delete OK

###Comandos para gerenciar bancos de dados SQL

Use estes comandos para gerenciar seus bancos de dados SQL

**sql db create [opções] &lt;nome_do_servidor> &lt;nome_do_banco_de_dados> &lt;senha_do_administrador>**

Cria uma nova instância do banco de dados

	~$ azure sql db create fr8aelne00 newdb test
	info:    Executando o comando sql db create
	Senha do administrador: ********
	+ Criando um banco de dados SQL Server
	info:    comando sql db create OK

**sql db show [opções] &lt;nome_do_servidor> &lt;nome_do_banco_de_dados> &lt;senha_do_administrador>**

Exibir detalhes do banco de dados

	C:\windows\system32>azure sql db show fr8aelne00 newdb test
	info:    Executando o comando sql db show
	Senha do administrador: ********
	+ Obtendo bancos de dados SQL Server
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
	data:    Id do banco de dados 4
	data:    Nome do Banco de Dados newdb
	data:    Banco de dados ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Banco de dados AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Banco de dados ServiceObjectiveAssignmentState 1
	data:    Banco de dados ServiceObjectiveAssignmentStateDescription Concluído
	data:    Banco de dados ServiceObjectiveAssignmentErrorCode
	data:    Banco de dados ServiceObjectiveAssignmentErrorDescription
	data:    Banco de dados ServiceObjectiveAssignmentSuccessDate
	data:    Banco de dados Web Edition
	data:    Banco de dados MaxSizeGB 1
	data:    Banco de dados MaxSizeBytes 1073741824
	data:    Banco de dados CollationName SQL_Latin1_General_CP1_CI_AS
	data:    CreationDate do banco de dados
	data:    Banco de dados RecoveryPeriodStartDate
	data:    IsSystemObject do banco de dados
	data:    Status do banco de dados 1
	data:    Banco de dados IsFederationRoot
	data:    Banco de dados SizeMB -1
	data:    Banco de dados IsRecursiveTriggersOn
	data:    Banco de dados IsReadOnly
	data:    Banco de dados IsFederationMember
	data:    Banco de dados IsQueryStoreOn
	data:    Banco de dados IsQueryStoreReadOnly
	data:    Banco de dados QueryStoreMaxSizeMB
	data:    Banco de dados QueryStoreFlushPeriodSeconds
	data:    Banco de dados QueryStoreIntervalLengthMinutes
	data:    Banco de dados QueryStoreClearAll
	data:    Banco de dados QueryStoreStaleQueryThresholdDays
	info:    comando sql db show OK

**sql db list [opções] &lt;nome_do_servidor> &lt;senha_do_administrador>**

Listar os bancos de dados

	~$ azure sql db list fr8aelne00 test
	info:    Executando o comando sql db list
	Senha do administrador: ********
	+ Obtendo bancos de dados SQL Server
	data:    Nome    Edição  Agrupamento                     MaxSizeInGB
	data:    ------  -------  ----------------------------  -----------
	data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
	info:    comando sql db list OK

**sql db delete [opções] &lt;nome_do_servidor> &lt;nome_do_banco_de_dados> &lt;senha_do_administrador>**

Exclui um banco de dados 

	~$ azure sql db delete fr8aelne00 newdb test
	info:    Executando o comando sql db delete
	Senha do administrador: ********
	Excluir de banco de dados newdb? [s/n] s
	+ Obtendo bancos de dados SQL Server
	+ Removendo o banco de dados
	info:    comando sql db delete OK

###Comandos para gerenciar suas regras de firewall do SQL Server

Use estes comandos para gerenciar suas regras de firewall do SQL Server

**sql firewallrule create [opções] &lt;nome_do_servidor> &lt;nome_da_regra> &lt;endereço_IP_inicial> &lt;endereço_IP_final>**

Criar uma nova regra de firewall para um SQL Server

	~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
	info:    Executando o comando sql firewallrule create
	+ Criando Regra de Firewall
	info:    comando sql firewallrule create OK

**sql firewallrule show [opções] &lt;nome_do_servidor> &lt;nome_da_regra>**

Mostrar detalhes da regra de firewall

	~$ azure sql firewallrule show fr8aelne00 allowed
	info:    Executando o comando sql firewallrule show
	+ Obtendo a regra de firewall
	data:    Nome da regra de firewall permitido
	data:    Regra de firewall tipo Microsoft.SqlAzure.FirewallRule
	data:    Estado da regra de firewall Normal
	data:    Regra de firewall SelfLink https://management.core.windows.net/9e672699-105
	5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
	data:    Regra de firewall ParentLink https://management.core.windows.net/9e672699-1
	055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
	data:    Endereço_IP_Inicial da regra de firewall 131.107.0.0
	data:    Endereço_IP_Final da regra de firewall 131.107.255.255
	info:    comando sql firewallrule show OK

**sql firewallrule list [opções] &lt;nome_do_servidor>**

Listar as regras de firewall

	~$ azure sql firewallrule list fr8aelne00
	info:    Executando o comando sql firewallrule list
	\data:    Nome        Endereço IP inicial   Endereço IP final
	data:    -----------  ------------------    ---------------
	data:    permitido     131.107.0.0          131.107.255.255
	+
	info:    comando sql firewallrule list OK

**sql firewallrule delete [opções] &lt;nome_do_servidor> &lt;nome_da_regra>**

Esse comando irá excluir uma regra de firewall

	~$ azure sql firewallrule delete fr8aelne00 allowed
	info:    Executando o comando sql firewallrule delete
	Excluir a regra permitida? [s/n] s
	+ Removendo a regra de firewall
	info:    comando sql firewallrule delete OK

##<a name ="Commands_to_manage_vnet"></a>Comandos para gerenciar suas Redes Virtuais

Use estes comandos para gerenciar suas Redes Virtuais

**network vnet create [opções] &lt;local>**

Criar uma nova Rede Virtual

	~$ azure network vnet create vnet1 --location "West US" -v
	info:    Executando o comando network vnet create
	info:    Usando o IP inicial do espaço de endereço padrão: 10.0.0.0
	info:    Usando o cidr do espaço de endereço padrão: 8
	info:    Usando o IP inicial de sub-rede padrão: 10.0.0.0
	info:    Usando o cidr de sub-rede padrão: 11
	verbose: Espaço de Endereço [iniciando IP/CIDR (contagem de VM máxima)]: 10.0.0.0/8 (16777216)
	verbose: Sub-rede [Iniciando IP/CIDR (Contagem de VM máxima)]: 10.0.0.0/11 (2097152)
	verbose: Buscando a configuração da rede
	verbose: Buscando ou criando grupo de afinidade
	verbose: Buscando Grupos de Afinidade
	verbose: Buscando locais
	verbose: Criando novo grupo de afinidade AG1
	info:    Usando o grupo de afinidade AG1
	verbose: Atualizando a Configuração da Rede
	info:    comando network vnet create OK

**network vnet show &lt;nome>**

Mostrar detalhes de uma Rede Virtual

	~$ azure network vnet show vnet1
	info:    Executando o comando network vnet show
	+ Buscando Redes Virtuais
	data:    Nome "vnet1"
	data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
	data:    AffinityGroup "AG1"
	data:    Estado "Criada"
	data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
	data:    Sub-redes 0 Nome "subnet-1"
	data:    Sub-redes 0 AddressPrefix "10.0.0.0/11"
	info:    comando network vnet show OK

**vnet list**

Listar todas as Redes Virtuais existentes

	~$ azure network vnet list
	info:    Executando o comando network vnet list
	+ Buscando Redes Virtuais
	data:    Nome        Status   AffinityGroup
	data:    ----------  -------  -------------
	data:    vnet1      Criada   AG1
	data:    vnet2      Criada   AG1
	data:    vnet3      Criada   AG1
	data:    vnet4      Criada   AG1
	info:    comando network vnet list OK

**network vnet show &lt;nome>**

Mostrar detalhes de uma Rede Virtual especificada

	~$ azure network vnet show opentechvn1
	info:    Executando o comando network vnet show
	+ Buscando Redes Virtuais
	data:    Nome "opentechvn1"
	data:    Id "cab41cb0-396a-413b-83a1-302f0f1c867d"
	data:    AffinityGroup "AG-CLI-456f89eaa7fae2b3"
	data:    Estado "Criada"
	data:    AddressSpace AddressPrefixes 0 "10.100.23.255/27"
	data:    Sub-redes 0 Nome "frontend"
	data:    Sub-redes 0 AddressPrefix "10.100.23.224/29"
	info:    comando network vnet show OK

**network vnet delete &lt;nome>**

Exclui a Rede Virtual especificada

	~$ azure network vnet delete opentechvn1
	info:    Executando o comando network vnet delete
	+ Buscando a Configuração da Rede
	Excluir a rede virtual opentechvn1?  (s/n) s
	+ Excluindo a rede virtual opentechvn1?
	info:    comando network vnet delete OK

**network export [caminho_do_arquivo]**

Para configuração de rede avançada, você pode exportar sua configuração de rede localmente. Observe que a configuração de rede exportada inclui as configurações do servidor DNS, as configurações de rede virtual, as configurações de site de rede local e outras configurações.

**network import [caminho_do_arquivo]**

Importar uma configuração de rede local.

**network dnsserver register [opções] &lt;dnsIP>**

Registrar um servidor DNS que você pretende usar para a resolução de nomes em sua configuração de rede

	~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
	info:    Executando o comando network dnsserver register
	+ Buscando a Configuração da Rede
	+ Atualizando a Configuração de Rede
	info:    comando network dnsserver register OK

**network dnsserver list**

Listar todos os servidores DNS registrados em sua configuração de rede

	~$ azure network dnsserver list
	info:    Executando o comando network dnsserver list
	+ Buscando a Configuração da Rede
	data:    ID do Servidor DNS    IP do Servidor DNS
	data:    --------------------  --------------
	data:    DNS-bb39b4ac34d66a86  44.55.22.11
	data:    FrontEndDnsServer     98.138.253.109
	info:    comando network dnsserver list OK

**network dnsserver unregister [opções] &lt;dnsIP>**

Remove uma entrada de servidor DNS da configuração de rede

	~$ azure network dnsserver unregister 77.88.99.11
	info:    Executando o comando network dnsserver unregister
	+ Buscando a Configuração da Rede
	Excluir a entrada do servidor DNS dns-4 (77.88.99.11) %s? (s/n) s
	+ Excluindo a entrada do servidor DNS dns-4 ( 77.88.99.11 )
	info:    comando network dnsserver unregister OK


