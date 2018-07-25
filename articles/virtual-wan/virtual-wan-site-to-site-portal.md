---
title: Usar a WAN Virtual do Azure para criar uma conexão site a site para o Azure | Microsoft Docs
description: Neste tutorial, saiba como usar a WAN Virtual do Azure para criar uma conexão VPN site a site para o Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 1b61c335dec2c641862c08fd6f752d78b2ee5866
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056657"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan-preview"></a>Tutorial: Criar uma conexão site a site usando a WAN Virtual do Azure (versão prévia)

Este tutorial mostra como usar a WAN Virtual para se conectar aos recursos do Azure em uma conexão de VPN IPsec/IKE (IKEv2). Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele. Para obter mais informações sobre a WAN Virtual, consulte a [Visão Geral de WAN Virtual](virtual-wan-about.md)

> [!NOTE]
> Se você tem vários sites, normalmente usaria um [parceiro de WAN Virtual](https://aka.ms.virtualwan) para criar esta configuração. No entanto, você poderá criar essa configuração por conta própria se estiver familiarizado com a rede e tiver domínio sobre a configuração de seu próprio dispositivo VPN.
>

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwan.png)

Neste tutorial, você aprenderá como:

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

> [!IMPORTANT]
> A WAN Virtual do Azure é atualmente uma versão prévia pública gerenciada. Para usar a WAN Virtual, você deve [registrar-se na versão prévia](#enroll).
>
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Verifique se que você tem um dispositivo VPN baseado em rota compatível que pode usar IKEv2 e conhece alguém que possa configurá-lo. Se você estiver trabalhando com um parceiro de WAN Virtual, as definições de configuração serão criadas automaticamente e você não precisará se preocupar em saber como configurar o dispositivo manualmente.
* Verifique se você possui um endereço IPv4 público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.
* Se você já tiver uma rede virtual à qual deseja se conectar, verifique se nenhuma das sub-redes da rede local se sobrepõe às redes virtuais que serão conectadas. Sua rede virtual não requer uma sub-rede de gateway e não pode ter gateways de rede virtual. Se você não tiver uma rede virtual, poderá criar uma usando as etapas neste artigo.
* Obtenha um intervalo de endereços IP para sua região de hub. O hub é uma rede virtual, e o intervalo de endereços especificado para a região de hub não pode se sobrepor a alguma rede virtual à qual você se conecta. Ele também não pode se sobrepor aos intervalos de endereços aos quais você se conecta localmente. Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede local, você precisará trabalhar em conjunto com alguém que possa lhe fornecer os detalhes.
* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="enroll"></a>1. Registrar-se na versão prévia

Antes de configurar a WAN Virtual, você precisa inscrever sua assinatura na versão prévia. Caso contrário, você não poderá trabalhar com a WAN Virtual no portal. Para se inscrever, envie um email para **azurevirtualwan@microsoft.com** com sua ID de assinatura. Após a inscrição da sua assinatura, você receberá um email.

## <a name="vnet"></a>2. Criar uma rede virtual

Se você ainda não tem uma rede virtual, crie uma rapidamente usando o PowerShell. Você também pode criar uma rede virtual usando o portal do Azure.

* Verifique se o espaço de endereço para a VNET criada não corresponde a nenhum dos intervalos de endereço de outras VNETs às quais você deseja se conectar ou a espaços de endereço de rede local. 
* Se você já tiver uma VNET, verifique se ela atende aos critérios necessários e não tem um gateway de rede virtual.

Você pode criar sua rede virtual facilmente clicando em "Experimentar" neste artigo para abrir um console do PowerShell. Ajuste os valores e copie e cole os comandos na janela de console.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Ajuste os comandos do PowerShell e crie um grupo de recursos.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Criar uma VNET

Ajuste os comandos do PowerShell para criar a VNET compatível com seu ambiente.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```

## <a name="wan"></a>3. Criar uma WAN virtual

1. Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.
2. Neste momento, você pode encontrar a WAN Virtual navegando até **Todos os serviços** e pesquisando WAN Virtual. Ou pode procurar WAN Virtual na caixa de pesquisa na parte superior do portal do Azure. Clique em **WAN Virtual** para abrir a página.
3. Clique em **Criar** para abrir a página **Criar WAN**. Se a página não estiver disponível, você ainda não foi aprovado para a versão prévia.

  ![Criar WAN](./media/virtual-wan-site-to-site-portal/createwan.png)
4. Na página Criar WAN, preencha os campos a seguir.

  * **Nome**: selecione o nome da WAN.
  * **Assinatura**: selecione a assinatura que você quer usar.
  * **Grupo de Recursos**: crie um novo ou use um existente.
  * **Local do recurso**: escolha uma localização de recursos na lista suspensa. Uma WAN é um recurso global e não pode residir em uma região específica. No entanto, você deve selecionar uma região a fim de gerenciar e localizar o recurso de WAN criado mais facilmente.
5. Clique em **Criar** para criar a WAN.

## <a name="site"></a>4. Criar um site

Crie quantos sites forem necessários para corresponder a seus locais físicos. Por exemplo, se você tiver uma filial em Nova York, uma filial em Londres e uma filial em Los Angeles, crie três sites separados. Esses sites contêm seus pontos de extremidade do dispositivo VPN local. Neste momento, você pode especificar apenas um espaço de endereço privado para o site.

1. Navegue até **Todos os recursos**.
2. Clique na WAN virtual criada.
3. Clique em **+Criar site** na parte superior da página para abrir a página **Criar site**.

  ![novo site](media/virtual-wan-site-to-site-portal/createsite.png)
4. Na página **Criar site**, preencha os seguintes campos:

  *  **Nome**: esse é o nome pelo qual você deseja se referir ao site local.
  *  **Endereço IP público:** esse é o endereço IP público do dispositivo VPN local.
  *  **Espaço de endereço privado:** espaço de endereço localizado no local. O tráfego destinado a esse espaço de endereço é roteado para o site local.
  *  **Assinatura**: verifique a assinatura.
  *  **Grupo de Recursos:** o grupo de recursos que você deseja usar.
5. Clique em **Mostrar opções avançadas** para exibir as configurações adicionais. Você pode **habilitar o BGP** (campo opcional, que permitirá essa funcionalidade em todas as conexões criadas para o site no Azure. Você também pode inserir **Informações do dispositivo** (campo opcional). Isso pode ajudar a equipe do Azure a entender melhor seu ambiente para adicionar possibilidades de otimização adicionais futuramente ou para ajudá-lo a solucionar problemas.

  ![BGP](media/virtual-wan-site-to-site-portal/sitebgp.png)
6. Clique em **Confirmar** para criar o site.
7. Repita essas etapas para cada site que você deseja criar.

## <a name="hub"></a>5. Criar um hub e conectar sites

1. Na página de sua WAN virtual, clique em **Sites**.
2. Em **Sites não associados**, você verá uma lista de sites que ainda não foram conectados a um hub.
3. Selecione os sites que você deseja associar.
4. Na lista suspensa, selecione a região a que seu hub será associado. Você deve associar o hub à região em que as redes virtuais a que você deseja se conectar residem.
5. Clique em **Confirmar**. Se você ainda não tem um hub nessa região, uma VNET de hub virtual será criada automaticamente. Nesse caso, a página **Criar hubs regionais** será exibida.
6. Na página **Criar hubs regionais**, insira o intervalo de endereços para a VNET do hub. Essa é a rede virtual que conterá seus serviços do hub. O intervalo digitado aqui deve ser um intervalo de endereços IP privados e não pode se sobrepor a nenhum dos seus espaços de endereço local ou espaços de endereço de rede virtual. Um ponto de extremidade VPN subsequente será criado na VNET do hub. (A criação automática de hub e gateway só está disponível no portal.)
7. Clique em **Criar**.

## <a name="vnet"></a>6. Conectar sua VNET a um hub

Nesta etapa, você pode criar a conexão de emparelhamento entre uma VNET e seu hub. Repita as etapas para cada VNET que você deseja se conectar.

1. Na página da WAN virtual, clique em **Conexão de rede virtual**.
2. Na página de conexão de rede virtual, clique em **+Adicionar conexão**.
3. Na página **Adicionar conexão**, preencha os seguintes campos:

    * **Nome da Conexão**: nomeie sua conexão.
    * **Hubs**: selecione o hub que você deseja associar a essa conexão.
    * **Assinatura**: verifique a assinatura.
    * **Rede virtual:** selecione a rede virtual que você deseja conectar a esse hub. A rede virtual não pode ter um gateway de rede virtual já existente.

## <a name="device"></a>7. Baixar configuração de VPN

Use a configuração do dispositivo VPN para configurar seu dispositivo VPN local.

1. Na página de sua WAN virtual, clique em **Visão Geral**.
2. Na parte superior da página de visão geral, clique em **Baixar configuração de VPN**. O Azure cria uma conta de armazenamento no grupo de recursos 'microsoft-network-[local]', em que local é o local da rede remota. Depois de aplicar a configuração a seus dispositivos VPN, você poderá excluir essa conta de armazenamento.
3. Depois que o arquivo foi criado, você pode clicar no link para baixá-lo.
4. Aplique a configuração ao dispositivo VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Entender o arquivo de configuração do dispositivo VPN

O arquivo de configuração do dispositivo contém as configurações a serem usadas ao configurar o dispositivo VPN local. Ao exibir esse arquivo, observe as seguintes informações:

* **vpnSiteConfiguration -** Essa seção indica os detalhes do dispositivo configurados como um site que se conecta à WAN Virtual. Inclui o nome e o endereço IP público do dispositivo de branch.
* **vpnSiteConnections -** Essa seção fornece informações sobre o seguinte:

    * **Espaço de endereço** dos hubs virtuais da VNet<br>Exemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espaço de endereço** das VNets conectadas ao hub<br>Exemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Endereços IP** do hub virtual vpngateway. Como o vpngateway tem cada conexão composta por 2 túneis na configuração ativa-ativa, você verá os dois endereços IP listados neste arquivo. Neste exemplo, você vê "Instance0" e "Instance1" para cada site.<br>Exemplo:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Detalhes da configuração da conexão do Vpngateway** como BGP, chave pré-compartilhada, etc. O PSK é a chave pré-compartilhada gerada automaticamente para você. Você sempre pode editar a conexão na página Visão Geral de um PSK personalizado.
  
### <a name="example-device-configuration-file"></a>Exemplo de arquivo de configuração de dispositivo

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>Configurar o dispositivo VPN

>[!NOTE]
> Se você estiver trabalhando com uma solução de parceiro de WAN Virtual, a configuração do dispositivo VPN acontece automaticamente quando o controlador do dispositivo obtém o arquivo de configuração do Azure e o aplica ao dispositivo para configurar a conexão com o Azure. Isso significa que você não precisa saber como configurar seu dispositivo VPN manualmente.
>

Se você precisar de instruções para configurar o dispositivo, poderá usar as instruções sobre a [página de scripts de configuração de dispositivo VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) com as seguintes condições:

* As instruções na página de dispositivos VPN não foram criadas para WAN Virtual, mas você pode usar os valores de WAN Virtual do arquivo de configuração para configurar seu dispositivo VPN manualmente. 
* Os scripts de configuração de dispositivo que podem ser baixados para o Gateway de VPN não funcionam para WAN Virtual, já que a configuração é diferente.
* A WAN Virtual só pode usar IKEv2, não IKEv1.
* A WAN Virtual só pode usar dispositivos VPN baseados em rota e instruções do dispositivo.

## <a name="viewwan"></a>8. Exibir a WAN virtual

1. Navegue até a WAN virtual.
2. Na página de visão geral, cada ponto do mapa representa um hub. Passe o mouse sobre qualquer ponto para exibir o resumo de integridade do hub.
3. Na seção Hubs e conexões, você pode exibir status do hub, site, região, status de conexão de VPN e bytes de entrada e saída.

## <a name="viewhealth"></a>9. Exibir a integridade do recurso

1. Navegue até a WAN.
2. Na página de sua WAN, na seção **SUPORTE + Solução de problemas**, clique em **Integridade** e exiba seu recurso.

## <a name="connectmon"></a>10. Monitorar uma conexão

Crie uma conexão para monitorar a comunicação entre uma VM do Azure e um site remoto. Para obter informações sobre como configurar um monitor de conexão, consulte [Monitorar a comunicação de rede](~/articles/network-watcher/connection-monitor.md). O campo de origem é o IP da VM no Azure e o IP de destino é o IP do site.

## <a name="cleanup"></a>11. Limpar recursos

Quando não precisar mais desses recursos, você poderá utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="feedback"></a>Comentários sobre a versão prévia

Agradecemos por seus comentários. Envie um email para <azurevirtualwan@microsoft.com> para relatar possíveis problemas ou fornecer comentários (positivos ou negativos) sobre a WAN Virtual. Inclua nome da sua empresa entre "[ ]" na linha do assunto. Também inclua sua ID de assinatura se estiver relatando um problema.

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