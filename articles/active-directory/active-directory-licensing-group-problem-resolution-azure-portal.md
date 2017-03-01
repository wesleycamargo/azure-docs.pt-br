---

title: "Identificar e resolver problemas de licença para um grupo no Azure Active Directory | Microsoft Docs"
description: "Como identificar e resolver problemas de atribuição de licença usando o licenciamento baseado em grupo do Azure Active Directory"
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
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 6a9cebafd1ad8f513bfab897970241f7b82b2a53
ms.openlocfilehash: 9a434cf35d7934dc5eb759851fb65ad2a9f06eef
ms.lasthandoff: 02/22/2017


---

# <a name="identifying-and-resolving-license-problems-for-a-group-in-azure-active-directory"></a>Identificar e resolver problemas de licença para um grupo no Azure Active Directory


O licenciamento baseado em grupo no Azure Active Directory (Azure AD) introduz o conceito de usuários em estado de erro de licenciamento. Neste artigo, vamos explicar por que os usuários podem acabar nesse estado. Quando as licenças são atribuídas diretamente a usuários individuais, sem o uso de licenciamento baseado em grupo, a operação de atribuição pode falhar. Por exemplo, quando o administrador executa o cmdlet `Set-MsolUserLicense` do PowerShell em um usuário, o cmdlet pode falhar por vários motivos relacionados à lógica de negócios, como um número insuficiente de licenças ou um conflito entre dois planos de serviço que não podem ser atribuídos ao mesmo tempo. O problema é relatado imediatamente ao usuário que está executando o comando.

Ao usar o licenciamento baseado em grupo, os mesmos erros podem ocorrer, mas eles ocorrem em segundo plano quando o serviço do Azure AD é atribuir licenças. Por esse motivo que eles não podem ser comunicados imediatamente para o administrador. Em vez disso, eles são registrados no objeto do usuário e relatados por meio do portal administrativo. A intenção original para licenciar o usuário nunca é perdida, mas pode ser aplicada no estado ativo ou gravado em estado de erro para investigação futura e resolução.

Para localizar usuários no estado de erro para cada grupo, abra a folha de cada grupo e, em **licenças** haverá uma notificação exibida se houver usuários em estado de erro. Selecione a notificação para abrir um modo de exibição lista afetados todos os usuários que podem ser exibidas individualmente para entender o problema subjacente. Neste artigo, descreveremos cada problema e como resolvê-lo.

## <a name="not-enough-licenses"></a>Não há licenças suficientes

Não há licenças suficientes disponíveis para um dos produtos especificados no grupo. Você precisa adquirir mais licenças para o produto ou liberar as licenças não utilizadas de outros usuários ou grupos.

Para ver quantas licenças estão disponíveis, vá para **Azure Active Directory &gt; licenças &gt; todos os produtos**.

Para ver quais usuários e grupos estão consumindo licenças, clique em um produto. Em **licenciado a usuários** você verá todos os usuários para quem os licenças foram atribuídas diretamente ou por meio de um ou mais grupos. Em **licenciado grupos** você verá todos os grupos que têm esse produto atribuído.

## <a name="conflicting-service-plans"></a>Planos de serviço conflitante

Um dos produtos especificados no grupo contém um plano de serviço que está em conflito com outro plano de serviço já está atribuído ao usuário por meio de um produto diferente. Alguns planos de serviço são configurados de forma para que eles não sejam atribuídos ao mesmo usuário como outro plano de serviço relacionado.

Considere o seguinte exemplo: um usuário tem uma licença para o Office 365 Enterprise **E1** atribuído diretamente, com todos os planos habilitados. O usuário foi adicionado a um grupo que tem o Office 365 Enterprise **E3** produto atribuído a ele. Este produto contém serviço planos que não podem se sobrepor entre E1 e E3, então a atribuição de licença de grupo irá falhar com o erro "Planos de serviço em conflito". Neste exemplo, os planos de serviço conflitantes são:

-   SharePoint Online (plano 2) está em conflito com o SharePoint Online (plano 1)

-   O Exchange Online (plano 2) está em conflito com o Exchange Online (plano 1)

Para resolver esse conflito, você precisará desabilitar esses dois planos em licença E1 diretamente atribuídas ao usuário, ou modificar a atribuição de licença do grupo inteiro e desabilitar os planos na licença E3. Como alternativa, você pode decidir remover a licença de E1 do usuário se ela é redundante no contexto da licença E3.

A decisão de como resolver conflitantes licenças de produtos sempre pertence ao administrador. Azure AD não será resolvido automaticamente conflitos de licença.

## <a name="other-products-depend-on-this-license"></a>Outros produtos dependem desta licença

Um dos produtos especificados no grupo contém um plano de serviço deve ser habilitado para outro plano de serviço em outro produto, a função. Esse erro ocorre quando o Azure AD tenta remover o plano de serviço subjacente, por exemplo, como resultado o usuário que está sendo removido do grupo.

## <a name="usage-location-not-allowed"></a>Local de uso não permitido

Alguns serviços da Microsoft não estão disponíveis em todos os locais devido às leis e regulamentações locais. Antes de uma licença pode ser atribuída a um usuário, o administrador precisa especificar a propriedade "Local de uso" para o usuário. Isso pode ser feito na seção **Usuário &gt; Perfil &gt; Configurações** no portal do Azure.

Ao usar a atribuição de grupo de licenças, qualquer usuário sem um local de uso especificado herdará o local do diretório. Se você tiver usuários em locais onde os planos não estão disponíveis, considere modificar a atribuição de licença no nível de grupo para desabilitar os planos afetados. Como alternativa, você pode mover os usuários para um grupo diferente cujas atribuições de licença não entrem em conflito com o local.

Se você tiver usuários em locais diferentes, certifique-se de refletir isso corretamente nos objetos do usuário antes de adicionar usuários a grupos de licenças.

## <a name="what-happens-when-there-is-more-than-1-product-license-on-a-group"></a>O que acontece quando há mais de 1 licença do produto em um grupo?

Você pode atribuir mais de 1 licença do produto a um grupo. Por exemplo, você poderia atribuir Office 365 Enterprise E3 e mobilidade corporativa + segurança a um grupo para habilitar facilmente incluídos todos os serviços para os usuários.

Azure AD tenta atribuir todas as licenças especificadas no grupo para cada usuário. Se, não é possível atribuir um dos produtos devido a problemas de lógica de negócios (por exemplo, não há licenças todos os conflitos com outros serviços habilitados no usuário), não atribuirá as outras licenças do grupo, ou.

Você poderá ver os usuários para quem atribuição falhou e verificar quais produtos foram afetados por isso.

## <a name="how-to-force-processing-of-licenses-in-a-group-to-resolve-errors"></a>Como forçar o processamento de licenças em um grupo para resolver erros?

Dependendo de quais etapas foram executadas para resolver erros, pode ser necessário acionar o processamento de um grupo para atualizar o estado do usuário manualmente.

Por exemplo, se você adquiriu mais licenças para cobrir todos os usuários, você precisará acionar o processamento de grupos que falharam anteriormente totalmente licenciar todos os membros de usuário. Para fazer isso, localize a folha de grupo, abra **licenças** e selecione o **reprocessar** na barra de ferramentas.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licença por meio de grupos, leia

* [Atribuição de licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [O que é o licenciamento baseado em grupo no Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](active-directory-licensing-group-advanced.md)

