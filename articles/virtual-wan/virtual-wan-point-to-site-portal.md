---
title: Usar a WAN Virtual do Azure para criar uma conexão ponto a site com o Azure | Microsoft Docs
description: Neste tutorial, saiba como usar a WAN Virtual do Azure para criar uma conexão VPN ponto a site com o Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alzam
Customer intent: As someone with a networking background, I want to connect remote users to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 9fe0c7f7ae0c19833421b647449f0e4100904f5b
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226225"
---
# <a name="tutorial-create-a-point-to-site-connection-using-azure-virtual-wan-preview"></a>Tutorial: Criar uma conexão ponto a site usando a WAN Virtual do Azure (versão prévia)

Este tutorial mostra como usar a WAN Virtual para se conectar-se aos recursos no Azure em uma conexão VPN IPsec/IKE (IKEv2) ou OpenVPN. Esse tipo de conexão exige que um cliente seja configurado no computador cliente. Para saber mais sobre WAN Virtual, confira a [Visão geral de WAN Virtual](virtual-wan-about.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar uma configuração P2S
> * Criar um hub
> * Aplicar a configuração P2S a um hub
> * Conectar uma VNET a um hub
> * Fazer o download e aplicar a configuração do cliente VPN
> * Exibir a WAN virtual
> * Exibir a integridade dos recursos
> * Monitorar uma conexão

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>Registrar este recurso

Clique em **TryIt** para registrar esse recurso com facilidade usando o Azure Cloud Shell. Se preferir executar o PowerShell localmente, verifique se você tem a versão mais recente e entre usando os comandos **Connect-AzAccount** e **Select-AzSubscription**.

>[!NOTE]
>Se você não registrar esse recurso, não poderá usá-lo nem o ver no portal.
>
>

Depois de clicar em **TryIt** para abrir o Azure Cloud Shell, copie e cole os seguintes comandos:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```
 
```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Depois que o recurso aparecer como registrado, registre a assinatura no namespace Microsoft.Network.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Criar uma rede virtual

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Criar uma WAN virtual

Em um navegador, acesse o [portal do Azure (versão prévia)](https://aka.ms/azurevirtualwanpreviewfeatures) e entre com sua conta do Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Criar um hub

> [!NOTE]
> Não selecione a configuração "Incluir gateway ponto a site"nesta etapa.
>

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="site"></a>4. Criar uma configuração P2S

Uma configuração P2S define os parâmetros para conexão de clientes remotos.

1. Navegue até **Todos os recursos**.
2. Clique na WAN virtual criada.
3. Sob **Arquitetura WAN Virtual**, clique em **Configurações ponto a site**.
4. Clique em **+ Adicionar configuração ponto a site** na parte superior da página para abrir a página **Criar nova configuração ponto a site**.
5. Na página **Criar nova configuração ponto a site**, preencha os campos a seguir:

   *  **Nome da configuração**: esse é o nome atribuído à sua configuração.
   *  **Tipo de túnel**: o protocolo a ser usado para o túnel.
   *  **Pool de endereços**: é o pool de endereços IP do qual os clientes receberão o IP.
   *  **Nome do certificado raiz**: um nome descritivo para o certificado.
   *  **Dados de certificado raiz**: dados do certificado X.509 codificado em Base 64.

6. Clique em **Criar** para criar a configuração.

## <a name="hub"></a>5. Editar atribuição de hub

1. Na página da sua WAN virtual, clique em **Hubs**.
2. Selecione o hub para o qual você deseja atribuir a configuração ponto a site.
3. Clique em **"..."** e selecione **Editar hub virtual**
4. Marque **Incluir gateway de ponto a site**.
5. Na lista suspensa, selecione **Unidades de escala de gateway**.
6. Na lista suspensa, selecione a **Configuração ponto a site** que você criou.
7. Configure o **pool de endereços** dos clientes.
8. Clique em **Confirmar**. Essa operação pode levar até 30 minutos para ser concluída.

## <a name="vnet"></a>6. Conectar sua VNET a um hub

Nesta etapa, você pode criar a conexão de emparelhamento entre uma VNET e seu hub. Repita as etapas para cada VNET que você deseja se conectar.

1. Na página da WAN virtual, clique em **Conexões de rede virtual**.
2. Na página de conexão de rede virtual, clique em **+Adicionar conexão**.
3. Na página **Adicionar conexão**, preencha os seguintes campos:

    * **Nome da Conexão**: nomeie sua conexão.
    * **Hubs**: selecione o hub que você deseja associar a essa conexão.
    * **Assinatura**: verifique a assinatura.
    * **Rede virtual:** selecione a rede virtual que você deseja conectar a esse hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para adicionar a conexão.

## <a name="device"></a>7. Fazer o download do perfil de VPN

Use o perfil VPN para configurar seus clientes.

1. Na página da sua WAN virtual, clique em **Hubs**.
2. Selecione o hub para o qual você deseja baixar o perfil.
3. Clique em **"..."** e selecione **Baixar perfil**. 
4. Depois que o arquivo foi criado, você pode clicar no link para baixá-lo.
4. Use o arquivo de perfil para configurar os clientes de ponto a site.

## <a name="device"></a>8. Configurar clientes ponto a site
Use o perfil baixado para configurar os clientes de acesso remoto. O procedimento para cada sistema operacional é diferente. Siga as instruções corretas abaixo:

### <a name="microsoft-windows"></a>Microsoft Windows
#### <a name="openvpn"></a>OpenVPN

1.  Faça o download e instale o cliente OpenVPN do site oficial.
2.  Faça o download do perfil VPN para o gateway. Isso pode ser feito na guia Configurações ponto a site no portal do Azure ou em New-AzVpnClientConfiguration no PowerShell.
3.  Descompacte o perfil. Abra o arquivo de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
4.  Preencha a seção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Em um certificado formatado em PEM, basta abrir o arquivo .cer e copiar a chave base64 entre os cabeçalhos de certificado. Veja aqui como exportar um certificado para obter a chave pública codificada.
5.  Preencha a seção de chave privada com a chave privada do certificado de cliente P2S em base64. Veja aqui como extrair a chave privada.
6.  Não altere os outros campos. Use a configuração preenchida da entrada do cliente para se conectar à VPN.
7.  Copie o arquivo vpnconfig.ovpn para a pasta C:\Arquivos de Programas\OpenVPN\config.
8.  Clique com botão direito no ícone OpenVPN na bandeja do sistema e clique em conectar.

#### <a name="ikev2"></a>IKEv2

1. Selecione os arquivos de configuração de cliente VPN que correspondem à arquitetura do computador com Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'.
2. Clique duas vezes no pacote para instalá-lo. Se vir um pop-up do SmartScreen, clique em Mais informações e em Executar mesmo assim.
3. No computador cliente, navegue até Configurações de Rede e clique em VPN. A conexão VPN mostra o nome da rede virtual a que ele se conecta.
4. Antes de tentar se conectar, verifique se você instalou um certificado do cliente no computador cliente. Um certificado do cliente é necessário para autenticação ao usar o tipo de autenticação de certificado do Azure nativo. Para saber mais sobre como gerar certificados, consulte Gerar Certificados. Para saber mais sobre como instalar um certificado do cliente, consulte Instalar um certificado do cliente.

### <a name="mac-os-x"></a>Mac (OS X)
#### <a name="openvpn"></a>OpenVPN

1.  Faça o download e instale um cliente de OpenVPN, como TunnelBlik do https://tunnelblick.net/downloads.html 
2.  Faça o download do perfil VPN para o gateway. Isso pode ser feito na guia Configurações ponto a site no portal do Azure ou em New-AzVpnClientConfiguration no PowerShell.
3.  Descompacte o perfil. Abra o arquivo de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
4.  Preencha a seção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Em um certificado formatado em PEM, basta abrir o arquivo .cer e copiar a chave base64 entre os cabeçalhos de certificado. Veja aqui como exportar um certificado para obter a chave pública codificada.
5.  Preencha a seção de chave privada com a chave privada do certificado de cliente P2S em base64. Veja aqui como extrair a chave privada.
6.  Não altere os outros campos. Use a configuração preenchida da entrada do cliente para se conectar à VPN.
7.  Clique duas vezes no arquivo de perfil para criar o perfil no tunnelblik
8.  Inicie o Tunnelblik na pasta de aplicativos
9.  Clique no ícone do Tunneblik na bandeja do sistema e escolha conectar

#### <a name="ikev2"></a>IKEv2

O Azure não oferece arquivo mobileconfig para autenticação de certificado do Azure nativa. Você precisa configurar manualmente o cliente VPN IKEv2 nativo em cada Mac que se conecta ao Azure. A pasta Genérico tem todas as informações necessárias para configurá-lo. Caso não veja a pasta Genérico em seu download, talvez o IKEv2 não tenha sido selecionado como um tipo de túnel. Depois que o IKEv2 for selecionado, gere o arquivo zip novamente para recuperar a pasta Genérico. A pasta Genérico contém os seguintes arquivos:

- VpnSettings.xml, que contém configurações importantes, como o tipo de túnel e o endereço do servidor.
- VpnServerRoot.cer, que contém o certificado raiz necessário para validar o Gateway de VPN do Azure durante a instalação de conexão P2S.

## <a name="viewwan"></a>9. Exibir a WAN virtual

1. Navegue até a WAN virtual.
2. Na página de visão geral, cada ponto do mapa representa um hub. Passe o mouse sobre qualquer ponto para exibir o resumo de integridade do hub.
3. Na seção Hubs e conexões, você pode exibir status do hub, site, região, status de conexão de VPN e bytes de entrada e saída.

## <a name="viewhealth"></a>10. Exibir a integridade do recurso

1. Navegue até a WAN.
2. Na página de sua WAN, na seção **SUPORTE + Solução de problemas**, clique em **Integridade** e exiba seu recurso.

## <a name="connectmon"></a>11. Monitorar uma conexão

Crie uma conexão para monitorar a comunicação entre uma VM do Azure e um site remoto. Para obter informações sobre como configurar um monitor de conexão, consulte [Monitorar a comunicação de rede](~/articles/network-watcher/connection-monitor.md). O campo de origem é o IP da VM no Azure e o IP de destino é o IP do site.

## <a name="cleanup"></a>12. Limpar recursos

Quando esses recursos não forem mais necessários, você poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar um site
> * Criar um hub
> * Conectar um hub a um site
> * Conectar uma VNET a um hub
> * Baixar e aplicar a configuração do dispositivo VPN
> * Exibir a WAN virtual
> * Exibir a integridade dos recursos
> * Monitorar uma conexão

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
