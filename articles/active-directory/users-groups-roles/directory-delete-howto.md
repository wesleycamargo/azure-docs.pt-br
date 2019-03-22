---
title: Excluir um diretório do locatário - Azure Active Directory | Microsoft Docs
description: Explica como preparar um diretório de locatário do Azure AD para exclusão
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 833c2e460ae306a7673e580aaa304be93c3cd044
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199725"
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

1. Entrar para o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja o Administrador Global do locatário.

2. Selecione **Azure Active Directory**.

3. Alterne para a organização que deseja excluir.
  
   ![Confirme se a organização antes de excluir](./media/directory-delete-howto/delete-directory-command.png)

4. Selecione **Excluir diretório**.
  
   ![Selecione o comando para excluir a organização](./media/directory-delete-howto/delete-directory-list.png)

5. Se o locatário não passar em uma ou mais verificações, você receberá um link para obter mais informações de como passar. Depois de passar em todas as verificações, selecione **Excluir** para concluir o processo.

## <a name="i-have-an-expired-subscription-but-i-cant-delete-the-tenant"></a>Eu tenho uma assinatura expirada, mas não consigo excluir o locatário

Quando você tiver configurado um locatário do Azure AD, você pode também ativou baseados em licença assinaturas para sua organização, como o Azure AD Premium P2, Office 365 Business Premium ou Enterprise Mobility + Security E5. Essas assinaturas bloqueiam a exclusão de diretório até que elas sejam totalmente excluídas para evitar a perda acidental de dados. As assinaturas precisam estar em um estado **Desprovisionado** para permitir a exclusão do locatário. Uma assinatura **Expirada** ou **Cancelada** passa para o estado **Desabilitado** e o estágio final é o **Desprovisionado**. 

Para saber o que esperar quando uma assinatura de avaliação do Office 365 expirar (não incluindo Parceiro/CSP, Contrato Enterprise ou Licenciamento por Volume), confira a tabela a seguir. Para obter mais informações sobre a retenção de dados e o ciclo de vida de assinatura do Office 365, confira [O que acontecerá com meus dados e o acesso ao Office 365 para empresas quando a assinatura terminar?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Estado da assinatura | Dados | Acesso a dados
----- | ----- | -----
Ativo (30 dias para avaliação)  | Dados acessíveis a todos    | <li>Os usuários têm acesso normal a aplicativos ou arquivos do Office 365<li>Os administradores têm acesso normal ao centro de administração do Microsoft 365 e recursos 
Expirado (30 dias)   | Dados acessíveis a todos    | <li>Os usuários têm acesso normal a aplicativos ou arquivos do Office 365<li>Os administradores têm acesso normal ao centro de administração do Microsoft 365 e recursos
Desabilitado (30 dias) | Dados acessíveis somente ao administrador  | <li>Os usuários não podem acessar arquivos ou aplicativos do Office 365<li>Administradores podem acessar o Centro de administração do Microsoft 365, mas não é possível atribuir licenças ou atualizar usuários
Desprovisionado (30 dias depois de ser desabilitado) | Dados excluídos (excluídos automaticamente se nenhum outro serviço estiver em uso) | <li>Os usuários não podem acessar arquivos ou aplicativos do Office 365<li>Os administradores podem acessar o Centro de administração do Microsoft 365 para comprar e gerenciar outras assinaturas 

## <a name="delete-a-subscription-in-the-microsoft-365-admin-center"></a>Excluir uma assinatura no Centro de administração do Microsoft 365

Você pode colocar uma assinatura em uma **Deprovisoned** estado a ser excluído em 3 dias usando o Centro de administração do Microsoft 365.

1. Entrar para o [Centro de administração do Microsoft 365](https://admin.microsoft.com) com uma conta que seja um Administrador Global no locatário. Se você está tentando excluir o locatário "Contoso" que tem o domínio inicial padrão contoso.onmicrosoft.com, entre com um UPN como admin@contoso.onmicrosoft.com.

2. Vá para o **cobrança** e selecione **produtos e serviços**, em seguida, escolha a assinatura que deseja cancelar. Depois de clicar em **Cancelar**, atualize a página.
  
   ![Link de exclusão para excluir assinatura](./media/directory-delete-howto/delete-command.png)
  
3. Selecione **Excluir** para excluir a assinatura e aceite os termos e condições. Todos os dados são excluídos permanentemente em até três dias. Se você mudar de ideia, será possível reativar a assinatura durante esse período de três dias.
  
   ![Leia atentamente os termos e condições](./media/directory-delete-howto/delete-terms.png)

4. Agora, o estado da assinatura foi alterado, a assinatura está marcada para exclusão. A assinatura entra o **Desprovisionado** estado 72 horas mais tarde.

5. Depois que você excluir uma assinatura no locatário e as 72 horas tiverem se passado, você poderá entrar novamente no Centro de administração do Azure AD, onde não haverá nenhuma ação necessária e nenhuma assinatura bloqueando a exclusão do locatário. Você poderá excluir o locatário do Azure AD com êxito.
  
   ![passar a verificação de assinatura na tela de exclusão](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="next-steps"></a>Próximas etapas

[Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
