---
title: Criar uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure
description: Este artigo orienta você quanto às etapas para criar uma definição de política para identificar recursos sem conformidade.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 9b1cfd762d6f050994688d1cd6afd91f0e43bfc3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Criar uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos. Este guia de início rápido orienta você no processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados.

No final deste processo, você identificará com êxito quais máquinas virtuais não estão usando discos gerenciados. Eles *não estão em conformidade* com a atribuição da política.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, você cria uma atribuição de política e atribuímos a definição da política *Auditar máquinas virtuais sem Managed Disks*.

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

   ![Pesquisar pela política](media/assign-policy-definition/search-policy.png)

2. Selecione **Atribuições** no lado esquerdo da página de Política do Azure. Uma atribuição é uma política que foi atribuída para entrar em vigor em um escopo específico.
3. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   ![Atribuir uma definição de política](media/assign-policy-definition/select-assign-policy.png)

4. Na página **Atribuir Política**, selecione o **Escopo** clicando no botão de reticências e selecionando uma assinatura (obrigatório) e o grupo de recursos (opcional). Um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta.  Em seguida, clique em **Selecionar** na parte inferior da página **Escopo**.

   Este exemplo usa a **Assinatura Contoso**. Sua assinatura será diferente.

5. Se você quiser excluir um ou mais grupos de recursos (se você apenas tiver um escopo de uma assinatura) ou recursos específicos dentro de um grupo de recursos (qualquer um dos casos de escopo), você poderá configurar **exclusões** de atribuição de política. Deixar em branco.

6. Selecione a reticências **Definição de política** para abrir a lista de definições. A Política do Azure vem com definições de políticas internas que você pode usar. Muitas definições de política internas estão disponíveis, como:

   - Impor marca e seu valor
   - Aplicar marca e seu valor
   - Requer o SQL Server versão 12.0

    Para uma lista completa de todas as políticas internas disponíveis, veja [Modelos de política](json-samples.md).

7. Pesquise a lista de definições de política para encontrar a definição *Auditar VMs que não usam discos gerenciados*. Clique na política e clique em **Selecionar**.

   ![Encontre a definição de política correta](media/assign-policy-definition/select-available-definition.png)

8. O **Nome da atribuição** é automaticamente preenchido com o nome da política selecionada, mas você pode alterá-lo. Neste exemplo, deixe *Auditar VMs que não usam discos gerenciados*. Você também pode adicionar uma **Descrição**opcional. A descrição fornece detalhes sobre essa atribuição de política.

9. Clique em **Atribuir**.

Agora, você está pronto para identificar recursos sem conformidade para compreender o estado de conformidade de seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos sem conformidade

Selecione **Conformidade** no lado esquerdo da página e localize as **VMs de auditoria que não usam a atribuição**  de política de discos gerenciados que você criou.

![Conformidade da política](media/assign-policy-definition/policy-compliance.png)

Se houver recursos sem conformidade com essa nova atribuição, aparecem em **Recursos sem conformidade**.

Quando uma condição é avaliada em relação a seus recursos existentes e resulta ser verdadeira, então esses recursos são marcados como em não conformidade com a política. A tabela a seguir mostra como os diferentes efeitos da política funcionam com a avaliação da condição para o estado de conformidade resultante: Embora você não veja a lógica de avaliação no portal do Azure, os resultados do estado de conformidade são mostrados. O resultado do estado de conformidade pode ser ou compatível ou incompatível.

| **Estado do recurso** | **Efeito** | **Avaliação da política** | **Estado de conformidade** |
| --- | --- | --- | --- |
| Exists | Negar, Auditoria, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Sem conformidade |
| Exists | Negar, Auditoria, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Em conformidade |
| Novo | Auditoria, AuditIfNotExist\* | True | Sem conformidade |
| Novo | Auditoria, AuditIfNotExist\* | Falso | Em conformidade |

\* Os efeitos de Acrescentar, DeployIfNotExist e AuditIfNotExist exigem que a instrução IF seja TRUE. Os efeitos também exigem que a condição de existência seja FALSE para não estar em conformidade. Quando TRUE, a condição IF dispara a avaliação da condição de existência para os recursos relacionados.

## <a name="clean-up-resources"></a>Limpar recursos

Outros guias desta coleção dão continuidade a este guia de início rápido. Se você planeja continuar trabalhando com os tutoriais subsequentes, não limpe os recursos criados neste guia de início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no Portal do Azure.

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página e localize as VMs de auditoria que não usam a atribuição  de política de discos gerenciados que você criou.

2. Clique com o botão direito na atribuição de política de **VMs de auditoria que não usam discos gerenciados** e selecione **Excluir atribuição**

   ![Excluir uma atribuição](media/assign-policy-definition/delete-assignment.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para um escopo e avaliou seu relatório de conformidade. A definição de política garante que todos os recursos no escopo estão em conformidade e identifica quais não estão.

Para saber mais sobre a atribuição de políticas, para garantir que recursos que você criar no **futuro** estejam em conformidade, continue com o tutorial:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](create-manage-policy.md)