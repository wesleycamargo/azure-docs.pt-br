---
title: Como usar o Gerenciamento de API do Azure com redes virtuais
description: Saiba como configurar uma conexão a uma rede virtual no Gerenciamento de API do Azure e acessar serviços Web por meio dela.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: apimpm
ms.openlocfilehash: 7cbd21ce9f8e5d9d3c03eb7c626ab41b8d28498d
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59505661"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Como usar o Gerenciamento de API do Azure com redes virtuais
As redes virtuais do Azure (VNETs) permitem que você coloque qualquer um dos recursos do Azure em uma rede não roteável para a Internet com acesso controlado. Essas redes podem ser conectadas às redes locais usando várias tecnologias VPN. Para saber mais sobre Redes Virtuais do Azure comece com as informações aqui: [Visão geral da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

O Gerenciamento de API do Azure pode ser implantado na VNET (rede virtual) para que possa acessar serviços de back-end na rede. O portal do desenvolvedor e o gateway de API podem ser configurados para serem acessados pela Internet ou somente na rede virtual.

> [!NOTE]
> O Gerenciamento de API do Azure oferece suporte às VNets clássicas e do Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você precisa ter:

+ Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância do APIM. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

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

     ![Emparelhamento privado][api-management-vnet-private]

     Agora você verá uma lista de todas as regiões em que o serviço de Gerenciamento de API é disponibilizado. Selecione uma VNET e uma sub-rede para cada região. A lista é preenchida com as redes virtuais clássicas e do Resource Manager disponíveis em sua assinatura do Azure que estão instaladas na região que você está configurando.

     > [!NOTE]
     > O **Ponto de Extremidade de Serviço** no diagrama acima inclui Gateway/Proxy, o Portal do Azure, o Portal do Desenvolvedor, GIT e o Ponto de Extremidade de Gerenciamento Direto.
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
> Se você remove o gerenciamento de API de uma rede virtual ou alterar aquela na qual ele é implantado, a rede virtual usada anteriormente pode ficar bloqueada por até duas horas. Durante esse período, não será possível excluir a rede virtual nem implantar um novo recurso nela.

## <a name="enable-vnet-powershell"> </a>Habilitar a conexão de VNET usando cmdlets do PowerShell
Você também pode habilitar a conectividade de VNET usando os cmdlets do PowerShell

* **Criar um serviço de Gerenciamento de API dentro de uma VNET**: Use o cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) para criar um serviço de gerenciamento de API dentro de uma rede virtual.

* **Implantar um serviço de Gerenciamento de API existente dentro de uma VNET**: Use o cmdlet [AzApiManagementRegion atualização](/powershell/module/az.apimanagement/update-azapimanagementregion) para mover um serviço de gerenciamento de API existente dentro de uma rede Virtual.

## <a name="connect-vnet"> </a>Conectar-se a um serviço Web hospedado em uma rede virtual
Depois que o serviço de Gerenciamento de API for conectado à VNET, acessar os serviços de back-end nela será o mesmo que acessar serviços públicos. Basta digitar o endereço IP local ou o nome do host (se um servidor DNS estiver configurado para a VNET) do serviço Web no campo **URL do serviço Web** ao criar uma nova API ou editar uma já existente.

