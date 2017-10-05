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
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 955efc9e6b209195935d1f7c13f96c6a42536b2a
ms.contentlocale: pt-br
ms.lasthandoff: 09/27/2017

---

# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identificar e resolver problemas de atribuição de licenças para um grupo no Azure Active Directory

O licenciamento baseado em grupo no Azure Active Directory (Azure AD) introduz o conceito de usuários em um estado de erro de licenciamento. Neste artigo, explicaremos os motivos pelos quais os usuários podem acabar tendo esse estado.

Quando você atribui licenças diretamente a usuários individuais, sem usar o licenciamento baseado em grupo, a operação de atribuição pode falhar. Por exemplo, quando você executa o cmdlet do PowerShell `Set-MsolUserLicense` em um usuário, o cmdlet pode falhar por vários motivos relacionados à lógica de negócios. Por exemplo, pode haver um número insuficiente de licenças ou um conflito entre dois planos de serviço que não podem ser atribuídos ao mesmo tempo. O problema é relatado imediatamente para você.

Quando você está usando o licenciamento com base em grupo, o mesmo erro pode ocorrer, mas ocorre em segundo plano quando o serviço do Azure AD está atribuindo licenças. Por esse motivo, os erros não podem ser comunicados a você imediatamente. Em vez disso, eles são registrados no objeto do usuário e relatados por meio do portal administrativo. Observe que a intenção original para licenciar o usuário nunca é perdida, mas é registrada em um estado de erro para investigação futura e resolução.

## <a name="how-to-find-license-assignment-errors"></a>Como localizar erros de atribuição de licenças

1. Para localizar usuários em estado de erro em um grupo específico, abra a folha de cada grupo. Em **Licenças**, uma notificação será exibida se houver usuários em um estado de erro.

![Grupo, notificação de erro](media/active-directory-licensing-group-problem-resolution-azure-portal/group-error-notification.png)

2. Clique na notificação para abrir uma lista de todos os usuários afetados. Você pode clicar em cada usuário individualmente para ver mais detalhes.

![Grupo, lista de usuários em estado de erro](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-users-with-errors.png)

3. Para localizar todos os grupos que têm pelo menos um erro, na folha **Azure Active Directory** selecione **Licenças** e, em seguida, **Visão Geral**. Uma caixa de informações é exibida quando alguns grupos exigem sua atenção.

![Visão geral, informações sobre grupos em estado de erro](media/active-directory-licensing-group-problem-resolution-azure-portal/group-errors-widget.png)

4. Clique na caixa para ver uma lista de todos os grupos com erros. Você pode clicar em cada grupo para obter mais detalhes.

![Visão geral, lista de grupos com erros](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-groups-with-errors.png)


Veja abaixo uma descrição de cada problema potencial e como resolvê-lo.

## <a name="not-enough-licenses"></a>Não há licenças suficientes

**Problema:** não há licenças suficientes disponíveis para um dos produtos especificados no grupo. Você precisa adquirir mais licenças para o produto ou liberar as licenças não utilizadas de outros usuários ou grupos.

Para ver quantas licenças estão disponíveis, acesse **Azure Active Directory** > **Licenças** > **Todos os produtos**.

Para ver quais usuários e grupos estão consumindo licenças, clique em um produto. Em **Usuários licenciados**, você verá todos os usuários que tiveram licenças atribuídas diretamente ou por meio de um ou mais grupos. Em **Grupos licenciados**, você verá todos os grupos que têm esse produto atribuído.

**PowerShell:** os cmdlets do PowerShell reportam esse erro como _CountViolation_.

## <a name="conflicting-service-plans"></a>Planos de serviço conflitante

**Problema:** um dos produtos especificados no grupo contém um plano de serviço que está em conflito com outro plano de serviço já está atribuído ao usuário por meio de um produto diferente. Alguns planos de serviço são configurados de forma para que eles não sejam atribuídos ao mesmo usuário como outro plano de serviço relacionado.

Considere o exemplo a seguir. Um usuário tem uma licença para o Office 365 Enterprise **E1** atribuída diretamente, com todos os planos habilitados. O usuário foi adicionado a um grupo que tem o Office 365 Enterprise **E3** produto atribuído a ele. Este produto contém planos de serviço que não podem se sobrepor aos planos incluídos em E1. Portanto, a atribuição de licença de grupo falhará com o erro "Planos de serviço conflitantes". Neste exemplo, os planos de serviço conflitantes são:

