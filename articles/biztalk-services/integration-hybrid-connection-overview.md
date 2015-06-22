<properties 
	pageTitle="Visão geral de Conexões Híbridas | Azure" 
	description="Saiba mais sobre conexões híbridas, incluindo segurança, portas TCP e configurações com suporte. MABS, WABS" 
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



# Visão geral de Conexões Híbridas
Este tópico apresenta Conexões Híbridas, lista as configurações com suporte e lista também as portas TCP necessárias. Especificamente:

- [O que é uma Conexão Híbrida](#HCOverview)
- [Configurações com suporte](#KnownIssues)
- [Segurança e portas](#HCSecurity)

##<a name="HCOverview"></a>O que é uma Conexão Híbrida

As Conexões Híbridas oferecem um modo fácil e prático de conectar sites do Azure e Serviços Móveis do Azure a recursos locais. As Conexões Híbridas são um recurso dos Serviços BizTalk do Azure:

![Hybrid Connections][HCImage]

Os benefícios das Conexões Híbridas incluem:

- Sites e Serviços Móveis podem acessar, com segurança, serviços e dados locais existentes.
- Sites ou Serviços Móveis diversos podem compartilhar uma Conexão Híbrida para acessar um recurso local. 
- O mínimo de portas TCP é necessário para acessar sua rede.
- Os aplicativos utilizando Conexões Híbridas acessam somente o recurso específico local que é publicado por meio da Conexão Híbrida.
- Eles podem se conectar a qualquer recurso local que utilize uma porta TCP estática, como o SQL Server, MySQL, APIs Web HTTP e a maioria dos serviços web personalizados.

> [WACOM.NOTE] Serviços baseados em TCP que utilizam portas dinâmicas (como um Modo passivo de FTP ou Modo passivo estendido) não têm suporte atualmente.

- Podem ser utilizados com todas as estruturas suportadas pelos Sites do Azure (.NET, PHP, Java, Python, Node.js) e Serviços Móveis do Azure (Node.js, .NET).
- Sites e Serviços Móveis podem acessar recursos locais exatamente do mesmo modo que fariam se o Site ou Serviço Móvel estivesse localizado em sua rede local. Por exemplo, a mesma cadeia de conexão utilizada localmente também pode ser utilizada no Azure.


As Conexões Híbridas também fornecem aos Administradores Corporativos controle e visibilidade dos recursos corporativos acessados pelos aplicativos híbridos, incluindo:

- Usando configurações de Política de Grupo, os Administradores podem permitir Conexões Híbridas na rede e também atribuir recursos que podem ser acessados por aplicativos híbridos.
- Logs de Evento e Auditoria na rede corporativa oferecem visibilidade dos recursos acessados por Conexões Híbridas.


##<a name="KnownIssues"></a>Configurações com suporte

As Conexões Híbridas suportam as combinações a seguir de estrutura e aplicativo:

- Acesso .NET framework ao SQL Server
- Acesso .NET framework a serviços HTTP/HTTPS com WebClient
- Acesso PHP a SQL Server, MySQL
- Acesso Java a SQL Server, MySQL e Oracle
- Acesso Java a serviços HTTP/HTTPS

Ao utilizar Conexões Híbridas para acessar o SQL Server local, considere o seguinte:

- Instâncias nomeadas como SQL Express devem ser configuradas para utilizar portas estáticas. Por padrão, as instâncias nomeadas como SQL Express utilizam portas dinâmicas.
- As instâncias padrão do SQL Express utilizam uma porta estática, mas o uso de TCP precisa estar habilitado. Por padrão, o uso de TCP não está habilitado.
- Ao utilizar Armazenamento em Cluster ou Grupos de Disponibilidade, o modo MultiSubnetFailover=true não é suportado atualmente.
- O ApplicationIntent=ReadOnly não é suportado atualmente.
- A autenticação SQL pode ser exigida como o método de autorização de ponta a ponta suportado pelo aplicativo do Azure e o SQL Server local.


##<a name="HCSecurity"></a>Segurança e portas

As Conexões Híbridas utilizam a autorização de assinatura de acesso compartilhado (SAS) para proteger as conexões dos aplicativos do Azure e o Gerenciador de Conexões Híbridas local para a Conexão Híbrida. Chaves de conexão separadas são criadas para o aplicativo e para o Gerenciador de Conexões Híbridas local. É possível fazer a rolagem por essas chaves de conexão e revogá-las de modo independente.

As Conexões Híbridas fornecem uma distribuição perfeita e segura das chaves aos aplicativos e ao Gerenciador de Conexões Híbridas local. 

Consulte [Criar e gerenciar conexões híbridas](http://azure.microsoft.com/documentation/articles/integration-hybrid-connection-create-manage). 

**A autorização do aplicativo é separada da Conexão Híbrida**. Qualquer método adequado de autorização pode ser usado. O método de autorização depende dos métodos de autorização de ponta a ponta suportados pela nuvem do Azure e nos componentes locais. Por exemplo, o seu aplicativo do Azure acessa um SQL Server local. Neste cenário, a Autorização SQL pode ser o método de autorização com suporte de ponta a ponta.

####Portas TCP
Conexões Híbridas requerem somente conectividade TCP ou HTTP de saída da sua rede privada. Você não precisa abrir portas de firewall ou alterar sua configuração de perímetro de rede para permitir qualquer conectividade de entrada em sua rede.

As seguintes portas TCP são usadas por Conexões Híbridas:

<table border="1">
    <tr>
       <th><strong>Port</strong></th>
        <th>Porque</th>
    </tr>
    <tr>
        <td>80</td>
        <td>Porta HTTP; Utilizada para validação de certificados.</td>
    </tr>
    <tr>
        <td>443</td>
        <td>Porta HTTPS</td>
    </tr>
	<tr>
        <td>5671</td>
        <td>Utilizada para conexão ao Azure. Se a porta TCP 5671 não estiver disponível, a porta TCP 443 será usada.</td>
	</tr>
	<tr>
        <td>9352</td>
        <td>Usada para dados de push e pull. Se a porta TCP 9352 não estiver disponível, a porta TCP 443 será usada.</td>
	</tr>
</table>


## Avançar

- [Criar e gerenciar Conexões Híbridas](http://azure.microsoft.com/documentation/articles/integration-hybrid-connection-create-manage)
- [Conectar um site do Azure a um recurso local](http://go.microsoft.com/fwlink/p/?LinkId=397538)
- [Conexões Híbridas passo a passo: Conectar-se a um SQL Server local por meio de um Site do Azure](http://go.microsoft.com/fwlink/?LinkID=397979)
- [Conexões Híbridas e Serviços Móveis do Azure](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)


## Consulte também

- [API REST para gerenciamento dos Serviços do BizTalk no Windows Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
- [Serviços do BizTalk: Tabela de edições](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Criar um Serviço do BizTalk usando o Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços do BizTalk: guias Painel, Monitoramento e Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

<!--HONumber=46--> 
 