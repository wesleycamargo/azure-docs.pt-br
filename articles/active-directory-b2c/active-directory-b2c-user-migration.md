---
title: "Azure Active Directory B2C: abordagens de migração do usuário"
description: "Discuta conceitos centrais e avançados sobre a migração do usuário usando a API do Graph e, opcionalmente, políticas personalizadas do Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/04/2017
ms.author: yoelh
ms.openlocfilehash: f98f1826b492b8596f352b403b3b12775814c399
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2017
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: migração do usuário
Quando você planeja migrar seu provedor de identidade para o Azure Active Directory B2C (Azure AD B2C), também é necessário migrar a conta de usuário. Este artigo explica como migrar as contas de usuário existentes de qualquer provedor de identidade para o Azure AD B2C. Este artigo não se destina a ser prescritivo, mas a descrever dois entre vários métodos. O desenvolvedor é responsável por escolher o mais adequado.

## <a name="user-migration-flows"></a>Fluxos de migração do usuário
Com o Azure AD B2C, você pode migrar usuários por meio da [API do Graph](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). O processo de migração do usuário se enquadra em dois fluxos:

* **Pré-migração**: este fluxo se aplica quando você precisar limpar o acesso às credenciais do usuário (nome de usuário e senha) ou quando as credenciais forem criptografadas, mas você pode descriptografá-las. O processo de pré-migração envolve a leitura dos usuários do provedor de identidade antigo e a criação de novas contas no diretório do Azure AD B2C.

* **Pré-migração e redefinição de senha**: esse fluxo se aplica quando a senha do usuário não está acessível. Por exemplo:
    * A senha é armazenada no formato HASH.
    * A senha é armazenada em um provedor de identidade que você não pode acessar. Seu provedor de identidade antigo valida a credencial do usuário chamando um serviço Web.

Em ambos os fluxos, primeiro, você executa o processo de pré-migração, lê os usuários do seu provedor de identidade antigo e cria novas contas no diretório do Azure AD B2C. Se você não tiver a senha, crie a conta usando uma senha gerada aleatoriamente. Em seguida, peça ao usuário para alterar a senha ou, quando o usuário fizer logon pela primeira vez, o Azure AD B2C pedirá ao usuário para redefini-la.

## <a name="password-policy"></a>Política de senha
A política de senha do Azure AD B2C (para contas locais) se baseia na política do Azure AD. As políticas de inscrição ou entrada e redefinição de senha do Azure AD B2C usam o nível de segurança de senha "forte" e não expiram as senhas. Para obter mais informações, consulte [Política de senha do Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx).

Se as contas que você deseja migrar usarem um nível de segurança de senha mais fraco do que o [nível de segurança de senha forte imposto pelo Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), você poderá desabilitar o requisito de senha forte. Para alterar a política de senha padrão, defina a propriedade `passwordPolicies` como `DisableStrongPassword`. Por exemplo, você pode modificar a solicitação de criação de usuário da seguinte maneira: 

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-graph-api-to-migrate-users"></a>Etapa 1: usar a API do Graph para migrar usuários
Você cria a conta de usuário do Azure AD B2C por meio da API do Graph (com a senha ou com uma senha aleatória). Esta seção descreve o processo de como criar contas de usuário no diretório do Azure AD B2C usando a API do Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Etapa 1.1: registrar seu aplicativo em seu locatário
Para se comunicar com a API do Graph, primeiro, você precisa ter uma conta de serviço com privilégios administrativos. No Azure AD, você registra um aplicativo e a autenticação para o Azure AD. As credenciais do aplicativo são **ID do Aplicativo** e **Segredo do Aplicativo**. O aplicativo atua em nome próprio e não como usuário, para chamar a API do Graph.

Primeiro, registre seu aplicativo de migração no Azure AD. Em seguida, crie uma chave de aplicativo (segredo do aplicativo) e defina o aplicativo com os privilégios de gravação.

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Escolha seu locatário do Azure AD **B2C** selecionando sua conta no canto superior direito da janela.

3. No painel esquerdo, selecione **Azure Active Directory** (não Azure AD B2C). Talvez seja necessário selecionar **Mais Serviços** para localizá-lo.

4. Selecione **Registros do Aplicativo**.

5. Selecione **Novo registro de aplicativo**.

    ![Novo registro de aplicativo](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)

6. Crie um novo aplicativo fazendo o seguinte:
    * Para **Nome**, use **B2CUserMigration** ou qualquer outro nome que desejar.
    * Para o **Tipo de aplicativo**, use **Aplicativo Web/API**.
    * Para **URL de logon**, use **https://localhost** (já que não é relevante para esse aplicativo).
    * Selecione **Criar**.

7. Depois que o aplicativo tiver sido criado, na lista de **Aplicativos**, selecione o aplicativo recém-criado **B2CUserMigration**.

