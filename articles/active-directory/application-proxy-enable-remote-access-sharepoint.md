---
title: Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: "Cobre as noções básicas sobre os conectores do Proxy de Aplicativo do Azure AD."
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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 8436238546515b66b58b31673d54586e4a20f50f
ms.openlocfilehash: 86f10c04368d1c382939b418c6909ca807b3bf5a


---

# <a name="enable-remote-access-to-sharepoint-with-azure-ad-app-proxy"></a>Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD

Este artigo descreve como integrar o servidor local do SharePoint no proxy de aplicativo do Azure AD.

> [!NOTE]
> O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
> 

##<a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha o SharePoint 2013 ou mais recente instalado e em execução no seu ambiente. Além disso, considere os seguintes pré-requisitos:

* O SharePoint inclui suporte nativo a Kerberos. Portanto, os usuários que acessam sites internos remotamente por meio do Proxy de Aplicativo do Azure AD podem supor que terão uma perfeita experiência de logon único.

* Você precisará fazer algumas mudanças na configuração do seu servidor do SharePoint. É recomendável usar um ambiente de preparo. Dessa forma, você pode fazer atualizações no servidor de preparo primeiro e depois facilitar um ciclo de testes antes de passar para o de produção.

* Supomos que você já configurou o SSL para SharePoint, pois exigimos o SSL na URL publicada. Será preciso ter o SSL habilitado no site interno, a fim de garantir que os links sejam enviados/mapeados corretamente. Caso não tenha configurado o SSL, confira [Configure SSL for SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) (Configurar SSL para o SharePoint 2013), que tem instruções para configurar o SSL. Além disso, verifique se o computador conector confia no certificado que você emite. (Não precisa ser um certificado emitido publicamente).

##<a name="steps-to-set-up-sharepoint"></a>Etapas para configurar o SharePoint

Siga estas etapas para habilitar o acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD:

**Parte 1: Configurar o SSO (logon único)**

  * Etapa A. Verificar se o servidor do SharePoint está em execução como uma conta de serviço.
  * Etapa B. Configurar o SharePoint para Kerberos.
  * Etapa C. Defina um SPN (nome da entidade de serviço) para a conta que é atribuída ao SharePoint.
  * Etapa D. Verificar se o conector está definido como um delegado confiável para o SharePoint.

**Parte 2: Habilitar acesso remoto seguro**

 * Publique o farm do SharePoint no Proxy de Aplicativo do Azure AD.

**Parte 3: Garantir que o SharePoint saiba sobre a URL externa**

 * Defina mapeamentos alternativos de acesso no SharePoint.

### <a name="part-1-set-up-single-sign-on-sso-to-sharepoint"></a>Parte 1: Configurar o SSO (logon único) para o SharePoint

Nossos clientes querem a melhor experiência de SSO para seus aplicativos de back-end, nesse caso, o SharePoint Server. Nesse cenário comum do Azure AD, o usuário se autenticará apenas uma vez, pois não será mais solicitado que ele faça isso novamente.

Para aplicativos locais que exigem ou usam a autenticação do Windows, a questão é resolvida usando o protocolo de autenticação Kerberos e um recurso chamado KCD (delegação restrita de Kerberos). A KCD, quando configurada, permite que o conector do proxy de aplicativo obtenha um tíquete/token do Windows para um determinado usuário, mesmo que ele não tenha se conectado ao Windows diretamente. Para saber mais sobre a KCD, confira [Visão geral da delegação restrita de Kerberos](https://technet.microsoft.com/en-us/library/jj553400.aspx).

Siga as etapas abaixo para configurá-la para um servidor do SharePoint.

**Etapa A: Verificar se o SharePoint está em execução em uma conta de serviço, não no sistema local, serviço local ou serviço de rede**

Antes de tudo, você precisa verificar se o SharePoint está em execução em uma conta de serviço definida. Isso é necessário para que possamos anexar SPNs (nomes da entidade de serviço) a uma conta válida. Os SPNs são a maneira como o protocolo Kerberos identifica serviços diferentes. E você precisará deles mais tarde para configurar a KCD.

Para garantir que seus sites estejam em execução em uma conta de serviço definida, faça o seguinte:

1. Abra o site **Administração Central do SharePoint 2013**.
2. Vá para **Segurança** e escolha **Configurar contas de serviço**.
3. Selecione **Pool de Aplicativos Web — SharePoint — 80**. As opções podem ser ligeiramente diferentes com base no nome do seu pool Web, ou caso ele use o SSL por padrão.

  ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-web-application.png)

