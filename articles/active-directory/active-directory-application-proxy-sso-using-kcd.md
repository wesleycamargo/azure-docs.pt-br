<properties
	pageTitle="SSO para Aplicativos IWA Local usando o KCD com Proxy de Aplicativo"
	description="Aborda como colocar em funcionamento com o Proxy de aplicativo do Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="rkarlin"/>



# SSO para aplicativos IWA local usando o KCD com Proxy de Aplicativo


Você pode habilitar SSO (Logon Único) para seus aplicativos usando a IWA (Autenticação Integrada do Windows) concedendo permissão aos Conectores de Proxy de Aplicativo no Active Directory para representar usuários e enviar e receber tokens em seu nome.

> [AZURE.IMPORTANT]O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).


## Diagrama de rede

![Diagrama de fluxo de autenticação do Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

Este diagrama explica o fluxo de quando um usuário tenta acessar um aplicativo local que usa IWA. O fluxo geral é:

1. O usuário insere a URL para acessar o aplicativo local por meio do Proxy de Aplicativo.
2. O Proxy de Aplicativo redireciona a solicitação para serviços de autenticação do AD do Azure para pré-autenticação. Neste ponto, o AD do Azure se aplica a qualquer política de autenticação e autorização aplicável, tal como autenticação multifator. Se o usuário for validado, o AD do Azure cria um token e o envia para o usuário.
3. O usuário passa o token para o Proxy de Aplicativo.
4. O Proxy de Aplicativo valida o token e recupera o nome UPN (nome UPN), enviando em seguida a solicitação, o UPN e o SPN (Nome da Entidade de Serviço) para o Conector por meio de um canal de segurança duplamente autenticado.
5. O conector realiza a negociação do KCD (Delegação Restrita de Kerberos) com o AD local, representando o usuário para obter um token Kerberos para o aplicativo.
6. O Active Directory envia o token Kerberos para o aplicativo para o Conector.
7. O Conector envia a solicitação original para o servidor de aplicativos usando o token Kerberos recebido do AD.
8. O aplicativo envia a resposta para o Conector, que é retornada para o serviço de Proxy de Aplicativo e, finalmente, para o usuário.

### Pré-requisitos

1. Certifique-se de que seus aplicativos, tais como seus aplicativos Web do SharePoint, sejam definidos para usar a Autenticação Integrada do Windows. Para obter mais informações, consulte [Habilitar suporte para autenticação Kerberos](https://technet.microsoft.com/library/dd759186.aspx), ou para o SharePoint, consulte [Planejar a autenticação Kerberos no SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
2. Criar nomes de entidade de serviço para seus aplicativos.
3. Certifique-se de que o servidor que executa o Conector e o servidor que executa o aplicativo que você está publicando estejam ingressados do domínio e façam parte do mesmo domínio. Para obter mais informações sobre o ingresso no domínio, consulte [Ingressar um computador em um domínio](https://technet.microsoft.com/library/dd807102.aspx).


## Configuração do Active Directory

A configuração do Active Directory varia, dependendo se o conector de Proxy de Aplicativo e o servidor publicado estão no mesmo domínio ou não.

### O Conector e o servidor publicado estão no mesmo domínio

No Active Directory, vá para **Ferramentas** > **Usuários e Computadores**. Selecione o servidor que executa o Conector. Clique com o botão direito do mouse e selecione **Propriedades** > **Delegação**. Selecione **Confiar neste computador para delegação apenas a serviços especificados** e, em **Serviços aos quais esta conta pode apresentar credenciais delegadas**, adicione o valor para a identidade do SPN (Nome da Entidade de Serviço) do servidor de aplicativos. Isso permite que o Conector do Proxy de Aplicativo represente usuários no AD para os aplicativos definidos na lista.

![Captura de tela da janela Propriedades do Conector SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

### O Conector e o servidor publicado estão em domínios diferentes

1. Para obter uma lista de pré-requisitos para trabalhar com o KCD entre domínios, consulte [Delegação restrita de Kerberos nos domínios](https://technet.microsoft.com/library/hh831477.aspx).
2. No Windows 2012 R2, use a propriedade `principalsallowedtodelegateto` no servidor do Conector para habilitar o Proxy de Aplicativo para delegar para o servidor do Conector, em que o servidor publicado é `sharepointserviceaccount` e o servidor de delegação é `connectormachineaccount`.

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE]`sharepointserviceaccount` pode ser a conta do computador do SPS ou uma conta de serviço sob a qual o pool de aplicativos do SPS está sendo executado.


## Configuração do Portal do Azure

1. Publique seu aplicativo seguindo as instruções descritas em [Publicar aplicativos com o Proxy de Aplicativo](active-directory-application-proxy-publish.md). Certifique-se de selecionar **Active Directory do Azure** como o **Método de pré-autenticação**.
2. Depois que o aplicativo aparecer na lista de aplicativos, selecione-o e clique em **Configurar**.
3. Em **Propriedades**, defina o **Método de Autenticação Interna** para **Autenticação Integrada do Windows**.

![Configuração de Aplicativo Avançada](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)

4. Insira o **SPN do Aplicativo Interno** do servidor de aplicativos. Neste exemplo, o SPN para nosso aplicativo publicado é http/lob.contoso.com.

>[AZURE.IMPORTANT]Os UPNs no Active Directory do Azure devem ser idênticos aos UPNs no Active Directory local para que a pré-autenticação funcione. Verifique se o Active Directory do Azure está sincronizado com o Active Directory local.

| | |
| --- | --- |
| Método de autenticação interna | Se você usar o AD do Azure para pré-autenticação, poderá definir um método de autenticação interna para permitir que os usuários aproveitem o logon único (SSO) para este aplicativo. <br><br> Selecione **IWA (Autenticação Integrada do Windows)** se o aplicativo usar IWA, e você pode configurar a Delegação Restrita de Kerberos (KCD) para habilitar o SSO para este aplicativo. Aplicativos que usam IWA devem ser configurados usando a KCD; caso contrário, o Proxy de Aplicativo não conseguirá publicar esses aplicativos. <br><br> Selecione **Nenhum** se o aplicativo não usar IWA. |
| SPN do aplicativo interno | Esse é o SPN (Nome da Entidade de Serviço) do aplicativo interno, conforme configurado no AD do Azure local. O SPN é usado pelo Conector do Proxy de Aplicativo para buscar tokens Kerberos para o aplicativo usando o KCD. |

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=August15_HO9-->