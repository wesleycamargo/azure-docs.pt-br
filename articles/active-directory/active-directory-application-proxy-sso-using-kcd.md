<properties
	pageTitle="Logon único com Proxy de Aplicativo | Microsoft Azure"
	description="Aborda como fornecer o logon único usando o Proxy de Aplicativo do Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="kgremban"/>


# Logon único com Proxy de Aplicativo

> [AZURE.NOTE] O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

O logon único é um elemento fundamental do Proxy de Aplicativo do Azure AD. Ele oferece a melhor experiência do usuário por meio das seguintes etapas:
1. Um usuário entra na nuvem
2. Todas as validações de segurança acontecem na nuvem (pré-autenticação)
3. Quando a solicitação é enviada ao aplicativo local, o Conector do Proxy de Aplicativo representa o usuário para que o aplicativo de back-end pense que se trata de um usuário normal proveniente de um dispositivo ingressado no domínio.

![Diagrama de acesso do usuário final, por meio do Proxy de Aplicativo, à rede corporativa](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

O Proxy de Aplicativo do AD do Azure permite que você forneça uma SSO (experiência de um logon único) a seus usuários. Use as instruções a seguir para publicar seus aplicativos usando o SSO:


## SSO para aplicativos IWA local usando o KCD com Proxy de Aplicativo
Você pode habilitar o logon único para seus aplicativos usando a IWA (Autenticação Integrada do Windows) concedendo permissão aos Conectores de Proxy de Aplicativo no Active Directory para representar usuários e enviar e receber tokens em seu nome.


### Diagrama de rede

Este diagrama explica o fluxo de quando um usuário tenta acessar um aplicativo local que usa IWA.

![Diagrama de fluxo de autenticação do Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. O usuário insere a URL para acessar o aplicativo local por meio do Proxy de Aplicativo.
2. O Proxy de Aplicativo redireciona a solicitação para serviços de autenticação do AD do Azure para pré-autenticação. Neste ponto, o AD do Azure se aplica a qualquer política de autenticação e autorização aplicável, tal como autenticação multifator. Se o usuário for validado, o AD do Azure cria um token e o envia para o usuário.
3. O usuário passa o token para o Proxy de Aplicativo.
4. O Proxy de Aplicativo valida o token e recupera o nome UPN (nome UPN), enviando em seguida a solicitação, o UPN e o SPN (Nome da Entidade de Serviço) para o Conector por meio de um canal de segurança duplamente autenticado.
5. O conector realiza a negociação do KCD (Delegação Restrita de Kerberos) com o AD local, representando o usuário para obter um token Kerberos para o aplicativo.
6. O Active Directory envia o token Kerberos para o aplicativo para o Conector.
7. O Conector envia a solicitação original para o servidor de aplicativos usando o token Kerberos recebido do AD.
8. O aplicativo envia a resposta para o Conector, que é retornada para o serviço de Proxy de Aplicativo e, finalmente, para o usuário.

### Pré-requisitos

- Certifique-se de que seus aplicativos, tais como seus aplicativos Web do SharePoint, sejam definidos para usar a Autenticação Integrada do Windows. Para obter mais informações, consulte [Habilitar suporte para autenticação Kerberos](https://technet.microsoft.com/library/dd759186.aspx), ou para o SharePoint, consulte [Planejar a autenticação Kerberos no SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
- Criar nomes de entidade de serviço para seus aplicativos.
- Certifique-se de que o servidor que executa o Conector e o servidor que executa o aplicativo que você está publicando estejam ingressados do domínio e façam parte do mesmo domínio. Para obter mais informações sobre o ingresso no domínio, consulte [Ingressar um computador em um domínio](https://technet.microsoft.com/library/dd807102.aspx).


### Configuração do Active Directory

A configuração do Active Directory varia, dependendo se o conector de Proxy de Aplicativo e o servidor publicado estão no mesmo domínio ou não.

#### O Conector e o servidor publicado estão no mesmo domínio

1. No Active Directory, vá para **Ferramentas** > **Usuários e Computadores**.
2. Selecione o servidor que executa o Conector.
3. Clique com o botão direito do mouse e selecione **Propriedades** > **Delegação**.
4. Selecione **Confiar no computador para delegação apenas a serviços especificados** e, em **Serviços aos quais esta conta pode apresentar credenciais delegadas**, adicione o valor da identidade do SPN do servidor de aplicativos.
5. Isso permite que o Conector do Proxy de Aplicativo represente usuários no AD para os aplicativos definidos na lista.

![Captura de tela da janela Propriedades do Conector SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### O Conector e o servidor publicado estão em domínios diferentes

1. Para obter uma lista de pré-requisitos para trabalhar com o KCD entre domínios, consulte [Delegação restrita de Kerberos nos domínios](https://technet.microsoft.com/library/hh831477.aspx).
2. No Windows 2012 R2, use a propriedade `principalsallowedtodelegateto` no servidor do Conector para habilitar o Proxy de Aplicativo a fim de delegar para o servidor do Conector, em que o servidor publicado é `sharepointserviceaccount` e o servidor de delegação é `connectormachineaccount`.

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE] `sharepointserviceaccount` pode ser a conta do computador do SPS ou uma conta de serviço sob a qual o pool de aplicativos do SPS está sendo executado.


### Trabalho de criação do Portal clássico do Azure

1. Publique seu aplicativo seguindo as instruções descritas em [Publicar aplicativos com o Proxy de Aplicativo](active-directory-application-proxy-publish.md). Não deixe de selecionar **Active Directory do Azure** como o **Método de Pré-autenticação**.
2. Depois que o aplicativo aparecer na lista de aplicativos, selecione-o e clique em **Configurar**.
3. Em **Propriedades**, defina o **Método de Autenticação Interna** como **Autenticação Integrada do Windows**. ![Configuração de Aplicativo Avançada](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Insira o **SPN do Aplicativo Interno** do servidor de aplicativos. Neste exemplo, o SPN para nosso aplicativo publicado é http/lob.contoso.com.  

>[AZURE.IMPORTANT] Os UPNs no Active Directory do Azure devem ser idênticos aos UPNs no Active Directory local para que a pré-autenticação funcione. Verifique se o AD do Azure está sincronizado com o AD local.

| | |
| --- | --- |
| Método de autenticação interna | Se usar o Azure AD para pré-autenticação, você poderá definir um método de autenticação interna para permitir que os usuários aproveitem o SSO (logon único) para este aplicativo. <br><br> Selecione **IWA** (Autenticação Integrada do Windows) se o aplicativo usar IWA, e você poderá configurar a KCD (Delegação Restrita de Kerberos) a fim de habilitar o SSO para este aplicativo. Aplicativos que usam IWA devem ser configurados usando a KCD; caso contrário, o Proxy de Aplicativo não conseguirá publicar esses aplicativos. <br><br> Selecione **Nenhum** se seu aplicativo não usar IWA. |
| SPN do aplicativo interno | Esse é o SPN (Nome da Entidade de Serviço) do aplicativo interno, conforme configurado no AD do Azure local. O SPN é usado pelo Conector do Proxy de Aplicativo para buscar tokens Kerberos para o aplicativo usando o KCD. |


## SSO para aplicativos não Windows
O fluxo de delegação de Kerberos no Proxy de Aplicativo do AD do Azure é iniciado quando o AD do Azure autentica o usuário na nuvem. Depois que a solicitação chega no local, o conector do Proxy de Aplicativo do AD do Azure emite um tíquete Kerberos em nome do usuário para interagir com o Active Directory local. Esse processo é conhecido como KCD (delegação restrita do Kerberos). Na próxima fase, uma solicitação é enviada ao aplicativo de back-end com esse tíquete Kerberos. Há vários protocolos que definem como enviar essas solicitações. A maioria dos servidores não Windows espera Negotiate/SPNego, que agora tem suporte no Proxy de Aplicativo do AD do Azure.

![Diagrama de SSO não Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### Identidade delegada parcial
Aplicativos não Windows normalmente obtém a identidade do usuário na forma de um nome de usuário ou nome de conta SAM, não um endereço de email (username@domain). Isso é diferente da maioria dos sistemas baseados no Windows que preferem um UPN, que é mais conclusivo e garante que nenhuma duplicação entre domínios.

Por esse motivo, o Proxy de Aplicativo permite que você selecione qual identidade aparece no tíquete Kerberos, por aplicativo. Algumas dessas opções são adequadas para sistemas que não aceitam o formato de endereço de email.

![Captura de tela de parâmetro de identidade de logon delegada](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Se a identidade parcial é usada, e talvez essa identidade não seja exclusiva para todos os domínios ou florestas em sua organização, convém publicar esses aplicativos duas vezes usando dois grupos diferentes de Conector. Como cada aplicativo tem um público de usuários diferente, é possível ingressar seus Conectores em um domínio diferente.


## Trabalhando com o SSO as identidades no local e na nuvem não são idênticas
A menos que configurado de outra forma, o Proxy de Aplicativo pressupõe que os usuários têm a mesma identidade na nuvem e no local. Você pode configurar, para cada aplicativo, qual identidade deve ser usada ao executar o logon único.

Essa capacidade permite que muitas organizações com identidades diferentes no local e na nuvem usem o SSO da nuvem para aplicativos locais, sem exigir que os usuários insiram senhas e nomes de usuários diferentes. Isso inclui as organizações que:

- Têm vários domínios internamente (joe@us.contoso.com, joe@eu.contoso.com) e um único domínio na nuvem (joe@contoso.com).

- Têm um nome de domínio não roteável internamente (joe@contoso.usa) e um nome legal na nuvem.

- Não usem nomes de domínio internamente (joe)

- Usem aliases diferentes no local e na nuvem. Por exemplo, joe-johns@contoso.com vs. joej@contoso.com

Isso também ajuda com aplicativos que não aceitam endereços na forma de endereço de email, que é um cenário muito comum para servidores back-end não Windows.


### Configurando o SSO para diferentes identidades na nuvem e no local

1. Defina as configurações do Azure AD Connect para que a identidade principal seja o endereço de email (email). Isso é feito como parte do processo de personalização, alterando o campo **Nome UPN** nas configurações de sincronização. Observe que essas configurações também determinam como os usuários fazem logon no Office365, em dispositivos Windows 10 e em outros aplicativos que usam o Azure AD como seu repositório de identidades. ![Identificando a captura de tela de usuários - lista suspensa Nome UPN](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Nas definições de configuração de aplicativo para o aplicativo que você deseja modificar, selecione a **Identidade de Logon Delegada** a ser usada:
  - UPN: joe@contoso.com  
  - UPN alternativo: joed@contoso.local  
  - Parte do nome de usuário do UPN: joe  
  - Parte do nome de usuário do UPN alternativo: joed  
  - Nome da conta SAM local: dependendo da configuração do controlador de domínio local

  ![Captura de tela de menu suspenso de identidade delegada de logon](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

### Solucionando problemas de SSO para diferentes identidades
Se houver um erro no processo de SSO, ele aparecerá no log de eventos do computador do conector conforme explicado na [Solução de problemas](active-directory-application-proxy-troubleshoot.md). Porém, em alguns casos, a solicitação será enviada com êxito para o aplicativo de back-end embora este aplicativo responderá em várias outras respostas HTTP. Nesses casos, a solução de problemas deve começar examinando o número de evento 24029 na máquina do conector no log de eventos de sessão do Proxy de Aplicativo. A identidade do usuário que foi usada para delegação será exibida no campo "usuário" nos detalhes do evento. Para ativar o log de sessão, selecione **Mostrar logs analíticos e de depuração** no menu de exibição do visualizador de eventos.


## Consulte também

- [Publique aplicativos com proxy de aplicativo](active-directory-application-proxy-publish.md)
- [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)
- [Trabalho com aplicativos com reconhecimento de declaração](active-directory-application-proxy-claims-aware-apps.md)
- [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)

Para ver as últimas notícias e atualizações, confira o [blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=AcomDC_0511_2016-->