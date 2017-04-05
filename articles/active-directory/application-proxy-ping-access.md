---
title: PingAccess para o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: "Publique aplicativos com o PingAccess e o Proxy de Aplicativo a fim de oferecer suporte à autenticação baseada em cabeçalho."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 173607c481d0ba7ceece6310fcd131ff622a0677
ms.lasthandoff: 03/23/2017

---

# <a name="publish-applications-that-support-header-based-authentication-with-pingaccess-for-azure-ad"></a>Publicar aplicativos que oferecem suporte a autenticação baseada em cabeçalho com o PingAccess para Azure AD

O Proxy de Aplicativo do Azure Active Directory e o PingAccess fizeram uma parceria para fornecer aos clientes do Azure Active Directory acesso a ainda mais aplicativos. O PingAccess expande as [ofertas existentes do Proxy de Aplicativo](active-directory-application-proxy-get-started.md) para incluir acesso remoto a aplicativos que usam cabeçalhos para autenticação. 

## <a name="what-is-pingaccess-for-azure-ad"></a>O que é PingAccess para Azure AD?

Para conceder aos usuários acesso aos aplicativos que usam cabeçalhos para autenticação, publique o aplicativo para acesso remoto no Proxy de Aplicativo e no PingAccess. O Proxy de Aplicativo trata esses aplicativos como qualquer outro, usando o Azure AD para autenticar o acesso e, depois, passando o tráfego por meio do serviço de conector. O PingAccess fica na frente dos aplicativos e converte o token de acesso do Azure AD em um cabeçalho, para que o aplicativo receba a autenticação em um formato que consiga ler. 

Os usuários não perceberão algo diferente quando entrarem para usar seus aplicativos corporativos. Eles ainda podem trabalhar de qualquer lugar e em qualquer dispositivo. Quando seus usuários estão no escritório, o Proxy de Aplicativo nem o PingAccess interceptam o tráfego, para que os usuários tenham a mesma experiência de sempre.

Como os conectores do Proxy de Aplicativo direcionam o tráfego remoto a todos os aplicativos, independentemente do tipo de autenticação, eles também continuarão a balancear a carga automaticamente. 

## <a name="how-do-i-get-access"></a>Como posso obter acesso?

Como esse cenário é oferecido por meio de uma parceria entre o Azure Active Directory e o PingAccess, você precisa de licenças para os dois serviços. As assinaturas do Azure Active Directory Premium vêm com uma licença inicial do PingAccess para que você possa configurar até 20 aplicativos com esse fluxo. 

Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

## <a name="publish-your-first-application"></a>Publicar seu primeiro aplicativo

Este artigo destina-se a pessoas que estão publicando um aplicativo com esse cenário pela primeira vez. Ele explica como começar com o Aplicativo e o PingAccess, além das etapas de publicação. Se você já configurou ambos os serviços, mas quer relembrar as etapas de publicação, ignore as duas seções de registro.

>[!NOTE]
>Como esse cenário é uma parceria entre o Azure AD e o PingAccess, algumas das instruções estão no site da Identidade do Ping. 

### <a name="install-an-application-proxy-connector"></a>Instalar um conector do Proxy de Aplicativo

Se o Proxy de Aplicativo já estiver habilitado, e um conector estiver instalado, ignore essas etapas.

O conector do Proxy de Aplicativo é um serviço do Windows Server que direciona o tráfego de seus funcionários remotos para seus aplicativos publicados. Para obter informações mais detalhadas, confira [Habilitar o Proxy de Aplicativo no Portal do Azure](active-directory-application-proxy-enable.md).

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global. 
2. Selecione **Azure Active Directory** > **Proxy de aplicativo**.
3. Selecione **Baixar Conector** para iniciar o download do conector do Proxy de Aplicativo. Siga as instruções de instalação. 
4. O download do conector deve habilitar automaticamente o Proxy de Aplicativo para seu diretório, mas se isso não acontecer, selecione **Habilitar Proxy de Aplicativo**. 

![Habilitar o Proxy de Aplicativo e baixar o conector](./media/application-proxy-ping-access/install-connector.png)

### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Adicionar o aplicativo ao Azure AD com o Proxy de Aplicativo

Há duas partes nesta seção. Primeiro, você precisa publicar o aplicativo no Azure AD. Depois, colete algumas informações sobre o aplicativo para usar durante as etapas do PingAccess. 

#### <a name="publish-the-app"></a>Publicar o aplicativo

