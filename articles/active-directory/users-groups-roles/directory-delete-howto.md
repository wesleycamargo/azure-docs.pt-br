---
title: Excluir um diretório do locatário do Azure Active Directory | Microsoft Docs
description: Explica como preparar um diretório de locatário do Azure AD para exclusão
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 30e5a147e1cde80c1ab80b1080aad4317a2de968
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197714"
---
# <a name="delete-an-azure-active-directory-tenant"></a>Excluir um locatário do Azure Active Directory

Quando um locatário é excluído, todos os recursos que estão contidos no locatário também são excluídos. Você precisa preparar o locatário minimizando seus recursos associados antes da exclusão. Somente um administrador global do Azure AD (Azure Active Directory) pode excluir um locatário do Azure AD do portal.

## <a name="prepare-the-tenant-for-deletion"></a>Preparar o locatário para exclusão

Você apenas poderá excluir um locatário do Azure AD depois que ele passar por várias verificações. Essas verificações reduzem o risco de que a exclusão de um locatário prejudique o acesso do usuário, como a capacidade de entrar no Office 365 ou de acessar recursos no Azure. Por exemplo, se o locatário associado a uma assinatura for excluído acidentalmente, os usuários não poderão acessar os recursos do Azure dessa assinatura. O exemplo a seguir explica as condições que são verificadas:

* Não pode haver nenhum usuário no locatário, exceto um administrador global que excluirá o locatário. Todos os outros usuários precisam ser excluídos para que o locatário possa ser excluído. Se os usuários estiverem sincronizados localmente, a sincronização precisará ser desabilitada e os usuários precisarão ser excluídos do locatário de nuvem usando o portal do Azure ou os cmdlets do Azure PowerShell. 
* Não pode haver nenhum aplicativo no locatário. Todos os aplicativos precisam ser removidos para que o locatário possa ser excluído.
* Não pode haver nenhum provedor de autenticação multifator vinculado ao locatário.
* Não pode haver nenhuma assinatura de nenhum serviço online da Microsoft como o Microsoft Azure, o Office 365 ou o Azure AD Premium associada ao locatário. Por exemplo, se um locatário padrão tiver sido criado para você no Azure, não será possível excluir esse locatário se sua assinatura do Azure ainda depender dele para autenticação. Da mesma forma, você não poderá excluir um locatário se outro usuário tiver associado uma assinatura a ele. 

## <a name="delete-an-azure-ad-tenant"></a>Excluir um locatário do Azure AD

1. Entre no [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta de Administrador Global do locatário.

2. Selecione **Azure Active Directory**.

3. Mude para o locatário que deseja excluir.
  
  ![botão para excluir diretório](./media/directory-delete-howto/delete-directory-command.png)

4. Selecione **Excluir diretório**.
  
  ![botão para excluir diretório](./media/directory-delete-howto/delete-directory-list.png)

5. Se o locatário não passar em uma ou mais verificações, você receberá um link para obter mais informações de como passar. Depois de passar em todas as verificações, selecione **Excluir** para concluir o processo.

## <a name="i-have-an-expired-subscription-but-i-cant-delete-the-tenant"></a>Eu tenho uma assinatura expirada, mas não consigo excluir o locatário

Quando você configurou o locatário do Azure Active Directory, talvez você também tenha ativado assinaturas baseadas em licença para a organização, como o Azure Active Directory Premium P2, o Office 365 Business Premium ou Enterprise Mobility + Security E5. Essas assinaturas bloqueiam a exclusão de diretório até que elas sejam totalmente excluídas para evitar a perda acidental de dados. As assinaturas precisam estar em um estado **Desprovisionado** para permitir a exclusão do locatário. Uma assinatura **Expirada** ou **Cancelada** passa para o estado **Desabilitado** e o estágio final é o **Desprovisionado**. 

Para saber o que esperar quando uma assinatura de avaliação do Office 365 expirar (não incluindo Parceiro/CSP, Contrato Enterprise ou Licenciamento por Volume), confira a tabela a seguir. Para obter mais informações sobre a retenção de dados e o ciclo de vida de assinatura do Office 365, confira [O que acontecerá com meus dados e o acesso ao Office 365 para empresas quando a assinatura terminar?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Estado da assinatura | Dados | Acesso a dados
----- | ----- | -----
Ativo (30 dias para avaliação)  | Dados acessíveis a todos    | <li>Os usuários têm acesso normal a aplicativos ou arquivos do Office 365<li>Os administradores têm acesso normal ao Centro de administração e aos recursos do Office 365 
Expirado (30 dias)   | Dados acessíveis a todos    | <li>Os usuários têm acesso normal a aplicativos ou arquivos do Office 365<li>Os administradores têm acesso normal ao Centro de administração e aos recursos do Office 365
Desabilitado (30 dias) | Dados acessíveis somente ao administrador  | <li>Os usuários não podem acessar arquivos ou aplicativos do Office 365<li>Os administradores podem acessar o Centro de administração do Office 365, mas não é possível atribuir licenças ou atualizar usuários
Desprovisionado (30 dias depois de ser desabilitado) | Dados excluídos (excluídos automaticamente se nenhum outro serviço estiver em uso) | <li>Os usuários não podem acessar arquivos ou aplicativos do Office 365<li>Os administradores podem acessar o Centro de administração do Office 365 para comprar e gerenciar outras assinaturas

Você pode colocar uma assinatura no estado **Desprovisionado** a ser excluída após três dias usando o Centro de administração do Microsoft Store para Empresas. Essa funcionalidade chegará em breve ao Centro de administração do Office 365.

1. Entre no [Centro de administração do Microsoft Store para Empresas](https://businessstore.microsoft.com/manage/) com uma conta de Administrador Global do locatário. Se você está tentando excluir o locatário "Contoso" que tem o domínio inicial padrão contoso.onmicrosoft.com, entre com um UPN como admin@contoso.onmicrosoft.com.

2. Acesse a guia **Gerenciar** e selecione **Produtos e Serviços**; em seguida, escolha a assinatura que deseja cancelar e selecione **Excluir**.
  
  ![Link de exclusão para excluir assinatura](./media/directory-delete-howto/delete-command.png)
  
3. Selecione **Excluir assinatura** para aceitar os termos e condições e excluir a assinatura. Todos os dados são excluídos permanentemente em até três dias. Se você mudar de ideia, será possível reativar a assinatura durante os próximos três dias.
  
  ![Termos e condições](./media/directory-delete-howto/delete-terms.png)

4. Agora, o estado da assinatura foi alterado, a assinatura está marcada para exclusão. A assinatura entra o **Desprovisionado** estado 72 horas mais tarde.

5. Depois que você excluir uma assinatura no locatário e as 72 horas tiverem se passado, você poderá entrar novamente no Centro de administração do Azure AD, onde não haverá nenhuma ação necessária e nenhuma assinatura bloqueando a exclusão do locatário. Você poderá excluir o locatário do Azure AD com êxito.
  
  ![passar a verificação de assinatura na tela de exclusão](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="next-steps"></a>Próximas etapas
[Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
