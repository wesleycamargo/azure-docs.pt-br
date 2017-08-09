---
title: Criar ou atualizar um Gateway de Aplicativo do Azure com o firewall do aplicativo Web | Microsoft Docs
description: Saiba como criar um Gateway de Aplicativo com o firewall do aplicativo Web usando o portal
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 3ee146a0be3c3338cf0037e2ec92a3b8d0c05a4e
ms.contentlocale: pt-br
ms.lasthandoff: 07/28/2017

---

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Criar um gateway de aplicativo com o firewall do aplicativo Web usando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-web-application-firewall-powershell.md)

Aprenda como criar um gateway de aplicativo habilitado para firewall de aplicativo Web.

O firewall de aplicativo Web (WAF) no Gateway de Aplicativo do Azure protege os aplicativos Web contra ataques comuns baseados na Web, como injeção de SQL, ataques de scripts entre sites e sequestros de sessão. O aplicativo Web protege contra muitas das 10 principais vulnerabilidades da Web OWASP.

## <a name="scenarios"></a>Cenários

Neste artigo, há dois cenários:

No primeiro cenário, você aprenderá a [criar um gateway de aplicativo com o firewall do aplicativo Web](#create-an-application-gateway-with-web-application-firewall)

No segundo cenário, você aprende a [adicionar o firewall do aplicativo Web a um gateway de aplicativo existente](#add-web-application-firewall-to-an-existing-application-gateway).

![Cenário de exemplo][scenario]

> [!NOTE]
> A configuração adicional do Gateway de Aplicativo, incluindo investigações de integridade personalizadas, endereços de pool de back-end e regras adicionais são configuradas após o Gateway de Aplicativo ser configurado e não durante a implantação inicial.

## <a name="before-you-begin"></a>Antes de começar

O Azure Gateway de Aplicativo requer sua própria sub-rede. Ao criar uma rede virtual, certifique-se de deixar espaço de endereço suficiente para ter várias sub-redes. Depois de implantar um gateway de aplicativo a uma sub-rede, apenas gateway de aplicativos adicionais poderão ser adicionados à sub-rede.

##<a name="add-web-application-firewall-to-an-existing-application-gateway"></a> Adicionar o firewall do aplicativo Web a um gateway de aplicativo existente

Este exemplo atualiza um gateway de aplicativo existente para dar suporte ao firewall do aplicativo Web no modo de prevenção.

1. No painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique no Gateway de Aplicativo existente, na folha **Todos os recursos**. Se a assinatura que você selecionou já contém vários recursos, você pode inserir o nome em **Filtrar por nome...** para acessar a Zona DNS facilmente.

   ![Criação de um gateway de aplicativo][1]

1. Clique em **Firewall do Aplicativo Web** e atualize as configurações do gateway de aplicativo. Quando concluir, clique em **Salvar**

    As configurações para atualizar um gateway de aplicativo existente para dar suporte ao firewall do aplicativo da Web são:

   | **Configuração** | **Valor** | **Detalhes**
   |---|---|---|
   |**Atualizar para a camada WAF**| Verificado | Isso define a camada do gateway de aplicativo para a camada WAF.|
   |**Status do firewall**| habilitado | habilitado | Essa configuração habilita o firewall no WAF.|
   |**Modo de firewall** | Prevenção | Essa configuração é como o firewall do aplicativo Web lida com o tráfego mal-intencionado. O modo **Detecção** só registra em log os eventos, onde o modo **Prevenção** registra os eventos e interrompe o tráfego mal-intencionado.|
   |**Conjunto de regras**|3.0|Essa configuração determina o [conjunto principal de regras](application-gateway-web-application-firewall-overview.md#core-rule-sets) usado para proteger os membros do pool de back-end.|
   |**Configurar regras desabilitadas**|varia|Para evitar possíveis falsos positivos, essa configuração permite que você desabilite certas [regras e grupos de regras](application-gateway-crs-rulegroups-rules.md).|

    >[!NOTE]
    > Ao fazer o upgrade de um gateway de aplicativo existente para o SKU do WAF, o tamanho do SKU muda para **médio**. Isso pode ser reconfigurado após a conclusão da configuração.

    ![folha mostrando configurações básicas][2-1]

    > [!NOTE]
    > Para exibir logs de firewall do aplicativo Web, o diagnóstico deve ser habilitado e ApplicationGatewayFirewallLog selecionado. É possível escolher uma contagem de instâncias de 1 para fins de teste. É importante saber que qualquer contagem de instâncias em duas instâncias não é coberta por um SLA e, portanto, não são recomendadas. Os gateways pequenos não estão disponíveis ao usar o firewall do aplicativo Web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>criar um gateway de aplicativo com o firewall do aplicativo Web

Este cenário:

* Crie um gateway de aplicativo de firewall do aplicativo Web médio com duas instâncias.
* Criará uma rede virtual chamada AdatumAppGatewayVNET, com um bloco CIDR 10.0.0.0/16 reservado.
* Criará uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.
* Configurará um certificado para descarregamento SSL.

1. Faça logon no [Portal do Azure](https://portal.azure.com). Caso você ainda não tenha uma conta, poderá se inscrever para obter uma [avaliação gratuita de um mês](https://azure.microsoft.com/free)
1. No painel Favoritos do portal, clique em **Novo**
1. Na folha **Novo**, clique em **Rede**. Na folha **Rede**, clique em **Gateway de Aplicativo**, conforme mostrado na seguinte imagem:
1. Navegue até o portal do Azure, clique em **Novo** > **Rede** > **Gateway de Aplicativo**

    ![Criação de um gateway de aplicativo][1]

1. Na folha **Informações Básicas** exibida, insira os seguintes valores e clique em **OK**:

   | **Configuração** | **Valor** | **Detalhes**
   |---|---|---|
   |**Nome**|AdatumAppGateway|O nome do gateway de aplicativo|
   |**Camada**|WAF|Os valores disponíveis são Standard e WAF. Visite a página [Firewall do aplicativo Web](application-gateway-web-application-firewall-overview.md) para saber mais sobre o WAF.|
   |**Tamanho do SKU**|Média|As opções ao escolher a camada Standard são Pequeno, Médio e Grande. Ao escolher a camada do WAF, as opções são apenas Médio e Grande.|
   |**Contagem de instâncias**|2|Número de instâncias do gateway de aplicativo para alta disponibilidade. Contagens de instância iguais a 1 devem ser usadas apenas para fins de teste.|
   |**Assinatura**|[Sua assinatura]|Selecione uma assinatura na qual o gateway de aplicativo será criado.|
   |**Grupo de recursos**|**Criar novo:** AdatumAppGatewayRG|Crie um grupos de recursos. O nome do grupo de recursos deve ser exclusivo na assinatura selecionada. Para saber mais sobre grupos de recursos, leia o artigo [Visão geral do Gerenciador de Recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Localidade**|Oeste dos EUA||

   ![folha mostrando configurações básicas][2-2]

1. Na folha **Configurações** exibida em **Rede virtual**, clique em **Escolher uma rede virtual**. Isso abre a folha **Escolher rede virtual**.  Clique em **Criar nova** para abrir a folha **Criar rede virtual**.

   ![escolher uma rede virtual][2]

1. Na folha **Criar rede virtual**, insira os valores a seguir e clique em **OK**. Isso fecha as folhas **Criar rede virtual** e **Escolher rede virtual**. Isso também populará o campo **Sub-rede** na folha **Configurações** com a sub-rede escolhida.

   |**Configuração** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|AdatumAppGatewayVNET|Nome do gateway de aplicativo|
   |**Espaço de Endereço**|10.0.0.0/16| Esse é o espaço de endereço da rede virtual|
   |**Nome da sub-rede**|AppGatewaySubnet|Nome da sub-rede do gateway de aplicativo|
   |**Intervalo de endereços da sub-rede**|10.0.0.0/28| Essa sub-rede permite sub-redes adicionais na rede virtual para membros do pool de back-end|

1. Na folha **Configurações** em **Configuração de IP de front-end**, escolha **Público** como o **Tipo de endereço IP**

1. Na folha **Configurações** em **Endereço IP público**, clique em **Escolher um endereço IP público**. Isso abrirá a folha **Escolher o endereço IP público**. Em seguida, clique em **Criar novo**.

   ![escolher o IP público][3]

1. Na folha **Criar endereço IP público**, aceite o valor padrão e clique em **OK**. Isso fecha as folhas **Escolher endereço IP público** e **Criar endereço IP público** e populará **Endereço IP público** com o endereço IP público escolhido.

1. Na folha **Configurações** em **Configuração do ouvinte**, clique em **HTTP** em **Protocolo**. Um certificado é necessário para usar **https**. A chave privada do certificado é necessária, portanto uma exportação .pfx do certificado é necessária para ser fornecida, além da senha do arquivo.

1. Definir as configurações específicas do **WAF** .

   |**Configuração** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Status do firewall**| habilitado| Essa configuração ativa ou desativa o WAF.|
   |**Modo de firewall** | Prevenção| Essa configuração determina as ações tomadas pelo WAF em relação ao tráfego mal-intencionado. Se **Detecção** for escolhido, o tráfego só será registrado em log.  Se **Prevenção** for escolhido, o tráfego será registrado em log e interrompido com uma resposta 403 Não Autorizado.|


1. Examine a página Resumo e clique em **OK**.  Agora o Gateway de Aplicativo está na fila e será criado.

1. Quando o Gateway de Aplicativo tiver sido criado, navegue até ele no portal para continuar a configuração do Gateway de Aplicativo.

    ![Modo de exibição de recursos do Gateway de Aplicativo][10]

Estas etapas criam um gateway de aplicativo básico com configurações padrão para o ouvinte, pool de back-end, configurações de http de back-end e regras. Você pode modificar essas configurações de acordo com sua implantação quando o provisionamento for bem-sucedido

> [!NOTE]
> Os gateways de aplicativo criados com a configuração básica do firewall do aplicativo Web são configurados com o CRS 3.0 para proteções.

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender a configurar um alias de domínio personalizado para o [endereço IP público](../dns/dns-custom-domain.md#public-ip-address) usando DNS do Azure ou outro provedor DNS.

Saiba como configurar o log de diagnóstico para registrar os eventos detectados ou impedidos pelo firewall do aplicativo Web ao visitar o [Diagnóstico do Gateway de Aplicativo](application-gateway-diagnostics.md)

Saiba como criar investigações de integridade personalizados visitando [Criar uma investigação de integridade personalizada](application-gateway-create-probe-portal.md)

Saiba como configurar o Descarregamento de SSL e levar a descriptografia SSL cara longe dos seus servidores Web visitando [Configurar Descarregamento de SSL](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png

