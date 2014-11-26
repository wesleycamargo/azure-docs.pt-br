<properties linkid="manage-services-integration-hybrid-connection" urlDisplayName="Create and Manage Hybrid Connections - BizTalk Services" pageTitle="Create and Manage Hybrid Connections | Azure" metaKeywords="BizTalk Services, BizTalk, web sites, website, hybrid connections, Azure" description="Learn how to create a hybrid connection, manage the connection, and install the Hybrid Connection Manager." metaCanonical="" services="integration-services" documentationCenter="" title="Create and Manage Hybrid Connections" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mandia" />

# Criar e gerenciar Conexões Híbridas

Este tópico lista as etapas para criar e gerenciar Conexões Híbridas do Azure.

As etapas para conectar-se a um servidor local incluem:

1.  [Criar uma Conexão Híbrida][Criar uma Conexão Híbrida] especificando o nome do host ou o endereço IP do recurso local em sua rede privada.

2.  [Conecte seu site do Azure ou serviço móvel do Azure][Conecte seu site do Azure ou serviço móvel do Azure] à Conexão Híbrida.

3.  [Instale o Gerenciador de Conexão Híbrida][Instale o Gerenciador de Conexão Híbrida] em seu recurso local e conecte-se à Conexão Híbrida específica. O portal do Azure oferece uma experiência de apenas um clique para instalar e conectar.

4.  [Gerenciar Conexões Híbridas][Gerenciar Conexões Híbridas] e suas chaves de conexão.

## <a name="CreateHybridConnection"></a>Criar uma Conexão Híbrida

Uma Conexão Híbrida pode ser criada no Portal de Gerenciamento do Azure usando sites **ou** usando Serviços do BizTalk.

**Para criar Conexões Híbridas utilizando sites**, consulte [Conectar um site do Azure a um recurso local][Conectar um site do Azure a um recurso local].

**Para criar Conexões Híbridas em Serviços do BizTalk**:

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  No painel de navegação esquerdo, selecione **Serviços do BizTalk** e, então, selecione seu Serviço do BizTalk.
    Se você não possui um Serviço do BizTalk já existente, você pode [Criar um Serviço do BizTalk][Criar um Serviço do BizTalk].
3.  Selecione a guia Conexões Híbridas:
    ![Guia Conexões Híbridas][Guia Conexões Híbridas]

4.  Selecione **Criar uma Conexão Híbrida** ou selecione o botão **ADD** na barra de tarefas. Insira as seguintes informações:

    <table border="1">
    <tr>
       <td><strong>Nome</strong></td>
        <td>O nome da Conexão Híbrida precisa ser único e não pode ser igual ao nome do Serviço do BizTalk. Você pode inserir qualquer nome, mas seja específico quanto à sua finalidade. Os exemplos incluem:<br/><br/>
		SQLServer<em>FolhaDePagamento</em><br/>
		ListaDeFornecimento<em>ServidorSharepoint</em><br/>
		ServidorOracle<em>DoCliente</em>
        </td>
    </tr>
    <tr>
        <td><strong>Nome de host</strong></td>
        <td>Insira o nome de host totalmente qualificado, somente o nome de host ou, então, o endereço IPv4 do recurso local. Os exemplos incluem:
        <br/><br/>
<em>mySQLServer</em>
<br/>
<em>mySQLServer</em>.<em>Domínio</em>.corp.<em>suaEmpresa</em>.com
<br/>
<em>ServidormyHTTPSharePoint</em>
<br/>
<em>ServidormyHTTPSharePoint</em>.<em>suaEmpresa</em>.com
<br/>
10.100.10.10
       </td>
    </tr>
	<tr>
        <td><strong>Porta</strong></td>
        <td>Insira o número da porta no recurso local. Por exemplo, se você estiver usando um site, insira a porta 80 ou 443. Se você estiver usando o SQL Server, insira a porta 1433.</td>
	</tr>
	</table>

5.  Selecione a marca de seleção.

#### Múltiplas

-   Conexões Híbridas adicionais podem ser criadas. Consulte os [Serviços do BizTalk: Tabela de edições][Serviços do BizTalk: Tabela de edições] para o número de conexões permitidas.
-   Cada Conexão Híbrida é criada com um par de cadeias de conexão: Chaves de aplicativo que ENVIAM e chaves locais que ESCUTAM. Cada par possui uma chave primária e outra secundária.

## <a name="LinkWebSite"></a>Conecte seu site do Azure ou serviço móvel do Azure

Para conectar o site do Azure a uma Conexão Híbrida existente, selecione **usar uma Conexão Híbrida existente** na lâmina Conexões Híbridas. Consulte [Conectar um site do Azure a um recurso local][Conectar um site do Azure a um recurso local].

Para conectar o Serviço Móvel do Azure a uma Conexão Híbrida existente, selecione **Incluir Conexão Híbrida** ao alterar ou criar um Serviço Móvel. Consulte [Conexões Híbridas e Serviços Móveis do Azure][Conexões Híbridas e Serviços Móveis do Azure].

## <a name="InstallHCM"></a>Instalar o Gerenciador de Conexões Híbridas local

Após uma Conexão Híbrida ser criada, instale o Gerenciador de Conexões Híbridas no recurso local. Seu download pode ser feito por meio do site do Azure ou por meio de seu Serviço do BizTalk. Etapas dos Serviços do BizTalk:

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Selecione **Serviços do BizTalk** no painel de navegação à esquerda e selecione o seu Serviço do BizTalk.
3.  Selecione a guia **Conexões Híbridas**:
    ![Guia Conexões Híbridas][Guia Conexões Híbridas]
