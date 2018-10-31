---
title: Conectar redes virtuais do Azure de Aplicativos Lógicos do Azure por meio de um ambiente do serviço de integração (ISE)
description: Criar um ambiente do serviço de integração (ISE) para que os aplicativos lógicos e contas de integração possam acessar as redes virtuais do Azure, enquanto permanece privado e isolado do Azure público ou “global”
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/25/2018
ms.openlocfilehash: b4e4e801c3c54b635f2f13b319257018ea544c03
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404113"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>Conectar redes virtuais do Azure de Aplicativos Lógicos do Azure por meio de um ambiente do serviço de integração (ISE)

> [!NOTE]
> Essa funcionalidade está em *versão prévia privada*. Para solicitar acesso, [crie sua solicitação para ingressar aqui](https://aka.ms/iseprivatepreview).

Para cenários de integração onde seus aplicativos lógicos e contas de integração precisam acessar a uma[rede virtual do Azure](../virtual-network/virtual-networks-overview.md), criar um [ *ambiente do serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que é um ambiente privado e isolado que usa armazenamento dedicado e outros recursos mantidos separados do público ou serviços dos *Aplicativos* Lógicos globais. Essa separação também reduz os impactos que outros locatários do Azure podem ter no desempenho de seus aplicativos. Você pode vincular este ISE à sua rede virtual do Azure, que, em seguida, implanta o serviço de aplicativos lógicos em sua rede virtual. Quando cria aplicativos lógicos e contas de integração, você seleciona esse ISE como sua localização. Sua conta de integração ou de aplicativo lógico, em seguida, pode acessar diretamente os recursos, como máquinas virtuais (VMs), servidores, sistemas e serviços em sua rede virtual. 

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Este artigo mostra como executar estas tarefas:

* Configure permissões em sua rede virtual do Azure para que a instância privada dos Aplicativos Lógicos possa acessar sua rede virtual.

* Crie seu ISE (ambiente de serviço de integração). 

* Crie um aplicativo lógico que pode ser executado no ISE. 

* Crie uma conta de integração para seus aplicativos lógicos no ISE.

Para obter mais informações sobre os ambientes do serviço de integração, consulte [Acesso aos recursos da VNET (rede virtual) do Azure do Aplicativo Lógico do Azure isolado](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* Se ainda não tiver uma rede virtual do Azure, aprenda como [criar uma](../virtual-network/quick-create-portal.md). 

  > [!IMPORTANT]
  > Embora não precise de uma rede virtual do Azure para criar seu ambiente, você pode selecionar uma rede virtual como par do seu ambiente *apenas* quando você cria o ambiente. 

* Para dar aos aplicativos lógicos acesso direto a sua rede virtual do Azure, [configure permissões de RBAC (controle de acesso baseado em função)](#vnet-access) para que o serviço dos Aplicativos Lógicos tenha as permissões para acessar sua rede virtual. 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Definir permissões de rede virtual

Quando cria o ambiente de serviço de integração, você pode selecionar uma rede virtual do Azure como *par* para o ambiente. No entanto, só é possível executar essa etapa, ou *emparelhamento*, ao criar o ambiente. Essa relação permite que o serviço de Aplicativos Lógicos se conecte diretamente aos recursos na rede virtual e dá a esses recursos acesso a seu ambiente. 

Antes que possa selecionar sua rede virtual, você deve configurar permissões de RBAC (controle de acesso baseado em função) na rede virtual. Para concluir essa tarefa, você precisa atribuir funções específicas ao serviço de Aplicativos Lógicos do Azure.

1. No [portal do Azure](https://portal.azure.com), encontre e selecione a rede virtual. 

1. No menu da rede virtual, selecione **IAM (Controle de acesso)**. 

1. Em **Controle de Acesso**, selecione **Atribuição de função** se ainda não estiver selecionado. Na barra de ferramentas **Atribuição de função**, escolha **Adicionar**. 

   ![Adicionar atribuições de função](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. No painel **Adicionar permissões**, configure cada função na tabela para o serviço de Aplicativos Lógicos do Azure. Certifique-se de escolher **Salvar** depois de concluir cada função:

   | Função | Atribuir acesso a | Selecionar | 
   |------|------------------|--------|
   | **Colaborador de rede** | **Usuário, grupo ou aplicativo do Azure AD** | Entre nos **Aplicativos Lógicos do Azure**. Quando a lista de membros for exibida, selecione o mesmo valor. <p>**Dica**: se não encontrar o serviço, insira a ID do aplicativo do serviço de Aplicativos Lógicos: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **Colaborador Clássico** | **Usuário, grupo ou aplicativo do Azure AD** | Entre nos **Aplicativos Lógicos do Azure**. Quando a lista de membros for exibida, selecione o mesmo valor. <p>**Dica**: se não encontrar o serviço, insira a ID do aplicativo do serviço de Aplicativos Lógicos: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

   Por exemplo: 

   ![Adicionar permissões](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   Para saber mais sobre as permissões de função exigidas para o emparelhamento, confira a [seção Permissões em Criar, alterar ou excluir um emparelhamento de rede virtual](../virtual-network/virtual-network-manage-peering.md#permissions). 

Se sua rede virtual estiver conectada por meio do Azure ExpressRoute, do VPN de ponto a site do Azure ou do VPN de site a site do Azure, continue para a próxima seção para que você possa adicionar a sub-rede de gateway necessária. Caso contrário, prossiga com [Criar seu ambiente](#create-environment).

<a name="add-gateway-subnet"></a>

## <a name="add-gateway-subnet-for-virtual-networks-with-expressroute-or-vpns"></a>Adicionar sub-rede de gateway para redes virtuais com VPNs ou ExpressRoute

Depois de concluir as etapas anteriores, para dar a seu ISE (ambiente de serviço de integração) acesso a uma rede virtual do Azure conectada por meio do [Azure ExpressRoute](../expressroute/expressroute-introduction.md), do [VPN de ponto a site do Azure](../vpn-gateway/point-to-site-about.md) ou do [VPN de site a site do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), você também precisará adicionar uma [*sub-rede de gateway*](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub) à sua rede virtual:

1. No [portal do Azure](https://portal.azure.com), encontre e selecione a rede virtual. No menu da rede virtual, selecione **Sub-redes** e, em seguida, escolha **Sub-rede de gateway** > **OK**.

   ![Adicionar sub-rede de gateway](./media/connect-virtual-network-vnet-isolated-environment/add-gateway-subnet.png)

1. Agora, crie uma [*tabela de rotas*](../virtual-network/manage-route-table.md), que você associará à sub-rede de gateway que criou anteriormente.

   1. No menu principal do Azure, selecione **Criar um recurso** > 
   **Rede** > **Tabela de rotas**.

      ![Criar tabela de rotas](./media/connect-virtual-network-vnet-isolated-environment/create-route-table.png)

   1. Forneça informações sobre a tabela de rotas, como o nome, a assinatura do Azure a ser usada, o grupo de recursos do Azure e o local. Certifique-se de que a propriedade **Propagação de rotas BGP** esteja definida como **Habilitado** e, em seguida, escolha **Criar**.

      ![Forneça detalhes sobre a tabela de rotas](./media/connect-virtual-network-vnet-isolated-environment/enter-route-table-information.png)

   1. No menu da tabela de rotas, selecione **Sub-redes** e, em seguida, escolha **Associar**. 

      ![Conecte a tabela de rotas à sub-rede](./media/connect-virtual-network-vnet-isolated-environment/associate-route-table.png)

   1. Selecione **Rede virtual** e, em seguida, selecione sua rede virtual.
   
   1. Selecione **Sub-rede** e, em seguida, selecione a sub-rede gateway criada anteriormente.

   1. Quando terminar, escolha **OK**.

1. Se você tiver uma VPN de ponto a site, conclua também estas etapas:

   1. No Azure, encontre e selecione seu recurso de gateway de rede virtual.

   1. No menu do gateway, selecione **Configuração de ponto a site**. 
   e, em seguida, escolha **Baixar cliente VPN** para que você tenha a configuração mais recente do cliente VPN.

      ![Baixe o cliente VPN mais recente](./media/connect-virtual-network-vnet-isolated-environment/download-vpn-client.png)

Você concluiu a configuração de uma sub-rede de gateway para redes virtuais que usam ExpressRoute, VPNs de ponto a site ou VPNs de site a site. Para continuar criando seu ambiente de serviço de integração, siga as próximas etapas.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Criar seu ISE

Para criar seu ISE (ambiente de serviço de integração), siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **Criar um recurso**.

   ![Criar novo recurso](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Na caixa de pesquisa, digite “ambiente de serviço de integração” como filtro.
Na lista de resultados, selecione **Ambiente de Serviço de Integração (versão prévia)** e, em seguida, escolha **Criar**.

   ![Selecione “Ambiente de Serviço de Integração”](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Escolha “Criar”](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Forneça estes detalhes para o ambiente:

   ![Fornecer detalhes do ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriedade | Obrigatório | Valor | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **Nome** | SIM | <*environment-name*> | O nome que o ambiente terá | 
   | **Assinatura** | SIM | <*Azure-subscription-name*> | A assinatura do Azure a ser usada para o ambiente | 
   | **Grupo de recursos** | SIM | <*Azure-resource-group-name*> | O grupo de recursos do Azure no qual deseja criar seu ambiente |
   | **Localidade** | SIM | <*Azure-datacenter-region*> | A região do datacenter do Azure na qual armazenar informações sobre o ambiente |
   | **Rede Virtual do Par** | Não  | <*Azure-VNET-name*> | A rede virtual do Azure a associar ao seu ambiente como *par* de modo que os aplicativos lógicos nesse ambiente possam acessar sua rede virtual. Antes que possa criar essa relação, verifique se você já [configurou o controle de acesso baseado em função em sua rede virtual para os Aplicativos Lógicos do Azure](#vnet-access). <p>**Importante**: embora não seja obrigatório ter uma rede virtual, você pode selecionar uma rede virtual *somente* ao criar seu ambiente. | 
   | **Nome do Emparelhamento** | Sim, com uma rede virtual selecionada | <*peering-name*> | O nome a ser atribuído à relação de pares | 
   | **Intervalo de IP de Rede Virtual** | Sim, com uma rede virtual selecionada | <*IP-address-range*> | O intervalo de endereços IP a ser usado para criar recursos em seu ambiente. Esse intervalo deve usar o [Formato CIDR (Roteamento entre Domínios sem Classe)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), por exemplo, 10.0.0.1/16, e requer um espaço de endereço de Classe B. O intervalo não pode existir no espaço de endereço da rede virtual selecionado na propriedade **Rede Virtual do Par**, nem em nenhum outro endereço IP em que a rede de pares está conectada, seja por emparelhamento ou por gateways. <p><p>**Importante**: você *não pode alterar* este intervalo de endereços depois de criar seu ambiente. |
   |||||
   
1. Quando terminar, escolha **Criar**. 

   O Azure começa a implantar seu ambiente, mas esse processo pode levar *até duas horas* para ser concluído. 
   Para verificar o status de implantação, na barra de ferramentas do Azure, escolha o ícone de notificações, o que abre o painel de notificações.

   ![Verifique o status da implantação](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Quando a implantação for concluída com êxito, o Azure mostrará esta notificação:

   ![Implantação bem-sucedida](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. Para exibir seu ambiente, escolha **Ir para o recurso** se o Azure não for automaticamente para seu ambiente após a conclusão da implantação.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Criar aplicativo lógico – ISE

Para criar aplicativos lógicos que usam o ISE (ambiente de serviço de integração), siga as etapas comuns em [como criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md), mas com estas diferenças e considerações: 

* Quando você cria o aplicativo lógico, a propriedade **Local** lista seus ISEs em **Ambientes do serviço de integração**, junto com as regiões disponíveis. Selecione seu ISE, em vez de uma região, por exemplo:

  ![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Você pode usar os mesmos recursos internos, como a ação ou o gatilho HTTP, que são executados no mesmo ISE que o aplicativo lógico pai. Conectores com o rótulo **ISE** também são executados no mesmo ISE que o aplicativo lógico pai. Os conectores sem o rótulo do **ISE** são executados no serviço global dos Aplicativos Lógicos.

  ![Selecionar conectores ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Caso você tenha configurado seu ISE com uma rede virtual do Azure como um par, os aplicativos lógicos no ISE poderão acessar diretamente os recursos na rede virtual. Para sistemas locais em uma rede virtual vinculada a um ISE, os aplicativos lógicos podem ter acesso direto a esses sistemas usando um destes itens: 

  * Conector ISE para o sistema, por exemplo, SQL Server

  * Ação HTTP 

  * Conector personalizado

  Para sistemas locais que não estão em uma rede virtual ou não têm conectores ISE, ainda é possível se conectar após [configurar e usar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Criar conta de integração – ISE

Para usar uma conta de integração com aplicativos lógicos em um ISE (ambiente de serviço de integração), essa conta de integração deverá usar o *mesmo ambiente* que os aplicativos lógicos. Os aplicativos lógicos em um ISE podem fazer referência somente às contas de integração no mesmo ISE. 

Para criar uma conta de integração que usa um ISE, siga as etapas comuns em [como criar contas de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), exceto pela propriedade **Local**, que agora lista seu ISEs em **Ambientes do serviço de integração**, junto com as regiões disponíveis. Selecione seu ISE, em vez de uma região, por exemplo:

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Fórum dos Aplicativos Lógicos do Azure</a>.
* Para enviar ou votar em ideias de recurso, visite o <a href="http://aka.ms/logicapps-wish" target="_blank">site de comentários do usuário de Aplicativos Lógicos</a>.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
