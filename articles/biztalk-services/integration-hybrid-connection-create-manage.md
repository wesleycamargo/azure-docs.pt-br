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
	ms.date="02/17/2015" 
	ms.author="mandia"/>

#Criar e gerenciar Conexões Híbridas

Este tópico lista as etapas para criar e gerenciar Conexões Híbridas do Azure. 

As etapas para conectar-se a um servidor local incluem:

1. [Criar uma Conexão Híbrida](#CreateHybridConnection) especificando o nome do host ou o endereço IP do recurso local em sua rede privada.

2.	[Conecte seu site do Azure ou serviço móvel do Azure](#LinkWebSite) à Conexão Híbrida.

3. [Instale o Gerenciador de Conexão Híbrida](#InstallHCM) em seu recurso local e conecte-se à Conexão Híbrida específica. O portal do Azure oferece uma experiência de apenas um clique para instalar e conectar.

4. [Gerenciar Conexões Híbridas](#ManageHybridConnection) e suas chaves de conexão.


##<a name="CreateHybridConnection"></a>Criar uma Conexão Híbrida

Uma Conexão Híbrida pode ser criada no Portal de Gerenciamento do Azure usando sites **ou** usando Serviços BizTalk. 

**Para criar Conexões Híbridas utilizando sites**, consulte [Conectar um site do Azure a um recurso local](http://go.microsoft.com/fwlink/p/?LinkId=397538).

**Para criar Conexões Híbridas em Serviços BizTalk**:

1. Entre no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços BizTalk** no painel de navegação à esquerda e selecione o seu Serviço BizTalk. 
<br/>Se você não tiver um Serviço do BizTalk existente, você pode [Criar um Serviço do BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329870).
3. Selecione a guia Conexões Híbridas:
<br/>
![Hybrid Connections Tab][HybridConnectionTab]

4. Selecione **Criar uma conexão híbrida** ou selecione o botão **ADD** na barra de tarefas. Insira o seguinte:

	<table border="1">
    <tr>
       <td><strong>Name</strong></td>
        <td>O nome da Conexão Híbrida precisa ser único e não pode ser igual ao nome do Serviço do BizTalk. Você pode inserir qualquer nome, mas seja específico quanto à sua finalidade. Os exemplos incluem:<br/><br/>
		Payroll<em>SQLServer</em><br/>
		SupplyList<em>SharepointServer</em><br/>
		Customers<em>OracleServer</em>
        </td>
    </tr>
    <tr>
        <td><strong>Nome de host</strong></td>
        <td>Insira o nome de host totalmente qualificado, somente o nome de host ou, então, o endereço IPv4 do recurso local. Os exemplos incluem:
        <br/><br/>
<em>mySQLServer</em>
<br/>
<em>mySQLServer</em>.<em>Domain</em>.corp.<em>yourCompany</em>.com
<br/>
<em>ServidormyHTTPSharePoint</em>
<br/>
<em>myHTTPSharePointServer</em>.<em>yourCompany</em>.com
<br/>
10.100.10.10
       </td>
    </tr>
	<tr>
        <td><strong>Port</strong></td>
        <td>Insira o número da porta no recurso local. Por exemplo, se você estiver usando um site, insira a porta 80 ou 443. Se você estiver usando o SQL Server, insira a porta 1433.</td>
	</tr>
	</table>


5. Selecione a marca de seleção. 

####Conexões

- Conexões Híbridas adicionais podem ser criadas. Consulte os [Serviços do BizTalk: Tabela de edições](http://go.microsoft.com/fwlink/p/?LinkID=302279) para o número de conexões permitidas. 
- Cada Conexão Híbrida é criada com um par de cadeias de conexão: Chaves de aplicativo que ENVIAM e chaves locais que ESCUTAM. Cada par possui uma chave primária e outra secundária. 


##<a name="LinkWebSite"></a>Conectar seu site do Azure ou serviço móvel do Azure

Para conectar o site do Azure a uma Conexão Híbrida existente, selecione **usar uma Conexão Híbrida existente** na lâmina Conexões Híbridas. Consulte [Conectar um site do Azure a um recurso local](http://go.microsoft.com/fwlink/p/?LinkId=397538).

Para conectar o Serviço Móvel do Azure a uma Conexão Híbrida existente, selecione **Incluir Conexão Híbrida** ao alterar ou criar um Serviço Móvel. Consulte [Conexões Híbridas e Serviços Móveis do Azure](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started).


##<a name="InstallHCM"></a>Instalar o Gerenciador de Conexões Híbridas local

Após uma Conexão Híbrida ser criada, instale o Gerenciador de Conexões Híbridas no recurso local. Seu download pode ser feito por meio do site do Azure ou por meio de seu Serviço do BizTalk. Etapas dos Serviços BizTalk: 

1. Entre no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços BizTalk** no painel de navegação à esquerda e selecione o seu Serviço BizTalk. 
3. Selecione a guia **Conexões Híbridas**:
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. Na barra de tarefas, selecione **Configuração Local**:
<br/>
![On-Premises Setup][HCOnPremSetup]
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

#### Conexões
- Híbridas adicionais suportam recursos locais instalados nos sistemas operacionais a seguir:

	- Windows Server 2008 R2
	- Windows Server 2012
	- Windows Server 2012 R2


- Após você instalar o Gerenciador de Conexões Híbridas, ocorre o seguinte: 

	- A Conexão Híbrida hospedada no Azure é configurada automaticamente para utilizar a cadeia de conexão primária do aplicativo. 
	- O recurso local é configurado automaticamente para utilizar a cadeia de conexão primária local.

- O Gerenciador de Conexões Híbridas precisa utilizar uma cadeia de conexão local válida para autorização. O Serviço Móvel ou o site do Azure precisa utilizar uma cadeia de conexão ao aplicativo válida para autorização.


##<a name="ManageHybridConnection"></a>Gerenciar Conexões Híbridas
Para gerenciar suas Conexões Híbridas, você pode:

- Utilizar o portal do Azure e ir até seu Serviço do BizTalk. 
- Utilizar [APIs REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

####Copiar/regenerar as cadeias de conexão híbrida

1. Entre no [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços BizTalk** no painel de navegação à esquerda e selecione o seu Serviço BizTalk. 
3. Selecione a guia **Conexões Híbridas**:
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. Selecione a Conexão Híbrida. Na barra de tarefas, selecione **Gerenciar Conexão**:
<br/>
![Manage Options][HCManageConnection]
<br/>
**Gerenciar Conexão** lista as cadeias de conexão do aplicativo e local. É possível copiar as cadeias de conexão ou regenerar a chave de acesso usada na cadeia de conexão. 
<br/>
<br/>
**Se você selecionar Regenerar**, a chave de acesso compartilhado usada dentro da cadeia de conexão será alterada. Faça o seguinte:
- No Portal de Gerenciamento do Azure, selecione **Sincronizar Chaves** no aplicativo do Azure.
- Execute novamente a **Configuração Local**. Ao executar novamente a Configuração Local, o recurso local é configurado automaticamente para usar a cadeia de conexão primária atualizada.


####Use a Política de Grupo para controlar os recursos locais utilizados por uma Conexão Híbrida

1. Baixe os modelos administrativos do Gerenciador de Conexões Híbridas.
2. Extraia os arquivos.
3. No computador que modifica a política de grupo, faça o seguinte: 
- Copie os arquivos .ADMX para a pasta *%WINROOT%\PolicyDefinitions*.
- Copie os arquivos .ADML para a pasta *%WINROOT%\PolicyDefinitions\pt-br*.

Após tê-los copiado, você pode usar o Editor de políticas de grupo para mudar a política.




## Avançar

- [Conectar um site do Azure a um recurso local](http://go.microsoft.com/fwlink/p/?LinkId=397538)
- [Conexões Híbridas passo a passo: Conectar-se a um SQL Server local por meio de um Site do Azure](http://go.microsoft.com/fwlink/?LinkID=397979)
- [Conexões Híbridas e Serviços Móveis do Azure](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)
- [Visão geral de Conexões Híbridas](http://azure.microsoft.com/documentation/articles/integration-hybrid-connection-overview)


## Consulte também

- [API REST para gerenciamento dos Serviços do BizTalk no Windows Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
- [Serviços do BizTalk: Tabela de edições](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Criar um Serviço do BizTalk usando o Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços do BizTalk: guias Painel, Monitoramento e Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png

<!--HONumber=46--> 
 