---
title: "Como configurar conexões VPN no Gerenciamento de API do Azure"
description: "Saiba como configurar uma conexão VPN no Gerenciamento de API do Azure e acessar serviços Web por meio dela."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 55a2a1e1-d07e-4111-9ce3-8837ed5040d6
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 98e88b1c69de8732f1a4ae54c8ecd0b11a547f35


---
# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>Como configurar conexões VPN no Gerenciamento de API do Azure
O suporte a VPN do Gerenciamento de API permite que você conecte seu gateway de Gerenciamento de API a uma Rede Virtual do Azure (clássico). Isso permite que os clientes do Gerenciamento de API se conectem com segurança a seus serviços Web de back-end locais ou que não estão acessíveis para a Internet pública.

> [!NOTE]
> O Gerenciamento de API do Azure funciona com redes virtuais clássicas. Para obter informações sobre como criar uma VNET clássica, confira [Criar uma rede virtual (clássica) usando o Portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Para obter informações sobre como conectar redes virtuais clássicas a VNETs ARM, confira [Conectando redes virtuais clássicas a novas redes virtuais](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).
> 
> 

## <a name="enable-vpn"> </a>Habilitar conexões VPN
> A conectividade VPN só está disponível nos tipos **Premium** e **Developer**. Para mudar para ela, abra o serviço de Gerenciamento de API no [Portal Clássico do Azure][Azure Classic Portal] e, em seguida, abra a guia **Escala**. Na seção **Geral** , selecione a camada Premium e clique em Salvar.
> 
> 

Para habilitar a conectividade VPN, abra o serviço de Gerenciamento de API no [Portal Clássico do Azure][Azure Classic Portal] e mude para a guia **Configurar**. 

Na seção VPN, alterne o status de **Conexão VPN** para **Ativa**.

![Configurar a guia da instância de Gerenciamento de API][api-management-setup-vpn-configure]

Agora você verá uma lista de todas as regiões em que o serviço de Gerenciamento de API é disponibilizado.

Selecione uma VPN e sub-rede para cada região. A lista de VPNs é preenchida com base nas redes virtuais disponíveis em sua assinatura do Azure que estão instaladas na região que você está configurando.

![Selecionar VPN][api-management-setup-vpn-select]

Na parte inferior da tela, clique em **Salvar** . Você não poderá executar outras operações no serviço de Gerenciamento de API no Portal Clássico do Azure enquanto ele está sendo atualizado. O gateway do serviço permanecerá disponível e as chamadas de tempo de execução não deverão ser afetadas.

É importante observar que o endereço VIP do gateway será alterado sempre que a VPN for habilitada ou desabilitada.

## <a name="connect-vpn"> </a>Conectar a um serviço Web via VPN
Depois que o serviço de Gerenciamento de API é conectado à VPN, acessar serviços Web na Rede Virtual é o mesmo que acessar serviços públicos. Basta digitar o endereço local ou o nome do host (se um servidor DNS tiver sido configurado para a Rede Virtual do Azure) do serviço Web no campo **URL do serviço Web** ao criar uma nova API ou editar uma já existente.

![Adicionar a API da VPN][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>Portas necessárias para suporte a VPN do Gerenciamento de API
Quando uma instância do serviço Gerenciamento de API está hospedada em uma rede virtual, as portas na tabela a seguir são usadas. Se essas portas estiverem bloqueadas, é possível que o serviço não funcione corretamente. A existência de uma ou mais dessas portas bloqueadas é o problema mais comum de configuração incorreta no uso do Gerenciamento de API em uma Rede Virtual.

| Porta(s) | Direção | Protocolo de Transporte | Finalidade | Origem/Destino |
| --- | --- | --- | --- | --- |
| 80, 443 |Entrada |TCP |Comunicação do cliente com o Gerenciamento de API |INTERNET / VIRTUAL_NETWORK |
| 80.443 |Saída |TCP |Dependência do Gerenciamento de API no Armazenamento do Azure e no Barramento de Serviço do Azure |VIRTUAL_NETWORK/INTERNET |
| 1433 |Saída |TCP |Dependências do Gerenciamento de API no SQL |VIRTUAL_NETWORK/INTERNET |
| 9350, 9351, 9352, 9353, 9354 |Saída |TCP |Dependências do Gerenciamento de API no Barramento de Serviço |VIRTUAL_NETWORK/INTERNET |
| 5671 |Saída |AMQP |Dependência do Gerenciamento de API para Registrar a política de Hub de evento |VIRTUAL_NETWORK/INTERNET |
| 6381, 6382, 6383 |Entrada/Saída |UDP |Dependências do Gerenciamento de API no Cache Redis |VIRTUAL_NETWORK / VIRTUAL_NETWORK |
| 445 |Saída |TCP |Dependência do Gerenciamento de API no Compartilhamento de Arquivos do Azure para GIT |VIRTUAL_NETWORK/INTERNET |

## <a name="custom-dns"> </a>Instalação de servidor DNS personalizado
O Gerenciamento de API depende de uma série de serviços do Azure. Quando uma instância do serviço de Gerenciamento de API é hospedada em uma rede virtual em que um servidor DNS personalizado é usado, precisa ser capaz de resolver nomes de host dos serviços do Azure. Siga [estas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) orientações sobre a configuração de DNS personalizada.  

## <a name="related-content"> </a>Conteúdo relacionado
* [Criar uma rede virtual com uma conexão VPN site a site usando o Portal Clássico do Azure][Create a virtual network with a site-to-site VPN connection using the Azure Classic Portal]
* [Como usar o Inspetor de API para rastrear chamadas no Gerenciamento de API do Azure][How to use the API Inspector to trace calls in Azure API Management]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Azure Classic Portal]: https://manage.windowsazure.com/

[Create a virtual network with a site-to-site VPN connection using the Azure Classic Portal]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[How to use the API Inspector to trace calls in Azure API Management]: api-management-howto-api-inspector.md



<!--HONumber=Dec16_HO3-->