8. Selecione **Propriedades**, copie a **ID do aplicativo** e salve-a para mais tarde.

### <a name="step-12-create-the-application-secret"></a>Etapa 1.2: criar o segredo de aplicativo
1. Na janela **Aplicativo Registrado** do portal do Azure, selecione **Chaves**.

2. Adicione uma nova chave (também conhecida como um segredo do cliente) e, em seguida, copie a chave para uso posterior.

    ![ID e chaves do aplicativo](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>Etapa 1.3: conceder permissão administrativa ao seu aplicativo
1. Na janela **Aplicativo Registrado** do portal do Azure, selecione **Permissões necessárias**.

2. Selecione **Microsoft Azure Active Directory**.

3. No painel **Habilitar Acesso**, em **Permissões de Aplicativo**, selecione **Ler e gravar dados do diretório** e, em seguida, selecione **Salvar**.

4. No painel **Permissões necessárias**, selecione **Conceder Permissões**.

    ![Permissões de aplicativo](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Agora, você tem um aplicativo com permissões para criar, ler e atualizar usuários de seu locatário do Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Etapa 1.4: (Opcional) limpeza de ambiente
A permissão de ler e gravar dados de diretório *não* inclui o direito de excluir usuários. Para permitir que seu aplicativo exclua os usuários (para limpar o ambiente), você deve executar uma etapa extra, que envolve a execução do PowerShell para definir permissões de Administrador da Conta de Usuário. Caso contrário, você pode pular para a próxima seção.

> [!IMPORTANT]
> Você precisa usar uma conta de administrador de locatário B2C que seja *local* para o locatário B2C. A sintaxe de nome da conta é *admin@contosob2c.onmicrosoft.com*.

>[!NOTE]
> O script do PowerShell a seguir requer o [Azure Active Directory PowerShell Versão 2](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).

No seguinte script do PowerShell, faça o seguinte:
1. Conecte-se ao seu serviço online. Para fazer isso, execute o cmdlet `Connect-AzureAD` no prompt de comando do Windows PowerShell e forneça suas credenciais. 

2. Use a **ID do Aplicativo** para atribuir o aplicativo à função de administrador da conta de usuário. Essas funções têm identificadores bem conhecidos, então, tudo o que você precisa fazer é inserir sua **ID de Aplicativo** no script.

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Altere o valor `$AppId` pela **ID do Aplicativo** do Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Etapa 2: Exemplo de aplicativo pré-migração
[Baixe e execute o código de amostra](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). Você pode baixá-lo como um arquivo .zip.

### <a name="step-21-edit-the-migration-data-file"></a>Etapa 2.1: editar o arquivo de dados de migração
O aplicativo de exemplo usa um arquivo JSON que contém dados de usuários fictícios. Após executar com êxito a amostra, você pode alterar o código para consumir os dados de seu próprio banco de dados. Ou você pode exportar o perfil do usuário para um arquivo JSON e, em seguida, definir o aplicativo para usar esse arquivo.

Para editar o arquivo JSON, abra a solução do Visual Studio `AADB2C.UserMigration.sln`. No projeto `AADB2C.UserMigration`, abra o arquivo `UsersData.json`.

![Arquivo de dados do usuário](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Como você pode ver, o arquivo contém uma lista de entidades do usuário. Cada entidade de usuário tem as seguintes propriedades:
* email
* displayName
* firstName
* lastName
* senha (pode estar vazia)

> [!NOTE]
> No tempo de compilação, o Visual Studio copia o arquivo para o diretório `bin`.

### <a name="step-22-configure-the-application-settings"></a>Etapa 2.2: definir as configurações do aplicativo
No projeto `AADB2C.UserMigration`, abra o arquivo *App.config*. Substitua as três configurações do aplicativo a seguir pelos seus próprios valores:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
    
</appSettings>
```

> [!NOTE]
> * O uso de uma cadeia de conexão de tabela do Azure é descrito posteriormente nas próximas seções.
> * O nome do locatário do B2C é o domínio que você inseriu durante a criação do locatário e é exibido no portal do Azure. O nome do locatário geralmente termina com o sufixo *.onmicrosoft.com* (por exemplo, *contosob2c.onmicrosoft.com*).
>

### <a name="step-23-run-the-pre-migration-process"></a>Etapa 2.3: executar o processo pré-migração
Clique com o botão direito do mouse na solução `AADB2C.UserMigration` e, em seguida, recompile o exemplo. Se tiver êxito, agora deverá você ter um arquivo executável `UserMigration.exe` localizado em `AADB2C.UserMigration\bin\Debug`. Para executar o processo de migração, use um dos seguintes parâmetros de linha de comando:

* Para **migrar usuários com senha**, use o comando `UserMigration.exe 1`.

* Para **migrar usuários com senha aleatória**, use o comando `UserMigration.exe 2`. Essa operação também cria uma entidade de tabela do Azure. Mais tarde, você configura a política para chamar o serviço de API REST. O serviço usa uma tabela do Azure para controlar e gerenciar o processo de migração.

![Demonstração do processo de migração](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Etapa 2.4: verificar o processo pré-migração
Para validar a migração, use um dos dois métodos a seguir:

* Para pesquisar um usuário por nome de exibição, use o portal do Azure:
    
    a. Abra o **Azure AD B2C** e selecione **Usuários e Grupos**.

    b. Na caixa de pesquisa, digite o nome de exibição do usuário e, em seguida, visualize o perfil do usuário. 

* Para recuperar um usuário por endereço de email de entrada, use esse aplicativo de exemplo:

    a. Execute o comando a seguir:

    ```Console
        UserMigration.exe 3 {email address}
    ```
        
    > [!TIP]
    > Você também pode salvar a saída em um arquivo JSON usando o seguinte comando:
    >
    >```Console
    >    UserMigration.exe 3 {email address} >> UserProfile.json
    >```

    > [!TIP]
    > Você também pode recuperar um usuário por nome de exibição usando o comando a seguir: `UserMigration.exe 4 <Display name>`.

    b. Abra o arquivo *UserProfile.json* em um editor JSON. Com o Visual Studio Code, você pode formatar um documento JSON selecionando Shift+Alt+F ou selecionando **Formatar Documento** no menu de contexto.

    ![O arquivo UserProfile.json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Etapa 2.5: (Opcional) limpeza de ambiente
Se deseja limpar seu locatário do Azure AD e remover os usuários do diretório do Azure AD, execute o comando `UserMigration.exe 5`.

> [!NOTE]
> * Para limpar o seu locatário, configure as permissões de Administrador da Conta de Usuário para seu aplicativo.
> * O aplicativo de migração de exemplo limpa todos os usuários listados no arquivo JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Etapa 2.6: entrar com usuários migrados (com senha)
Depois de executar o processo de pré-migração com as senhas dos usuários, as contas estão prontas para uso e os usuários podem entrar no seu aplicativo usando o Azure AD B2C. Se você não tiver acesso às senhas dos usuários, continue para a próxima seção.

## <a name="step-3-help-users-reset-their-password"></a>Etapa 3: ajudar os usuários a redefinir sua senha
Se você migrar usuários com uma senha aleatória, eles precisarão redefinir a senha. Para ajudá-los a redefinir a senha, envie um email de boas-vindo com um link para redefinir a senha.

Para obter o link para sua política de redefinição de senha, faça o seguinte:

1. Selecione **Configurações do Azure AD B2C** e, em seguida, selecione as propriedades da política **Redefinir senha**.

2. Selecione seu aplicativo.

    >[!NOTE]
    >Executar Agora exige que pelo menos um aplicativo esteja previamente registrado no locatário. Para saber como registrar aplicativos, confira os artigos [Introdução](active-directory-b2c-get-started.md) ou [Registro do aplicativo](active-directory-b2c-app-registration.md) do Azure AD B2C. 

3. Selecione **Executar agora** e, em seguida, verifique a política.

4. Na caixa **Executar o ponto de extremidade agora**, copie a URL e, em seguida, envie-a para seus usuários.

    ![Definir logs de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Etapa 4: [Opcional] alterar sua política para verificar e definir o status de migração do usuário

> [!NOTE]
> Para verificar e alterar o status de migração do usuário, você deve usar uma política personalizada. Para obter mais informações, consulte [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).
>

Quando os usuários tentarem entrar sem redefinir a senha primeiro, sua política deve retornar uma mensagem de erro amigável. Por exemplo: 
>*Sua senha expirou. Para redefini-la, selecione o link Redefinir Senha.* 

Esta etapa opcional requer o uso d e políticas personalizadas do Azure AD B2C conforme descrito no artigo [Introdução a políticas personalizadas](active-directory-b2c-get-started-custom.md).

Nesta seção, você deve alterar a política para verificar o status de migração de usuário na entrada. Se o usuário não alterou a senha, retorne uma mensagem de erro HTTP 409 pedindo para o usuário selecionar o link **Esqueceu sua senha?**. 

Para acompanhar a alteração da senha, você deve usar a tabela do Azure. Quando você executa o processo de pré-migração com parâmetro de linha de comando `2`, cria a entidade de usuário em uma tabela do Azure. O serviço faz o seguinte:

* Ao entrar, a política do Azure AD B2C invoca o serviço RESTful de migração, enviando uma mensagem de email como uma declaração de entrada. O serviço pesquisa o endereço de email na tabela do Azure. Se o endereço existir, o serviço gerará uma mensagem de erro: *Você deve alterar a senha*.

* Após o usuário alterar a senha com êxito, remova a entidade da tabela do Azure.

>[!NOTE]
>Usamos uma tabela do Azure para simplificar o exemplo. Você pode armazenar o status de migração em qualquer banco de dados ou como uma propriedade personalizada na conta do Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1: atualizar a configuração de aplicativo
1. Para testar a demonstração da API RESTful, abra a solução do Visual Studio `AADB2C.UserMigration.sln` no Visual Studio. 

2. No projeto `AADB2C.UserMigration.API`, abra o arquivo *App.config*. Substitua a configuração do aplicativo por seu próprio valor:

    ```XML
    <appSettings>
        <add key="BlobStorageConnectionString" value="{The Azure Blob storage connection string"} />
    </appSettings>
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Etapa 4.2: implantar o aplicativo Web no Serviço de Aplicativo do Azure
Publique seu aplicativo de API no Serviço de Aplicativo do Azure. Para obter mais informações, consulte: [Implantar seu aplicativo no Serviço de Aplicativo do Azure](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy).

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Etapa 4.3: adicionar perfil técnico e validação de perfil técnico à sua política 
1. No diretório de trabalho, abra o arquivo de política de extensão *TrustFrameworkExtensions.xml*. 

2. Pesquise pelo nó `<ClaimsProviders>` e, no nó, adicione o seguinte trecho de código XML. Certifique-se de alterar o valor de `ServiceUrl` para apontar para a URL do ponto de extremidade. 

    ```XML
    <ClaimsProvider>
        <DisplayName>REST APIs</DisplayName>
        <TechnicalProfiles>
    
        <TechnicalProfile Id="LocalAccountSignIn">
            <DisplayName>Local account just in time migration</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
    
        <TechnicalProfile Id="LocalAccountPasswordReset">
            <DisplayName>Local account just in time migration</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    
    <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
    
        <!-- This technical profile uses a validation technical profile to authenticate the user. -->
        <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="LocalAccountSignIn" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    
        <TechnicalProfile Id="LocalAccountWritePasswordUsingObjectId">
            <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="LocalAccountPasswordReset" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

O perfil técnico precedente define uma declaração de entrada: `signInName` (enviar como email). Ao entrar, a declaração é enviada para seu ponto de extremidade RESTful.

Depois de definir o perfil técnico para a sua API RESTful, diga para a sua política do Azure AD B2C chamar esse perfil técnico. O trecho de código XML substitui `SelfAsserted-LocalAccountSignin-Email`, que é definido na política de base. O trecho de código XML também adiciona `ValidationTechnicalProfile` com ReferenceId apontando para o seu perfil técnico `LocalAccountUserMigration`. 

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Etapa 4.4: carregar a política em seu locatário
1. No [Portal do Azure](https://portal.azure.com), mude para o [contexto do locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e, em seguida, selecione **Azure AD B2C**.

2. Selecione **Estrutura de Experiência de Identidade**.

3. Selecione **Todas as Políticas**.

4. Selecione **Carregar Política**.

5. Marque a caixa de seleção **Substituir a política caso ela exista**.

6. Carregue o arquivo *TrustFrameworkExtensions.xml* e certifique-se de que a validação dele seja aprovada.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Etapa 4.5: testar a política personalizada usando Executar Agora
1. Selecione **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.

2. Abra **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) carregada e selecione **Executar agora**.

3. Tente entrar com uma das credenciais de usuários migrados e clique em **Entrar**. A API REST deve emitir a seguinte mensagem de erro:

    ![Definir logs de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Etapa 4.6: (Opcional) solução de problemas na sua API REST
Você pode monitorar e exibir informações de log quase em tempo real.

1. No menu de configurações do seu aplicativo RESTful, em **Monitoramento**, selecione **Logs de diagnóstico**. 

2. Defina **Habilitar o log de aplicativo (sistema de arquivos)** como **Ativada**. 

3. Defina o **Nível** como **Detalhado**.

4. Selecione **Salvar**

    ![Definir logs de diagnóstico](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. No menu **Configurações**, selecione **Fluxo de Log**.

6. Verifique a saída da API RESTful.

Para obter mais informações, confira [Logs de streaming e o Console](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-streaming-logs-and-console).

> [!IMPORTANT]
> Use os logs de diagnóstico somente durante o desenvolvimento e teste. A saída API RESTful pode conter informações confidenciais que não devem ser expostas na produção.
>

## <a name="optional-download-the-complete-policy-files"></a>(Opcional) Baixar os arquivos da política completa
Depois de concluir o passo a passo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md), recomendamos que você crie o cenário usando seus próprios arquivos de política personalizados. Fornecemos os [Arquivos de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration) como referência. 
