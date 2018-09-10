---
title: Tutorial para configurar um aplicativo Web do Azure para ler um segredo do Cofre de Chaves | Microsoft Docs
description: 'Tutorial: Configurar um aplicativo ASP.NET Core para ler um segredo do Cofre de Chaves'
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 91e2047998d6e743691821c631e15c94cd63cf15
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41917887"
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Tutorial: Configurar um aplicativo Web do Azure para ler um segredo do Cofre de Chaves

Neste tutorial, você examinará as etapas necessárias para obter um aplicativo Web do Azure para ler informações do Cofre de Chaves, usando as identidades de serviço gerenciadas. Você aprenderá como:

> [!div class="checklist"]
> * Criar um Cofre de Chaves.
> * Armazenar um segredo no Cofre de Chaves.
> * Criar um aplicativo Web do Azure.
> * Habilitar identidades de serviço gerenciadas
> * Conceda as permissões necessárias para o aplicativo ler dados do Cofre de Chaves.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

Para fazer logon no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "ContosoResourceGroup" --location "East US"
```

O grupo de recursos que você acabou de criar é usado ao longo deste tutorial.

## <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure

Em seguida, você cria um Cofre de Chaves no grupo de recursos criado na etapa anterior. Embora “ContosoKeyVault” seja utilizado como o nome do Key Vault ao longo deste tutorial, você deverá usar um nome exclusivo. Forneça as seguintes informações:

* Nome do cofre **ContosoKeyVault**.
* Nome do grupo de recursos **ContosoResourceGroup**.
* O local **Leste dos EUA**.

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East US"
```

A saída deste comando mostra as propriedades do Cofre de Chaves criado recentemente. Anote as duas propriedades listadas abaixo:

* **Nome do Cofre**: no exemplo, isso é **ContosoKeyVault**. Você usará o nome do seu Cofre de Chaves para todos os comandos do Cofre de Chaves.
* **URI do Cofre**: Neste exemplo, é https://<YourKeyVaultName>.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

>[!IMPORTANT]
> Se você receber o erro, o Parâmetro 'vault_name' deverá estar de acordo com o seguinte padrão: '^[a-zA-Z0-9-]{3,24}$'. O valor de parâmetro -name não é exclusivo ou não está em conformidade com uma cadeia de caracteres composta de caracteres alfanuméricos de 24 a 24.

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-key-vault"></a>Adicione um segredo ao cofre de chaves

Estamos adicionando um segredo para ajudar a ilustrar como isso funciona. Você pode estar armazenando uma cadeia de conexão SQL ou qualquer outra informação que precise manter com segurança, mas disponibilizar para o aplicativo. Neste tutorial, a senha será chamada **AppSecret** e armazenará o valor de **MySecret** nela.

Digite os comandos abaixo para criar um segredo no Cofre de Chaves chamado **AppSecret** que armazenará o valor **MySecret**:

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "AppSecret" --value "MySecret"
```

Para exibir o valor contido no segredo como texto sem formatação:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "ContosoKeyVault"
```

Este comando mostra as informações secretas, incluindo o URI. Após concluir essas etapas, você deverá ter um URI para um segredo em um Azure Key Vault. Anote essa informação. Ela será necessária em uma etapa posterior.

## <a name="create-a-web-app"></a>Criar um aplicativo Web

Nesta seção, você cria um aplicativo ASP.NET MVC e o implanta no Azure como um aplicativo Web. Para obter mais informações sobre aplicativos Web do Azure, consulte [Visão geral de aplicativos Web](../app-service/app-service-web-overview.md).

1. No Visual Studio, crie um projeto selecionando **Arquivo > Novo > Projeto**. 

2. Na caixa de diálogo **Novo Projeto**, selecione **Visual C# > Web > Aplicativo Web ASP.NET Core**.

