---
title: Gerenciar os custos do AWS e o uso no gerenciamento de custos do Azure
description: Este artigo ajuda você a entender como usar os orçamentos e análise de custo no gerenciamento de custos para gerenciar os custos do AWS e uso.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 007b6c409dde248a4dde7a15fd16b543add234bc
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870306"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Gerenciar os custos do AWS e o uso no Azure

Depois de você ter instalado e configurado a integração de relatório de uso e custo do AWS para o gerenciamento de custos do Azure, você estará pronto para começar a gerenciar os custos do AWS e o uso. Este artigo ajuda você a entender como usar os orçamentos e análise de custo no gerenciamento de custos para gerenciar os custos do AWS e uso.

Se você ainda não tiver configurado a integração, consulte [definido para cima e para configurar a integração de relatório de uso do AWS](aws-integration-set-up-configure.md).

_Antes de começar_: Se você estiver familiarizado com a análise de custo, consulte o [explorar e analisar os custos com análise de custo](quick-acm-cost-analysis.md) guia de início rápido. E, se você estiver familiarizado com orçamentos no Azure, consulte a [criar e gerenciar orçamentos Azure](tutorial-acm-create-budgets.md) tutorial.

## <a name="view-aws-costs-in-cost-analysis"></a>Exibir os custos do AWS na análise de custo

Os custos do AWS estão disponíveis na análise de custo nos seguintes escopos:

- Contas do AWS vinculado em um grupo de gerenciamento
- Custos de conta do AWS vinculado
- Custos de conta do AWS consolidados

As seções a seguir descrevem como usar os escopos para que você veja os dados de uso e custo para cada uma delas.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Exibir contas do AWS vinculado em um grupo de gerenciamento

Exibindo os custos usando o escopo do grupo de gerenciamento é a única maneira de ver os custos agregados provenientes de diferentes assinaturas e contas vinculadas. Usar um grupo de gerenciamento fornece uma exibição de nuvem.

Na análise de custo, abra o seletor de escopo e selecione o grupo de gerenciamento que contém suas contas do AWS vinculado. Aqui está uma imagem de exemplo no portal do Azure:

![Exemplo de como o modo de exibição de escopo de Select](./media/aws-integration-manage/select-scope01.png)



Aqui está um exemplo que mostra o custo do grupo de gerenciamento de análise de custo, agrupado por provedor (do Azure e AWS).

