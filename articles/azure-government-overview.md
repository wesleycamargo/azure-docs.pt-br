<properties 
   pageTitle="Visão geral do Governo do Azure" 
   description="Este artigo fornece uma visão geral de como os recursos da Nuvem de Governo do Azure e o design e a segurança confiável são usados para oferecer suporte a conformidade aplicável a órgãos do governo federal, estadual e local e seus parceiros." 
   services="Azure-Government" 
   documentationCenter="" 
   authors="joharve2" 
   manager="chrisnie" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="azure-government" 
   ms.date="03/13/2015"
   ms.author="john.harvey@microsoft.com"/>

#  Visão geral do Governo do Microsoft Azure 

<p> O Microsoft Azure Government é uma instância isolada fisicamente e da rede do Microsoft Azure. Este guia de desenvolvedores fornecerá detalhes sobre as diferenças que os desenvolvedores de aplicativos e administradores precisam interagir e trabalhar com essas regiões separadas do Azure.


## <a name="Overview"></a>Visão geral

O Governo do Azure é uma nuvem de comunidade do governo (GCC) projetada para dar suporte estratégico a cenários de governo que necessitam de velocidade, dimensionamento, segurança, conformidade e economia para órgãos do governo dos EUA. Ele foi desenvolvido com base na experiência abrangente da Microsoft no fornecimento de software, segurança, conformidade e controles em outras ofertas de nuvem da Microsoft, como o Azure público, Office 365, O365 GCC, Microsoft CRM Online, etc.

Além disso, o Governo do Azure é projetado para atender a segurança de nível superior e conformidade necessária para cargas de trabalho privadas e exclusivas do setor público dos Estados Unidos encontradas em regulamentações, como FedRAMP (Federal Risk and Authorization Management Program), ECSB (Department of Defense Enterprise Cloud Service Broker), política de segurança do CJIS (Criminal Justice Information Services) e HIPAA (Health Insurance Portability and Accountability Act).

Abaixo está uma exibição resumida da infraestrutura, malha, serviços e estruturas da Nuvem do Governo do Azure que estão disponíveis para ajudar organizações governamentais a criar soluções de nuvem híbridas para atender às suas metas. Como alguns serviços abaixo só estão disponíveis no modo de visualização, consulte a [página de regiões](http://azure.microsoft.com/regions/#services) que os serviços mais atualizados normalmente disponíveis estão listados.

![][2]

O Governo do Azure inclui os principais componentes da Infraestrutura Como Serviço (IaaS) e Plataforma como Serviço (PaaS). Isso inclui a infraestrutura, rede, armazenamento, gerenciamento de dados, gerenciamento de identidades e muitos outros serviços. O Governo do Azure oferece suporte a excelentes recursos que os clientes do Azure público utilizaram como replicação de dados de sincronização geográfica e dimensionamento automático. A Microsoft foi identificada como líder em <a href="https://www.gartner.com/doc/2575715/magic-quadrant-cloud-infrastructure-service" target="_new">IaaS</a> e <a href="https://www.gartner.com/doc/2645317/magic-quadrant-enterprise-application-platform" target="_new">PaaS<a/> por analistas líderes do setor.

Além de fornecer os serviços robustos e recursos do Azure público, o Governo do Azure fornece uma série de recursos para garantir para as entidades governamentais dos EUA que seus dados sejam protegidos, fornecendo:

- **Instância isolada da rede e física** – O ambiente do Governo do Azure é uma instância completamente separada do Microsoft Azure público e é usado somente por órgãos governamentais e provedores de solução dos Estados Unidos qualificados.

- **Segurança, privacidade e conformidade** - A Microsoft implementou sua estrutura robusta de controles de segurança, privacidade e conformidade, além de controles rígidos adicionais para atender o maior nível de requisitos encontrados em Níveis de Impacto ECSB e CJIS.

- **Armazenamento de dados** – o ambiente do Governo do Azure mantém dois datacenters com mais de 500 milhas de distância. Todos os dados de clientes gerenciados são armazenados em datacenters nos Estados Unidos Continental (CONUS)

- **Equipe dos EUA** – Todos os operadores e administradores do Governo do Azure são cidadãos americanos investigados.

- **Gerenciamento de identidades** – O gerenciamento de identidades no ambiente do Governo do Azure é uma instância separada do Active Directory do Azure.

- **Conformidade** – A Microsoft está investindo continuamente para atender e manter rigorosos requisitos de conformidade federal, estadual e local como FedRAMP, CJIS, ECSB e HIPAA para soluções de nuvem do governo dos EUA.

- **Integração de nuvem** – O Governo do Azure fornece um ambiente integrado com o Governo O365 permitindo um logon único entre os serviços de nuvem e serviços avançados, como 1TB de espaço de armazenamento do OneDrive.

O Governo do Azure também permite às organizações manter seus investimentos em tecnologia existentes e perceber os benefícios dos serviços de nuvem. Como o Governo do Azure é uma plataforma de nuvem interoperável, com produtos e tecnologias que as organizações podem usar para criar aplicativos que são mais abertos desde o início. As agências podem escolher as ferramentas, os serviços, o sistema operacional, a arquitetura e as estruturas, incluindo Windows, Linux, Oracle, SharePoint, .NET, Java, PHP e Node. js, para suas soluções de nuvem. A flexibilidade da plataforma de Governo do Azure permite novas formas de colaboração entre agências, desenvolvimento de aplicativos e integração.

As organizações do governo dos EUA interessadas em serviços de nuvem podem ter certeza de que o Governo do Azure fornece enorme escala e práticas de segurança rigorosas para atender às suas necessidades em constante evolução.







## <a name="Features"></a> Recursos disponíveis atualmente no Microsoft Azure Government
O Azure Government atualmente tem os seguintes serviços disponíveis nas regiões do governo de IOWA e da VIRGÍNIA nos E.U.A.:

- Máquinas Virtuais
- Serviços de Nuvem
- Armazenamento
- Active Directory
- Agendador
- Rede Virtual
- Banco de Dados SQL
- Arquivos do Azure
- Serviços de mídia
- Gerenciador de Tráfego
- Barramento de Serviço

Outros serviços estão disponíveis e mais serviços serão adicionados continuamente. Para obter a lista mais atual dos serviços, consulte a [página regiões](http://azure.microsoft.com/regions/#services) que destacará todas as regiões disponíveis e seus serviços.

Atualmente, o Governo de Iowa e da Virgínia nos E.U.A. são os centros de dados de suporte para o Azure Government. Consulte acima a página de regiões de centros de dados existentes e serviços disponíveis.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>Próximas etapas

Se você estiver interessado em aprender mais sobre o Governo do Azure aproveite alguns dos links abaixo.

- **<A href="http://azure.com/gov">Adquirindo e acessando o Governo do Azure</a>**

- **<A href="/azure-government-developer-guide">Guia de Desenvolvedores do Governo do Azure</a>**

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=58_postMigration-->