---
title: "Soluções de armazenamento do Azure | Microsoft Docs"
description: "As soluções de gerenciamento incluem cenários de gerenciamento de empacotados no Azure que os clientes podem adicionar ao espaço de trabalho do Log Analytics.  Este artigo fornece detalhes sobre soluções personalizadas criadas por clientes e parceiros."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d46b869815fef44a8137bb5121133a1c0140ca30
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="working-with-management-solutions-in-azure-preview"></a>Trabalhar com soluções de gerenciamento no Azure (versão prévia)
> [!NOTE]
> Esta é documentação preliminar para as soluções de gerenciamento no Azure, que atualmente estão em versão prévia.    
> 
> 

As soluções de gerenciamento incluem cenários de gerenciamento empacotados que os clientes podem adicionar ao ambiente do Azure.  Além [soluções fornecidas pela Microsoft](../log-analytics/log-analytics-add-solutions.md), parceiros e clientes podem criar soluções de gerenciamento para serem usadas em seu próprio ambiente ou disponibilizadas para os clientes por meio da comunidade.

## <a name="finding-and-installing-management-solutions"></a>Localizando e instalando soluções de gerenciamento
Há vários métodos para localizar e instalar as soluções de gerenciamento, conforme descrito nas seções a seguir.

### <a name="azure-marketplace"></a>Azure Marketplace
Soluções de gerenciamento fornecidos pela Microsoft e parceiros confiáveis podem ser instaladas no Azure Marketplace no portal do Azure.

