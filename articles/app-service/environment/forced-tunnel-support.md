---
title: "Configurar seu Ambiente do Serviço de Aplicativo do Azure para ser forçado em túnel"
description: "Habilitar o Ambiente do Serviço de Aplicativo para trabalhar quando é realizado o túnel forçado do tráfego de saída"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/10/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 4caaf0df3f1dd4b2cb9b76283a6beed897531c1c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurar seu Ambiente de Serviço de Aplicativo com tunelamento forçado

O Ambiente do Serviço de Aplicativo é uma implantação do Serviço de Aplicativo do Azure em uma instância de Rede Virtual do Azure do cliente. Muitos clientes configuram suas redes virtuais para extensões de suas redes locais com VPNs ou conexões ExpressRoute do Azure. Devido a políticas corporativas ou outras restrições de segurança, eles configuraram rotas para enviar todo o tráfego de saída no local para poder entrar com a internet. Alterar o roteamento da rede virtual para que o tráfego de saída da rede virtual flua por meio da conexão VPN ou ExpressRoute para o local é chamado de túnel forçado. 

O túnel forçado pode causar problemas para um Ambiente do Serviço de Aplicativo. O Ambiente do Serviço de Aplicativo tem inúmeras dependências externas, enumeradas no documento [arquitetura de rede do Ambiente do Serviço de Aplicativo][network]. O Ambiente do Serviço de Aplicativo, por padrão, requer que todas as comunicações de saída atravessem o VIP provisionado com o Ambiente do Serviço de Aplicativo.

As rotas são um aspecto importante do que o túnel forçado é e como lidar com ele. Em uma rede virtual do Azure, o roteamento é feito com base na LPM (correspondência de prefixo mais longo). Se houver mais de uma rota com a mesma correspondência LPM, uma rota será selecionada com base em sua origem na seguinte ordem:

* UDR (rota definida pelo usuário)
* Rota BGP (quando o ExpressRoute é usado)
* Rota de sistema

Para saber mais sobre como rotear em uma rede virtual, leia [User-defined routes and IP forwarding][routes] (Rotas definidas pelo usuário e encaminhamento de IP). 

Se você desejar que o Ambiente do Serviço de Aplicativo opere em uma rede virtual de túnel forçado, você terá duas opções:

* Habilitar o Ambiente do Serviço de Aplicativo para ter acesso direto à Internet.
* Alterar o ponto de extremidade de saída para o Ambiente do Serviço de Aplicativo.

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Habilitar o Ambiente do Serviço de Aplicativo para ter acesso direto à Internet

Para o Ambiente do Serviço de Aplicativo funcionar enquanto sua rede virtual é configurada com uma conexão ExpressRoute, é possível:

* Configurar o ExpressRoute para anunciar 0.0.0.0/0. Por padrão, ele forçar túneis para todo o tráfego de saída local.
* Crie um UDR. Aplicá-lo à sub-rede que contém o Ambiente do Serviço de Aplicativo com um prefixo de endereço 0.0.0.0/0 e um tipo do próximo salto como Internet.

Se você fizer essas duas alterações, o tráfego destinado à Internet proveniente da sub-rede do Ambiente do Serviço de Aplicativo não será forçado a ir para a conexão ExpressRoute, e o Ambiente do Serviço de Aplicativo funcionará.

> [!IMPORTANT]
> As rotas definidas em uma UDR devem ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute. O exemplo anterior usa o intervalo de endereços amplo 0.0.0.0/0. É possível que ele seja acidentalmente substituído pelos anúncios de rota que usam intervalos de endereços mais específicos.
>
> Os Ambientes do Serviço de Aplicativo não são compatíveis com configurações do ExpressRoute que façam anúncios de modo cruzado de rotas do caminho de emparelhamento público para o caminho de emparelhamento privado. Configurações do ExpressRoute com emparelhamento público configurado recebem anúncios de rota da Microsoft. Os anúncios contêm um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se os intervalos de endereços forem anunciados de modo cruzado no caminho de emparelhamento privado, o túnel forçado de todos os pacotes de rede de saída da sub-rede do Ambiente do Serviço de Aplicativo será realizado para uma infraestrutura de rede local do cliente. Esse fluxo de rede não é compatível no momento com os Ambientes do Serviço de Aplicativo. Uma solução para esse problema é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento privado. Outra solução é habilitar o Ambiente do Serviço de Aplicativo para trabalhar em uma configuração de túnel forçado.

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>Alterar o ponto de extremidade de saída para o Ambiente do Serviço de Aplicativo ##

