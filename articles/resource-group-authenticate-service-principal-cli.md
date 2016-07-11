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
   ms.date="05/26/2016"
   ms.author="tomfitz"/>

# Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

Este tópico mostra como usar a [CLI do Azure para Mac, Linux e Windows](xplat-cli-install.md) para criar um aplicativo AD (Active Directory), como um processo automatizado, aplicativo ou serviço que possa acessar outros recursos em sua assinatura. Com o Azure Resource Manager, você pode usar o controle de acesso baseado em função para conceder ações permitidas ao aplicativo.

Neste artigo, você criará dois objetos: o aplicativo do AD e a entidade do serviço. O aplicativo do AD reside no locatário onde o aplicativo está registrado e define o processo a ser executado. A entidade de serviço contém a identidade do aplicativo do AD e é usada para atribuir permissões. A partir do aplicativo do AD, você pode criar várias entidades de serviço. Para obter uma explicação mais detalhada de aplicativos e entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço](./active-directory/active-directory-application-objects.md). Para obter mais informações sobre a autenticação do Active Directory, consulte [Cenários de autenticação do Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Há duas opções para autenticar seu aplicativo:

 - senha - adequada quando um usuário deseja entrar de forma interativa durante a execução
 - certificado - adequado para scripts autônomos que devem ser autenticados sem interação do usuário
 
## Criar um aplicativo do AD com senha

Nesta seção, você executará as etapas para criar o aplicativo do AD com uma senha.

1. Alterne para o modo do Gerenciador de Recursos do Azure e entre na sua conta.

        azure config mode arm
        azure login

2. Crie um novo aplicativo do AD executando o comando **azure ad app create**. Forneça um nome de exibição para seu aplicativo, o URI para uma página que descreve o aplicativo (o link não é verificado), os URIs que identificam seu aplicativo e a senha para a identidade do seu aplicativo.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    O aplicativo do AD retorna. A propriedade AppId é necessária para criar entidades de serviço e adquirir tokens de acesso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### Criar entidade de serviço e atribuir à função

1. Crie uma entidade de serviço para seu aplicativo. Forneça a ID do aplicativo que foi retornada na etapa anterior.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    A nova entidade de serviço será retornada. A ID de objeto é necessária ao conceder permissões.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

2. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md)

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Fornecer manualmente as credenciais por meio da CLI do Azure

Você criou um aplicativo do AD e uma entidade de serviço para esse aplicativo. Você atribuiu a entidade de serviço a uma função. Agora, você precisa fazer logon como o aplicativo para executar operações. Você pode fornecer manualmente as credenciais para o aplicativo ao executar scripts sob demanda ou comandos.

1. Determine a **TenantId** da assinatura que contém a entidade de serviço. Se você está recuperando a ID do locatário para a assinatura autenticada no momento, não precisa fornecer a ID de assinatura como um parâmetro. A opção **-r** recupera o valor sem as aspas.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. Para o nome de usuário, use a **AppId** que você usou ao criar a entidade de serviço. Se você precisar recuperar a id do aplicativo, use o comando a seguir. Forneça o nome do aplicativo do Active Directory no parâmetro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. Faça logon como a entidade de serviço.

        azure login -u "$appId" --service-principal --tenant "$tenantId"

    Você receberá uma solicitação pela senha. Forneça a senha especificada durante a criação do aplicativo do AD.

        info:    Executing command login
        Password: ********

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do AD criado.

## Criar um aplicativo do AD com certificado

Nesta seção, você executará as etapas para criar uma entidade de serviço para um aplicativo do AD que usa um certificado para autenticação. Para concluir essas etapas, você deve ter o [OpenSSL](http://www.openssl.org/) instalado.

1. Crie um certificado autoassinado.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combine as chaves pública e privada.

        cat privkey.pem cert.pem > examplecert.pem

3. Abra o arquivo **examplecert.pem** e copie os dados do certificado. Procure a longa sequência de caracteres entre **-----BEGIN CERTIFICATE-----** e **-----END CERTIFICATE-----**.

4. Crie um novo Aplicativo do Active Directory executando o comando **azure ad app create** e forneça os dados do certificado copiados na etapa anterior como o valor da chave.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    O aplicativo do Active Directory será retornado. A propriedade AppId é necessária para criar entidades de serviço e adquirir tokens de acesso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### Criar entidade de serviço e atribuir à função

1. Crie uma entidade de serviço para seu aplicativo. Forneça a ID do aplicativo que foi retornada na etapa anterior.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    A nova entidade de serviço será retornada. A ID de objeto é necessária ao conceder permissões.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para saber mais sobre o controle de acesso baseado em função, confira [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md)

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Preparar valores para seu script

No script, você passará três valores que são necessários para fazer logon como a entidade de serviço. Você precisará do seguinte:

- id do aplicativo
- ID do locatário
- Impressão digital do certificado

Você já viu a id do aplicativo e a impressão digital do certificado nas etapas anteriores. No entanto, se você precisar recuperar esses valores posteriormente, os comandos serão mostrados abaixo, junto com o comando para obter a id do locatário.

1. Para recuperar a impressão digital do certificado e remover os caracteres desnecessários, use:

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Que retorna um valor de impressão digital semelhante a:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Para recuperar a id do locatário, use:

        azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId'

3. Para recuperar a id do aplicativo, use:

        azure ad app show --search exampleapp --json | jq -r '.[0].appId'

### Fornecer certificado por meio do script CLI do Azure automatizado

Você criou um aplicativo do Active Directory e uma entidade de serviço para esse aplicativo. Você atribuiu a entidade de serviço a uma função. Agora, você precisa fazer logon como a entidade de serviço para executar operações como a entidade de serviço.

Para autenticar na CLI do Azure, forneça a impressão digital do certificado, arquivo do certificado, id do aplicativo e id do locatário.

        azure login --service-principal --tenant {tenant-id} -u {app-id} --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

## Próximas etapas
  
- Para obter exemplos de autenticação .NET, confira [SDK do Azure Resource Manager para .NET](resource-manager-net-sdk.md).
- Para obter exemplos de autenticação Java, confira [SDK do Azure Resource Manager para Java](resource-manager-java-sdk.md).
- Para obter exemplos de autenticação Python, confira [Autenticação de Gerenciamento de Recursos para Python](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html).
- Para obter exemplos de autenticação REST, confira [APIs REST do Resource Manager](resource-manager-rest-api.md).
- Para obter etapas detalhadas sobre como integrar um aplicativo do Azure para gerenciar recursos, consulte [Guia do desenvolvedor para autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).
- Para saber mais sobre como usar os certificados e a CLI do Azure, confira [Autenticação baseada em certificados com as Entidades de Serviço do Azure a partir da linha de comando do Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)

<!---HONumber=AcomDC_0629_2016-->