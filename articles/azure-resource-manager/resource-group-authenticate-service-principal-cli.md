---
title: Criar identidade para o aplicativo do Azure com a CLI do Azure | Microsoft Docs
description: "Descreve como usar a CLI do Azure para criar um aplicativo do Active Directory do Azure e uma entidade de serviço, e conceder acesso a recursos por meio do controle de acesso baseado em função. Ele mostra como autenticar um aplicativo com uma senha ou certificado."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c224a189-dd28-4801-b3e3-26991b0eb24d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/15/2017
ms.author: tomfitz
ms.openlocfilehash: 3c5826d58887ff1af4df8e66999d9c1a1643bcc7
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2017
---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos

Quando você tiver um aplicativo ou script que precisa acessar recursos, poderá configurar uma identidade para o aplicativo e autenticá-lo com suas próprias credenciais. Essa identidade é conhecida como uma entidade de serviço. Essa abordagem permite:

* Atribuir permissões à identidade do aplicativo que são diferentes de suas próprias permissões. Normalmente, essas permissões são restritas a exatamente o que o aplicativo precisa fazer.
* Use um certificado para a autenticação ao executar um script autônomo.

Este artigo mostra como usar a [CLI do Azure 1.0](../cli-install-nodejs.md) para configurar um aplicativo a ser executado com suas próprias credenciais e identidade. Instale a versão mais recente do [Azure CLI 1.0](../cli-install-nodejs.md) para certificar-se de que seu ambiente corresponde aos exemplos neste artigo.

## <a name="required-permissions"></a>Permissões necessárias
Para concluir este tópico, você deve ter permissões suficientes no Azure Active Directory e em sua assinatura do Azure. Especificamente, você deve ser capaz de criar um aplicativo no Active Directory do Azure e atribuir a entidade de serviço a uma função. 

