<properties
	pageTitle="Adicione ou crie uma nova API e conceda permissões de usuários do PowerApps | Microsoft Azure"
	description="Adicione, crie e configure uma nova API, conexão ou o perfil de conexão e conceda permissões e direitos de acesso do usuário no portal do Azure"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="litran"/>


# Adicionar uma nova API, adicionar uma conexão e fornecer acesso aos usuários

Existem APIs em um [ambiente de serviço de aplicativo](powerapps-get-started-azure-portal.md). As APIs podem ser criadas usando as APIs disponíveis para PowerApps, de aplicativos de API hospedados no seu ambiente de serviço de aplicativo ou do Swagger 2.0. Há muitas APIs disponíveis criadas previamente que podem ser facilmente adicionadas em seu PowerApps. Você também pode carregar sua própria API no formato JSON ou Swagger 2.0.

Este tópico:

- Relaciona as etapas para adicionar uma API do PowerApps e dar permissões aos usuários em sua empresa para usarem a API, além de alternar suas propriedades.
- Relaciona as etapas para adicionar uma conexão à sua API e dar permissões aos usuários em sua empresa para usarem a conexão.


#### Pré-requisitos para iniciar

- Habilite o [PowerApps na sua assinatura do Azure](powerapps-get-started-azure-portal.md).
- Crie um [Ambiente de Serviço de aplicativo](powerapps-get-started-azure-portal.md).
- Crie uma API usando qualquer um dos seguintes métodos:  
	- Crie uma [API gerenciada pela Microsoft ou pela TI](powerapps-register-from-available-apis.md).
	- Crie uma API hospedada em [seu ambiente de serviço de aplicativo](powerapps-register-api-hosted-in-app-service.md).
	- Crie usando uma [definição de API do Swagger 2.0](powerapps-register-existing-api-from-api-definition.md).


## Forneça acesso à API aos usuários
Agora que a API foi criada e adicionada ao seu ambiente de serviço de aplicativo, é hora de dar aos usuários em sua empresa as permissões para usá-la.

1. No PowerApps, selecione **Gerenciar APIs** e, então, selecione sua API. Por exemplo, se você tiver criado uma API *MS Power BI*, selecione-a para abrir sua folha. Selecione o **acesso de usuário da API**: ![][1]  

2. Selecione **Adicionar** para adicionar usuários e selecione os corretos. Quando terminar, selecione **Adicionar** para salvar suas alterações. A contagem de usuários ou grupos aumenta na janela **Acesso de usuário da API**.


## Adicione uma nova conexão à sua API
A próxima etapa é criar a “conexão” para sua API, que é uma variante como uma cadeia de conexão. Isso permite que a API seja conectada com êxito ao sistema de “back-end”. Para visualização pública do PowerApps Enterprise, somente a conexão do SQL Server pode ser adicionada e configurada. Mais estão sendo adicionados.

- [Crie uma conexão do SQL Server](powerapps-create-api-sqlserver.md)

## Forneça aos usuários o acesso de tempo de execução para a conexão
Agora, dê aos usuários em sua empresa as permissões para usar a conexão.

1. Abra sua API, selecione **Conexões** e selecione sua conexão específica. Isso abre uma nova folha que relaciona o nome da conexão na parte superior. 
2. Nesta nova folha, selecione **Acesso de usuário de conexão**. No exemplo a seguir, a conexão **Túnel híbrido** é selecionada. A nova folha será aberta e é nela que você seleciona o **Acesso de usuário de conexão**: ![][2]
  
3. Em **Acesso de usuário de conexão**, selecione **Adicionar** e, em seguida, selecione a permissão que você deseja conceder: ![][3]
  
4. Adicione seu usuário ou grupo. Selecione **Adicionar** para salvar suas alterações.

Agora que os usuários têm permissões para a API e sua conexão, eles podem adicionar essas APIs em seus aplicativos criados no PowerApps. Especificamente:

- Os usuários podem ver a API relacionada nas **Conexões disponíveis** no PowerApps.
- Os usuários podem ver a conexão em **Minhas conexões** no PowerApps.


## Excluir uma API
Você também pode excluir uma API adicionada anteriormente. No PowerApps, selecione **Gerenciar APIs**, selecione a API e, então, **Excluir**: ![][4]


## Resumo e próximas etapas
Neste tópico, você:

- Adicionou uma API e concedeu aos usuários em sua empresa os direitos para usá-la. Você também pode usar estas etapas para gerenciar o acesso de tempo de execução a qualquer momento. Por exemplo, se o userA sair da sua empresa, você pode usar o portal do Azure para remover as permissões do usuário facilmente. Mesmo cenário se um UserB entrar em sua empresa.
- Adicionou uma conexão (que é semelhante a uma cadeia de conexão). Esta etapa permite que a API hospedada no Azure seja conectada ao seu sistema, como um SQL Server local. Você também deu aos usuários em sua empresa as permissões para usar a conexão. 
- Você trabalhou com folhas diferentes, dependendo da tarefa. Para adicionar uma conexão, você abre a API e usa sua folha. Para conceder acesso de usuário, você abre a conexão ou a API, dependendo do que você está concedendo acesso. 
- Você também pode excluir qualquer uma das APIs que criar em seu ambiente de serviço de aplicativo.

Em seguida, você pode [gerenciar e monitorar seu PowerApps](powerapps-manage-monitor-usage.md).

[1]: ./media/powerapps-manage-api-connection-user-access/apiuseraccess.png
[2]: ./media/powerapps-manage-api-connection-user-access/connectionuseraccess.png
[3]: ./media/powerapps-manage-api-connection-user-access/selectpermission.png
[4]: ./media/powerapps-manage-api-connection-user-access/deleteapi.png

<!---HONumber=AcomDC_0309_2016-->