---
title: Criar identidade para o aplicativo do Azure com a CLI do Azure | Microsoft Docs
description: "Descreve como usar a CLI do Azure para criar um aplicativo do Active Directory e uma entidade de serviço, e conceder acesso a recursos por meio do controle de acesso baseado em função. Ele mostra como autenticar um aplicativo com uma senha ou certificado."
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
ms.date: 01/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 76c5bdeb2a27b733d8566c7a19f9457feebdc273


---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portal](resource-group-create-service-principal-portal.md)
> 
> 

Quando houver um aplicativo ou script que precisa acessar os recursos, você poderá configurar uma identidade para o aplicativo e autenticá-la com suas próprias credenciais. Esta abordagem é preferencial para executar o aplicativo com suas próprias credenciais porque:

* Você pode atribuir permissões para a identidade do aplicativo que são diferentes de suas próprias permissões. Normalmente, essas permissões são restritas a exatamente o que o aplicativo precisa fazer.
* Você não precisa alterar as credenciais do aplicativo se alterar suas responsabilidades. 
* Você pode usar um certificado para automatizar a autenticação ao executar um script autônomo.

Este tópico mostra como usar a [CLI do Azure para Mac, Linux e Windows](../xplat-cli-install.md) para configurar um aplicativo a ser executado com suas próprias credenciais e identidade.

Com a CLI do Azure, você tem duas opções para autenticar seu aplicativo do AD:

* Senha
* certificado

