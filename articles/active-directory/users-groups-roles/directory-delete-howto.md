---
title: Excluir um diretório do AD do Azure - Active Directory do Azure | Microsoft Docs
description: Explica como preparar um diretório do AD do Azure para exclusão, incluindo diretórios de autoatendimento
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60473023"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Excluir um diretório no Azure Active Directory

Quando um diretório do AD do Azure é excluído, todos os recursos que estão contidos no diretório também são excluídos. Prepare sua organização, minimizando a seus recursos associados antes de excluir. Somente um administrador global do Azure Active Directory (Azure AD) pode excluir um diretório do AD do Azure no portal.

## <a name="prepare-the-directory"></a>Preparar o diretório

Você não pode excluir um diretório no Azure AD até que ele passe várias verificações. Essas verificações de reduzem o risco que a exclusão de um diretório do AD do Azure negativamente afeta o acesso do usuário, como a capacidade de entrar no Office 365 ou acessar recursos no Azure. Por exemplo, se o diretório associado a uma assinatura for acidentalmente excluído, os usuários não podem acessar os recursos do Azure para essa assinatura. As seguintes condições são verificadas:

* Não pode haver nenhum usuário no diretório, exceto um administrador global que é excluir o diretório. Quaisquer outros usuários devem ser excluídos antes que o diretório possa ser excluído. Se os usuários estiverem sincronizados no local, em seguida, sincronizar primeiro deve ser desativada e os usuários devem ser excluídos no diretório de nuvem usando o portal do Azure ou os cmdlets do PowerShell do Azure.
* Não pode haver nenhum aplicativo no diretório. Todos os aplicativos devem ser removidos antes que o diretório pode ser excluído.
* Não pode haver nenhum provedor de autenticação multifator vinculado ao diretório.
* Não pode haver assinaturas para Microsoft Online Services, como o Microsoft Azure, Office 365 ou Azure AD Premium associadas ao diretório. Por exemplo, se um diretório padrão tiver sido criado para você no Azure, você não poderá excluir esse diretório se sua assinatura do Azure ainda depender desse diretório para autenticação. Da mesma forma, você não pode excluir um diretório se outro usuário tiver associado uma assinatura a ele.

## <a name="delete-the-directory"></a>Excluir o diretório

1. Entrar para o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que seja o Administrador Global para sua organização.

2. Selecione **Azure Active Directory**.

3. Alternar para o diretório que você deseja excluir.
  
   ![Confirme se a organização antes de excluir](./media/directory-delete-howto/delete-directory-command.png)

4. Selecione **Excluir diretório**.
  
   ![Selecione o comando para excluir a organização](./media/directory-delete-howto/delete-directory-list.png)

5. Se seu diretório não passar uma ou mais verificações, você receberá um link para obter mais informações sobre como passar. Depois de passar em todas as verificações, selecione **Excluir** para concluir o processo.

## <a name="if-you-cant-delete-the-directory"></a>Se você não pode excluir o diretório

Quando você tiver configurado seu diretório do AD do Azure, você pode também ativou baseados em licença assinaturas para sua organização, como o Azure AD Premium P2, Office 365 Business Premium ou Enterprise Mobility + Security E5. Para evitar a perda acidental de dados, você não pode excluir um diretório até que as assinaturas são totalmente excluídas. As assinaturas devem estar em um **Desprovisionado** estado para permitir a exclusão de diretório. Uma **expirado** ou **cancelado** assinatura for movida para o **desabilitado** estado e o estágio final é o **Desprovisionado** estado.

