<properties linkid="manage-services-integration-hybrid-connection" urlDisplayName="Hybrid Connections Overview - BizTalk Services" pageTitle="Hybrid Connections Overview | Azure" metaKeywords="BizTalk Services, BizTalk, websites, web sites, hybrid connections, Azure" description="Learn about hybrid connections, including Security." metaCanonical="" services="integration-services" documentationCenter="" title="Hybrid Connections Overview" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mandia" />

# Visão geral de Conexões Híbridas

Este tópico apresenta Conexões Híbridas, lista as configurações suportadas e lista também as portas TCP necessárias. Especificamente:

-   [O que é uma Conexão Híbrida][O que é uma Conexão Híbrida]
-   [Configurações com suporte][Configurações com suporte]
-   [Segurança][Segurança]

## <a name="HCOverview"></a>O que é uma Conexão Híbrida

As Conexões Híbridas oferecem um modo fácil e prático de conectar sites do Azure e Serviços Móveis do Azure a recursos locais. As Conexões Híbridas são um recurso dos Serviços BizTalk do Azure:

![Conexões Híbridas][Conexões Híbridas]

Os benefícios das Conexões Híbridas incluem:

-   Sites e Serviços Móveis podem acessar, com segurança, serviços e dados locais existentes.
-   Sites ou Serviços Móveis diversos podem compartilhar uma Conexão Híbrida para acessar um recurso local.
-   O mínimo de portas TCP é necessário para acessar sua rede.
-   Os aplicativos utilizando Conexões Híbridas acessam somente o recurso específico local que é publicado por meio da Conexão Híbrida.
-   Eles podem se conectar a qualquer recurso local que utilize uma porta TCP estática, como o SQL Server, MySQL, APIs Web HTTP e a maioria dos serviços web personalizados.

> [WACOM.NOTE] Serviços baseados em TCP que utilizam portas dinâmicas (como um Modo passivo de FTP ou Modo passivo estendido) não são suportados atualmente.

-   Podem ser utilizados com todas as estruturas suportadas pelos Sites do Azure (.NET, PHP, Java, Python, Node.js) e Serviços Móveis do Azure (Node.js, .NET).
-   Sites e Serviços Móveis podem acessar recursos locais exatamente do mesmo modo que fariam se o Site ou Serviço Móvel estivesse localizado em sua rede local. Por exemplo, a mesma cadeia de conexão utilizada no local também pode ser utilizada no Azure.

As Conexões Híbridas também fornecem aos Administradores Corporativos controle e visibilidade dos recursos corporativos acessados pelos aplicativos híbridos, incluindo:

-   Usando configurações de Política de Grupo, os Administradores podem permitir Conexões Híbridas na rede e também atribuir recursos que podem ser acessados por aplicativos híbridos.
-   Logs de Evento e Auditoria na rede corporativa oferecem visibilidade dos recursos acessados por Conexões Híbridas.

## <a name="KnownIssues"></a>Configurações com suporte

As Conexões Híbridas suportam as combinações a seguir de estrutura e aplicativo:

-   Acesso .NET framework ao SQL Server
-   Acesso .NET framework a serviços HTTP/HTTPS com WebClient
-   Acesso PHP a SQL Server, MySQL
-   Acesso Java a SQL Server, MySQL e Oracle
-   Acesso Java a serviços HTTP/HTTPS

Ao utilizar Conexões Híbridas para acessar o SQL Server local, considere o seguinte:

-   Instâncias nomeadas como SQL Express devem ser configuradas para utilizar portas estáticas. Por padrão, as instâncias nomeadas como SQL Express utilizam portas dinâmicas.
-   As instâncias padrão do SQL Express utilizam uma porta estática, mas o uso de TCP precisa estar habilitado. Por padrão, o uso de TCP não está habilitado.
-   Ao utilizar Armazenamento em Cluster ou Grupos de Disponibilidade, o modo MultiSubnetFailover=true não é suportado atualmente.
-   O ApplicationIntent=ReadOnly não é suportado atualmente.
-   A autenticação SQL pode ser exigida como o método de autorização de ponta a ponta suportado pelo aplicativo do Azure e o SQL Server local.

