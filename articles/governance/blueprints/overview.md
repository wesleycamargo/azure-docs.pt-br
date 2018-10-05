---
title: Visão geral do Azure Blueprint
description: O Azure Blueprint é um serviço no Azure usado para criar, definir e implantar os artefatos no seu ambiente do Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: c7ffbe86407bc776870890e5b4151556f572832e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957480"
---
# <a name="what-is-azure-blueprints"></a>O que é o Azure Blueprints?

Assim como uma planta permite que um engenheiro ou arquiteto desenhe os parâmetros de um projeto, o Azure Blueprints permite que arquitetos de nuvem e TI definam um conjunto repetível de recursos do Azure que implementa e adere aos padrões, modelos e requisitos de uma organização. O Azure Blueprints permite que as equipes de desenvolvimento forneçam e criem novos ambientes rapidamente, sabendo que eles são criados dentro da conformidade organizacional e contêm um conjunto de componentes integrados - como redes - para acelerar o desenvolvimento e a entrega.

Blueprints são uma maneira declarativa de orquestrar a implementação de vários modelos de recursos e outros artefatos, como:

- Atribuições de Funções
- Atribuições de Políticas
- Modelos do Azure Resource Manager
- Grupos de recursos

## <a name="how-it-is-different-from-resource-manager-templates"></a>Como é diferente dos modelos do Resource Manager

O Blueprints foi desenvolvido para ajudar na _configuração do ambiente_, que geralmente consiste em um conjunto de grupos de recursos, políticas e atribuições de funções, além de implantações de modelos do Resource Manager. Um blueprint é um pacote para reunir cada um desses _artefatos_ e permitir que você componha e versione esse pacote - inclusive por meio de um pipeline de CI / CD. Por fim, cada um é atribuído a uma assinatura em uma única operação que pode ser auditada e rastreada.

Quase tudo o que você deseja incluir na implantação em Blueprints pode ser feito com um modelo do Resource Manager. No entanto, um modelo do Resource Manager é um documento que não existe nativamente no Azure - cada um é armazenado localmente ou no controle de origem. O modelo é usado para implantações de um ou mais recursos do Azure, mas, quando esses recursos são implantados, a conexão e o relacionamento com o modelo usado são perdidos.

Com o Blueprints, a relação entre a definição do blueprint (o _que deve ser_ implementado) e a atribuição do blueprint (o _que foi_ implementado) permanece. Essa conexão permite rastreamento e auditoria aprimorados de implantações, a capacidade de atualizar várias assinaturas de uma só vez que são governadas pelo mesmo blueprint e muito mais.

Não há necessidade de escolher entre um modelo do Resource Manager e um blueprint. Cada blueprint pode consistir em zero ou mais artefatos _do modelo do Resource Manager_. Isso significa que os esforços anteriores para desenvolver e manter uma biblioteca de modelos do Resource Manager podem ser aproveitados no Blueprints.

## <a name="how-it-is-different-from-azure-policy"></a>Como é diferente da política do Azure

Um blueprint é um pacote ou contêiner para composição de conjuntos específicos de foco de padrões, modelos e requisitos relacionados à implementação de serviços em nuvem, segurança e design do Azure que podem ser reutilizados para garantir consistência e conformidade.

Uma [política](../policy/overview.md) é uma permissão padrão e um sistema de negação explícito focado nas propriedades do recurso durante a implementação e para recursos já existentes. Ele suporta a governança de TI, garantindo que os recursos dentro de uma assinatura cumpram os requisitos e os padrões.

A inclusão de uma política em um blueprint permite não apenas a criação do padrão ou design correto durante a atribuição do blueprint, mas garante que somente mudanças aprovadas ou esperadas possam ser feitas no ambiente para garantir a conformidade contínua com a intenção do blueprint.

Uma política pode ser incluída como um dos muitos _artefatos_ em uma definição de blueprints. Os Blueprints também suportam o uso de parâmetros com políticas e iniciativas.

## <a name="blueprint-definition"></a>Definição do blueprint

Um blueprint é composto por _artefatos_. Blueprints atualmente dão suporte aos recursos a seguir como artefatos:

|Recurso  | Opções de hierarquia| DESCRIÇÃO  |
|---------|---------|---------|
|Grupos de recursos     | Assinatura | Crie um novo grupo de recursos para uso por outros artefatos no blueprint.  Esses grupos de recursos de espaço reservado permitem que você organize os recursos exatamente da maneira que você deseja que eles sejam estruturados e fornecem um limitador de escopo para os artefatos de atribuição de diretivas e funções incluídos, além dos modelos do Azure Resource Manager.         |
|Modelo do Azure Resource Manager      | Grupo de recursos | Esses modelos podem ser usados para compor ambientes complexos, como um farm do SharePoint, a Configuração de Estado de Automação do Azure ou um espaço de trabalho do Log Analytics. |
|Atribuição de política     | Assinatura, Grupo de Recursos | Permite a atribuição de uma política ou iniciativa ao grupo de gerenciamento ou assinatura ao qual o blueprint está atribuído. A política ou iniciativa deve estar dentro do escopo do blueprint (no grupo de gerenciamento de blueprint ou abaixo). Se a política ou iniciativa tiver parâmetros, esses parâmetros poderão ser atribuídos na criação do blueprint ou durante a atribuição do blueprint.       |
|Atribuição de função   | Assinatura, Grupo de Recursos | Adicione um usuário ou grupo existente a uma função interna para garantir que as pessoas certas tenham sempre o acesso correto aos seus recursos. Atribuições de função podem ser definidas para a assinatura inteira ou aninhadas em um grupo de recursos específico incluído no blueprint. |