A maneira mais fácil de verificar se a sua conta tem as permissões adequadas é por meio do portal. Consulte [Verificar permissão necessária no portal](resource-group-create-service-principal-portal.md#required-permissions).

Agora, vá para uma seção para ver uma autenticação da [senha](#create-service-principal-with-password) ou do [certificado](#create-service-principal-with-certificate).

## <a name="create-service-principal-with-password"></a>Criar a entidade de serviço com a senha
Nesta seção, você executará as etapas para criar o aplicativo do AD com uma senha e atribuirá a função Leitor à entidade de serviço.

1. Entre na sua conta.
   
   ```azurecli
   azure login
   ```
2. Para criar uma identidade de aplicativo, forneça o nome do aplicativo e uma senha, conforme mostrado no comando a seguir:
     
   ```azurecli
   azure ad sp create -n exampleapp -p {your-password}
   ```
     
   A nova entidade de serviço será retornada. A ID de objeto é necessária ao conceder permissões. O guid listado com os Nomes de Entidade de Serviço é necessário ao fazer logon. Esse guid tem o mesmo valor da ID do aplicativo. Em aplicativos de exemplo, esse valor é conhecido como `Client ID`. 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating application exampleapp
     / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
     data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
     data:    Display Name:            exampleapp
     data:    Service Principal Names:
     data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
     data:                             https://www.contoso.org/example
     info:    ad sp create command OK
   ```

3. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo, você adiciona a entidade de serviço à função Leitor , que concede permissão para ler todos os recursos na assinatura. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md). Para obter o parâmetro objectid, forneça o a ID de Objeto que você usou ao criar o aplicativo. Antes de executar esse comando, dê um tempo para a nova entidade de serviço se propagar pelo Active Directory do Azure. Geralmente, ao executar esses comandos manualmente, um tempo suficiente já decorreu entre as tarefas. Em um script, adicione uma etapa de espera entre os comandos (como `sleep 15`). Se você vir um erro informando que a entidade não existe no diretório, execute o comando novamente.
   
   ```azurecli
   azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
É isso! Seu aplicativo do AD e a entidade de serviço estão configurados. A próxima seção mostra como fazer logon com as credenciais por meio da CLI do Azure. Se você quiser usar a credencial em seu aplicativo de código, não precisará continuar com este tópico. Pode ir para os [aplicativos de amostra](#sample-applications) para ter exemplos de logon com sua ID do aplicativo e senha. 

### <a name="provide-credentials-through-azure-cli"></a>Fornecer credenciais por meio da CLI do Azure
Agora, você precisa fazer logon como o aplicativo para executar as operações.

1. Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory do Azure. Para recuperar a id do locatário para sua assinatura autenticada no momento, use:
   
   ```azurecli
   azure account show
   ```
   
   Que retorna:
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     Se você precisar obter a id de locatário de outra assinatura, use o seguinte comando:
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. Se você precisa recuperar a id de cliente a ser usada para fazer logon, use:
   
   ```azurecli
   azure ad sp show -c exampleapp --json
   ```
   
     O valor a ser usado para fazer logon é o guid listado nos nomes de entidade de serviço.
   
   ```azurecli
   [
     {
       "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "7132aca4-1bdb-4238-ad81-996ff91d8db4"
       ]
     }
   ]
   ```
3. Faça logon como a entidade de serviço.
   
   ```azurecli
   azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}
   ```
   
    Você receberá uma solicitação de senha. Forneça a senha especificada durante a criação do aplicativo do AD.
   
   ```azurecli
   info:    Executing command login
   Password: ********
   ```

Agora, você está autenticado como a entidade de serviço para a entidade criada.

Como alternativa, você pode invocar operações REST a partir da linha de comando para fazer logon. Na resposta de autenticação, você pode recuperar o token de acesso para uso com outras operações. Para obter um exemplo de como recuperar o token de acesso invocando operações REST, consulte [Gerar um token de acesso](resource-manager-rest-api.md#generating-an-access-token).

## <a name="create-service-principal-with-certificate"></a>Criar a entidade de serviço com o certificado
Nesta seção, você deve executar as etapas para:

* criar um certificado autoassinado
* criar o aplicativo do AD com o certificado e a entidade de serviço
* atribuir a função Leitor à entidade de serviço

Para concluir essas etapas, você deve ter o [OpenSSL](http://www.openssl.org/) instalado.

1. Crie um certificado autoassinado.
   
   ```
   openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
   ```

2. A etapa anterior criou dois arquivos – privkey.pem e cert.pem. Combine as chaves pública e privada em um único arquivo.

   ```
   cat privkey.pem cert.pem > examplecert.pem
   ```

3. Abra o arquivo **examplecert.pem** e procure a longa sequência de caracteres entre **-----BEGIN CERTIFICATE-----** e **-----END CERTIFICATE-----**. Copie os dados do certificado. Você pode passar esses dados como um parâmetro ao criar a entidade de serviço.

4. Entre na sua conta.

   ```azurecli
   azure login
   ```
5. Para criar a entidade de serviço, forneça o nome do aplicativo e os dados do certificado, conforme mostrado no seguinte comando:
     
   ```azurecli
   azure ad sp create -n exampleapp --cert-value {certificate data}
   ```
     
   A nova entidade de serviço será retornada. A ID de objeto é necessária ao conceder permissões. O guid listado com os Nomes de Entidade de Serviço é necessário ao fazer logon. Esse guid tem o mesmo valor da ID do aplicativo. Em aplicativos de exemplo, esse valor é conhecido como a ID do Cliente. 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
     data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
     data:    Display Name:     exampleapp
     data:    Service Principal Names:
     data:                      4fd39843-c338-417d-b549-a545f584a745
     data:                      https://www.contoso.org/example
     info:    ad sp create command OK
   ```
6. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo, você adiciona a entidade de serviço à função Leitor , que concede permissão para ler todos os recursos na assinatura. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md). Para obter o parâmetro objectid, forneça o a ID de Objeto que você usou ao criar o aplicativo. Antes de executar esse comando, dê um tempo para a nova entidade de serviço se propagar pelo Active Directory do Azure. Geralmente, ao executar esses comandos manualmente, um tempo suficiente já decorreu entre as tarefas. Em um script, adicione uma etapa de espera entre os comandos (como `sleep 15`). Se você vir um erro informando que a entidade não existe no diretório, execute o comando novamente.
   
   ```azurecli
   azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   ```
  
### <a name="provide-certificate-through-automated-azure-cli-script"></a>Fornecer certificado por meio do script CLI do Azure automatizado
Agora, você precisa fazer logon como o aplicativo para executar as operações.

1. Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory do Azure. Para recuperar a id do locatário para sua assinatura autenticada no momento, use:
   
   ```azurecli
   azure account show
   ```
   
   Que retorna:
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
   Se você precisar obter a id de locatário de outra assinatura, use o seguinte comando:
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. Para recuperar a impressão digital do certificado e remover os caracteres desnecessários, use:
   
   ```
   openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   ```
   
   Que retorna um valor de impressão digital semelhante a:
   
   ```
   30996D9CE48A0B6E0CD49DBB9A48059BF9355851
   ```
3. Se você precisa recuperar a id de cliente a ser usada para fazer logon, use:
   
   ```azurecli
   azure ad sp show -c exampleapp
   ```
   
   O valor a ser usado para fazer logon é o guid listado nos nomes de entidade de serviço.
     
   ```azurecli
   [
     {
       "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "4fd39843-c338-417d-b549-a545f584a745",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "4fd39843-c338-417d-b549-a545f584a745"
       ]
     }
   ]
   ```
4. Faça logon como a entidade de serviço.
   
   ```azurecli
   azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}
   ```

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory do Azure criado.

## <a name="change-credentials"></a>Alterar credenciais

Para alterar as credenciais para um aplicativo do AD, por causa de uma violação de segurança ou expiração de credencial, use `azure ad app set`.

Para alterar uma senha, use:

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --password p@ssword
```

Para alterar um valor de certificado, use:

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --cert-value {certificate data}
```

## <a name="debug"></a>Depurar

Você pode receber os seguintes erros ao criar uma entidade de serviço:

* **"Authentication_Unauthorized"** ou **"Nenhuma assinatura encontrada no contexto".** – Você verá esse erro quando sua conta não tiver as [permissões necessárias](#required-permissions) no Active Directory do Azure para registrar um aplicativo. Normalmente, você verá esse erro somente quando os usuários administradores no Active Directory do Azure puderem registrar aplicativos e sua conta não for um administrador. Solicite ao administrador para lhe atribuir uma função de administrador ou para permitir que os usuários registrem aplicativos.

* Sua conta **"não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no escopo '/subscriptions/{guid}'".**  – Você verá esse erro quando sua conta não tiver permissões suficientes para atribuir uma função a uma identidade. Solicite ao administrador da assinatura para adicioná-lo à função Administrador de Acesso do Usuário.

## <a name="sample-applications"></a>Aplicativos de exemplo
Para obter informações sobre como fazer logon no aplicativo por meio de diferentes plataformas, consulte:

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Próximas etapas
* Para ver as etapas detalhadas sobre como integrar um aplicativo no Azure para gerenciar os recursos, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).
* Para obter mais informações sobre como usar os certificados e a CLI do Azure, consulte [Autenticação baseada em certificados com as Entidades de Serviço do Azure a partir da linha de comando do Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx). 
* Para obter uma lista de ações disponíveis que podem ser concedidas ou negadas a usuários, consulte [Operações do Provedor de Recursos do Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).