Este tópico mostra como usar as duas opções na CLI do Azure. Se você pretende fazer logon no Azure a partir de uma estrutura de programação (como o Python, Ruby ou Node.js), a autenticação de senha poderá ser a melhor opção. Antes de decidir se você deve usar uma senha ou um certificado, consulte a seção [Aplicativos de exemplo](#sample-applications) para obter exemplos de autenticação em diferentes estruturas.

## <a name="active-directory-concepts"></a>Conceitos do Active Directory
Neste artigo, você cria dois objetos: o aplicativo do Active Directory (AD) e a entidade de serviço. O aplicativo do AD é a representação global do seu aplicativo. Ele contém as credenciais (uma id do aplicativo e uma senha ou certificado). A entidade de serviço é a representação local de seu aplicativo em um Active Directory. Ele contém a atribuição de função. Este tópico foca em um aplicativo com um único locatário no qual o aplicativo se destina a ser executado dentro de uma só organização. Você normalmente usa os aplicativos com um único locatário para os aplicativos da linha de negócios executados em sua organização. Em um aplicativo de locatário único, você tem um aplicativo AD e uma entidade de serviço.

Você pode estar imaginando: Por que preciso dos dois objetos? Essa abordagem faz mais sentido quando você considera os aplicativos multilocatários. Normalmente, você usa aplicativos multilocatários para o software como serviço (aplicativos SaaS), no qual o aplicativo é executado em várias assinaturas diferentes. Para os aplicativos multilocatários, você tem um aplicativo AD e várias entidades de serviço (uma em cada Active Directory que concede acesso ao aplicativo). Para configurar um aplicativo multilocatário, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Permissões necessárias
Para concluir este tópico, você deve ter permissões suficientes no Azure Active Directory e em sua assinatura do Azure. Especificamente, você deve ser capaz de criar um aplicativo no Active Directory e atribuir a entidade de serviço a uma função. 

A maneira mais fácil de verificar se a sua conta tem as permissões adequadas é por meio do portal. Consulte [Verificar permissão necessária no portal](resource-group-create-service-principal-portal.md#required-permissions).

Agora, vá para uma seção para ver uma autenticação da [senha](#create-service-principal-with-password) ou do [certificado](#create-service-principal-with-certificate).

## <a name="create-service-principal-with-password"></a>Criar a entidade de serviço com a senha
Nesta seção, você executará as etapas para criar o aplicativo do AD com uma senha e atribuirá a função Leitor à entidade de serviço.

Vamos percorrer essas etapas.

1. Entre na sua conta.
   
   ```azurecli
   azure login
   ```
2. Há duas opções para criar o aplicativo do AD. Você pode criar o aplicativo do AD e a entidade de serviço em uma etapa ou pode criá-los separadamente. Crie-os em uma única etapa se não precisar especificar uma home page e URIs de identificador para o aplicativo. Crie-os separadamente se precisar definir esses valor para um aplicativo Web. Ambas as opções são mostradas nesta etapa.
   
   * Para criar o aplicativo do AD e a entidade de serviço em uma única etapa, forneça o nome do aplicativo e uma senha, conforme mostrado no seguinte comando:
     
     ```azurecli
     azure ad sp create -n exampleapp -p {your-password}
     ```
   * Para criar o aplicativo do AD separadamente, forneça:

      * nome do aplicativo
      * URL para a home page do aplicativo
      * lista delimitada por vírgulas de URIs que identificam o aplicativo
      * Senha

      Conforme mostra o seguinte comando:
     
     ```azurecli
     azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p {Your_Password}
     ```

       O comando anterior retorna um valor de AppId. Para criar uma entidade de serviço, forneça esse valor como um parâmetro no seguinte comando:
     
     ```azurecli
     azure ad sp create -a {AppId}
     ```
     
     Se sua conta não tiver as [permissões necessárias](#required-permissions) no Active Directory, você verá uma mensagem de erro informando "Authentication_Unauthorized" ou "Nenhuma assinatura encontrada no contexto".
     
     A nova entidade de serviço será retornada para ambas as opções. A `Object Id` é necessária ao conceder permissões. O guid listado com os `Service Principal Names` é necessário ao fazer logon. Esse guid tem o mesmo valor da ID do aplicativo. Em aplicativos de exemplo, esse valor é conhecido como `Client ID`. 
     
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

3. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo, você adiciona a entidade de serviço à função Leitor , que concede permissão para ler todos os recursos na assinatura. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md). No caso do parâmetro `objectid`, forneça a `Object Id` que você usou ao criar o aplicativo. Antes de executar esse comando, dê um tempo para a nova entidade de serviço se propagar pelo Active Directory. Geralmente, ao executar esses comandos manualmente, um tempo suficiente já decorreu entre as tarefas. Em um script, adicione uma etapa de espera entre os comandos (como `sleep 15`). Se você vir um erro informando que a entidade não existe no diretório, execute o comando novamente.
   
   ```azurecli
   azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
     Se sua conta não tiver permissões suficientes para atribuir uma função, verá uma mensagem de erro. A mensagem informa que sua conta “não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no escopo '/subscriptions/{guid}'”.

É isso! Seu aplicativo do AD e a entidade de serviço estão configurados. A próxima seção mostra como fazer logon com as credenciais por meio da CLI do Azure. Se você quiser usar a credencial em seu aplicativo de código, não precisará continuar com este tópico. Pode ir para os [aplicativos de amostra](#sample-applications) para ter exemplos de logon com sua ID do aplicativo e senha. 

### <a name="provide-credentials-through-azure-cli"></a>Fornecer credenciais por meio da CLI do Azure
Agora, você precisa fazer logon como o aplicativo para executar as operações.

1. Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Para recuperar a id do locatário para sua assinatura autenticada no momento, use:
   
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
2. Combine as chaves pública e privada.
   
   ```
   cat privkey.pem cert.pem > examplecert.pem
   ```
3. Abra o arquivo **examplecert.pem** e procure a longa sequência de caracteres entre **-----BEGIN CERTIFICATE-----** e **-----END CERTIFICATE-----**. Copie os dados do certificado. Você pode passar esses dados como um parâmetro ao criar a entidade de serviço.
4. Entre na sua conta.
   
   ```azurecli
   azure login
   ```
5. Há duas opções para criar o aplicativo do AD. Você pode criar o aplicativo do AD e a entidade de serviço em uma etapa ou pode criá-los separadamente. Crie-os em uma única etapa se não precisar especificar uma home page e URIs de identificador para o aplicativo. Crie-os separadamente se precisar definir esses valor para um aplicativo Web. Ambas as opções são mostradas nesta etapa.
   
   * Para criar o aplicativo do AD e a entidade de serviço em uma única etapa, forneça o nome do aplicativo e os dados do certificado, conforme mostrado no seguinte comando:
     
     ```azurecli
     azure ad sp create -n exampleapp --cert-value {certificate data}
     ```
   * Para criar o aplicativo do AD separadamente, forneça:
      
      * nome do aplicativo
      * URL para a home page do aplicativo
      * lista delimitada por vírgulas de URIs que identificam o aplicativo
      * os dados do certificado

      Conforme mostra o seguinte comando:

     ```azurecli
     azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value {certificate data}
     ```
     
       O comando anterior retorna um valor de AppId. Para criar uma entidade de serviço, forneça esse valor como um parâmetro no seguinte comando:
     
     ```azurecli
     azure ad sp create -a {AppId}
     ```
     
     Se sua conta não tiver as [permissões necessárias](#required-permissions) no Active Directory, você verá uma mensagem de erro informando "Authentication_Unauthorized" ou "Nenhuma assinatura encontrada no contexto".
     
     A nova entidade de serviço será retornada para ambas as opções. A ID de objeto é necessária ao conceder permissões. O guid listado com os `Service Principal Names` é necessário ao fazer logon. Esse guid tem o mesmo valor da ID do aplicativo. Em aplicativos de exemplo, esse valor é conhecido como `Client ID`. 
     
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
6. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo, você adiciona a entidade de serviço à função Leitor , que concede permissão para ler todos os recursos na assinatura. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md). No caso do parâmetro `objectid`, forneça a `Object Id` que você usou ao criar o aplicativo. Antes de executar esse comando, dê um tempo para a nova entidade de serviço se propagar pelo Active Directory. Geralmente, ao executar esses comandos manualmente, um tempo suficiente já decorreu entre as tarefas. Em um script, adicione uma etapa de espera entre os comandos (como `sleep 15`). Se você vir um erro informando que a entidade não existe no diretório, execute o comando novamente.
   
   ```azurecli
   azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
     Se sua conta não tiver permissões suficientes para atribuir uma função, verá uma mensagem de erro. A mensagem informa que sua conta “não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no escopo '/subscriptions/{guid}'”.

### <a name="provide-certificate-through-automated-azure-cli-script"></a>Fornecer certificado por meio do script CLI do Azure automatizado
Agora, você precisa fazer logon como o aplicativo para executar as operações.

1. Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Para recuperar a id do locatário para sua assinatura autenticada no momento, use:
   
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

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

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


## <a name="sample-applications"></a>Aplicativos de exemplo
Os aplicativos de exemplo a seguir mostram como fazer logon como a entidade de serviço.

**.NET**

* [Implantar uma VM Habilitada para SSH com um Modelo com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Gerenciar recursos e grupos de recursos do Azure com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Introdução aos Recursos - Implantar Usando o Modelo do Azure Resource Manager - em Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Introdução aos Recursos - Gerenciar o Grupo de Recursos - em Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Implantar uma VM Habilitada para SSH com um Modelo no Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Gerenciando o Recurso e Grupos de Recursos do Azure com o Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

* [Implantar uma VM Habilitada para SSH com um Modelo no Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Gerenciar recursos e grupos de recursos do Azure com Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Implantar uma VM Habilitada para SSH com um Modelo no Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Gerenciando o Recurso e Grupos de Recursos do Azure com Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Próximas etapas
* Para ver as etapas detalhadas sobre como integrar um aplicativo no Azure para gerenciar os recursos, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).
* Para obter mais informações sobre como usar os certificados e a CLI do Azure, consulte [Autenticação baseada em certificados com as Entidades de Serviço do Azure a partir da linha de comando do Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx). 




<!--HONumber=Jan17_HO4-->


