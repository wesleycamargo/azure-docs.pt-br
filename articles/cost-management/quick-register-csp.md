---
title: "Registrar-se usando as informações do Parceiro CSP no Gerenciamento de Custo do Azure | Microsoft Docs"
description: "Use as informações do Parceiro CSP para se registrar no Gerenciamento de Custo do Azure pelo Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: bcb072a2f2ab8c0e5097fca2c95309464483cb53
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registrar-se mo programa de Parceiro CSP e exibir dados de custo

Como parceiro CSP, você pode se registrar no Gerenciamento de Custo do Azure pelo Cloudyn. O registro fornece acesso ao portal do Cloudyn. Este guia de início rápido fornece detalhes do processo de registro necessários para criar uma assinatura de avaliação do Cloudyn e entrar no portal do Cloudyn. Também mostra como começar a exibir dados de custo imediatamente.


>[!NOTE]
>Para concluir o registro, você deve ser um administrador do programa de parceria com acesso à API do Partner Center. A configuração da API do Partner Center é necessária para autenticação e acesso a dados. Para obter mais informações, consulte [Conectar-se à API do Partner Center](https://msdn.microsoft.com/library/partnercenter/mt709136.aspx). Além disso, os usuários CSP indiretos só podem usar Cloudyn quando o revendedor direto CSP registrar-se com a Cloudyn para fornecer acesso a seus clientes e assinaturas.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

- Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Criar um registro de avaliação

1. No portal do Azure, clique em **Gerenciamento de Custo + Cobrança** na lista de serviços.
2. Em **Visão Geral**, clique em **Gerenciamento de Custo**  
    ![Página Gerenciamento de Custo](./media/quick-register-csp/cost-mgt-billing-service.png)
3. Na página **Gerenciamento de Custo**, clique em **Ir para Gerenciamento de Custo** para abrir a página de registro do Cloudyn em uma nova janela.
4. Na página de registro de avaliação do portal do Cloudyn, digite o nome de sua empresa, selecione **Administrador do Microsoft CSP Partner Program** e, em seguida, clique em **Avançar**.  
5. Insira uma **ID do Aplicativo**, **ID de Comércio**, **Chave secreta do aplicativo** e selecione o **Plano de Preços Padrão**. Caso não tenha as informações em mãos, entre no portal do Partner Center em [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) com sua conta principal de administrador e execute as seguintes etapas:
  1. Acesse **Painel** > **Configurações de Conta** > **Gerenciamento de Aplicativo**.
  2. Caso tenha criado anteriormente um Aplicativo Web, ignore esta etapa. Caso contrário, clique em **Adicionar novo aplicativo Web** na seção **Aplicativo Web**.
  3. Copie o GUID da **ID do Aplicativo** do aplicativo Web.
  4. Copie o GUID da **ID do comércio** do aplicativo Web.
  5. Selecione a duração da validade da chave como um ou dois anos, conforme necessário. Selecione **Adicionar chave** e, em seguida, copie e salve o valor da chave secreta.  
    ![CSP Partner Center](./media/quick-register-csp/csp-partner-center.png)
  6. Volte para a página de registro e cole as informações.  
      ![Credenciais da conta do CSP](./media/quick-register-csp/csp-reg.png)
6. Concorde com os Termos de Uso e, em seguida, valide as informações. Clique em **Avançar** para autorizar o Cloudyn a coletar dados do recurso do Azure. Os dados coletados incluem dados de uso, desempenho, cobrança e marcação de suas assinaturas.  
7. Em **Convidar outros stakeholders**, adicione usuários digitando seus endereços de email. Quando concluir, clique em **Avançar**. Leva cerca de duas horas para que todos os seus dados de cobrança sejam adicionados ao Cloudyn.
8. Clique em **Ir para o Cloudyn** para abrir o portal de Cloudyn e, em seguida, na página **Gerenciamento de Contas de Nuvem**, você deverá ver as informações de sua conta do CSP registrada.

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Configurar acesso indireto de CSP em Cloudyn

Por padrão, a API do Partner Center só está acessível a CSPs diretos. No entanto, um provedor de CSP direto pode configurar o acesso para seus clientes ou parceiros de CSP indireto usando grupos de entidades em Cloudyn.

Para habilitar o acesso para clientes ou parceiros de CSP indireto, siga as etapas em [Criar um registro de avaliação](#create-a-trial-registration) para configurar um registro de avaliação. Em seguida, conclua estas etapas para segmentar dados de CSP indireto usando grupos de entidades Cloudyn. Em seguida, atribua as permissões de usuário apropriadas para os grupos de entidades.

1. Crie um grupo de entidades com as informações em [Criar entidades](tutorial-user-access.md#create-entities).
2. Siga as etapas em [Atribuir assinaturas a Entidades de Custo](https://support.cloudyn.com/hc/en-us/articles/115005139425-Video-Assigning-subscriptions-to-Cost-Entities). Associe a conta do cliente de CSP indireto e suas assinaturas do Azure para a entidade que você criou anteriormente.
3. Siga as etapas em [Criar um usuário com acesso de administrador](tutorial-user-access.md#create-a-user-with-admin-access) para criar uma conta de usuário com acesso de Administrador. Em seguida, verifique se a conta de usuário tem acesso de administrador às entidades específicas criadas anteriormente para a conta indireta.

Parceiros de CSP indiretos entram no portal Cloudyn usando as contas que você criou para eles.


[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você usou as informações do CSP para se registrar no Gerenciamento de Custo. Você também entrou no portal do Cloudyn e começou a exibir os dados de custo. Para saber mais sobre o Gerenciamento de Custo do Azure pelo Cloudyn, continue com o tutorial sobre o Gerenciamento de Custo.

> [!div class="nextstepaction"]
> [Examinar o uso e os custos](./tutorial-review-usage.md)
