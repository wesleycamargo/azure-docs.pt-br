---
title: Acessar fontes de dados locais para Aplicativo Lógico do Azure | Microsoft Docs
description: Criar e configurar o gateway de dados local para poder acessar fontes de dados locais de aplicativos lógicos
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: e8e8d85d2c95c1dda7271de72491594562b7d3c1
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413699"
---
# <a name="connect-to-data-sources-on-premises-from-azure-logic-apps-with-on-premises-data-gateway"></a>Conectar a fontes de dados locais do Aplicativo Lógico do Azure com gateway de dados local

Para acessar fontes de dados locais dos aplicativos lógicos, é possível criar um recurso de gateway de dados no Azure para que os aplicativos lógicos possam usar [conectores locais](../logic-apps/logic-apps-gateway-install.md#supported-connections). Este artigo mostra como criar o recurso de gateway do Azure *após* você [baixar e instalar o gateway no computador local](../logic-apps/logic-apps-gateway-install.md). 

Para obter informações sobre como usar o gateway com outros serviços, consulte estes artigos:

* [Gateway de dados local do Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Gateway de dados local do Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Gateway de dados local do Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Gateway de dados local do Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Pré-requisitos

* Você já [baixou e instalou o gateway de dados em um computador local](../logic-apps/logic-apps-gateway-install.md).

* A instalação de gateway não está associada a um recurso de gateway no Azure. É possível vincular a instalação do gateway apenas a um recurso de gateway, o que acontece quando você cria o recurso de gateway e seleciona a instalação do gateway. Essa vinculação torna a instalação do gateway indisponível para outros recursos.

* Quando você entra no portal do Azure e cria o recurso de gateway, use a mesma conta de entrada usada anteriormente para [instalar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md#requirements) juntamente com a mesma [assinatura do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer) usada para instalar o gateway. Caso você ainda não tenha uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Para criar e manter o recurso de gateway no portal do Azure, a [conta de serviço Windows](../logic-apps/logic-apps-gateway-install.md#windows-service-account) precisa ter pelo menos permissões de **Colaborador**. O gateway de dados local é executado como um serviço do Windows e configurado para usar `NT SERVICE\PBIEgwService` para as credenciais de logon do serviço Windows. 

  > [!NOTE]
  > A conta de serviço Windows é diferente da conta usada para conectar fontes de dados locais e conta corporativa ou de estudante do Azure usada para entrar nos serviços de nuvem.

## <a name="download-and-install-gateway"></a>Baixar e instalar o gateway

Antes de continuar com as etapas deste artigo, verifique se você já tem o gateway instalado em um computador local.
Caso ainda não o tenha, siga as etapas para [baixar e instalar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Criar recurso do Azure para gateway

Após instalar o gateway em um computador local, será possível criar um recurso do Azure para o gateway. Essa etapa também associa o recurso de gateway à sua assinatura do Azure.

1. Entre no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>. Certifique-se de usar o mesmo endereço de email corporativo ou de estudante do Azure usado para instalar o gateway.

2. No menu principal do Azure, selecione **Criar um recurso** > 
**Integração** > **Gateway de dados local**.

   ![Localize “Gateway de dados local”](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Na página **Criar gateway de conexão**, forneça estas informações para o recurso de gateway:

   | Propriedade | DESCRIÇÃO | 
   |----------|-------------|
   | **Nome** | O nome do recurso de gateway | 
   | **Assinatura** | O nome da assinatura do Azure, que deve ser a mesma assinatura do aplicativo lógico. A assinatura padrão é baseada na conta do Azure que você usou para entrar. | 
   | **Grupo de recursos** | O nome do [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) para organizar recursos relacionados | 
   | **Localidade** | O Azure restringe esse local à mesma região que foi selecionada para o serviço de nuvem do gateway durante a [instalação de gateway](../logic-apps/logic-apps-gateway-install.md). <p>**Observação**: certifique-se de que esse local de recurso do gateway corresponda ao local do serviço de nuvem do gateway. Caso contrário, a instalação do gateway poderá não aparecer na lista de gateways instalados para você selecionar na próxima etapa. Você pode usar diferentes regiões para o recurso de gateway e para seu aplicativo lógico. | 
   | **Nome de Instalação** | Se a instalação do gateway não estiver selecionada, selecione o gateway que você instalou anteriormente. | 
   | | | 

   Veja um exemplo:

   ![Fornecer detalhes para criar o gateway de dados local](./media/logic-apps-gateway-connection/createblade.png)

4. Para adicionar o recurso de gateway ao painel do Azure, selecione **Fixar no painel**. Quando terminar, escolha **Criar**.

   Para localizar ou exibir o gateway a qualquer momento, no menu principal do Azure, selecione **Todos os serviços**. 
   Na caixa de pesquisa, digite "gateways de dados locais" e selecione **Gateways de Dados Locais**.

   ![Localize "Gateways de Dados Locais"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Conectar-se a dados locais

Após criar o recurso de gateway e associar a assinatura do Azure a esse recurso, será possível criar uma conexão entre o aplicativo lógico e a fonte de dados local usando o gateway.

1. No portal do Azure, crie ou abra o aplicativo lógico no Designer do Aplicativo Lógico.

2. Adicione um conector que dê suporte a conexões locais, por exemplo **SQL Server**.

3. Agora, configure a conexão:

   1. Selecione **Conectar por meio do gateway de dados local**. 

   2. Para **Gateways**, selecione o recurso de gateway criado anteriormente. 

      Embora o local da conexão do gateway deva existir na mesma região do aplicativo lógico, é possível selecionar um gateway em uma região diferente.

   3. Forneça um nome de conexão exclusivo e as outras informações necessárias. 

      O nome de conexão exclusivo ajudará a identificar facilmente essa conexão posteriormente, especialmente ao criar várias conexões. Se aplicável, também inclua o domínio qualificado para seu nome de usuário.
   
      Veja um exemplo:

      ![Criar a conexão entre o aplicativo lógico e o gateway de dados](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Quando terminar, escolha **Criar**. 

A conexão de gateway agora está pronta para o aplicativo lógico usar.

## <a name="edit-connection"></a>Editar conexão

Depois de criar uma conexão de gateway para seu aplicativo lógico, você talvez queira atualizar as configurações para essa conexão específica.

1. Localize a conexão de gateway:

   * Para localizar todas as conexões de API apenas para o aplicativo lógico, no menu do aplicativo lógico, em **Ferramentas de Desenvolvimento**, selecione **Conexões de API**. 
   
     ![Vá para seu aplicativo lógico e selecione "Conexões de API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Para localizar todas as conexões de API associadas à assinatura do Azure: 

     * No menu principal do Azure, acesse **Todos os serviços** > **Web** > **Conexões de API**. 
     * Ou, no menu principal do Azure, acesse **Todos os recursos**.

2. Selecione a conexão de gateway que você quer e, em seguida, escolha **Editar conexão de API**.

   > [!TIP]
   > Se suas atualizações não entrarem em vigor, tente [interromper e reiniciar o serviço Windows do gateway](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Excluir recurso de gateway

Para criar um recurso de gateway diferente, associar seu gateway a um recurso diferente ou remover o recurso de gateway, exclua o recurso de gateway sem afetar a instalação do gateway. 

1. No menu principal do Azure, acesse **Todos os recursos**. 

2. Localize e selecione o recurso de gateway.

3. Se ainda não estiver selecionado, no menu de recursos do gateway, selecione **Gateway de Dados Local**. 

4. Na barra de ferramentas de recursos, escolha **Excluir**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas frequentes

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Proteja seus aplicativos lógicos](./logic-apps-securing-a-logic-app.md)
* [Exemplos comuns e cenários de aplicativos lógicos](./logic-apps-examples-and-scenarios.md)
