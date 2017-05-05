## <a name="how-to-create-a-vnet-using-a-network-config-file-from-powershell"></a>Como criar uma rede virtual usando um arquivo de configuração de rede do PowerShell
O Azure usa um arquivo xml para definir todas as redes virtuais disponíveis para uma assinatura. Você pode baixar esse arquivo e editá-lo para modificar ou excluir redes virtuais existentes e criar novas. Neste documento, você aprenderá a baixar esse arquivo, conhecido como arquivo de configuração de rede (ou netcgf) e editá-lo para criar uma nova rede virtual. Confira [Esquema de configuração de rede virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) para saber mais sobre o arquivo de configuração de rede.

Para criar uma rede virtual usando o arquivo netcfg com o PowerShell, siga as etapas abaixo.

1. Se você nunca usou o Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) e siga as instruções até o fim para entrar no Azure e selecionar sua assinatura.
2. No console do Azure PowerShell, use o cmdlet **Get-AzureVnetConfig** para baixar o arquivo de configuração de rede executando o comando abaixo. 
   
        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml
   
    Saída esperada:
   
        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  
3. Abra o arquivo salvo na etapa 2 acima usando qualquer aplicativo de editor de texto ou XML e procure o elemento **<VirtualNetworkSites>** . Se já tiver redes criadas, cada rede será exibida como seu próprio elemento **<VirtualNetworkSite>** .
4. Para criar a rede virtual descrita neste cenário, adicione o seguinte XML logo abaixo do elemento **<VirtualNetworkSites>** :
   
        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>
5. Salve o arquivo de configuração de rede.
6. No console do Azure PowerShell, use o cmdlet **Set-AzureVnetConfig** para carregar o arquivo de configuração de rede executando o comando abaixo. Observe a saída no comando; você deve ver **Succeeded** em **OperationStatus**. Se esse não for o caso, verifique o arquivo xml em busca de erros.
   
       Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml
   
   Este é o resultado esperado para o comando descrito acima:
   
       OperationDescription OperationId                          OperationStatus
       -------------------- -----------                          ---------------
       Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
7. No console do Azure PowerShell, use o cmdlet **Get-AzureVnetSite** para verificar se a nova rede foi adicionada executando o comando abaixo. 
   
       Get-AzureVNetSite -VNetName TestVNet
   
   Este é o resultado esperado para o comando descrito acima:
   
       AddressSpacePrefixes : {192.168.0.0/16}
       Location             : Central US
       AffinityGroup        : 
       DnsServers           : {}
       GatewayProfile       : 
       GatewaySites         : 
       Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
       InUse                : False
       Label                : 
       Name                 : TestVNet
       State                : Created
       Subnets              : {FrontEnd, BackEnd}
       OperationDescription : Get-AzureVNetSite
       OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
       OperationStatus      : Succeeded

