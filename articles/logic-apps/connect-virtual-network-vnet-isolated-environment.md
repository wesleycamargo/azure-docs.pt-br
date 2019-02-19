---
title: Conectar redes virtuais do Azure a partir dos Aplicativos Lógicos do Azure por meio de um ISE (Ambiente de Serviço de Integração)
description: Criar um ISE (Ambiente de Serviço de Integração) para que os aplicativos lógicos e contas de integração possam acessar as VNets (redes virtuais) do Azure, enquanto permanecem privados e isolados do Azure público ou “global”
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 02/12/2019
ms.openlocfilehash: 8d7fc6d8f581c3ad0e0f3266ea615acadcb7bc25
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176196"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Conectar redes virtuais do Azure a partir dos Aplicativos Lógicos do Azure, usando um ISE (Ambiente de Serviço de Integração)

> [!NOTE]
> Essa funcionalidade está em *versão prévia privada*. Para ingressar na versão prévia privada, [crie sua solicitação aqui](https://aka.ms/iseprivatepreview).

Para cenários em que seus aplicativos lógicos e contas de integração precisam de acesso a uma [Rede virtual do Azure](../virtual-network/virtual-networks-overview.md), crie um [*ISE* (Ambiente de Serviço de Integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Um ISE é um ambiente privado e isolado que usa armazenamento dedicado e outros recursos mantidos separadamente do serviço público ou "global" dos Aplicativos Lógicos. Essa separação também reduz os impactos que outros locatários do Azure podem ter no desempenho de seus aplicativos. Seu ISE é *injetado* na rede virtual do Azure, que, em seguida, implanta o serviço de Aplicativos Lógicos em sua rede virtual. Quando cria aplicativos lógicos e contas de integração, você seleciona esse ISE como sua localização. Sua conta de integração ou de aplicativo lógico, em seguida, pode acessar diretamente os recursos, como máquinas virtuais (VMs), servidores, sistemas e serviços em sua rede virtual.

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Este artigo mostra como concluir essas tarefas:

* Configure portas na rede virtual do Azure para que o tráfego possa percorrer o ISE (Ambiente de Serviço de Integração) em sub-redes na rede virtual.

* Configure permissões em sua rede virtual do Azure para que a instância privada dos Aplicativos Lógicos possa acessar sua rede virtual.

* Crie seu ISE (Ambiente de Serviço de Integração).

* Crie um aplicativo lógico que pode ser executado no ISE.

* Crie uma conta de integração para seus aplicativos lógicos no ISE.

Para obter mais informações sobre os ambientes do serviço de integração, confira [Acesso aos recursos da Rede Virtual do Azure dos Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

  > [!IMPORTANT]
  > Os aplicativos lógicos, as ações internas e os conectores executados no ISE usam um plano de preços diferente, e não um plano de preços com base no consumo. Para obter mais informações, consulte [Preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md).

* Uma [Rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Se ainda não tiver uma rede virtual, aprenda a [criar uma Rede virtual do Azure](../virtual-network/quick-create-portal.md). Também será necessário ter sub-redes na rede virtual para implantar o ISE. É possível criar essas sub-redes antecipadamente ou aguardar até criar o ISE, onde você poderá criar as sub-redes simultaneamente. Além disso, [certifique-se de que a rede virtual disponibiliza essas portas](#ports) para que o ISE funcione corretamente e permaneça acessível.

* Para fornecer aos aplicativos lógicos o acesso direto à rede virtual do Azure, [configure as permissões de RBAC (controle de acesso baseado em função) da rede](#vnet-access) para que o serviço de Aplicativos Lógicos tenha as permissões para acessar a rede virtual.

* Para usar um ou mais servidores DNS personalizados para implantar a rede virtual do Azure, [configure esses servidores seguindo estas diretrizes](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) antes de implantar o ISE na rede virtual. Caso contrário, todas as vezes que você alterar o servidor DNS, será necessário também reiniciar o ISE, que é um recurso disponível na visualização pública do ISE.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="set-up-network-ports"></a>Configurar portas de rede

Para funcionar corretamente e permanecer acessível, o ISE (Ambiente de Serviço de Integração) precisará ter portas específicas disponíveis na rede virtual. Caso contrário, se alguma dessas portas estiver indisponível você poderá perder o acesso ao ISE que, por consequência, poderá parar de funcionar. Ao usar um ISE em uma rede virtual, um problema comum de configuração é ter uma ou mais portas bloqueadas. Para conexões entre o ISE e o sistema de destino, o conector usado também poderá ter seus próprios requisitos de porta. Por exemplo, ao comunicar-se com um sistema FTP usando o conector FTP, certifique-se de que a porta usada nesse sistema FTP, como a porta 21 para enviar comandos, está disponível.

Para controlar o tráfego de entrada e saída nas sub-redes da rede virtual onde você implanta o ISE, é possível configurar [grupos de segurança de rede](../virtual-network/security-overview.md) para essas sub-redes, aprendendo [como filtrar tráfego em sub-redes](../virtual-network/tutorial-filter-network-traffic.md). Essas tabelas descrevem as portas na rede virtual que o ISE usa e onde essas portas são usadas. O asterisco (*) representa todas e quaisquer origens do tráfego. A [marca de serviço](../virtual-network/security-overview.md#service-tags) representa um grupo de prefixos de endereço IP que ajudam a minimizar a complexidade ao criar regras de segurança.

| Finalidade | Direção | Porta de origem <br>Porta de destino | Marca de serviço de origem <br>Marca de serviço de destino |
|---------|-----------|---------------------------------|-----------------------------------------------|
| Comunicação para Aplicativos Lógicos do Azure <br>Comunicação a partir do Aplicativos Lógicos do Azure | Entrada <br>Saída | * <br>80 & 443 | INTERNET <br>REDE_VIRTUAL |
| Azure Active Directory | Saída | * <br>80 e 443 | REDE_VIRTUAL <br>AzureActiveDirectory |
| Dependência de Armazenamento do Azure | Saída | * <br>80 e 443 | REDE_VIRTUAL <br>Armazenamento |
| Gerenciamento de Conexão | Saída | * <br>443 | REDE_VIRTUAL <br>INTERNET |
| Publicar métricas e logs de diagnóstico | Saída | * <br>443 | REDE_VIRTUAL <br>AzureMonitor |
| Designer de Aplicativos Lógicos - propriedades dinâmicas <br>O histórico de execuções do aplicativo lógico <br>Implantação do conector <br>Ponto de extremidade de gatilho de solicitação | Entrada | * <br>454 | INTERNET <br>REDE_VIRTUAL |
| Dependência de Gerenciamento de Serviço de Aplicativo | Entrada | * <br>454 e 455 | AppServiceManagement <br>REDE_VIRTUAL |
| Gerenciamento de API - ponto de extremidade de gerenciamento | Entrada | * <br>3443 | APIManagement <br>REDE_VIRTUAL |
| Dependência do Log para agente de monitoramento e política do Hub de Eventos | Saída | * <br>5672 | REDE_VIRTUAL <br>EventHub |
| Acessar Instâncias do Cache do Azure para Redis entre Instâncias de Função | Entrada <br>Saída | * <br>6381-6383 | REDE_VIRTUAL <br>REDE_VIRTUAL |
|||||

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Definir permissões de rede virtual

Ao criar um ISE (Ambiente de Serviço de Integração), você seleciona uma rede virtual do Azure na qual *injeta* do ambiente. No entanto, antes que possa selecionar sua rede virtual para injetar o ambiente, você deve configurar permissões de RBAC (controle de acesso baseado em função) na rede virtual. Para configurar permissões, atribua essas funções específicas ao dos Aplicativos Lógicos do Azure:

1. No [portal do Azure](https://portal.azure.com), encontre e selecione a rede virtual.

1. No menu da rede virtual, selecione **IAM (Controle de acesso)**.

1. Em **IAM (Controle de acesso)**, escolha **Adicionar atribuição de função**.

   ![Adicionar funções](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. No painel **Adicionar atribuição de função**, adicione a função necessária para o serviço dos Aplicativos Lógicos do Azure, conforme descrito.

   1. Em **Função**, selecione **Colaborador de Rede**.

   1. Em **Atribuir acesso a**, selecione **Usuário, grupo ou entidade de serviço do Azure AD**.

   1. Em **Selecionar**, insira **Aplicativos Lógicos do Azure**.

   1. Quando a lista de membros for exibida, selecione **Aplicativos Lógicos do Azure**.

      > [!TIP]
      > Se não encontrar o serviço, insira a ID do aplicativo do serviço de Aplicativos Lógicos: `7cd684f4-8a78-49b0-91ec-6a35d38739ba`

   1. Quando terminar, escolha **Salvar**.

   Por exemplo: 

   ![Adicionar atribuição de função](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

Para obter mais informações, confira [Permissões para acesso à rede virtual](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Criar seu ISE

Para criar seu ISE (ambiente de serviço de integração), siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **Criar um recurso**.

   ![Criar novo recurso](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Na caixa de pesquisa, digite “ambiente de serviço de integração” como filtro.
Na lista de resultados, selecione **Ambiente de Serviço de Integração (versão prévia)** e, em seguida, escolha **Criar**.

   ![Selecione “Ambiente de Serviço de Integração”](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Escolha “Criar”](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Forneça esses detalhes para seu ambiente e, em seguida, escolha **Revisar + criar**, por exemplo:

   ![Fornecer detalhes do ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriedade | Obrigatório | Value | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **Assinatura** | Sim | <*Azure-subscription-name*> | A assinatura do Azure a ser usada para o ambiente |
   | **Grupo de recursos** | Sim | <*Azure-resource-group-name*> | O grupo de recursos do Azure no qual deseja criar seu ambiente |
   | **Nome do Ambiente de Serviço de Integração** | Sim | <*environment-name*> | O nome que o ambiente terá |
   | **Localidade** | Sim | <*Azure-datacenter-region*> | A região do datacenter do Azure na qual o ambiente será implantado |
   | **Capacidade adicional** | Sim | 0, 1, 2, 3 | O número de unidades de processamento a serem usadas para esse recurso de ISE |
   | **Rede virtual** | Sim | <*Azure-virtual-network-name*> | A rede virtual do Azure na qual você deseja injetar seu ambiente para que os aplicativos lógicos no ambiente possam acessar sua rede virtual. Se não tiver uma rede, você poderá criar uma aqui. <p>**Importante**: Você pode executar essa injeção *apenas* quando cria seu ISE. No entanto, antes que possa criar essa relação, verifique se você já [configurou o controle de acesso baseado em função em sua rede virtual para os Aplicativos Lógicos do Azure](#vnet-access). |
   | **Sub-redes** | Sim | <*subnet-resource-list*> | Um ISE requer quatro sub-redes *vazias* para criar recursos em seu ambiente. Assim, certifique-se de que essas sub-redes *não estejam delegadas* a nenhum serviço. Você *não pode alterar* esses endereços de sub-rede depois de criar seu ambiente. <p><p>Para criar cada sub-rede, [siga as etapas nesta tabela](#create-subnet). Cada sub-rede deve atender a estes critérios: <p>– Deve estar vazia. <br>– Usa um nome que não começa com um número ou um hífen. <br>– Usa o [formato CIDR (Roteamento entre Domínios sem Classe)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e um espaço de endereço de classe B. <br>– Inclui pelo menos um `/27` no espaço de endereço para que a sub-rede receba pelo menos 32 endereços. Para saber mais sobre como calcular o número de endereços, consulte [blocos CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks). Por exemplo:  <p>- `10.0.0.0/24` tem 256 endereços porque 2<sup>(32-24)</sup> é 2<sup>8</sup> ou 256. <br>- `10.0.0.0/27` tem 32 endereços porque 2<sup>(32-27)</sup> é 2<sup>5</sup> ou 32. <br>- `10.0.0.0/28` tem somente 16 endereços porque 2<sup>(32-28)</sup> é 2<sup>4</sup> ou 16. |
   |||||

   <a name="create-subnet"></a>

   **Criar sub-rede**

   1. Na lista **Sub-redes**, escolha **Gerenciar configuração de sub-rede**.

      ![Gerenciar configuração de sub-rede](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. No painel **Sub-redes**, escolha **Sub-rede**.

      ![Adicionar sub-rede](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. No painel **Adicionar sub-rede**, forneça estas informações.

      * **Nome**: o nome da sub-rede
      * **Intervalo de endereços (bloco CIDR)**: o intervalo da sub-rede em sua rede virtual e no formato CIDR

      ![Adicionar detalhes da sub-rede](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Quando terminar, escolha **OK**.

   1. Repita essas etapas para mais três sub-redes.

1. Depois que o Azure validar com êxito suas informações do ISE, escolha **Criar**; por exemplo:

   ![Após a validação bem-sucedida, escolha "Criar"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   O Azure começa a implantar seu ambiente, mas esse processo pode levar *até duas horas* para ser concluído. 
   Para verificar o status de implantação, na barra de ferramentas do Azure, escolha o ícone de notificações, o que abre o painel de notificações.

   ![Verifique o status da implantação](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se a implantação for concluída com êxito, o Azure mostrará esta notificação:

   ![Implantação bem-sucedida](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > Se a implantação falhar ou se você excluir o ISE, o Azure *poderá* levar até uma hora para liberar as sub-redes. Portanto, você precisará esperar antes de reutilizar essas sub-redes em outro ISE.

1. Para exibir seu ambiente, escolha **Ir para o recurso** se o Azure não for automaticamente para seu ambiente após a conclusão da implantação.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Criar aplicativo lógico – ISE

Para criar aplicativos lógicos que usam o ISE (Ambiente de Serviço de Integração), siga as etapas em [como criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md), mas com estas diferenças: 

* Quando você cria o aplicativo lógico, na propriedade **Location**, selecione seu ISE na seção **Ambientes do serviço de integração**, por exemplo:

  ![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Você pode usar os mesmos gatilhos e ações internos, como HTTP, que são executados no mesmo ISE que o aplicativo lógico. Conectores com o rótulo **ISE** também são executados no mesmo ISE que o aplicativo lógico. Os conectores sem o rótulo do **ISE** são executados no serviço global dos Aplicativos Lógicos.

  ![Selecionar conectores ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Após você injetar seu ISE em uma rede virtual do Azure, os aplicativos lógicos no ISE poderão acessar diretamente os recursos na rede virtual. Para sistemas locais conectados a uma rede virtual, injete um ISE nessa rede para que os aplicativos lógicos possam ter acesso direto a esses sistemas usando um destes itens: 

  * Conector ISE para o sistema, por exemplo, SQL Server
  
  * Ação HTTP 
  
  * Conector personalizado

  Para sistemas locais que não estão em uma rede virtual ou não têm conectores ISE, primeiro [configure o gateway de dados local](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Criar conta de integração – ISE

Para usar uma conta de integração com aplicativos lógicos em um ISE (ambiente de serviço de integração), essa conta de integração deverá usar o *mesmo ambiente* que os aplicativos lógicos. Os aplicativos lógicos em um ISE podem fazer referência somente às contas de integração no mesmo ISE. 

Para criar uma conta de integração que usa um ISE, siga as etapas em [como criar contas de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), exceto pela propriedade **Location**, em que agora a seção **Ambientes do serviço de integração** aparece. Selecione seu ISE, em vez de uma região, por exemplo:

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Fórum dos Aplicativos Lógicos do Azure</a>.
* Para enviar ou votar em ideias de recurso, visite o <a href="https://aka.ms/logicapps-wish" target="_blank">site de comentários do usuário de Aplicativos Lógicos</a>.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
