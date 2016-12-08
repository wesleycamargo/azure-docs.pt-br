---
title: "Plug-in do Serviço de Contêiner do Azure para Jenkins | Microsoft Docs"
description: "Implante um contêiner docker em um serviço de cluster do Serviço de Contêiner do Azure usando o plug-in do Serviço de Contêiner do Azure para Jenkins."
services: container-service
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
tags: azure-container-service, jenkins
keywords: "Azure, Contêineres, Jenkins"
ms.assetid: dd719bda-6d1f-452b-a918-9a3aa9302107
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 435262867e8ead23d1453e8a2171f1c215fc5205


---
# <a name="azure-container-service-plugin-for-jenkins"></a>Plug-in do Serviço de Contêiner do Azure para Jenkins
O plug-in do Serviço de Contêiner do Azure ajuda a simplificar o processo de implantação de contêineres [Docker] usando o [Marathon] para um Cluster de Contêiner do Azure que executa um sistema de processamento distribuído, como o [Sistema Operacional de Datacenter do Mesosphere (DC/SO)][mesosphere] ou o [Apache Mesos][mesos].

Este tutorial mostrará como instalar o Plug-in do Serviço de Contêiner do Azure para Jenkins, e como configurar um projeto Jenkins que criará um Serviço de Contêiner do Azure com o Marathon e um orquestrador DC/SO e usar o Marathon para implantar um contêiner Docker no cluster. Se o cluster do Serviço de Contêiner do Azure não existir, o Jenkins criará o cluster de contêiner e usará o Marathon para implantar seu contêiner do docker no cluster quando ele é criado; caso contrário, o Jenkins implantará seu contêiner do docker no cluster existente do Serviço de Contêiner do Azure usando o Marathon.

Para saber mais sobre o Serviço de Contêiner do Azure, confira o artigo [Introdução ao Serviço de Contêiner do Azure][acs-intro].

### <a name="prerequisites"></a>Pré-requisitos
Antes de realizar as etapas neste artigo, você precisa registrar e autorizar o aplicativo cliente e, em seguida, recuperar a ID do cliente e o Segredo do Cliente que será enviado ao Azure Active Directory durante a autenticação. Para saber mais sobre esses pré-requisitos, confira os artigos a seguir:

* [Integração de aplicativos com o Azure Active Directory][integrate-apps-with-AAD]
* [Registrar um aplicativo cliente][register-client-app]

Além disso, você precisará baixar o arquivo **azure-acs-plugin.hpi** da seguinte URL:

