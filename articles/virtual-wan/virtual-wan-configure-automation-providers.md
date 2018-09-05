---
title: Configurar automação de WAN Virtual do Azure - para parceiros de WAN Virtual | Microsoft Docs
description: Este artigo ajuda os parceiros de soluções de conectividade definida pelo software a configurarem a automação de WAN Virtual do Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: bac728f286c90550107b27da76a070623577ed82
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918893"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>Configurar automação de WAN Virtual - para parceiros de WAN Virtual (versão prévia)

Este artigo ajuda a entender como configurar o ambiente de automação para conectar e configurar um dispositivo de branch (um dispositivo de VPN local do cliente ou SDWAN) para WAN Virtual do Azure. Se você é um provedor que fornece dispositivos de branch que podem acomodar conectividade de VPN sobre IPsec/IKEv2, este artigo é para você.

As soluções de conectividade definidas pelo software geralmente usam um controlador ou um centro de provisionamento de dispositivos para gerenciar os dispositivos de branch. O controlador pode usar APIs do Azure para automatizar a conectividade para WAN Virtual do Azure. Esse tipo de conexão requer um dispositivo SDWAN ou VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele.

##  <a name="access"></a>Controle de acesso

Os clientes devem ser capazes de configurar o controle de acesso apropriado para WAN Virtual na interface do usuário do dispositivo. Para isso, é recomendável usar uma Entidade de Serviço do Azure. O acesso baseado em entidade de serviço fornece a autenticação apropriada do controlador de dispositivo para carregar informações de branch. Para obter mais informações, veja [Criar entidade de serviço](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).

##  <a name="site"></a>Carregar informações de branch

Projete a experiência do usuário para carregar informações de branch (site local) para o Azure. [APIs REST](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) para **VPNSite** podem ser usadas para criar as informações do site na WAN Virtual. É possível fornecer todos os dispositivos VPN/SDWAN de branch ou selecionar personalizações de dispositivo, conforme apropriado.

##  <a name="hub"></a>Hub e serviços

Depois que o dispositivo de branch é carregado no Azure, o cliente geralmente faz seleções de região de hub e ou serviços no portal do Azure, que invoca um conjunto de operações para criar a rede virtual de hub e o ponto de extremidade de VPN dentro do hub. O gateway de VPN é um gateway escalonável que dimensiona apropriadamente com base nas necessidades de largura de banda e conexão.

## <a name="device"></a>Configuração do dispositivo

Nesta etapa, um cliente que não estivesse usando um provedor, baixaria manualmente a configuração do Azure e a aplicaria ao dispositivo VPN/SDWAN local. Como provedor, é necessário automatizar esta etapa. O controlador pode chamar a API REST **GetVpnConfiguration** para baixar a configuração do Azure, que normalmente será semelhante ao seguinte arquivo.

**Notas de configuração**

  * Se VNets do Azure estiverem anexadas ao hub virtual, elas aparecerão como ConnectedSubnets.
  * A conectividade VPN usa a configuração baseada em rota e IKEv2.

### <a name="understanding-the-device-configuration-file"></a>Entender o arquivo de configuração do dispositivo

O arquivo de configuração do dispositivo contém as configurações a serem usadas ao configurar o dispositivo VPN local. Ao exibir esse arquivo, observe as seguintes informações:

* **vpnSiteConfiguration -** Essa seção indica os detalhes do dispositivo configurados como um site que se conecta à WAN Virtual. Inclui o nome e o endereço IP público do dispositivo de branch.
* **vpnSiteConnections -** Essa seção fornece informações sobre o seguinte:

    * **Espaço de endereço** do(s) hub(s) virtual(is) da VNet.<br>Exemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espaço de endereço** das VNets conectadas ao hub.<br>Exemplo:

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

## <a name="default"></a>Políticas padrão

### <a name="initiator"></a>Iniciador

As seções a seguir listam as combinações de política com suporte quando o Azure é o iniciador do túnel.

**Fase 1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**Fase 2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE


### <a name="responder"></a>Respondente

As seções a seguir listam as combinações de políticas com suporte quando o Azure é o respondente do túnel.

**Fase 1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**Fase 2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE
* CBC_DES, SHA_1, PFS_NONE 
* AES_256, SHA_1, PFS_1
* AES_256, SHA_1, PFS_2
* AES_256, SHA_1, PFS_14
* AES_128, SHA_1, PFS_1
* AES_128, SHA_1, PFS_2
* AES_128, SHA_1, PFS_14
* CBC_3DES, SHA_1, PFS_1
* CBC_3DES, SHA_1, PFS_2
* CBC_3DES, SHA_256, PFS_2
* AES_256, SHA_256, PFS_1
* AES_256, SHA_256, PFS_2
* AES_256, SHA_256, PFS_14
* AES_256, SHA_1, PFS_24
* AES_256, SHA_256, PFS_24
* AES_128, SHA_256, PFS_NONE
* AES_128, SHA_256, PFS_1
* AES_128, SHA_256, PFS_2
* AES_128, SHA_256, PFS_14
* CBC_3DES, SHA_1, PFS_14

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>É necessário que haja correspondência de todos os aspectos entre a política de vpngateway do hub virtual e o dispositivo VPN/SDWAN local ou a configuração SD-WAN?

O dispositivo VPN/SDWAN local ou a configuração SD-WAN devem corresponder ou conter os algoritmos e parâmetros a seguir, especificados na política de IPsec/IKE do Azure. Os tempos de vida de SA são apenas especificações locais e não precisam corresponder.

* Algoritmo de criptografia IKE
* Algoritmo de integridade de IKE
* Grupo DH
* Algoritmo de criptografia IPsec
* Algoritmo de integridade de IPsec
* Grupo PFS

## <a name="feedback"></a>Comentários sobre a versão prévia

Agradecemos por seus comentários. Envie um email para <azurevirtualwan@microsoft.com> para relatar possíveis problemas ou fornecer comentários (positivos ou negativos) sobre a WAN Virtual. Inclua nome da sua empresa entre "[ ]" na linha do assunto. Também inclua sua ID de assinatura se estiver relatando um problema.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre WAN Virtual, consulte [Sobre a WAN Virtual do Azure](virtual-wan-about.md) e [Perguntas frequentes sobre a WAN Virtual do Azure](virtual-wan-faq.md).
