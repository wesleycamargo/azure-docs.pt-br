---
title: 'Conectar um computador a uma rede virtual do Azure usando o Ponto a Site: PowerShell | Microsoft Docs'
description: "Conectar com segurança um computador à Rede Virtual do Azure criando uma conexão de gateway de VPN Ponto a Site."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: d340210d799f995cb10a20cf48a9245bbd3bc8d3
ms.lasthandoff: 04/27/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configurar uma conexão Ponto a Site com uma rede virtual usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure - Clássico](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Uma configuração Ponto a Site (P2S) permite que você crie uma conexão segura de um computador cliente individual com uma rede virtual. O P2S é uma conexão VPN sobre SSTP (Secure Socket Tunneling Protocol). Conexões ponto a site são úteis quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência, ou quando há apenas alguns clientes que precisam se conectar a uma rede virtual. As conexões ponto a site não exigem um dispositivo VPN ou um endereço IP voltado para o público. Estabeleça a conexão VPN no computador cliente.

Este artigo orienta você durante a criação de uma rede virtual com uma conexão Ponto a Site no modelo de implantação do Resource Manager usando o portal o PowerShell. Para saber mais sobre conexões Ponto a site, consulte as [Perguntas frequentes sobre Ponto a site](#faq) no final deste artigo.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implantação para as conexões P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os dois modelos de implantação e os métodos de implantação disponíveis para configurações de P2S. Quando houver um artigo com etapas de configuração disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Conectar um computador a um diagrama de conexão de ponto para Site da rede virtual do Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

Nesse cenário, você criará uma rede virtual com uma conexão Ponto a Site. As instruções também ajudam você a gerar certificados, que são necessários para esta configuração. Uma conexão P2S é composta pelos itens a seguir: uma rede virtual com um gateway de VPN, um arquivo .cer do certificado raiz (chave pública), um certificado de cliente e a configuração de VPN no cliente. 

Nós usamos os seguintes valores para esta configuração. Definimos as variáveis na seção [1](#declare) do artigo. Você pode usar as etapas como um passo a passo e usar os valores sem alterá-los, ou alterá-los para refletir seu ambiente. 

### <a name="example"></a>Valores de exemplo
* **Nome: VNet1**
* **Espaço de endereço: 192.168.0.0/16** e **10.254.0.0/16**<br>Neste exemplo, usamos mais de um espaço de endereço para ilustrar que esta configuração funciona com vários espaços de endereço. No entanto, vários espaços de endereço não são necessários para esta configuração.
* **Nome da sub-rede: FrontEnd**
  * **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Nome da sub-rede: BackEnd**
  * **Intervalo de endereços da sub-rede: 10.254.1.0/24**
* **Nome da sub-rede: GatewaySubnet**<br>O nome da Sub-rede *GatewaySubnet* é obrigatório para que o gateway de VPN funcione.
  * **Intervalo de endereços da GatewaySubnet: 192.168.200.0/24** 
* **Pool de endereços do cliente VPN: 172.16.201.0/24**<br>Os clientes VPN que se conectarem à rede virtual usando esta conexão Ponto a Site receberão um endereço IP do pool de endereços do cliente VPN.
* **Assinatura:** verifique se você tem mais de uma assinatura, verifique se está usando a correta.
* **Grupo de Recursos: TestRG**
* **Local: Leste dos EUA**
* **Servidor DNS: Endereço IP** do servidor DNS que você deseja usar para a resolução de nome.
* **Nome de GW: Vnet1GW**
* **Nome do IP público: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="before-beginning"></a>Antes de começar
* Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).
* Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para saber mais sobre como instalar cmdlets do PowerShell, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). 

## <a name="declare"></a>Parte 1 - Faça logon e defina as variáveis
Nesta seção, faça logon e declare os valores usados para esta configuração. Os valores declarados são usados nos scripts de exemplo. Altere os valores para refletir seu próprio ambiente. Ou, você pode usar os valores declarados e percorrer as etapas como um exercício.

1. Abra o console do PowerShell com privilégios elevados e entre na sua conta do Azure. Esse cmdlet solicita as credenciais de logon. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell.

  ```powershell
  Login-AzureRmAccount
  ```
2. Obtenha uma lista das assinaturas do Azure.

  ```powershell  
  Get-AzureRmSubscription
  ```
3. Especifique a assinatura que você quer usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
4. Declare as variáveis que você quer usar. Use o exemplo a seguir, substituindo os valores existentes pelos seus quando necessário.

  ```powershell
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $DNS = "8.8.8.8"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="ConfigureVNet"></a>Parte 2 - Configurar uma rede virtual
1. Crie um grupos de recursos.

  ```powershell
  New-AzureRmResourceGroup -Name $RG -Location $Location
  ```
2. Crie as configurações de sub-rede para a rede virtual e dê a elas os nomes *FrontEnd*, *BackEnd* e *GatewaySubnet*. Esses prefixos devem fazer parte do espaço de endereço da rede virtual declarada por você.

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
  ```
3. Crie a rede virtual. O servidor DNS especificado deve ser um servidor DNS que pode resolver os nomes dos recursos aos quais você está se conectando. Neste exemplo, usamos um endereço IP público. Use seus próprios valores.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
  ```
4. Especifique as variáveis da rede virtual que você criou.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  ```
5. Solicite um endereço IP público atribuído dinamicamente. Esse endereço IP é necessário para o gateway funcionar corretamente. Posteriormente, você conectará o gateway à configuração de IP do gateway.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```


## <a name="Certificates"></a>Parte 3 - Certificados

Os certificados são usados pelo Azure para autenticar clientes de VPN para as VPNs Ponto a Site. Depois de criar o certificado raiz, exporte os dados de certificado público (não a chave privada) como o arquivo .cer X.509 codificado em Base 64. Em seguida, carregue os dados do certificado público do certificado raiz para o Azure.

Cada computador cliente que se conecta a uma rede virtual usando ponto a site deve ter um certificado de cliente instalado. O certificado de cliente é gerado a partir do certificado raiz e instalado em cada computador cliente. Se um certificado de cliente válido não for instalado, e o cliente tentar se conectar à rede virtual, a autenticação falhará.

### <a name="cer"></a>Etapa 1 - Obter o arquivo .cer do certificado raiz

#### <a name="enterprise-certificate"></a>Certificado corporativo
 
Se você estiver usando uma solução empresarial, poderá usar a cadeia de certificados existente. Obtenha o arquivo .cer do certificado raiz que você deseja usar.

#### <a name="self-signed-root-certificate"></a>Certificado raiz autoassinado

Se você não estiver usando uma solução de certificado corporativo, será necessário criar um certificado raiz autoassinado. Para criar um certificado raiz autoassinado que contém os campos necessários para a autenticação P2S, use o PowerShell. [Criar um certificado raiz autoassinado para conexões ponto a site usando o PowerShell](vpn-gateway-certificates-point-to-site.md) explica as etapas para criar um certificado raiz autoassinado.

> [!NOTE]
> Anteriormente, o makecert era o método recomendado para criar certificados raiz autoassinados e gerar certificados cliente para conexões ponto a site. Agora você pode usar o PowerShell para criar esses certificados. Uma vantagem de usar o PowerShell é a capacidade de criar certificados SHA-2. Consulte [Criar um certificado raiz autoassinado para conexões ponto a site usando o PowerShell](vpn-gateway-certificates-point-to-site.md) para os valores necessários.
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Para exportar a chave pública para um certificado raiz autoassinado

Conexões ponto a site exigem a chave pública (.cer) carregadas no Azure. As etapas a seguir ajudarão você a exportar o arquivo .cer para seu certificado raiz autoassinado.

1. Para obter um arquivo .cer do certificado, abra **Gerenciar certificados de usuário**.
2. Localize o certificado raiz autoassinado ‘P2SRootCert’ em 'Certificados – Usuário Atual\Pessoal\Certificados' e clique nele com o botão direito do mouse. Clique em **Todas as Tarefas**e em **Exportar** para abrir o **Assistente para Exportação de Certificados**.
3. No Assistente, clique em **Avançar**. Selecione **Não exportar a chave privada** e clique em **Avançar**.
4. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base 64 (.CER).** e clique em **Avançar**. 
5. Na página **Arquivo a ser Exportado**, navegue até 'C:', crie uma subpasta chamada 'cert' e selecione-a. Nomeie o arquivo de certificado 'P2SRootCert.cer' e clique em **Salvar**. 
6. Clique em **Avançar** e em **Concluir** para exportar o certificado. A mensagem **A exportação foi bem-sucedida** é exibida. Clique em **OK** para fechar o assistente.

### <a name="generate"></a>Etapa 2 - Gerar um certificado de cliente
Você pode gerar um certificado exclusivo para cada cliente ou pode usar o mesmo certificado em vários clientes. A vantagem da geração de certificados de cliente exclusivos é a capacidade de revogar um único certificado. Caso contrário, se todos estiverem usando o mesmo certificado de cliente for necessário revogar o certificado para um cliente, você precisará gerar e instalar novos certificados para todos os clientes que usam aquele certificado para autenticação.

#### <a name="enterprise-certificate"></a>Certificado corporativo
- Se você estiver usando uma solução de certificado corporativo, gere um certificado de cliente com o formato de valor de nome comum 'name@yourdomain.com', em vez do formato 'nome do domínio\nomedeusuário'.
- Verifique se o certificado do cliente baseia-se no modelo de certificado 'Usuário' que tem 'Autenticação de cliente' como o primeiro item na lista de uso, em vez de Logon de Cartão Inteligente, etc. Você pode verificar o certificado clicando duas vezes no certificado do cliente e exibindo **Detalhes > Uso Avançado de Chave**.

#### <a name="self-signed-root-certificate"></a>Certificado raiz autoassinado 
Se você estiver usando um certificado raiz autoassinado, consulte [Gerar um certificado de cliente usando o PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) para obter as etapas para gerar um certificado de cliente compatível com as conexões ponto a site.


### <a name="exportclientcert"></a>Etapa 3 - Exportar o certificado de cliente

Se você gerar um certificado de cliente de um certificado autoassinado usando as instruções do [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert), ele será instalado automaticamente no computador que você usou para gerá-lo. Se você quiser instalar um certificado de cliente em outro computador cliente, será necessário exportá-lo.
 
1. Para exportar um certificado de cliente, abra **Gerenciar certificados de usuário**. Clique com o botão direito do mouse no certificado do cliente que você deseja exportar, clique em **todas as tarefas** e clique em **exportar** para abrir o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**, escolha **Sim, exportar a chave privada** e clique em **Avançar**.
3. Na página **Formato do Arquivo de Exportação**, deixe os padrões selecionados. Não deixe de selecionar a opção **Incluir todos os certificados no caminho de certificação, se possível** para exportar as informações de certificado raiz necessárias. Em seguida, clique em **Avançar**.
4. Na página **Segurança** , você deve proteger a chave privada. Se você optar por usar uma senha, não deixe de anotar ou lembrar da senha definida para esse certificado. Em seguida, clique em **Avançar**.
5. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Avançar**.
6. Clique em **Concluir** para exportar o certificado.

### <a name="upload"></a>Etapa 4 - Carregar o arquivo .cer do certificado raiz

Depois que o gateway tiver sido criado, você poderá carregar o arquivo .cer para um certificado raiz confiável no Azure. Você pode carregar arquivos em até 20 certificados raiz. Você não carrega a chave privada do certificado raiz no Azure. Depois que o arquivo .cer for carregado, o Azure o utilizará para autenticar clientes que se conectam à rede virtual.

1. Declare a variável para o nome do certificado, substituindo o valor pelo seu:

  ```powershell
  $P2SRootCertName = "Mycertificatename.cer"
  ```
2. Substitua o caminho do arquivo pelo seu e execute os cmdlets.

  ```powershell
  $filePathForCert = "C:\cert\Mycertificatename.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
  ```


## <a name="creategateway"></a>Parte 4 - Criar o gateway de VPN
Configurar e criar o gateway de rede virtual para sua rede virtual. O *-GatewayType* deve ser **Vpn** e o *-VpnType* deve ser **RouteBased**. Um gateway de VPN pode demorar até 45 minutos para ser concluído.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert
```


## <a name="clientconfig"></a>Parte 5 - Baixar o pacote de configuração do cliente VPN
Para se conectar a uma rede virtual usando uma VPN Ponto a Site, cada cliente deverá instalar um pacote de configuração de cliente VPN. O pacote não instala um cliente VPN. Ele configura o cliente VPN do Windows nativo com as configurações necessárias para se conectar à rede virtual. Para obter a lista de sistemas operacionais clientes com suporte, consulte as [Perguntas frequentes sobre conexões Ponto a site](#faq) ao final desse artigo.

1. Depois que o gateway tiver sido criado, você poderá gerar e baixar o pacote de configuração do cliente. Este exemplo baixa o pacote para clientes de 64 bits. Se você quiser baixar o cliente de 32 bits, substitua 'Amd64' por 'x86'. Você também pode baixar o cliente VPN usando o portal do Azure.

  ```powershell
  Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
  -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
  ```
2. Copie e cole o link que é retornado em um navegador da Web para baixar o pacote, tomando cuidado para remover o """ ao redor do link. 
3. Baixe e instale o pacote no computador cliente. Se vir um pop-up do SmartScreen, clique em **Mais informações** e em **Executar mesmo assim**. Você também pode salvar o pacote de instalação em outros computadores clientes.
4. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. A conexão VPN mostra o nome da rede virtual a que ele se conecta.



## <a name="clientcertificate"></a>Parte 6 - Instalar um certificado de cliente exportado

Se você quiser criar uma conexão P2S de um computador cliente diferente daquele usada para gerar os certificados cliente, instale um certificado de cliente. Ao instalar um certificado do cliente, você precisará da senha criada durante a exportação do certificado do cliente.

1. Localize e copie o arquivo *.pfx* no computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Deixe o **Local do Repositório** como **Usuário Atual** e clique em **Avançar**.
2. Na página **Arquivo** a importar, não faça nenhuma alteração. Clique em **Próximo**.
3. Na página **Proteção da chave privada**, insira a senha do certificado ou verifique se a entidade de segurança está correta e clique em **Avançar**.
4. Na página **Repositório de Certificados**, deixe a localização padrão e clique em **Avançar**.
5. Clique em **Concluir**. No **Aviso de Segurança** da instalação do certificado, clique em **Sim**. Clique em 'Sim' sem medo, pois você gerou o certificado. O certificado foi importado com êxito.

## <a name="connect"></a>Parte 7 - Conectar-se ao Azure
1. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele terá o mesmo nome da sua rede virtual. Clique em **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Clique em **Continuar** para usar os privilégios elevados. 
2. Na página de status **Conexão**, clique em **Conectar** para iniciar a conexão. Se for exibida uma tela de **Selecionar certificado** , verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não for, use a seta suspensa para selecionar o certificado correto e clique em **OK**.
   
    ![Cliente VPN se conecta ao Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. A conexão é estabelecida.
   
    ![Conexão estabelecida](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

Se estiver tendo problemas para se conectar, verifique os seguintes itens:

- Abra **Gerenciar certificados de usuário** e navegue até **Trusted Root Certification Authorities\Certificates**. Verifique se o certificado raiz está listado. O certificado raiz deve estar presente para que a autenticação funcione. Quando você exporta um .pfx de certificado de cliente usando o valor padrão 'Incluir todos os certificados no caminho de certificação, se possível', as informações do certificado raiz também são exportadas. Quando você instala o certificado do cliente, o certificado raiz também é instalado no computador cliente. 

- Se você estiver usando um certificado emitido com uma solução de AC corporativa e estiver com problemas na autenticação, verifique a ordem de autenticação no certificado do cliente. Você pode verificar a ordem da lista de autenticação ao clicar duas vezes no certificado do cliente e acessar **Detalhes > Uso Avançado de Chave**. Verifique se a lista mostra 'Autenticação de cliente' como o primeiro item. Caso contrário, é necessário emitir um certificado de cliente com base no modelo Usuário que tem a Autenticação de cliente como o primeiro item na lista.  


## <a name="verify"></a>Parte 8 - Verificar sua conexão
1. Para verificar se a conexão VPN está ativa, abra um prompt de comandos com privilégios elevados e execute *ipconfig/all*.
2. Exiba os resultados. Observe que o endereço IP que você recebeu está dentro do pool de endereços de cliente VPN Ponto a Site especificado em sua configuração. Os resultados são semelhantes a este exemplo:
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled


## <a name="connectVM"></a>Conectar-se a uma máquina virtual

1. Depois de se conectar à rede virtual, você poderá se conectar a uma VM em sua conexão P2S. Para se conectar à máquina virtual, você precisará ter o endereço IP privado da máquina virtual. O exemplo a seguir ajuda a obter o endereço IP privado com [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface). Os resultados retornam uma lista de máquinas virtuais e endereços IP privados correspondentes em todos os grupos de recursos. 

  ```powershell   
  $vms = get-azurermvm
  $nics = get-azurermnetworkinterface | where VirtualMachine -NE $null #skip Nics with no VM
  
  foreach($nic in $nics)
  {
    $vm = $vms | where-object -Property Id -EQ $nic.VirtualMachine.id
    $prv =  $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAddress
    $alloc =  $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAllocationMethod
    Write-Output "$($vm.Name) : $prv , $alloc"
  }
  ```
2. Use o seguinte comando para criar uma sessão de área de trabalho remota com a máquina virtual. Substitua o endereço IP pelo endereço IP privado associado à máquina virtual a que você deseja se conectar. Quando solicitado, insira as credenciais usadas ao criar a máquina virtual. 

  ```powershell   
  mstsc /v:192.168.1.4
  ```

Se você estiver tendo problemas para se conectar a uma máquina virtual em P2S, use 'ipconfig' para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador do qual está se conectando. Se o endereço IP está dentro do intervalo de endereços da VNet a que você está se conectando ou dentro do intervalo de endereços de seu VPNClientAddressPool, isso é chamado de espaço de endereço sobreposto. Quando o espaço de endereço se sobrepõe dessa forma, o tráfego de rede não alcança o Azure; ele permanece na rede local. Se seus espaços de endereço de rede não se sobrepõem e você ainda não consegue se conectar à sua VM, confira [Solucionar problemas de conexão de área de trabalho remota a uma VM](../virtual-machines/windows/troubleshoot-rdp-connection.md).

## <a name="addremovecert"></a>Adicionar ou remover um certificado raiz confiável

Você pode adicionar e remover um certificado raiz do Azure. Quando você remove um certificado confiável, os certificados de cliente gerados desde o certificado raiz não podem se conectar ao Azure por meio de Ponto a Site. Se desejar que os clientes se conectem, você precisará instalar um novo certificado de cliente gerado de um certificado confiável no Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado raiz confiável
Você pode adicionar até 20 arquivos .cer de certificado raiz ao Azure. As seguintes etapas o ajudarão a adicionar um certificado raiz:

1. Crie e prepare o novo certificado raiz para adicionar ao Azure. Exporte a chave pública como X.509 codificado em Base 64 (.CER) e abra-a com um editor de texto. Copie os valores, conforme mostra o exemplo a seguir:
   
  ![certificado](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

    > [!NOTE]
    > Ao copiar os dados do certificado, certifique-se de copiar o texto como uma linha contínua sem retornos de carro ou alimentações de linha. Talvez seja necessário modificar a exibição no editor de texto para 'Mostrar símbolo/Mostrar todos os caracteres' para ver os retornos de carro e alimentações de linha.
  >
  >

2. Especifique o nome do certificado e as informações de chave como uma variável. Substitua as informações por suas próprias, conforme mostrado no seguinte exemplo:

  ```powershell
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
3. Adicionar o novo certificado raiz. Você pode adicionar apenas um certificado por vez.

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
4. Você pode verificar se o novo certificado foi adicionado corretamente usando o exemplo a seguir:

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```


### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado raiz confiável


1. Declare as variáveis.

  ```powershell
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
2. Remova o certificado.

  ```powershell
  Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
3. Use o exemplo a seguir para verificar se o certificado foi removido com êxito.

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

## <a name="revoke"></a>Revogar um certificado de cliente
É possível revogar certificados de cliente. A lista de certificados revogados permite que você negue seletivamente conectividade ponto a site com base em certificados de cliente individuais. Isso é diferente da remoção de um certificado raiz confiável. Se você remover um arquivo .cer de certificado raiz confiável do Azure, ele revogará o acesso para todos os certificados de cliente gerados/assinados pelo certificado raiz revogado. Revogar um certificado de cliente, em vez do certificado raiz, permite que os outros certificados gerados a partir do certificado raiz continuem a ser usados para autenticação.

A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, enquanto estiver usando certificados de cliente revogados para controle de acesso refinado em usuários individuais.

### <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

1. Recupere a impressão digital do certificado de cliente. Para saber mais, confira [Como recuperar a impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços para que seja uma cadeia de caracteres contínua. Isso é declarado como uma variável na próxima etapa.
3. Declare as variáveis. Declare a impressão digital recuperada na etapa anterior.

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
4. Adicione a impressão digital à lista de certificados revogados. Você verá "Êxito" quando a impressão digital tiver sido adicionada.

  ```powershell
  Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
  -Thumbprint $RevokedThumbprint1
  ```
5. Verifique se a impressão digital foi adicionada à lista de certificados revogados.

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```
6. Após a adição da impressão digital, o certificado não poderá mais ser usado para se conectar. Os clientes que tentam se conectar usando este certificado recebem uma mensagem informando que o certificado não é mais válido.

### <a name="to-reinstate-a-client-certificate"></a>Para restabelecer um certificado de cliente
Você pode restabelecer um certificado de cliente removendo a impressão digital da lista de certificados de cliente revogados.

1. Declare as variáveis. Declare a impressão digital correta para o certificado que você deseja restabelecer.

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
2. Remova a impressão digital do certificado da lista de revogação de certificado.

  ```powershell
  Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
  ```
3. Verifique se a impressão digital foi removida da lista revogada.

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```

## <a name="faq"></a>Perguntas frequentes sobre Ponto a site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Próximas etapas
Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Para saber mais sobre redes e máquinas virtuais, consulte [Visão geral de rede do Azure e VM Linux](../virtual-machines/linux/azure-vm-network-overview.md).

