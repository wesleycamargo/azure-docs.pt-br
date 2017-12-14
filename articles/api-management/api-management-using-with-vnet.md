---
title: Como usar o Gerenciamento de API do Azure com redes virtuais
description: "Saiba como configurar uma conexão a uma rede virtual no Gerenciamento de API do Azure e acessar serviços Web por meio dela."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: b37c9d9de171e69e38a4bae58f9fbac99eae2091
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Como usar o Gerenciamento de API do Azure com redes virtuais
As redes virtuais do Azure (VNETs) permitem que você coloque qualquer um dos recursos do Azure em uma rede não roteável para a Internet com acesso controlado. Essas redes podem ser conectadas às redes locais usando várias tecnologias VPN. Para saber mais sobre redes virtuais do Azure, confira [Visão geral da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

O Gerenciamento de API do Azure pode ser implantado na VNET (rede virtual) para que possa acessar serviços de back-end na rede. O portal do desenvolvedor e o gateway de API podem ser configurados para serem acessados pela Internet ou somente na rede virtual.

> [!NOTE]
> O Gerenciamento de API do Azure oferece suporte às VNets clássicas e do Azure Resource Manager.
>

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

+ Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância do APIM. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ A conectividade VNET está disponível somente nas camadas Premium e Developer. Alterne para uma dessas camadas seguindo as instruções no tópico [atualizar e colocar em escala](upgrade-and-scale.md#upgrade-and-scale).

## <a name="enable-vpn"> </a>Habilitar conexão de VNET

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Habilitar a conectividade VNET usando o portal do Azure

1. Navegue para sua instância do APIM no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Rede Virtual**.
3. Configure a instância de Gerenciamento de API a ser implantada dentro de uma Rede virtual.

    ![Menu de rede virtual de Gerenciamento de API][api-management-using-vnet-menu]
4. Selecione o tipo de acesso desejado:
    
    * **Externo**: o portal de desenvolvedor e o gateway de Gerenciamento de API podem ser acessados pela Internet pública por meio de um balanceador de carga externo. O gateway pode acessar recursos na rede virtual.
    
    ![Emparelhamento público][api-management-vnet-public]
    
    * **Interno**: o portal de desenvolvedor e o gateway de Gerenciamento de API só podem ser acessados de dentro da rede virtual por meio de um balanceador de carga interno. O gateway pode acessar recursos na rede virtual.
    
    ![Emparelhamento privado][api-management-vnet-private]`

    Agora você verá uma lista de todas as regiões em que o serviço de Gerenciamento de API é disponibilizado. Selecione uma VNET e uma sub-rede para cada região. A lista é preenchida com as redes virtuais clássicas e do Resource Manager disponíveis em sua assinatura do Azure que estão instaladas na região que você está configurando.
    
    > [!NOTE]
    > O **Ponto de Extremidade de Serviço**, no diagrama acima, inclui Gateway/Proxy, Portal do Publicador, Portal do Desenvolvedor, GIT e o Ponto de Extremidade de Gerenciamento Direto.
    > O **Ponto de Extremidade de Gerenciamento**, no diagrama acima, é o ponto de extremidade hospedado no serviço para gerenciar a configuração por meio do Portal do Azure e do Powershell.
    > Além disso, observe que, mesmo que o diagrama mostre os endereços IP de seus vários pontos de extremidade, o serviço de Gerenciamento de API responde **somente** em seus nomes de host configurados.
    
    > [!IMPORTANT]
    > Ao implantar uma instância do Gerenciamento de API do Azure a uma VNET do Resource Manager, o serviço deve estar em uma sub-rede dedicada que não contém outros recursos, exceto as instâncias do Gerenciamento de API do Azure. Se for feita uma tentativa de implantar uma instância do Gerenciamento de API do Azure em uma VNET do Resource Manager que contém outros recursos, a implantação falhará.
    >

    ![Selecionar VPN][api-management-setup-vpn-select]

5. Clique em **Salvar** na parte superior da tela.

> [!NOTE]
> O endereço VIP da instância do Gerenciamento de API mudará sempre que a VNET for habilitada ou desabilitada.  
> O endereço VIP também será alterado quando o Gerenciamento de API for movido de **Externo** para **Interno** ou vice-versa
>

> [!IMPORTANT]
> Se você remover o Gerenciamento de API de uma rede virtual ou alterar uma em que ele esteja implantado, a rede virtual usada anteriormente poderá permanecer bloqueada por até 4 horas. Durante esse período, não será possível excluir a rede virtual nem implantar um novo recurso nela.

## <a name="enable-vnet-powershell"> </a>Habilitar a conexão de VNET usando cmdlets do PowerShell
Você também pode habilitar a conectividade de VNET usando os cmdlets do PowerShell

* **Criar um serviço de Gerenciamento de API dentro de uma VNET**: use o cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) para criar um serviço de Gerenciamento de API do Azure dentro de uma VNET.

* **Implantar um serviço de Gerenciamento de API existente dentro de uma VNET**: use o cmdlet [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) para mover um serviço de Gerenciamento de API do Azure existente dentro de uma rede virtual.

## <a name="connect-vnet"> </a>Conectar-se a um serviço Web hospedado em uma rede virtual
Depois que o serviço de Gerenciamento de API for conectado à VNET, acessar os serviços de back-end nela será o mesmo que acessar serviços públicos. Basta digitar o endereço IP local ou o nome do host (se um servidor DNS estiver configurado para a VNET) do serviço Web no campo **URL do serviço Web** ao criar uma nova API ou editar uma já existente.

![Adicionar a API da VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Problemas comuns de configuração de rede
Veja a seguir uma lista de problemas comuns de erro de configuração que podem ocorrer ao implantar o serviço de Gerenciamento de API em uma rede virtual.

* **Configuração personalizada de servidor DNS**: o serviço Gerenciamento de API depende de vários serviços do Azure. Quando o Gerenciamento de API estiver hospedado em uma VNET com um servidor DNS personalizado, será necessário resolver os nomes de host desses serviços do Azure. Siga [estas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) orientações sobre a configuração de DNS personalizada. Consulte a tabela de portas abaixo e outros requisitos de rede para ter uma referência.

> [!IMPORTANT]
> Se estiver usando um ou mais servidores DNS personalizados para a VNET, é aconselhável que você os configure **antes** de implantar um serviço Gerenciamento de API nele. Caso contrário, você precisará atualizar o serviço de Gerenciamento de API sempre que você alterar os servidores DNS executando a [operação Aplicar Configuração de Rede](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Portas necessárias para o Gerenciamento de API**: o tráfego de entrada e saída para a sub-rede na qual o Gerenciamento de API foi implantado pode ser controlado usando o [Grupo de Segurança de Rede][Network Security Group]. Se qualquer uma dessas portas estiver indisponível, o Gerenciamento de API poderá não funcionar corretamente e poderá se tornar inacessível. A existência de uma ou mais dessas portas bloqueadas é outro problema de configuração incorreta no uso do Gerenciamento de API em uma VNET.

Quando uma instância do serviço Gerenciamento de API está hospedada em uma rede virtual, as portas na tabela a seguir são usadas.

| Porta(s) de Origem/Destino | Direção | Protocolo de transporte | Origem/Destino | Finalidade (*) | Tipo de Rede Virtual |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |Entrada |TCP |INTERNET / VIRTUAL_NETWORK|Comunicação do cliente com o Gerenciamento de API|Externo |
| * / 3443 |Entrada |TCP |INTERNET / VIRTUAL_NETWORK|Ponto de extremidade de gerenciamento para o Portal do Azure e o Powershell |Interna |
| * / 80, 443 |Saída |TCP |VIRTUAL_NETWORK/INTERNET|Dependência no Armazenamento do Microsoft Azure, Barramento de Serviço do Microsoft Azure e Azure Active Directory (quando aplicável).|Interno e externo | 
| * / 1433 |Saída |TCP |VIRTUAL_NETWORK/INTERNET|**Acesso aos pontos de extremidade do SQL do Azure** |Interno e externo |
| * / 11000 - 11999 |Saída |TCP |VIRTUAL_NETWORK/INTERNET|**Acesso ao Azure SQL V12** |Interno e externo |
| * / 14000 - 14999 |Saída |TCP |VIRTUAL_NETWORK/INTERNET|**Acesso ao Azure SQL V12** |Interno e externo |
| * / 5671, 5672 |Saída |TCP |VIRTUAL_NETWORK/INTERNET|Dependência para registrar em log a política de Hub de Eventos e o agente de monitoramento |Interno e externo |
| * / 445 |Saída |TCP |VIRTUAL_NETWORK/INTERNET|Dependência do Compartilhamento de Arquivos do Azure para GIT |Interno e externo |
| * / 25028 |Saída |TCP |VIRTUAL_NETWORK/INTERNET|Conectar à retransmissão de SMTP para enviar emails |Interno e externo |
| * / 6381 - 6383 |Entrada e Saída |TCP |VIRTUAL_NETWORK / VIRTUAL_NETWORK|Acesso a Instâncias de Cache Redis entre RoleInstances |Interno e externo |
| * / * | Entrada |TCP |AZURE_LOAD_BALANCER / VIRTUAL_NETWORK| Balanceador de carga de infraestrutura do Azure |Interno e externo |

>[!IMPORTANT]
> * As Portas para as quais a *Finalidade* é **negrito** são necessárias para o serviço de Gerenciamento de API ser implantado com êxito. No entanto, bloquear as outras portas causará degradação da capacidade de usar e monitorar o serviço em execução.

* **Funcionalidade SSL**: para habilitar a criação e validação de cadeia de certificados SSL o serviço Gerenciamento de API precisa de conectividade de rede de saída para ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. Essa dependência não é necessária, se qualquer certificado que você carrega no Gerenciamento de API contém a cadeia completa para a raiz de CA.

* **Acesos de DNS**: O acesso de saída na porta 53 é necessário para a comunicação com servidores DNS. Se houver um servidor DNS personalizado na outra extremidade de um gateway de VPN, o servidor DNS deverá estar acessível pela sub-rede que hospeda o Gerenciamento de API.

* **Monitoramento de integridade e métricas**: conectividade de rede de saída para pontos de extremidade do Monitoramento do Azure, que são resolvidos sob os seguintes domínios: global.metrics.nsatc.net, shoebox2.metrics.nsatc.net e prod3.metrics.nsatc.net.

* **Configuração da ExpressRoute**: uma configuração de cliente comum é definir sua própria rota padrão (0.0.0.0/0), que força o tráfego de saída da Internet para o fluxo local. Esse fluxo de tráfego invariavelmente interrompe a conectividade com o Gerenciamento de API do Azure, pois o tráfego de saída é bloqueado localmente ou convertido em NAT para um conjunto irreconhecível de endereços que não funcionam mais com vários pontos de extremidade do Azure. A solução é definir uma (ou mais) [UDRs][UDRs] (rotas definidas pelo usuário) na sub-rede que contém o Gerenciamento de API do Azure. Uma UDR define rotas específicas de sub-rede que serão consideradas no lugar da rota padrão.
  Se possível, é recomendável usar a seguinte configuração:
 * A configuração de ExpressRoute anuncia 0.0.0.0/0 e, por padrão, encapsula à força todo o tráfego de saída no local.
 * O UDR aplicada à sub-rede que contém o Gerenciamento de API do Azure define 0.0.0.0/0 com um tipo do próximo salto da Internet.
 O efeito combinado dessas etapas é que a UDR do nível de sub-rede tem precedência sobre o túnel forçado do ExpressRoute, garantindo acesso de Internet de saída do Gerenciamento de API do Azure.

**Roteamento por meio de dispositivos de rede virtual**: as configurações que usam um UDR com uma rota padrão (0.0.0.0/0) para rotear o tráfego de internet destinado da sub-rede de Gerenciamento de API por meio de um dispositivo de rede virtual em execução no Azure impedirão a comunicação completa entre o Gerenciamento de API e os serviços necessários. Não há suporte para essa configuração. 

>[!WARNING]  
>O Gerenciamento de API do Azure não tem suporte com configurações do ExpressRoute que **incorretamente cruzam anúncios de rotas do caminho de emparelhamento público para o caminho de emparelhamento privado**. As configurações de ExpressRoute com emparelhamento público definido receberão anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se esses intervalos de endereços forem incorretamente anunciados de modo cruzado no caminho de emparelhamento privado, o resultado final será que todos os pacotes de saída de rede da sub-rede da instância do Gerenciamento de API do Azure serão incorretamente encapsulados à força em uma infraestrutura de rede local do cliente. Esse fluxo de rede interrompe o Gerenciamento de API do Azure. A solução para esse problema é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento particular.


## <a name="troubleshooting"> </a>Solução de problemas
* **Instalação Inicial**: quando a implantação inicial do serviço de Gerenciamento de API em uma sub-rede não for bem-sucedida, é recomendável primeiro implantar uma máquina virtual na mesma sub-rede. Em seguida, acesse a área de trabalho remota na máquina virtual e valide se há conectividade a um de cada recurso abaixo em sua assinatura do azure 
    * Azure Storage Blob
    * Banco de Dados SQL do Azure

 > [!IMPORTANT]
 > Após validar a conectividade, certifique-se de remover todos os recursos implantados na sub-rede antes de implantar o Gerenciamento de API na sub-rede.

* **Atualizações Incrementais**: ao fazer alterações em sua rede, consulte a [API NetworkStatus](https://docs.microsoft.com/rest/api/apimanagement/networkstatus) para verificar se o serviço de Gerenciamento de API não perdeu o acesso a nenhum dos recursos críticos dos quais ele depende. O status de conectividade deve ser atualizado a cada 15 minutos.

* **Links de Navegação do Recurso**: ao implantar na sub-rede da VNET do estilo Resource Manager, o Gerenciamento de API reserva a sub-rede criando um Link de navegação do recurso. Se a sub-rede já contiver um recurso de outro provedor, a implantação **falhará**. Da mesma forma, quando você move um serviço de Gerenciamento de API para uma sub-rede diferente ou o exclui, removeremos esse link de navegação do recurso. 

## Roteamento do <a name="routing"> </a>
+ Um endereço IP público com balanceamento de carga (VIP) será reservado para fornecer acesso a todos os pontos de extremidade de serviço.
+ Um endereço IP de um intervalo IP de sub-rede (DIP) será usado para acessar recursos na VNET e um VIP (endereço IP público) será usado para acessar recursos fora da VNET.
+ O endereço IP público com carga balanceada podem ser encontrados na folha Visão Geral/Essentials no portal do Azure.

## <a name="limitations"> </a>Limitações
* Uma sub-rede que contém instâncias de Gerenciamento de API não pode conter outros tipos de recursos do Azure.
* A sub-rede e o serviço de Gerenciamento de API devem estar na mesma assinatura.
* Uma sub-rede que contém instâncias de Gerenciamento de API não pode ser movida entre assinaturas.
* Para implantações de Gerenciamento de API de várias regiões configuradas no modo de rede virtual interna, os usuários são responsáveis por gerenciar o balanceamento de carga em várias regiões, uma vez que eles detêm o roteamento.


## <a name="related-content"> </a>Conteúdo relacionado
* [Conectando uma rede virtual ao back-end usando o Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Conectar uma rede virtual de diferentes modelos de implantação](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Como usar o Inspetor de API para rastrear chamadas no Gerenciamento de API do Azure](api-management-howto-api-inspector.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/virtual-networks-nsg.md
