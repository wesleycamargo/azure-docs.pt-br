---
title: Gerenciar certificados de federação no Azure AD | Microsoft Docs
description: Saiba como personalizar a data de vencimento para seus certificados de federação e como renovar certificados que vencerão em breve.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: 5b4e2694a9b7e67643cb1e3b3cbc99fab940bd9d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337094"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gerenciar certificados para logon único federado no Azure Active Directory
Este artigo aborda dúvidas comuns e informações relacionadas aos certificados que o Azure Active Directory (Azure AD) cria para estabelecer SSO (logon único) federado para seus aplicativos SaaS. Adicione aplicativos pela galeria de aplicativos do Azure AD ou usando um modelo de aplicativo inexistente na galeria. Configure o aplicativo usando a opção de SSO federada.

Esse artigo só é relevante para aplicativos configurados para usar o SSO do Azure AD por meio da federação SAML, conforme mostrado no exemplo abaixo:

![Logon Único do AD do Azure](./media/manage-certificates-for-federated-single-sign-on/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificado gerado automaticamente para aplicativos da galeria e inexistentes na galeria
Quando você adiciona um novo aplicativo da galeria e configura um logon baseado em SAML, o Azure AD gera um certificado válido por 3 anos para o aplicativo. É possível baixar esse certificado da seção **Certificado de Autenticação SAML**. Para aplicativos da galeria, essa seção pode mostrar uma opção de baixar o certificado ou os metadados, dependendo do requisito do aplicativo.

![Logon único do Azure AD](./media/manage-certificates-for-federated-single-sign-on/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizar a data de vencimento do seu certificado de federação e transferir para um novo certificado
Por padrão, os certificados são definidos para expirar depois de dois anos. É possível escolher uma data de vencimento diferente para o certificado completando as etapas abaixo.
As capturas de tela usam Salesforce para fins de exemplo, mas podem aplicar essas etapas para qualquer aplicativo de SaaS federado.

1. No [portal do Azure](https://aad.portal.azure.com), clique em **Aplicativo empresarial** no painel esquerdo e clique em **Novo aplicativo** na página **Visão geral**:

   ![Abra o assistente de configuração de SSO](./media/manage-certificates-for-federated-single-sign-on/enterprise_application_new_application.png)

2. Pesquise o aplicativo da galeria e selecione o aplicativo que deseja adicionar. Se você não encontrar o aplicativo necessário, adicione o aplicativo usando a opção **Aplicativo inexistente na galeria**. Esse recurso só está disponível no SKU Azure AD Premium (P1 e P2).

    ![Logon único do Azure AD](./media/manage-certificates-for-federated-single-sign-on/add_gallery_application.png)

3. Clique no link **Logon único** no painel esquerdo e altere o **Modo de Logon Único** para **Logon baseado em SAML**. Isso gera um certificado válido por três anos para seu aplicativo.

4. Para criar um novo certificado, clique no link **Criar novo certificado** na seção **Certificado de Autenticação SAML**.

    ![Gerar um novo certificado](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

5. O link **Criar um novo certificado** abre o controle de calendário. Você pode definir qualquer data e hora até três anos a partir da data atual. A data e a hora selecionadas devem ser a nova data e hora de validade do seu novo certificado. Clique em **Salvar**.

    ![Baixe e depois carregue o certificado](./media/manage-certificates-for-federated-single-sign-on/certifcate_date_selection.PNG)

6. Agora o novo certificado está disponível para download. Clique no link **Certificado** para baixá-lo. Neste momento, seu certificado não está ativo. Quando você quiser transferir para esse certificado, clique na caixa de seleção **Ativar novo certificado** e clique em **Salvar**. A partir daí, o Azure AD começa a usar o novo certificado para assinar a resposta.

7.  Para saber como carregar o certificado para um aplicativo SaaS específico, clique no link **Exibir o tutorial de configuração do aplicativo**.

## <a name="certificate-expiration-notification-email"></a>Email de notificação de expiração de certificado

O Azure AD enviará uma notificação por email 60, 30 e 7 dias antes da expiração do certificado SAML. Para especificar o endereço de email ao qual enviar a notificação:

- Na página de Logon Único do aplicativo do Azure Active Directory, vá até o campo Email de Notificação.
- Insira o endereço de email que deve receber o email de notificação de expiração do certificado. Por padrão, este campo usa o endereço de email do administrador que adicionou o aplicativo.

Você receberá o email de notificação de aadnotification@microsoft.com. Para evitar que seu email vá para a pasta de spam, certifique-se de adicionar este email aos seus contatos. 

## <a name="renew-a-certificate-that-will-soon-expire"></a>Renovar um certificado que vencerá em breve
As etapas de renovação abaixo não devem resultar em nenhum tempo de inatividade significativo para seus usuários. As capturas de tela desta seção apresentam o Salesforce como exemplo, mas essas etapas podem ser aplicadas a qualquer aplicativo SaaS federado.

1. Na página **Logon Único** do aplicativo **Azure Active Directory**, gere o novo certificado para o seu aplicativo. É possível fazer isso clicando no link **Criar novo certificado** na seção **Certificado de Autenticação SAML**.

    ![Gerar um novo certificado](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

2. Selecione a data e a hora de validade desejadas do seu novo certificado e clique em **Salvar**.

3. Baixe o certificado na opção **Certificado de Autenticação SAML**. Carregue o novo certificado para a tela de configuração de logon único do aplicativo SaaS. Para saber como fazer isso para seu aplicativo SaaS específico, clique no link **Exibir o tutorial de configuração do aplicativo**.
   
4. Para ativar o novo certificado no Azure AD, marque a caixa de seleção **Criar novo certificado ativo** e clique no botão **Salvar** na parte superior da página. Isso transfere o novo certificado no lado do Azure AD. O status do certificado muda de **Novo** para **Ativo**. A partir daí, o Azure AD começa a usar o novo certificado para assinar a resposta. 
   
    ![Gerar um novo certificado](./media/manage-certificates-for-federated-single-sign-on/new_certificate_download.png)

## <a name="related-articles"></a>Artigos relacionados
* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](../saas-apps/tutorial-list.md)
* [Índice de artigos para gerenciamento de aplicativos no Azure Active Directory](../active-directory-apps-index.md)
* [Acesso a aplicativos e logon único com o Active Directory do Azure](what-is-single-sign-on.md)
* [Solução de problemas de logon único baseado em SAML](../develop/active-directory-saml-debugging.md)
