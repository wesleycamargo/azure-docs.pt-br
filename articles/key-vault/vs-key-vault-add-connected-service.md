---
title: Adicionar suporte do Key Vault ao seu projeto do ASP.NET usando o Visual Studio | Microsoft Docs
description: Use este tutorial para aprender a adicionar suporte do Key Vault para um aplicativo Web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 9f2adfcbf2d6ca5de79cc787029f5139138b0e52
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230430"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Adicione o Key Vault ao seu aplicativo Web usando os Serviços Conectados do Visual Studio

Neste tutorial, você aprenderá como adicionar facilmente tudo o que você precisa para começar a usar o Azure Key Vault para gerenciar seus segredos para projetos Web no Visual Studio, quer esteja usando o ASP.NET Core ou qualquer outro tipo de projeto do ASP.NET. Com o uso do recurso de Serviços Conectados no Visual Studio 2017, você pode fazer com que o Visual Studio adicione automaticamente todos os pacotes e definições de configuração do NuGet que você precisa para se conectar ao Key Vault no Azure. 

Para obter detalhes sobre as alterações que os Serviços Conectados realizam em seu projeto para habilitar o Key Vault, confira [Serviço Conectado do Key Vault — O que aconteceu com meu projeto do ASP.NET 4.7.1](vs-key-vault-aspnet-what-happened.md) ou [Serviço Conectado do Key Vault — O que aconteceu com meu projeto do ASP.NET Core](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Pré-requisitos

- **Uma assinatura do Azure**. Se você não tiver uma, poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 versão 15.7** com a carga de trabalho **Desenvolvimento Web** instalada. [Baixe agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Para ASP.NET (não Core), você precisa ter as Ferramentas de Desenvolvimento do .NET Framework 4.7.1, que não são instaladas por padrão. Para instalá-las, inicie o Instalador do Visual Studio, escolha **Modificar** e, em seguida, selecione **Componentes Individuais**. Em seguida, do lado direito, expanda **Desenvolvimento Web e do ASP.NET** e selecione **Ferramentas de Desenvolvimento do .NET Framework 4.7.1**.
- Um projeto Web do ASP.NET 4.7.1 ou do ASP.NET Core 2.0. é aberto.

## <a name="add-key-vault-support-to-your-project"></a>Como adicionar suporte do Key Vault ao seu projeto

1. No **Gerenciador de Soluções**, selecione **Adicionar** > **Serviço Conectado**.
   A página do Serviço Conectado aparece com os serviços que você pode adicionar ao seu projeto.
1. No menu de serviços disponíveis, selecione **Proteger Segredos com o Azure Key Vault**.

   ![Selecione "Proteger Segredos com o Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Se você tiver entrado no Visual Studio e tiver uma assinatura do Azure associada à sua conta, será exibida uma página com uma lista suspensa com suas assinaturas. Certifique-se de que está conectado ao Visual Studio e que a conta conectada é a mesma que você utiliza para a assinatura do Azure.

1. Selecione a assinatura que você deseja usar e, em seguida, escolha um Key Vault novo ou existente, ou selecione o link de edição para modificar o nome gerado automaticamente.

   ![Selecione sua assinatura](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Digite o nome que você deseja usar para o Key Vault.

   ![Renomeie o Key Vault e selecione um grupo de recursos](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Selecione um Grupo de Recursos existente ou crie um novo com um nome exclusivo gerado automaticamente.  Se você quiser criar um novo grupo com um nome diferente, use o [Portal do Azure](https://portal.azure.com) e, em seguida, feche a página e reinicie para recarregar a lista de grupos de recursos.
1. Selecione a região na qual um Key Vault será criado. Se seu aplicativo Web estiver hospedado no Azure, escolha a região que hospeda o aplicativo Web para um desempenho ideal.
1. Selecione um modelo de preços. Para obter mais detalhes, confira [Preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Selecione Ok para aceitar as opções de configuração.
1. Escolha **Adicionar** para criar o Key Vault. O processo de criação pode falhar se você escolher um nome que já foi usado.  Se isso acontecer, use o link **Editar** para renomear o Key Vault e tente novamente.

   ![Adicionando o serviço conectado ao projeto](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Agora, adicione um segredo ao seu Key Vault no Azure. Para chegar ao local correto no portal, clique no link para Gerenciar segredos armazenados neste Key Vault. Se você fechou a página ou o projeto, poderá navegar no [portal do Azure](https://portal.azure.com) escolhendo **Todos os serviços**. Em **Segurança**, escolha **Key Vault** e, em seguida, selecione o Key Vault que você acabou de criar.

   ![Navegar para o portal](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Na seção do Key Vault para o cofre de chaves que você criou, selecione **Segredos** e, em seguida, **Gerar/importar**.

   ![Gerar/Importar um segredo](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Digite um segredo, como MySecret, e dê a ele qualquer valor de cadeia de caracteres como um teste e clique no botão **Criar**.

   ![Criar um segredo](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (opcional) Insira outro segredo, mas, desta vez, coloque-o em uma categoria nomeando-o como "Segredos--MySecret". Esta sintaxe especifica uma categoria "Segredos" que contém um segredo "MySecret".
 
Agora, você pode acessar seus segredos no código. As próximas etapas são diferentes dependendo se você estiver usando o ASP.NET 4.7.1 ou ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Acesse seus segredos em código

1. Instale esses dois pacotes do nuget [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) bibliotecas NuGet.

2. Abra o arquivo Program.cs e substitua o código pelo seguinte código: 
```
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
```
3. Em seguida, abra o arquivo de About.cshtml.cs e escreva o código a seguir
    1. Incluir uma referência para Microsoft.Extensions.Configuration usando essa afirmação    
        ```
        using Microsoft.Extensions.Configuration
        ```
    2. Adicione esse construtor
        ```
        public AboutModel(IConfiguration configuration)
        {
            _configuration = configuration;
        }
        ```
    3. Atualize o método OnGet. Atualize o valor de espaço reservado mostrado aqui com o nome do segredo criado nos comandos acima
        ```
        public void OnGet()
        {
            //Message = "Your application description page.";
            Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
        }
        ```

Execute o aplicativo localmente, navegando até a página Sobre. Você deve recuperado o valor do segredo

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos. Isso exclui o Key Vault e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:**, digite o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o desenvolvimento do Key Vault. Leia o [Guia de Desenvolvedor do Key Vault](key-vault-developers-guide.md)