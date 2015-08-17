<properties 
	pageTitle="Criar e gerenciar Conexões Híbridas | Azure" 
	description="Saiba como criar uma conexão híbrida, gerenciar a conexão e instalar o Gerenciador de conexões híbridas. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="mandia"/>


# Criar e gerenciar Conexões Híbridas


## Visão geral das etapas
1. Crie uma Conexão Híbrida inserindo o nome do host ou o endereço IP do recurso local em sua rede privada.
2. Conecte seus Aplicativos Web do Azure ou Aplicativos Móveis do Azure à Conexão Híbrida.
3. Instale o Gerenciador de Conexão Híbrida em seu recurso local e conecte-se à Conexão Híbrida específica. O portal do Azure oferece uma experiência de apenas um clique para instalar e conectar.
4. Gerenciar Conexões Híbridas e suas chaves de conexão.

Este tópico lista estas etapas.


## <a name="CreateHybridConnection"></a>Criar uma Conexão Híbrida

É possível criar uma Conexão Híbrida no Portal de Gerenciamento do Azure usando Aplicativos Web **ou** os Serviços BizTalk.

**Para criar Conexões Híbridas usando Aplicativos Web**, consulte [Conectar os Aplicativos Web do Azure a um recurso local](../web-sites-hybrid-connection-get-started.md).

**Para criar Conexões Híbridas em Serviços do BizTalk**:

1. Entre no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços do BizTalk** no painel de navegação à esquerda e selecione o seu Serviço do BizTalk. <br/>Se você não tiver um Serviço BizTalk, você poderá [Criar um Serviço BizTalk](biztalk-provision-services.md).
3. Selecione a guia Conexões Híbridas: <br/> ![Guia de Conexões Híbridas][HybridConnectionTab]

4. Selecione **Criar uma Conexão Híbrida** ou selecione o botão **ADD** na barra de tarefas. Insira o seguinte:

	Propriedade | Descrição
--- | ---
Nome | O nome da Conexão Híbrida precisa ser único e não pode ser igual ao nome do Serviço do BizTalk. Você pode inserir qualquer nome, mas seja específico quanto à sua finalidade. Os exemplos incluem:<br/><br/>Pagamento*SQLServer*<br/>ListadeSuprimentos*SharepointServer*<br/>Clientes*OracleServer*
Nome de host | Insira o nome de host totalmente qualificado, somente o nome de host ou, então, o endereço IPv4 do recurso local. Os exemplos incluem:<br/><br/>mySQLServer<br/>*mySQLServer*.*Domínio*.corp.*suaEmpresa*.com<br/>*meuServidorSharePointHTTP*<br/>*meuServidorSharePointHTTP*.*suaEmpresa*.com<br/>10.100.10.10
Porta | Insira o número da porta no recurso local. Por exemplo, se você estiver usando Aplicativos Web, insira a porta 80 ou 443. Se você estiver usando o SQL Server, insira a porta 1433.

5. Clique na marca de seleção para concluir a configuração.

#### Adicional

- Conexões Híbridas adicionais podem ser criadas. Consulte os [Serviços BizTalk: gráfico de edições](biztalk-editions-feature-chart.md) para obter o número de conexões permitidas. 
- Cada Conexão Híbrida é criada com um par de cadeias de conexão: chaves de aplicativo que ENVIAM e chaves locais que ESCUTAM. Cada par possui uma chave primária e outra secundária. 


## <a name="LinkWebSite"></a>Conectar seus Aplicativos Web do Azure ou Aplicativos Móveis do Azure

Para conectar os Aplicativos Web do Azure a uma Conexão Híbrida existente, selecione **usar uma Conexão Híbrida existente** na folha Conexões Híbridas. Consulte [Conectar Aplicativos Web do Azure a um recurso local](../web-sites-hybrid-connection-get-started.md).

Para conectar os Aplicativos Móveis do Azure a uma Conexão Híbrida existente, selecione **adicionar conexão híbrida** ao alterar ou criar um Serviço Móvel. Consulte [Conexões Híbridas e Serviços Móveis do Azure](../mobile-services-dotnet-backend-hybrid-connections-get-started.md).


## <a name="InstallHCM"></a>Instalar o Gerenciador de Conexões Híbridas local

Após uma Conexão Híbrida ser criada, instale o Gerenciador de Conexões Híbridas no recurso local. É possível fazer o download por meio dos Aplicativos Web do Azure ou por meio de seu Serviço BizTalk. Etapas dos Serviços BizTalk:

