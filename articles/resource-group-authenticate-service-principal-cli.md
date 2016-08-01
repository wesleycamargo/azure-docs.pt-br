<properties
   pageTitle="Criar entidade de serviço com a CLI do Azure | Microsoft Azure"
   description="Descreve como usar a CLI do Azure para criar um aplicativo do Active Directory e uma entidade de serviço, e conceder acesso a recursos por meio do controle de acesso baseado em função. Ele mostra como autenticar um aplicativo com uma senha ou certificado."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

Quando você tiver um aplicativo ou script que precisa acessar recursos, provavelmente não desejará executar esse processo com as credenciais de um usuário. Esse usuário pode ter permissões diferentes que você gostaria de atribuir ao processo e as responsabilidades de trabalho do usuário podem ser alteradas. Em vez disso, você pode criar uma identidade para o aplicativo que inclui as credenciais de autenticação e as atribuições de função. Seu aplicativo fará logon com essa identidade sempre que for executado. Este tópico mostra como usar a [CLI do Azure para Mac, Linux e Windows](xplat-cli-install.md) para configurar tudo que você precisa para um aplicativo ser executado com suas próprias credenciais e identidade.

Neste artigo, você criará dois objetos: o aplicativo do Active Directory (AD) e a entidade de serviço. O aplicativo do AD contém as credenciais (uma id do aplicativo e uma senha ou certificado). A entidade de serviço contém a atribuição da função. A partir do aplicativo do AD, você pode criar várias entidades de serviço. Este tópico foca em um aplicativo com um único locatário no qual o aplicativo se destina a ser executado dentro de uma só organização. Você normalmente usa os aplicativos com um único locatário para os aplicativos da linha de negócios executados em sua organização. Você também pode criar aplicativos multilocatários quando seu aplicativo precisa ser executado em muitas organizações. Em geral, você usa os aplicativos multilocatários para os aplicativos do software como serviço (SaaS). Para configurar um aplicativo multilocatário, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).

