<properties 
	pageTitle="Saiba como proteger o acesso aos dados no Banco de Dados de Documentos | Microsoft Azure" 
	description="Saiba mais sobre conceitos de controle de acesso no Banco de Dados de Documentos, incluindo chaves mestras, chaves somente leitura, usuários e permissões." 
	services="documentdb" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/16/2015" 
	ms.author="ryancraw"/>

# Protegendo o acesso a dados no Banco de Dados de Documentos #

Este artigo fornece uma visão geral de como proteger o acesso a dados armazenados no [Banco de Dados de Documentos do Microsoft Azure](../../services/documentdb/).

Após ler esta visão geral, você poderá responder as perguntas a seguir:

-	O que são chaves mestras do Banco de Dados de Documentos?
-	O que são chaves somente leitura do Banco de Dados de Documentos?
-	O que são os tokens de recurso do Banco de Dados de Documentos?
-	Como eu posso usar os usuários e permissões do Banco de Dados de Documentos para proteger o acesso aos dados que estão nele?

##<a id="Sub1"></a>Conceitos de controle de acesso do Banco de Dados de Documentos##

O Banco de Dados de Documentos conta com conceitos de primeira classe de controle de acesso a seus recursos. Neste tópico, os recursos do Banco de Dados de Documentos estão agrupados em duas categorias:

- Recursos administrativos
	- Conta
	- Banco de dados
	- Usuário
	- Permissão
- Recursos de aplicativos
	- Coleção
	- Documento
	- Anexo
	- Procedimento armazenado
	- Gatilho
	- Função definida pelo usuário

No contexto dessas duas categorias, o Banco de Dados de Documentos dá suporte a três tipos de personas de controle de acesso: administrador da conta, administrador somente leitura e usuário do banco de dados. Os direitos de cada identidade de controle de acesso são:
 
- Administrador da conta: acesso completo a todos os recursos (administrativos e de aplicativos) de uma determinada conta do Banco de Dados de Documentos.
- Administrador somente leitura: acesso somente leitura a todos os recursos (administrativos e de aplicativos) de uma determinada conta do Banco de Dados de Documentos. 
- Usuário do banco de dados: O recurso de usuário do Banco de Dados de Documentos associado a um conjunto específico de recursos de banco de dados do Banco de Dados de Documentos (como coleções, documentos, scripts). Pode haver um ou mais recursos de usuário associados a um determinado banco de dados, e cada recurso de usuário pode ter uma ou mais permissões associadas a ele.

Tendo em mente as categorias e recursos mencionados, o modelo de controle de acesso do Banco de Dados de Documentos define três tipos de constructos de acesso:

- Chaves mestras: Durante a criação de uma conta do Banco de Dados de Documentos, são criadas duas chaves mestras (primária e secundária). Essas chaves habilitam acesso administrativo total a todos os recursos da conta do Banco de Dados de Documentos.

![Ilustração das chaves mestras do Banco de Dados de Documentos](./media/documentdb-secure-access-to-data/masterkeys.png)

- Chaves somente leitura: Durante a criação de uma conta do Banco de Dados de Documentos, são criadas duas chaves somente leitura(primária e secundária). Essas chaves habilitam acesso somente leitura a todos os recursos da conta do Banco de Dados de Documentos.

![Ilustração das chaves somente leitura do Banco de Dados de Documentos](./media/documentdb-secure-access-to-data/readonlykeys.png)

- Tokens de recurso: Um token de recurso é associado a um recurso de permissão do Banco de Dados de Documentos e captura a relação entre o usuário de um banco de dados e a permissão que ele tem para acessar um recurso de aplicativo específico do Banco de Dados de Documentos (como uma coleção ou documento).

![Ilustração dos tokens de recurso do Banco de Dados de Documentos](./media/documentdb-secure-access-to-data/resourcekeys.png)

##<a id="Sub2"></a>Trabalhando com chaves mestras e somente leitura do Banco de Dados de Documentos ##
Como mencionado anteriormente, chaves mestras do Banco de Dados de Documentos fornecem acesso administrativo total a todos os recursos em uma conta do Banco de Dados de Documentos, enquanto chaves somente leitura habilitam acesso de leitura a todos os recursos dentro da conta. O trecho de código a seguir ilustra como usar o ponto de extremidade e a chave mestra de uma conta do Banco de Dados de Documentos para criar uma instância de DocumentClient e criar um novo banco de dados.

    //Read the DocumentDB endpointUrl and authorization keys from config.
    //These values are available from the Azure Management Portal on the DocumentDB Account Blade under "Keys".
    //NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.
    
	private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
    private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);
        
    client = new DocumentClient(new Uri(endpointUrl), authorizationKey);
    
	//Create Database
    Database database = await client.CreateDatabaseAsync(
        new Database
        {
            Id = databaseName
        });


##<a id="Sub3"></a>Visão geral dos tokens de recursos do Banco de Dados de Documentos ##
Você pode usar um token de recurso (criando usuários e permissões do Banco de Dados de Documentos) quando quiser fornecer acesso a recursos de sua conta do Banco de Dados de Documentos a um cliente que não é confiável para receber a chave mestra. Suas chaves mestras do Banco de Dados de Documentos incluem uma chave primária e uma chave secundária, que concedem acesso administrativo à sua conta e a todos os recursos que ela contém. A exposição de qualquer uma de suas chaves mestras torna sua conta vulnerável a um possível uso mal-intencionado ou negligente.

