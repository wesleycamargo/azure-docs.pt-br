---
title: Criar um gateway de aplicativo usando o portal | Microsoft Docs
description: Saiba como criar um Application Gateway usando o portal
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 2195eb4ce0e22c8c7c72ac0638ab927f13c4d454
ms.contentlocale: pt-br
ms.lasthandoff: 05/02/2017


---
# <a name="create-an-application-gateway-by-using-the-portal"></a>Criar um Application Gateway usando o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modelo do Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI do Azure](application-gateway-create-gateway-cli.md)

Saiba como criar um gateway de aplicativo usando o descarregamento de SSL.

![Cenário de exemplo][scenario]

O Gateway de Aplicativo é uma solução de virtualização dedicada que fornece o ADC (controlador de entrega de aplicativos) como serviço, oferecendo várias funcionalidades de balanceamento de carga de camada 7 para o aplicativo.

Este cenário:

1. [Criará um gateway de aplicativo médio](#create-an-application-gateway) usando o descarregamento de SSL com duas instâncias em sua própria sub-rede.
1. [Adicionará servidores ao pool de back-end](#add-servers-to-backend-pools)
1. [Excluir todos os recursos](#delete-all-resources). Existem encargos para alguns dos recursos criados neste exercício enquanto eles são provisionados. Para minimizar os encargos, conclua as etapas nesta seção depois de concluir o exercício para excluir os recursos criados.



> [!IMPORTANT]
> A configuração adicional do Application Gateway, incluindo investigações de integridade personalizadas, endereços de pool de back-end e regras adicionais são configuradas após o Application Gateway ser configurado e não durante a implantação inicial.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicativo

Para criar um gateway de aplicativo, conclua as etapas a seguir. O gateway de aplicativo exige sua própria sub-rede. Ao criar uma rede virtual, certifique-se de deixar espaço de endereço suficiente para ter várias sub-redes. Depois de implantar um gateway de aplicativo a uma sub-rede, apenas gateway de aplicativos adicionais poderão ser adicionados à sub-rede.

1. Faça logon no [Portal do Azure](https://portal.azure.com). Caso você ainda não tenha uma conta, poderá se inscrever para obter uma [avaliação gratuita de um mês](https://azure.microsoft.com/free)
1. No painel Favoritos do portal, clique em **Novo**
1. Na folha **Novo**, clique em **Rede**. Na folha **Rede**, clique em **Gateway de Aplicativo**, conforme mostrado na seguinte imagem:

    ![Criação de um gateway de aplicativo][1]

1. Na folha **Informações Básicas** exibida, insira os seguintes valores e, em seguida, clique em **OK**:

   | **Configuração** | **Valor** | **Detalhes**
   |---|---|---|
   |**Nome**|AdatumAppGateway|O nome do gateway de aplicativo|
   |**Camada**|Standard|Os valores disponíveis são Standard e WAF. Visite a página [Firewall do aplicativo Web](application-gateway-web-application-firewall-overview.md) para saber mais sobre o WAF.|
   |**Tamanho do SKU**|Média|As opções ao escolher a camada Standard são Pequeno, Médio e Grande. Ao escolher a camada do WAF, as opções são apenas Médio e Grande.|
   |**Contagem de instâncias**|2|Número de instâncias do gateway de aplicativo para alta disponibilidade. Contagens de instância iguais a 1 devem ser usadas apenas para fins de teste.|
   |**Assinatura**|[Sua assinatura]|Selecione uma assinatura na qual o gateway de aplicativo será criado.|
   |**Grupo de recursos**|**Criar novo:** AdatumAppGatewayRG|Crie um grupos de recursos. O nome do grupo de recursos deve ser exclusivo na assinatura selecionada. Para saber mais sobre grupos de recursos, leia o artigo [Visão geral do Gerenciador de Recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Localidade**|Oeste dos EUA||

1. Na folha **Configurações** exibida em **Rede virtual**, clique em **Escolher uma rede virtual**. Isso abrirá a folha **Escolher rede virtual**.  Clique em **Criar nova** para abrir a folha **Criar rede virtual**.

 ![escolher uma rede virtual][2]

1. Na folha **Criar rede virtual**, insira os valores a seguir e clique em **OK**. Isso fechará as folhas **Criar rede virtual** e **Escolher rede virtual**. Isso também populará o campo **Sub-rede** na folha **Configurações** com a sub-rede escolhida.

   |**Configuração** | **Valor** | **Detalhes** |
   |---|---|---|
   |**Nome**|AdatumAppGatewayVNET|Nome do gateway de aplicativo|
   |**Espaço de Endereço**|10.0.0.0/16| Esse é o espaço de endereço da rede virtual|
   |**Nome da sub-rede**|AppGatewaySubnet|Nome da sub-rede do gateway de aplicativo|
   |**Intervalo de endereços da sub-rede**|10.0.0.0/28| Essa sub-rede permite sub-redes adicionais na rede virtual para membros do pool de back-end|

1. Na folha **Configurações** em **Configuração de IP de front-end**, escolha **Público** como o **Tipo de endereço IP**

1. Na folha **Configurações** em **Endereço IP público**, clique em **Escolher um endereço IP público**. Isso abrirá a folha **Escolher o endereço IP público**. Em seguida, clique em **Criar novo**.

 ![escolher o IP público][3]

1. Na folha **Criar endereço IP público**, aceite o valor padrão e clique em **OK**. Isso fechará as folhas **Escolher endereço IP público** e **Criar endereço IP público** e populará **Endereço IP público** com o endereço IP público escolhido.

1. Na folha **Configurações** em **Configuração do ouvinte**, clique em **HTTPS** em **Protocolo**. Isso adicionará outros campos. Clique no ícone de pasta do campo **Carregar certificado PFX** e escolha o certificado .pfx apropriado. Insira as seguintes informações nos campos adicionais de **Configuração do ouvinte**:

   |**Configuração** | **Valor** | **Detalhes** |
   |---|---|---|
   |Nome|Nome do certificado|Esse valor é um nome amigável usado para referenciar o certificado|
   |Senha|Senha do .pfx| Essa é a senha usada para a chave privada|

1. Clique em **OK** na folha **Configurações** para continuar.

1. Examine as configurações na folha **Resumo** e clique em **OK** para iniciar a criação do gateway de aplicativo. A criação de um gateway de aplicativo é uma tarefa de execução longa e levará muito tempo para ser concluída.

## <a name="add-servers-to-backend-pools"></a>Adicionar servidores aos pools de back-end

Depois que o gateway de aplicativo é criado, os sistemas que hospedam o aplicativo que terá a carga balanceada ainda precisará ser adicionado ao gateway de aplicativo. Os endereços IP, o FQDN ou as NICs desses servidores são adicionados aos pools de endereços de back-end.

### <a name="ip-address-or-fqdn"></a>Endereço IP ou FQDN

1. Com o gateway de aplicativo criado, no painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique no gateway de aplicativo **AdatumAppGateway** na folha Todos os recursos. Se a assinatura selecionada já contiver vários recursos, você poderá inserir **AdatumAppGateway** na caixa **Filtrar por nome...** para acessar facilmente o gateway de aplicativo.

1. O gateway de aplicativo criado é exibido. Clique em **Pools de back-end** e selecione o pool de back-end atual **appGatewayBackendPool**. Isso abrirá a folha **appGatewayBackendPool**.

   ![Pools de back-end do Gateway de Aplicativo][4]

1. Clique em **Adicionar Destino** para adicionar endereços IP de valores de FQDN. Escolha **Endereço IP ou FQDN** como o **Tipo** e insira o endereço IP ou o FQDN no campo. Repita esse procedimento para outros membros do pool de back-end. Quando terminar, clique em **Salvar**.

### <a name="virtual-machine-and-nic"></a>Máquina virtual e NIC

Também é possível adicionar NICs de máquina virtual como membros do pool de back-end. Somente as máquinas virtuais que estão dentro da mesma rede virtual que o Gateway de Aplicativo ficarão disponíveis na lista suspensa.

1. Com o gateway de aplicativo criado, no painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique no gateway de aplicativo **AdatumAppGateway** na folha Todos os recursos. Se a assinatura selecionada já contiver vários recursos, você poderá inserir **AdatumAppGateway** na caixa **Filtrar por nome...** para acessar facilmente o gateway de aplicativo.

1. O gateway de aplicativo criado é exibido. Clique em **Pools de back-end** e selecione o pool de back-end atual **appGatewayBackendPool**. Isso abrirá a folha **appGatewayBackendPool**.

   ![Pools de back-end do Gateway de Aplicativo][5]

1. Clique em **Adicionar Destino** para adicionar endereços IP de valores de FQDN. Escolha **Máquina Virtual** como o **Tipo** e selecione a máquina virtual e a NIC a serem usadas. Quando terminar, clique em **Salvar**

   > [!NOTE]
   > Somente as máquinas virtuais que estão na mesma rede virtual do gateway de aplicativo ficarão disponíveis na caixa suspensa.

## <a name="delete-all-resources"></a>Excluir todos os recursos

Para excluir todos os recursos criados neste artigo, conclua as seguintes etapas:

1. No painel **Favoritos** do portal do Azure, clique em **Todos os recursos**. Clique no grupo de recursos **AdatumAppGatewayRG** na folha Todos os recursos. Se a assinatura selecionada já contiver vários recursos, você poderá inserir **AdatumAppGatewayRG** na caixa **Filtrar por nome...** para acessar o grupo de recursos facilmente.
1. Na folha **AdatumAppGatewayRG**, clique no botão **Excluir**.
1. O portal requer que você digite o nome do grupo de recursos para confirmar se deseja excluí-lo. Clique em **Excluir**, digite AdatumAppGateway para o nome do grupo de recursos e, em seguida, clique em **Excluir**. A exclusão de um grupo de recursos exclui todos os recursos contidos nele e, portanto, confirme sempre o conteúdo de um grupo de recursos antes de excluí-lo. O portal exclui todos os recursos contidos no grupo de recursos e o exclui em seguida. Esse processo leva vários minutos.

## <a name="next-steps"></a>Próximas etapas

Este cenário cria um gateway de aplicativo padrão. A próxima etapa é configurar o gateway de aplicativo modificando as configurações e ajustando as regras no gateway. Essas etapas podem ser encontradas nos artigos a seguir:

Saiba como criar investigações de integridade personalizados visitando [Criar uma investigação de integridade personalizada](application-gateway-create-probe-portal.md)

Saiba como configurar o Descarregamento de SSL e levar a descriptografia SSL cara longe dos seus servidores Web visitando [Configurar Descarregamento de SSL](application-gateway-ssl-portal.md)

Saiba como proteger seus aplicativos com o [Firewall do Aplicativo Web](application-gateway-webapplicationfirewall-overview.md), um recurso do gateway de aplicativo.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

