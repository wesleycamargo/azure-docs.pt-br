---
title: "Adicionando um Azure Active Directory usando Serviços Conectados no Visual Studio | Microsoft Docs"
description: "Adicionar um Active Directory do Azure usando a caixa de diálogo Adicionar Serviços Conectados do Visual Studio"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: bf3274f69cae8ec463fd158178394c7ba662ae75


---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adicionando um Active Directory do Azure usando Serviços Conectados no Visual Studio
## <a name="overview"></a>Visão geral
Ao usar o AD do Azure (Active Directory do Azure), você pode oferecer suporte ao SSO (Logon Único) para aplicativos Web MVC ASP.NET ou uma Autenticação AD nos serviços API Web. Com a Autenticação AD do Azure, os usuários podem usar suas contas no AD do Azure para se conectar aos aplicativos Web. As vantagens da Autenticação AD do Azure com a API Web incluem segurança aprimorada de dados ao expor uma API de um aplicativo Web. Com o AD do Azure, você não precisa gerenciar um sistema de autenticação separado com a própria conta e gerenciamento de usuários.

## <a name="supported-project-types"></a>Tipos de projeto com suporte
Você pode usar a caixa de diálogo Serviços Conectados para se conectar ao AD do Azure nos tipos de projeto a seguir.

* Projetos MVC ASP.NET
* Projetos API Web ASP.NET

### <a name="connect-to-azure-ad-using-the-connected-services-dialog"></a>Conectar-se ao AD do Azure usando a caixa de diálogo Serviços Conectados
1. Certifique-se de que tenha uma conta do Azure. Se não tiver uma conta do Azure, poderá se inscrever para uma [avaliação gratuita](http://go.microsoft.com/fwlink/?LinkId=518146).
2. No Visual Studio, abra o menu de atalho do nó **Referências** em seu projeto e escolha **Adicionar Serviços Conectados**.
3. Selecione **Autenticação do Azure AD** e escolha **Configurar**.
   
    ![Escolher Adicionar Autenticação AD do Azure](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)
4. Na primeira página de **Configurar Autenticação do Azure AD**, marque **Configurar Logon Único usando o Azure AD**.
   
    Se o seu projeto estiver definido com outra configuração de autenticação, o assistente avisará que continuar desabilitará a configuração anterior.
   
    ![Configurar o AD do Azure no assistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)
5. Na segunda página, selecione um domínio na lista suspensa **Domínio** . A lista de domínios contém todos os domínios que podem ser acessados por contas listadas na caixa de diálogo Configurações de Conta. Como alternativa, você poderá inserir um nome de domínio se não encontrar o que estiver procurando, como mydomain.onmicrosoft.com. Você pode escolher a opção para criar um novo aplicativo do AD do Azure ou usar as configurações de um aplicativo do AD do Azure existente. 
   
   ![Configurar o AD do Azure no assistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)
6. Na terceira página do assistente, verifique se a opção **Ler dados do diretório** está marcada. O assistente preencherá o **Segredo do cliente**. 
   
    ![Configurar o AD do Azure no assistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)
7. Escolha o botão **Concluir** . A caixa de diálogo adiciona as referências e o código de configuração necessários para habilitar seu projeto para autenticação AD do Azure. Você pode ver o domínio do AD no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
8. Analise a página de Introdução exibida no seu navegador para obter ideias sobre as próximas etapas e a página O que Aconteceu para ver como seu projeto foi modificado. Para verificar se tudo funcionou, abra um dos arquivos de configuração modificados e veja se as configurações mencionadas na página O Que Aconteceu estão lá. Por exemplo, o web.config principal em um projeto MVC ASP.NET terá estas configurações adicionadas:
   
        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## <a name="how-your-project-is-modified"></a>Como o projeto é modificado
Quando você executa o assistente, o Visual Studio adiciona o AD do Azure e referências associadas a seu projeto. Os arquivos de configuração e os arquivos de código em seu projeto também são modificados para adicionar suporte ao AD do Azure. As modificações específicas que o Visual Studio faz dependem do tipo de projeto. Para obter informações detalhadas sobre como os projetos MVC ASP.NET são modificados, consulte [O que aconteceu — projetos MVC](http://go.microsoft.com/fwlink/p/?LinkID=513809). Para projetos de API Web, confira [O que aconteceu — projetos API Web](http://go.microsoft.com/fwlink/p/?LinkId=513810).

## <a name="next-steps"></a>Próximas etapas
Faça perguntas e obtenha ajuda.

* [Fórum do MSDN: AD do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Documentação do AD do Azure](https://azure.microsoft.com/documentation/services/active-directory/)
* [Postagem do blog: Introdução ao AD do Azure](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)




<!--HONumber=Nov16_HO3-->


