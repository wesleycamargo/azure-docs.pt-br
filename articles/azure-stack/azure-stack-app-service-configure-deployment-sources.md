---
title: "Configurar fontes de implantação para Serviços de Aplicativos no Azure Stack | Microsoft Docs"
description: "Como um Administrador de Serviço pode configurar as fontes de implantação (Git, GitHub, BitBucket, DropBox e OneDrive) para o Serviço de Aplicativo no Azure Stack"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: anwestg
translationtype: Human Translation
ms.sourcegitcommit: b64d943516f4debf91db1de2066b48158b6dcd35
ms.openlocfilehash: 485fdab5ad1cd2c8a9ea27e3a2b1f93e2c2513a0
ms.lasthandoff: 02/08/2017


---

# <a name="configure-deployment-sources"></a>Configurar fontes de implantação

O Serviço de Aplicativo no Azure Stack oferece suporte à implantação sob demanda de vários Provedores de controle de origem.  Esse recurso permite que os desenvolvedores de aplicativo possam implantar direto de seus repositórios de controle de origem.  Para que os locatários possam configurar a conexão do Serviço de Aplicativo aos seus repositórios, os Administradores devem configurar primeiro a integração entre o Serviço de Aplicativo no Azure Stack e o Provedor de controle de origem.  Os Provedores de Controle de Origem com suporte, além do Git local, são:

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Exibir Fontes de implantação na administração do Serviço de Aplicativo

1. Faça logon no Portal do Azure Stack como administrador de serviços.
2. Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**.
    ![Administrador de Provedor de Recursos do Serviço de Aplicativo][1]
3. Clique em **Configuração do controle de origem**.  Aqui você pode ver a lista de todas as Fontes de Implantação configuradas.
    ![Configuração do controle de origem da Administração de Provedor de Recursos do Serviço de Aplicativo][2]

## <a name="configure-github"></a>Configurar o GitHub

> [!NOTE]
> Você precisa de uma conta do GitHub para concluir essa tarefa.  Você pode usar uma conta de sua organização em vez de uma conta pessoal.

1. Faça logon no GitHub, navegue até https://www.github.com/settings/developers e clique em **Registrar um novo aplicativo**
    ![GitHub - Registrar um novo aplicativo][3]
2. Insira um **Nome de aplicativo**, por exemplo, Serviço de Aplicativo no Azure Stack
3. Insira a **URL da Home Page**.  **A URL da Home Page deve ser o endereço do Portal do Azure Stack** por exemplo - https://portal.azurestack.local
4. Insira uma **Descrição do Aplicativo**
5. Insira a **URL de retorno de chamada de autorização**.  Em uma implantação padrão do Azure Stack, a Url está no formato https://portal.azurestack.local/tokenauthorize, se você estiver executando em um domínio diferente, substitua o domínio por azurestack.local  ![GitHub - Registrar um novo aplicativo com os valores preenchidos][4]
6. Clique em **Registrar aplicativo**.  Agora você verá uma página que lista a **ID do Cliente** e o **Segredo do Cliente** do aplicativo.
    ![GitHub - Registro de aplicativo concluído][5]
7.  Em uma nova guia do navegador, faça logon no Portal do Azure Stack como administrador de serviços. 
8.  Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**. 
9. Clique em **Configuração do controle de origem**.
10. Copie e cole a **Id do Cliente** e o **Segredo do Cliente** nas caixas de entrada correspondentes para o GitHub.
11. Clique em **Salvar**.
12. Se você não quiser configurar outras Fontes de Implantação, vá para [Agendar reparo de funções de gerenciamento](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles).


## <a name="configure-bitbucket"></a>Configurar o BitBucket

> [!NOTE]
> Você precisa de uma conta do BitBucket para concluir essa tarefa.  Você pode usar uma conta de sua organização em vez de uma conta pessoal.

1. Faça logon no BitBucket e navegue até **Integrações** em sua conta  ![Painel do BitBucket - Integrações][7]
2. Em Gerenciamento de Acesso, clique em **OAuth** e **Adicionar Consumidor**
    ![BitBucket Adicionar Consumidor do OAuth][8]
3. Insira um **Nome** para o consumidor, por exemplo, Serviço de Aplicativo no Azure Stack
4. Insira uma **Descrição** para o aplicativo
5. Insira a **URL de Retorno de Chamada**.  Em uma implantação padrão do Azure Stack, a Url de Retorno de Chamada está no formato https://portal.azurestack.local/tokenauthorize, se você estiver executando em um domínio diferente, substitua o domínio por azurestack.local.  A Url deve seguir a capitalização listada aqui para que a integração do BitBucket tenha êxito.
6. Insira a **URL** - Essa Url deve ser a URL do Portal do Azure Stack, por exemplo, https://portal.azurestack.local
7. Selecione as **Permissões** necessárias para os  **Repositórios**: **Leitura**
    **Webhooks**: **Leitura e gravação**
8. Clique em **Salvar**.  Agora você verá esse novo aplicativo, junto com a **Chave** e o **Segredo** em **Consumidores de OAuth**.
    ![Listagem de aplicativos do BitBucket][9]
9.  Em uma nova guia do navegador, faça logon no Portal do Azure Stack como administrador de serviços. 
10.  Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**. 
11. Clique em **Configuração do controle de origem**.
12. Copie e cole a **Chave** na caixa de entrada **Id do Cliente** e o **Segredo** na caixa de entrada **Segredo do Cliente** do BitBucket.
13. Clique em **Salvar**.
14. Se você não quiser configurar outras Fontes de Implantação, vá para [Agendar reparo de funções de gerenciamento](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles).

