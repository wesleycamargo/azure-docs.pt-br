---
title: Organizar seus recursos com grupos de gerenciamento do Azure | Microsoft Docs
description: Saiba mais sobre os grupos de gerenciamento e como usá-los.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/31/2018
ms.author: rithorn
ms.openlocfilehash: edc57d146ccb034ac3fd627386000a1953b0e558
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480315"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizar seus recursos com grupos de gerenciamento do Azure

Se sua organização tiver muitas assinaturas, talvez seja necessária uma maneira de gerenciar com eficiência o acesso, as políticas e a conformidade dessas assinaturas. Os grupos de gerenciamento do Azure fornecem um nível de escopo acima das assinaturas. Você organiza assinaturas em contêineres chamados "grupos de gerenciamento" e aplica as condições de governança aos grupos de gerenciamento. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as condições aplicadas ao grupo de gerenciamento. Os grupos de gerenciamento fornecem gerenciamento de nível empresarial em larga escala, independentemente do tipo de assinaturas que você possa ter.

Por exemplo, aplique políticas a um grupo de gerenciamento que limite as regiões disponíveis para a criação de VM (máquina virtual). Essa política seria aplicada a todos os grupos de gerenciamento, assinaturas e recursos nesse grupo de gerenciamento, permitindo que as VMs fossem criadas nessa região.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarquia de grupos de gerenciamento e assinaturas

É possível compilar uma estrutura flexível de grupos de gerenciamento e assinaturas para organizar seus recursos em uma hierarquia para políticas unificadas e gerenciamento de acesso.
O diagrama a seguir mostra um exemplo de criação de uma hierarquia para governança usando grupos de gerenciamento.

![árvore](media/management-groups/MG_overview.png)

Ao criar uma hierarquia como este exemplo, você pode aplicar uma política, por exemplo, locais de VMs limitados à Região Oeste dos EUA no grupo “Grupo de gerenciamento de equipe de infraestrutura” para habilitar políticas internas de conformidade e segurança. Essa política herdará as assinaturas do EA nesse grupo de gerenciamento e será aplicada a todas as VMs nessas assinaturas. Como essa política herda do grupo de gerenciamento para as assinaturas, essa política de segurança não pode ser alterada pelo recurso ou pelo proprietário da assinatura, permitindo uma governança aprimorada.

Outro cenário em que você usaria grupos de gerenciamento é fornecer acesso de usuário a várias assinaturas.  Ao mover várias assinaturas nesse grupo de gerenciamento, você pode criar uma atribuição de RBAC no grupo de gerenciamento, que herdará esse acesso a todas as assinaturas.  Sem a necessidade de fazer script de atribuições de RBAC em várias assinaturas, uma atribuição no grupo de gerenciamento pode permitir que os usuários tenham acesso a tudo o que precisam.

### <a name="important-facts-about-management-groups"></a>Fatos importantes sobre os grupos de gerenciamento

- 10.000 grupos de gerenciamento podem ter suporte em um único diretório.
- Uma árvore do grupo de gerenciamento pode dar suporte a até seis níveis de profundidade.
  - Esse limite não inclui o nível Raiz nem o nível da assinatura.
