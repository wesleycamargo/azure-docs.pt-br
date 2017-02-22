---
title: "Logon único com o Proxy de Aplicativo | Microsoft Docs"
description: "Aborda como fornecer o logon único usando o Proxy de Aplicativo do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: ded0d9c9-45f6-47d7-bd0f-3f7fd99ab621
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c308524e41047220fbad026edb6a87f196d89580
ms.openlocfilehash: 3f293996d2565c495f707f99a0bb75bb7c24054e

---

# <a name="single-sign-on-with-application-proxy"></a>Logon único com Proxy de Aplicativo
O logon único é um elemento fundamental do Proxy de Aplicativo do Azure AD. Ele oferece a melhor experiência do usuário por meio das seguintes etapas:

1. Um usuário entra na nuvem.  
2. Todas as validações de segurança ocorrem na nuvem (pré-autenticação).  
3. Quando a solicitação é enviada ao aplicativo local, o Conector de Proxy de Aplicativo representa o usuário. O aplicativo de back-end acha que se trata de um usuário normal proveniente de um dispositivo de domínio.

![Diagrama de acesso do usuário final, por meio do Proxy de Aplicativo, à rede corporativa](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

O Proxy de Aplicativo do Azure AD permite que você forneça uma experiência de logon único (SSO) a seus usuários. Use as instruções a seguir para publicar seus aplicativos usando o SSO:

## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>SSO para aplicativos IWA local usando o KCD com Proxy de Aplicativo
Você pode habilitar o logon único para seus aplicativos usando a IWA (Autenticação Integrada do Windows) concedendo permissão aos Conectores de Proxy de Aplicativo no Active Directory para representar usuários e enviar e receber tokens em seu nome.

### <a name="network-diagram"></a>Diagrama de rede
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

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar com o SSO para o Proxy de aplicativo, certifique-se de que seu ambiente está preparado com as seguintes configurações e definições:

* Seus aplicativos, como os aplicativos Web do SharePoint, são definidos para usar a Autenticação Integrada do Windows. Para sabe rmais, veja [Habilitar suporte para autenticação Kerberos](https://technet.microsoft.com/library/dd759186.aspx), ou para o SharePoint, consulte [Planejar a autenticação Kerberos no SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Todos os seus aplicativos têm nomes de entidade de serviço.
* O servidor que executa o Conector e o servidor que executa o aplicativo que você está publicando são ingressados em domínio e fazem parte desse mesmo domínio ou em domínios confiáveis. Para obter mais informações sobre o ingresso no domínio, consulte [Ingressar um computador em um domínio](https://technet.microsoft.com/library/dd807102.aspx).
* O servidor que executa o conector tem acesso de leitura ao TokenGroupsGlobalAndUniversal para usuários. Isso é uma configuração padrão que talvez seja afetada pela segurança que protege o ambiente. Obtenha mais ajuda sobre essa configuração no [KB2009157](https://support.microsoft.com/en-us/kb/2009157).

### <a name="active-directory-configuration"></a>Configuração do Active Directory
A configuração do Active Directory varia, dependendo se o conector de Proxy de Aplicativo e o servidor publicado estão no mesmo domínio ou não.

#### <a name="connector-and-published-server-in-the-same-domain"></a>O Conector e o servidor publicado estão no mesmo domínio
1. No Active Directory, vá para **Ferramentas** > **Usuários e Computadores**.
2. Selecione o servidor que executa o Conector.
.3. Clique com o botão direito do mouse e selecione **Propriedades** > **Delegação**.
4. Selecione **Confiar no computador para delegação apenas a serviços especificados**. Em **Serviços aos quais esta conta pode apresentar credenciais delegadas**, adicione o valor da identidade SPN do servidor de aplicativos.
5. Isso permite que o Conector do Proxy de Aplicativo represente usuários no AD para os aplicativos definidos na lista.

![Captura de tela da janela Propriedades do Conector SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>O Conector e o servidor publicado estão em domínios diferentes
1. Para obter uma lista de pré-requisitos para trabalhar com o KCD entre domínios, consulte [Delegação restrita de Kerberos nos domínios](https://technet.microsoft.com/library/hh831477.aspx).
2. No Windows 2012 R2, use a propriedade `principalsallowedtodelegateto` no servidor do Conector para habilitar o Proxy de Aplicativo a fim de delegar para o servidor do Conector, em que o servidor publicado é `sharepointserviceaccount` e o servidor de delegação é `connectormachineaccount`.

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

> [!NOTE]
> `sharepointserviceaccount` pode ser a conta do computador do SPS ou uma conta de serviço sob a qual o pool de aplicativos do SPS está sendo executado.
>
>

### <a name="azure-classic-portal-configuration"></a>Trabalho de criação do Portal clássico do Azure
1. Publique seu aplicativo seguindo as instruções descritas em [Publicar aplicativos com o Proxy de Aplicativo](active-directory-application-proxy-publish.md). Certifique-se de selecionar **Azure Active Directory** como o **Método de Pré-autenticação**.
2. Depois que o aplicativo aparecer na lista de aplicativos, selecione-o e clique em **Configurar**.
3. Em **Propriedades**, defina o **Método de Autenticação Interna** como **Autenticação Integrada do Windows**.  
   ![Configuração de Aplicativo Avançada](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Insira o **SPN do Aplicativo Interno** do servidor de aplicativos. Neste exemplo, o SPN para nosso aplicativo publicado é http/lob.contoso.com.  

> [!IMPORTANT]
> Se o UPN local e o UPN no Azure Active Directory não forem idênticos, você precisará configurar a [identidade de logon delegada](#delegated-login-identity) para que a pré-autenticação funcione.
>
>

|  |  |
| --- | --- |
| Método de autenticação interna |Se usar o Azure AD para pré-autenticação, você poderá definir um método de autenticação interna para permitir que os usuários aproveitem o SSO (logon único) para este aplicativo. <br><br> Selecione **IWA** (Autenticação Integrada do Windows) se o aplicativo usar IWA, e você poderá configurar a KCD (Delegação Restrita de Kerberos) a fim de habilitar o SSO para este aplicativo. Os aplicativos que usam IWA devem ser configurados usando a KCD; caso contrário, o Proxy de Aplicativo não poderá publicar esses aplicativos. <br><br> Selecione **Nenhum** se seu aplicativo não usar IWA. |
| SPN do Aplicativo Interno |Esse é o SPN (Nome da Entidade de Serviço) do aplicativo interno, conforme configurado no AD do Azure local. O SPN é usado pelo Conector do Proxy de Aplicativo para buscar tokens Kerberos para o aplicativo usando o KCD. |

## <a name="sso-for-non-windows-apps"></a>SSO para aplicativos não Windows
O fluxo de delegação de Kerberos no Proxy de Aplicativo do AD do Azure é iniciado quando o AD do Azure autentica o usuário na nuvem. Depois que a solicitação chega no local, o conector do Proxy de Aplicativo do AD do Azure emite um tíquete Kerberos em nome do usuário para interagir com o Active Directory local. Esse processo é conhecido como KCD (delegação restrita do Kerberos). Na próxima fase, uma solicitação é enviada ao aplicativo de back-end com esse tíquete Kerberos. Há vários protocolos que definem como enviar essas solicitações. A maioria dos servidores não Windows espera Negotiate/SPNego, que agora tem suporte no Proxy de Aplicativo do AD do Azure.

![Diagrama de SSO não Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

Para obter mais informações sobre o Kerberos, consulte [Tudo o que você deseja saber sobre a delegação restrita de Kerberos (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

### <a name="delegated-login-identity"></a>identidade de logon delegada
A identidade de logon delegada ajuda a lidar com dois cenários de logon diferentes:

* Os aplicativos não Windows normalmente obtém a identidade do usuário na forma de um nome de usuário ou nome de conta SAM, e não de um endereço de email (username@domain).
* Configurações de logon alternativas em que o UPN no Azure AD e o UPN no Active Directory local são diferentes.

Com o Proxy de Aplicativo, você pode selecionar qual identidade deve ser usada para obter o tíquete Kerberos. Essa configuração é por aplicativo. Algumas dessas opções são adequadas para sistemas que não aceitam o formato de endereço de email, e outras são desenvolvidas para logon alternativo.

![Captura de tela de parâmetro de identidade de logon delegada](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Se a identidade de logon delegada for usada, o valor não poderá ser exclusivo para todos os domínios ou florestas em sua organização. Você pode evitar esse problema publicando esses aplicativos duas vezes com dois grupos diferentes de Conectores. Como cada aplicativo tem um público de usuários diferente, é possível ingressar seus Conectores em um domínio diferente.

## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>Trabalhando com o SSO as identidades no local e na nuvem não são idênticas
A menos que configurado de outra forma, o Proxy de Aplicativo pressupõe que os usuários têm a mesma identidade na nuvem e no local. Você pode configurar, para cada aplicativo, qual identidade deve ser usada ao executar o logon único.  

Essa capacidade permite que muitas organizações com identidades diferentes no local e na nuvem usem o SSO da nuvem para aplicativos locais, sem exigir que os usuários insiram senhas e nomes de usuários diferentes. Isso inclui as organizações que:

* Têm vários domínios internamente (joe@us.contoso.com, joe@eu.contoso.com) e um único domínio na nuvem (joe@contoso.com).
* Têm um nome de domínio não roteável internamente (joe@contoso.usa) e um nome legal na nuvem.
* Não usem nomes de domínio internamente (joe)
* Usem aliases diferentes no local e na nuvem. Por exemplo joe-johns@contoso.com vs. joej@contoso.com  

Isso também ajuda com aplicativos que não aceitam endereços na forma de endereço de email, que é um cenário muito comum para servidores back-end não Windows.

### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>Configurando o SSO para diferentes identidades na nuvem e no local
1. Defina as configurações do Azure AD Connect para que a identidade principal seja o endereço de email (email). Isso é feito como parte do processo de personalização, alterando o campo **Nome UPN** nas configurações de sincronização. Essas configurações também determinam como os usuários fazem logon no Office365, em dispositivos Windows10 e outros aplicativos que usam o Azure AD como seu armazenamento de identidade.  
   ![Identificando a captura de tela de usuários - lista suspensa Nome UPN](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Nas definições de configuração de aplicativo para o aplicativo que você deseja modificar, selecione a **Identidade de Logon Delegada** a ser usada:

   * Nome UPN: joe@contoso.com  
   * Nome UPN alternativo: joed@contoso.local  
   * Nome de usuário que faz parte do nome UPN: pedro  
   * Nome de usuário parte do nome UPN alternativo: pedrog  
   * Nome da conta SAM local: dependendo da configuração do controlador de domínio local

   ![Captura de tela de menu suspenso de identidade delegada de logon](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>Solucionando problemas de SSO para diferentes identidades
Se houver um erro no processo de SSO, ele aparecerá no log de eventos do computador do conector conforme explicado na [Solução de problemas](active-directory-application-proxy-troubleshoot.md).
Porém, em alguns casos, a solicitação será enviada com êxito para o aplicativo de back-end embora este aplicativo responderá em várias outras respostas HTTP. Nesses casos, a solução de problemas deve começar examinando o número de evento 24029 na máquina do conector no log de eventos de sessão do Proxy de Aplicativo. A identidade do usuário que foi usada para delegação será exibida no campo "usuário" nos detalhes do evento. Para ativar o log de sessão, selecione **Mostrar logs analíticos e de depuração** no menu de exibição do visualizador de eventos.

## <a name="see-also"></a>Confira também
* [Publique aplicativos com proxy de aplicativo](active-directory-application-proxy-publish.md)
* [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)
* [Trabalho com aplicativos com reconhecimento de declaração](active-directory-application-proxy-claims-aware-apps.md)
* [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)

Para ver as últimas notícias e atualizações, confira o [blog Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg



<!--HONumber=Feb17_HO2-->


