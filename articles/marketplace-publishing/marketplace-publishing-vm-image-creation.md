<properties
   pageTitle="Criar uma imagem de máquina virtual para o Azure Marketplace | Microsoft Azure"
   description="Instruções detalhadas sobre como criar uma imagem de máquina virtual para o Azure Marketplace para outras pessoas comprarem."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="03/07/2016"
   ms.author="hascipio; v-divte"/>

# Guia para criar uma imagem de máquina virtual para o Azure Marketplace

Este artigo, **Etapa 2**, mostra a preparação dos VHDs (discos rígidos virtuais) que você implantará no Azure Marketplace. Seus VHDs são a base de sua SKU. O processo é diferente dependendo se você está fornecendo uma SKU baseada em Linux ou em Windows. Este artigo aborda ambos os cenários. Esse processo pode ser executado em paralelo com [Criação e registro de conta][link-acct-creation].

## 1\. Definir ofertas e SKUs

Nesta seção, você aprenderá a definir as ofertas e as SKUs associadas.

Uma oferta é um "pai" de todas as suas SKUs. Pode haver várias ofertas. Como você decide estruturar suas ofertas é com você. Quando uma oferta é movida para teste, vai com todas as suas SKUs. Considere cuidadosamente seus identificadores de SKU, já que ficarão visíveis na URL:

- Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}

- Portal de Visualização do Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}

Uma SKU é o nome comercial para uma imagem de VM. Uma imagem de VM contém um disco de sistema operacional e zero ou mais discos de dados. É, essencialmente, o perfil completo de armazenamento para uma máquina virtual. É necessário um VHD por disco. Mesmo discos de dados em branco exigem a criação de um VHD.

Independentemente de qual sistema operacional que você usa, adicione apenas o número mínimo de discos de dados necessários para a SKU. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implantação, mas sempre podem adicionar discos durante ou após a implantação, se precisar deles.

### 1\.1 Adicionar uma oferta

1. Entre no [Portal de Publicação][link-pubportal] usando a sua conta de vendedor.
2. Selecione a guia **Máquinas virtuais** do Portal de Publicação. No campo de entrada solicitado, insira o nome da oferta. O nome da oferta geralmente é o nome do produto ou serviço que você pretende vender no Azure Marketplace.
3. Selecione **Criar**.

### 1\.2 Defina uma SKU
Depois de adicionar uma oferta, você terá de definir e identificar sua SKU. Você pode ter várias ofertas e cada oferta pode ter várias SKUs abaixo dela. Quando uma oferta é movida para teste, vai com todas as suas SKUs.

1. **Adicionar uma SKU.** A SKU requer um identificador, que é usado na URL. O identificador tem que ser exclusivo no perfil de publicação, mas não há risco de conflito de identificador com outros editores.

> [AZURE.NOTE] A oferta e o identificador da SKU são exibido na URL da oferta no Marketplace.

2. **Adicionar uma descrição resumida para a sua SKU.** As descrições resumidas ficam visíveis para os clientes; portanto, você deve torná-las fáceis de ler. Essa informação não precisa ser bloqueada até a fase "Mover para o Preparo". Até lá, você estará livre para editá-la.
3. Se você estiver usando SKUs baseadas no Windows, siga os links sugeridos para adquirir as versões aprovadas do Windows Server.

## 2\. Criar VHD compatível com o Azure (baseado em Linux)
Esta seção concentra-se nas melhores práticas para a criação de uma imagem VM baseada em Linux para o Azure Marketplace. Para uma explicação passo a passo, consulte a seguinte documentação: [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux][link-azure-vm-1]

> [AZURE.TIP] Muitas das etapas a seguir (por exemplo, instalação de agente, parâmetros de inicialização do kernel) já foram realizadas para imagens do Linux disponíveis na galeria de imagens do Microsoft Azure. Assim, começar com uma dessas imagens como uma base pode representar uma economia de tempo em relação a configurar uma imagem do Linux que não está ciente do Azure.

### 2\.1 Escolha o tamanho correto do VHD
SKUs publicadas (imagens de VM) devem ser projetadas para trabalhar com todos os tamanhos de VM que dão suporte ao número de discos da SKU. Você pode fornecer orientações sobre tamanhos recomendados, mas estes serão tratados como recomendações e não imposições:

1. VHD do sistema operacional Linux: o VHD do sistema operacional Linux em sua imagem VM deve ser criado como um VHD de formato fixo de 30 GB a 50 GB. Ele não pode ser inferior a 30 GB. Se o tamanho físico for menor do que o tamanho do VHD, o VHD deve ser esparso. VHDs do Linux maiores de 50 GB serão considerados caso a caso. Se você já tiver um VHD em um formato diferente, poderá usar o [cmdlet PowerShell de conversão de VHD para alterar o formato.][link-technet-1]
2. VHD de disco de dados: os discos de dados podem ser de até 1 TB. Os VHDs de disco de dados devem ser criados como um VHD de formato fixo. Eles também devem ser esparsos. Ao decidir sobre o tamanho do disco, lembre-se que os clientes não podem redimensionar VHDs dentro de uma imagem.