1. Faça logon no Portal do Azure.
2. No painel esquerdo, selecione **Todos os serviços**.
3. Role para baixo até **soluções** ou digite *soluções* na caixa de diálogo **Filtrar**.
4. Clique no botão **+ Adicionar**.
5. Procure soluções nas quais você está interessado ao navegar, clicar no botão **Filtrar** ou digitar na caixa **Pesquisar Tudo**.
6. Clique em um item do Marketplace para exibir as informações detalhadas.
7. Clique em **Criar** para abrir o painel **Adicionar solução**.
8. Você será solicitado a fornecer informações necessárias, como o [Espaço de trabalho do Log Analytics e a Conta de automação](#log-analytics-workspace-and-automation-account), além dos valores para quaisquer parâmetros na solução.
9. Clique em **Criar** para instalar a solução.

### <a name="oms-portal"></a>Portal do OMS
Soluções de gerenciamento fornecidas pela Microsoft podem ser instaladas da Galeria de Soluções no portal do OMS.

1. Entre no portal do OMS.
2. Clique no bloco **Galeria de Soluções**.
3. Na página Galeria de Soluções do OMS, saiba mais sobre cada solução disponível. Clique no nome da solução que deseja adicionar.
4. Na página para a solução que você escolheu, são exibidas informações detalhadas sobre a solução. Clique em **Adicionar**.
5. Um novo bloco para a solução adicionada aparecerá na página de visão geral no Portal e você poderá começar a utilizá-lo, depois que o Log Analytics processar os dados.

### <a name="azure-quickstart-templates"></a>Modelos de início rápido do Azure
Os membros da comunidade podem enviar soluções de gerenciamento para Modelos de Início Rápido do Azure.  Você pode baixar esses modelos para instalação posterior ou inspecioná-los para saber como [criar suas próprias soluções](#creating-a-solution).

1. Siga o processo descrito no [Espaço de trabalho do Log Analytics e na Conta de automação](#log-analytics-workspace-and-automation-account) para vincular um espaço de trabalho e uma conta.
2. Acesse os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).  
3. Pesquise uma solução na qual você esteja interessado.
4. Selecione a solução nos resultados para exibir seus detalhes.
5. Clique no botão **Implantar no Azure**.
6. Você será solicitado a fornecer informações como o grupo de recursos e a localização, além de valores para parâmetros na solução.
7. Clique em **Comprar** para instalar a solução.

### <a name="deploy-azure-resource-manager-template"></a>Implantar o modelo do Azure Resource Manager
Soluções que você obtêm da comunidade ou que você [cria por conta própria](#creating-a-solution) são implementadas como um modelo do Resource Manager e você pode usar qualquer um dos métodos padrão para [implantar um modelo](../azure-resource-manager/resource-group-template-deploy-portal.md).  Observe que, antes de instalar a solução, você deve criar e vincular o [Espaço de trabalho do Log Analytics e a conta de automação](#log-analytics-workspace-and-automation-account).

## <a name="log-analytics-workspace-and-automation-account"></a>Espaço de trabalho do Log Analytics e Conta de automação
A maioria das soluções de gerenciamento requer que um [espaço de trabalho do Log Analytics](../log-analytics/log-analytics-manage-access.md) contenha modos de exibição e que uma [Conta de automação](../automation/automation-security-overview.md#automation-account-overview) contenha runbooks e recursos relacionados. O espaço de trabalho e a conta devem atender aos seguintes requisitos.

* Uma solução pode usar somente um espaço de trabalho do Log Analytics e uma conta de automação.  
* O espaço de trabalho do Log Analytics e a conta de automação usados por uma solução devem ser vinculados uns aos outros. Um espaço de trabalho do Log Analytics só pode ser vinculado a uma Conta de automação e uma Conta de automação só pode ser vinculada a um espaço de trabalho do Log Analytics.
* Para ser vinculado, o espaço de trabalho do Log Analytics e a Conta de automação devem estar no mesmo grupo de recursos e região.  A exceção é um espaço de trabalho do Log Analytics na região Leste dos EUA e a Conta de automação no Leste dos EUA 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Criar um vínculo entre um espaço de trabalho do Log Analytics e a Conta de automação
Como você especifica o espaço de trabalho do Log Analytics e Conta de automação depende do método de instalação para sua solução.

* Quando você instala uma solução do Microsoft através do Portal do OMS, ele é instalado no espaço de trabalho atual e nenhuma Conta de automação é necessária.
* Ao instalar uma solução através do Azure Marketplace, você será solicitado para um espaço de trabalho e uma Conta de automação, e o vínculo entre eles será criado para você.  
* Para soluções fora do Azure Marketplace, você deverá vincular o espaço de trabalho do Log Analytics e a Conta de automação antes de instalar a solução.  Você poderá fazer isso selecionando qualquer solução no Azure Marketplace e selecionando o espaço de trabalho do Log Analytics e a Conta de automação.  Não é necessário efetivamente instalar a solução porque o vínculo será criado assim que o espaço de trabalho do Log Analytics e a conta Automação estiverem selecionados.  Depois que o vínculo é criado, você poderá usar esse espaço de trabalho do Log Analytics e a Conta de automação para qualquer solução. 

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verificar o vínculo entre um espaço de trabalho do Log Analytics e uma Conta de automação
Você pode verificar o vínculo entre um espaço de trabalho do Log Analytics e uma Conta de automação usando o procedimento a seguir.

1. Selecione a conta de automação no portal do Azure.
2. Se a configuração **Espaço de trabalho** na seção **Recursos relacionados** do menu estiver habilitada, essa conta será anexada a um espaço de trabalho do Log Analytics.  Você pode clicar no **Espaço de trabalho** para exibir os detalhes do espaço de trabalho.

## <a name="listing-management-solutions"></a>Listando as soluções de gerenciamento
Use o procedimento a seguir para exibir as soluções de gerenciamento em espaços de trabalho vinculados à sua assinatura do Azure.

1. Faça logon no Portal do Azure.
2. No painel esquerdo, selecione **Todos os serviços**.
3. Role para baixo até **soluções** ou digite *soluções* na caixa de diálogo **Filtrar**.
4. Soluções instaladas em todos os seus espaços de trabalho serão listadas.

Observe que você pode exibir apenas as soluções da Microsoft instaladas no espaço de trabalho atual usando o portal do OMS.

## <a name="removing-a-management-solution"></a>Removendo uma solução de gerenciamento
Quando uma solução de gerenciamento é removida, todos os recursos da solução também são removidos.  

1. Localize a solução no portal do Azure usando o procedimento em [Listando soluções](#listing-solutions).
2. Selecione a solução que você deseja remover.
3. Clique no botão **Excluir** .

## <a name="creating-a-management-solution"></a>Criando uma solução de gerenciamento
Diretrizes completas sobre como criar soluções de gerenciamento estão disponíveis em [Criando soluções no OMS (Operations Management Suite)](operations-management-suite-solutions-creating.md). 

## <a name="next-steps"></a>Próximas etapas
* Pesquise entre os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates) para obter exemplos de diferentes modelos do Resource Manager.
* Crie suas próprias [soluções de gerenciamento](operations-management-suite-solutions-creating.md).