1. Entre no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços do BizTalk** no painel de navegação à esquerda e selecione o seu Serviço do BizTalk. 
3. Selecione a guia **Conexões Híbridas**: <br/> ![Guia de Conexões Híbridas][HybridConnectionTab]
4. Na barra de tarefas, selecione **Configuração Local**: <br/> ![Configuração Local][HCOnPremSetup]
5. Selecione **Instalar e Configurar** para executar ou baixar o Gerenciador de Conexões Híbridas no sistema local. 
6. Selecione a marca de seleção para iniciar a instalação. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
-->

#### Adicional
- Híbridas adicionais suportam recursos locais instalados nos sistemas operacionais a seguir:

	- Windows Server 2008 R2
	- Windows Server 2012
	- Windows Server 2012 R2


- Após você instalar o Gerenciador de Conexões Híbridas, ocorre o seguinte:

	- A Conexão Híbrida hospedada no Azure é configurada automaticamente para utilizar a cadeia de conexão primária do aplicativo. 
	- O recurso local é configurado automaticamente para utilizar a cadeia de conexão primária local.

- O Gerenciador de Conexões Híbridas precisa utilizar uma cadeia de conexão local válida para autorização. Os Aplicativos Web ou Aplicativos Móveis do Azure precisam utilizar uma cadeia de conexão ao aplicativo válida para autorização.
- Você pode dimensionar conexões híbridas instalando outra instância do Gerenciador de conexões híbridas em outro servidor. Configure o ouvinte local para usar o mesmo endereço como o primeiro ouvinte local. Nessa situação, o tráfego é distribuído aleatoriamente (round robin) entre os ouvintes ativos locais. 


## <a name="ManageHybridConnection"></a>Gerenciar Conexões Híbridas
Para gerenciar suas Conexões Híbridas, você pode:

- Utilizar o portal do Azure e ir até seu Serviço do BizTalk. 
- Usar [APIs REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### Copiar/regenerar as cadeias de conexão híbrida

1. Entre no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços do BizTalk** no painel de navegação à esquerda e selecione o seu Serviço do BizTalk. 
3. Selecione a guia **Conexões Híbridas**: <br/> ![Guia de Conexões Híbridas][HybridConnectionTab]
4. Selecione a Conexão Híbrida. Na barra de tarefas, selecione **Gerenciar Conexão**: <br/> ![Gerenciar opções][HCManageConnection] <br/> **Gerenciar Conexão** lista as cadeias de conexão Local e do Aplicativo. É possível copiar as cadeias de conexão ou regenerar a chave de acesso usada na cadeia de conexão. <br/> <br/> **Se você selecionar Regenerar**, a Chave de Acesso compartilhada usada na Cadeia de Conexão será alterada. Faça o seguinte:
- No Portal de Gerenciamento do Azure, selecione **Sincronizar Chaves** no aplicativo do Azure.
- Execute novamente a **Configuração Local**. Ao executar novamente a Configuração Local, o recurso local é configurado automaticamente para usar a cadeia de conexão primária atualizada.


#### Use a Política de Grupo para controlar os recursos locais utilizados por uma Conexão Híbrida

1. Baixe os [modelos administrativos do Gerenciador de Conexões Híbridas](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extraia os arquivos.
3. No computador que modifica a política de grupo, faça o seguinte: 

	- Copie os arquivos .ADMX para a pasta *%WINROOT%\\PolicyDefinitions*.
	- Copie os arquivos .ADML para a pasta *%WINROOT%\\PolicyDefinitions\\pt-br*.

Após tê-los copiado, você pode usar o Editor de políticas de grupo para mudar a política.




## Avançar

[Conectar Aplicativos Web do Azure a um recurso local](../web-sites-hybrid-connection-get-started.md)<br/> [Conectar ao SQL Server local a partir de Aplicativos Web do Azure](../web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/> [Serviços Móveis do Azure e Conexões Híbridas](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)<br/> [Visão geral de Conexões Híbridas](integration-hybrid-connection-overview.md)


## Consulte também

[API REST para gerenciar Serviços BizTalk no Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)<br/> [Serviços BizTalk: gráfico de edições](biztalk-editions-feature-chart.md)<br/> [Criar um Serviço BizTalk usando o Portal de Gerenciamento do Azure](biztalk-provision-services.md)<br/> [Serviços BizTalk: guias Painel, Monitor e Escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png

<!---HONumber=August15_HO6-->