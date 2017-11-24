---
title: "Configurar seu Ambiente do Serviço de Aplicativo do Azure para ser forçado em túnel"
description: "Habilitar seu ASE para trabalhar quando o tráfego de saída é forçado em túnel"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ccompy
ms.openlocfilehash: cd89bd23074dec1de6fa0e8784d42a5c539938b1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurar seu Ambiente de Serviço de Aplicativo com tunelamento forçado

Um Ambiente do Serviço de Aplicativo (ASE) é uma implantação do Serviço de Aplicativo do Azure na Rede Virtual (VNet) do Azure de um cliente. Muitos clientes configuram seus VNets para extensões de suas redes locais com VPNs ou conexões ExpressRoute. Devido a políticas corporativas ou outras restrições de segurança, eles configuraram rotas para enviar todo o tráfego de saída no local para poder entrar com a internet. Alterar o roteamento de rede virtual para que o tráfego de saída do VNet fla através de conexão VPN ou ExpressRoute para o local é chamado de túnel forçado.  

O túnel forçado pode causar problemas para um ASE. O ASE tem um número de dependências externas, que são enumeradas no documento [arquitetura de rede ASE][network]. ASE, por padrão, requer que todas as comunicações de saída atravessem o VIP que está provisionado com o ASE.

As rotas são um aspecto importante o que o túnel forçado é e como lidar com ele. Em uma rede Virtual do Azure, o roteamento é feito com base na correspondência de prefixo mais longo (LPM).  Se houver mais de uma rota com a mesma correspondência LPM, então uma rota será selecionada com base em sua origem na seguinte ordem:

1. Rota definida pelo usuário
1. Rota BGP (quando o ExpressRoute é usado)
1. Rota de sistema

Para saber mais sobre VNet, leia [Rotas Definidas pelo Usuário e Encaminhamento de IP][routes]. 

Se você quiser que seu ASE opere em um túnel forçado VNet, você tem duas opções:

1. Habilitar seu ASE para ter acesso direto à internet
1. Alterar o ponto de extremidade de saída para o ASE

## <a name="enable-your-ase-to-have-direct-internet-access"></a>Habilitar seu ASE para ter acesso direto à internet

Para o ASE funcionar enquanto a VNet estiver configurada com um ExpressRoute, você pode:

* Configurar o ExpressRoute para anunciar 0.0.0.0/0. Por padrão, ele forçar túneis para todo o tráfego de saída local.
* Crie um UDR. Aplique-o à sub-rede que contém o ASE com um prefixo de endereço 0.0.0.0/0 e um tipo de próximo salto de Internet.

Se você fizer essas duas alterações, tráfego destinado à Internet proveniente da sub-rede do ASE não será forçado a ir para o ExpressRoute e o ASE funcionará.

> [!IMPORTANT]
> As rotas definidas em uma UDR devem ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute. O exemplo anterior usa o intervalo de endereços amplo 0.0.0.0/0. É possível que ele seja acidentalmente substituído pelos anúncios de rota que usam intervalos de endereços mais específicos.
>
> Não há suporte para ASEs com configurações do ExpressRoute que façam anúncio cruzado de rotas do caminho de emparelhamento público para o caminho de emparelhamento privado. Configurações do ExpressRoute com emparelhamento público configurado recebem anúncios de rota da Microsoft. Os anúncios contêm um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se os intervalos de endereços forem anunciados de modo cruzado no caminho de emparelhamento privado, todos os pacotes de rede de saída da sub-rede do ASE serão enviados em túnel de modo forçado a uma infraestrutura de rede local do cliente. No momento, não há suporte padrão para esse fluxo de rede com ASEs. Uma solução para esse problema é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento privado.  Outra solução é habilitar seu ASE para trabalhar em uma configuração de túnel forçado.

## <a name="change-the-egress-endpoint-for-your-ase"></a>Alterar o ponto de extremidade de saída para o ASE ##

