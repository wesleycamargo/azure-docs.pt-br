<properties
	pageTitle="Trabalhando com Aplicativos com Reconhecimento de Declarações no Proxy de Aplicativo"
	description="Aborda como colocar em funcionamento com o Proxy de aplicativo do Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>



# Trabalhando com aplicativos com reconhecimento de declarações no Proxy de Aplicativo


Aplicativos com reconhecimento de declarações executam um redirecionamento para o STS, que por sua vez solicita as credenciais do usuário em troca de um token antes de redirecionar o usuário para o aplicativo. Para habilitar o Proxy de Aplicativo a fim de poder trabalhar com esses redirecionamentos, as etapas a seguir precisam ser executadas para habilitar o Proxy de Aplicativo a fim de trabalhar com aplicativos com reconhecimento de declarações.

> [AZURE.IMPORTANT]O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).


## Pré-requisito

Antes de executar este procedimento, certifique-se de que o STS para o qual o aplicativo com reconhecimento de declarações é redirecionado esteja disponível fora da sua rede local.

### Configuração do Portal do Azure

1. Publique seu aplicativo seguindo as instruções descritas em [Publicar aplicativos com o Proxy de Aplicativo](active-directory-application-proxy-publish.md).
2. Na lista de aplicativos, selecione o aplicativo com reconhecimento de declarações e clique em **Configurar**.
3. Se você escolheu **Passagem** como seu **Método de Pré-autenticação**, selecione **HTTPS** como seu esquema de **URL Externa**.
4. Se você escolheu o Active Directory do Azure como seu **Método de Pré-autenticação**, selecione **Nenhum** como seu **Método de Autenticação Interna**.

### Configuração do AD FS

1. Abra o **Gerenciamento de ADFS**.
2. Acesse a guia **Objeto de confiança de terceira parte confiável**, clique com o botão direito do mouse no aplicativo que você está publicando com o Proxy de Aplicativo e escolha **Propriedades**.

![Objeto de confiança de terceira parte confiável, clique com o botão direito do mouse no nome do aplicativo - captura de tela](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)

3. Na guia **Pontos de Extremidade**, em **Tipo de Ponto de Extremidade**, selecione **Web Services Federation**.
4. Em **URL Confiável**, insira a URL que você inseriu no Proxy de Aplicativo em **URL Externa** e clique em **OK**.

![Adicionar um ponto de extremidade - definir valor de URL Confiável - captura de tela](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)

<!---HONumber=Oct15_HO3-->