---
title: Como usar um feed NuGet personalizado no Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: article
description: Use um feed NuGet personalizado para acessar e usar os pacotes NuGet em um Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
manager: ghogen
ms.openlocfilehash: 3badd15bcfd09c97b43744a20c5df05f4ff57e84
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199102"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Use um feed NuGet personalizado em um Azure Dev Space

Um feed NuGet fornece uma maneira conveniente de incluir fontes de pacotes em um projeto. O Azure Dev Spaces precisará acessar esse feed para que as dependências sejam instaladas corretamente no contêiner do Docker.

## <a name="set-up-a-nuget-feed"></a>Configurar um feed NuGet

Para configurar um feed NuGet:
1. Adicione uma [referência de pacote](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files) no arquivo `*.csproj` no nó `PackageReference`.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Crie um arquivo [NuGet.Config](https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file) na pasta do projeto.
     * Use a seção `packageSources` para referenciar o local do feed NuGet. Importante: o feed NuGet deve estar publicamente acessível.
     * Use a seção `packageSourceCredentials` para configurar credenciais de nome de usuário e senha. 

   ```xml
   <packageSources>
       <add key="Contoso" value="https://contoso.com/packages/" />
   </packageSources>

   <packageSourceCredentials>
       <Contoso>
           <add key="Username" value="user@contoso.com" />
           <add key="ClearTextPassword" value="33f!!lloppa" />
       </Contoso>
   </packageSourceCredentials>
   ```

3. Se você estiver usando o controle do código-fonte:
    - Referencie `NuGet.Config`no arquivo `.gitignore` para que você não confirme credenciais acidentalmente no repositório de origem.
    - Abra o arquivo `azds.yaml` no projeto, localize a seção `build` e insira o trecho de código a seguir para garantir que o arquivo `NuGet.Config` seja sincronizado com o Azure e utilizado durante o processo de compilação da imagem do container. (Por padrão, o Azure Dev Spaces não sincroniza arquivos que correspondam às regras `.gitignore` e `.dockerignore`.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>Próximas etapas

Após concluir as etapas acima, na próxima vez em que você executar `azds up` (ou clicar `F5` no VSCode ou Visual Studio), o Azure Dev Spaces sincronizará o arquivo `NuGet.Config` com o Azure, o qual será usado por `dotnet restore` para instalar dependências de pacotes no contêiner.

