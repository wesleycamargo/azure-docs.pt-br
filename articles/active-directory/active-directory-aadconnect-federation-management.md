<properties
	pageTitle="Gerenciamento e personalização dos Serviços de Federação do Active Directory (AD FS) com o Azure AD Connect | Microsoft Azure"
	description="Gerenciamento do AD FS usando o Azure AD Connect e personalização da experiência de entrada do AD FS do usuário usando o Azure AD e o PowerShell."
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="anandy"/>

# Gerenciamento e personalização dos Serviços de Federação do Active Directory (AD FS) com o Azure AD Connect

Este artigo detalha as várias tarefas relacionadas aos Serviços de Federação do Active Directory (AD FS) que podem ser realizadas usando o Azure AD Connect e outras tarefas comuns dos AD FS que podem ser necessárias para a configuração completa de um farm dos AD FS.

## Gerenciamento dos AD FS

O Azure AD Connect fornece várias tarefas relacionadas ao AD FS que podem ser executadas usando o assistente do Azure AD Connect com mínima intervenção do usuário. Após instalar o Azure AD Connect executando o assistente, você pode executar o assistente novamente para executar tarefas adicionais.

### Reparando a confiança

O Azure AD Connect pode verificar a integridade atual da confiança do AD FS e Azure AD e tomar as medidas apropriadas para reparar a relação de confiança. Siga as etapas a seguir para reparar a confiança do Azure AD e AD FS.

Selecione **Reparar Confiança do AAD e ADFS** na lista de tarefas disponíveis.

