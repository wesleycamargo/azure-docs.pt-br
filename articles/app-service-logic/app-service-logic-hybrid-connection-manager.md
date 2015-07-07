<properties 
	pageTitle="Usando o Gerenciador de Conexão Híbrida para Serviço de Aplicativo do Azure" 
	description="Instalar e configurar o Gerenciador de Conexão Híbrida no Serviço de Aplicativo do Azure; arquitetura de microsserviços" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2015" 
	ms.author="mandia"/>

# Usando o Gerenciador de Conexão Híbrida no Serviço de Aplicativo do Azure
Alguns conectores podem se conectar a um sistema local, como SAP, SQL Server, SharePoint e assim por diante. Para usar um sistema local, O Serviço de Aplicativo do Azure usa o Gerenciador de Conexão Híbrida.

O HCM (Gerenciador de Conexão Híbrida) é um instalador com clique único, instalado em um servidor IIS em sua rede, por trás do firewall. Usando uma retransmissão do Barramento de Serviço do Azure, o HCM autentica o sistema local com o conector no Azure.

> [AZURE.NOTE]O Gerenciador de Conexão Híbrida será necessário apenas se você estiver se conectando a um recurso local por trás do firewall. Se não estiver se conectando a um sistema local, você não precisará do Gerenciador de Conexão Híbrida.

Para começar, você precisa do seguinte:

- Cadeia de conexão SAS do namespace de retransmissão do Barramento de Serviço do Azure. Consulte [Preços do Barramento de Serviço](http://azure.microsoft.com/pricing/details/service-bus/) para determinar qual camada inclui retransmissões.
- Informações de entrada do sistema local, incluindo nome de usuário e senha. Por exemplo, se estiver se conectando a um SQL Server local, você precisará da conta e senha de logon do SQL Server.
- Informações do servidor local, incluindo número da porta e nome do servidor. Por exemplo, se estiver se conectando a um SQL Server local, você precisará do número da porta TCP e nome do SQL Server.

## Obter a cadeia de conexão do Barramento de Serviço

No portal do Azure, copie a cadeia de conexão SAS da raiz do Barramento de Serviço. Essa cadeia de conexão conecta o conector do Azure ao seu sistema local.

1. No [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885), selecione o namespace do Barramento de Serviço e selecione **Informações de Conexão**:

	![][SB_ConnectInfo]

2. Copie a cadeia de conexão SAS:

	![][SB_SAS]

## Instalar o Gerenciador de Conexão Híbrida

1. Na visualização do [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), selecione o conector que você criou. Para abri-lo, você pode selecionar **Procurar**, selecionar **Aplicativos de API** e selecionar o conector ou aplicativo de API. <br/><br/> Em **Conexão híbrida**, a configuração está **incompleta**: <br/> ![][2] 

2. Selecione **Conexão híbrida**. A cadeia de conexão do Barramento de Serviço inserida anteriormente é listada.
3. Copie a **cadeia de configuração principal**: <br/> ![][PrimaryConfigString]

4. Em **Gerenciador de Conexão Híbrida Local**, você pode baixar o Gerenciador de Conexão Híbrida ou instalá-lo diretamente a partir do portal. <br/><br/> Para instalar diretamente a partir do portal, vá para o servidor IIS local, navegue até o portal e selecione **Baixar e Configurar**. <br/><br/> Para fazer o download do Gerenciador de Conexão Híbrida, vá para o servidor IIS local e acesse o **aplicativo ClickOnce** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). A instalação inicia automaticamente para que você possa executá-la.

5. Na janela **Configuração de Ouvinte**, digite a **cadeia de configuração principal** colada anteriormente (na etapa 3) e selecione **Instalar**.

Quando a instalação estiver concluída, você verá isto: <br/> ![][3]

Agora, quando você navegar para o conector novamente, o status da conexão híbrida será **Conectado**. Pode ser necessário fechar o conector e reabri-lo: <br/> ![][4]

> [AZURE.NOTE]Para alternar para a cadeia de conexão secundária, execute novamente a configuração de conexão híbrida e insira a **cadeia de configuração secundária**.


## Portas TCP e segurança

Quando você cria uma conexão híbrida, um site é criado no servidor IIS local. O servidor IIS pode estar em um DMZ. Os requisitos de porta TCP no servidor IIS incluem:

Porta TCP | Porque
--- | ---
 | Nenhuma porta TCP de entrada é necessária.
9350 - 9354 | Essas portas são usadas para a transmissão de dados. O gerenciador de retransmissão do Barramento de Serviço investiga a porta 9350 para determinar se a conectividade TCP está disponível. Quando está disponível, ele pressupõe que a porta 9352 também está. O tráfego de dados passa pela porta 9352. <br/><br/>Permita conexões de saída para essas portas.
5671 | Quando a porta 9352 é usada para tráfego de dados, a porta 5671 é usada como canal de controle. <br/><br/>Permita conexões de saída para essa porta. 
80, 443 | Se as portas 9352 e 5671 não forem utilizáveis, *então* as portas 80 e 443 serão as portas de fallback usadas para transmissão de dados e o canal de controle.<br/><br/>Permita conexões de saída para essas portas.
Porta do sistema local | No sistema local, abra a porta usada pelo sistema. Por exemplo, o SQL Server normalmente usa a porta 1433. Abra essa porta TCP.