![Adicionar a API da VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Problemas comuns de configuração de rede
Veja a seguir uma lista de problemas comuns de erro de configuração que podem ocorrer ao implantar o serviço de Gerenciamento de API em uma rede virtual.

* **Instalação de servidor DNS personalizado**: O serviço de Gerenciamento de API depende de vários serviços do Azure. Quando o Gerenciamento de API estiver hospedado em uma VNET com um servidor DNS personalizado, será necessário resolver os nomes de host desses serviços do Azure. Siga [estas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) orientações sobre a configuração de DNS personalizada. Consulte a tabela de portas abaixo e outros requisitos de rede para ter uma referência.

> [!IMPORTANT]
> Se você planeja usar Servidor(es) de DNS personalizados para a rede virtual, você deve configurá-lo **antes** de implantar um serviço de gerenciamento de API para ele. Caso contrário, você precisa atualizar o serviço de gerenciamento de API cada vez que alterar o(s) servidor(es) DNS executando a [Operação Aplicar configurações de rede](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Portas necessárias para o Gerenciamento de API**: O tráfego de entrada e saída para a sub-rede na qual o Gerenciamento de API foi implantado pode ser controlado usando o [Grupo de Segurança de Rede][Network Security Group]. Se qualquer uma dessas portas estiver indisponível, o Gerenciamento de API poderá não funcionar corretamente e poderá se tornar inacessível. A existência de uma ou mais dessas portas bloqueadas é outro problema de configuração incorreta no uso do Gerenciamento de API em uma VNET.

Quando uma instância do serviço Gerenciamento de API está hospedada em uma rede virtual, as portas na tabela a seguir são usadas.

| Porta(s) de Origem/Destino | Direção          | Protocolo de transporte |   [Marcas de serviço](../virtual-network/security-overview.md#service-tags) <br> Origem/Destino   | Finalidade ( * )                                                 | Tipo de Rede Virtual |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Entrada            | TCP                | INTERNET / VIRTUAL_NETWORK            | Comunicação do cliente com o Gerenciamento de API                      | Externo             |
| * / 3443                     | Entrada            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Ponto de extremidade de gerenciamento para o Portal do Azure e o Powershell         | Interno e externo  |
| * / 80, 443                  | Saída           | TCP                | VIRTUAL_NETWORK / Armazenamento             | **Dependência no armazenamento do Azure**                             | Interno e externo  |
| * / 80, 443                  | Saída           | TCP                | VIRTUAL_NETWORK/AzureActiveDirectory | Azure Active Directory (onde aplicável)                   | Interno e externo  |
| * / 1433                     | Saída           | TCP                | VIRTUAL_NETWORK / SQL                 | **Acesso aos pontos de extremidade do SQL do Azure**                           | Interno e externo  |
| * / 5672                     | Saída           | TCP                | VIRTUAL_NETWORK / EventHub            | Dependência para registrar em log a política de Hub de Eventos e o agente de monitoramento | Interno e externo  |
| * / 445                      | Saída           | TCP                | VIRTUAL_NETWORK / Armazenamento             | Dependência do Compartilhamento de Arquivos do Azure para GIT                      | Interno e externo  |
| * / 1886                     | Saída           | TCP                | VIRTUAL_NETWORK/INTERNET            | Necessário para publicar o status da Integridade no Resource Health          | Interno e externo  |
| */443                     | Saída           | TCP                | VIRTUAL_NETWORK/AzureMonitor         | Publicar Logs de Diagnóstico e Métricas                        | Interno e externo  |
| * / 25                       | Saída           | TCP                | VIRTUAL_NETWORK/INTERNET            | Conectar a retransmissão SMTP para enviar emails                    | Interno e externo  |
| * / 587                      | Saída           | TCP                | VIRTUAL_NETWORK/INTERNET            | Conectar a retransmissão SMTP para enviar emails                    | Interno e externo  |
| * / 25028                    | Saída           | TCP                | VIRTUAL_NETWORK/INTERNET            | Conectar a retransmissão SMTP para enviar emails                    | Interno e externo  |
| * / 6381 - 6383              | Entrada e Saída | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Acesso a Instâncias do Cache para Redis do Azure entre RoleInstances          | Interno e externo  |
| * / *                        | Entrada            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Balanceador de carga de infraestrutura do Azure                          | Interno e externo  |

>[!IMPORTANT]
> As Portas para as quais a *Finalidade* é **negrito** são necessárias para o serviço de Gerenciamento de API ser implantado com êxito. No entanto, bloquear as outras portas causará degradação da capacidade de usar e monitorar o serviço em execução.

+ **Funcionalidade de SSL**: Para habilitar a criação e validação de cadeia de certificados SSL o serviço Gerenciamento de API precisa de conectividade de rede de saída para ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. Essa dependência não é necessária, se qualquer certificado que você carrega no Gerenciamento de API contém a cadeia completa para a raiz de CA.

+ **Acesso DNS**: O acesso de saída na porta 53 é necessário para a comunicação com servidores DNS. Se houver um servidor DNS personalizado na outra extremidade de um gateway de VPN, o servidor DNS deverá estar acessível pela sub-rede que hospeda o Gerenciamento de API.

+ **Monitoramento de integridade e métricas**: Conectividade de rede de saída para pontos de extremidade do Monitoramento do Azure, que são resolvidos sob os seguintes domínios:

    | Azure Environment | Pontos de extremidade                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Público do Azure      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. warm.ingestion.msftcloudes.com onde `East US 2` é eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure China       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **Retransmissão de SMTP**: Conectividade de rede de saída para a retransmissão de SMTP, que resolve em um host `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` e `ies.global.microsoft.com`

+ **CAPTCHA do portal do desenvolvedor**: Conectividade de rede de saída para o CAPTCHA do portal do desenvolvedor, que é resolvido sob o host `client.hip.live.com`.

+ **Diagnósticos do portal do Azure**: Para habilitar o fluxo de log de diagnóstico do portal do Azure ao usar a extensão de Gerenciamento de API de dentro de uma Rede Virtual, é necessário ter acesso de saída a `dc.services.visualstudio.com` na porta 443. Isso ajuda na solução de problemas que você pode enfrentar ao usar a extensão.

+ **Força o túnel de tráfego para o Firewall local usando a solução de virtualização de rede ou rota expressa**: Uma configuração de cliente comum é definir sua própria rota padrão (0.0.0.0/0) que força todo o tráfego de gerenciamento de API delegada sub-rede para o fluxo por meio de um firewall local ou para um dispositivo de rede virtual. Esse fluxo de tráfego invariavelmente interrompe a conectividade com o Gerenciamento de API do Azure, pois o tráfego de saída é bloqueado localmente ou convertido em NAT para um conjunto irreconhecível de endereços que não funcionam mais com vários pontos de extremidade do Azure. A solução requer que você faça duas coisas:

  * Habilite pontos de extremidade de serviço na sub-rede na qual o serviço de gerenciamento de API é implantado. [Pontos de extremidade de serviço] [ ServiceEndpoints] precisam ser habilitados para o Sql do Azure, o armazenamento do Azure, o hub de eventos do Azure e o barramento de serviço do Azure. Habilitar pontos de extremidade diretamente da sub-rede delegado a esses serviços permite que eles usem a rede de backbone do Microsoft Azure fornece roteamento ideal para o tráfego do serviço de gerenciamento de API. Se você usar pontos de extremidade de serviço com um gerenciamento de Api em túnel forçado, os serviços do Azure acima tráfego não será forçado em túnel. O gerenciamento de API que o tráfego de dependência do serviço é forçado em túnel e não pode ser perdido ou o serviço de gerenciamento de API não funcionará corretamente.
    
  * Todo o controle plano o tráfego da Internet para o ponto de extremidade de gerenciamento do seu serviço de gerenciamento de API são roteadas por meio de um conjunto específico de IPs de entrada hospedado pelo gerenciamento de API. Quando o tráfego é forçado em túnel as respostas não simetricamente mapeará para esses IPs de origem de entrada. Para superar a limitação, precisamos adicionar as seguintes rotas definidas pelo usuário ([UDRs][UDRs]) para conduzir tráfego de volta para o Azure, definindo o destino dessas rotas de host para "Internet". O conjunto de IPs de entrada para tráfego de plano de controle é da seguinte maneira:
    
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32

  * Para outras dependências de serviço de gerenciamento de API que são forçado em túnel, deve haver uma maneira de resolver o nome do host e em contato com o ponto de extremidade. Eles incluem
      - Monitoramento de integridade e métricas
      - Diagnóstico do portal do Azure
      - Retransmissão de SMTP
      - Portal do desenvolvedor CAPTCHA

## <a name="troubleshooting"> </a>Solução de problemas
* **Configuração inicial**: Quando a implantação inicial do serviço de Gerenciamento de API em uma sub-rede não for bem-sucedida, é recomendável primeiro implantar uma máquina virtual na mesma sub-rede. Em seguida, acesse a área de trabalho remota na máquina virtual e valide se há conectividade a um de cada recurso abaixo em sua assinatura do azure
    * Azure Storage Blob
    * Banco de Dados SQL do Azure
    * Tabela de armazenamento do Azure

  > [!IMPORTANT]
  > Após validar a conectividade, certifique-se de remover todos os recursos implantados na sub-rede antes de implantar o Gerenciamento de API na sub-rede.

* **Atualizações incrementais**: Ao fazer alterações em sua rede, consulte [API NetworkStatus](https://docs.microsoft.com/rest/api/apimanagement/networkstatus), para verificar que o serviço de gerenciamento de API não perdeu o acesso a qualquer um dos recursos críticos, dos quais ele depende. O status de conectividade deve ser atualizado a cada 15 minutos.

* **Links de Navegação do Recurso**: Ao implantar na sub-rede da VNET do estilo Resource Manager, o Gerenciamento de API reserva a sub-rede criando um Link de navegação do recurso. Se a sub-rede já contiver um recurso de outro provedor, a implantação **falhará**. Da mesma forma, quando você move um serviço de Gerenciamento de API para uma sub-rede diferente ou o exclui, removeremos esse link de navegação do recurso.

## <a name="subnet-size"> </a> Requisito de tamanho de sub-rede
O Azure reserva alguns endereços IP em cada sub-rede, os quais não podem ser usados. O primeiro e o último endereço IP das sub-redes são reservados para fins de conformidade de protocolo, juntamente com três outros endereços usados para os serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP usados pela infraestrutura de rede virtual do Azure, cada instância do Gerenciamento de API da sub-rede usa dois endereços IP por unidade de SKU Premium ou um endereço IP para a SKU do desenvolvedor. Cada instância de reserva um endereço IP adicional para o balanceador externo de carga. Ao implantar em redes virtuais internas, é necessário um endereço IP adicional para o balanceador de carga interno.

Dado o cálculo acima, o tamanho mínimo da sub-rede, na qual o gerenciamento de API pode ser implantado é/29, resultando três endereços IP.

## Roteamento do <a name="routing"> </a>
+ Um endereço IP público com balanceamento de carga (VIP) será reservado para fornecer acesso a todos os pontos de extremidade de serviço.
+ Um endereço IP de um intervalo IP de sub-rede (DIP) será usado para acessar recursos na VNET e um VIP (endereço IP público) será usado para acessar recursos fora da VNET.
+ O endereço IP público com carga balanceada podem ser encontrados na folha Visão Geral/Essentials no portal do Azure.

## <a name="limitations"> </a>Limitações
* Uma sub-rede que contém instâncias de Gerenciamento de API não pode conter outros tipos de recursos do Azure.
* A sub-rede e o serviço de Gerenciamento de API devem estar na mesma assinatura.
* Uma sub-rede que contém instâncias de Gerenciamento de API não pode ser movida entre assinaturas.
* Para implantações de Gerenciamento de API de várias regiões configuradas no modo de rede virtual interna, os usuários são responsáveis por gerenciar o balanceamento de carga em várias regiões, uma vez que eles detêm o roteamento.
* Conectividade de um recurso em uma rede virtual emparelhada globalmente em outra região para o serviço de gerenciamento de API no modo interno não funcione devido à limitação de plataforma. Para obter mais informações, consulte [Recursos em uma rede virtual não podem se comunicar com o endereço IP de um balanceador de carga interno do Azure na rede virtual emparelhada](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)


## <a name="related-content"> </a>Conteúdo relacionado
* [Conectar uma rede Virtual ao back-end usando o Gateway de Vpn](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Conectar uma rede Virtual de diferentes modelos de implantação](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Como usar o Inspetor de API para rastrear chamadas no Gerenciamento de API do Azure](api-management-howto-api-inspector.md)
* [Perguntas frequentes sobre rede virtual](../virtual-network/virtual-networks-faq.md)
* [Marcas de serviço](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
