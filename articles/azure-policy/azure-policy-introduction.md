---
title: "Visão geral da Política do Azure | Microsoft Docs"
description: "A Política do Azure é um serviço no Azure que você pode usar para criar, atribuir e gerenciar definições de política em seu ambiente do Azure."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders; nini
ms.date: 01/17/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: bb6f08e8e6252233c51c47c63ca0aec4636247dd
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="what-is-azure-policy"></a>O que é a Política do Azure?

A governança de TI cria clareza entre projetos de TI e metas de negócios. A boa governança de TI envolve planejar suas iniciativas e prioridades de configuração em um nível estratégico. A empresa tem um número significativo de problemas de TI que parecem nunca ser resolvidos? Implementar políticas ajuda a gerenciar e impedir esses problemas com mais eficiência. É na implementação de políticas que entra o Azure Policy.

A Política do Azure é um serviço no Azure que você pode usar para criar, atribuir e gerenciar definições de política. As definições de política aplicam diferentes regras e ações sobre seus recursos para que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço. O Azure Policy executa uma avaliação dos recursos, verificando aqueles que não estão em conformidade com as definições de política que você tem. Por exemplo, você pode ter uma política que permita somente determinados tipos de máquinas virtuais. Outra que exija que todos os recursos tenham uma marca específica. Essas políticas então são avaliadas durante a criação e a atualização de recursos.

## <a name="how-is-it-different-from-rbac"></a>Qual é a diferença dela em relação ao RBAC?

Há algumas diferenças importantes entre a política e o RBAC (controle de acesso baseado em função). O RBAC concentra-se nas ações do usuário em escopos diferentes. Por exemplo, você pode ser adicionado à função de colaborador de um grupo de recursos no escopo desejado. A função permite que você faça alterações àquele grupo de recursos. Política concentra-se nas propriedades de recurso durante a implantação e para recursos já existentes. Por exemplo, por meio de políticas, você pode controlar os tipos de recursos que podem ser provisionados. Ou você pode restringir os locais em que os recursos podem ser provisionados. Ao contrário do RBAC, a política é um sistema de permissão padrão e negação explícita.

Para usar políticas, você deve estar autenticado pelo RBAC. Especificamente, a conta precisa de:

- Permissão do `Microsoft.Authorization/policydefinitions/write` para definir uma política.
- Permissão do `Microsoft.Authorization/policyassignments/write` para atribuir uma política.

Essas permissões não estão incluídas na função **Colaborador**.


## <a name="policy-definition"></a>Definição de política

Cada definição de política tem condições sob as quais ela é imposta. E ela tem uma ação de acompanhamento que ocorrerá se as condições forem atendidas.

Na Política do Azure, oferecemos algumas políticas internas que estão disponíveis para você por padrão. Por exemplo: 

- **Requer o SQL Server 12.0**: essa definição de política tem condições/regras para garantir que todos os servidores SQL usem a versão 12.0. A ação é negar todos os servidores que não atendam a esses critérios.
- **SKUs de contas de armazenamento permitidas**: esta definição de política tem um conjunto de condições/regras que determinam se uma conta de armazenamento que está sendo implantada está dentro de um conjunto de tamanhos de SKU. Sua ação é negar a todos os servidores que não são compatíveis com o conjunto de tamanhos de SKU definido.
- **Tipo de Recurso Permitido**: esta definição de política tem um conjunto de condições/regras para especificar os tipos de recursos que sua organização pode implantar. Sua ação é negar a todos os recursos que não fazem parte dessa lista definida.
- **Locais permitido**: esta política permite que você restrinja os locais que sua organização pode especificar ao implantar recursos. A ação é usada para impor seus requisitos de conformidade de área geográfica.
- **SKUs de Máquina Virtual permitidas**: esta política permite que você especifique um conjunto de SKUs de máquina virtual que sua organização pode implantar.
- **Aplicar marca e seu valor padrão**: esta política aplica uma marca obrigatória e seu valor padrão, se não for especificado pelo usuário.
- **Impor a marca e seu valor**: esta política impõe uma marca necessária e seu valor para um recurso.
- **Tipos de recurso não permitidos**: essa política permite que você especifique os tipos de recurso que sua organização não consegue implantar.

Você pode atribuir qualquer uma dessas políticas usando o portal do Azure, o PowerShell ou a CLI do Azure.

