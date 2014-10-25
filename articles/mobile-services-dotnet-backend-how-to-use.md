<properties linkid="mobile-services-dotnet-backend-how-to-use" urlDisplayName="Use the Mobile Services .NET Backend" pageTitle="Use the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn the details of the .NET Backend programming model for Azure Mobile Services, including how to work with table data, APIs, authentication, and scheduled jobs" metaCanonical="" services="" documentationCenter="Mobile" title="Use the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# Usar o back-end .NET dos Serviços Móveis

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este artigo fornece informações detalhadas e exemplos de como trabalhar com o back-end .NET nos Serviços Móveis do Azure. Este tópico é dividido nas seguintes seções:

-   [Introdução][Introdução]
-   [Operações de tabela][Operações de tabela]

## <a name="intro"></a> Introdução

O back-end .NET dos Serviços Móveis permite que você escreva lógica rica de negócios de back-end usando [API da Web ASP.NET][API da Web ASP.NET] e sua linguagem .NET favorita. Os Serviços Móveis expõem uma pequena superfície de modelo de programação como [Pacotes NuGet][Pacotes NuGet], que facilitam o acesso sem interrupções ao seu serviço por meio de SDKs clientes dos Serviços Móveis entre plataformas para Windows, Android, iOS, etc. Essas APIs também asseguram que, adicionando autenticação e suporte de notificações por push, isso se torne o mais simples possível. Entretanto, para a maior parte do modelo de programação, a base é Web API, e com isso os desenvolvedores que estão familiarizados devem se sentir em casa.

## <a name="table-scripts"></a>Operações de tabela

O back-end .NET dos Serviços Móveis fornece uma abstração de “tabela" universal, que representa uma API HTTP com base em CRUD para armazenamento em banco de dados. Essa abstração separa as preocupações com o armazenamento de dados de sua lógica de negócios e permite que seu serviço móvel exponha armazenamentos de dados diferentes em um formato eletrônico com base em JSON e consistente, isso é prontamente entendido pelos SDKs clientes dos Serviços Móveis de várias plataformas.

Na raiz desse modelo de programação está a classe [**TableController<t>**][**TableController<t>**], que é apenas uma API Web regular [**ApiController**][**ApiController**], ou seja, personalizada para um padrão de acesso a dados CRUD. O **TableController** pode usar uma variedade de armazenamentos de dados, incluindo SQL (por meio de [Entity Framework][Entity Framework]), [Azure Table Storage][Azure Table Storage], [MongoDB][MongoDB] ou seu próprio armazenamento personalizado.

  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-how-to-use-server-scripts/ "Back-end do JavaScript"
  [Introdução]: #intro
  [Operações de tabela]: #table-scripts
  [API da Web ASP.NET]: http://www.asp.net/web-api
  [Pacotes NuGet]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
  [**TableController<t>**]: http://msdn.microsoft.com/library/dn643359.aspx
  [**ApiController**]: http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx
  [Entity Framework]: http://msdn.microsoft.com/data/ef.aspx
  [Azure Table Storage]: http://azure.microsoft.com/documentation/services/storage/
  [MongoDB]: http://www.mongodb.org