### <a name="blueprints-and-management-groups"></a>Blueprints e grupos de gerenciamento

Ao criar uma definição de blueprint, você definirá onde o blueprint será salvo. Atualmente, os blueprints só podem ser salvos em um [grupo de gerenciamento](../management-groups/overview.md) ao qual você tenha acesso **Contributor**. O blueprint estará disponível para atribuir a qualquer filho (grupo de gerenciamento ou assinatura) desse grupo de gerenciamento.

> [!IMPORTANT]
> Se você não tiver acesso a nenhum grupo de gerenciamento ou grupo de gerenciamento configurado, carregar a lista de definições de blueprint mostrará que nenhuma está disponível e clicar em **Escopo** abrirá uma janela com um aviso sobre a recuperação de grupos de gerenciamento . Para resolver isso, certifique-se de que uma assinatura à qual você tenha acesso apropriado faça parte de um grupo de [gerenciamento](../management-groups/overview.md).

### <a name="blueprint-parameters"></a>Parâmetros de blueprint

Blueprints podem passar parâmetros para uma iniciativa/política ou um modelo do Azure Resource Manager.
Quando qualquer um dos _artefato_ é adicionada para um blueprint, o autor é capaz de se decidir para fornecer um valor definido para cada atribuição de blueprint ou para permitir que cada atribuição de blueprint forneça um valor em tempo de atribuição. Essa flexibilidade fornece a opção para definir um valor predeterminado para todos os usos do blueprint ou para habilitar essa decisão a ser feita no momento da atribuição.

> [!NOTE]
> Um blueprint pode ter seus próprios parâmetros, mas elas atualmente podem ser criado somente se um blueprint é gerado pela API REST, ao invés de pelo Portal.

Para obter mais informações, consulte [parâmetros de blueprint](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publicação de blueprint

Quando um blueprint é criado, ele é considerado para estar no modo **rascunho**. Quando estiver pronto para ser atribuído, ele precisa ser **Publicado**. A publicação requer a definição de uma cadeia de caracteres da **Versão** (letras, números e hifens com um comprimento máximo de 20 caracteres) juntamente com **Alterar anotações** opcionais.
A **versão** a diferencia de futuras alterações no mesmo blueprint e permite que cada versão seja atribuída. Isso também significa que diferentes **versões** o mesmo projeto podem ser atribuídos à mesma assinatura. Quando são feitas alterações adicionais para o plano gráfico, o **publicado** **versão** persistir, além para o **alterações não publicadas**. Depois que as alterações forem concluídas, o plano gráfico atualizado está **publicado** com um novo e exclusivo **versão** e agora também podem ser atribuídos.

## <a name="blueprint-assignment"></a>Atribuição de blueprint

Cada **publicado** **versão** de um plano gráfico podem ser atribuídos a uma assinatura existente. No portal, o plano gráfico usará como padrão o **versão** àquela **publicado** mais recentemente. Se houver parâmetros de artefatos (ou parâmetros de blueprint), os parâmetros serão definidos durante o processo de atribuição.

## <a name="permissions-in-azure-blueprints"></a>Permissões nos Blueprints do Azure

Para usar blueprints, você deve estar autorizado por meio de [controle de acesso baseado em função](../../role-based-access-control/overview.md) (RBAC). Para criar planos gráficos, sua conta precisa das seguintes permissões:

- `Microsoft.Blueprint/blueprints/write` -Criar uma definição de blueprint
- `Microsoft.Blueprint/blueprints/artifacts/write` -Criar artefatos em uma definição de blueprint
- `Microsoft.Blueprint/blueprints/versions/write` -Publicar um blueprint

Para excluir os blueprint, sua conta precisa das seguintes permissões:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Como as definições de blueprint são criadas em um grupo de gerenciamento, as permissões de definição de blueprint devem ser concedidas em um escopo de grupo de gerenciamento ou ser herdadas para um escopo de grupo de gerenciamento.

Para atribuir ou desatribuir um blueprint, sua conta precisa das seguintes permissões:

- `Microsoft.Blueprint/blueprintAssignments/write` -Atribuir um blueprint
- `Microsoft.Blueprint/blueprintAssignments/delete` -Cancelar a atribuição de um blueprint

> [!NOTE]
> Como as atribuições de blueprint são criadas em uma assinatura, atribuir o blueprint e cancelar a atribuição de permissões devem ser concedidas em um escopo de assinatura ou ser herdadas para um escopo de assinatura.

Essas permissões são incluídas na função **Proprietário** e, com exceção das permissões de atribuição de blueprint, também são incluídas nas funções **Colaborador**. Se essas funções internas não se ajustarem a suas necessidades de segurança, considere a criação de um [função personalizada](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> A entidade de serviço para Azure Blueprint requer a função **proprietário** na assinatura atribuída para habilitar a implantação. Se estiver usando o portal, essa função é concedida automaticamente e revogada para a implantação. Se estiver usando a API REST, essa função deve ser concedida manualmente, mas é revogada ainda automaticamente após a conclusão da implantação.

## <a name="next-steps"></a>Próximas etapas

- [Criar um plano gráfico - Portal](create-blueprint-portal.md)
- [Criar um plano gráfico - API REST](create-blueprint-rest-api.md)