-   SharePoint Online (plano 2) está em conflito com o SharePoint Online (plano 1).
-   O Exchange Online (plano 2) está em conflito com o Exchange Online (plano 1).

Para resolver esse conflito, você deverá desabilitar esses dois planos na licença E1 que está diretamente atribuída ao usuário. Ou você precisa modificar a atribuição de licença do grupo inteiro e desabilitar os planos na licença E3. Como alternativa, você pode decidir remover a licença de E1 do usuário se ela é redundante no contexto da licença E3.

A decisão de como resolver conflitantes licenças de produtos sempre pertence ao administrador. O Azure AD não resolve conflitos de licença automaticamente.

**PowerShell:** os cmdlets do PowerShell reportam esse erro como _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Outros produtos dependem desta licença

**Problema:** um dos produtos especificados no grupo contém um plano de serviço deve ser habilitado para outro plano de serviço em outro produto, a função. Esse erro ocorre quando o Azure AD tenta remover o plano de serviço subjacente. Por exemplo, isso pode acontecer porque o usuário foi removido do grupo.

Para resolver esse problema, você precisará garantir que o plano necessário ainda esteja atribuído aos usuários por meio de algum outro método ou que os serviços dependentes sejam desabilitados para esses usuários. Depois de fazer isso, você pode remover corretamente a licença de grupo dos usuários.

**PowerShell:** os cmdlets do PowerShell reportam esse erro como _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>O local de uso não é permitido

**Problema:** alguns serviços da Microsoft não estão disponíveis em todos os locais devido a leis e regulamentações locais. Para poder atribuir uma licença a um usuário, você precisa especificar a propriedade "Local de uso" para o usuário. Você pode fazer isso na seção **Usuário** > **Perfil** > **Configurações** no portal do Azure.

Quando o Azure AD tentar atribuir uma licença de grupo a um usuário cujo local de uso não tenha suporte, ele falhará e registrará esse erro no usuário.

Para resolver esse problema, remova os usuários locais sem suporte do grupo licenciado. Como alternativa, se os valores atuais de local de uso não representarem o local real do usuário, você poderá modificá-los para que as licenças sejam atribuídas corretamente na próxima vez (se houver suporte para o novo local).

**PowerShell:** os cmdlets do PowerShell reportam esse erro como _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Quando o Azure AD atribui licenças de grupo, quaisquer usuários sem um local de uso especificado herdam o local do diretório. É recomendável que os administradores definam os valores de local de uso corretos nos usuários antes de usar o licenciamento baseado em grupo para cumprir as leis e regulamentações locais.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>O que acontece quando há mais de uma licença de produto em um grupo?

Você pode atribuir mais de uma licença de produto a um grupo. Por exemplo, você pode atribuir o Office 365 Enterprise E3 e o Enterprise Mobility + Security a um grupo para habilitar facilmente todos os serviços incluídos para os usuários.

O Azure AD tenta atribuir todas as licenças que são especificadas no grupo para cada usuário. Se o Azure AD não puder atribuir um dos produtos devido a problemas de lógica de negócios (por exemplo, se não houver licenças suficientes para todos ou se houver conflitos com outros serviços que estão habilitados no usuário), ele também não atribuirá outras licenças no grupo.

Você poderá ver os usuários que não foram atribuídos e verificar quais produtos foram afetados por isso.

## <a name="how-to-manage-licenses-for-products-with-prerequisites"></a>Como gerenciar licenças de produtos com pré-requisitos?

Alguns produtos do Microsoft Online que talvez você tenha são "complementos" - eles exigem que um plano de serviço de pré-requisito seja habilitado para um usuário, ou um grupo, antes que possam ser atribuídos. Com o licenciamento baseado em grupo, o sistema exige que tanto os planos de serviço de complemento como de pré-requisito estejam presentes no mesmo grupo. Isso é feito para garantir que qualquer usuário adicionado ao grupo possa receber o produto totalmente funcional. Considere o seguinte exemplo:

O *Microsoft Workplace Analytics* é um produto complementar. Contém um único plano de serviço com o mesmo nome. Esse plano de serviço somente pode ser atribuído a um usuário, ou grupo, quando um dos seguintes pré-requisitos também forem atribuídos:
- *Exchange Online (Plano 1)*
- ou, *Exchange Online (Plano 2)*