### 2\.2 Verifique se o mais recente agente Linux do Azure está instalado
Ao preparar o sistema operacional do VHD, verifique se o [Agente Linux do Azure][link-azure-vm-2] mais recente está instalado. Usando os pacotes RPM ou Deb. O pacote é frequentemente chamado walinuxagent ou WALinuxAgent, mas verifique a sua distribuição para ter certeza. O agente oferece funções-chave para a implantação da IaaS do Linux no Azure, como provisionamento de VM e recursos de rede.

Apesar do agente poder ser configurado em uma variedade de formas, recomendamos que você use uma configuração de agente genérico para maximizar a compatibilidade. Você pode instalar o agente manualmente, mas recomendamos que você use os pacotes pré-configurados da sua distribuição, se disponíveis.

Se você optar por instalar o agente manualmente usando o [repositório GitHub][link-github-waagent], primeiro copie o arquivo Waagent em /usr/sbin e execute os seguintes comandos no diretório-raiz:

    # chmod 755 /usr/sbin/waagent
    # /usr/sbin/waagent -install

O arquivo de configuração do agente é colocado em /etc/waagent.conf.

### 2\.3 Verifique se as bibliotecas necessárias estão incluídas
Além do agente Linux do Azure, as seguintes bibliotecas também devem ser incluídas:

1. O [Linux Integration Services][link-intsvc] versão 3.0 ou superior deve estar habilitado no kernel. Consulte [Requisitos do kernel do Linux](./virtual-machines-linux-create-upload-vhd-generic/#linux-kernel-requirements)
2. [Patch do Kernel](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c) para estabilidade de E/S do Azure (provavelmente não é necessário para kernel recente, mas ele deve ser verificado)
3. [Python][link-python] 2.6 ou posterior
4. Pacote Python pyasn1, se já não estiver instalado
5. [OpenSSL][link-openssl] (versão 1.0 ou superior recomendado)

### 2\.4 Configuração das partições de disco
Recomendamos que você não use o Gerenciador de Volume Lógico. Crie uma partição única raiz para o disco do sistema operacional. Não use uma partição swap no disco de dados ou no sistema operacional. Recomendamos remover uma partição swap, mesmo que não esteja montada em /etc/fstab. Se necessário, uma partição swap pode ser criada no disco de recursos local (/dev/sdb) pelo Agente Linux.

### 2\.5 Adicionar parâmetros necessários de linha de inicialização do kernel
Os seguintes parâmetros também precisam ser adicionados à linha de inicialização do kernel:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

Isso garante que o Suporte Azure pode oferecer aos clientes saída de console serial quando necessário. Ele também fornece o tempo limite adequado para montar o disco do sistema operacional usando o armazenamento na nuvem. Mesmo que suas SKU impeçam os clientes de usar o SSH diretamente na máquina virtual, a saída do console serial deve ser habilitada.

### 2\.6 Inclua o servidor SSH por padrão
Recomendamos fortemente habilitar o SSH para o cliente. Se o servidor SSH estiver habilitado, adicione o SSH keep alive à configuração sshd com a seguinte opção: **ClientAliveInterval 180**. Recomenda-se 180, mas o intervalo de 30 a 235 é aceitável. Nem todos os aplicativos desejam fornecer aos clientes acesso direto de SSH à máquina virtual. Se o SSH for explicitamente bloqueado, a opção **ClientAliveInterval** não precisará ser definida.

### 2\.7 Atenda aos requisitos de rede
A seguir estão os requisitos de rede para uma imagem VM do Linux compatível com Azure.

- Em muitos casos, é melhor desativar o NetworkManager. Uma exceção é com sistemas baseados em CentOS 7.x (e derivados), que devem manter o NetworkManager habilitado.
- A configuração de rede deve ser controlável por meio de scripts de **ifup** e **ifdown**. O agente Linux pode usar esses comandos para reiniciar o sistema de rede durante o provisionamento.
- Não deve haver nenhuma configuração de rede personalizada. O arquivo Resolv.conf deve ser excluído como etapa final. Isso geralmente é feito como parte do desprovisionamento (consulte [Guia do usuário do Azure Linux Agent](./virtual-machines-linux-agent-user-guide/)). Você também pode executar essa etapa manualmente com o comando a seguir.

        rm /etc/resolv.conf

- O dispositivo de rede precisa ser usado na inicialização e usar o DHCP.
- Não há suporte para IPv6 6 no Azure. Se essa propriedade estiver habilitada, ele não vai funcionar.

### 2\.8 Verifique se as práticas recomendadas de segurança estão em vigor
É fundamental que as SKUs no Azure Marketplace sigam as melhores práticas no que diz respeito à segurança. Elas incluem as seguintes:

- Instale todos os patches de segurança para a sua distribuição.
- Siga as diretrizes de segurança de distribuição.
- Evite criar contas padrão, que permanecem as mesmas, nas instâncias de provisionamento.
- Limpe entradas de histórico de bash.
- Inclua software de iptables (firewall), mas não habilite regras. Isto fornece uma experiência padrão perfeita para os clientes. Clientes que desejam usar um firewall de VM para configuração adicional podem configurar as regras de iptables para atender às suas necessidades específicas.

### 2\.9 Generalizar a imagem
Todas as imagens do Azure Marketplace devem ser reutilizáveis de uma forma genérica, o que exige tirar delas certas especificidades de configuração. Para conseguir isso no Linux, o VHD do sistema operacional deve ser desprovisionado.

O comando Linux para desprovisionamento é este abaixo.

        # waagent -deprovision

Este comando automaticamente:

- Remove a configuração do servidor de nome em /etc/resolv.conf.
- Remove concessões de cliente DHCP em cache.
- Reinicia o nome de host para localdomain.localdomain.

Recomendamos definir o arquivo de configuração (/etc/waagent.conf) para garantir que as seguintes ações também sejam concluídas:

- Definir Provisioning.RegenerateSshHostKeyPair como “y” no arquivo de configuração para remover todas as chaves de host SSH.
- Definir Provisioning.DeleteRootPassword como “y” no arquivo de configuração para remover a senha raiz de /etc/shadow. Para obter a documentação sobre o conteúdo do arquivo de configuração, consulte a seção "Configuração" do arquivo Leia-me na página do repositório Github Agent ([https://github.com/Azure/WALinuxAgent](https://github.com/Azure/WALinuxAgent) e role para baixo).  

Neste ponto, você concluiu a generalização da VM Linux. Desligar a VM no Portal do Azure, na linha de comando ou na VM. Quando a VM estiver desligada, continue a Etapa 3.4.

## 3\. Criar um VHD compatível com Azure (baseado no Windows)
Esta seção enfoca as etapas para criar uma SKU baseada no Windows Server para o Azure Marketplace.

### 3\.1 Verifique se você está usando os VHDs de base corretos
O VHD do sistema operacional para a imagem VM deve ser baseado em uma imagem de base aprovada do Azure que contém o Windows Server ou o SQL Server.

Para começar, crie uma VM de uma das seguintes imagens, localizadas no [portal do Microsoft Azure][link-azure-portal]\:

- Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
- SQL Server 2014 ([Enterprise][link-sql-2014-ent], [padrão][link-sql-2014-std], [Web][link-sql-2014-web])
- SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [padrão][link-sql-2012-std], [Web][link-sql-2012-web])

Esses links também podem ser encontrados no portal de publicação na página de SKU.

> [AZURE.TIP] Se você estiver usando o atual Portal de do Azure ou PowerShell, as imagens do Windows Server publicadas em 8 de setembro de 2014 e posteriormente serão aprovadas.


### 3\.2 Criar sua VM baseada em Windows
No portal do Microsoft Azure, você pode criar sua VM com base em uma imagem aprovada em apenas algumas etapas simples. O que se segue é uma visão geral do processo.

1. Na página de imagem base, selecione **Criar Máquina Virtual** para ser direcionado para o novo [portal do Microsoft Azure][link-azure-portal].

    ![desenho][img-acom-1]

2. Entre no portal com a conta da Microsoft e senha da assinatura do Azure que você deseja usar.
3. Siga os prompts para criar uma VM usando a imagem de base que você selecionou. Você precisa fornecer um nome de host (nome do computador), um nome de usuário (registrado como administrador) e a senha para a VM.

    ![desenho][img-portal-vm-create]

4. Selecione o tamanho da VM a implantar:

    a. Se você pretende desenvolver o VHD no local, o tamanho não importa. Considere o uso de uma das VMs menores.

    b. Se você pretende desenvolver a imagem no Azure, considere o uso de um dos tamanhos de VM recomendados para a imagem selecionada.

    c. Para informações de preços, consulte o seletor do **tipo de preço recomendado** exibido no portal. Ele fornecerá os três tamanhos recomendados oferecidos pelo editor. (Neste caso, o editor é a Microsoft.)

    ![desenho][img-portal-vm-size]

5. Defina as propriedades:

    a. Para implantação rápida, você pode deixar os valores padrão para as propriedades em **Configuração Opcional** e **Grupo de Recursos**.

    b. Em **Conta de Armazenamento**, você pode opcionalmente selecionar a conta de armazenamento no qual o VHD do sistema operacional será armazenado.

    c. Em **Grupo de Recursos**, você pode opcionalmente selecionar o grupo lógico no qual coloca a VM.
6. Selecione o **Local** para implantação:

    a. Se você pretende desenvolver o VHD no local, a localização não importa porque você carregará a imagem no Azure mais tarde.

    b. Se você pretende desenvolver a imagem no Azure, considere o uso de uma das regiões do Microsoft Azure sediadas nos EUA desde o início. Isso acelera o processo de cópia VHD que a Microsoft realiza em seu nome quando você enviar sua imagem para a certificação.

    ![desenho][img-portal-vm-location]

7. Clique em **Criar**. A VM começa a implantar. Em poucos minutos, você terá uma implantação bem-sucedida e poderá começar a criar a imagem para a sua SKU.

### 3\.3 Desenvolver seu VHD na nuvem
Recomendamos fortemente que você desenvolva seu VHD na nuvem usando o protocolo RDP. Você se conecta ao RDP com o nome de usuário e a senha especificada durante o provisionamento.

> [AZURE.IMPORTANT] Se você desenvolve seu VHD no local (não recomendado), consulte [Criando um imagem de máquina virtual no local](marketplace-publishing-vm-image-creation-on-premise.md). É necessário baixar o VHD se você está desenvolvendo na nuvem.


**Conecte-se via RDP usando o [portal do Microsoft Azure][link-azure-portal]**

1. Selecione **Procurar** > **VMs**.
2. A folha Máquinas Virtuais se abrirá. Verifique se a VM com a qual você deseja se conectar está em execução e selecione-a na lista de VMs implantadas.
3. Uma folha abre descrevendo a VM selecionada. Na parte superior, clique em **Conectar**.
4. Você será solicitado a inserir o nome de usuário e a senha que especificou durante o provisionamento.

**Conecte-se via RDP usando PowerShell**

Para baixar um arquivo de área de trabalho remota para um computador local, utilize o cmdlet [Get-AzureRemoteDesktopFile][link-technet-2]. Para usar esse cmdlet, você precisa saber o nome do serviço e o nome da VM. Se você criou a VM no [portal do Microsoft Azure][link-azure-portal], encontrará essas informações em propriedades da VM.

1. No portal do Microsoft Azure, selecione **Procurar** > **VMs**.
2. A folha Máquinas Virtuais se abrirá. Selecione a VM que você implantou.
3. Uma folha abre descrevendo a VM selecionada.
4. Clique em **Propriedades**.
5. A primeira parte do nome de domínio é o nome do serviço. O nome do host é o nome da VM.

    ![desenho][img-portal-vm-rdp]

6. O cmdlet para baixar o arquivo RDP da VM criada na área de trabalho local do administrador é o que se segue.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Mais informações sobre RDP podem ser encontradas no MSDN, no artigo [Conectar a uma VM do Azure com RDP ou SSH.](http://msdn.microsoft.com/library/azure/dn535788.aspx)

**Configure uma VM e crie sua SKU**

Depois que o sistema operacional do VHD é baixado, use o Hyper-V e configure uma VM para começar a criar sua SKU. Etapas detalhadas podem ser encontradas no seguinte link TechNet: [Instalar o Hyper-V e configurar uma VM.](http://technet.microsoft.com/library/hh846766.aspx)

### 3\.4 Escolher o tamanho correto do VHD
O VHD do sistema operacional do Windows em sua imagem VM deve ser criado como um VHD de formato fixo de 128 GB.

Se o tamanho físico for menor de 128 GB, o VHD deve ser esparso. As imagens básicas do Windows e SQL Server fornecidas já atenderam a esses requisitos; portanto, não altere o formato nem o tamanho do VHD obtido.

Discos de dados podem ser de até 1 TB. Ao decidir sobre o tamanho do disco, lembre-se de que os clientes não podem redimensionar VHDs dentro de uma imagem no momento da implantação. Os VHDs de disco de dados devem ser criados como um VHD de formato fixo. Eles também devem ser esparsos. Discos de dados podem estar vazios ou conter dados.


### 3\.5 Instalar as últimas correções do Windows
As imagens de base contêm as últimas correções até a data de publicação. Antes de publicar o VHD do sistema operacional que você criou, verifique se o Windows Update foi executado e se todas as atualizações de segurança Críticas e Importantes mais recentes foram instaladas.

### 3\.6 Execute as tarefas de configuração e programação adicionais, conforme necessário
Se uma configuração adicional for necessária, considere o uso de uma tarefa agendada que seja executada na inicialização para fazer as alterações finais na VM assim que ela for implantada:

- É uma boa prática que a tarefa mesma se exclua após uma execução bem-sucedida.
- Nenhuma configuração deve ter como base unidades diferentes de C ou D, uma vez que estas são as duas únicas unidades com existência sempre garantida. A unidade C é o disco do sistema operacional e a unidade D é o disco local temporário.

### 3\.7 Generalizar a imagem
Todas as imagens do Azure Marketplace devem ser reutilizáveis de uma forma genérica. Em outras palavras, o VHD do sistema operacional deve ser generalizado:

- Para o Windows, a imagem deve ser "sysprepped" e nenhuma configuração que não dê suporte ao comando **sysprep** deve ser feita.
- Você pode executar o comando a seguir a partir do diretório WINDIR%\\System32\\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Orientação sobre como aplicar o sysprep no sistema operacional é fornecida na Etapa do seguinte artigo MSDN: [Criar e carregar um VHD do Windows Server no Azure](./virtual-machines-create-upload-vhd-windows-server/).

## 4\. Implantar uma VM por meio dos seus VHDs
Depois de carregar seus VHDs (o VHD do sistema operacional generalizado e zero ou mais VHDs de disco de dados) para uma conta de armazenamento do Azure, você pode registrá-los como uma imagem VM do usuário. Em seguida, você pode testar essa imagem. Observe que, como seu VHD do sistema operacional é generalizado, você não pode implantar a VM diretamente fornecendo a URL do VHD.

Para obter mais informações sobre imagens de VM revise os posts de blog abaixo:

- [Image da VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Guia do PowerShell da Imagem da VM](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
- [Sobre imagens VM no Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### 4\.1 Criar uma imagem VM de usuário
Para criar uma imagem VM de usuário de sua SKU para iniciar a implantação de várias VMs, você precisa usar [Criar API Rest de Imagem VM](http://msdn.microsoft.com/library/azure/dn775054.aspx) para registrar VHDs como uma Imagem VM.

Você pode usar o cmdlet **Invoke-WebRequest** para criar uma Imagem VM no PowerShell. O script do PowerShell a seguir mostra como criar uma imagem VM com um disco do sistema operacional e um disco de dados. Observe que você já deve ter uma assinatura e uma sessão PowerShell configuradas.

        # Image Parameters to Specify
        $ImageName=’ENTER-YOUR-OWN-IMAGE-NAME-HERE’
        $Label='ENTER-YOUR-LABEL-HERE'
        $Description='DESCRIBE YOUR IMAGE HERE’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'https://mystorageaccount.blob.core.windows.net/vhds/myosvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $dataMediaLink='http://mystorageaccount.blob.core.windows.net/vhds/mydatavhd.vhd'
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription -Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" + Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages" $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body
        $body -Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        {
        echo "Accepted"
        } else {
        echo "Not Accepted" }
        $opId = $response.Headers.'x-ms-request-id'
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + $opId $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" -
        Certificate $certificate -Headers $headers -Method GET
        $response2.RawContent


Ao executar o script, você cria uma Imagem VM de usuário com o nome fornecido para o parâmetro ImageName, myVMImage. Ele consiste em disco de um sistema operacional e um disco de dados.

Essa API é uma operação assíncrona e responde com um 202 “Aceito”. Para ver se a Imagem VM foi criada, você tem que consultar o status da operação. A x-ms-request-id na resposta de retorno é a ID da operação. Essa ID deve ser definida no $opId abaixo.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "opId"
        $response2 = Invoke‐WebRequest ‐Uri $uri2 ‐ContentType "application/xml" ‐Certificate $certificate ‐Headers $headers ‐Method GET

Para criar uma Imagem VM usando um VHD de sistema operacional e discos de dados vazios adicionais (você não tem o VHD desse disco criado), com a opção Criar API de Imagem VM use o seguinte script:

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get‐AzureSubscription –Current ‐ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =
        "<VMImage xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema‐instance">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" +
        "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x‐ms‐version"="2014‐06‐01"}
        $response = Invoke‐WebRequest ‐Uri $uri ‐ContentType "application/xml" ‐Body $body ‐Certificate $certificate ‐Headers $headers ‐Method POST
        if ($response.StatusCode ‐ge 200 ‐and $response.StatusCode ‐lt 300)
        {
        echo "Accepted"
        }
        else
        {
        echo "Not Accepted"
        }

Ao executar o script, você cria uma Imagem VM de usuário com o nome fornecido para o parâmetro ImageName, myVMImage. Ele consiste em disco de um sistema operacional e um disco de dados.

Essa API é uma operação assíncrona e responde com um 202 “Aceito”. Para ver se a Imagem VM foi criada, você tem que consultar o status da operação. A x-ms-request-id na resposta de retorno é a ID da operação. Essa ID deve ser definida no $opId abaixo.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "$opId"
        $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" Certificate $certificate -Headers $headers -Method GET

Para criar uma Imagem VM usando um VHD de sistema operacional e discos de dados vazios adicionais (você não tem o VHD desse disco criado), com a opção Criar API de Imagem VM use o seguinte script:

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink =
        'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription –Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" + "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" + "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body $body Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        { echo "Accepted"
        } else
        { echo "Not Accepted"
        }

Ao executar o script, você cria uma Imagem VM de usuário com o nome fornecido para o parâmetro ImageName, myVMImage. É composto por um disco de sistema operacional, com base no VHD passado, e um disco de dados vazio de 32 GB.

### 4\.2 Implante uma VM usando uma imagem de VM de usuário
Para implantar uma VM de uma imagem VM de usuário, você pode usar o [portal do Azure](https://manage.windowsazure.com) ou o PowerShell atuais.

**Implante uma VM no portal do Azure atual**

1. Clique em **Novo** > **Computação** > **Máquina virtual** > **Da galeria**.

    ![desenho][img-manage-vm-new]

2. Vá para **Minhas Imagens** e selecione a imagem VM por meio da qual implantar uma VM.
  1. Preste muita atenção à imagem selecionada, uma vez que a exibição **Minhas imagens** lista imagens do sistema operacional e imagens de VM.
  2. Examinar o número de discos pode ajudar a determinar qual tipo de imagem você está implantando, já que a maioria das imagens de VM tem mais de um disco. No entanto, é ainda possível ter uma imagem VM com apenas um único disco de sistema operacional, o qual teria, então, um **Número de discos** definido como 1.

    ![desenho][img-manage-vm-select]

3. Siga o assistente de criação da VM e especifique o nome da VM, o tamanho da VM, a localização, o nome de usuário e a senha.

**Implantar uma VM a partir do PowerShell**

Para implantar uma VM grande, a partir da imagem VM generalizada recém-criada, você pode usar os cmdlets a seguir.

    $img = Get‐AzureVMImage ‐ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New‐AzureVMConfig ‐Name "VMImageVM" ‐InstanceSize "Large" ‐ImageName $img.ImageName | Add‐AzureProvisioningConfig ‐Windows ‐AdminUsername $user ‐Password $pass
    New‐AzureVM ‐ServiceName "VMImageCloudService" ‐VMs $myVM ‐Location "West US" ‐WaitForBoot

## 5\. Obter a certificação para a sua Imagem VM
A próxima etapa na preparação de sua Imagem VM para a Azure Marketplace é certificá-la.

Este processo inclui a execução de uma ferramenta de certificação especial, o carregamento dos resultados de verificação no contêiner do Azure onde o VHD reside, a adição de uma oferta, a definição da SKU e o envio da Imagem VM para certificação.

### 5\.1 Baixe e execute a Ferramenta do teste de certificação para o Certificado Azure
A ferramenta de certificação é executada em uma máquina virtual em execução, provisionada da Imagem VM do usuário, para garantir que a Imagem VM seja compatível com o Microsoft Azure. Ela verificará se as orientações e os requisitos de preparação do VHD foram cumpridos. A saída da ferramenta é um relatório de compatibilidade, o que deve ser carregado no Portal de publicação ao solicitar a certificação.

A ferramenta de certificação pode ser usada com VMs de Windows e Linux. Ele conecta às VMs baseadas em Windows via PowerShell, e a VMs Linux via SSH.Net.

1. Primeiro, baixe a ferramenta de certificação no [Site de download da Microsoft][link-msft-download]
2. Abra a ferramenta de certificação e clique no botão **Iniciar Novo Teste**.
3. Na tela **Informações do Teste**, digite um nome para a execução de teste.
4. Escolha se a VM está em Linux ou Windows. Dependendo do que você escolher, selecione as opções subsequentes.

### **Conecte a ferramenta de certificação a uma Imagem VM do Linux**

1. Selecione o modo de autenticação SSH: senha ou arquivo de chave.
2. Se estiver usando autenticação baseada em senha, digite o nome DNS (Sistema de Nome de Domínio), o nome de usuário e a senha.
3. Se estiver usando a autenticação do arquivo de chave, digite o nome DNS, o nome de usuário e a localização da chave privada.

  ![Autenticação de senha da Imagem de VM do Linux][img-cert-vm-pswd-lnx]

  ![Autenticação de chave de arquivo da Imagem de VM do Linux][img-cert-vm-key-lnx]

### **Conectar a ferramenta de certificação a uma Imagem VM baseada em Windows**

1. Insira o nome DNS totalmente qualificado da VM (por exemplo, MyVMName.Cloudapp.net).
2. Digite um nome de usuário e uma senha.

  ![Autenticação de senha de Imagem de VM do Windows][img-cert-vm-pswd-win]

Depois de selecionar as opções corretas para a sua Imagem VM baseada em Linux ou Windows, selecione **Testar Conexão** para garantir que o SSH.Net ou o PowerShell tenha uma conexão válida para fins de teste. Quando a conexão for estabelecida, selecione **Avançar** para iniciar o teste.

Quando o teste for concluído, você receberá os resultados (Êxito/Falha/Aviso) para cada elemento de teste.

![Casos de teste para a Imagem de VM do Linux][img-cert-vm-test-lnx]

![Casos de teste para a Imagem de VM do Windows][img-cert-vm-test-win]

Se algum dos testes falhar, a imagem não será certificada. Se isso ocorrer, examine os requisitos e faça as alterações necessárias.

Após o teste automatizado, você é solicitado a fornecer informações adicionais na sua Imagem de VM por meio de uma tela de questionário. Responda às perguntas e selecione **Avançar**.

![Questionário da Ferramenta de Certificação][img-cert-vm-questionnaire]

![Questionário da Ferramenta de Certificação][img-cert-vm-questionnaire-2]

Depois de concluir o questionário, você pode fornecer informações adicionais, como informações de acesso do SSH para a Imagem VM do Linux e explicações para as avaliações com falha. Você pode baixar os resultados do teste e arquivos de log para os casos de teste executados, bem como suas respostas ao questionário. Salve os resultados no mesmo contêiner como seus VHDs.

![Salvar resultados do teste de certificação][img-cert-vm-results]

### 5\.2 Obtenha o URI de assinatura de acesso compartilhado para suas imagens VM

Durante o processo de publicação, você especifica os URIs (identificadores de recurso uniformes) que governam cada um dos VHDs que você criou para sua SKU. A Microsoft precisa de acesso a esses VHDs durante o processo de certificação. Portanto, você precisa criar um URI de assinatura de acesso compartilhado para cada VHD. Esta é o URI que deve ser inserida na guia **Imagens** no portal de publicação.

O URI da assinatura de acesso compartilhado criado deve atender aos seguintes requisitos:

- Ao gerar um URI de assinatura de acesso compartilhado para seus VHDs, as permissões de leitura e lista são suficientes. Não forneça acesso para gravação ou exclusão.
- A duração de acesso deve ser de no mínimo sete dias úteis a partir da criação do URI de assinatura de acesso compartilhado.
- Para evitar erros imediatos devidos a defasagem horária, especifique um tempo de 15 minutos antes da hora atual.

Para criar um URI de assinatura de acesso compartilhado, você pode seguir as instruções fornecidas em [Assinaturas de acesso compartilhado, Parte 1: Compreendendo o Modelo SAS][link-azure-1], [Assinaturas de acesso compartilhado, Parte 2: criar e usar um SAS com o serviço Blob do Azure][link-azure-2].

Em vez de gerar uma chave de acesso compartilhado usando o código, você também pode usar ferramentas de armazenamento, tal como [Gerenciador de Armazenamento do Azure][link-azure-codeplex].

**Use o Gerenciador do Armazenamento do Azure para gerar uma chave de acesso compartilhado**

1. Baixe o [Gerenciador do Armazenamento do Azure][link-azure-codeplex] 6 e superior no CodePlex.
2. Depois de instalado, abra o aplicativo.
3. Clique em **Adicionar Conta**.

    ![desenho][img-azstg-add]

4. Especifique o nome da conta de armazenamento, chave de conta de armazenamento e domínio de pontos de extremidade de armazenamento. Não selecione **Use HTTPS**.

    ![desenho][img-azstg-setup-1]

5. O Gerenciador de Armazenamento do Azure já está conectado à sua conta de armazenamento específica. Ele é iniciado mostrando todos os contêineres na conta de armazenamento. Selecione o contêiner onde você copiou o arquivo de VHD de disco do sistema operacional (também discos de dados se eles forem aplicáveis para seu cenário).

    ![desenho][img-azstg-setup-2]

6. Depois de selecionar o contêiner de blob, o Gerenciador de Armazenamento do Azure é iniciado mostrando os arquivos dentro do contêiner. Selecione o arquivo de imagem (.vhd) que precisa ser enviado.

    ![desenho][img-azstg-setup-3]

7. Após selecionar o arquivo .vhd no contêiner, clique na guia **Segurança**.

    ![desenho][img-azstg-setup-4]

8. Na caixa de diálogo **Segurança de Contêiner de Blob**, deixe os padrões na guia **Nível de Acesso** e clique na guia **Assinaturas de Acesso Compartilhado**.

    ![desenho][img-azstg-setup-5]

9. Siga as etapas abaixo para gerar um URI de assinatura de acesso compartilhado para a imagem. vhd:

    ![desenho][img-azstg-setup-6]

    a. **Acesso permitido de**: para garantir a hora UTC, selecione o dia anterior à data atual. Por exemplo, se a data atual for 6 de outubro de 2014, selecione 5/10/2014.

    b. **Acesso permitido até**: selecione uma data que seja pelo menos sete a oito dias após a data **Acesso permitido de**.

    c. **Ações permitidas**: selecione as permissões de **Lista** e **Leitura**.

    d. Se você tiver selecionado o arquivo. vhd corretamente, ele será exibido no **Nome Blob a acessar** com extensão .vhd.

    e. Clique em **Gerar Assinatura**.

    f. No **URI de Assinatura de Acesso Compartilhado Gerado desse contêiner**, verifique o seguinte conforme destacado acima:

    - 	Verifique se a URL não começa com "https".
    - 	Verifique se o nome do arquivo de imagem e "VHD" estão no URI.
    - 	No final da assinatura, verifique se "= rl" é exibido. Isso demonstra que os acessos de leitura e lista foram fornecidos com êxito.

    g. Para garantir que o URI de assinatura de acesso compartilhado funciona, clique em **Testar no navegador**. Ele deve começar o processo de download.
10. Copie o URI da assinatura de acesso compartilhado. Este é o URI para colar no Portal de Publicação.
11. Repita essas etapas para cada VHD na SKU.

### 5\.3 Forneça informações sobre a Imagem VM e solicite a certificação no Portal de Publicação
Depois de criar sua oferta e SKU, você deve digitar os detalhes da imagem associados a essa SKU:

1. Vá para o [Portal de publicação][link-pubportal] e entre com sua conta de vendedor.
2. Selecione a guia **Imagens VM**.
3. O identificador listado no topo da página é realmente o identificador da Oferta e não o identificador da SKU.
4. Preencha as propriedades na seção **SKUs**.
5. Em **Família do sistema operacional**, selecione o tipo de sistema operacional associado ao VHD do sistema operacional.
6. Na caixa **Sistema Operacional**, descreva o sistema operacional. Considere um formato como família do sistema operacional, tipo, versão e atualizações. Um exemplo é o “Windows Server Datacenter 2014 R2”.
7. Selecione até seis tamanhos de máquina virtual recomendados. Essas são recomendações exibidas para o cliente na folha Tipo de preço no Portal do Azure quando eles decidem comprar e implantar sua imagem. **Essas são apenas recomendações. O cliente é capaz de selecionar qualquer tamanho de VM que acomode os discos especificados em sua imagem.**
8. Insira a versão. O campo de versão encapsula uma versão semântica para identificar o produto e suas atualizações.
  -	Versões devem ser no formato X.Y.Z, onde X, Y e Z são inteiros.
  -	Imagens em SKUs diferentes podem ter diferentes versões principais e secundárias.
  -	Versões em uma SKU só devem ser alterações incrementais, aumentando a versão de patch (Z de X.Y.Z).
9. Na caixa **URL do VHD de sistema operacional**, digite a assinatura de acesso compartilhado criado para o VHD do sistema operacional.
10. Se houver discos de dados associados a esta SKU, selecione o LUN (número de unidade lógica) para o qual você quer que este disco de dados seja montado na implantação.
11. Na caixa **URL do VHD LUN X**, digite a assinatura de acesso compartilhado criado para o VHD com os primeiros dados.

    ![desenho](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)

## Próxima etapa
Depois de terminar com os detalhes do SKU, avance até o [Guia de conteúdo de marketing do Azure Marketplace][link-pushstaging]. Nessa etapa do processo de publicação, você fornece o conteúdo de marketing, o preço e outras informações necessárias antes da **Etapa 3: testando sua oferta de VM em preparo**, onde pode testar vários cenários de caso de uso antes de implantar a oferta no Azure Marketplace para visibilidade pública e compra.

## Consulte também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]: media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]: media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]: media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]: media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]: media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]: media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-azstg-setup-1]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup.png
[img-azstg-setup-2]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-2.png
[img-azstg-setup-3]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-3.png
[img-azstg-setup-4]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-4.png
[img-azstg-setup-5]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-5.png
[img-azstg-setup-6]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-6.png
[img-manage-vm-new]: media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]: media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]: media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-github-waagent]: https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]: https://azurestorageexplorer.codeplex.com/
[link-azure-2]: ../storage/storage-dotnet-shared-access-signature-part-2/
[link-azure-1]: ../storage/storage-dotnet-shared-access-signature-part-1/
[link-msft-download]: http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]: https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]: https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]: https://portal.azure.com
[link-pubportal]: https://publish.windowsazure.com
[link-sql-2014-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md
[link-azure-vm-1]: ./virtual-machines-linux-create-upload-vhd/
[link-technet-1]: https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]: ./virtual-machines-linux-agent-user-guide/
[link-openssl]: https://www.openssl.org/
[link-intsvc]: http://www.microsoft.com/download/details.aspx?id=41554
[link-python]: https://www.python.org/

<!---HONumber=AcomDC_0309_2016-->