Há muitos conceitos a entender ao trabalhar com o Active Directory. Para obter uma explicação mais detalhada de aplicativos e entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço](./active-directory/active-directory-application-objects.md). Para obter mais informações sobre a autenticação do Active Directory, consulte [Cenários de autenticação do Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Com a CLI do Azure, você tem duas opções para autenticar seu aplicativo do AD:

 - Senha
 - certificado

Se, depois de configurar seu aplicativo do AD, você pretende fazer logon no Azure a partir de outra estrutura de programação (como Python, Ruby ou Node.js), a autenticação de senha pode ser a melhor opção. Antes de decidir se você deve usar uma senha ou um certificado, consulte a seção [Aplicativos de exemplo](#sample-applications) para obter exemplos de autenticação em diferentes estruturas.

## Obter a ID do locatário

Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Como você precisará desse valor para a autenticação da senha ou do certificado, obteremos esse valor agora.

1. Entre na sua conta.

        azure config mode arm
        azure login

1. Se você está recuperando a ID do locatário para a assinatura autenticada no momento, não precisa fornecer a ID de assinatura como um parâmetro. O argumento **-r** recupera o valor sem as aspas.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

Agora, vá para uma seção abaixo para ver uma autenticação da [senha](#create-service-principal-with-password) ou do [certificado](#create-service-principal-with-certificate).


## Criar a entidade de serviço com a senha

Nesta seção, você executará as etapas para criar uma entidade de serviço com uma senha e irá atribuí-la a uma função.

1. Crie uma entidade de serviço para seu aplicativo. Forneça um nome de exibição para seu aplicativo, o URI para uma página que descreve o aplicativo (o link não é verificado), os URIs que identificam seu aplicativo e a senha para a identidade do seu aplicativo. Este comando cria o aplicativo do AD e a entidade de serviço.

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
        
    A nova entidade de serviço será retornada. A ID de objeto é necessária ao conceder permissões. O nome da entidade de serviço é necessário ao fazer logon.
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md) Para atribuir uma função, você deve ter o acesso `Microsoft.Authorization/*/Write` que é concedido por meio da função [Proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrador de Acesso do Usuário](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

É isso! Seu aplicativo do AD e a entidade de serviço estão configurados. A próxima seção mostra como fazer logon com as credenciais por meio da CLI do Azure. No entanto, se você quiser usar a credencial em seu aplicativo de código, não precisará continuar com este tópico. Pode ir para os [aplicativos de amostra](#sample-applications) para ter exemplos de logon com sua ID do aplicativo e senha.

### Fornecer credenciais por meio da CLI do Azure

1. Para o nome de usuário, use o nome da entidade de serviço que foi retornado quando você criou a entidade. Se você precisar recuperar a id do aplicativo, use o comando a seguir. Forneça o nome do aplicativo do Active Directory no parâmetro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Faça logon como a entidade de serviço.

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant "$tenantId"

    Você receberá uma solicitação pela senha. Forneça a senha especificada durante a criação do aplicativo do AD.

        info:    Executing command login
        Password: ********

Agora, você está autenticado como a entidade de serviço para a entidade criada.

## Criar a entidade de serviço com o certificado

Nesta seção, você executará as etapas para criar uma entidade de serviço para a entidade que usa um certificado para a autenticação. Para concluir essas etapas, você deve ter o [OpenSSL](http://www.openssl.org/) instalado.

1. Crie um certificado autoassinado.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combine as chaves pública e privada.

        cat privkey.pem cert.pem > examplecert.pem

3. Abra o arquivo **examplecert.pem** e copie os dados do certificado. Procure a longa sequência de caracteres entre **-----BEGIN CERTIFICATE-----** e **-----END CERTIFICATE-----**.

1. Crie uma entidade de serviço para seu aplicativo. Forneça a ID do aplicativo que foi retornada na etapa anterior.

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
    A nova entidade de serviço será retornada. A ID de objeto é necessária ao conceder permissões.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md) Para atribuir uma função, você deve ter o acesso `Microsoft.Authorization/*/Write` que é concedido por meio da função [Proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrador de Acesso do Usuário](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Fornecer certificado por meio do script CLI do Azure automatizado

Para autenticar na CLI do Azure, forneça a impressão digital do certificado, arquivo do certificado, id do aplicativo e id do locatário.

    azure login --service-principal --tenant 00000 -u 000000 --certificate-file C:\certificates\examplecert.pem --thumbprint 000000

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

Se você precisar recuperar a impressão digital do certificado e remover os caracteres desnecessários, use:

    openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
Que retorna um valor de impressão digital semelhante a:

    30996D9CE48A0B6E0CD49DBB9A48059BF9355851

Se precisar recuperar a ID do aplicativo, use:

    azure ad app show --search exampleapp --json | jq -r '.[0].appId'

Se precisar recuperar a ID do locatário mais tarde, consulte [Obter ID do locatário](#get-tenant-id).


## Aplicativos de exemplo

Os aplicativos de exemplo a seguir mostram como fazer logon como a entidade de serviço.

**.NET**

- [Implantar uma VM Habilitada para SSH com um Modelo com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gerenciar recursos e grupos de recursos do Azure com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Introdução aos Recursos - Implantar Usando o Modelo do Azure Resource Manager - em Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introdução aos Recursos - Gerenciar o Grupo de Recursos - em Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Implantar uma VM Habilitada para SSH com um Modelo no Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gerenciando o Recurso e Grupos de Recursos do Azure com o Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Implantar uma VM Habilitada para SSH com um Modelo no Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gerenciar recursos e grupos de recursos do Azure com Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Implantar uma VM Habilitada para SSH com um Modelo no Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gerenciando o Recurso e Grupos de Recursos do Azure com Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## Próximas etapas
  
- Para ver as etapas detalhadas sobre como integrar um aplicativo no Azure para gerenciar os recursos, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).
- Para obter mais informações sobre como usar os certificados e a CLI do Azure, consulte [Autenticação baseada em certificados com as Entidades de Serviço do Azure a partir da linha de comando do Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx).

<!---HONumber=AcomDC_0720_2016-->