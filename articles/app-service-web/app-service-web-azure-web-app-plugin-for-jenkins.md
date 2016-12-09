---
title: "Como usar o plug-in de Aplicativo Web do Azure com a integração contínua Jenkins | Microsoft Docs"
description: "Descreve como usar o plug-in de Aplicativo Web do Azure com integração contínua Jenkins."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: be6c4e62-da76-44f6-bb00-464902734805
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c6f18c9b802d6fa18c4f50d3a537003eaaf25d14


---
# <a name="how-to-use-the-azure-web-app-plugin-with-jenkins-continuous-integration"></a>Como usar o plug-in de Aplicativo Web do Azure com integração contínua Jenkins
O plug-in de Aplicativo Web do Azure para Jenkins facilita a criação de aplicativos Web no Azure ao executar compilações distribuídas e implantar um arquivo WAR em seu aplicativo Web.

### <a name="prerequisites"></a>Pré-requisitos
Antes de realizar as etapas neste artigo, você precisa registrar e autorizar o aplicativo cliente e recuperar a ID do cliente e o Segredo do Cliente que será enviado ao Azure Active Directory durante a autenticação. Para saber mais sobre esses pré-requisitos, confira os artigos a seguir:

* [Integração de aplicativos com o Azure Active Directory][integrate-apps-with-AAD]
* [Registrar um aplicativo cliente][register-client-app]

Além disso, você precisará baixar o arquivo **azure-webapp-plugin.hpi** da seguinte URL:

* [https://github.com/Microsoft/azure-webapp-plugin/tree/master/install](https://github.com/Microsoft/azure-webapp-plugin/tree/master/install)

## <a name="how-to-install-the-azure-web-app-plugin-for-jenkins"></a>Como instalar o Plug-in de Aplicativo Web do Azure para Jenkins
1. [Baixe o arquivo **azure-webapp-plugin.hpi** do GitHub][azure-webapp-plugin-install]
2. Faça logon em seu painel do Jenkins.
3. No painel, clique em **Gerenciar Jenkins**.
   
    ![Gerenciar o Jenkins][jenkins-dashboard]
4. Na página **Gerenciar Jenkins**, clique em **Gerenciar Plug-ins**.
   
    ![Gerenciar plug-ins][manage-jenkins]
5. Clique na guia **Avançado** e clique em **Procurar** na seção **Carregar Plug-in**. Navegue até o local onde você baixou o arquivo **azure-webapp-plugin.hpi** nos **Pré-requisitos** e clique em **Carregar** depois de selecionar o arquivo.
   
    ![Carregar plug-in][upload-plugin]
6. Reinicie o Jenkins se necessário.

## <a name="configure-the-azure-web-app-plugin"></a>Configurar o plug-in de Aplicativo Web do Azure
1. No painel do Jenkins, clique em um dos seus projetos.
   
    ![Selecionar o projeto][select-project]
2. Na página do projeto, clique em **Configurar** no menu à esquerda.
   
    ![Configurar o projeto][configure-project]
3. Na seção **Ações de Pós-compilação**, clique no menu suspenso **Adicionar ação pós-compilação** e selecione **Configuração do Aplicativo Web do Azure**. 
   
    ![Opções avançadas][advanced-options]
4. Quando a seção **Configuração do Aplicativo Web do Azure** for exibida, insira sua **ID de Assinatura**, sua **ID de Cliente**, o **Segredo do Cliente** e o **Ponto de Extremidade do Token OAuth 2.0** na **Configuração do Perfil do Azure**.
   
    ![Configuração do Perfil do Azure][azure-profile-configuration]
5. Na seção **Configuração do Aplicativo Web**, insira suas informações de **Nome do Grupo de Recursos**, **Local**, **Nome do Plano de Hospedagem**, **Nome do Aplicativo Web**, **Nome do Sku**, **Capacidade de Sku** e **Caminho do Arquivo War**.
   
    ![Configuração do Aplicativo Web][webapp-configuration]
6. Clique em **Salvar** para salvar as configurações do projeto.
   
    ![Salvar projeto][save-project]
7. Clique em **Criar Agora** no menu à esquerda.
   
    ![Compilar projeto][build-project]

> [!NOTE]
> Um contêiner de Aplicativo Web será criado somente quando um contêiner de Aplicativo Web ainda não existir.
> 
> 

<a name="see-also"></a>

## <a name="see-also"></a>Consulte também
Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure].

Para obter informações adicionais sobre o Plug-in de Aplicativo Web do Azure para Jenkins, veja o projeto [Plug-in de Aplicativo Web do Azure] no GitHub.

<!-- URL List -->

[Central de desenvolvimento Java do Azure]: https://azure.microsoft.com/develop/java/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[Plug-in de Aplicativo Web do Azure]: https://github.com/Microsoft/azure-webapp-plugin
[azure-webapp-plugin-install]: https://github.com/Microsoft/azure-webapp-plugin/tree/master/install

<!-- IMG List -->

[jenkins-dashboard]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/jenkins-dashboard.png
[manage-jenkins]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/manage-jenkins.png
[upload-plugin]:     ./media/app-service-web-azure-web-app-plugin-for-jenkins/upload-plugin.png
[select-project]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/select-project.png
[configure-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/configure-project.png
[advanced-options]:  ./media/app-service-web-azure-web-app-plugin-for-jenkins/advanced-options.png
[azure-profile-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/azure-profile-configuration.png
[build-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/build-project.png
[save-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/save-project.png
[webapp-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/webapp-configuration.png



<!--HONumber=Nov16_HO3-->


