---
title: 'Tutorial: Criar um aplicativo Web de página única do Azure Time Series Insights | Microsoft Docs'
description: Saiba como criar um aplicativo Web de página única que consulta e processa dados de um ambiente TSI.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: a9baf36221c99ece0703e2caa3f3e6c3b9364480
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312199"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Tutorial: Criar um aplicativo Web de página única do Azure Time Series Insights

Este tutorial orienta você pelo processo de criação de seu próprio SPA (aplicativo Web de página única) para acessar dados TSI, modelados de acordo com o [aplicativo de exemplo do TSI (Time Series Insights)](https://insights.timeseries.azure.com/clientsample). Neste tutorial, você aprenderá:

> [!div class="checklist"]
> * O design do aplicativo
> * Como registrar o aplicativo no Azure AD (Active Directory)
> * Como criar, publicar e testar o aplicativo Web 

## <a name="prerequisites"></a>Pré-requisitos

Inscreva-se em uma [assinatura do Azure gratuita](https://azure.microsoft.com/free/) se já não possuir uma. 

Você também precisará instalar o Visual Studio se ainda não o fez. Para este tutorial, você pode [baixar/instalar a versão gratuita de Comunidade ou uma avaliação gratuita](https://www.visualstudio.com/downloads/).

## <a name="application-design-overview"></a>Visão geral do design do aplicativo

Conforme mencionado, o aplicativo de exemplo do TSI fornece a base para o design e o código usados neste tutorial. O código inclui o uso da biblioteca JavaScript de cliente do TSI. A biblioteca de cliente do TSI fornece uma abstração para duas categorias principais de API:

- **Métodos de wrapper para chamar as APIs de consulta TSI**: APIs REST que permitem consultar dados TSI usando expressões baseadas em JSON. Os métodos estão organizados no namespace `TsiClient.server` da biblioteca.
- **Métodos para criar e popular os vários tipos de controles de criação de gráficos**: Métodos que são usados para visualizar os dados de TSI em uma página da Web. Os métodos estão organizados no namespace `TsiClient.ux` da biblioteca.

Este tutorial também usa os dados do ambiente de TSI do aplicativo de exemplo. Para obter detalhes sobre a estrutura do aplicativo de exemplo do TSI e seu uso da biblioteca de cliente do TSI, consulte o tutorial [Explorar a biblioteca de cliente JavaScript do Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registrar o aplicativo no Azure AD 

Antes de compilar o aplicativo, você precisará registrá-lo no Azure AD. O registro fornece a configuração de identidade de um aplicativo, permitindo que ele use o suporte do OAuth para logon único. O OAuth exige que os SPAs usem a concessão de autorização "implícita", que você atualizará no manifesto do aplicativo. Um manifesto do aplicativo é uma representação JSON da configuração de identidade do aplicativo. 

1. Entre no [Portal do Azure](https://portal.azure.com) usando sua conta da assinatura do Azure.  
1. Selecione o recurso **Azure Active Directory** no painel esquerdo, seguido de **Registros do aplicativo** e **+ Novo registro de aplicativo**:  
   
   ![Registro de aplicativo do Azure AD no Portal do Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

1. Na página **Criar**, preencha os parâmetros necessários:
   
   Parâmetro|DESCRIÇÃO
   ---|---
   **Nome** | Forneça um nome de registro relevante.  
   **Tipo de Aplicativo** | Como você está criando um aplicativo Web do SPA, deixe como "API/Aplicativo Web."
   **URL de logon** | Insira a URL para a página inicial/página de entrada do aplicativo. Como o aplicativo estará hospedado no Serviço de Aplicativo do Azure (posteriormente), você deverá usar a URL dentro do domínio "https:\//azurewebsites.net". Neste exemplo, o nome é baseado no nome do registro.

   Quando terminar, clique em **Criar** para criar o novo registro do aplicativo.

   ![Registro do aplicativo do Azure AD no Portal do Azure – criação](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

1. Os aplicativos de recursos fornecem APIs REST para uso por outros aplicativos e também são registrados no Azure AD. As APIs fornecem acesso granular/protegido para aplicativos cliente pela exposição de "escopos". Como seu aplicativo chamará a API do "Azure Time Series Insights", você precisa especificar a API e o escopo para os quais a permissão será solicitada/concedida no tempo de execução. Selecione **Configurações**, seguido de **Permissões necessárias** e **+ Adicionar**:

   ![Adicionar permissões do Azure AD no Portal do Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

1. Em **Adicionar acesso à API**, clique em **1 Selecionar uma API** para especificar a API do TSI. Na página **Selecionar uma API** digite "azure time" no campo de pesquisa. Em seguida, selecione a API do "Azure Time Series Insights" na lista de resultados e clique em **Selecionar**: 

   ![Adicionar permissões do Azure AD no Portal do Azure – API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

1. Agora você pode especificar um escopo na API. Novamente na página **Adicionar acesso à API**, clique em **2 Selecionar permissões**. Na página **Habilitar Acesso**, selecione o escopo "Serviço Azure Time Series Insights". Clique em **Selecionar**, o que o levará de volta para a página **Adicionar acesso à API** para poder clicar em **Concluído**:

   ![Adicionar permissões do Azure AD no Portal do Azure – escopo](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

1. Quando retornar para a página **Permissões necessárias**, observe que a API "Azure Time Series Insights" agora aparece na lista. Você também precisa de consentimento prévio para o aplicativo poder acessar a API e o escopo em nome de todos os usuários. Clique no botão **Conceder permissões** na parte superior e selecione **Sim**:

   ![Permissões obrigatórias para do Azure AD no Portal do Azure – consentimento](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)

1. Conforme mencionado anteriormente, você também precisa atualizar o manifesto do aplicativo. Clique no nome do aplicativo na trilha de navegação a fim de voltar para a página **Aplicativo registrado**. Selecione **Manifesto**, altere a `oauth2AllowImplicitFlow` propriedade para `true` e clique em **Salvar**:

   ![Manifesto de atualização do Azure AD no Portal do Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

1. Por fim, clique na trilha de navegação a fim de voltar para a página **Aplicativo registrado** novamente e copie a URL da **Página inicial** e as propriedades **ID do aplicativo** de seu aplicativo. Você usará essas propriedades em uma etapa posterior:

   ![Propriedades do Azure AD no Portal do Azure](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## <a name="build-and-publish-the-web-application"></a>Compilar e publicar o aplicativo Web

1. Crie um diretório para armazenar os arquivos do projeto de aplicativo. Em seguida, navegue até cada uma das seguintes URLs, clique no link "Raw" na área superior direita da página e "Salvar como" no diretório do projeto:

   > [!NOTE]
   > Dependendo do navegador, você precisará corrigir a extensão de arquivo (como HTML ou CSS) antes de salvá-lo.

   - **index.html** HTML e JavaScript para a página https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html
   - **sampleStyles.css:** Folha de estilos CSS: https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css
    
1. Inicie e entre no Visual Studio para criar um projeto de aplicativo Web. No menu **Arquivo**, selecione as opções **Abrir**, **Site da Web**. No diálogo **Abrir Site da Web**, selecione o diretório de trabalho onde você armazenou os arquivos HTML e CSS e clique em **Abrir**:

   ![VS - Arquivo Abrir site da Web](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

1. Abra o **Solution Explorer** no menu **Exibição** do Visual Studio. Você deve ver a nova solução, com um projeto de site da Web (ícone de globo), que contém os arquivos HTML e CSS:

   ![VS - Nova solução no gerenciador de soluções](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

1. Antes de publicar o aplicativo, você precisará atualizar partes do código JavaScript em **index.html**: 

    a. Primeiro, altere os caminhos da referência do JavaScript e dos arquivos de folha de estilos no elemento `<head>`. Abra o arquivo **index.html** na solução do Visual Studio e localize as linhas de código JavaScript a seguir. Remova marcas de comentário nas três linhas abaixo de "LINKS DE RECURSOS DE PRODUÇÃO" e comente as três linhas abaixo de "LINKS DE RECURSOS DE DESENVOLVIMENTO":
   
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Seu código alterado deve ficar mais ou menos assim:

      ```javascript
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="sampleStyles.css"></link>
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css"></link>

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="pages/samples/sampleStyles.css"></link>
      <script src="dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="dist/tsiclient.css"></link> -->
      ```

   b. Em seguida, altere a lógica de token de acesso para usar o novo registro de aplicativo do Azure AD. Alterar as seguintes variáveis `clientID` e `postLogoutRedirectUri`, respectivamente, para usar a ID do aplicativo e a URL da página inicial que você copiou na etapa 9 da seção [Registrar o aplicativo no Azure AD](#register-the-application-with-azure-ad):

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Seu código alterado deve ficar mais ou menos assim:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   c. Salve o **index.html** quando terminar de editá-lo.

1. Agora, publique o aplicativo Web em sua assinatura do Azure como um Serviço de Aplicativo do Azure:  

   > [!NOTE]
   > Vários campos nas caixas de diálogo a seguir são preenchidos com dados de sua assinatura do Azure. Por conta disso, pode levar alguns segundos até cada diálogo carregar completamente e você poder continuar.  

    a. Clique com o botão direito do mouse no nó do projeto de site da Web no **Gerenciador de Soluções** e selecione **Publicar Aplicativo Web**:  

      ![VS – Publicar aplicativo Web no Gerenciador de Soluções](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. Selecione **Serviço de Aplicativo do Microsoft Azure** para criar um destino de publicação:  

      ![VS – Perfil de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)  

   c. Selecione a assinatura a ser usada para publicar o aplicativo e clique em "Novo": 

      ![VS – Perfil de publicação – serviço de aplicativo](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)  

   d. Dê alguns segundos ao diálogo **Criar Serviço de Aplicativo** para carregar todos os campos e modifique os seguintes campos:
   
      Campo | DESCRIÇÃO
      ---|---
      **Nome do aplicativo** | Altere para o nome de registro do aplicativo do Azure AD usado na etapa 3 em [Registrar o aplicativo no Azure AD](#register-the-application-with-azure-ad). 
      **Grupo de recursos** | Usando o botão **Novo...**  botão, altere para corresponder ao campo **Nome do Aplicativo**.
      **Plano do Serviço de Aplicativo** | Usando o botão **Novo...**  botão, altere para corresponder ao campo **Nome do Aplicativo**.

      Ao terminar, clique em **Criar**. O botão **Exportar** no canto inferior esquerdo é substituído por "Implantando:" durante alguns segundos, enquanto os recursos do Azure estão sendo criados. Após a criação dos recursos, você voltará para o diálogo anterior. 

      ![VS – Perfil de publicação – adicionar novo serviço de aplicativo](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)  

   e. Ao retornar para o diálogo **Publicar**, verifique se o **Método de publicação** está definido como "Implantação na Web". Altere também a **URL de destino** para usar `https` em vez de `http`, a fim de corresponder ao registro de aplicativo do Azure AD. Em seguida, clique em "Publicar" para implantar o aplicativo Web:  

      ![VS – Publicar aplicativo Web – publicar o serviço de aplicativo](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)  

   f. Você deve ver um log de publicação bem-sucedido na janela **Saída** do Visual Studio. Após a implantação, o Visual Studio também abrirá o aplicativo Web em uma guia do navegador e solicitará a entrada. Depois de entrar com êxito, você verá todos os controles do TSI preenchidos com dados:  

      [![VS – Publicar aplicativo Web – saída de log de publicação](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-output.png#lightbox)

      ![Aplicativo SPA do TSI – logon](media/tutorial-create-tsi-sample-spa/tsispaapp-azurewebsites-net.png)  

## <a name="troubleshooting"></a>solução de problemas  

Condição/código de erro | DESCRIÇÃO
---------------------| -----------
*AADSTS50011: Nenhum endereço de resposta está registrado para o aplicativo.* | O registro do Azure AD não tem a propriedade "URL de resposta". Vá para a página **Configurações** / **URLs de Resposta** do registro de seu aplicativo do Azure AD. Verifique se a URL de **Logon** especificada na etapa 3 de [Registrar o aplicativo no Azure AD](#register-the-application-with-azure-ad) está presente. 
*AADSTS50011: A URL de resposta especificada na solicitação não corresponde às URLs de resposta configuradas para o aplicativo: '<Application ID GUID>'.* | O `postLogoutRedirectUri` especificado na etapa 4.b de [Compilar e publicar o aplicativo Web](#build-and-publish-the-web-application) deve corresponder ao valor especificado na propriedade **Configurações** / **URLs de Resposta** no registro de seu aplicativo do Azure AD. Altere também a **URL de Destino** para usar `https` de acordo com a etapa 5.e de [Compilar e publicar o aplicativo Web](#build-and-publish-the-web-application).
O aplicativo Web é carregado, mas tem uma página de entrada somente texto e sem estilo, com uma tela de fundo branca. | Verifique se os caminhos discutidos na etapa 4.a de [Compilar e publicar o aplicativo Web](#build-and-publish-the-web-application) estão corretos. Se o aplicativo Web não puder localizar os arquivos .css, a página não será estilizada corretamente.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços do Azure em execução. Se você não planeja concluir esta série de tutoriais, recomendamos a exclusão de todos os recursos para evitar incorrer em custos desnecessários. 

No menu à esquerda no portal do Azure:

1. Clique no ícone de **Grupos de recursos**, depois selecione o grupo de recursos que você criou para o ambiente TSI. Na parte superior da página, clique em **Excluir grupo de recursos**, insira o nome do grupo de recursos e depois clique em **Excluir**. 
1. Clique no ícone de **Grupos de recursos**, depois selecione o grupo de recursos que foi criado com o acelerador de solução de simulação do dispositivo. Na parte superior da página, clique em **Excluir grupo de recursos**, insira o nome do grupo de recursos e depois clique em **Excluir**. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * O design do aplicativo
> * Como registrar o aplicativo no Azure AD (Active Directory)
> * Como criar, publicar e testar o aplicativo Web 

Este tutorial se integra ao Azure AD usando a identidade do usuário conectado para adquirir um token de acesso. Para saber como acessar a API do TSI usando a identidade de um aplicativo de serviço/daemon, confira:

> [!div class="nextstepaction"]
> [Autenticação e autorização para API do Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
