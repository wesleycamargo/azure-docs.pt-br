---
title: Gerenciamento e personalização dos Serviços de Federação do Active Directory (AD FS) com o Azure AD Connect | Microsoft Docs
description: Gerenciamento do AD FS com o Azure AD Connect e personalização da experiência de entrada do AD FS do usuário com o Azure AD e o PowerShell.
keywords: AD FS,ADFS,gerenciamento do AD FS, AAD Connect, Conectar, entrada, personalização do AD FS, reparar confiança, O365, federação, terceira parte confiável
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy

---
# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Gerenciamento e personalização dos Serviços de Federação do Active Directory (AD FS) com o Azure AD Connect
Este artigo fornece detalhes sobre tarefas relacionadas ao AD FS (Serviços de Federação do Active Directory) que podem ser executadas usando o Microsoft Azure Active Directory Connect, além de outras tarefas comuns do AD FS que podem ser necessárias para uma configuração completa de um farm do AD FS.

| Tópico | O que ele abrange |
|:--- |:--- |
| **Gerenciamento dos AD FS** | |
| [Reparar a relação de confiança](#repairthetrust) |Reparar a confiança de federação com o Office 365 |
| [Adicionar um servidor do AD FS](#addadfsserver) |Expandir o farm do AD FS com um servidor do AD FS adicional |
| [Adicionar um servidor proxy de aplicativo Web do AD FS](#addwapserver) |Expandir o farm do AD FS com um servidor WAP adicional |
| [Adicionar um domínio federado](#addfeddomain) |Adicionar um domínio federado |
| **Personalização do AD FS** | |
| [Adicionar uma ilustração ou um logotipo da empresa personalizado](#customlogo) |Personalizar uma página de entrada do AD FS com um logotipo e uma ilustração da empresa |
| [Adicionar uma descrição de entrada](#addsignindescription) |Adicionar uma descrição de página de entrada |
| [Modificar regras de declaração do AD FS](#modclaims) |Modificar declarações do AD FS para vários cenários de federação |

## <a name="ad-fs-management"></a>Gerenciamento dos AD FS
O Azure AD Connect fornece várias tarefas relacionadas ao AD FS que podem ser executadas usando o assistente do Azure AD Connect com mínima intervenção do usuário. Após instalar o Azure AD Connect executando o assistente, você pode executar o assistente novamente para executar tarefas adicionais.

### <a name="repair-the-trust-a-namerepairthetrusta"></a>Reparar a relação de confiança <a name=repairthetrust></a>
O Azure AD Connect pode verificar a integridade atual da confiança do AD FS e Azure Active Directory e tomar as medidas apropriadas para reparar a relação de confiança. Siga estas etapas para reparar a confiança do Azure AD e AD FS.

1. Selecione **Reparar a relação de confiança do AAD e do ADFS** na lista de tarefas adicionais.
   ![Reparar a relação de confiança do AAD e do ADFS](media\\active-directory-aadconnect-federation-management\\RepairADTrust1.PNG)
2. Na página **Conectar ao Azure AD**, forneça suas credenciais de administrador global do Azure AD e clique em **Avançar**.
   ![Conecte-se ao AD do Azure](media\\active-directory-aadconnect-federation-management\\RepairADTrust2.PNG)
3. Na página **Credenciais de acesso remoto** , digite as credenciais de administrador de domínio.
   ![Credenciais de acesso remoto](media\\active-directory-aadconnect-federation-management\\RepairADTrust3.PNG)
   
    Depois que você clicar em **Avançar**, o Azure AD Connect verificará a integridade do certificado e mostrará os problemas.
   
    ![Estado de certificados](media\\active-directory-aadconnect-federation-management\\RepairADTrust4.PNG)
   
    A página **Pronto para configurar** mostrará a lista de ações que serão executadas para reparar a confiança.
   
    ![Pronto para configurar](media\\active-directory-aadconnect-federation-management\\RepairADTrust5.PNG)
4. Clique em **Instalar** para reparar a relação de confiança.

> [!NOTE]
> O Azure AD Connect só pode reparar ou agir em relação a certificados autoassinados. O Azure AD Connect não pode reparar certificados de terceiros.
> 
> 

### <a name="add-an-ad-fs-server-a-nameaddadfsservera"></a>Adicionar um servidor do AD FS <a name=addadfsserver></a>
> [!NOTE]
> O Azure AD Connect requer o arquivo de certificado PFX para adicionar um servidor do AD FS. Portanto, você só poderá executar essa operação se tiver configurado o farm do AD FS usando o Azure AD Connect.
> 
> 

1. Selecione **Implantar um Servidor de Federação adicional** e clique em **Avançar**.
   ![Servidor de federação adicional](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer1.PNG)
2. Na página **Conectar ao Azure AD**, digite suas credenciais de administrador global do Azure AD e clique em **Avançar**. 
   ![Conecte-se ao AD do Azure](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer2.PNG)
3. Forneça as credenciais de administrador de domínio.
   ![Credenciais de administrador de domínio](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer3.PNG)
4. O Azure AD Connect solicitará a senha do arquivo PFX que você forneceu ao configurar o novo farm do AD FS com o Azure AD Connect. Clique em **Digitar Senha** para fornecer a senha para o arquivo PFX.
   ![Senha de certificado](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer4.PNG)
   
    ![Especificar certificado SSL](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer5.PNG)
5. Na página **Servidores do AD FS** , insira o nome do servidor ou o endereço IP a ser adicionado ao farm do AD FS.
   ![AD FS servers](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer6.PNG)
6. Clique em **Avançar** e passe pela página **Configurar** final. Após o Azure AD Connect terminar de adicionar os servidores no farm do AD FS, você terá a opção de verificar a conectividade.
   ![Pronto para configurar](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer7.PNG)
   
    ![Instalação concluída](media\\active-directory-aadconnect-federation-management\\AddNewADFSServer8.PNG)

### <a name="add-an-ad-fs-web-application-proxy-server-a-nameaddwapservera"></a>Adicionar um servidor proxy de aplicativo Web do AD FS <a name=addwapserver></a>
> [!NOTE]
> O Azure AD Connect requer o arquivo de certificado PFX para adicionar um servidor proxy de aplicativo Web. Portanto, você poderá executar esta operação somente se tiver configurado o farm do AD FS usando o Azure AD Connect.
> 
> 

1. Selecione **Implantar Proxy de Aplicativo Web** na lista de tarefas disponíveis.
   ![Deploy web application proxy](media\\active-directory-aadconnect-federation-management\\WapServer1.PNG)
2. Forneça as credenciais de administrador global do Azure.
   ![Conecte-se ao Azure AD](media\\active-directory-aadconnect-federation-management\\wapserver2.PNG)
3. Na página **Especificar certificado SSL** , forneça a senha para o arquivo PFX que você forneceu ao configurar o farm do AD FS com o Azure AD Connect.
   ![Senha de certificado](media\\active-directory-aadconnect-federation-management\\WapServer3.PNG)
   
    ![Especificar certificado SSL](media\\active-directory-aadconnect-federation-management\\WapServer4.PNG)
4. Adicione o servidor a ser adicionado como um proxy de aplicativo Web. Como o servidor proxy de aplicativo Web pode não ter ingressado no domínio, o assistente solicitará as credenciais administrativas para o servidor que está sendo adicionado.
   ![Credenciais administrativas de servidor](media\\active-directory-aadconnect-federation-management\\WapServer5.PNG)
5. Na página **Credenciais de confiança de proxy** , forneça as credenciais administrativas para configurar a confiança de proxy e acessar o servidor primário no farm do AD FS.
   ![Credenciais de confiança de proxy](media\\active-directory-aadconnect-federation-management\\WapServer6.PNG)
6. Na página **Pronto para configurar** , o assistente mostra a lista de ações que serão executadas.
   ![Pronto para configurar](media\\active-directory-aadconnect-federation-management\\WapServer7.PNG)
7. Clique em **Instalar** para concluir a configuração. Depois que a configuração for concluída, o assistente lhe dará a opção para verificar a conectividade com os servidores. Clique em **Verificar** para verificar a conectividade.
   ![Instalação concluída](media\\active-directory-aadconnect-federation-management\\WapServer8.PNG)

### <a name="add-a-federated-domain-a-nameaddfeddomaina"></a>Adicionar um domínio federado <a name=addfeddomain></a>
É fácil adicionar um domínio a ser federado com o Azure AD usando o Azure AD Connect. O Azure AD Connect adiciona o domínio para federação e modifica as regras de declaração para refletir corretamente o emissor quando você tem vários domínios federados com o Azure AD.

1. Para adicionar um domínio federado, selecione a tarefa **Adicionar um domínio do Azure AD adicional**.
   ![Domínio do Azure AD adicional](media\\active-directory-aadconnect-federation-management\\AdditionalDomain1.PNG)
2. Na próxima página do assistente, forneça as credenciais de administrador global do Azure AD.
   ![Conecte-se ao Azure AD](media\\active-directory-aadconnect-federation-management\\AdditionalDomain2.PNG)
3. Na página **Credenciais de acesso remoto** , forneça as credenciais do administrador de domínio.
   ![Credenciais de acesso remoto](media\\active-directory-aadconnect-federation-management\\additionaldomain3.PNG)
4. Na próxima página, o assistente fornecerá uma lista de domínios do Azure AD com os quais você pode federar seu diretório local. Escolha o domínio na lista.
   ![Domínio do Azure AD](media\\active-directory-aadconnect-federation-management\\AdditionalDomain4.PNG)
   
    Depois de escolher o domínio, o assistente fornecerá as informações apropriadas sobre outras ações que o assistente realizará e o impacto da configuração. Em alguns casos, se você selecionar um domínio que ainda não seja verificado no Azure AD, o assistente fornecerá informações para ajudá-lo a verificar o domínio. Confira [Adicionar seu nome de domínio personalizado ao Azure Active Directory](active-directory-add-domain.md) para obter mais detalhes.
5. Clique em **Avançar** e a página **Pronto para configurar** mostrará a lista de ações que o Azure AD Connect executará. Clique em **Instalar** para concluir a configuração.
   ![Pronto para configurar](media\\active-directory-aadconnect-federation-management\\AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>Personalização do AD FS
As seções a seguir fornecem detalhes sobre algumas das tarefas comuns que talvez você precise executar ao personalizar a página de entrada do AD FS.

### <a name="add-a-custom-company-logo-or-illustration-a-namecustomlogoa"></a>Adicionar uma ilustração ou um logotipo da empresa personalizado <a name=customlogo></a>
Para alterar o logotipo da empresa que é exibido na página de **Entrada** , use o seguinte cmdlet e sintaxe do Windows PowerShell.

> [!NOTE]
> As dimensões recomendadas para o logotipo são 260x35 @ 96 dpi com um tamanho do arquivo de no máximo 10 KB.
> 
> 

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> O parâmetro *TargetName* é obrigatório. O tema padrão liberado com o AD FS é denominado Default.
> 
> 

### <a name="add-a-signin-description-a-nameaddsignindescriptiona"></a>Adicionar uma descrição de entrada <a name=addsignindescription></a>
Para adicionar uma descrição à página de **Entrada**, use o seguinte cmdlet e sintaxe do Windows PowerShell.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### <a name="modify-ad-fs-claim-rules-a-namemodclaimsa"></a>Modificar regras de declaração do AD FS <a name=modclaims></a>
O AD FS dá suporte a uma linguagem de declarações avançada que você pode usar para criar regras de declaração personalizada. Para obter mais informações, confira [A função da linguagem de regras de declaração](https://technet.microsoft.com/library/dd807118.aspx).

As seções a seguir descrevem como você pode escrever regras personalizadas para alguns cenários pertencentes à federação do Azure AD e AD FS.

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Condicional de ID imutável em um valor presente no atributo
O Azure AD Connect permite que você especifique um atributo a ser usado como âncora de origem quando objetos são sincronizados ao Azure AD. Se o valor do atributo personalizado não estiver vazio, convém emitir uma declaração de ID imutável. Por exemplo, você pode selecionar **ms-ds-consistencyguid** como o atributo de âncora de origem e emitir **ImmutableID** como **ms-ds-consistencyguid**, caso o atributo tenha um valor em relação a ele. Se não houver um valor com o atributo, execute **objectGuid** como a ID imutável.  Você pode construir o conjunto de regras de declaração personalizada, conforme descrito na seção a seguir.

**Regra 1: consultar atributos**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Nessa regra, você simplesmente consulta os valores de **ms-ds-consistencyguid** e **objectGuid** do usuário do Active Directory. Altere o nome do repositório para um nome de armazenamento apropriado em sua implantação do AD FS. Também altere o tipo de declarações para um tipo apropriado para a federação, conforme definido para **objectGuid** e **ms-ds-consistencyguid**.

Além disso, usando **add** e não **issue**, você evita adicionar uma emissão de saída à entidade e pode usar os valores como valores intermediários. Você emitirá a declaração em uma regra mais recente depois de estabelecer o valor a ser usado como a ID imutável.

**Regra 2: verificar se ms-ds-consistencyguid existe para o usuário**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Essa regra define um sinalizador temporário chamado **idflag** que é definido como **useguid** se não há nenhum **ms-ds-concistencyguid** populado para o usuário. A lógica por trás disso é o fato de que o AD FS não permite declarações vazias. Assim, quando você adiciona as declarações http://contoso.com/ws/2016/02/identity/claims/objectguid e http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid à Regra 1, você só acabará com uma declaração **msdsconsistencyguid** se o valor for preenchido para o usuário. Se não estiver populado, o AD FS verá que ele terá um valor vazio e o descartará imediatamente. Todos os objetos terão **objectGuid**. Portanto, essa declaração sempre estará presente após a Regra 1 ser executada.

**Regra 3: emitir ms-ds-consistencyguid como a ID imutável, se estiver presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Essa é uma verificação **Exist** implícita. Se o valor para a declaração existir, emita-o como a ID imutável. O exemplo anterior usa a declaração **nameidentifier** . Você precisará alterar isso para o tipo de declaração adequado para a ID imutável no seu ambiente.

**Regra 4: emitir o objectGuid como a ID imutável se ms-ds-consistencyGuid não estiver presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Nessa regra, você simplesmente verifica o sinalizador temporário **idflag**. Você decide se deve emitir a declaração com base em seu valor.

> [!NOTE]
> A sequência dessas regras é importante.
> 
> 

#### <a name="sso-with-a-subdomain-upn"></a>SSO com um subdomínio UPN
Você pode adicionar mais de um domínio a ser federado usando o Azure AD Connect, conforme descrito em [Adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain). Você deve modificar a declaração de UPN para que a ID do emissor corresponda ao domínio raiz e não ao subdomínio, pois o domínio raiz federado também abrange o filho.

Por padrão, a regra da declaração da ID do emissor é definida como:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Declaração de ID do emissor padrão](media\\active-directory-aadconnect-federation-management\\issuer_id_default.png)

A regra padrão simplesmente usa o sufixo do UPN na declaração da ID do emissor. Por exemplo, John é um usuário em sub.contoso.com e contoso.com é federado com o Azure AD. Carlos insere john@sub.contoso.com como nome de usuário ao entrar no Azure AD, e a regra de declaração de ID de emissor padrão no AD FS lida com isso da maneira a seguir.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Valor de declaração:**  http://sub.contoso.com/adfs/services/trust/

Para ter apenas o domínio raiz no valor de declaração do emissor, altere a regra de declaração para corresponder ao seguinte.

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre as [opções de entrada do usuário](active-directory-aadconnect-user-signin.md).

<!--HONumber=Oct16_HO2-->