Esta seção descreve como habilitar um ASE para operar em uma configuração de túnel forçado, alterando o ponto de extremidade de saída usado pelo ASE. Se o tráfego de saída do ASE é forçado em túnel em uma rede local, então você precisa permitir que o tráfego para a fonte de endereços IP que não seja o endereço VIP ASE.

Um ASE não só tem dependências externas, mas ele também deve escutar o tráfego de entrada para gerenciar o ASE. O ASE deve ser capaz de responder a esse tráfego e as respostas não poderem ser enviadas do outro endereço pois isso quebra o TCP.  Assim, há três etapas necessárias para alterar o ponto de extremidade de saída para o ASE.

1. Definir uma tabela de rotas para garantir que o tráfego de entrada de gerenciamento possa voltar do mesmo endereço IP
1. Adicionar seu de endereços IP para que possa ser usado para a saída para o firewall ASE
1. Configurar as rotas para o tráfego de saída do ASE a ser encapsulado

![Fluxo de rede de túnel forçado][1]

Você pode configurar o ASE com endereços de saída diferentes depois que o ASE já estiver em funcionamento ou elas podem ser definidas durante a implantação ASE.  

### <a name="changing-the-egress-address-after-the-ase-is-operational"></a>Alterando o endereço de saída depois que o ASE estiver funcionando ###
1. Obter os endereços IP que você deseja usar como saída IPs para seu ASE. Se você estiver fazendo túnel forçado, este será o NAT ou gateway IPs.  Se você quiser encaminhar o tráfego de saída ASE por meio de uma NVA, o endereço de saída seria o IP público da NVA.
2. Defina os endereços de saída em suas informações de configuração ASE. Vá para resource.azure.com e navegue até: Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>, em seguida, você pode ver o json que descreve o ASE.  Verifique se ele diz leitura/gravação na parte superior.  Clique em Editar Desça até a parte inferior e altere de userWhitelistedIpRanges  

       "userWhitelistedIpRanges": null 
      
  para algo parecido o seguinte. Use os endereços que você deseja definir como o intervalo de endereços de saída. 

      "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

  Clique em COLOCAR na parte superior. Isso dispara uma operação de dimensionamento do seu ASE e ajusta o firewall.
   
3. Criar ou editar uma tabela de rota e preencher as regras para permitir o acesso de/para os endereços de gerenciamento que são mapeados para seu local ASE.  Os endereços de gerenciamento estão aqui, [endereços de Ambiente do Serviço de Aplicativo][management] 

4. Ajuste as rotas aplicadas à sub-rede ASE com uma tabela de rota ou BGP.  

Se o ASE ficar sem resposta a partir do portal, há um problema com as suas alterações.  Pode ser que sua lista de endereços de saída estava incompleta, o tráfego foi perdido ou o tráfego foi bloqueado.  

### <a name="create-a-new-ase-with-a-different-egress-address"></a>Criar um novo ASE com um endereço de saída diferente  ###

Se sua rede virtual já está configurada para forçar todo o tráfego de túnel, você precisará executar algumas etapas adicionais para criar seu ASE, de modo que ele possa criar com êxito. Isso significa que você precisa habilitar o uso de outro ponto de extremidade de saída durante a criação de ASE.  Para fazer isso, você precisa criar o ASE com um modelo que especifica os endereços de saída permitidos.

1. Obter os endereços IP a serem usado como os endereços de saída para o ASE.
1. Crie com antecedência a sub-rede a ser usada pelo ASE. Isso é necessário para permitir que você defina rotas e também porque o modelo requer isso.  
1. Criar uma tabela de rotas com o gerenciamento de IPs que mapeie para seu local ASE e a atribua a seu ASE
1. Siga as instruções aqui, [Criando uma ASE com um modelo][template], e baixar o modelo apropriado
1. Edite a seção de "recursos" do arquivo azuredeploy.json. Incluir uma linha para **userWhitelistedIpRanges** com seus valores, como:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Se isso estiver configurado corretamente, o ASE deve iniciar sem problemas.  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