3. Nomeie o aplicativo **WebKeyVault** e, em seguida, selecione **OK**.
   >[!IMPORTANT]
   > Você deve nomear o aplicativo WebKeyVault para que o código copiado e colado corresponda ao namespace. Se você nomeou o site de uma forma diferente, precisará modificar o código para corresponder ao nome do site.

    ![Caixa de diálogo Novo Projeto ASP .NET](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Você pode implantar qualquer tipo de aplicativo Web ASP.NET Core no Azure. Para este tutorial, selecione o modelo **Aplicativo Web** e certifique-se de que a autenticação está definida como **Sem Autenticação**.

    ![ASPNET sem caixa de diálogo de autenticação](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Selecione **OK**.

6. Após a criação do projeto do ASP.NET Core, a página de boas-vindas do ASP.NET Core será exibida, fornecendo vários links para recursos que ajudarão você a começar.

7. No menu, selecione **Depurar > Iniciar sem depuração** para executar o aplicativo Web localmente.

## <a name="modify-the-web-app"></a>Modifique o aplicativo Web

Há dois pacotes do NuGet que o aplicativo Web precisa ter instalado. Para instalá-los, siga as etapas abaixo:

1. No Gerenciador de Soluções, clique com o botão direito do mouse no nome do site.
2. Selecione **Gerenciar pacotes do NuGet para solução...**
3. Marque a caixa de seleção próxima à caixa de pesquisa. **Incluir pré-lançamento**
4. Procure os dois pacotes do NuGet listados abaixo e aceite que eles sejam adicionados à solução:

    * [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) - facilita a busca de tokens de acesso para cenários de autenticação Serviço para Serviço do Azure. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) - contém métodos para interagir com o Cofre de Chaves.

5. Use o Gerenciador de Soluções para abrir `Program.cs` e substituir o conteúdo do arquivo Program.cs pelo código a seguir. Substitua ```<YourKeyVaultName>``` pelo nome do seu cofre de chaves:

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
    namespace WebKeyVault
    {
       public class Program
       {
           public static void Main(string[] args)
           {
               BuildWebHost(args).Run();
           }

           public static IWebHost BuildWebHost(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>()
             .Build();

           private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
         }
    }
    ```

6. Use o Gerenciador de Soluções para navegar até a seção **Páginas** e abra `About.cshtml`. Substitua o conteúdo de **About.cshtml.cs** pelo código abaixo:

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. No menu principal, escolha **Depurar** > **Iniciar sem Depuração**. Quando o navegador aparecer, navegue até a página **Sobre**. O valor para o AppSecret é exibido.

>[!IMPORTANT]
> Se você receber um erro HTTP 502.5 - mensagem de Falha do Processo
> > então verifique se o nome do Key Vault especificado no `Program.cs`

## <a name="publish-the-web-application-to-azure"></a>Publicar o aplicativo Web no Azure

1. Acima do editor, selecione **WebKeyVault**.
2. Selecione **Publicar**, em seguida, **Iniciar**.
3. Crie um novo **Serviço de Aplicativo**, selecione **Publicar**.
4. Selecione **Criar**.

>[!IMPORTANT]
> Uma janela do navegador será aberta e você verá um 502.5 - Mensagem de falha de processo. Isso é esperado. Você precisará conceder os direitos de identidade do aplicativo para ler os segredos do Cofre de Chaves.

## <a name="enable-managed-service-identity"></a>Habilitar Identidade de Serviço Gerenciada

O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar para o Key Vault para recuperá-los. A Identidade de Serviço Gerenciado (MSI) torna a solução desse problema mais simples, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

1. Retornar para a CLI do Azure
2. Execute o comando assign-identity para criar a identidade para esse aplicativo:

```azurecli
az webapp identity assign --name "WebKeyVault" --resource-group "ContosoResourcegroup"
```

>[!NOTE]
>Esse comando é o equivalente de ir para o portal e alternar **Identidade de serviço gerenciada** para **On** nas propriedades do aplicativo Web.

## <a name="grant-rights-to-the-application-identity"></a>Conceder direitos à identidade do aplicativo

Usando o Portal do Azure, vá para as políticas de acesso do Cofre de Chaves e conceda a você mesmo o acesso de Gerenciamento de Segredos ao Cofre de Chaves. Isso permitirá que você execute o aplicativo no computador de desenvolvimento local.

1. Pesquise o Cofre de Chaves na caixa de diálogo **Pesquisar Recursos** no Portal do Azure.
2. Selecione **Políticas de acesso**.
3. Selecione **Adicionar Novo**, na seção **Permissões do segredo**, selecione **Obter** e **Listar**.
4. Selecione **Selecionar entidade de segurança** e adicione a identidade do aplicativo. O nome será o mesmo do aplicativo.
5. Escolha **Ok**.

Agora, a conta no Azure e a identidade do aplicativo têm direitos para ler informações do Cofre de Chaves. Ao atualizar a página, deverá ver a página de aterrissagem do site. Se selecionar **Sobre**, você verá o valor que foi armazenado no Key Vault.

## <a name="clean-up-resources"></a>Limpar recursos

Para excluir um grupo de recursos e todos os seus recursos, use o comando **az group delete**.

  ```azurecli
  az group delete -n "ContosoResourceGroup"
  ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Guia do Desenvolvedor do Azure Key Vault](key-vault-developers-guide.md)