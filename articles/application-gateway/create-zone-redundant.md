---
title: Criar um Gateway de Aplicativo do Azure com redundância de zona
description: Saiba como criar um Gateway de Aplicativo com redundância de zona que não requer um gateway separado em cada zona.
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937699"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>Criar um Gateway de Aplicativo do Azure com redundância de zona – versão prévia privada

A plataforma com redundância de zona do Gateway de Aplicativo é um novo SKU que oferece muitos aprimoramentos em relação ao SKU de Gateway de Aplicativo existente:
- **Resiliência de zona** – uma implantação do Gateway de Aplicativo agora pode abranger várias Zonas de Disponibilidade, eliminando a necessidade de provisionar e fixar instâncias do Gateway de Aplicativo separadas em cada zona com um gerenciador de tráfego. Você pode escolher uma única zona ou várias zonas nas quais as instâncias do Gateway de Aplicativo são implantadas, garantindo a resiliência para falhas de zona. O pool de back-end para aplicativos pode ser distribuído de maneira semelhante em Zonas de Disponibilidade.
- **Aprimoramentos de desempenho**
- **VIP estático** – o VIP do Gateway de Aplicativo agora permite o tipo de VIP estático exclusivamente. Isso garante que o VIP associado ao Gateway de Aplicativo não se altere após uma reinicialização.
- **Integração do cofre de chaves para certificados SSL de cliente**
- **Tempo mais rápido de implantação e atualização**

> [!NOTE]
> O Gateway de Aplicativo com redundância de zona está atualmente na versão prévia privada. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="supported-regions"></a>Regiões com suporte

No momento, há suporte para os gateways de aplicativo com redundância de zona na região Leste dos EUA 2. Mais regiões serão adicionadas em breve.

## <a name="topologies"></a>Topologias

Com a versão atual, você não precisa mais criar gateways de aplicativo fixos em uma zona para obter redundância zonal. A mesma implantação de Gateway de Aplicativo agora pode abranger várias zonas.

Pelo menos três instâncias são necessárias para garantir que eles sejam distribuídos nas três zonas. O Gateway de Aplicativo distribui as instâncias entre as zonas à medida que mais instâncias são adicionadas.

As topologias com redundância de zona anteriores pareciam com o diagrama a seguir:

![Topologia redundante antiga](media/create-zone-redundant/old-redundant.png)

A nova topologia com redundância de zona é semelhante a este diagrama:

