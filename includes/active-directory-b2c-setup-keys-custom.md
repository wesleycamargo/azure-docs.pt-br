> [!NOTE]
> Estamos planejando melhorar essa experiência e substituir as etapas a seguir.

### <a name="create-an-administrator-credential-in-the-azure-ad-b2c-tenant"></a>Criar uma credencial de administrador no locatário do Azure AD B2C.

Para a próxima seção, você precisará usar uma credencial que usa o domínio do seu locatário Azure AD B2C. Para fazer isso, você precisa criar uma conta de administrador com essas credenciais. Para fazer isso:

1. No [portal do Azure](https://portal.azure.com), alterne para o contexto do locatário do Azure AD B2C e abra a folha do Azure AD B2C. [Mostre-me como.](..\articles\active-directory-b2c\active-directory-b2c-navigate-to-b2c-context.md)
1. Selecione **Usuários e grupos**.
1. Selecione **Todos os usuários**.
1. Clique em **+ Novo Usuário**.
    * Defina o **Nome** = `Admin`.
    * Defina o **Nome de usuário** = `admin@{tenantName}.onmicrosoft.com` onde `{tenantName}` é o nome do seu locatário do Azure AD B2C.
1. Em **Função do diretório**, escolha **Administrador global** e clique em **Ok**.
1. Clique em **Criar** para criar o usuário administrador.
1. Marque **Mostrar senha** e copie a senha.

### <a name="set-up-the-key-container"></a>Configure o contêiner de chave

O contêiner de chave é usado para armazenar as chaves. Para configurar um:

1. Abra um prompt de comando do PowerShell.  Um método é **tecla de logotipo do Windows + R**, digitar `powershell` e pressione Enter.
1. Baixe essa reprodução para obter a ferramenta de ExploreAdmin do Powershell.

    ```powershell
    git clone https://github.com/Azure-Samples/active-directory-b2c-advanced-policies
    ```

1. Alterne para a pasta com a ferramenta ExploreAdmin.

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. Importe a ferramenta ExploreAdmin para o PowerShell.

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. Confirmar `b2c_1a_TokenSigningKeyContainer` ainda não existe.  Substitua `{tenantName}` pelo nome do seu locatário.

    ```powershell
    Get-CpimKeyContainer -TenantId {tenantName}.onmicrosoft.com -StorageReferenceId b2c_1a_TokenSigningKeyContainer -ForceAuthenticationPrompt
    ```

    a. Quando o prompt de logon for exibido, use a conta do administrador que você criou na seção anterior.

    b. Quando solicitado, forneça seu número de telefone para configurar a autenticação multifator.

    c. Quando solicitado, insira a senha.

    d. **Um erro é esperado!**  O erro deve indicar que `b2c_1a_TokenSigningKeyContainer` não pode ser encontrado.  Se não houver nenhum erro porque você já concluiu essas etapas, ignore o restante desta seção.

1. Crie `b2c_1a_TokenSigningKeyContainer`.  Substitua `{tenantName}` pelo nome do seu locatário.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_TokenSigningKeyContainer  b2c_1a_TokenSigningKeyContainer rsa 2048 0 0
    ```

1. Crie `b2c_1a_TokenEncryptionKeyContainer`.  Substitua `{tenantName}` pelo nome do seu locatário.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com b2c_1a_TokenEncryptionKeyContainer b2c_1a_TokenEncryptionKeyContainer rsa 2048 0 0
    ```

1. Crie `b2c_1a_FacebookSecret`.  Substitua `{tenantName}` pelo nome do seu locatário.

    ```powershell
    New-CpimKeyContainer {tenantName}.onmicrosoft.com  b2c_1a_FacebookSecret  b2c_1a_FacebookSecret rsa 2048 0 0
    ```