4. Se **Selecionar uma conta para este componente** for **Serviço Local** ou **Serviço de Rede**, você precisará criar uma conta. Caso contrário, você já pode passar para a próxima etapa. 
5. Escolha **Registrar nova conta gerenciada**. Depois que a conta é criada, é preciso definir o **Pool de Aplicativos Web** para poder usar a conta.

> [!NOTE]
Você precisará ter uma conta do AD pré-criada para o serviço. Sugerimos que você permita uma alteração automática de senha. Para obter mais detalhes sobre o conjunto completo de etapas e a solução de problemas, confira [Configurar a alteração automática da senha no SharePoint 2013](https://technet.microsoft.com/EN-US/library/ff724280.aspx). 

**Etapa B: Configurar o SharePoint para Kerberos**

Usamos a KCD para executar o SSO (logon único) no servidor do SharePoint, e isso funciona somente com Kerberos. 

Para configurar o site do SharePoint para autenticação Kerberos:

1. Abra o site **Administração Central do SharePoint 2013**.
2. Vá para **Gerenciamento de Aplicativos**, escolha **Gerenciar aplicativos Web** e selecione seu site do SharePoint. Neste exemplo, é **SharePoint — 80**.

  ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-manage-web-applications.png)
  
3. Clique em **Provedores de Autenticação** na barra de ferramentas.
4. Na caixa **Provedores de Autenticação**, clique em **Zona Padrão** para exibir as configurações.
5. Na caixa **Editar Autenticação**, role para baixo até ver **Tipos de Autenticação por Declarações** e verifique se ambas as opções **Habilitar Autenticação do Windows** e **Autenticação Integrada do Windows** estão marcadas. 
6. Na caixa suspensa, verifique se **Negociar (Kerberos)** está selecionada.

  ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-edit-authentication.png)

7. Na parte inferior da caixa **Editar Autenticação**, clique em **Salvar**.

**Etapa C: Definir um SPN para a conta de serviço do SharePoint**

