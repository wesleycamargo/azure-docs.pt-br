---
title: "Azure Active Directory Domain Services: introdução | Microsoft Docs"
description: "Habilite o Azure Active Directory Domain Services usando o portal do Azure (Versão prévia)"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: f87bcf33d3b1eb21c7d84814e4c4086f664e293d
ms.contentlocale: pt-br
ms.lasthandoff: 07/17/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal-preview"></a>Habilite o Azure Active Directory Domain Services usando o portal do Azure (Versão prévia)


## <a name="task-3-configure-administrative-group"></a>Tarefa 3: configurar um grupo administrativo
Nesta tarefa de configuração, você cria um grupo administrativo em seu diretório do Azure AD. Este grupo administrativo especial é chamado *AAD DC Administrators*. Os membros desse grupo recebem permissões administrativas nos computadores ingressados no domínio gerenciado. Em computadores ingressados no domínio, esse grupo é adicionado ao grupo de administradores. Além disso, os membros desse grupo também poderão usar a Área de Trabalho Remota para se conectar remotamente a computadores ingressados no domínio.

> [!NOTE]
> Você não tem permissões de Administrador de Domínio nem de Administrador Enterprise no domínio gerenciado criado com o Azure Active Directory Domain Services. Em domínios gerenciados, essas permissões são reservadas pelo serviço e não são disponibilizadas aos usuários dentro do locatário. No entanto, você poderá usar o grupo administrativo especial criado nesta tarefa de configuração para executar algumas operações privilegiadas. Essas operações incluem ingressar os computadores no domínio, pertencer ao grupo de administração em computadores ingressados no domínio e configurar a Política de Grupo.
>

O assistente cria automaticamente o grupo administrativo em seu diretório do Azure AD. Esse grupo se chama "Administradores do AAD DC". Se houver um grupo existente com esse nome em seu diretório do Azure AD, o assistente o selecionará. Você pode configurar a associação ao grupo usando a página do assistente **Grupo de administradores**.

1. Para configurar a associação ao grupo, clique em **Administradores do AAD DC**.

    ![Configurar associação ao grupo](./media/getting-started/domain-services-blade-admingroup.png)

2. Clique no botão **Adicionar membros** para adicionar usuários de seu diretório do Azure AD ao grupo de administradores.

3. Quando terminar, clique em **OK** para ir para a página **Resumo** do assistente.

4. Na página **Resumo** do assistente, examine as definições de configuração do domínio gerenciado. Você pode voltar para qualquer etapa do assistente para fazer alterações se for necessário. Quando terminar, clique em **OK** para criar o novo domínio gerenciado.

    ![Resumo](./media/getting-started/domain-services-blade-summary.png)

5. Você verá uma notificação que mostra o andamento da implantação do Azure AD Domain Services. Clique na notificação para ver detalhes do andamento da implantação.

    ![Notificação – implantação em andamento](./media/getting-started/domain-services-blade-deployment-in-progress.png)


## <a name="provision-your-managed-domain"></a>Provisionar o domínio gerenciado
O processo de provisionamento de seu domínio gerenciado pode levar até uma hora.

1. Enquanto a implantação está em andamento, você pode pesquisar por "domain services" na caixa de pesquisa **Pesquisar recursos**. Selecione **Azure AD Domain Services** nos resultados da pesquisa. A folha **Azure AD Domain Services** lista o domínio gerenciado que está sendo provisionado.

    ![Localize o domínio gerenciado que está sendo provisionado](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Clique no nome do domínio gerenciado (por exemplo, "contoso100.com") para ver mais detalhes sobre ele.

    ![Domain Services – estado de provisionamento](./media/getting-started/domain-services-provisioning-state.png)

3. A guia **Visão Geral** mostra que o domínio está sendo provisionado. Você não pode configurar o domínio gerenciado até que ele esteja totalmente provisionado. Pode levar até uma hora para que o domínio gerenciado seja totalmente provisionado.

    ![Serviços de domínio – guia Visão Geral no estado de provisionamento ](./media/getting-started/domain-services-provisioning-state-details.png)

4. Quando o domínio gerenciado está totalmente provisionado, a guia **Visão Geral** mostra o status do domínio como **Em execução**.

    ![Domain Services - guia Visão geral depois de totalmente provisionado](./media/getting-started/domain-services-provisioned.png)

5. Na guia **Propriedades**, você vê dois endereços IP nos quais controladores de domínio estão disponíveis para a rede virtual.

    ![Serviços de domínio – guia Propriedades após a conclusão do provisionado](./media/getting-started/domain-services-provisioned-properties.png)


## <a name="next-step"></a>Próxima etapa
Tarefa 4: [atualizar as configurações do DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)