4.  Na barra de tarefas, selecione **Configuração Local**:
    ![Configuração Local][Configuração Local]
5.  Selecione **Instalar e Configurar** para executar ou baixar o Gerenciador de Conexões Híbridas no sistema local.
6.  Selecione a marca de seleção para iniciar a instalação.

<!-- You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:  1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps. 2. Download the Hybrid Connection Manager MSI file.  3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file.  4. Using Windows PowerShell, type:  > Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*”  -->

#### Conexões

-   Híbridas adicionais suportam recursos locais instalados nos sistemas operacionais a seguir:

    -   Windows Server 2008 R2
    -   Windows Server 2012
    -   Windows Server 2012 R2
-   Após você instalar o Gerenciador de Conexões Híbridas, ocorre o seguinte:

    -   A Conexão Híbrida hospedada no Azure é configurada automaticamente para utilizar a cadeia de conexão primária do aplicativo.
    -   O recurso local é configurado automaticamente para utilizar a cadeia de conexão primária local.
-   O Gerenciador de Conexões Híbridas precisa utilizar uma cadeia de conexão local válida para autorização. O Serviço Móvel ou o site do Azure precisa utilizar uma cadeia de conexão ao aplicativo válida para autorização.

## <a name="ManageHybridConnection"></a>Gerenciar Conexões Híbridas

Para gerenciar suas Conexões Híbridas, você pode:

-   Utilizar o portal do Azure e ir até seu Serviço do BizTalk.
-   Utilizar [APIs REST][APIs REST].
    <!-- - Use Windows PowerShell cmdlets  **INSERT LINK**. -->

#### Copiar/regenerar as cadeias de conexão híbrida

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Selecione **Serviços do BizTalk** no painel de navegação à esquerda e selecione o seu Serviço do BizTalk.
3.  Selecione a guia **Conexões Híbridas**:
        
    ![Guia Conexões Híbridas][Guia Conexões Híbridas]
4.  Selecione a Conexão Híbrida. Na barra de tarefas, selecione **Gerenciar Conexão**:
        
    ![Opções de Gerenciamento][Opções de Gerenciamento]
        
    **Gerenciar Conexão** lista as cadeias de conexão locais e do aplicativo. Você pode copiar as cadeias de conexão ou regenerar a chave de acesso utilizada na cadeia de conexão.
        
    **Se você selecionar Regenerar**, a chave de acesso compartilhada usada na cadeia de conexão será alterada. Faça o seguinte:

5.   No Portal de Gerenciamento do Azure, selecione **Sincronizar Chaves** no aplicativo do Azure.
6.   Execute novamente a **Configuração Local**. Ao executar novamente a Configuração Local, o recurso local é configurado automaticamente para usar a cadeia de conexão primária atualizada.

#### Use a Política de Grupo para controlar os recursos locais utilizados por uma Conexão Híbrida

1.  Baixe os modelos administrativos do Gerenciador de Conexões Híbridas.
2.  Extraia os arquivos.
3.  No computador que modifica a política de grupo, faça o seguinte:

4.   Copie os arquivos .ADMX para a pasta *%WINROOT%\\PolicyDefinitions*.
5.   Copie os arquivos .ADML para a pasta *%WINROOT%\\PolicyDefinitions\\pt-br*.

Após tê-los copiado, você pode usar o Editor de políticas de grupo para mudar a política.

## Avançar

-   [Conecte um site do Azure a um recurso local][Conectar um site do Azure a um recurso local]
-   [Conexões Híbridas passo a passo: Conectar-se a um SQL Server local por meio de um Site do Azure][Conexões Híbridas passo a passo: Conectar-se a um SQL Server local por meio de um Site do Azure]
-   [Conexões Híbridas e Serviços Móveis do Azure][Conexões Híbridas e Serviços Móveis do Azure]
-   [Visão geral de Conexões Híbridas][Visão geral de Conexões Híbridas]

## Consulte também

-   [API REST para gerenciamento dos Serviços do BizTalk no Windows Azure][APIs REST]
-   [Serviços do BizTalk: Tabela de edições][Serviços do BizTalk: Tabela de edições]
-   [Criar um Serviço do BizTalk usando o Portal de Gerenciamento do Azure][Criar um Serviço do BizTalk usando o Portal de Gerenciamento do Azure]
-   [Serviços do BizTalk: guias Painel, Monitoramento e Escala][Serviços do BizTalk: guias Painel, Monitoramento e Escala]

  [Criar uma Conexão Híbrida]: #CreateHybridConnection
  [Conecte seu site do Azure ou serviço móvel do Azure]: #LinkWebSite
  [Instale o Gerenciador de Conexão Híbrida]: #InstallHCM
  [Gerenciar Conexões Híbridas]: #ManageHybridConnection
  [Conectar um site do Azure a um recurso local]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [Criar um Serviço do BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Guia Conexões Híbridas]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
  [Serviços do BizTalk: Tabela de edições]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Conexões Híbridas e Serviços Móveis do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [Configuração Local]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
  [APIs REST]: http://msdn.microsoft.com/library/azure/dn232347.aspx
  [Opções de Gerenciamento]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
  [Conexões Híbridas passo a passo: Conectar-se a um SQL Server local por meio de um Site do Azure]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Visão geral de Conexões Híbridas]: http://azure.microsoft.com/pt-br/documentation/articles/integration-hybrid-connection-overview
  [Criar um Serviço do BizTalk usando o Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Serviços do BizTalk: guias Painel, Monitoramento e Escala]: http://go.microsoft.com/fwlink/p/?LinkID=302281