Antes de configurar a KCD, precisamos identificar o serviço do SharePoint em execução como a conta de serviço que você configurou acima. Fazemos isso definindo um SPN (nome da entidade de serviço). Para saber mais, confira [Service Principal Names](https://technet.microsoft.com/en-us/library/cc961723.aspx) (Nomes da entidade de serviço).

O formato SPN é:

```
<service class>/<host>:<port>
```

_service class_ é um nome exclusivo para o serviço. Para o SharePoint, usamos HTTP.

_host_ é o domínio totalmente qualificado ou o nome Netbios do host em que o serviço está em execução. No caso de um site do SharePoint, pode ser a URL do site, dependendo da versão do IIS que você está usando.

_port_ é opcional. Se o FQDN do servidor do SharePoint for:

```
sharepoint.demo.o365identity.us
```

O SPN será: 

```
HTTP/ sharepoint.demo.o365identity.us demo
```

Além disso, você talvez precise definir SPNs para sites específicos em seu servidor. Para obter mais detalhes, confira [Configurar a autenticação Kerberos](https://technet.microsoft.com/en-us/library/cc263449(v=office.12).aspx). Preste muita atenção à seção "Criar nomes de entidade de serviço para seus aplicativos Web usando a autenticação Kerberos". 

A maneira mais fácil de fazer isso é seguir os formatos de SPN que já podem estar presentes para seu site. Copie esses SPNs a serem registrados na conta de serviço. Para fazer isso:

1. Navegue até o site com o SPN de outro computador. 
 Quando você faz isso, o conjunto relevante de tíquetes Kerberos são armazenados em cache no computador. Esses tíquetes contêm o SPN do local de destino para o qual você navegou. Podemos extrair o SPN desse site usando uma ferramenta chamada [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html).
 
2. Em uma janela de comando em execução no mesmo contexto do usuário que acessou o site no navegador, execute o seguinte comando: 
```
Klist
```
3. Ele retornará o conjunto de SPNs do serviço de destino. Neste exemplo, o valor realçado é o SPN necessário:

  ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)
  
4. Agora que você tem o SPN, é preciso ter certeza de que ele está configurado corretamente na conta de serviço configurada para o Aplicativo Web anteriormente. Siga as etapas na próxima seção.

**Definir o SPN**

Para definir o SPN, execute o comando abaixo no prompt de comando como um Administrador do domínio.

```
setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
```

Esse comando define o SPN para a conta de serviço do SharePoint em execução como _demo\sp_svc_.

Lembre-se de substituir _http/sharepoint.demo.o365identity.us_ pelo SPN do seu servidor e _demo\sp_svc_ pela conta de serviço no seu ambiente. O comando setspn vai procurar o SPN antes de adicioná-lo. Nesse caso, você pode ver um erro **Valor de SPN duplicado**. Caso esse erro seja exibido, verifique se o valor está associado à conta de serviço.

Você pode verificar se o SPN foi adicionado executando o comando Setspn com a opção -I. Para saber mais sobre a ferramenta Setspn, confira [Setspn](https://technet.microsoft.com/en-us/library/cc731241.aspx).

**Etapa D: Garantir que os conectores sejam confiáveis para delegar ao SharePoint**

Nesta etapa, você vai configurar a KCD para que o serviço Proxy de Aplicativo do Azure AD seja capaz de delegar identidades do usuário ao serviço do SharePoint. Faça isso habilitando o conector Proxy de Aplicativo para poder recuperar tíquetes Kerberos para seus usuários que foram autenticados no Azure AD. Em seguida, esse servidor passará o contexto ao aplicativo de destino, ou o SharePoint, nesse caso. 

Para configurar a KCD, você precisará repetir as etapas a seguir para cada computador conector:

1. Faça logon como um Administrador de domínio em um DC e abra **Usuários e Computadores do Active Directory**.
2. Encontre o computador em que o conector está em sendo executado. Neste exemplo, ele é o mesmo servidor do SharePoint.
3. Clique duas vezes no computador e na guia **Delegação**.
4. Verifique se as configurações de delegações estão definidas como **Confiar neste computador para delegação somente para os serviços especificados** e selecione **Usar qualquer protocolo de autenticação**.

  ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-delegation-box.png)
  
5. Agora você precisa adicionar o SPN que criou anteriormente para a conta de serviço. Clique no botão **Adicionar**, em seguida, em **Usuários ou Computadores** e localize a conta que você criou anteriormente.

  ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-users-computers.png)

 Você deve ver uma lista de SPNs para escolher. É preciso adicionar aquele definido acima. 
6. Selecione esse item e clique em **OK**. Clique em **OK** novamente para salvar a alteração.

### <a name="part-2-enable-remote-access-to-sharepoint"></a>Parte 2: Habilitar acesso remoto para o SharePoint

Agora, com o SharePoint para Kerberos habilitado e a KCD configurada, você está pronto para configurar o SSO (logon único) para o SharePoint. No conector, você pode publicar o SharePoint para acesso remoto por meio do Proxy de Aplicativo do Azure AD.

**Publicar o SharePoint com o Proxy de Aplicativo do Azure AD**

Para executar as etapas abaixo, você precisa ser um membro da Função Administrador Global na conta do Azure Active Directory da sua organização.

