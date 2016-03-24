<properties
	pageTitle="Adicione a API do SQL Server no PowerApps Enterprise | Microsoft Azure"
	description="Crie ou configure uma nova API do SQL Server no ambiente de serviço de aplicativo da sua organização e adicione conexão aos dados locais"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>


<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>


# Crie uma nova API do SQL Server no PowerApps Enterprise

Adicione a API do SQL Server ao ambiente de serviço de aplicativo (locatário) da sua organização.

## Crie a API no portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), entre com sua conta de trabalho. Por exemplo, entre com *Seunomedeusuário*@* SuaEmpresa*.com. Quando fizer isso, entrará automaticamente na assinatura de sua empresa. 
2. Selecione **Procurar** na barra de tarefas: ![][14]  
3. Na lista, você pode usar a barra de rolagem para localizar o PowerApps ou digitar *powerapps*: ![][15]  
4. Em **PowerApps**, selecione **Gerenciar APIs**.
5. Em **Gerenciar APIs**, selecione **Adicionar** para adicionar a nova API.
6. Insira um **nome** descritivo para sua API. Por exemplo, você está adicionando a API de demonstração do SQL Server, você pode chamá-la de *SQLServerDemo*.  	
7. Em **Fonte**, selecione as **APIs disponíveis** para selecionar as APIs criadas previamente e selecione **SQL Server**. 
8. Selecione **OK** para concluir as etapas.

Quando terminar, uma nova API do SQL Server é adicionada ao seu ambiente de serviço de aplicativo.

## Configurar a conectividade para o SQL Server local

Você pode se conectar ao SQL Server local. Para estabelecer essa conectividade híbrida, você pode aproveitar as soluções de rede híbridas existentes no Azure, incluindo:

- [Rota Expressa](../expressroute/expressroute-introduction.md)
- [VPN site a site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Conectividade ponto a site](../vpn-gateway/vpn-gateway-point-to-site-create.md)  

	> [AZURE.NOTE]  Cada ambiente de serviço de aplicativo tem uma rede virtual associada a ele. Você pode estabelecer essa conectividade de rede para essa rede virtual.  
- [Conexões Híbridas](../app-service-web/web-sites-hybrid-connection-get-started.md)  

	> [AZURE.NOTE]  Cada API registrada em seu ambiente de serviço de aplicativo tem um aplicativo Web correspondente. Você pode estabelecer conexões híbridas deste aplicativo Web exatamente como faria com qualquer outro aplicativo Web.
	
O exemplo a seguir mostra como criar uma conexão híbrida:

1. Selecione a API do SQL Server recém-criado e selecione o grupo de recursos. Neste exemplo, selecione a API chamada *sqlconnectordemo*, e selecione o grupo de recursos *DedicatedAses*: ![Grupo de recursos](./media/powerapps-create-api-sqlserver/sqlapi.png)

2.  Selecione o bloco **Recursos** e, em seguida, selecione o aplicativo Web com o mesmo nome como sua API do SQL Server. Neste exemplo, selecione *sqlconnectordemo*: ![Aplicativo Web do SQL](./media/powerapps-create-api-sqlserver/sqlwebapp.png)

3.  Em **Configurações**, selecione **Rede**. Selecione **Configurar seus pontos de extremidade de conexão híbrida** e, em seguida, execute [estas instruções](../app-service-web/web-sites-hybrid-connection-get-started.md) para criar a conexão híbrida: ![Rede](./media/powerapps-create-api-sqlserver/network.png)

Uma vez que sua conexão híbrida for criada e conectada, você habilitou a conexão com o servidor local. Em seguida, crie a conexão com seus dados e forneça acesso aos usuários: ![Conexão híbrida](./media/powerapps-create-api-sqlserver/hybridconn.png)

## Criar conexão para a API do SQL Server

1. No portal do Azure, abra o PowerApps e selecione a opção **Gerenciar APIs**. Uma lista das APIs configuradas será exibida: ![](./media/powerapps-create-api-sqlserver/apilist.png)

2. Selecione a API que você deseja. Neste exemplo, selecione a opção **SQLServerDemo** e, em seguida, **Conexões**.

3. Na seção Conexões, selecione **Adicionar conexão**: ![](./media/powerapps-create-api-sqlserver/addconnection.png)

4. Insira um nome para a conexão e, em seguida, insira a cadeia de conexão. A inserção da cadeia de conexão requer que você conheça algumas propriedades específicas sobre o serviço em que está se conectando. Por exemplo, se estiver se conectando ao SQL Server local, então você precisará saber o nome de usuário, senha e outras propriedades necessárias para fazer a conexão com êxito.

5. Selecione **Adicionar** para salvar suas alterações.

## Resumo e próximas etapas
Neste tópico, você adicionou a API do SQL Server para se conectar ao SQL Server local. Em seguida, forneça aos usuários acesso à API para que ela possa ser adicionada aos seus aplicativos:

[Adicione uma conexão e forneça acesso aos usuários](powerapps-manage-api-connection-user-access.md)


[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_0309_2016-->