Se tentarmos atribuir esse produto por conta própria a um grupo, o portal retornará um erro - ao clicar na notificação de erro, os seguintes detalhes serão exibidos:

![Grupo, pré-requisito ausente](media/active-directory-licensing-group-problem-resolution-azure-portal/group-prerequisite-required.png)

Ao clicar nos detalhes, a seguinte mensagem de erro será exibida:

*Falha na operação de licença. Certifique-se de que o grupo possua os serviços necessários, antes de adicionar ou remover um serviço dependente. **O serviço do Microsoft Workplace Analytics exige que o Exchange Online (Plano 2) também seja habilitado.***

Para atribuir essa licença complementar a um grupo, é necessário garantir que o grupo também contenha o plano de serviço de pré-requisito. Por exemplo, é possível atualizar um grupo existente que já contenha o produto *Office 365 E3* completo e adicionar o produto complementar.

Adicionalmente, é possível criar um grupo autônomo que contenha apenas os produtos mínimos necessários para fazer o trabalho de complemento - ele pode ser utilizado para licenciar apenas os usuários selecionados para o produto complementar. Nesse exemplo, atribuímos os seguintes produtos ao mesmo grupo:
- *Office 365 Enterprise E3*, com apenas o plano de serviço do *Exchange Online (Plano 2)* habilitado
- *Microsoft Workplace Analytics*

![Grupo, pré-requisito incluído](media/active-directory-licensing-group-problem-resolution-azure-portal/group-addon-with-prerequisite.png)

A partir de agora, qualquer usuário adicionado a este grupo consumirá 1 licença do produto E3 e uma licença do produto Workplace Analytics. Ao mesmo tempo, esses usuários poderão ser membros de outro grupo que lhes forneça o produto E3 completo e continuarão consumindo apenas uma licença para esse produto.

> [!TIP]
> É possível criar vários grupos para cada plano de serviço de pré-requisito. Por exemplo, se você utilizar tanto o *Office 365 Enterprise **E1*** como o *Office 365 Enterprise **E3*** para seus usuários, você poderá criar dois grupos para licença do *Microsoft Workplace Analytics*; um utilizando E1 como pré-requisito e o outro, utilizando o E3. Isso permitirá que o complemento seja distribuído aos usuários E1 e E3 sem consumir licenças adicionais.

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>A atribuição de licença falha silenciosamente para um usuário devido a endereços de proxy duplicados no Exchange Online

Se você estiver usando o Exchange Online, alguns usuários em seu locatário poderão estar configurados incorretamente com o mesmo valor de endereço do proxy. Quando o licenciamento baseado em grupo tentar atribuir uma licença para esse usuário, ele falhará e não registrará um erro (diferente dos outros casos de erro descritos acima) – esta é uma limitação da versão de visualização deste recurso e vamos solucioná-la antes da *disponibilidade geral*.

> [!TIP]
> Se você notar que alguns usuários não receberam uma licença e não há nenhum erro gravado nesses usuários, verifique primeiro se eles têm o endereço de proxy duplicado.
> Isso pode ser feito executando o seguinte cmdlet do PowerShell no Exchange Online:
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> [Este artigo](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online) contém mais detalhes sobre o problema, incluindo informações sobre [Como conectar o Exchange Online usando o PowerShell remoto](https://technet.microsoft.com/library/jj984289.aspx).

Depois de solucionar problemas de endereço de proxy para os usuários afetados, certifique-se de forçar o processamento de licença no grupo para garantir que as licenças possam ser aplicadas novamente.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Como forçar o processamento de licença em um grupo para resolver erros?

Dependendo de quais etapas você executou para resolver erros, pode ser necessário disparar manualmente o processamento de um grupo para atualizar o estado do usuário.

Por exemplo, se liberou algumas licenças removendo atribuições de licença diretas dos usuários, você precisará disparar o processamento de grupos que falharam anteriormente para licenciar totalmente todos os membros usuários. Para fazer isso, localize a folha do grupo, abra **Licenças** e selecione o botão **Reprocessar** na barra de ferramentas.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licenças por meio de grupos, leia o seguinte:

* [Atribuição de licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [O que é o licenciamento baseado em grupo no Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](active-directory-licensing-group-advanced.md)