1. Faça logon no portal de Gerenciamento do Azure https://manage.windowsazure.com e encontre seu Locatário do Azure AD.
2. Clique em **Aplicativos** e em **Adicionar**.
3. Selecione **Publicar um aplicativo que estará acessível fora de sua rede**. Caso não visualize essa opção, verifique se você configurou o Azure AD Básico ou Premium no locatário.
4. Na caixa resultante, preencha cada uma das opções como se segue: 
 * **Nome**: qualquer valor que você queira, por exemplo, _SharePoint_.
 * **URL interna**: essa é a URL do site do SharePoint internamente, como https://SharePoint/. Neste exemplo, certifique-se de usar https.
 * **Método de Pré-autenticação**: selecione _Azure Active Directory_.

  ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-add-application.png)

5. Depois que o aplicativo for publicado, clique na guia **Configurar**.
6. Role para baixo até a opção **Traduzir URL nos Cabeçalhos**. O valor padrão é _SIM_; altere-o para _NÃO_. 

 O SharePoint usa o valor _Cabeçalho de Host_ para pesquisar o site e também gera links com base nesse valor. Fazer isso garante que qualquer link que o SharePoint gere será uma URL publicada corretamente definida para usar a URL externa. Definir o valor para _SIM_ também permite que o conector encaminhe a solicitação ao aplicativo de back-end. No entanto, a definição como _NÃO_ significa que o conector não enviará o nome de host interno, e sim o cabeçalho de host como a URL publicada ao aplicativo de back-end.

 Além disso, para garantir que o SharePoint aceite essa URL, você precisa concluir mais uma configuração no servidor do SharePoint. Você fará isso na próxima seção.

7. Altere o **Método de Autenticação Interna** para _Integrado ao Windows_. Se seu locatário do Azure AD usar um UPN na nuvem diferente do local, lembre-se de atualizar também a **Identidade de Logon Delegada**.
8. Defina o **SPN do Aplicativo Interno** para o valor que definimos acima. Por exemplo, _http/sharepoint.demo.o365identity.us_.
9. Agora você pode atribuir o aplicativo aos usuários de destino.

Seu aplicativo deve ser semelhante ao seguinte:

  ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-internal-application-spn.png)

###<a name="part-3-ensure-sharepoint-knows-about-the-external-url"></a>Parte 3: Garantir que o SharePoint saiba sobre a URL externa

A última etapa é garantir que o SharePoint possa localizar o site com base na URL externa, assim, ele renderizará links baseados nessa URL externa. Faça isso configurando mapeamentos alternativos de acesso para o SharePoint.

**Configurar um nome alternativo para o site do SharePoint**

1. Abra o site **Administração Central do SharePoint 2013**.
2. Em **Configurações do Sistema**, selecione **Configurar Mapeamentos Alternativos de Acesso**. 

 Isso abre a caixa **Mapeamentos Alternativos de Acesso**.

  ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access1.png)

3. Na lista suspensa ao lado de **Coleções de Mapeamentos Alternativos de Acesso**, selecione **Alterar Coleção de Mapeamentos Alternativos de Acesso**.
4. Selecione o seu site, por exemplo **SharePoint — 80**.

  ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access2.png)

5. Você pode optar por adicionar a URL publicada como uma URL interna ou uma URL pública. Este exemplo usa uma **URL pública** como a extranet.
6. Clique em **Editar URLs Públicas** no caminho **Extranet** e insira o caminho em que publica o aplicativo, como na etapa anterior. Por exemplo, _https://sharepoint-iddemo.msappproxy.net_.

  ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access3.png)

7. Clique em **Salvar**. 

 Agora você pode acessar o site do SharePoint externamente por meio do Proxy de Aplicativo do Azure AD.

##<a name="next-steps"></a>Próximas etapas

[Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md)<br>
[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md)<br>
[Publicando o SharePoint 2016 e o Servidor do Office Online com o Proxy de Aplicativo do Azure AD](https://blogs.technet.microsoft.com/dawiese/2016/06/09/publishing-sharepoint-2016-and-office-online-server-with-azure-ad-application-proxy/)









<!--HONumber=Feb17_HO1-->


