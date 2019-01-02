---
title: 'Tutorial: Configurar o G Suite para provisionamento automático de usuários com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e descontinuar automaticamente as contas de usuário do Azure AD para o G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 48a835bf8c63ffa5512173b600fb85abd5c6cb45
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840343"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: Configurar o G Suite para provisionamento automático de usuários

O objetivo desse tutorial é mostrar como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD (Azure Active Directory) para o G Suite.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD com o G Suite, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do G Suite
- Uma assinatura do Google Apps ou uma assinatura do Google Cloud Platform.

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-g-suite"></a>Atribuir usuários ao G Suite

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir que usuários e/ou grupos no Azure AD precisam de acesso a seu aplicativo. Depois de decidir, você pode atribuir esses usuários a seu aplicativo seguindo as instruções em [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Recomendamos a atribuição de um único usuário do Azure AD ao G Suite para o teste de configuração de provisionamento. Você pode atribuir usuários e grupos adicionais mais tarde.

> Ao atribuir um usuário ao G Suite, selecione a função **Usuário** ou **Grupo** na caixa de diálogo de atribuição. A função de **Acesso Padrão** não funciona para provisionamento.

## <a name="enable-automated-user-provisioning"></a>Habilitar o provisionamento automatizado de usuários

Esta seção orienta sobre o processo de conexão do Azure AD à API de provisionamento de conta de usuário do G Suite. Também ajuda a configurar o serviço de provisionamento para a criação, atualização e desabilitação de contas de usuário atribuído no G Suite com base na atribuição de usuário e de grupo no Azure AD.

>[!TIP]
>Você também pode optar por habilitar o Logon Único baseado em SAML para G Suites, seguindo as instruções do [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o provisionamento automático de conta de usuário

> [!NOTE]
> Outra opção viável para automatizar o provisionamento do usuário para o G Suite é usar o [GADS (Google Apps Directory Sync)](https://support.google.com/a/answer/106368?hl=en). O GADS provisiona suas identidades locais do Active Directory no G Suite. Por outro lado, a solução neste tutorial provisiona os usuários do Azure Active Directory (nuvem) e grupos habilitados para email para o G Suite. 

1. Entre no [Console de Admin do Google Apps](https://admin.google.com/) usando sua conta de administrador e selecione **Segurança**. Se você não vê o link, ele pode estar oculto sob o menu **Mais Controles**, na parte inferior da tela.
   
    ![Selecione segurança.][10]

1. Na página **Segurança**, selecione **Referência de API**.
   
    ![Selecione Referência de API.][15]

1. Selecione **Habilitar o acesso à API**.
   
    ![Selecione Referência de API.][16]

    > [!IMPORTANT]
    > Todo usuário que você pretende provisionar para o G Suite *deve* ter o nome de usuário no Azure Active Directory vinculado a um domínio personalizado. Por exemplo, nomes de usuários do tipo bob@contoso.onmicrosoft.com não são aceitos pelo G Suite. Por outro lado, bob@contoso.com é aceito. É possível alterar o domínio de um usuário existente editando as propriedades dele no AD do Azure. Incluímos instruções de como definir um domínio personalizado para o Azure Active Directory e o G Suite nas etapas a seguir.
      
1. Se você ainda não adicionou um nome de domínio personalizado ao Azure Active Directory, siga as seguintes etapas:
  
    a. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Active Directory**. Na lista de diretórios, selecione o diretório. 

    b. Selecione **Nome de domínio** no painel de navegação à esquerda e, depois, clique em **Adicionar**.
     
     ![Domínio](./media/google-apps-provisioning-tutorial/domain_1.png)

     ![Adição de domínio](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. Digite seu nome de domínio no campo **Nome de domínio** . Esse nome de domínio deve ser o mesmo que você pretende usar para o G Suite. Selecione o botão **Adicionar Domínio**.
     
     ![Nome de domínio](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. Selecione **Avançar** para ir até a página de verificação. Para verificar se você é proprietário desse domínio, é preciso editar os registros DNS do domínio de acordo com os valores fornecidos nesta página. É possível optar por verificar usando **Registros MX** ou **Registros TXT**, dependendo do que você selecionar na opção **Tipo de Registro**. 
    
    Para obter instruções mais abrangentes sobre como verificar nomes de domínio com o Azure AD, consulte [Adicionar seu próprio nome de domínio ao Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![Domínio](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. Repita as etapas anteriores para todos os domínios que você pretende adicionar ao diretório.

    > [!NOTE]
    Para provisionamento de usuário, o domínio personalizado deve corresponder ao nome de domínio do Azure AD de origem. Caso não coincidam, talvez seja possível resolver o problema implementando uma personalização de mapeamento de atributo.


1. Agora que confirmou todos os domínios com o Azure AD, você deve confirmá-los novamente com o Google Apps. Para todo domínio ainda não registrado no Google, execute as etapas a seguir:
   
    a. No [Console de Administrador do Google Apps](https://admin.google.com/), selecione **Domínios**.
     
     ![Selecione Domínios][20]

    b. Selecione **Adicionar um domínio ou um alias de domínio**.
     
     ![Adicione um novo domínio][21]

    c. Selecione **Adicionar outro domínio** e digite o nome do domínio que você deseja adicionar.
     
     ![Digite o nome de domínio][22]

    d. Selecione **Continuar e confirmar a propriedade do domínio**. Em seguida, siga as etapas para verificar se você possui o nome de domínio. For comprehensive instructions on how to verify your domain with Google, see [Verify your site ownership with Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Repita as etapas anteriores para os domínios adicionais que você pretende adicionar ao Google Apps.
     
     > [!WARNING]
     > Se você alterar o domínio principal do locatário do G Suite e já tiver configurado o logon único com o Microsoft Azure Active Directory, será necessário repetir a etapa nº 3 em [ Etapa 2: Habilitar logon único](#step-two-enable-single-sign-on).
       
1. No [Console de Administrador do Google Apps](https://admin.google.com/), selecione **Funções de Administrador**.
   
     ![Selecione Google Apps][26]

1. Determine a conta do administrador que você gostaria de usar para gerenciar o provisionamento de usuários. Para a **função de administrador** dessa conta, edite os **Privilégios** para essa função. Verifique se você habilitou todos os **Privilégios de API de Administrador**, de modo que essa conta possa ser usada para o provisionamento.
   
     ![Selecione Google Apps][27]
   
    > [!NOTE]
    > Se estiver configurando um ambiente de produção, a melhor prática será criar uma conta de administrador no G Suite especificamente para esta etapa. Essas contas devem ter uma função de administrador associada a elas que tem os privilégios de API necessários.
     
1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory** > **Aplicativos Empresariais** > **Todos os aplicativos**.

1. Se já tiver configurado o G Suite para logon único, pesquise a instância do G Suite usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise por **G Suite** ou **Google Apps** na galeria de aplicativos. Selecione seu aplicativo nos resultados da pesquisa e adicione-o à lista de aplicativos.

1. Selecione sua instância do G Suite e, em seguida, selecione a guia **Provisionamento**.

1. Defina o **Modo de Provisionamento** como **Automático**. 

     ![Provisionamento](./media/google-apps-provisioning-tutorial/provisioning.png)

1. Na seção **Credenciais de Administrador**, selecione **Autorizar**. Isso abre uma caixa de diálogo de autorização do Google em uma nova janela do navegador.

1. Confirme que você deseja dar permissão ao Azure Active Directory para fazer alterações em seu locatário do G Suite. Selecione **Aceitar**.
    
     ![Confirme permissões.][28]

1. No Portal do Azure, selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar a seu aplicativo. Se a conexão falhar, verifique se sua conta do G Suite tem permissões de Administrador de Equipe. Em seguida, tente a etapa **Autorizar** novamente.

1. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação**. Em seguida, selecione a caixa de seleção.

1. Selecione **Salvar**.

1. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Google Apps**.

1. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o G Suite. Os atributos que são **Matching** são usados para corresponder às contas de usuário no G Suite nas operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

1. Para habilitar o serviço de provisionamento do Azure AD para o G Suite, altere o **Status de provisionamento** para **no** em **Configurações**.

1. Clique em **Salvar**.

Esse processo inicia a sincronização inicial de todos os usuários ou grupos atribuídos ao G Suite na seção Usuários e Grupos. A sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos enquanto o serviço está em execução. É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os logs de atividade de provisionamento. Esses logs descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Logon Único](google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
