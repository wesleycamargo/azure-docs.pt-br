---
title: 'Tutorial: Atribuir acesso no Gerenciamento de Custos do Azure | Microsoft Docs'
description: Neste tutorial, você aprende a atribuir acesso aos dados de gerenciamento de custos com contas de usuário que definem os níveis de acesso para entidades.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/17/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 3ceed8b88b9c81954c967d3d7ddd964c532867ab
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301600"
---
# <a name="tutorial-assign-access-to-cost-management-data"></a>Tutorial: Atribuir acesso aos dados de gerenciamento de custos

O acesso aos dados de gerenciamento de custos é fornecido pelo gerenciamento de usuário ou de entidade. As contas de usuário da Cloudyn determinam o acesso a *entidades* e a funções administrativas. Há dois tipos de acesso: administrador e usuário. A menos que seja modificado para cada usuário, o acesso de administrador permite o uso irrestrito de todas as funções no portal da Cloudyn, incluindo: gerenciamento de usuários, gerenciamento de listas de destinatários e acesso de entidade raiz a todos os dados de entidade. O acesso do usuário é indicado para que os usuários finais exibam relatórios e criem relatórios usando o acesso que têm aos dados de entidade.

As entidades são usadas para refletir a estrutura hierárquica da sua organização de negócios. Elas identificam os departamentos, as divisões e as equipes da organização na Cloudyn. A hierarquia de entidades ajuda a acompanhar com precisão os gastos de cada entidade.

Quando você registrou sua conta ou um contrato do Azure, uma conta com permissão de administrador foi criada na Cloudyn, portanto, você pode executar todas as etapas neste tutorial. Este tutorial aborda o acesso aos dados de gerenciamento de custos, incluindo o gerenciamento de usuários e gerenciamento de entidades. Você aprenderá como:

> [!div class="checklist"]
> * Criar um usuário com acesso de administrador
> * Criar um usuário com acesso de usuário
> * Criar e gerenciar entidades


Se você não tem uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

- Você deve ter uma conta do Azure.
- Você deve ter um registro de avaliação ou uma assinatura paga do Gerenciamento de Custos do Azure.

## <a name="create-a-user-with-admin-access"></a>Criar um usuário com acesso de administrador

Embora você já tenha acesso de administrador, os colegas de trabalho em sua organização também precisaram ter acesso de administrador. No portal da Cloudyn, clique no símbolo de engrenagem no canto superior direito e selecione **Gerenciamento de Usuários**. Clique em **Adicionar Novo Usuário** para adicionar um novo usuário.

Insira as informações necessárias sobre o usuário. A ID de logon deve ser um endereço de email válido. Escolha permissões para permitir o Gerenciamento de Usuários para que o usuário possa criar e modificar a outros usuários. O Gerenciamento de Listas de Destinatários permite que o usuário edite listas de destinatários. Um link com informações de entrada é enviado da Cloudyn para o email do usuário quando você seleciona **Notificar usuário por email**. Na primeira entrada, o usuário define uma senha.

