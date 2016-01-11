<properties
   pageTitle="Noções básicas sobre o manifesto de aplicativo do Active Directory do Azure | Microsoft Azure"
   description="Cobertura detalhada de como usar o manifesto do aplicativo do Active Directory do Azure, que representa a configuração de identidade de um aplicativo em um locatário do AD do Azure, e é usado para facilitar a autorização do OAuth, experiência de consentimento e muito mais."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/18/2015"
   ms.author="dkershaw;bryanla"/>

# Noções básicas sobre o manifesto de aplicativo do Active Directory do Azure

Os aplicativos que se integram com o Active Directory do Azure (AD) devem ser registrados em um locatário do Azure AD, fornecendo uma configuração persistente de identidade para o aplicativo. Essa configuração é consultada em tempo de execução, permitindo cenários que permitem que um aplicativo terceirize e agencie autenticação/autorização por meio do Azure AD. Para saber mais sobre o modelo de aplicativo Azure AD, consulte o artigo [Adicionando, atualizando e removendo um aplicativo][ADD-UPD-RMV-APP].

## Atualizando a configuração de identidade do aplicativo

Na verdade, várias opções estão disponíveis para atualizar propriedades de configuração de identidade do aplicativo, que variam em recursos e graus de dificuldade, incluindo o seguinte:

- A**[ interface do usuário Web][AZURE-CLASSIC-PORTAL] do portal clássico do Azure** permite que você atualize as propriedades mais comuns de um aplicativo. Essa é a maneira mais rápida e menos propensa a erro de atualizar as propriedades do aplicativo, mas não lhe dá acesso total a todas as propriedades como os dois métodos a seguir.
- Para cenários mais avançados em que é necessário atualizar as propriedades que não são expostas no portal clássico do Azure, você pode modificar o **manifesto do aplicativo**. Esse é o foco deste artigo e será discutido mais detalhadamente, começando na próxima seção.
- Também é possível **escrever um aplicativo que usa a [Graph API][GRAPH-API]** para atualizar seu aplicativo, o que requer maior esforço. Isso pode ser uma opção atraente se você estiver gravando o software de gerenciamento ou precisar atualizar as propriedades do aplicativo regularmente de forma automática.

## Usando o manifesto do aplicativo para atualizar a configuração de identidade do aplicativo
Através do [portal clássico do Azure][AZURE-CLASSIC-PORTAL], você pode gerenciar a configuração de identidade do aplicativo baixando e carregando uma representação de arquivo JSON, que é chamada de manifesto de aplicativo. Nenhum arquivo real é armazenado no diretório; o manifesto do aplicativo é meramente uma operação HTTP GET na entidade de aplicativo do Azure AD Graph API, e o carregamento é uma operação HTTP PATCH na entidade de aplicativo.

Como resultado, para entender o formato e as propriedades do manifesto do aplicativo, você precisará consultar a documentação de Graph API [Entidade de aplicativo][APPLICATION-ENTITY]. Exemplos de atualizações que podem ser executadas através do carregamento do manifesto do aplicativo incluem:

- Declarar escopos de permissão (oauth2Permissions) expostos pela sua API Web. Consulte o tópico "Expondo APIs Web para outros aplicativos" em [Integrando aplicativos ao Active Directory do Azure][INTEGRATING-APPLICATIONS-AAD] para obter informações sobre como implementar a representação de usuário usando o escopo de permissão delegada oauth2Permissions. Conforme mencionado anteriormente, todas as propriedades da entidade de aplicativo estão documentadas no artigo de referência do Graph API [Referência de Entidade e Tipo Complexo][APPLICATION-ENTITY], incluindo a propriedade oauth2Permissions, que é uma coleção do tipo [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION]
- Declarar funções de aplicativo (appRoles) expostas pelo seu aplicativo. A propriedade appRole da entidade de aplicativo é uma coleção do tipo [AppRole][APPLICATION-ENTITY-APP-ROLE]. Consulte o artigo [Controle de acesso com base em função nos aplicativos em nuvem usando o Azure AD][RBAC-CLOUD-APPS-AZUREAD] para obter um exemplo de implementação.
- Declarar aplicativos cliente conhecidos (knownClientApplications), que permitem vincular de modo lógico o consentimento dos aplicativos cliente especificados à API de recurso/Web.
- Solicitar ao AD do Azure que emita uma declaração de associações de grupo para o usuário conectado (groupMembershipClaims). Observação: isso pode ser configurado para emitir adicionalmente declarações sobre as associações de funções de diretório do usuário. Consulte o artigo [Autorização em aplicativos na nuvem usando grupos do AD][AAD-GROUPS-FOR-AUTHORIZATION] para obter um exemplo de implementação.
- Permitir que o aplicativo ofereça suporte aos fluxos de concessão implícitos do OAuth 2.0 (oauth2AllowImplicitFlow). Esse tipo de fluxo de concessão é usado com páginas da Web JavaScript ou SPA (Aplicativos de Única Página) inseridos.
- Habilitar o uso dos certificados X509 como chave secreta (keyCredentials). Consulte os artigos [Compilar aplicativos de serviço e daemon no Office 365][O365-SERVICE-DAEMON-APPS] e [Guia do desenvolvedor para autenticação com a API do Gerenciador de Recursos do Azure ][DEV-GUIDE-TO-AUTH-WITH-ARM] para ver exemplos de implementação.

O manifesto do aplicativo também fornece uma boa maneira de controlar o estado de seu registro de aplicativo. Porque ele está disponível no formato JSON, a representação de arquivo pode ser inserida no seu controle de origem, junto com o código-fonte do aplicativo.