- Cada grupo de gerenciamento e assinatura pode dar suporte a apenas um pai.
- Cada grupo de gerenciamento pode ter vários elementos filhos.
- Todas as assinaturas e todos os grupos de gerenciamento estão contidos em uma única hierarquia em cada diretório. Confira [Fatos importantes sobre o Grupo de gerenciamento raiz](#important-facts-about-the-root-management-group) para obter as exceções durante a Versão Prévia.

## <a name="root-management-group-for-each-directory"></a>Grupo de gerenciamento raiz para cada diretório

Cada diretório recebe um único grupo de gerenciamento de nível superior chamado grupo de gerenciamento "Raiz". Esse grupo de gerenciamento raiz é compilado na hierarquia para que todos os grupos de gerenciamento e assinaturas sejam dobrados nele. Esse grupo de gerenciamento raiz permite que políticas globais e atribuições de RBAC sejam aplicadas no nível de diretório. O Administrador de Diretório [precisa elevar-se](../role-based-access-control/elevate-access-global-admin.md) para ser o proprietário desse grupo raiz inicialmente. Quando o administrador for o proprietário do grupo, ele poderá atribuir qualquer função de RBAC a outros usuários ou grupos do diretório para gerenciar a hierarquia.  

### <a name="important-facts-about-the-root-management-group"></a>Fatos importantes sobre o grupo de gerenciamento raiz

- O nome e a ID do grupo de gerenciamento raiz são fornecidos por padrão. O nome de exibição pode ser atualizado a qualquer momento para mostrar diferentes no Portal do Azure.
  - O nome será "Grupo raiz de locatário".
  - A ID será a ID do Azure Active Directory.
- O grupo de gerenciamento raiz não pode ser movido nem excluído, ao contrário de outros grupos de gerenciamento.  
- Todas as assinaturas e todos os grupos de gerenciamento estão inseridos no único grupo de gerenciamento raiz dentro do diretório.
  - Todos os recursos do diretório estão inseridos no grupo de gerenciamento raiz para o gerenciamento global.
  - As novas assinaturas usam como padrão automaticamente o grupo de gerenciamento raiz quando criadas.
- Todos os clientes do Azure podem ver o grupo de gerenciamento raiz, mas nem todos os clientes têm acesso para gerenciá-lo.
  - Qualquer pessoa que tenha acesso a uma assinatura pode ver o contexto no qual essa assinatura está na hierarquia.  
  - Ninguém recebeu o acesso padrão ao grupo de gerenciamento raiz. Os Administradores Globais do Diretório são os únicos usuários que podem se elevar para obter acesso.  Depois de terem acesso, os administradores de diretório podem atribuir qualquer função RBAC a outros usuários para gerenciamento.  

>[!NOTE]
>Se o diretório começou a usar o serviço de grupos de gerenciamento antes de 25/6/2018, o diretório pode não estar configurado com todas as assinaturas na hierarquia. A equipe do grupo de gerenciamento está atualizando retroativamente cada diretório que começou a usar grupos de gerenciamento no Public Preview antes dessa data em julho/agosto de 2018. Todas as assinaturas dos diretórios se tornarão filho no grupo de gerenciamento raiz anterior.  
>
>Em caso de dúvidas sobre esse processo retroativo, contate managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>Configuração inicial dos grupos de gerenciamento

Quando um usuário começa a usar grupos de gerenciamento, ocorre um processo de configuração inicial. A primeira etapa é a criação do grupo de gerenciamento raiz no diretório. Depois que esse grupo é criado, todas as assinaturas existentes no diretório se tornam filho do grupo de gerenciamento raiz.  O motivo para esse processo é verificar se há apenas uma hierarquia de grupo de gerenciamento em um diretório.  A única hierarquia dentro do diretório permite que os clientes administrativos apliquem políticas e o acesso global que outros clientes no diretório não podem ignorar. Tudo o que foi atribuído na raiz será aplicado a todos os grupos de gerenciamento, todas as assinaturas, todos os grupos de recursos e todos os recursos do diretório, tendo uma hierarquia dentro do diretório.  

> [!IMPORTANT]
> Qualquer atribuição de acesso de usuário ou atribuição de política no grupo de gerenciamento raiz **se aplica a todos os recursos do diretório**. Devido a isso, todos os clientes devem avaliar a necessidade de ter os itens definidos nesse escopo.  As atribuições de acesso do usuário e de política devem ser "Deve ter" somente nesse escopo.  
  
## <a name="management-group-access"></a>Acesso do grupo de gerenciamento

Os grupos de gerenciamento do Azure dão suporte ao [RBAC (Controle de Acesso Baseado em Função) do Azure](../role-based-access-control/overview.md) para todos os acessos de recursos e definições de função. Essas permissões são herdadas de recursos filho existentes na hierarquia. Qualquer função RBAC interna pode ser atribuída a um grupo de gerenciamento que herdará a hierarquia para os recursos.  Por exemplo, o colaborador da VM com a função RBAC pode ser atribuído a um grupo de gerenciamento. Essa função não tem nenhuma ação no grupo de gerenciamento, mas herdará de todas as VMs nesse grupo de gerenciamento.  

O gráfico a seguir mostra a lista de funções e as ações compatíveis nos grupos de gerenciamento.

| Nome da função RBAC             | Criar | Renomear | Mover | Excluir | Atribuir acesso | Atribuir política | Ler  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Proprietário                       | X      | X      | X    | X      | X             | X             | X     |
|Colaborador                 | X      | X      | X    | X      |               |               | X     |
|Colaborador de MG*             | X      | X      | X    | X      |               |               | X     |
|Leitor                      |        |        |      |        |               |               | X     |
|MG leitor*                  |        |        |      |        |               |               | X     |
|Colaborador da política de recurso |        |        |      |        |               | X             |       |
|Administrador de Acesso do Usuário   |        |        |      |        | X             |               |       |

*: O MG Contributor e o MG Reader só permitem que os usuários executem essas ações no escopo do grupo de gerenciamento.  

### <a name="custom-rbac-role-definition-and-assignment"></a>Atribuição e definição de função RBAC personalizada

No momento, não há suporte para funções RBAC personalizadas em grupos de gerenciamento.  Confira o [fórum de comentários do grupo de gerenciamento](https://aka.ms/mgfeedback) para exibir o status desse item.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre grupos de gerenciamento, consulte:

- [Criar grupos de gerenciamento para organizar recursos do Azure](management-groups-create.md)
- [Como alterar, excluir ou gerenciar grupos de gerenciamento](management-groups-manage.md)
- [Instalar o módulo Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Revisar as especificações API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Instalar a extensão CLI do Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)