![Nova topologia com redundância de zona](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>Implantação

### <a name="prerequisites"></a>pré-requisitos

Atualmente, o recurso de redundância de zona apenas está disponível na versão prévia privada. Você precisa enviar um email a appgwxzone@microsoft.com para ser colocado na lista de permissões. Ao receber uma confirmação, você poderá continuar para as próximas etapas. Inclua as seguintes informações em seu email para a lista de permissões:

- ID da assinatura
- Nome da região
- Contagem aproximada de gateways de aplicativo necessários

### <a name="resource-manager-template-deployment"></a>Implantação de modelo do Resource Manager

1. Verifique se a assinatura que você usa está na lista de permissões, conforme já mencionado.
2. Depois de receber uma confirmação, entre na conta do Azure e selecione a assinatura apropriada se houver mais de uma assinatura. Selecione a assinatura que estava na lista de permissões.

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. Criar um novo grupo de recursos

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. Baixe os modelos do [GitHub](https://github.com/amitsriva/CrossZonePreview) e anote a pasta em que você os salvar.
5. Crie uma nova implantação no grupo de recursos que você criou. Modifique o modelo e o arquivo de parâmetros apropriadamente antes de implantar. 

   A diagrama a seguir mostra onde você pode recuperar a ID do locatário no portal do Azure:

   ![ID do locatário no portal](media/create-zone-redundant/tenant-id.png)

Este modelo cria os seguintes recursos:

- **Identidade Atribuída ao Usuário** – é usada para permitir que as instâncias do Gateway de Aplicativo acessem o cofre de chaves e recuperem os certificados armazenados pelo usuário.
- **KeyVault** – o Key Vault em que o certificado do usuário é armazenado. Também pode ser um Key Vault já existente.
- **Segredo** – a chave privada que é armazenada no Key Vault.
- **Política de Acesso** – uma política de acesso aplicada ao Key Vault que concede permissão para usar a Identidade Atribuída ao Usuário para que as implantações do Gateway de Aplicativo possam recuperar certificados de usuário.
- **Endereço IP público** – um endereço IP reservado usado para acessar o Gateway de Aplicativo. Esse endereço IP nunca é alterado durante o ciclo de vida do Gateway de Aplicativo.
- **Grupos de Segurança de Rede** – um NSG criado automaticamente na sub-rede do Gateway de Aplicativo que abre o tráfego da porta no ouvinte configurado. Ele é criado e gerenciado explicitamente no novo SKU em comparação ao SKU anterior, no qual esse NSG era implícito.
- **Rede Virtual** – a VNET em que o Gateway de Aplicativo e os aplicativos são implantados.
- **Gateway de Aplicativo** – cria um Gateway de Aplicativo com instâncias nas zonas necessárias. Por padrão, todas as zonas (1, 2 e 3) são selecionadas. O nome do SKU é alterado para *Standard_v2*. Esse nome do SKU está sujeito a alterações. Atualmente, a configuração de dimensionamento automático tem o mínimo e o máximo definidos como o número de instâncias necessário. Quando o dimensionamento automático está habilitado, isso pode ser ajustado.

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>Implantação do PowerShell

1. Verifique se a assinatura usada está na lista de permissões, conforme já mencionado nos pré-requisitos.
2. Baixe e instale o MSI PowerShell privado do [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi).
3. Baixe o arquivo zip do PowerShell privado do local mencionado no email de confirmação de registro da versão prévia. Descompacte o arquivo na unidade e anote o local.
4. Quando a versão prévia estiver habilitada, carregue os módulos da versão prévia primeiro antes de entrar na conta:

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. Entre na conta do Azure e selecione a assinatura desejada, caso haja mais de uma assinatura. Selecione a assinatura apropriada que foi colocada na lista de permissões.
5. Execute os seguintes comandos para estabelecer constantes comuns que incluem nomes para a maioria das entidades que estão sendo criadas. 

   Modifique as entradas, conforme o necessário para suas preferências de nomenclatura.

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. Crie o grupo de recursos:

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. Crie a Identidade Atribuída ao Usuário usada para conceder acesso ao Gateway de Aplicativo para recuperar certificados do Key Vault.

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. Crie o Key Vault usado para armazenar seus certificados:

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. Carregue o certificado no Key Vault como um segredo:

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. Atribua a política de acesso ao Key Vault usando a Identidade Atribuído ao Usuário. Isso permite que as instâncias do Gateway de Aplicativo acessem o segredo do Key Vault:

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. Crie o NSG (Grupo de Segurança de Rede) para permitir o acesso à sub-rede do Gateway de Aplicativo nas portas em que os novos ouvintes são criados.

    Por exemplo, para HTTP/HTTPS nas portas padrão, o NSG permitiria o acesso de entrada como 80, 443 e 65200 a 65535 para operações de gerenciamento.

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. Crie a VNET e as sub-redes:

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. Crie um endereço IP público do tipo reservado ou estático:

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. Crie o Gateway de Aplicativo:

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. Recupere o endereço IP público do Gateway de Aplicativo criado:

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

-  Eu serei cobrado pelo Gateway de Aplicativo na versão prévia?

   Durante a versão prévia, não há nenhum encargo. Você será cobrado por outros recursos, que não sejam o Gateway de Aplicativo, como o Key Vault, as máquinas virtuais, etc.
- Em quais regiões a versão prévia está disponível?

   A versão prévia está atualmente disponível na região Leste dos EUA 2. Mais regiões serão adicionadas em breve.
- Há suporte para o portal na versão prévia?

   Não, o suporte é limitado a um módulo do PowerShell privado e a um modelo do Resource Manager durante a versão prévia privada.

- Há suporte para cargas de trabalho de produção durante a versão prévia privada?

   Não há nenhum SLA nem suporte durante a versão prévia privada. Não é recomendável colocar cargas de trabalho de produção durante as versões prévias. O suporte é limitado à interação direta com o grupo de produtos usando o alias de email para a versão prévia.

- Como faço para relatar problemas?

   A versão prévia privada pode conter bugs e ter implantações frequentes no código. Use o alias de suporte appgwxzone@microsoft.com para relatar problemas e obter assistência.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos


|Problema  |Detalhes  |
|---------|---------|---------|
|Cobrança     |Não há cobrança no momento|
|Logs de diagnóstico (não métricas)     |Os logs de desempenho e de solicitação/resposta não aparecem no momento|
|Portal/CLI/SDK     |Não há suporte para o portal, a CLI e o SDK. O portal não pode ser usado para emitir atualizações para os gateways da versão prévia.|
|A atualização por meio do modelo falha ocasionalmente     |Isso ocorre devido à condição de corrida com a política de acesso do KeyVault|Depois o que o Key Vault e a Identidade do Usuário Atribuído são criados, eles podem ser removidos do modelo e apenas as referências a identidade e o segredo são necessários no modelo.|
|Dimensionamento automático     |Não há suporte para dimensionamento automático no momento|
|WAF     |Atualmente não há suporte para WAF|
|Certificados fornecidos pelo usuário e VIPs dinâmico     |Não há suporte para eles no novo modelo. Use o Key Vault para armazenar certificados e VIPs estáticos.|
|A mesma sub-rede para a versão antiga e a versão prévia do Gateway de Aplicativo     |Uma sub-rede com um Gateway de Aplicativo existente (modelo antigo) não pode ser usada para a versão prévia privada.|
|HTTP/2, modo FIPS, WebSocket, Aplicativos Web do Azure como back-end     |Não há suporte no momento |


## <a name="support-and-feedback"></a>Suporte e comentários

Para suporte e comentários, contate appgwxzone@microsoft.com. O grupo de produtos do Gateway de Aplicativo fica feliz em receber seus comentários para gerar melhorias e fornecer diretrizes quando necessário.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros recursos do Gateway de Aplicativo:

- [O que é o Gateway de Aplicativo do Azure?](overview.md)