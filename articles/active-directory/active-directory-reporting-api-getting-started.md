<properties
    pageTitle="Introdução à API de Relatório do Azure AD | Microsoft Azure"
    description="Como começar a usar a API de relatório do Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# Introdução à API de relatório do Azure Active Directory

*Este tópico faz parte do [Guia de Relatórios do Azure Active Directory](active-directory-reporting-guide.md).*

O Azure Active Directory fornece vários relatórios. Os dados desses relatórios podem ser muito úteis para seus aplicativos, como sistemas SIEM, auditoria e ferramentas de business intelligence. As APIs de relatório do Azure AD fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

Este artigo fornece as informações necessárias para começar com as APIs de relatório do Azure AD. Na próxima seção, você encontrará mais detalhes sobre como usar as APIs de auditoria e de entrada. Para todas as outras APIs, consulte o artigo [Eventos e relatórios do Azure AD (visualização)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview).


Para dúvidas, problemas ou comentários, entre em contato com a [Ajuda de relatório do AAD](mailto:aadreportinghelp@microsoft.com).


##Mapa de aprendizagem

1. **Prepare-se** – Antes de testar os exemplos de API, você precisa atender aos [pré-requisitos para acessar a API de relatório do Azure AD](active-directory-reporting-api-prerequisites.md).

2. **Explore** – Tenha uma primeira impressão das APIs de relatórios:

  - [Usar os exemplos para a API de auditoria](active-directory-reporting-api-audit-samples.md)
  - [Usar os exemplos para a API de relatório de atividade de entrada](active-directory-reporting-api-sign-in-activity-samples.md)

3. **Personalizar** – Criar sua própria solução:

  - [Usar a referência de API de auditoria](active-directory-reporting-api-audit-reference.md)
  - [Usar a referência da API de relatório de atividade de entrada](active-directory-reporting-api-sign-in-activity-reference.md)





## Próximas etapas

Se você estiver curioso para ver todos os pontos de extremidade de API do Graph do Azure AD navegando até [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta).

<!---HONumber=AcomDC_0928_2016-->