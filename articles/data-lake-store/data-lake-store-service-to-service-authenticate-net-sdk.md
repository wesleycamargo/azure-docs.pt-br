---
title: "Autenticação serviço a serviço: SDK do .NET com o Data Lake Store usando o Azure Active Directory | Microsoft Docs"
description: "Saiba como obter a autenticação serviço a serviço com o Data Lake Store usando o Azure Active Directory com o SDK do .NET"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: fb3d3dd518cbe0a603524f1bdc38d122582aa6b0
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-net-sdk"></a>Autenticação serviço a serviço com o Data Lake Store usando o SDK do .NET
> [!div class="op_single_selector"]
> * [Usando o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Usar o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Usando o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Usar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Neste artigo, você aprenderá como usar o SDK do .NET para fazer a autenticação serviço a serviço com o Azure Data Lake Store. Para a autenticação do usuário final com o Data Lake Store usando o SDK do .NET, consulte [Autenticação do usuário final com Data Lake Store usando SDK do .NET](data-lake-store-end-user-authenticate-net-sdk.md).


## <a name="prerequisites"></a>Pré-requisitos
* **Visual Studio 2013, 2015 ou 2017**. As instruções abaixo usam o Visual Studio 2017.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Web" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação serviço a serviço com o Data Lake Store usando o Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Criar um aplicativo .NET
1. Abra o Visual Studio e crie um aplicativo de console.
2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.
3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

   | Propriedade | Valor |
   | --- | --- |
   | Categoria |Modelos/Visual C#/Windows |
   | Modelo |Aplicativo de console |
   | NOME |CreateADLApplication |
4. Clique em **OK** para criar o projeto.

5. Adicione os pacotes NuGet ao seu projeto.

   1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
   2. Na guia **Gerenciador de Pacotes NuGet**, verifique se a **Origem do pacote** está definida como **nuget.org** e se a caixa de seleção **Incluir pré-lançamento** está marcada.
   3. Procure e instale os seguintes pacotes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - este tutorial usa a versão 2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - este tutorial usa a versão v2.2.12.

        ![Adicionar uma origem de NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Criar uma nova conta do Azure Data Lake")
   4. Feche o **Gerenciador de Pacotes NuGet**.

6. Abra **Program.cs**, exclua o código existente e inclua as instruções a seguir para adicionar referências aos namespaces.

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

## <a name="service-to-service-authentication-with-client-secret"></a>Autenticação serviço a serviço com o segredo do cliente
Adicione este trecho no aplicativo cliente do .NET. Substitua os valores de espaço reservado pelos valores recuperados de um aplicativo Web do Azure AD (listado como um pré-requisito).  Este trecho de código permite autenticar seu aplicativo **de maneira não interativa** com o Data Lake Store usando a chave/segredo do cliente do aplicativo Web do Azure AD. 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
        var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
        var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
    }

O trecho de código anterior usa uma função auxiliar `GetCreds_SPI_SecretKey`. O código para essa função auxiliar está disponível [aqui no Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Autenticação serviço a serviço com certificado

Adicione este trecho no aplicativo cliente do .NET. Substitua os valores de espaço reservado pelos valores recuperados de um aplicativo Web do Azure AD (listado como um pré-requisito). Este trecho de código permite autenticar seu aplicativo **de maneira não interativa** com o Data Lake Store usando o certificado para um aplicativo Web do Azure AD. Para obter instruções sobre como criar um aplicativo do Azure AD, consulte [Criar entidade de serviço com certificados](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    
    private static void Main(string[] args)
    {
        // Service principal / application authentication with certificate
        // Use the client ID and certificate of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
        var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
        var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
    }

O trecho de código anterior usa uma função auxiliar `GetCreds_SPI_Cert`. O código para essa função auxiliar está disponível [aqui no Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar a autenticação serviço a serviço para autenticar no Azure Data Lake Store com o SDK do .NET. Agora você pode examinar os seguintes artigos que discorrem sobre como usar o SDK do .NET para trabalhar com o Azure Data Lake Store.

* [Operações de gerenciamento de conta no Data Lake Store usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Operações de dados no Data Lake Store usando o SDK do .NET](data-lake-store-data-operations-net-sdk.md)


