---
title: "Criar uma máquina virtual do Azure com Rede Acelerada | Microsoft Docs"
description: "Saiba como criar uma máquina virtual com Rede Acelerada."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 43663ed8becfa69c06699709a18623652df28ed6
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>Criar uma máquina virtual com Rede Acelerada

Neste tutorial, você aprenderá a criar uma VM (máquina Virtual) do Azure com Rede Acelerada. Rede acelerada permite SR-IOV (virtualização de E/S de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de dados, reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes nos tipos de VM com suporte. A figura abaixo mostra a comunicação entre duas VMs (máquinas virtuais) com e sem rede acelerada:

![Comparação](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Sem rede acelerada, todo o tráfego de rede que entra e sai da VM deve cruzar o host e o comutador virtual. O comutador virtual fornece toda a imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços virtualizados de rede para tráfego de rede. Para saber mais sobre comutadores virtuais, leia o artigo [Virtualização de rede Hyper-V e comutador virtual](https://technet.microsoft.com/library/jj945275.aspx).

Com Rede Acelerada, o tráfego de rede chega à NIC (interface de rede) da VM e então é encaminhado para a VM. Todas as políticas de rede que o comutador virtual aplica sem rede acelerada são descarregadas e aplicadas em hardware. Aplicar a política de hardware permite que a NIC encaminhe tráfego de rede diretamente à VM, ignorando o host e o comutador virtual, mantendo toda a política que aplicou no host.

Os benefícios da rede acelerada aplicam-se somente à VM em que ela está habilitada. Para obter melhores resultados, é ideal habilitar esse recurso em pelo menos duas VMs conectadas à mesma VNet (rede virtual) do Azure. Ao se comunicar entre VNets ou fazer conexão local, esse recurso tem impacto mínimo sobre a latência geral.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>Benefícios
* **Latência menor/mais pps (pacotes por segundo):** remover o comutador virtual do caminho de dados elimina o tempo que os pacotes gastam no host para processamento da política e aumenta o número de pacotes que podem ser processados dentro da VM.
* **Tremulação reduzida:** processamento de comutador virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade ao entregar pacotes diretamente à VM, removendo a comunicação do host para a VM e todas as interrupções e mudanças de contexto de software.
* **Menor utilização da CPU:** ignorar o comutador virtual no host resulta em menor utilização da CPU para processar o tráfego de rede.

## <a name="Limitations"></a>Limitações
Existem as seguintes limitações ao usar essa funcionalidade:

* **Criação de interface de rede:** rede acelerada só pode ser habilitada para uma NIC nova. Não pode ser habilitada para uma NIC existente.
* **Criação da VM:** uma NIC com rede acelerada habilitada somente poderá ser conectada a uma VM quando a VM for criada. A NIC não pode ser anexada a uma VM existente.
* **Regiões:** VMs do Windows com rede acelerada são oferecidas na maioria das regiões do Azure. VMs do Linux com rede acelerada são oferecidas apenas em duas regiões: Centro-Sul dos EUA e Oeste dos EUA 2. As regiões em que essa capacidade está disponível serão expandidas no futuro.
* **Sistemas operacionais com suporte:** Windows: Microsoft Windows Server 2012 R2 Datacenter e Windows Server 2016. Linux: LTS do Ubuntu Server 16.04 com kernel 4.4.0-77 ou superior. Outras distribuições serão adicionadas em breve.
* **Tamanho da VM:** tamanhos de instância com computação otimizada e de finalidade geral com oito ou mais núcleos. Para obter mais informações, consulte os artigos de tamanhos de VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). O conjunto de tamanhos de instância VM com suporte será expandido no futuro.

Alterações a essas limitações serão anunciadas na página [Atualizações de Rede Virtual do Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview).

## <a name="create-a-windows-vm"></a>Criar uma VM do Windows
Você pode usar o portal do Azure ou o Azure [PowerShell](#windows-powershell) para criar a VM.

### <a name="windows-portal"></a>Portal

1. Em um navegador da Internet, abra o [portal](https://portal.azure.com) do Azure e entre com a sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. No portal, clique em **+ Novo** > **Computação** > **Windows Server Datacenter 2016**. 
3. Na folha do **Windows Server Datacenter 2016** exibida, deixe *Resource Manager* selecionado em **Selecionar um modelo de implantação** e clique em **Criar**.
4. Na folha **Fundamentos** que aparece, digite os seguintes valores, deixe as opções padrão restantes ou selecione ou insira seus próprios valores e clique no botão **OK**:

    |Configuração|Valor|
    |---|---|
    |Nome|MyVm|
    |Grupo de recursos|Deixe **Criar novo** selecionado e digite *MyResourceGroup*|
    |Local|Oeste dos EUA 2|

    Se você for novo no Azure, saiba mais sobre [Grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [locais](https://azure.microsoft.com/regions) (que também são chamados de regiões).
5. Na folha **Escolher um tamanho** que aparece, digite *8* na caixa **Mínimo de núcleos** e, em seguida, clique em **Exibir tudo**.
6. Clique em **Padrão DS4_V2** e, em seguida, clique no botão **Selecionar**.
7. Na folha **Configurações** exibida, deixe todas as configurações como estão, porém, clique em **Habilitado** em **Rede acelerada** e depois clique no botão **OK**. **Observação:** se, nas etapas anteriores, você tiver selecionado valores de tamanho, sistema operacional ou local da VM que não estejam listados na seção [Limitações](#Limitations) deste artigo, **Rede acelerada** não estará visível.
8. Na folha **Resumo** que aparece, clique no botão **OK**. O Azure começa a criar a VM. A criação da VM leva alguns minutos.
9. Para instalar o driver de rede acelerado para Windows, conclua as etapas na seção [Configurar o Windows](#configure-windows) deste artigo.

### <a name="windows-powershell"></a>PowerShell
1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do Azure PowerShell. Se você for novo no Azure PowerShell, leia o artigo [Visão geral do Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell clicando no botão Iniciar do Windows, digitando **powershell** e, em seguida, clicando em **PowerShell** nos resultados da pesquisa.
3. Na janela do PowerShell, digite o comando `login-azurermaccount` para entrar na sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. No navegador, copie o script a seguir:
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. Na janela do PowerShell, clique com o botão direito do mouse para colar o script e começar a executá-lo. Você será solicitado a informar um nome de usuário e senha. Use essas credenciais para fazer logon na VM ao conectar-se a ela na próxima etapa. Se o script falhar e você tiver alterado os valores no script antes de executá-lo, verifique se os valores usados para tamanho, sistema operacional e local da VM estão listados na seção [Limitações](#Limitations) deste artigo.
6. Para instalar o driver de rede acelerado para Windows, conclua as etapas na seção [Configurar o Windows](#configure-windows) deste artigo.

### <a name="configure-windows"></a>Configurar Windows
Depois de criar a VM no Azure, você deve instalar o driver de rede acelerada para Windows. Antes de concluir as etapas a seguir, você deve ter criado uma VM do Windows com rede acelerada usando as etapas do [portal](#windows-portal) ou do [PowerShell](#windows-powershell) neste artigo. 

1. Em um navegador da Internet, abra o [portal](https://portal.azure.com) do Azure e entre com a sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *MyVm*. Quando **MyVm** aparecer nos resultados da pesquisa, clique nela.
3. Na folha **MyVm** que é exibida, clique no botão **Conectar** no canto superior esquerdo da folha. **Observação:** se **Criando** estiver visível sob o botão **Conectar**, o Azure ainda não terminou de criar a VM. Clique em **Conectar** somente depois que **Criando** não for mais exibido sob o botão **Conectar**.
4. Permite ao navegador baixar o arquivo **MyVm.rdp**.  Após o download, clique no arquivo para abri-lo. 
5. Clique no botão **Conectar** na caixa **Conexão de área de trabalho remota** exibida, informando que o editor da conexão remota não pode ser identificado.
6. Na caixa **Segurança do Windows** que aparece, clique em **Mais opções** e, em seguida, clique em **Usar uma conta diferente**. Digite o nome de usuário e a senha digitados na etapa 4 e, em seguida, clique no botão **OK**.
7. Clique no botão **Sim** na caixa de Conexão de Área de Trabalho Remota exibida, informando-o de que não é possível verificar a identidade do computador remoto.
8. Na VM MyVm, clique no botão Iniciar do Windows e clique em **Internet Explorer**.
9. Na caixa **Internet Explorer 11** que aparece, clique em **Usar as configurações de segurança, privacidade e compatibilidade recomendadas** e, em seguida, clique em **OK**.
10. Na barra de endereços do Internet Explorer, digite https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84 e, em seguida, a tecla Enter.
11. Na caixa **Alerta de Segurança** que aparece, clique em **OK**.
12. Na caixa do **Internet Explorer** que aparece, clique em **Adicionar**, clique no botão **Adicionar** na caixa **Sites confiáveis** e, em seguida, clique no botão **Fechar**. Conclua estas etapas para todas as caixas subsequentes que aparecerem.
13. Para baixar o arquivo, clique nele.
14. Quando a caixa **Licença, Termos de Uso** aparecer, clique em **Eu concordo**.
15. Permita que o Internet Explorer salve o arquivo clicando no botão **Salvar** na caixa que aparece na parte inferior da tela e clique no botão **Abrir pasta**.
16. Para instalar o driver de rede acelerada, clique duas vezes no arquivo. Durante o assistente de instalação, aceite todos os padrões e clique no botão **Sim** no final do assistente para reiniciar a VM.
17. Depois que a VM for reiniciada, conclua as etapas 9 a 12 novamente para conectar-se à VM.
18. Clique com o botão direito do mouse em Iniciar do Windows e clique em **Gerenciador de Dispositivos**. Expanda o nó **Adaptadores de rede**. Verifique se o **Adaptador de Ethernet de Função Virtual Mellanox ConnectX-3** aparece, como mostrado na figura a seguir:
   
    ![Gerenciador de Dispositivos](./media/virtual-network-create-vm-accelerated-networking/image2.png)

## <a name="create-a-linux-vm"></a>Criar uma VM do Linux
Você pode usar o portal do Azure ou o Azure [PowerShell](#linux-powershell) para criar a VM.

### <a name="linux-portal"></a>Portal
1. Registre-se na rede acelerada para a versão prévia do Linux concluindo as etapas 1 a 5 da seção [Criar uma VM do Linux – PowerShell](#linux-powershell) deste artigo.  Você não pode se registrar para a versão prévia no portal.
2. Conclua as etapas 1 a 8 na seção [Criar uma VM do Windows – portal](#windows-portal) deste artigo. Na etapa 2, clique em **Ubuntu Server 16.04 LTS**, em vez de **Windows Server 2016 Datacenter**. Para este tutorial, opte por usar uma senha, em vez de uma chave SSH, embora você pode usar qualquer uma para implantações de produção. Caso **Rede acelerada** não apareça quando você conclui a etapa 7 da seção [Criar uma VM do Windows – portal](#windows-portal) deste artigo, é provável que isso seja devido a um dos seguintes motivos:
    - Você ainda não está registrado para a versão prévia. Verifique se o seu estado do registro é **Registrado**, conforme explicado na etapa 4 da seção [Criar uma VM do Linux – Powershell](#linux-powershell) deste artigo. **Observação:** se você tiver participado da rede Acelerada para a versão prévia de VMs do Windows (não é mais necessário registrar-se para usar a rede Acelerada para VMs do Windows), você não estará automaticamente registrado para a rede Acelerada para a versão prévia de VMs do Linux. Você deverá registrar-se na rede acelerada para a versão prévia de VMs do Linux para participar delas.
    - Você não selecionou um tamanho de VM, sistema operacional nem local listado na seção [Limitações](#simitations) deste artigo.
3. Para instalar o driver de rede acelerado para Linux, conclua as etapas na seção [Configurar o Linux](#configure-linux) deste artigo.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Se você criar VMs do Linux com rede acelerada em uma assinatura e, em seguida, tentar criar uma VM do Windows com rede acelerada na mesma assinatura, a criação da VM do Windows poderá falhar. Durante essa versão prévia, é recomendável testar VMs do Linux e do Windows com rede acelerada em assinaturas diferentes.
>

1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do Azure PowerShell. Se você for novo no Azure PowerShell, leia o artigo [Visão geral do Azure PowerShell](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell clicando no botão Iniciar do Windows, digitando **powershell** e, em seguida, clicando em **PowerShell** nos resultados da pesquisa.
3. Na janela do PowerShell, digite o comando `login-azurermaccount` para entrar na sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Registre-se para rede acelerada para versão prévia do Azure concluindo as seguintes etapas:
    - Digite os seguintes comandos:

        ```powershell
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
        ```
    - Envie um email para [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura do Azure e o uso pretendido. 
    - Digite o seguinte comando para confirmar que você está registrado para a versão prévia:
    
        `Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network`

        Não continue com a etapa 5 até **Registrado** aparecer na saída após a execução do comando anterior. A saída deve se parecer com a saída a seguir antes de continuar:
    
        ```
        FeatureName                       ProviderName      RegistrationState
        -----------                       ------------      -----------------
        AllowAcceleratedNetworkingFeature Microsoft.Network Registered
        ```
      >[!NOTE]
      >Se você tiver participado da Rede acelerada para versão prévia de VMs do Windows (não é mais necessário registrar-se para usar Rede Acelerada para VMs do Windows), você não estará automaticamente registrado para a Rede acelerada para a versão prévia de VMs do Linux. Você deverá registrar-se na rede acelerada para a versão prévia de VMs do Linux para participar delas.
      >
5. No navegador, copie o script a seguir:
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
6. Na janela do PowerShell, clique com o botão direito do mouse para colar o script e começar a executá-lo. Você será solicitado a informar um nome de usuário e senha. Use essas credenciais para fazer logon na VM ao conectar-se a ela na próxima etapa. Se o script falhar, verifique:
    - Se você está registrado para a versão prévia, conforme explicado na etapa 4
    - Se você tiver alterado o tamanho, o tipo de sistema operacional ou os valores de local da VM no script antes de executá-lo, se os valores estão listados na seção [Limitações](#Limitations) deste artigo.
7. Para instalar o driver de rede acelerado para Linux, conclua as etapas na seção [Configurar o Linux](#configure-linux) deste artigo.

### <a name="configure-linux"></a>Configurar Linux

Depois de criar a VM no Azure, você deve instalar o driver de rede acelerada para Linux. Antes de concluir as etapas a seguir, você deve ter criado uma VM do Linux com rede acelerada usando as etapas do [portal](#linux-portal) ou do [PowerShell](#linux-powershell) neste artigo. 

1. Em um navegador da Internet, abra o [portal](https://portal.azure.com) do Azure e entre com a sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Na parte superior do portal, à direita da barra *Pesquisar recursos*, clique no ícone **>_** para iniciar um cloud shell de Busca (Versão Prévia). O painel do cloud shell de Busca é exibido na parte inferior do portal e, depois de alguns segundos, apresenta um prompt **username@Azure:~$**. Embora você possa usar SSH para a VM do seu computador, em vez do cloud shell, as instruções neste tutorial presumem que você esteja usando o cloud shell.
3. Na parte superior do portal, na caixa que contém o texto *Pesquisar recursos*, digite *MyVm*. Quando **MyVm** aparecer nos resultados da pesquisa, clique nela.
4. Na folha **MyVm** que é exibida, clique no botão **Conectar** no canto superior esquerdo da folha. **Observação:** se **Criando** estiver visível sob o botão **Conectar**, o Azure ainda não terminou de criar a VM. Clique em **Conectar** somente depois que **Criando** não for mais exibido sob o botão **Conectar**.
5. O Azure abre uma caixa pedindo para você inserir o `ssh adminuser@<ipaddress>`. Digite esse comando no cloud shell (ou copie-o na caixa exibida na etapa 4 e cole-o no cloud shell e, em seguida, pressione Enter.
6. Insira **Sim** para a pergunta consultando se você deseja continuar a conexão e, em seguida, pressione Enter.
7. Digite a senha inserida ao criar a VM. Depois de fazer logon com êxito na VM, você verá um prompt adminuser@MyVm:~$. Agora você está conectado à VM por meio da sessão de cloud shell. **Observação:** as sessões de cloud shell atingem o tempo limite depois de 10 minutos de inatividade.
8. No prompt, digite `uname -r` e confirme que a saída corresponde à seguinte versão: "4.4.0-77-generic".
9.    Crie um vínculo entre a vNIC de rede padrão e a vNIC de rede acelerada executando os comandos a seguir. O tráfego de rede usa a vNIC de rede acelerada de maior desempenho, enquanto o vínculo garante que o tráfego de rede não seja interrompido em determinadas alterações de configuração. 
    - `wget https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git/plain/tools/hv/bondvf.sh`
    - `chmod +x ./bondvf.sh`
    - `sudo ./bondvf.sh`
    - `sudo mv ~/bondvf.sh /etc/init.d`
    - `sudo update-rc.d bondvf.sh defaults` Observação: se você receber um erro dizendo *insserv: aviso: o script 'bondvf.sh' não tem marcas e substituições LSB*, poderá ignorá-lo.
    - O `sudo nano /etc/network/interfaces.d/50-cloud-init.cfg` abre o nanoeditor GNU para editar o arquivo.
    - No editor, comente as linhas *auto etho0* e *iface eth0 inet dhcp* adicionando *#* ao início de cada linha. Depois de adicionar *#* a cada linha, as linhas se parecerão com o exemplo a seguir:
        - #<a name="auto-eth0"></a>auto eth0
        - #<a name="iface-eth0-inet-dhcp"></a>iface eth0 inet dhcp
10. Mantenha as teclas **Ctrl+X** pressionadas, digite **Y** e, em seguida, pressione a tecla **Enter** para salvar o arquivo.
11. Reinicie a VM inserindo o comando `sudo shutdown -r now`.
12. Depois que a VM for reiniciada, reconecte-a ao concluir as etapas 5 a 7 novamente.
13.    Execute o comando `ifconfig` e confirme se bond0 surgiu e se a interface está aparecendo como UP. **Observação:** o aplicativo usando a rede acelerada deve se comunicar por meio da interface *bond0*, não *eth0*.  O nome da interface pode mudar antes de a rede acelerada atingir disponibilidade geral.

