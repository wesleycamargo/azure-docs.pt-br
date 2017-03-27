---

title: "Resolver problemas de licença para um grupo no Azure Active Directory | Microsoft Docs"
description: "Como identificar e resolver problemas de atribuição de licença quando você estiver usando o licenciamento baseado em grupo do Azure Active Directory"
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 40042fd5ca2671cc26a67736b1c7a1e907c004d8
ms.lasthandoff: 03/10/2017


---

# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identificar e resolver problemas de atribuição de licenças para um grupo no Azure Active Directory

O licenciamento baseado em grupo no Azure Active Directory (Azure AD) introduz o conceito de usuários em um estado de erro de licenciamento. Neste artigo, explicaremos os motivos pelos quais os usuários podem acabar tendo esse estado.

Quando você atribui licenças diretamente a usuários individuais, sem usar o licenciamento baseado em grupo, a operação de atribuição pode falhar. Por exemplo, quando você executa o cmdlet do PowerShell `Set-MsolUserLicense` em um usuário, o cmdlet pode falhar por vários motivos relacionados à lógica de negócios. Por exemplo, pode haver um número insuficiente de licenças ou um conflito entre dois planos de serviço que não podem ser atribuídos ao mesmo tempo. O problema é relatado imediatamente para você.

Quando você está usando o licenciamento com base em grupo, o mesmo erro pode ocorrer, mas ocorre em segundo plano quando o serviço do Azure AD está atribuindo licenças. Por esse motivo, os erros não podem ser comunicados a você imediatamente. Em vez disso, eles são registrados no objeto do usuário e relatados por meio do portal administrativo. Observe que a intenção original para licenciar o usuário nunca é perdida, mas é registrada em um estado de erro para investigação futura e resolução.

Para localizar os usuários que estão em um estado de erro para cada grupo, abra a folha de cada grupo. Em **Licenças**, uma notificação será exibida se houver usuários em um estado de erro. Selecione a notificação para abrir uma lista de todos os usuários afetados. Você pode exibir os usuários individualmente para entender o problema subjacente. Neste artigo, descrevemos cada problema e a maneira de resolvê-lo.

## <a name="not-enough-licenses"></a>Não há licenças suficientes

**Problema:** não há licenças suficientes disponíveis para um dos produtos especificados no grupo. Você precisa adquirir mais licenças para o produto ou liberar as licenças não utilizadas de outros usuários ou grupos.

Para ver quantas licenças estão disponíveis, acesse **Azure Active Directory** > **Licenças** > **Todos os produtos**.

Para ver quais usuários e grupos estão consumindo licenças, clique em um produto. Em **Usuários licenciados**, você verá todos os usuários que tiveram licenças atribuídas diretamente ou por meio de um ou mais grupos. Em **Grupos licenciados**, você verá todos os grupos que têm esse produto atribuído.

## <a name="conflicting-service-plans"></a>Planos de serviço conflitante

**Problema:** um dos produtos especificados no grupo contém um plano de serviço que está em conflito com outro plano de serviço já está atribuído ao usuário por meio de um produto diferente. Alguns planos de serviço são configurados de forma para que eles não sejam atribuídos ao mesmo usuário como outro plano de serviço relacionado.

Considere o exemplo a seguir. Um usuário tem uma licença para o Office 365 Enterprise **E1** atribuída diretamente, com todos os planos habilitados. O usuário foi adicionado a um grupo que tem o Office 365 Enterprise **E3** produto atribuído a ele. Este produto contém planos de serviço que não podem se sobrepor aos planos incluídos em E1. Portanto, a atribuição de licença de grupo falhará com o erro "Planos de serviço conflitantes". Neste exemplo, os planos de serviço conflitantes são:

-   SharePoint Online (plano 2) está em conflito com o SharePoint Online (plano 1).
-   O Exchange Online (plano 2) está em conflito com o Exchange Online (plano 1).

Para resolver esse conflito, você deverá desabilitar esses dois planos na licença E1 que está diretamente atribuída ao usuário. Ou você precisa modificar a atribuição de licença do grupo inteiro e desabilitar os planos na licença E3. Como alternativa, você pode decidir remover a licença de E1 do usuário se ela é redundante no contexto da licença E3.

A decisão de como resolver conflitantes licenças de produtos sempre pertence ao administrador. O Azure AD não resolve conflitos de licença automaticamente.

## <a name="other-products-depend-on-this-license"></a>Outros produtos dependem desta licença

**Problema:** um dos produtos especificados no grupo contém um plano de serviço deve ser habilitado para outro plano de serviço em outro produto, a função. Esse erro ocorre quando o Azure AD tenta remover o plano de serviço subjacente. Por exemplo, isso pode acontecer porque o usuário foi removido do grupo.

Para resolver esse problema, você precisará garantir que o plano necessário ainda esteja atribuído aos usuários por meio de algum outro método ou que os serviços dependentes sejam desabilitados para esses usuários. Depois de fazer isso, você pode remover corretamente a licença de grupo dos usuários.

## <a name="usage-location-isnt-allowed"></a>O local de uso não é permitido

**Problema:** alguns serviços da Microsoft não estão disponíveis em todos os locais devido a leis e regulamentações locais. Para poder atribuir uma licença a um usuário, você precisa especificar a propriedade "Local de uso" para o usuário. Você pode fazer isso na seção **Usuário** > **Perfil** > **Configurações** no portal do Azure.

Quando o Azure AD tentar atribuir uma licença de grupo a um usuário cujo local de uso não tenha suporte, ele falhará e registrará esse erro no usuário.

Para resolver esse problema, remova os usuários locais sem suporte do grupo licenciado. Como alternativa, se os valores atuais de local de uso não representarem o local real do usuário, você poderá modificá-los para que as licenças sejam atribuídas corretamente na próxima vez (se houver suporte para o novo local).

> [!NOTE]
> Quando o Azure AD atribui licenças de grupo, quaisquer usuários sem um local de uso especificado herdam o local do diretório. É recomendável que os administradores definam os valores de local de uso corretos nos usuários antes de usar o licenciamento baseado em grupo para cumprir as leis e regulamentações locais.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>O que acontece quando há mais de uma licença de produto em um grupo?

Você pode atribuir mais de uma licença de produto a um grupo. Por exemplo, você pode atribuir o Office 365 Enterprise E3 e o Enterprise Mobility + Security a um grupo para habilitar facilmente todos os serviços incluídos para os usuários.

O Azure AD tenta atribuir todas as licenças que são especificadas no grupo para cada usuário. Se o Azure AD não puder atribuir um dos produtos devido a problemas de lógica de negócios (por exemplo, se não houver licenças suficientes para todos ou se houver conflitos com outros serviços que estão habilitados no usuário), ele também não atribuirá outras licenças no grupo.

Você poderá ver os usuários que não foram atribuídos e verificar quais produtos foram afetados por isso.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Como forçar o processamento de licença em um grupo para resolver erros?

Dependendo de quais etapas você executou para resolver erros, pode ser necessário disparar manualmente o processamento de um grupo para atualizar o estado do usuário.

Por exemplo, se liberou algumas licenças removendo atribuições de licença diretas dos usuários, você precisará disparar o processamento de grupos que falharam anteriormente para licenciar totalmente todos os membros usuários. Para fazer isso, localize a folha do grupo, abra **Licenças** e selecione o botão **Reprocessar** na barra de ferramentas.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licenças por meio de grupos, leia o seguinte:

* [Atribuição de licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [O que é o licenciamento baseado em grupo no Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](active-directory-licensing-group-advanced.md)