![Exemplo mostrando os custos do Azure e AWS para análise de custo de um trimestre em](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Exibir custos de conta do AWS vinculado

Para exibir os custos de conta de link do AWS, abra o seletor de escopo e selecione a AWS conta vinculada. Observe que as contas vinculadas são associadas a um grupo de gerenciamento, conforme definido no conector do AWS.

Aqui está um exemplo que mostra a seleção de um AWS vinculada escopo da conta.

![Exemplo de como o modo de exibição de escopo de Select](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Exibição AWS consolidados os custos de conta

Para exibir o AWS conta consolidada custos, abra o seletor de escopo e selecione a AWS consolidados conta. Aqui está um exemplo que mostra a seleção de um AWS consolidados escopo da conta.

![Exemplo de como o modo de exibição de escopo de Select](./media/aws-integration-manage/select-scope03.png)



Esse escopo fornece que uma exibição agregada de todas as AWS vinculado contas associadas à conta AWS consolidados. Aqui está um exemplo que mostra os custos para um AWS consolidados conta, agrupada por nome do serviço.

![Exemplo mostrando AWS consolidados custos na análise de custo](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensões disponíveis para filtragem e agrupamento

A tabela a seguir descreve as dimensões disponíveis para agrupar e filtrar na análise de custo.

| Dimensão | Cabeçalho CUR Amazon | Escopos | Comentários |
| --- | --- | --- | --- |
| Zona de disponibilidade | lineitem/AvailabilityZone | Todos |   |
| Local padrão | produto/região | Todos |   |
| Medidor |   | Todos |   |
| Medir categoria | lineItem/ProductCode | Todos |   |
| Medidor de subcategoria | lineitem/UsageType | Todos |   |
| Operação | lineItem/operação | Todos |   |
| Resource | lineItem/ResourceId | Todos |   |
| Tipo de recurso | product/instanceType | Todos | Se o produto/instanceType for nulo, lineItem/UsageType será usado. |
| ResourceGuid | N/D | Todos | GUID de medidor do Azure. |
| Nome do serviço | produto/ProductName | Todos | Se o produto/ProductName for nulo, lineItem/ProductCode será usado. |
| Camada de serviço |   |   |   |
| ID da assinatura | lineItem/UsageAccountId | Grupo de conta e gerenciamento consolidado |   |
| Nome da assinatura | N/D | Grupo de conta e gerenciamento consolidado | Nomes de conta são coletados usando a API de organização do AWS. |
| Marca | resourceTags/\* | Todos | O _usuário:_ prefixo é removido do marcas definidas pelo usuário para permitir que as marcas de nuvem. O _aws:_ prefixo permanecem intacto. |
| ID da conta de cobrança | bill/PayerAccountId | Grupo de gerenciamento |   |
| Nome da conta de cobrança | N/D | Grupo de gerenciamento | Nomes de conta são coletados usando a API de organização do AWS. |
| Provedor | N/D | Grupo de gerenciamento | AWS ou no Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Orçamentos de conjunto de escopos de AWS

Use os orçamentos para gerenciar proativamente os custos e aumentar responsabilidade em sua organização. Orçamentos são definidos na conta AWS consolidados e AWS escopos conta vinculada. Aqui está um exemplo dos orçamentos de uma conta do AWS consolidados mostrado no gerenciamento de custos:

![Exemplo mostrando os orçamentos de um AWS consolidados conta](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Processo de coleta de dados do AWS

Depois de configurar o conector do AWS, coleta de dados e processos de descoberta em Iniciar. Pode levar algumas horas para coletar todos os dados de uso. A duração depende:

- O tempo necessário para processar arquivos CUR que estão no bucket de S3 do AWS.
- O tempo necessário para criar a conta AWS consolidados e escopos da conta AWS vinculado.
- O tempo e a frequência do AWS são gravar os arquivos de relatório de uso e custo no bucket de S3

## <a name="aws-integration-pricing"></a>Preços de integração do AWS

Cada conector AWS obtém 90 dias de avaliação gratuitos. Durante a visualização pública, não há nenhum custo.

O preço é de 1% de seus custos mensais do AWS. Cada mês será cobrado com base em seus custos faturados do mês anterior.

Acesso às APIs do AWS pode incorrer em custos adicionais.

## <a name="aws-integration-limitations"></a>Limitações de integração do AWS

- Gerenciamento de custos não dá suporte a relatórios de custo que contêm vários tipos de moeda. Uma mensagem de erro é mostrada se você selecionar um escopo que tenha várias moedas.
- Conectores de nuvem não oferecem suporte a AWS China, Gov AWS ou GovCloud do AWS (EUA).
- Gerenciamento de custos mostra AWS _os custos de uso_ apenas. Imposto, suporte, reembolsos, RI, créditos ou outros tipos de custo ainda não têm suporte.

## <a name="troubleshooting-aws-integration"></a>Solucionar problemas de integração do AWS

Use as seguintes informações de solução de problemas para resolver problemas comuns.

### <a name="no-permission-to-aws-linked-accounts"></a>Sem permissão para as contas vinculadas do AWS

Há duas maneiras de obter permissões para acessar os custos de contas do AWS vinculado:

- Obtenha acesso ao grupo de gerenciamento que contenha as contas vinculadas do AWS.
- Ter alguém lhe dar permissão à conta AWS vinculado.

Por padrão, o criador de conector do AWS é o proprietário de todos os objetos que o conector criado. O AWS, incluindo conta consolidada e o AWS conta vinculada.

### <a name="collection-failed-with-assumerole"></a>Coleção falhou com o AssumeRole

Esse erro significa que o gerenciamento de custos não é possível chamar a API de AssumeRole AWS. Esse problema pode ocorrer devido a um problema com a definição de função. Verifique se que as seguintes condições forem verdadeiras:

- A ID externa é o mesmo que da definição de função e a definição de conector.
- O tipo de função é definido como **AWS outra conta que pertence a você ou terceiros 3ª.**
- O **exigir MFA** opção está desmarcada.
- É a conta AWS confiável na função do AWS _432263259397_.

### <a name="collection-failed-with-access-denied"></a>Coleção falhou com acesso negado

Essa mensagem de erro significa que o gerenciamento de custos não é possível acessar os arquivos CUR armazenados no bucket do Amazon S3. Certifique-se de que a política de AWS JSON anexada à função semelhante ao exemplo mostrado na parte inferior a [criar uma função e a política no AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) seção.

### <a name="connector-error-with-failedtofindreport"></a>Erro do conector com FailedToFindReport

Esse erro significa que o gerenciamento de custos não é possível localizar o relatório de uso e custo que foi definido no conector. Verifique se ele não é excluído e a política de AWS JSON anexada à função é semelhante ao exemplo mostrado na parte inferior da [criar uma função e a política no AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) seção.

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não tiver configurado seu ambiente do Azure com grupos de gerenciamento, consulte [instalação de grupos de gerenciamento inicial](../governance/management-groups/index.md#initial-setup-of-management-groups).
