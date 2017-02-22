---
title: "Criar uma rede virtual (clássica) do Azure – Portal Clássico | Microsoft Docs"
description: "Aprenda a criar uma rede virtual (clássica) usando um arquivo netcfg no Portal Clássico do Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 69894a0b-8050-451e-8a25-c513e1bd271e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: d365f7137527d60eb509b4f431295de2218ea706
ms.openlocfilehash: 5ff91cccb711d61ed120e4a4e820d6a5dfc5e4a6


---

# <a name="create-a-virtual-network-classic-with-a-netcfg-file-using-the-azure-classic-portal"></a>Criar uma rede virtual (clássica) com um arquivo netcfg usando o Portal Clássico do Azure
[!INCLUDE [virtual-networks-create-vnet-selectors-classic-include](../../includes/virtual-networks-create-vnet-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo explica como criar uma rede virtual com um arquivo netcfg por meio do modelo de implantação clássica usando o Portal Clássico do Azure. Também é possível [criar uma rede virtual por meio do modelo de implantação clássica sem usar um arquivo netcfg](virtual-networks-create-vnet-classic-pportal.md) ou [criar uma rede virtual por meio do modelo de implantação do Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) usando o Portal do Azure.

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="how-to-create-a-vnet-with-a-network-config-file-in-the-microsoft-azure-classic-portal"></a>Como criar uma VNet com um arquivo de configuração de rede no Portal Clássico do Microsoft Azure
O Azure usa um arquivo xml para definir todas as redes virtuais disponíveis para uma assinatura. Você pode baixar esse arquivo e editá-lo para criar VNets por meio do modelo de implantação clássica, ou para modificar ou excluir VNets existentes. Este artigo explica como baixar esse arquivo, conhecido como arquivo de configuração de rede (ou netcfg), adicionar uma VNet a ele e carregar o arquivo para criar a VNet. Para saber mais sobre o arquivo de configuração de rede, confira o [Esquema de configuração de rede virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Para criar uma VNet usando um arquivo netcfg por meio do Portal Clássico do Azure, execute as etapas abaixo:

1. Em um navegador, navegue até http://manage.windowsazure.com e, se necessário, entre com sua conta do Azure.
2. Role a lista de serviços para baixo, clique em **REDES**, em **EXPORTAR**, conforme mostra a figura a seguir:

    ![Redes virtuais do Azure](./media/virtual-networks-create-vnet-classic-portal/networks.png)
3. Na caixa de diálogo **Exportar configuração de rede**, selecione a assinatura da qual você deseja exportar a configuração da rede virtual e clique no botão da marca de seleção no canto inferior direito da caixa.
4. Siga as instruções do navegador para salvar o arquivo **NetworkConfig.xml** . Não se esqueça de anotar onde está salvando o arquivo.
5. Abra o arquivo salvo na etapa 4 usando qualquer aplicativo de editor de texto ou XML e procure o elemento `<VirtualNetworkSites>` dentro do elemento `<VirtualNetworkConfiguration>`. Se já houver VNets, cada uma será listada em seu próprio elemento `<VirtualNetworkSite>`. Se o arquivo não contiver um elemento `<VirtualNetworkSites>` dentro do elemento `<VirtualNetworkConfiguration>`, crie um.
6. Se o arquivo NetworkConfig existente não tiver VNets, o arquivo Networkconfig.xml será semelhante ao exemplo a seguir após a adição da VNet descrita neste cenário:

    ```xml
    <NetworkConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
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
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    ```
7. Salve o arquivo de configuração de rede.
8. No Portal Clássico do Azure, clique em **NOVO**, **SERVIÇOS DE REDE**, **REDE VIRTUAL** e clique em **IMPORTAR CONFIGURAÇÃO** conforme mostra a figura a seguir:

    ![IMPORTAR CONFIGURAÇÃO](./media/virtual-networks-create-vnet-classic-portal/import.png)
10. Na caixa de diálogo **Importar o arquivo de configuração de rede**, clique em **PROCURAR ARQUIVO...**, navegue até a pasta na qual você salvou o arquivo na etapa 7, selecione o arquivo e clique em **Abrir**, como mostra a imagem a seguir:

    ![Página Importar arquivo de configuração de rede](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure4.png)

    No canto inferior direito da caixa, clique no botão de seta para passar para a próxima etapa.

9. Na caixa de diálogo **Construindo a rede**, observe a entrada da sua nova VNet, conforme mostra a figura abaixo:

    ![Página Construindo a rede](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure5.png)
10. Para criar a VNet, clique no botão de marca de seleção no canto inferior direito da caixa na figura anterior. Após alguns segundos, sua VNet aparecerá na lista de VNets disponíveis, conforme mostra a figura abaixo:

    ![Nova rede virtual](./media/virtual-networks-create-vnet-classic-portal/vnet-create-portal-netcfg-figure6.png)



<!--HONumber=Jan17_HO5-->


