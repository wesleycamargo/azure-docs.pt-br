---
title: "Configurar fontes de implantação para Serviços de Aplicativos no Azure Stack | Microsoft Docs"
description: "Como um Administrador de Serviço pode configurar as fontes de implantação (Git, GitHub, BitBucket, DropBox e OneDrive) para o Serviço de Aplicativo no Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 310bea356ac611a92d9e3899da5d4fb19c50e94e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-deployment-sources"></a>Configurar fontes de implantação
*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*


O Serviço de Aplicativo no Azure Stack oferece suporte à implantação sob demanda de vários Provedores de controle de origem. Esse recurso permite que os desenvolvedores de aplicativos implante direto de seus repositórios de controle de origem. Se quiserem que os usuários configurar o serviço de aplicativo para se conectar a seus repositórios, um operador de nuvem deve primeiro configurar a integração entre o serviço de aplicativo na pilha do Azure e o provedor de controle de origem.  

Os Provedores de Controle de Origem com suporte, além do Git local, são:

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Exibir fontes de implantação na administração do serviço de aplicativo

1. Faça logon Portal de administração de pilha (https://adminportal.local.azurestack.external) do Azure como administrador de serviço.
2. Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**.  ![Administrador de Provedor de Recursos do Serviço de Aplicativo][1]
3. Clique em **Configuração do controle de origem**.  Aqui você pode ver a lista de todas as Fontes de Implantação configuradas.
    ![Configuração do controle de origem da Administração de Provedor de Recursos do Serviço de Aplicativo][2]

## <a name="configure-github"></a>Configurar o GitHub

Você deve ter uma conta do GitHub para concluir esta tarefa. Você talvez queira usar uma conta para sua organização em vez de uma conta pessoal.

1. Efetue logon no GitHub, navegue até https://www.github.com/settings/developers e clique em **registrar um novo aplicativo**.
    ![GitHub - registrar um novo aplicativo][3]
2. Insira um **nome do aplicativo** , por exemplo - serviço de aplicativo na pilha do Azure.
3. Insira a **URL da Home Page**. A URL da página inicial deve ser o endereço do Portal de pilha do Azure. Por exemplo, https://portal.local.azurestack.external.
4. Insira um **descrição do aplicativo**.
5. Insira a **URL de retorno de chamada de autorização**.  Em uma implantação de pilha do Azure padrão, a Url está em https://portal.local.azurestack.external/tokenauthorize forma, se você estiver executando em um substituto de outro domínio no domínio local.azurestack.external ![GitHub - registrar um novo aplicativo com valores populados][4]
6. Clique em **Registrar aplicativo**.  Agora você verá uma página que lista a **ID do Cliente** e o **Segredo do Cliente** do aplicativo.
    ![GitHub - Registro de aplicativo concluído][5]
7.  Em uma nova guia do navegador ou janela efetuar login no Portal de administração de pilha (https://adminportal.local.azurestack.external) do Azure como administrador de serviço.
8.  Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**.
9. Clique em **Configuração do controle de origem**.
10. Copie e cole a **Id do Cliente** e o **Segredo do Cliente** nas caixas de entrada correspondentes para o GitHub.
11. Clique em **Salvar**.

## <a name="configure-bitbucket"></a>Configurar o BitBucket

Você deve ter uma conta de BitBucket para concluir esta tarefa. Você talvez queira usar uma conta para sua organização em vez de uma conta pessoal.

1. Faça logon no BitBucket e navegue até **integrações** em sua conta.
    ![Painel BitBucket - integrações][7]
2. Clique em **OAuth** sob o gerenciamento de acesso e **adicionar consumidor**.
    ![BitBucket Adicionar cliente OAuth][8]
3. Insira um **nome** para o consumidor, por exemplo o serviço de aplicativo na pilha do Azure.
4. Insira um **descrição** para o aplicativo.
5. Insira a **URL de Retorno de Chamada**.  Em uma implantação de pilha do Azure padrão, a Url de retorno de chamada está em https://portal.local.azurestack.external/TokenAuthorize formulário, se você estiver executando em um substituto de outro domínio no domínio azurestack.local.  A Url deve seguir a capitalização listada aqui para que a integração do BitBucket tenha êxito.
6. Insira o **URL** -essa Url deve ser a URL do Portal do Azure pilha, por exemplo https://portal.local.azurestack.external.
7. Selecione o **permissões** necessária:
    - **Repositórios**: *leitura*
    - **Webhooks**: *de leitura e gravação*
8. Clique em **Salvar**.  Agora você verá esse novo aplicativo, junto com a **Chave** e o **Segredo** em **Consumidores de OAuth**.
    ![Listagem de aplicativos do BitBucket][9]
9.  Em uma nova guia do navegador ou janela efetuar login no Portal de administração de pilha (https://adminportal.local.azurestack.external) do Azure como administrador de serviço.
10.  Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**.
11. Clique em **Configuração do controle de origem**.
12. Copie e cole a **Chave** na caixa de entrada **Id do Cliente** e o **Segredo** na caixa de entrada **Segredo do Cliente** do BitBucket.
13. Clique em **Salvar**.


## <a name="configure-onedrive"></a>Configurar o OneDrive

Você deve ter uma Account da Microsoft vinculada a uma conta do OneDrive para concluir esta tarefa.  Você talvez queira usar uma conta para sua organização em vez de uma conta pessoal.

> [!NOTE]
> Atualmente, não há suporte para contas do OneDrive para Empresas.

1. Navegue até https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm e faça logon usando sua conta da Microsoft.
2. Em **meus aplicativos**, clique em **adicionar um aplicativo**.
![Aplicativos OneDrive][10]
3. Insira um **Nome** para o Novo Registro de Aplicativo, insira **Serviço de Aplicativo no Azure Stack** e clique em **Criar Aplicativo**
4. A próxima tela lista as propriedades de seu novo aplicativo. Registro de **Id do aplicativo**. ![Propriedades do aplicativo OneDrive][11]
5. Em **segredos do aplicativo**, clique em **gerar nova senha**. Anote **nova senha gerada**. Este é o segredo do aplicativo e não é recuperável depois de clicar em **Okey** neste estágio.
6. Em **plataformas** clique **Adicionar plataforma** e selecione **Web**.
7. Insira o **URI de Redirecionamento**.  Em uma implantação de pilha do Azure padrão, o URI de redirecionamento está em https://portal.local.azurestack.external/tokenauthorize forma, se você estiver executando em um substituto de outro domínio no domínio azurestack.local ![aplicativo OneDrive - adicionar Plataforma da Web][12]
8. Adicionar o **Microsoft Graph permissões** - **permissões delegadas**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Aplicativo do OneDrive - Permissões do Graph][13]
9. Clique em **Salvar**.
10.  Em uma nova guia do navegador ou janela efetuar login no Portal de administração de pilha (https://adminportal.local.azurestack.external) do Azure como administrador de serviço.
11.  Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**.
12. Clique em **Configuração do controle de origem**.
13. Copie e cole a **ID do Aplicativo** na caixa de entrada **Id do Cliente** e o **Senha** na caixa de entrada **Segredo do Cliente** do OneDrive.
14. Clique em **Salvar**.

## <a name="configure-dropbox"></a>Configurar o DropBox

> [!NOTE]
> Você precisa ter uma conta do DropBox para concluir essa tarefa.  Você pode usar uma conta de sua organização em vez de uma conta pessoal.

1. Navegue até https://www.dropbox.com/developers/apps e de Log usando sua conta do DropBox.
2. Clique em **Criar aplicativo**.

    ![Aplicativos do dropbox][14]

3. Selecione **DropBox API**.
4. Defina o acesso a **pasta do aplicativo**.
5. Insira um **Nome** para seu aplicativo.
![Registro do aplicativo Dropbox][15]
6. Clique em **Criar Aplicativo**.  Agora você verá uma página listando as configurações para o Aplicativo, incluindo a **Chave do aplicativo** e **Segredo do aplicativo**.
7. Verifique o **nome de pasta do aplicativo** é definido como **do serviço de aplicativo no Azure pilha**.
8. Defina o **URI de Redirecionamento do OAuth 2** e clique em **Adicionar**.  Em uma implantação de pilha do Azure padrão, o URI de redirecionamento está em https://portal.local.azurestack.external/tokenauthorize forma, se você estiver executando em um substituto de outro domínio azurestack.local no domínio.
![Configuração de aplicativo do dropbox][16]
9.  Em uma nova guia do navegador ou janela efetuar login no Portal de administração de pilha (https://adminportal.local.azurestack.external) do Azure como administrador de serviço.
10.  Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**.
11. Clique em **Configuração do controle de origem**.
12. Copie e cole a **Chave do Aplicativo** na caixa de entrada **Id do Cliente** e o **Segredo do aplicativo** na caixa de entrada **Segredo do Cliente** do DropBox.
13. Clique em **Salvar**.


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

## <a name="next-steps"></a>Próximas etapas

Os usuários agora podem usar as fontes de implantação para itens como [implantação contínua](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment), [implantação local do Git](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git), e [sincronização de pastas de nuvem](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync).