* [https://github.com/Microsoft/azure-acs-plugin/tree/master/install](https://github.com/Microsoft/azure-acs-plugin/tree/master/install)

## <a name="how-to-install-the-azure-container-service-plugin-for-jenkins"></a>Como instalar o Plug-in do Serviço de Contêiner do Azure para Jenkins
1. [Baixe o arquivo **azure-acs-plugin.hpi** do GitHub][azure-acs-plugin-install]
2. Faça logon no seu painel do Jenkins.
3. No painel, clique em **Gerenciar Jenkins**.
   
    ![Gerenciar o Jenkins][jenkins-dashboard]
4. Na página **Gerenciar Jenkins**, clique em **Gerenciar Plug-ins**.
   
    ![Gerenciar plug-ins][manage-jenkins]
5. Clique na guia **Avançado** e clique em **Procurar** na seção **Carregar Plug-in**. Navegue até o local onde você baixou o **do azure-acs-plugin.hpi** arquivo o **pré-requisitos**, e clique no **carregar** depois de selecionar o arquivo.
   
    ![Carregar o plug-in][upload-plugin]
6. Reinicie o Jenkins se necessário.

## <a name="configure-the-azure-container-service-plugin"></a>Configurar o Plug-in do Serviço de Contêiner do Azure
1. No painel do Jenkins, clique em um dos seus projetos.
   
    ![Selecionar projeto][select-project]
2. Na página do projeto, clique em **Configurar** no menu à esquerda.
   
    ![Configurar projeto][configure-project]
3. Na seção **Ações de Pós-compilação**, clique no menu suspenso **Adicionar ação pós-compilação** e selecione **Configuração do Serviço de Contêiner do Azure**. 
   
    ![Opções avançadas][advanced-options]
4. Quando a seção **Configuração do Serviço de Contêiner do Azure** for exibida, insira sua ID de assinatura, sua ID de cliente, o segredo do cliente e o Ponto de Extremidade do Token OAuth 2.0.
   
    ![Configuração do Serviço de Contêiner do Azure][azure-container-service-config]
5. Na seção **Configuração de Perfil do ACS**, digite a região, o prefixo de nome DNS, a contagem de agentes, o tamanho da VM de agente, o nome de usuário do Administrador, a contagem principal e a chave pública RSA do SSH.
   
    ![Configuração do Perfil do ACS][acs-profile-configuration]
6. Na seção **Configuração do Perfil do Marathon**, digite o caminho que está no arquivo de configuração do Marathon, seu caminho de arquivo particular RSA do SSH e sua senha do arquivo privado RSA do SSH.
   
    ![Configuração do Perfil do Marathon][marathon-profile-configuration]
7. Clique em **Salvar** para salvar as configurações do projeto.
   
    ![Salvar projeto][save-project]
8. Clique em **Criar Agora** no menu à esquerda.
   
    ![Compilar projeto][build-project]

Após a conclusão da compilação, os logs ficarão disponíveis nos logs do console de compilações.

> [!NOTE]
> Se o cluster do Serviço de Contêiner do Azure não existir, o Jenkins criará o cluster de contêiner e usará o Marathon para implantar seu contêiner do docker no cluster quando ele é criado; caso contrário, o Jenkins implantará seu contêiner do docker no cluster existente do Serviço de Contêiner do Azure usando o Marathon.
> 
> 

<a name="see-also"></a>

## <a name="see-also"></a>Consulte também
Para saber mais sobre como trabalhar com o Serviço de Contêiner do Azure e os clusters DC/SO, confira os seguintes artigos:

* [Introdução ao Serviço de Contêiner do Azure][acs-intro]
* [Implantar um cluster do Serviço de Contêiner do Azure][acs-deploy]
* [Conectar a um cluster do Serviço de Contêiner do Azure][acs-connect]
* [Gerenciamento de contêiner por meio da interface do usuário da Web][acs-webui-management]

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure].

<!-- URL List -->

[azure-acs-plugin-install]: https://github.com/Microsoft/azure-acs-plugin/tree/master/install
[acs-intro]: ./container-service-intro.md
[acs-deploy]: ./container-service-deployment.md
[acs-connect]: ./container-service-connect.md
[acs-webui-management]: ./container-service-mesos-marathon-ui.md
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx

[Marathon]: https://mesosphere.github.io/marathon/
[Docker]: http://docker.io/
[mesosphere]: https://mesosphere.com/products/
[mesos]: https://mesos.apache.org/

[Central de desenvolvimento Java do Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[jenkins-dashboard]: ./media/container-service-plugin-for-jenkins/jenkins-dashboard.png
[manage-jenkins]: ./media/container-service-plugin-for-jenkins/manage-jenkins.png
[search-plugins]: ./media/container-service-plugin-for-jenkins/search-for-azure-plugin.png
[install-plugin]: ./media/container-service-plugin-for-jenkins/install-plugin.png
[select-project]: ./media/container-service-plugin-for-jenkins/select-project.png
[configure-project]: ./media/container-service-plugin-for-jenkins/configure-project.png
[advanced-options]: ./media/container-service-plugin-for-jenkins/advanced-options.png
[azure-container-service-config]: ./media/container-service-plugin-for-jenkins/azure-container-service-configuration.png
[acs-profile-configuration]: ./media/container-service-plugin-for-jenkins/acs-profile-configuration.png
[marathon-profile-configuration]: ./media/container-service-plugin-for-jenkins/marathon-profile-configuration.png
[save-project]: ./media/container-service-plugin-for-jenkins/save-project.png
[build-project]: ./media/container-service-plugin-for-jenkins/build-project.png
[upload-plugin]: ./media/container-service-plugin-for-jenkins/upload-plugin.png



<!--HONumber=Nov16_HO3-->