1. Se você não fez isso na última seção, entre no [Portal do Azure](https://portal.azure.com) como um administrador global. 
2. Selecione **Azure Active Directory** > **Aplicativos empresariais**. 
3. Selecione **Adicionar** na parte superior da folha. 
4. Selecione **Aplicativo local**.
5. Preencha os campos obrigatórios com informações sobre seu novo aplicativo. Use as diretrizes a seguir para as configurações:
  - **URL interna**: normalmente, você fornece a URL que levará você até a página de entrada do aplicativo quando você estiver na rede corporativa. Para essa parceria o conector deve tratar o proxy do PingAccess como a página inicial do aplicativo. Use este formato: `https://<host name of your PA server>:<port>/<App path name>`. A porta é a 3000 por padrão, mas você pode configurá-la no PingAccess.
  - **Método de Pré-autenticação**: Azure Active Directory
  - **Converter URL em cabeçalhos**: não
6. Selecione **Adicionar** na parte inferior da folha. Seu aplicativo é adicionado e abre o menu de início rápido. 
7. No menu de início rápido, selecione **Atribuir um usuário para teste** e adicione pelo menos um usuário para o aplicativo. Verifique se essa conta de teste tem acesso ao aplicativo local. 
8. Selecione **Atribuir** para salvar a atribuição do usuário de teste. 
9. Na folha de gerenciamento do aplicativo, selecione **Logon único**. 
10. Escolha **Logon baseado em cabeçalho** no menu suspenso. Selecione **Salvar**.

  ![Selecione o logon com base no cabeçalho](./media/application-proxy-ping-access/sso-header.PNG)

11. Feche a folha Aplicativos empresariais ou role a tela totalmente para a esquerda para retornar ao menu do Azure Active Directory. 
12. Selecione **Registros do Aplicativo**.
13. Selecione o aplicativo que você acabou de adicionar e, depois, **URLs de Resposta**. 
14. Verifique se a URL externa que você atribuiu ao seu aplicativo na etapa 5 está na lista de URLs de Resposta. Se não estiver, adicione-a agora. 
15. Na folha de configurações do aplicativo, selecione **Permissões necessárias**. 
16. Selecione **Adicionar**. Para a API, escolha **Windows Azure Active Directory**, depois, **Selecionar**. Para as permissões, escolha **Leitura e gravação em todos os aplicativos** e **Entrar e ler o perfil do usuário**, depois **Selecionar** e **Concluído**.  

  ![Selecionar permissões](./media/application-proxy-ping-access/select-permissions.png) 

#### <a name="collect-information-for-the-pingaccess-steps"></a>Coletar informações sobre as etapas do PingAccess

1. Ainda na folha de configurações de seu aplicativo, selecione **Propriedades**. Salve o valor **ID do Aplicativo**. Isso é usado para a ID do cliente quando você configura o PingAccess.
2. Na folha de configurações, selecione **Chaves**. 
3. Crie uma chave inserindo uma descrição de chave e escolhendo uma data de validade no menu suspenso. 
4. Selecione **Salvar**. Um GUID será exibido no campo **Valor**. 

  Salve esse valor agora, pois você não poderá vê-lo novamente depois de fechar esta janela. 

5. Feche a folha Registros de aplicativo empresariais ou role a tela totalmente para a esquerda para retornar ao menu do Azure Active Directory.
6. Selecione **Propriedades**.
7. Salve o GUID em **ID de Diretório**. 

### <a name="download-pingaccess-and-configure-your-app"></a>Baixar o PingAccess e configurar seu aplicativo

As etapas detalhadas para a parte do PingAccess deste cenário continuam na documentação de Identidade de Ping, [Configurar o PingAccess para o Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Essas etapas orientarão você durante o processo de obtenção de uma conta do PingAccess, se você ainda não tiver uma, instalação do servidor do PingAccess e criação de uma conexão do Provedor do Azure AD OIDC com a ID de Diretório que você copiou do Portal do Azure. Depois, use os valores de ID do Aplicativo e Chave para criar uma Sessão da Web no PingAccess. Em seguida, configure o mapeamento de identidade e crie um host virtual, site e aplicativo.

### <a name="test-your-app"></a>Testar seu aplicativo

Depois de concluir todas essas etapas, seu aplicativo entrar em execução. Para testá-lo, abra um navegador e navegue até a URL externa que você criou quando publicou o aplicativo no Azure. Entre com a conta de teste que você atribuiu ao aplicativo. 

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas de Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)