Para saber mais sobre as estruturas das definições de políticas, consulte este artigo – [Estrutura da definição de política](policy-definition.md).

## <a name="policy-assignment"></a>Atribuição de política

Uma atribuição de política é uma definição de política que foi atribuída para entrar em vigor em um escopo específico. Esse escopo pode variar de um grupo de gerenciamento para um grupo de recursos. O termo *escopo* refere-se a todos os grupos de recursos, assinaturas ou grupos de gerenciamento aos quais a definição de política esteja atribuída. Atribuições de política são herdadas por todos os recursos filho. Então, se uma política for aplicada a um grupo de recursos, ela será aplicada a todos os recursos desse grupo de recursos. No entanto, você pode excluir um subescopo da atribuição de política. Por exemplo, no escopo da assinatura, você pode atribuir uma política que impede a criação de recursos de rede. No entanto, você exclui um grupo de recursos dentro da assinatura que se destina à infraestrutura de rede. Você concede acesso a esse grupo de recursos de rede a usuários em que você confia para criar recursos de rede.

Para obter mais informações sobre a configuração de definições e atribuições de política, consulte [Criar uma atribuição de política para identificar recursos que não estão em conformidade no seu ambiente do Azure](assign-policy-definition.md).

## <a name="policy-parameters"></a>Parâmetros de política

Parâmetros de política ajudam a simplificar o gerenciamento de política, reduzindo o número de definições de política que você precisa criar. Você pode definir parâmetros ao criar uma definição de política para torná-la mais genérica. Então você pode reutilizar essa definição de política para cenários diferentes. Faça isso passando valores diferentes ao atribuir a definição de política. Por exemplo, especificar um conjunto de locais para uma assinatura.

Parâmetros são definidos/criados durante a criação de uma definição de política. Quando um parâmetro é definido, ele recebe um nome e, opcionalmente, um valor. Por exemplo, é possível definir um parâmetro para uma política intitulada *local*. Em seguida, você poderá atribuir valores diferentes, como *EastUS* ou *WestUS* ao atribuir uma política.