Da mesma forma, as chaves somente leitura do Banco de Dados de Documentos fornecem acesso de leitura a todos os recursos - exceto recursos de permissão, é claro – de uma conta do Banco de Dados de Documentos e não podem ser usadas para fornecer um acesso mais granular a recursos específicos dele.

O token de recurso do Banco de Dados de Documentos fornece uma alternativa segura que permite que os clientes leiam, gravem e excluam recursos da sua conta de acordo com as permissões que você conceder, e sem a necessidade de uma chave mestra ou de somente leitura.

Este é um padrão de design típico no qual tokens de recurso podem ser solicitados, gerados e fornecidos aos clientes:

1. Um serviço de camada intermediária é configurado para atender a um aplicativo móvel de compartilhamento de fotos do usuário.
2. O serviço de camada intermediária tem a chave mestra da conta do Banco de Dados de Documentos.
3. O aplicativo de fotos é instalado em dispositivos móveis de usuários finais. 
4. No logon, o aplicativo de fotos estabelece a identidade do usuário com o serviço de camada intermediária. Esse mecanismo de estabelecimento de identidade depende apenas do aplicativo.
5. Depois que a identidade é estabelecida, o serviço de camada intermediária solicita permissões com base na identidade.
6. O serviço de camada intermediária envia um token de recurso de volta para o aplicativo móvel.
7. O aplicativo móvel pode continuar usando o token de recurso para acessar diretamente recursos do Banco de Dados de Documentos com as permissões definidas pelo token e no intervalo permitido por ele. 
8. Quando o token de recurso expira, as solicitações seguintes recebem uma exceção 401 de não autorizado. Nesse ponto, o aplicativo móvel restabelece a identidade e solicita um novo token de recurso.

![Fluxo de trabalho dos tokens de recurso do Banco de Dados de Documentos](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

##<a id="Sub4"></a>Trabalhando com usuários e permissões do Banco de Dados de Documentos ##
Um recurso de usuário do Banco de Dados de Documentos é associado a um banco de dados do Banco de Dados de Documentos. Cada banco de dados pode conter nenhum ou mais usuários do Banco de Dados de Documentos. O trecho de código a seguir mostra como criar um recurso de usuário do Banco de Dados de Documentos.

	//Create a user.
    User docUser = new User
    {
        Id = "mobileuser"
    };

    docUser = await client.CreateUserAsync(database.SelfLink, docUser);

> [AZURE.NOTE]Cada usuário do Banco de Dados de Documentos tem uma propriedade PermissionsLink que pode ser usada para recuperar a lista de permissões associadas ao usuário.

Um recurso de permissão do Banco de Dados de Documentos é associado a um usuário do Banco de Dados de Documentos. Cada usuário pode conter nenhuma ou mais permissões do Banco de Dados de Documentos. Um recurso de permissão fornece acesso a um token de segurança de que o usuário precisa ao tentar acessar um recurso de aplicativo específico. Há dois níveis de acesso disponíveis que podem ser fornecidos por um recurso de permissão:

- Tudo: O usuário tem permissão total com relação ao recurso
- Leitura: O usuário pode apenas ler o conteúdo do recurso, mas não pode executar operações de gravação, atualização ou exclusão no recurso.


> [AZURE.NOTE]Para executar procedimentos armazenados no Banco de Dados de Documentos, o usuário precisa ter a permissão Tudo na coleção na qual o procedimento armazenado será executado.


O trecho de código a seguir mostra como criar um recurso de permissão, ler o token de recurso (token) do recurso de permissão e associar as permissões ao usuário criado anteriormente.

	//Create a permission.

    Permission docPermission = new Permission
    {
        PermissionMode = PermissionMode.Read,
        ResourceLink = documentCollection.SelfLink,
        Id = "readperm"
    };
            
	docPermission = await client.CreatePermissionAsync(docUser.SelfLink, docPermission);
	Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);

Para obter facilmente todos os recursos de permissão associados a um determinado usuário, o Banco de Dados de Documentos disponibiliza um feed de permissões para cada objeto de usuário. O trecho de código a seguir mostra como recuperar a permissão associada ao usuário criado acima, construir uma lista de permissões e instanciar um novo DocumentClient em nome do usuário.

	//Read a permission feed.
    FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(docUser.SelfLink);
	
	List<Permission> permList = new List<Permission>();
    
	foreach (Permission perm in permFeed)
    {
        permList.Add(perm);
    }
            
    DocumentClient userClient = new DocumentClient(new Uri(endpointUrl),permList);

> [AZURE.TIP]Tokens de recurso têm um intervalo de tempo válido padrão de uma hora. O tempo de vida do token, no entanto, pode ser especificado explicitamente, até um máximo de cinco horas.

##<a name="NextSteps"></a>Próximas etapas

- Para saber mais sobre o Banco de Dados de Documentos, clique [aqui](http://azure.com/docdb).
- Para saber sobre o gerenciamento de chaves mestras e somente leitura, clique [aqui](documentdb-manage-account.md).
- Para saber como criar tokens de autorização do Banco de Dados de Documentos, clique [aqui](https://msdn.microsoft.com/library/azure/dn783368.aspx)
 

<!---HONumber=Nov15_HO1-->