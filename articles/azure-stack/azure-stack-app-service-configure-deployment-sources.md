---
title: Configurar fontes de implantação para serviços de aplicativos no Azure Stack | Microsoft Docs
description: Como um administrador de serviços pode configurar fontes de implantação (Git, GitHub, BitBucket, DropBox e OneDrive) para serviços de aplicativos no Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: f2101c685ff7b3820f826da1d2e1d52b687d26c6
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446624"
---
# <a name="configure-deployment-sources"></a>Configurar fontes de implantação

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Serviço de aplicativo no Azure Stack oferece suporte à implantação sob demanda de vários provedores de controle do código-fonte. Esse recurso permite que os desenvolvedores de aplicativos implantar direto de seus repositórios de controle do código-fonte. Se os usuários desejam configurar o serviço de aplicativo para se conectar aos seus repositórios, um operador de nuvem deve primeiro configurar a integração entre o serviço de aplicativo no Azure Stack e o provedor de controle do código-fonte.  

Além do Git local, há suporte para os seguintes provedores de controle do código-fonte:

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Exibir fontes de implantação na administração do serviço de aplicativo

1. Entrar no Portal de administração do Azure Stack (https://adminportal.local.azurestack.external) como o administrador de serviço.
2. Navegue até **todos os serviços** e selecione o **serviço de aplicativo**.
    ![Administrador de Provedor de Recursos do Serviço de Aplicativo][1]
3. Clique em **Configuração do controle de origem**. Você pode ver a lista de todas as fontes de implantação configurado.
    ![Configuração do controle de origem da Administração de Provedor de Recursos do Serviço de Aplicativo][2]

## <a name="configure-github"></a>Configurar o GitHub

Você deve ter uma conta do GitHub para concluir esta tarefa. Talvez você queira usar uma conta para sua organização em vez de uma conta pessoal.

1. Entre no GitHub, navegue até https://www.github.com/settings/developerse, em seguida, clique em **registrar um novo aplicativo**.
    ![GitHub - registrar um novo aplicativo][3]
2. Insira um **nome do aplicativo**; por exemplo, **serviço de aplicativo no Azure Stack**.
3. Insira a **URL da Home Page**. A URL da home page deve ser o endereço do portal do Azure Stack. Por exemplo, https://portal.local.azurestack.external.
4. Insira um **descrição do aplicativo**.
5. Insira a **URL de retorno de chamada de autorização**. Em uma implantação do Azure Stack padrão, a URL está no formato https://portal.local.azurestack.external/TokenAuthorize. Se você estiver executando em um domínio diferente, substitua o nome do domínio local.azurestack.external.
6. Clique em **Registrar aplicativo**. Uma página é exibida, listando os **ID do cliente** e **segredo do cliente** para o aplicativo.
    ![GitHub - Registro de aplicativo concluído][5]
7.  Em uma janela ou uma nova guia do navegador, entrar no portal de administração do Azure Stack (https://adminportal.local.azurestack.external) como o administrador de serviço.
8.  Navegue até **provedores de recursos**e selecione o **administrador de provedor de recursos de serviço de aplicativo**.
9. Clique em **Configuração do controle de origem**.
10. Copie e cole a **ID do cliente** e **segredo do cliente** para a entrada correspondente nas caixas para o GitHub.
11. Clique em **Salvar**.

## <a name="configure-bitbucket"></a>Configurar o BitBucket

Você deve ter uma conta do BitBucket para concluir esta tarefa. Talvez você queira usar uma conta para sua organização em vez de uma conta pessoal.

1. Entre no BitBucket e navegue até **integrações** em sua conta.
    ![Painel do BitBucket - integrações][7]
2. Clique em **OAuth** sob o gerenciamento de acesso e **adicionar consumidor**.
    ![BitBucket adicionar consumidor do OAuth][8]
3. Insira um **nome** para o consumidor; por exemplo, **serviço de aplicativo no Azure Stack**.
4. Insira um **descrição** para o aplicativo.
5. Insira a **URL de Retorno de Chamada**. Em uma implantação do Azure Stack padrão, o retorno de chamada URL está no formato https://portal.local.azurestack.external/TokenAuthorize. Se você estiver executando em um domínio diferente, substitua o nome de domínio por azurestack. local. Para a integração do BitBucket tenha êxito, a URL deve seguir a capitalização listada aqui.
6. Insira o **URL**. Essa URL deve ser o portal do Azure Stack URL; Por exemplo, https://portal.local.azurestack.external.
7. Selecione o **permissões** necessários:
    - **Repositórios**: *Ler*
    - **Webhooks**: *Leitura e gravação*
8. Clique em **Salvar**. Agora você ver esse novo aplicativo, juntamente com o **chave** e **segredo**, em **consumidores de OAuth**.
    ![Listagem de aplicativos do BitBucket][9]
9.  Em uma janela ou uma nova guia do navegador, entrar no portal de administração do Azure Stack (https://adminportal.local.azurestack.external) como o administrador de serviço.
10.  Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**.
11. Clique em **Configuração do controle de origem**.
12. Copie e cole a **chave** para o **ID do cliente** caixa de entrada e **segredo** no **segredo do cliente** caixa de entrada para o BitBucket.
13. Clique em **Salvar**.

## <a name="configure-onedrive"></a>Configurar o OneDrive

Você deve ter uma Account da Microsoft vinculada a uma conta do OneDrive para concluir esta tarefa.  Talvez você queira usar uma conta para sua organização em vez de uma conta pessoal.

> [!NOTE]
> OneDrive for Business contas não têm suporte no momento.

1. Navegue até https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm e entre usando sua Account da Microsoft.
2. Sob **meus aplicativos**, clique em **adicionar um aplicativo**.
![Aplicativos do OneDrive][10]
3. Insira um **nome** para o novo registro de aplicativo: insira **serviço de aplicativo no Azure Stack**e, em seguida, clique em **criar aplicativo**
4. A próxima tela lista as propriedades de seu novo aplicativo. Salvar a **ID do aplicativo** para um local temporário.
![Propriedades de aplicativo do OneDrive][11]
5. Em **Segredos do Aplicativo**, clique em **Gerar nova senha**. Anote o **nova senha gerada**. Isso é o segredo de aplicativo e não é possível recuperá-la depois de clicar em **Okey**.
6. Sob **plataformas**, clique em **Adicionar plataforma**e, em seguida, selecione **Web**.
7. Insira o **URI de Redirecionamento**. Em uma implantação do Azure Stack padrão, o URI de redirecionamento está no formato https://portal.local.azurestack.external/TokenAuthorize. Se você estiver executando em um domínio diferente, substitua o nome de domínio por azurestack. local.
![Aplicativo do OneDrive - Adicionar plataforma da Web][12]
8. Adicione a **permissões do Microsoft Graph** - **permissões delegadas**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Aplicativo do OneDrive - Permissões do Graph][13]
9. Clique em **Salvar**.
10.  Em uma janela ou uma nova guia do navegador, entrar no portal de administração do Azure Stack (https://adminportal.local.azurestack.external) como o administrador de serviço.
11.  Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**.
12. Clique em **Configuração do controle de origem**.
13. Copie e cole o **ID do aplicativo** para o **ID do cliente** caixa de entrada e **senha** no **segredo do cliente** caixa de entrada para o OneDrive.
14. Clique em **Salvar**.

## <a name="configure-dropbox"></a>Configurar o DropBox

> [!NOTE]
> Você deve ter uma conta do DropBox para concluir esta tarefa. Talvez você queira usar uma conta para sua organização em vez de uma conta pessoal.

1. Navegue até https://www.dropbox.com/developers/apps e entre usando suas credenciais de conta do DropBox.
2. Clique em **Criar aplicativo**.

    ![Aplicativos do dropbox][14]

3. Selecione **API do DropBox**.
4. Defina o acesso de nível como **pasta aplicativo**.
5. Insira um **Nome** para seu aplicativo.
![Registro do aplicativo Dropbox][15]
6. Clique em **Criar Aplicativo**. Você verá uma página listando as configurações para o aplicativo, incluindo **chave do aplicativo** e **segredo do aplicativo**.
7. Certifique-se de que o **nome da pasta de aplicativo** é definido como **serviço de aplicativo no Azure Stack**.
8. Defina as **URI de redirecionamento OAuth 2** e, em seguida, clique em **Add**. Em uma implantação do Azure Stack padrão, o URI de redirecionamento está no formato https://portal.local.azurestack.external/TokenAuthorize. Se você estiver executando em um domínio diferente, substitua o domínio por azurestack. local.
![Configuração de aplicativo do dropbox][16]
9.  Em uma janela ou uma nova guia do navegador, entrar no portal de administração do Azure Stack (https://adminportal.local.azurestack.external) como o administrador de serviço.
10.  Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**.
11. Clique em **Configuração do controle de origem**.
12. Copie e cole a **chave do aplicativo** para o **ID do cliente** caixa de entrada e **segredo do aplicativo** no **segredo do cliente** caixa de entrada do DropBox.
13. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Os usuários agora podem usar as fontes de implantação para ações, como [implantação contínua](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment), [implantação do Git local](https://docs.microsoft.com/azure/app-service/deploy-local-git), e [pasta de sincronização de nuvem](https://docs.microsoft.com/azure/app-service/deploy-content-sync).

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png