Esta seção descreve como habilitar um Ambiente do Serviço de Aplicativo para operar em uma configuração de túnel forçado alterando o ponto de extremidade de saída usado pelo Ambiente do Serviço de Aplicativo. Se o túnel forçado do tráfego de saída do Ambiente do Serviço de Aplicativo for realizado para uma rede local, será necessário permitir que esse tráfego tenha como origem endereços IP diferentes do endereço VIP do Ambiente do Serviço de Aplicativo.

Um Ambiente do Serviço de Aplicativo não tem apenas dependências externas, mas também deve ser escutado para tráfego de entrada e responder a esse tráfego. As respostas não podem ser enviadas de volta de outro endereço, porque isso interrompe o TCP. Há três etapas necessárias para alterar o ponto de extremidade de saída para o Ambiente do Serviço de Aplicativo:

1. Definir uma tabela de rotas para garantir que o tráfego de gerenciamento de entrada possa voltar do mesmo endereço IP.

2. Adicionar seus endereços IP que devem ser usados para a saída para o firewall do Ambiente do Serviço de Aplicativo.

3. Definir as rotas para o tráfego de saída do Ambiente do Serviço de Aplicativo a ter o túnel forçado realizado.

   ![Fluxo de rede de túnel forçado][1]

Será possível configurar o Ambiente do Serviço de Aplicativo com diferentes endereços de saída depois que o Ambiente do Serviço de Aplicativo já estiver em funcionamento ou eles poderão ser definidos durante a implantação do Ambiente do Serviço de Aplicativo.

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>Alterar o endereço de saída depois que o Ambiente do Serviço de Aplicativo estiver em funcionamento ###
1. Obtenha os endereços IP que você deseja usar como IPs de saída para o Ambiente do Serviço de Aplicativo. Se você estiver realizando túnel forçado, estes endereços virão dos das NATs ou dos IPs do gateway. Se desejar rotear o tráfego de saída do Ambiente do Serviço de Aplicativo por meio de uma NVA, o endereço de saída será o IP público da NVA.

2. Defina os endereços de saída em suas informações de configuração do Ambiente do Serviço de Aplicativo. Acesse resource.azure.com e vá para Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>. Em seguida, será possível ver o JSON que descreve o Ambiente do Serviço de Aplicativo. Certifique-se de que consta **leitura/gravação** na parte superior. Selecione **Editar**. Role até a parte inferior e altere o valor **userWhitelistedIpRanges** de **nulo** para algo semelhante ao seguinte. Use os endereços que você deseja definir como o intervalo de endereços de saída. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Selecione **PUT** na parte superior. Essa opção dispara uma operação de dimensionamento no Ambiente do Serviço de Aplicativo e ajusta o firewall.
 
3. Crie ou edite uma tabela de rota e preencha as regras para permitir o acesso de/para os endereços de gerenciamento mapeados para o local do Ambiente do Serviço de Aplicativo. Para localizar os endereços de gerenciamento, consulte [Endereços de gerenciamento de Ambiente de Serviço de Aplicativo][management].

4. Ajuste as rotas aplicadas à sub-rede do Ambiente do Serviço de Aplicativo com uma tabela de rotas ou com rotas BGP. 

Se o Ambiente do Serviço de Aplicativo ficar sem resposta no Portal, haverá um problema com as suas alterações. Talvez o problema seja que a sua lista de endereços de saída estava incompleta, o tráfego foi perdido ou bloqueado. 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>Criar um novo Ambiente do Serviço de Aplicativo com um endereço de saída diferente ###

Se sua rede virtual já estiver configurada para realizar túnel forçado de todo o tráfego, será necessário executar algumas etapas adicionais para criar o Ambiente do Serviço de Aplicativo para que ele possa ser criado com sucesso. É necessário habilitar o uso de outro ponto de extremidade de saída durante a criação de Ambiente do Serviço de Aplicativo. Para fazer isso, é necessário criar o Ambiente do Serviço de Aplicativo com um modelo que especifica os endereços de saída permitidos.

1. Obtenha os endereços IP a serem usados como os endereços de saída para o Ambiente do Serviço de Aplicativo.

2. Crie previamente a sub-rede a ser usada pelo Ambiente do Serviço de Aplicativo. Ela é necessária, para poder definir rotas e também porque o modelo precisa dela.

3. Crie uma tabela de rotas com IPs de gerenciamento que mapeie para seu local do Ambiente do Serviço de Aplicativo. Atribua-o a seu Ambiente do Serviço de Aplicativo.

4. Siga as instruções em [Criar um Ambiente do Serviço de Aplicativo com um modelo][template]. Obtenha o modelo apropriado.

5. Edite a seção "recursos" do arquivo azuredeploy.json. Inclua uma linha para **userWhitelistedIpRanges** com seus valores como esta:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Se esta seção estiver configurada corretamente, o Ambiente do Serviço de Aplicativo deverá ser iniciado sem problemas. 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