## <a name="HCSecurity"></a>Segurança

As Conexões Híbridas utilizam a autorização de assinatura de acesso compartilhado (SAS) para proteger as conexões dos aplicativos do Azure e o Gerenciador de Conexões Híbridas local para a Conexão Híbrida. Chaves de conexão separadas são criadas para o aplicativo e para o Gerenciador de Conexões Híbridas local. É possível fazer a rolagem por essas chaves de conexão e revogá-las de modo independente.

As Conexões Híbridas fornecem uma distribuição perfeita e segura das chaves aos aplicativos e ao Gerenciador de Conexões Híbridas local.

Consulte [Criar e gerenciar Conexões Híbridas][Criar e gerenciar Conexões Híbridas].

**A autorização do aplicativo é separada da Conexão Híbrida**. Qualquer método adequado de autorização pode ser usado. O método de autorização depende dos métodos de autorização de ponta a ponta suportados pela nuvem do Azure e nos componentes locais. Por exemplo, o seu aplicativo do Azure acessa um SQL Server local. Neste cenário, a Autorização SQL pode ser o método de autorização suportado de ponta a ponta.

#### Portas TCP

| **Porta** | Porque                                                                                                   |
|-----------|----------------------------------------------------------------------------------------------------------|
| 80        | Porta HTTP; Utilizada para validação de certificados.                                                    |
| 443       | Porta HTTPS                                                                                              |
| 5671      | Utilizada para conexão ao Azure. Se a porta TCP 5671 não estiver disponível, a porta TCP 443 será usada. |
| 9352      | Usada para dados de push e pull. Se a porta TCP 9352 não estiver disponível, a porta TCP 443 será usada. |

## Avançar

-   [Criar e gerenciar Conexões Híbridas][Criar e gerenciar Conexões Híbridas]
-   [Conectar um site do Azure a um recurso local][Conectar um site do Azure a um recurso local]
-   [Conexões Híbridas passo a passo: Conectar-se a um SQL Server local por meio de um Site do Azure][Conexões Híbridas passo a passo: Conectar-se a um SQL Server local por meio de um Site do Azure]
-   [Conexões Híbridas e Serviços Móveis do Azure][Conexões Híbridas e Serviços Móveis do Azure]

## Consulte também

-   [API REST para gerenciamento dos Serviços do BizTalk no Windows Azure][API REST para gerenciamento dos Serviços do BizTalk no Windows Azure]
-   [Serviços do BizTalk: Tabela de edições][Serviços do BizTalk: Tabela de edições]
-   [Criar um Serviço do BizTalk usando o Portal de Gerenciamento do Azure][Criar um Serviço do BizTalk usando o Portal de Gerenciamento do Azure]
-   [Serviços do BizTalk: guias Painel, Monitoramento e Escala][Serviços do BizTalk: guias Painel, Monitoramento e Escala]

  [O que é uma Conexão Híbrida]: #HCOverview
  [Configurações com suporte]: #KnownIssues
  [Segurança]: #HCSecurity
  [Conexões Híbridas]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
  [Criar e gerenciar Conexões Híbridas]: http://azure.microsoft.com/pt-br/documentation/articles/integration-hybrid-connection-create-manage
  [Conectar um site do Azure a um recurso local]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Conexões Híbridas passo a passo: Conectar-se a um SQL Server local por meio de um Site do Azure]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Conexões Híbridas e Serviços Móveis do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [API REST para gerenciamento dos Serviços do BizTalk no Windows Azure]: http://msdn.microsoft.com/library/azure/dn232347.aspx
  [Serviços do BizTalk: Tabela de edições]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Criar um Serviço do BizTalk usando o Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Serviços do BizTalk: guias Painel, Monitoramento e Escala]: http://go.microsoft.com/fwlink/p/?LinkID=302281