<!--
Next link should point to new Concept page for Parameters
-->
Para obter mais informações sobre parâmetros de política, consulte [Visão geral da política de recurso – parâmetros](policy-definition.md#parameters).

## <a name="initiative-definition"></a>Definição de iniciativa
Uma definição de iniciativa é a coleção de definições de política que são adaptadas para atingirem uma única meta abrangente. Definições de iniciativa simplificam o gerenciamento e a atribuição de definições da política. Elas simplificam agrupando um conjunto de políticas como um único item. Por exemplo, você pode criar uma iniciativa intitulada **Habilitar Monitoramento na Central de Segurança do Azure**, com uma meta para monitorar todas as recomendações de segurança disponíveis na Central de Segurança do Azure.

Com essa iniciativa, você teria definições de política como:

1. **Monitorar Banco de Dados SQL não criptografado na Central de Segurança** – para monitoramento de servidores e Bancos de Dados SQL não criptografados.
2. **Monitorar as vulnerabilidades do sistema operacional na Central de Segurança** – para monitoramento de servidores que não satisfazem a linha de base configurada.
3. **Monitorar Endpoint Protection ausente na Central de Segurança** – para monitoramento de servidores sem um agente de Endpoint Protection instalado.

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>Atribuição de iniciativa
Assim como uma atribuição de política, uma atribuição iniciativa é uma definição de iniciativa atribuída a um escopo específico. Atribuições de iniciativa reduzem a necessidade de fazer várias definições de iniciativa para cada escopo. Esse escopo também pode variar de um grupo de gerenciamento a um grupo de recursos.

No exemplo anterior, a iniciativa **Habilitar monitoramento na Central de Segurança do Azure** pode ser atribuída a escopos diferentes. Por exemplo, uma atribuição pode ser feita à **subscriptionA**. Outra pode ser feita à **subscriptionB**.

## <a name="initiative-parameters"></a>Parâmetros de iniciativa
Assim como parâmetros de política, os parâmetros de iniciativa ajudam a simplificar o gerenciamento iniciativa reduzindo a redundância. Parâmetros de iniciativa são essencialmente a lista de parâmetros que estão sendo usados pelas definições de política dentro da iniciativa.

Por exemplo, suponha um cenário em que você tem uma definição de iniciativa -**initiativeC** com duas definições de política. Cada definição de política com um parâmetro definido:

| Política | nome do parâmetro |Tipo do parâmetro  |Observação |
|---|---|---|---|
| policyA | allowedLocations | matriz  |Esse parâmetro espera uma lista de cadeias de caracteres para um valor, pois o tipo de parâmetro foi definido como uma matriz |
| policyB | allowedSingleLocation |string |Esse parâmetro espera uma palavra para um valor, pois o tipo de parâmetro foi definido como uma cadeia de caracteres |

Nesse cenário, ao definir os parâmetros de iniciativa para **initiativeC**, você tem três opções:

1. Use os parâmetros das definições de política dentro desta iniciativa: neste exemplo, *allowedLocations* e *allowedSingleLocation* tornam-se parâmetros de iniciativa para **initiativeC**.
2. Forneça valores para os parâmetros das definições de política dessa definição de iniciativa. Neste exemplo, você pode fornecer uma lista de localizações para **parâmetro de policyA – allowedLocations** e **parâmetro de policyB – allowedSingleLocation**.
Você também pode fornecer valores ao atribuir essa iniciativa.
3. Forneça uma lista de opções de *valor* que podem ser usadas ao atribuir essa iniciativa. Ao atribuir essa iniciativa, os parâmetros herdados das definições de política dentro da iniciativa só poderão ter valores dessa lista fornecida.

Por exemplo, você pode criar uma lista de opções de valor em uma definição de iniciativa que contenha *EastUS*, *WestUS*, *CentralUS* e *WestEurope*. Se for assim, não será possível inserir um valor diferente como *Sudeste Asiático* durante a atribuição iniciativa, porque ele não faz parte da lista.

## <a name="recommendations-for-managing-policies"></a>Recomendações para o gerenciamento de políticas

Ao criar e gerenciar atribuições e definições de política, aqui estão algumas diretrizes que aconselhamos você a seguir:

- Se você estiver criando definições de política em seu ambiente, é recomendável começar com um efeito de auditoria, em vez de um efeito de negação, para acompanhar o impacto da definição de política sobre os recursos no seu ambiente. Se você já tem scripts em vigor para expansão automática de seus aplicativos, configurar um efeito de negação pode atrapalhar as tarefas de automação que você já tem em vigor.
- É importante manter em mente as hierarquias organizacionais ao criar definições e atribuições. É recomendável criar definições em nível de assinatura ou em um nível mais alto, por exemplo, no grupo de gerenciamento ou no nível de assinatura, bem como atribuir no próximo nível filho. Por exemplo, se você criar uma definição de política no nível do grupo de gerenciamento, o escopo de uma atribuição de política dessa definição poderá ser limitado a um nível de assinatura dentro daquele grupo de gerenciamento.
- É recomendável usar o tipo de preço padrão para entender melhor o estado de conformidade do ambiente. Para obter mais informações sobre os modelos de preços e o que cada um deles oferece, dê uma olhada [Preço](https://azure.microsoft.com/pricing/details/azure-policy).
- É recomendável sempre usar definições de iniciativa em vez de definições de política, mesmo se você tiver apenas uma política em mente. Por exemplo, se você tiver uma definição de política – *policyDefA* e criá-la sob a definição de iniciativa – *initiativeDefC*, se você optar por criar outra definição de política mais tarde para *policyDefB* com metas similares às de *policyDefA*, poderá adicioná-la sob *initiativeDefC* e acompanhá-la melhor dessa maneira.

   Tenha em mente que, depois de criar uma atribuição de iniciativa de uma definição iniciativa, quaisquer novas definições de política adicionadas à definição da iniciativa serão automaticamente colocadas nas atribuições de iniciativa subjacentes àquela definição de iniciativa. No entanto, se houver um novo parâmetro introduzido na nova definição de política, você precisará atualizar a definição e as atribuições de iniciativa editando a definição ou a atribuição da iniciativa.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral da Política do Azure e alguns dos principais conceitos que estamos introduzindo, aqui estão as próximas etapas sugeridas:

- [Atribuir uma definição de política](./assign-policy-definition.md)
- [Atribuir uma definição de política usando a CLI do Azure](./assign-policy-definition-cli.md)
- [Atribuir uma definição de política usando o PowerShell](./assign-policy-definition-ps.md)