[Hospedagem atrás de um firewall com o Barramento de Serviço](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## Solucionar problemas

![][HCMFlow]

### Solução de problemas locais

1. No servidor IIS, confirme se a função web do IIS está instalada e se todos os serviços do IIS foram iniciados.
2. No servidor IIS, confirme se o Gerenciador de Conexão Híbrida está instalado e em execução:
 - No Gerenciador do IIS (inetmgr), o site ***MicrosoftAzureBizTalkHybridListener*** deve estar listado e em execução. 
 - Esse site usa o ***HybridListenerAppPool*** executado como a conta interna de usuário local *NetworkService*. Esse AppPool também deve ser iniciado.
3. No servidor IIS, confirme se o conector está instalado e em execução: 
 - Um site é criado para o conector do Serviço de Aplicativo. Por exemplo, se você criar um conector SQL, haverá um site ***MicrosoftSqlConnector_nnn***. No Gerenciador do IIS (inetmgr), confirme se esse site está listado e iniciado. 
 - Esse site usa seu próprio pool de aplicativos do IIS, denominado ***HybridAppPoolnnn***. Esse AppPool é executado como a conta interna de usuário local *NetworkService*. Esse site e o AppPool devem ser iniciados. 
 - Procure o conector local. Por exemplo, se o site do seu conector usar a porta 6569, navegue até http://localhost:6569. Como não há documento padrão configurado, espera-se um `HTTP Error 403.14 - Forbidden error`.
4. Em seu firewall, confirme se as portas TCP listadas neste tópico estão abertas.
5. Examine o sistema de origem ou de destino:
 - Alguns sistemas locais exigem arquivos de dependência adicionais. Por exemplo, se você estiver se conectando ao SAP local, alguns arquivos adicionais do SAP deverão ser instalados no servidor IIS.
 - Verifique a conectividade com o sistema usando a conta de logon. Por exemplo, a porta TCP usada pelo sistema deve estar aberta, como a porta 1433 para o SQL Server. A conta de logon que você inseriu no portal do Azure deve ter acesso ao sistema.
6. No servidor IIS, verifique os erros dos logs de eventos. 
7. Limpe e reinstale o Gerenciador de Conexão Híbrida: 
 - No IIS, exclua manualmente o site do conector e seu pool de aplicativos. 
 - Execute novamente o Gerenciador de Conexão Híbrida e confirme se está inserindo a **cadeia de configuração principal** correta para o conector.



### No portal do Azure

1. Confirme se o namespace do Barramento de Serviço está em estado **Ativo**.
2. Ao criar o conector, insira a cadeia de conexão SAS do Barramento de Serviço. Não insira a cadeia de conexão do ACS.


## Perguntas frequentes

**Pergunta**: há dois Gerenciadores de Conexões Híbridas. Qual é a diferença?<br/> **Resposta**: a tecnologia de [conexões híbridas](../integration-hybrid-connection-overview.md) é usada principalmente por aplicativos Web (antigos sites) e aplicativos móveis (antigos serviços móveis) para estabelecer conexão local. Esse Gerenciador de Conexões Híbridas é sua própria [instalação](../integration-hybrid-connection-create-manage.md) e usa um serviço BizTalk do Azure (em segundo plano). Ele dá suporte apenas aos protocolos TCP e HTTP.

Com os conectores do Serviço de Aplicativo do Azure, também temos um Gerenciador de Conexão Híbrida. Esse Gerenciador de Conexão Híbrida *não* usar um serviço BizTalk do Azure (em segundo plano) e dá suporte a mais protocolos além de TCP e HTTP. Consulte a [Lista de aplicativos de API e conectores](app-service-logic-connectors-list.md).

Ambos usam o Barramento de Serviço do Azure para se conectar ao sistema local.

**Pergunta**: quando eu crio um aplicativo de API personalizado, posso usar o Gerenciador de Conexão Híbrida do Serviço de Aplicativo para estabelecer conexão local? <br/> **Resposta**: não no sentido tradicional. Você pode usar um conector interno e configurar o Gerenciador de Conexão Híbrida do Serviço de Aplicativo para se conectar ao sistema local. Em seguida, use esse conector com seu aplicativo de API personalizado, possivelmente usando um aplicativo lógico. No momento, você não pode desenvolver ou criar seu próprio aplicativo de API híbrido (como o conector SQL ou conector de arquivo).

Se sua API personalizada usar uma porta TCP ou HTTP, você poderá usar [conexões híbridas](../integration-hybrid-connection-overview.md) e o Gerenciador de Conexão Híbrida. Nesse cenário, um serviço BizTalk do Azure é usado. [Conectar-se a um SQL Server local por meio de um aplicativo Web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) pode ajudar.


## Leia mais

[Monitorar seus aplicativos lógicos](app-service-logic-monitor-your-logic-apps.md)<br/> [Preços do Barramento de Serviço](http://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 

<!---HONumber=62-->