<properties 
	pageTitle="Usar o back-end do .NET dos Serviços Móveis - Serviços Móveis do Azure" 
	description="Conheça os detalhes do modelo de programação de back-end do .NET para os serviços móveis do Azure, incluindo como trabalhar com dados da tabela, APIs, autenticação e trabalhos agendados" 
	services="" 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="mahender"/>
# Usar o back-end do .NET dos Serviços Móveis

<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/en-us/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend">Back-end JavaScript</a></div>

Este artigo fornece informações detalhadas e exemplos de como trabalhar com o back-end do .NET nos Serviços Móveis do Azure. Este tópico é dividido nas seguintes seções:

+ [Introdução](#intro)
+ [Operações de tabela](#table-scripts)

##<a name="intro"></a>Introdução

O back-end do .NET de Serviços Móveis permite que você escreva a lógica de negócios de back-end avançado usando [API Web ASP.NET](http://www.asp.net/web-api) e sua linguagem favorita do .NET. Os Serviços Móveis expõem uma pequena superfície de modelo de programação como [Pacotes NuGet](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22), que facilitam o acesso sem interrupções ao seu serviço por meio de SDKs clientes de Serviços Móveis de plataformas cruzadas para Windows, Android, iOS, etc. Essas APIs também asseguram que, adicionando autenticação e suporte de notificações por push, isso se torne o mais simples possível. Entretanto, para a maior parte do modelo de programação, a base é Web API, e com isso os desenvolvedores que estão familiarizados devem se sentir em casa. 

##<a name="table-scripts"></a>Operações de tabela

O back-end do .NET dos Serviços Móveis fornece uma abstração de "tabela" universal, que representa uma API HTTP com base em CRUD para armazenamento em banco de dados. Essa abstração separa as preocupações com o armazenamento de dados de sua lógica de negócios e permite que seu serviço móvel exponha armazenamentos de dados diferentes em um formato de rede consistente, com base em JSON, que é prontamente entendido pelos SDKs clientes de Serviços Móveis de plataforma cruzada. 

Na raiz desse modelo de programação está a classe [**TableController<T>**](http://msdn.microsoft.com/library/dn643359.aspx), que é apenas uma API Web regular [**ApiController**](http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx), personalizada para um padrão de acesso a dados CRUD. O **TableController** pode usar uma variedade de fontes de dados, inclusive SQL (por meio de [Entity Framework](http://msdn.microsoft.com/data/ef.aspx)), [Armazenamento de Tabela do Azure](http://azure.microsoft.com/documentation/services/storage/), [MongoDB](http://www.mongodb.org), ou seu próprio repositório personalizado.\n<!--HONumber=42-->