Em **Usuário tem acesso de administrador**, a entidade de raiz de sua organização está selecionada. Deixe a raiz selecionada e, em seguida, salve as informações do usuário. Selecionar a entidade raiz permite que o usuário tenha permissão de administrador não apenas para a entidade raiz na árvore, mas também para todas as entidades que residem abaixo dela.  
  ![adicionar novo usuário com acesso de administrador](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Criar um usuário com acesso de usuário
Usuários típicos que precisam de acesso aos dados de gerenciamento de custos, como painéis e relatórios, devem ter acesso de usuário para exibi-los. Crie um novo usuário com acesso de usuário semelhante ao que você criou com acesso de administrador, com as seguintes diferenças:

- Limpe **Permitir gerenciamento de usuários**, **Permitir gerenciamento de listas de destinatários** e limpe tudo na lista **Usuário tem acesso de administrador**.
- Selecione as entidades para as quais o usuário precisa de acesso na lista **Usuário tem acesso de usuário**.
- Você também pode permitir que o administrador acesse entidades específicas, conforme o necessário.

![adicionar novo usuário com acesso de usuário](.\media\tutorial-user-access\new-user-access.png)

Para assistir a um vídeo de tutorial sobre como adicionar usuários, consulte [Como adicionara usuários ao Gerenciamento de Custos do Azure](https://youtu.be/Nzn7GLahx30).

## <a name="create-and-manage-entities"></a>Criar e gerenciar entidades

Ao definir a hierarquia de entidades de custo, uma prática recomendada é identificar a estrutura da organização. Entidades permitem segmentar gastos por contas ou assinaturas individuais. Você cria entidades de custo para criar grupos lógicos para gerenciar e controlar gastos. Ao criar a árvore, considere como você deseja ou precisa ver os custos separados por unidades de negócios, departamentos de vendas, ambientes e centros de custo. A árvore de entidades na Cloudyn é flexível devido à herança de entidade.

A assinaturas individuais das contas de nuvem são vinculadas a entidades específicas. Você pode associar uma entidade a uma conta ou assinatura de provedor de serviços de nuvem. Portanto, as entidades são multilocatárias. Você pode atribuir acesso a usuários específicos somente para o segmento deles na empresa usando entidades. Isso mantém os dados isolados, mesmo em uma parte grande da uma empresa, como nas subsidiárias. E o isolamento de dados ajuda na governança.  

Quando você registrou seu contrato do Azure ou sua conta com a Cloudyn, seus dados de recurso do Azure, incluindo dados de uso, desempenho, cobrança e marca das suas assinaturas foram copiados para sua conta da Cloudyn. No entanto, você deve criar a árvore de entidades manualmente. Se você tiver ignorado o registro do Azure Resource Manager, somente os dados de cobrança e alguns relatórios de ativos estarão disponíveis no portal da Cloudyn.

No portal da Cloudyn, clique no símbolo de engrenagem no canto superior direito e selecione **Contas do Cloud**. Você começa com uma única entidade (raiz) e cria a árvore de entidade na raiz. Aqui está um exemplo de uma hierarquia de entidades que pode ser semelhante à de muitas organizações de TI após a conclusão da árvore:

![árvore de entidades](.\media\tutorial-user-access\entity-tree.png)

Ao lado de **Entidades**, clique em **Adicionar Entidade**. Insira informações sobre a pessoa ou o departamento que você deseja adicionar. Os campos **Nome Completo** e **Email** não devem corresponder aos usuários existentes. Se você quiser exibir uma lista de níveis de acesso, pesquise na ajuda o tema *Adicionando uma entidade*.

![adicionar entidade](.\media\tutorial-user-access\add-entity.png)

Quando terminar, **Salve** a entidade.

### <a name="entity-access-levels"></a>Níveis de acesso de entidade

Os níveis de acesso de entidade em conjunto com o acesso do usuário permitem que você defina quais os tipos de ações estão disponíveis no portal do Cloudyn.

- **Enterprise** - Fornece a capacidade de criar e gerenciar entidades de custo do elemento filho.
- **Enterprise + Alocação de custos** - Fornece a capacidade de criar e gerenciar entidades de custo do elemento filho, incluindo alocação de custos para contas consolidadas.
- **Enterprise, Custo baseado em alocação de custos do elemento pai** - Fornece a capacidade de criar e gerenciar entidades de custo do elemento filho. Os custos da conta são baseados no modelo de alocação de custos do elemento pai.
- **Somente painéis personalizados** - O usuário enxerga apenas painéis personalizados predefinidos.
- **Somente painéis** - O usuário vê somente os painéis.

### <a name="create-a-cost-entity-hierarchy"></a>Criar uma hierarquia de entidade de custo

Para criar uma hierarquia de entidade de custo, você deve ter uma conta com acesso enterprise ou enterprise + alocação de custos.

No portal da Cloudyn, clique no símbolo de engrenagem no canto superior direito e selecione **Contas do Cloud**. A árvore **Entidades** é exibida no painel esquerdo. Se necessário, expanda a árvore de entidade para que você possa exibir a entidade que deseja associar a uma conta.  Suas contas de provedor de serviço de nuvem são mostradas nas guias no painel direito. Selecione uma guia e, em seguida, clique e arraste uma conta/assinatura até a entidade e solte-a. A caixa **Mover** informa que a conta foi movida com êxito. Clique em **OK**.

Você também pode associar várias contas a uma entidade. Selecione as contas e, em seguida, clique em **Mover**. Na caixa Mover contas, selecione a entidade para onde você deseja mover a conta e, em seguida, clique em **Salvar**. A caixa Mover contas pede para você confirmar que deseja mover as contas. Clique em **Sim** e depois em **OK**.

Para assistir a um vídeo de tutorial sobre como criar uma hierarquia de entidade de custo, consulte [Como criar uma hierarquia de entidade de custo no Gerenciamento de Custos do Azure](https://youtu.be/dAd9G7u0FmU).

Se você for um usuário do Contrato Enterprise do Azure, assista a um vídeo tutorial sobre como associar contas e assinaturas a entidades em [Como conectar-se ao Azure Resource Manager com o Gerenciamento de Custos do Azure](https://youtu.be/oCIwvfBB6kk).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um usuário com acesso de administrador
> * Criar um usuário com acesso de usuário
> * Criar e gerenciar entidades


Se você ainda não habilitou o acesso à API do Azure Resource Manager para suas contas, vá para o artigo a seguir.

> [!div class="nextstepaction"]
> [Ativar assinaturas e contas do Azure](activate-subs-accounts.md)