## <a name="configure-onedrive"></a>Configurar o OneDrive

> [!NOTE]
> Atualmente, não há suporte para contas do OneDrive para Empresas.  Você precisa ter uma conta da Microsoft vinculada a uma conta do OneDrive para concluir essa tarefa.  Você pode usar uma conta de sua organização em vez de uma conta pessoal.

1. Navegue até https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm e faça logon usando sua conta da Microsoft.
2. Clique em **Adicionar um aplicativo** em **Meus aplicativos**
![Aplicativos do OneDrive][10]
3. Insira um **Nome** para o Novo Registro de Aplicativo, insira **Serviço de Aplicativo no Azure Stack** e clique em **Criar Aplicativo**
4. A próxima tela lista as propriedades de seu novo aplicativo. Registre a **Id do Aplicativo**
![Propriedades de Aplicativo do OneDrive][11]
5. Em **Segredos do Aplicativo**, clique em **Gerar Nova Senha** e registre a **Nova senha gerada** -esse é o segredo de seu aplicativo.
> [!NOTE]
> Anote a nova senha, pois ela não poderá ser recuperada após clicar em OK neste estágio.
6. Em **Plataformas**, clique em **Adicionar Plataforma** e selecione **Web**
7. Insira o **URI de Redirecionamento**.  Em uma implantação padrão do Azure Stack, o URI de Redirecionamento está no formato https://portal.azurestack.local/tokenauthorize, se você estiver executando em um domínio diferente, substitua o domínio por azurestack.local ![Aplicativo OneDrive - Adicionar Plataforma Web][12]
8. Defina as **Permissões do Microsoft Graph** - **Permissões Delegadas**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Aplicativo do OneDrive - Permissões do Graph][13]
10. Clique em **Salvar**.
11.  Em uma nova guia do navegador, faça logon no Portal do Azure Stack como administrador de serviços. 
12.  Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**. 
13. Clique em **Configuração do controle de origem**.
14. Copie e cole a **ID do Aplicativo** na caixa de entrada **Id do Cliente** e o **Senha** na caixa de entrada **Segredo do Cliente** do OneDrive.
15. Clique em **Salvar**.
16. Se você não quiser configurar outras Fontes de Implantação, vá para [Agendar reparo de funções de gerenciamento](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles).

## <a name="configure-dropbox"></a>Configurar o DropBox

> [!NOTE]
> Você precisa ter uma conta do DropBox para concluir essa tarefa.  Você pode usar uma conta de sua organização em vez de uma conta pessoal.

1. Navegue até https://www.dropbox.com/developers/apps e faça logon usando sua conta do DropBox
2. Clique em **Criar aplicativo** 
![Aplicativos do Dropbox][14]
3. Selecione a **API do DropBox**
4. Defina o nível de acesso como **Pasta do Aplicativo**
5. Insira um **Nome** para seu aplicativo.
![Registro do aplicativo Dropbox][15]
6. Clique em **Criar Aplicativo**.  Agora você verá uma página listando as configurações para o Aplicativo, incluindo a **Chave do aplicativo** e **Segredo do aplicativo**.
7. Confira se o **Nome da pasta do aplicativo** está definido como **Serviço de Aplicativo no Azure Stack**
8. Defina o **URI de Redirecionamento do OAuth 2** e clique em **Adicionar**.  Em uma implantação padrão do Azure Stack, o URI de Redirecionamento está no formato https://portal.azurestack.local/tokenauthorize, se você estiver executando em um domínio diferente, substitua o domínio por azurestack.local ![Configuração do aplicativo Dropbox][16]
9.  Em uma nova guia do navegador, faça logon no Portal do Azure Stack como administrador de serviços. 
10.  Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**. 
11. Clique em **Configuração do controle de origem**.
12. Copie e cole a **Chave do Aplicativo** na caixa de entrada **Id do Cliente** e o **Segredo do aplicativo** na caixa de entrada **Segredo do Cliente** do DropBox.
13. Clique em **Salvar**.
14. Se você não quiser configurar outras Fontes de Implantação, vá para [Agendar reparo de funções de gerenciamento](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles).

## <a name="schedule-repair-of-management-roles"></a>Reparo agendado de funções de gerenciamento
Para que as configurações atualizadas na configuração das várias fontes de implantação sejam aplicadas, as Funções de Gerenciamento precisam ser reparadas.  Esse processo garante que os valores de configuração sejam aplicados corretamente, e as Fontes de Implantação configuradas sejam disponibilizadas para os locatários.

1. Em uma nova guia do navegador, faça logon no Portal do Azure Stack como administrador de serviços.
2. Navegue até **Provedores de Recursos** e selecione o **Administrador de Provedor de Recursos do Serviço de Aplicativo**.
3. Clique em **Configuração do controle de origem**
4. Copie e cole a **Id do Cliente** e o **Segredo do Cliente** nas caixas de entrada correspondentes para o GitHub.
5. Clique em **Salvar**
6. Clique em **Funções**
7. Clique em **Servidor de Gerenciamento**
8. Clique em **Reparar Todos** e selecione **Sim**.  Essa operação agenda um reparo em todos os Servidores de Gerenciamento para concluir a integração.  As operações de reparo são gerenciadas para minimizar o tempo de inatividade.
    ![Administrador do Provedor de Recursos do Serviço de Aplicativo - Funções - Servidor de Gerenciamento Reparar Todos][6]

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