![](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

Na página **Conectar ao Azure AD**, forneça suas credenciais de administrador global do Azure AD e clique em Avançar.

![](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

Na página **Credenciais de acesso remoto**, forneça as credenciais de administrador de domínio

![](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

Quando você clicar em Avançar, o Azure AD Connect verificará a integridade do certificado e mostrará os problemas se eles existirem.

![](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

A página **Pronto para configurar** mostrará a lista de ações que serão executadas para reparar a confiança.

![](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

Clique em instalar para continuar e reparar a confiança.

>[AZURE.NOTE] O Azure AD Connect pode reparar/realizar uma ação apenas nos certificados que são autoassinados. Os certificados de terceiros não podem ser reparados pelo Azure AD Connect.

### Adicionando um novo servidor do AD FS

> [AZURE.NOTE] O Azure AD Connect requer o arquivo de certificado PFX para adicionar um servidor do AD FS. Portanto, você poderá executar esta operação somente se tiver configurado o farm do AD FS usando o Azure AD Connect.

Selecione **Implantar um Servidor de Federação adicional** e clique em Avançar.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

Na página **Conectar ao Azure AD**, forneça suas credenciais de administrador global do Azure AD e clique em Avançar.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

Forneça as credenciais de administrador de domínio na próxima página.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

Na próxima página, o Azure Connect AD solicitará a senha do arquivo pfx que você forneceu ao configurar seu novo farm do AD FS com o Azure AD Connect. Clique em Digitar Senha para fornecer a senha para o arquivo PFX.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

Na próxima página, forneça o endereço IP ou o nome dos servidores adicionais a serem adicionados ao farm do AD FS.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

Clique em Avançar e passe pela página Configurar final. Após o Azure AD Connect terminar de adicionar os servidores no farm do AD FS, você terá a opção de verificar a conectividade.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Adicionando um novo servidor proxy de aplicativo Web dos AD FS

> [AZURE.NOTE] O Azure AD Connect requer o arquivo de certificado PFX para adicionar um servidor proxy de aplicativo Web. Portanto, você poderá executar esta operação somente se tiver configurado o farm do AD FS usando o Azure AD Connect.

Selecione **Implantar Proxy de Aplicativo Web** na lista de tarefas disponíveis.

![](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

Na próxima página, forneça as credenciais de administrador global do Azure.

![](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

Em seguida, você verá a página **Especificar certificado SSL**, em que precisa fornecer a senha para o arquivo PFX que você forneceu ao configurar o farm do AD FS com o Azure AD Connect.

![](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

![](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

Na próxima página, adicione o servidor a ser adicionado como proxy de aplicativo Web. Como o servidor proxy de aplicativo Web pode ou não estar ou não ingressado no domínio, o assistente solicitará as credenciais administrativas para o servidor que está sendo adicionado.

![](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

Na página **Credenciais de confiança de proxy**, forneça as credenciais administrativas para configurar a confiança de proxy e acessar o servidor primário no farm do AD FS.

![](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

Na página **Pronto para configurar**, o assistente mostra a lista de ações que serão executadas.

![](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

Clique em Instalar para concluir a configuração. Depois que a configuração for concluída, o assistente lhe dará a opção para verificar a conectividade com os servidores. Clique em Verificar para verificar a conectividade.

![](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Adicionar um novo domínio federado

É fácil adicionar um novo domínio a ser federado com o Azure AD usando o Azure AD Connect. O Azure AD Connect não apenas adiciona o novo domínio de federação, mas modifica as regras de declaração para refletir corretamente o emissor quando você tem vários domínios federados com o Azure AD.

Para adicionar um novo domínio federado, selecione a tarefa **Adicionar um domínio do Azure AD adicional**.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

Na próxima página do assistente, forneça as credenciais de administrador global do Azure AD.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

Nas credenciais de acesso remoto, forneça as credenciais do administrador de domínio.

![](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

Na próxima página, o assistente fornecerá uma lista de domínios do Azure AD com o qual você deseja federar seu diretório local. Escolha o domínio na lista.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

Depois de escolher o domínio, o assistente fornecerá as informações apropriadas sobre outras ações que o assistente realizará e o impacto da configuração. Em alguns casos, se você selecionar um domínio que ainda não seja verificado no Azure AD, o assistente fornecerá informações para ajudá-lo a verificar o domínio. Consulte [Adicionar seu nome de domínio personalizado ao Azure Active Directory](active-directory-add-domain.md) para obter mais detalhes sobre como verificar seu domínio.

Clique em Avançar e a página **Pronto para configurar** mostrará a lista de ações que o Azure AD Connect executará. Clique em Instalar para concluir a configuração.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## Personalização do AD FS

As seções a seguir fornecem detalhes sobre como você pode executar algumas tarefas comuns que talvez você precise realizar para personalizar sua página de entrada do AD FS.

### Adicionar a ilustração ou logotipo da empresa personalizado

Para alterar o logotipo da empresa que é exibido na página de entrada, use o seguinte cmdlet e sintaxe do Windows PowerShell.

> [AZURE.NOTE] As dimensões recomendadas para o logotipo são 260x35 a 96 dpi com um tamanho do arquivo de no máximo 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] O parâmetro TargetName é obrigatório. O tema padrão liberado com o AD FS é denominado default.
 

### Adicionar a descrição de entrada

Para adicionar uma descrição à página de entrada, use o seguinte cmdlet e sintaxe do Windows PowerShell.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Modificando as regras de declaração do AD FS

O AD FS fornece a opção para especificar regras personalizadas para emitir declarações. Ele dá suporte a uma linguagem de declaração avançada usando o que você pode usar para criar regras de declaração personalizadas. Para obter mais informações, você pode consultar o artigo [aqui](https://technet.microsoft.com/library/dd807118.aspx).

As seções a seguir detalham como você pode escrever regras personalizadas para alguns cenários pertencentes à federação do Azure AD e AD FS.

#### Condicional de ID imutável no valor presente no atributo

O Azure AD Connect permite que você especifique um atributo a ser usado como âncora de origem quando objetos serão sincronizados ao Azure AD. É possível que você deseje emitir uma declaração de ID imutável dependendo da condição caso o valor no atributo personalizado não esteja vazio. Por exemplo, considere que você selecionou ms-ds-consistencyguid como o atributo de âncora de origem e deseja emitir ImmutableID como ms-ds-consistencyguid caso o atributo tenha um valor em relação a ela, caso contrário, emita objectGuid como a ID imutável. Você pode construir o conjunto de regras de declaração personalizada como descrito abaixo:

**Regra 1 (consultar atributos)**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Nesta regra, você está simplesmente consultando os valores de ms-ds-consistencyguid e objectguid do usuário do Active Directory. Altere o nome do repositório para o nome de repositório apropriado conforme disponível em sua implantação do ADFS e o tipo de declarações para o tipo de declarações adequado que você tem em sua federação definida para objectGUID e ms-ds-consistencyguid. Eu defini os tipos de declaração personalizada no meu ambiente de teste.

Além disso, usando ‘add’ e não ‘issue’ você evita adicionar uma emissão de saída à entidade e apenas usa os valores como valores intermediários. Você emitirá a declaração na regra posterior ao estabelecer qual valor usar como ID imutável.

**Regra 2: (verificar se ms-ds-consistencyguid existe para o usuário)**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Essa regra simplesmente define um sinalizador temporário "idflag", que será definido como "useguid" se não houver nenhum ms-ds-consistencyguid populado para o usuário. A lógica por trás disso é o fato de que o ADFS não permite declarações vazias. Portanto, ao adicionar as declarações http://contoso.com/ws/2016/02/identity/claims/objectguid e http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid na regra 1, você acabará com uma declaração msdsconsistencyguid SOMENTE se o valor estiver preenchido para o usuário. Caso não esteja preenchido, o ADFS vê que ela aparecerá como um valor vazio e a descarta. O ObjectGuid, como você sabe, todos os objetos terão, portanto essa declaração sempre estará lá após a regra 1 ser executada.

**Regra 3: emitir ms-ds-consistencyguid como a ID imutável, se estiver presente**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Essa é uma verificação EXIST implícita. Se o valor para a declaração existir, emita-o como a ID imutável. Observe que eu estou emitindo a declaração do nameidentifier. Você precisará alterar isso com o tipo de declaração adequado para a ID imutável no seu ambiente.

**Regra 4: emitir o GUID do objeto como a ID imutável se ms-ds-consistencyGuid não estiver presente**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Nesta regra, você está simplesmente verificando o sinalizador temporário ‘idflag’ e, com base no valor, decidindo emitir ou não a declaração.

> [AZURE.NOTE] A sequência dessas regras é importante.

#### SSO com um UPN de subdomínio

Você pode adicionar mais de um domínio para ser federado usando o Azure AD Connect ([Adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain)). A declaração de UPN precisará ser modificada para que a ID do emissor corresponda ao domínio raiz e não ao subdomínio, porque o domínio raiz federado cobre o filho também.

Por padrão, a regra da declaração da ID do emissor é definida como:

	c:[Type 
	== “http://schemas.xmlsoap.org/claims/UPN“]

	=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Declaração de ID do emissor padrão](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

A regra padrão simplesmente usa o sufixo do UPN na declaração da ID do emissor. Por exemplo, John é um usuário em sub.contoso.com e contoso.com é federado com o Azure AD. John insere john@sub.contoso.com como nome de usuário ao entrar no Azure AD, e a regra padrão de declaração da ID do emissor nos AD FS lidará com isso da seguinte maneira:

c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Valor da declaração:** http://sub.contoso.com/adfs/services/trust/

Para ter somente o domínio raiz no valor da declaração do emissor, altere a regra de declaração para:

	c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

	=> issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## Próximas etapas

Saiba mais sobre as [opções de entrada do usuário](active-directory-aadconnect-user-signin.md)

<!---HONumber=AcomDC_0511_2016-->