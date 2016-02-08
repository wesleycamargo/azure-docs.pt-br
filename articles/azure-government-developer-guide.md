<properties 
	pageTitle="Guia de Desenvolvedores do Azure Government" 
	description="Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government" 
	services="" 
	documentationCenter="" 
	authors="Joharve2" 
	manager="Chrisnie" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="10/29/2015" 
	ms.author="jharve"/>


#  Guia do Desenvolvedor do Microsoft Azure Government 

<p> O Microsoft Azure Government é uma instância isolada fisicamente e da rede do Microsoft Azure. Este guia de desenvolvedores fornecerá detalhes sobre as diferenças que os desenvolvedores de aplicativos e administradores precisam interagir e trabalhar com essas regiões separadas do Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Neste tópico


+ [Visão geral](#Overview)
+ [Diretrizes para Desenvolvedores](#Guidance)
+ [Recursos disponíveis atualmente no Microsoft Azure Government](#Features)
+ [Mapeamento de ponto de extremidade](#Endpoint)
+ [Próximas etapas](#next)


## <a name="Overview"></a>Visão geral

O Microsoft Azure Government é uma instância separada do serviço Microsoft Azure para tratar dass necessidades de segurança e conformidade das agências federais, estaduais e municipais dos EUA e seus provedores de soluções. O Azure Government oferece isolamento físico e de rede de implantações não-governamentais e fornece triagem do pessoal dos EUA.

A Microsoft fornece várias ferramentas para criar e implantar aplicativos em nuvem no Microsoft global service do Microsoft Azure (“Serviço Global") e nos serviços Microsoft Azure Government.

Ao criar e implantar aplicativos para os serviços do Azure Government, em vez de serviços globais, os desenvolvedores precisam saber as principais diferenças entre os dois serviços. Especificamente sobre instalar e configurar o ambiente de programação, configurar pontos de extremidade, gravar aplicativos e implantá-lo como serviços do Azure Government.

As informações contidas neste documento resumem essas diferenças e complementam as informações disponíveis sobre o site do [Azure Government](http://www.azure.com/gov "Azure Government") e a [biblioteca técnica do Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") no MSDN. As informações oficiais também podem estar disponíveis em muitos outros locais, como a [Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/ “Central de Confiabilidade do Microsoft Azure”/), [Centro de documentação do Azure](https://azure.microsoft.com/documentation/) e no [Azure Blogs] (https://azure.microsoft.com/blog/ “Azure Blogs”/).

Este conteúdo destina-se a parceiros e desenvolvedores que estão implantando o Microsoft Azure Government.



## <a name="Guidance"></a>Diretrizes para Desenvolvedores
Porque a maioria do conteúdo técnico que está disponível no momento assume que os aplicativos estão sendo desenvolvidos para o Serviço Global em vez de para o governo do Microsoft Azure, é importante garantir que os desenvolvedores estejam cientes das principais diferenças dos aplicativos desenvolvidos para serem hospedado no Azure Government.

- Em primeiro lugar, há diferenças de recursos e de serviços, isso significa que alguns recursos que estão em regiões específicas do serviço Global não estão disponíveis no Azure Government.

- Em segundo lugar, os recursos que são oferecidos no Azure Government, têm configuração difente do Global Service. Portanto, você deve examinar o código de exemplo, as configurações e as etapas para garantir que esteja criando e executando dentro do ambiente de serviços de nuvem do Azure Government.


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
- StorSimple
- Cache Redis
- Serviço de Backup do Azure
- Automação
- Rota Expressa
- etc.

Outros serviços estão disponíveis e mais serviços serão adicionados continuamente. Para obter a lista mais atual dos serviços, consulte a [página regiões](https://azure.microsoft.com/regions/#services) que destacará todas as regiões disponíveis e seus serviços.

Atualmente, o Governo de Iowa e da Virgínia nos E.U.A. são os centros de dados de suporte para o Azure Government. Consulte acima a página de regiões de centros de dados existentes e serviços disponíveis.

## <a name="Endpoint"></a>Mapeamento de ponto de extremidade

Use a tabela a seguir para guiá-lo ao mapear pontos de extremidade públicos do Microsoft Azure e do Banco de dados SQL para os pontos de extremidade específicos do Azure Government.


Tipo de serviço|Público do Azure|Azure Government
---|---|---
Portal de Gerenciamento|manage.windowsazure.com|manage.windowsazure.us
Geral|*.windows.net|*.usgovcloudapi.net
Núcleo|*.core.windows.net|*.core.usgovcloudapi.net
Computação|*.cloudapp.net|*.usgovcloudapp.net
Armazenamento de Blob|*.blob.core.windows.net| *.blob.core.usgovcloudapi.net Armazenamento de Fila|*.queue.core.windows.net|*.queue.core.usgovcloudapi.net
Armazenamento de tabela|*.table.core.windows.net|*.table.core.usgovcloudapi.net
Gerenciamento de serviços|management.core.windows.net|management.core.usgovcloudapi.net
Banco de Dados SQL|*.database.windows.net|*.database.usgovcloudapi.net
Ponto de extremidade com balanceamento de carga do ARM|https://management.windows.net|https://management.usgovcloudapi.net  

* Para a autenticação do ARM por meio do Azure AD, veja [Fazendo a autenticação de solicitações do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="next"></a>Próximas etapas

Se você estiver interessado em aprender mais sobre o Governo do Azure aproveite alguns dos links abaixo.

- **[Inscrever-se para obter uma avaliação](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[Adquirindo e acessando o Azure Governamental](http://azure.com/gov)**

- **[Visão geral do Azure Governamental](/azure-government-overview)**

- **[Blog do Azure Governamental](http://blogs.msdn.com/b/azuregov/)**

- **[Conformidade do Azure](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=AcomDC_0128_2016-->