## Exemplo passo a passo
Agora, vamos percorrer as etapas necessárias para atualizar a configuração de identidade do aplicativo por meio do manifesto do aplicativo. Destacaremos um dos exemplos fornecidos acima, mostrando como declarar um novo escopo de permissão em um aplicativo de recurso:

1. Navegue até o [portal clássico do Azure][AZURE-CLASSIC-PORTAL] e entre com uma conta que tenha privilégios de administrador ou coadministrador de serviços.

2. Depois de autenticado, role para baixo e selecione a extensão do "Active Directory" do Azure no painel de navegação à esquerda (1) e clique no locatário do Azure AD onde seu aplicativo está registrado (2).

    ![Selecionar locatário do Azure AD][SELECT-AZURE-AD-TENANT]

3. Quando a página de diretório aparecer, clique em "Aplicativos" (1) na parte superior da página para ver uma lista de aplicativos registrados no locatário. Localize o aplicativo que você deseja atualizar na lista e clique nele (2).

    ![Selecionar locatário do Azure AD][SELECT-AZURE-AD-APP]

4. Agora que você selecionou a página principal do aplicativo, observe o recurso "Gerenciar Manifesto" na parte inferior da página (1). Se você clicar nesse link, será solicitado a baixar ou carregar o arquivo de manifesto JSON. Clique em "Baixar Manifesto" (2), que será seguido imediatamente pela caixa de diálogo de confirmação de download solicitando que você confirme clicando em "Baixar Manifesto" (3). Em seguida, abra ou salve o arquivo localmente (4).

    ![Gerenciar o manifesto, opção de download][MANAGE-MANIFEST-DOWNLOAD]

    ![Baixar o manifesto][DOWNLOAD-MANIFEST]

5. Neste exemplo, salvamos o arquivo localmente, o que nos permite abrir em um editor, fazer alterações em JSON e salvar novamente. Aqui está a aparência da estrutura JSON dentro do editor de JSON do Visual Studio. Observe que ela segue o esquema da [Entidade de aplicativo][APPLICATION-ENTITY] como mencionamos anteriormente:

    ![Atualizar o JSON do manifesto][UPDATE-MANIFEST]

    Por exemplo, supondo que queremos implementar/expor uma nova permissão chamada "Employees.Read.All" em nosso aplicativo de recurso (API). Para isso, basta adicionar um elemento novo/secundário à coleção oauth2Permissions, isto é:

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    A entrada deve ser exclusiva e, portanto, você deve gerar um novo GUID (identificador global exclusivo) para a propriedade `"id"`. Nesse caso, como especificamos `"type": "User"`, essa permissão pode ser consentida por qualquer conta autenticada pelo locatário do AD do Azure no qual o aplicativo de recurso/API está registrado, concedendo ao aplicativo cliente permissão para acessá-lo em nome da conta. As cadeias de caracteres de descrição e nome de exibição são usadas durante o consentimento e para exibição no portal clássico do Azure.

6. Quando você terminar de atualizar o manifesto, volte para a página de aplicativo do Azure AD no portal clássico do Azure, clique no recurso "Gerenciar manifesto" mais uma vez (1), mas dessa vez selecione a opção "Carregar Manifesto" (2). Semelhante ao download, você será saudado novamente com uma segunda caixa de diálogo solicitando o local do arquivo JSON. Clique em "Procurar arquivo..." (3), use a caixa de diálogo "Escolher arquivo para carregar" para selecionar o arquivo JSON (4) e pressione "Abrir". Depois que a caixa de diálogo desaparecer, marque a caixa de seleção "OK" (5) e seu manifesto será carregado.

    ![Gerenciar o manifesto, opção de carregamento][MANAGE-MANIFEST-UPLOAD]

    ![Carregar o JSON do manifesto][UPLOAD-MANIFEST]

    ![Carregar o JSON do manifesto - confirmação][UPLOAD-MANIFEST-CONFIRM]

Agora que o manifesto foi salvo, você pode fornecer a um aplicativo cliente registrado acesso à nova permissão que adicionamos acima, mas dessa vez, pode usar a interface de usuário da Web no portal clássico do Azure em vez de editar o manifesto do aplicativo cliente:

1. Primeiro, acesse a página "Configurar" do aplicativo cliente ao qual deseja adicionar acesso à nova API e clique no botão "Adicionar aplicativo".
2. Em seguida, você verá uma lista de aplicativos de recurso registrados (APIs) no locatário. Clique no sinal de mais (+) ao lado do nome do aplicativo de recurso para selecioná-lo.  
3. Em seguida, clique na marca de seleção no canto inferior direito. 
4. Ao retornar para a seção "Adicionar Aplicativo" da página de configuração do cliente, você verá o novo aplicativo de recurso na lista. Ao passar o mouse sobre a seção "Permissões Delegadas" à direita da linha, uma lista suspensa será exibida. Clique na lista e selecione a nova permissão para adicioná-la à lista de permissões solicitadas do cliente. Observação: essa nova permissão será armazenado na configuração de identidade do aplicativo cliente, na propriedade da coleção "requiredResourceAccess".

![Permissões para outros aplicativos][PERMS-TO-OTHER-APPS]

![Permissões para outros aplicativos][PERMS-SELECT-APP]

![Permissões para outros aplicativos][PERMS-SELECT-PERMS]

É isso. Agora seus aplicativos serão executados usando a nova configuração de identidade.

## Próximas etapas
Use a seção de comentários do DISQUS abaixo para fornecer seus comentários e ajudar a refinar e a moldar o nosso conteúdo.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=AcomDC_1223_2015-->