Para saber o que esperar quando uma assinatura de avaliação do Office 365 expirar (não incluindo Parceiro/CSP, Contrato Enterprise ou Licenciamento por Volume), confira a tabela a seguir. Para obter mais informações sobre a retenção de dados e o ciclo de vida de assinatura do Office 365, confira [O que acontecerá com meus dados e o acesso ao Office 365 para empresas quando a assinatura terminar?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Estado da assinatura | Dados | Acesso a dados
----- | ----- | -----
Ativo (30 dias para avaliação) | Dados acessíveis a todos | Os usuários têm acesso normal a aplicativos ou arquivos do Office 365<br>Os administradores têm acesso normal ao centro de administração do Microsoft 365 e recursos 
Expirado (30 dias) | Dados acessíveis a todos| Os usuários têm acesso normal a aplicativos ou arquivos do Office 365<br>Os administradores têm acesso normal ao centro de administração do Microsoft 365 e recursos
Desabilitado (30 dias) | Dados acessíveis somente ao administrador | Os usuários não podem acessar arquivos ou aplicativos do Office 365<br>Administradores podem acessar o Centro de administração do Microsoft 365, mas não é possível atribuir licenças ou atualizar usuários
Desprovisionado (30 dias depois de ser desabilitado) | Dados excluídos (excluídos automaticamente se nenhum outro serviço estiver em uso) | Os usuários não podem acessar arquivos ou aplicativos do Office 365<br>Os administradores podem acessar o Centro de administração do Microsoft 365 para comprar e gerenciar outras assinaturas

## <a name="delete-a-subscription"></a>Excluir uma assinatura

Você pode colocar uma assinatura no estado Desprovisionado a ser excluído em três dias usando o Centro de administração do Microsoft 365.

1. Entrar para o [Centro de administração do Microsoft 365](https://admin.microsoft.com) com uma conta que seja um administrador global na sua organização. Se você está tentando excluir o diretório "Contoso" que tem o contoso.onmicrosoft.com de domínio padrão inicial, entrar com um UPN como admin@contoso.onmicrosoft.com.

2. Selecione **cobrança** e selecione **assinaturas**, em seguida, escolha a assinatura que deseja cancelar. Depois de clicar em **Cancelar**, atualize a página.
  
   ![Link de exclusão para excluir assinatura](./media/directory-delete-howto/delete-command.png)
  
3. Selecione **Excluir** para excluir a assinatura e aceite os termos e condições. Todos os dados são excluídos permanentemente em até três dias. Se você mudar de ideia, será possível reativar a assinatura durante esse período de três dias.
  
   ![Leia atentamente os termos e condições](./media/directory-delete-howto/delete-terms.png)

4. Agora, o estado da assinatura foi alterado, a assinatura está marcada para exclusão. A assinatura entra o **Desprovisionado** estado 72 horas mais tarde.

5. Depois que você excluiu uma assinatura em seu diretório e 72 horas tiverem se passado, você pode se volta ao centro de administração do AD do Azure novamente e lá deve ser nenhuma ação necessária e nenhuma bloqueio de exclusão de seu diretório de assinatura. Você poderá excluir com êxito seu diretório do AD do Azure.
  
   ![passar a verificação de assinatura na tela de exclusão](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Eu tenho uma assinatura de avaliação que bloqueia a exclusão

Há [produtos de inscrição de autoatendimento](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) como usuários individuais do Rights Management Services, Microsoft Power BI, Microsoft Power Apps ou Dynamics 365, inscreva-se por meio do Office 365, que também cria um usuário convidado para autenticação em diretório do Azure AD. Esses produtos Self-serviços bloqueiam exclusões de diretório até que eles são totalmente excluídos no diretório, para evitar a perda de dados. Eles podem ser excluídos somente pelo administrador do Azure AD se o usuário se inscrever individualmente ou foi atribuído o produto.

Há dois tipos de autoatendimento produtos Inscreva-se em como eles são atribuídos: 

* Atribuição de nível da organização: Um administrador do AD do Azure atribui o produto em toda a organização e um usuário pode ser ativamente usando o serviço com essa atribuição de nível da organização, mesmo se eles não são licenciados individualmente.
* Atribuição de nível de usuário: Um usuário individual durante a inscrição de autoatendimento, essencialmente, atribui o produto entre si sem um administrador. Depois que a organização se torna gerenciada pelo administrador (consulte [controle de administrador de um diretório não gerenciado](domains-admin-takeover.md), em seguida, o administrador pode atribuir diretamente o produto para usuários sem inscrição de autoatendimento.  

Quando você começa a exclusão do produto de inscrição de autoatendimento, a ação permanentemente exclui os dados e remove todos os usuários acessem o serviço. Qualquer usuário que foi atribuído a oferta individualmente ou no nível da organização, em seguida, será impedido de entrar ou acessar quaisquer dados existentes. Se você quiser evitar a perda de dados com o produto de inscrição de autoatendimento como [painéis do Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) ou [configuração de política do Rights Management Services](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), certifique-se de que o backup dos dados e salvo em outro lugar.

Para obter mais informações sobre produtos de inscrição Self-serviços está disponíveis e serviços, consulte [programas disponíveis Self-serviços](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Para saber o que esperar quando uma assinatura de avaliação do Office 365 expirar (não incluindo Parceiro/CSP, Contrato Enterprise ou Licenciamento por Volume), confira a tabela a seguir. Para obter mais informações sobre o Office 365 dados o ciclo de vida de retenção e a assinatura, consulte [o que acontece com meus dados e o acesso quando termina minha Office 365 para a assinatura de negócios?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Estado do produto | Dados | Acesso a dados
------------- | ---- | --------------
Ativo (30 dias para avaliação) | Dados acessíveis a todos | Os usuários têm acesso normal ao produto inscrição de autoatendimento, arquivos ou aplicativos<br>Os administradores têm acesso normal ao centro de administração do Microsoft 365 e recursos
Deleted | Dados excluídos | Usuários não podem acessar aplicativos, arquivos ou produto inscrição de autoatendimento<br>Os administradores podem acessar o Centro de administração do Microsoft 365 para comprar e gerenciar outras assinaturas

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Como excluir um produto inscrição de autoatendimento no portal do Azure?

Você pode colocar um produto de inscrição Self-service, como o Microsoft Power BI ou Azure Rights Management Services em um **excluir** estado a ser excluído imediatamente no portal do Azure AD.

1. Entrar para o [Centro de administração do Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) com uma conta que seja um Administrador Global da organização. Se você está tentando excluir o diretório "Contoso" que tem o contoso.onmicrosoft.com de domínio padrão inicial de logon com o UPN como admin@contoso.onmicrosoft.com.

2. Selecione **licenças**e, em seguida, selecione **produtos de inscrição de autoatendimento**. Você pode ver todos os Self-serviços inscrição produtos separadamente das assinaturas com base em estação. Escolha o produto que você deseja excluir permanentemente. Aqui está um exemplo no Microsoft Power BI:

    ![o nome de usuário é digitado incorretamente ou não encontrado](./media/directory-delete-howto/licenses-page.png)

3. Selecione **exclua** para excluir o produto e aceite os termos que esses dados são excluídos imediatamente e irrevogáveis. Essa ação delete removerá todos os usuários e acesso da organização para o produto. Clique em Sim para prosseguir com a exclusão.  

    ![o nome de usuário é digitado incorretamente ou não encontrado](./media/directory-delete-howto/delete-product.png)

4. Quando você seleciona **Sim**, a exclusão do produto Self-service será iniciada. Há uma notificação que informa você sobre a exclusão em andamento.  

    ![o nome de usuário é digitado incorretamente ou não encontrado](./media/directory-delete-howto/progress-message.png)

5. Agora, o estado do produto inscrição de autoatendimento foi alterado para **Deleted**. Quando a página for atualizada, o produto deve ser removido do **produtos de inscrição de autoatendimento** página.  

    ![o nome de usuário é digitado incorretamente ou não encontrado](./media/directory-delete-howto/product-deleted.png)

6. Depois de excluir todos os produtos, você pode assinar novamente no Centro de administração do AD do Azure novamente e deve haver nenhuma ação necessária e sem bloqueio de exclusão de seu diretório de produtos. Você poderá excluir com êxito seu diretório do AD do Azure.

    ![o nome de usuário é digitado incorretamente ou não encontrado](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Próximos passos

[Documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
