---
title: "Introdução aos relatórios do Azure Active Directory | Microsoft Docs"
description: "Lista os diversos relatórios disponíveis nos relatórios do Active Directory do Azure"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 7ac99919-8df5-4424-9298-fc7c025ba949
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: eec9b73cbaccfa50eec6f237e4d1d810c6efa1d9
ms.openlocfilehash: e5b8ac91914203156bd395d7f462385e9f6dbcb4
ms.contentlocale: pt-br
ms.lasthandoff: 02/24/2017

---
# Introdução aos Relatórios do Active Directory do Azure
<a id="getting-started-with-azure-active-directory-reporting" class="xliff"></a>
## O que é
<a id="what-it-is" class="xliff"></a>
O Active Directory do Azure (Azure AD) inclui relatórios de auditoria, atividade e segurança para seu diretório. Aqui está uma lista dos relatórios incluídos:

### Relatórios de segurança
<a id="security-reports" class="xliff"></a>
* Entradas de fontes desconhecidas
* Entradas após várias falhas
* Entradas de várias geografias
* Entradas de endereços IP com atividade suspeita
* Atividades de entrada irregulares
* Entradas de dispositivos possivelmente infectados
* Usuários com atividade de entrada anômala

### Relatórios de atividades
<a id="activity-reports" class="xliff"></a>
* Uso do aplicativo: resumo
* Uso do aplicativo: detalhado
* Painel do aplicativo
* Erros de provisionamento de conta
* Dispositivos de usuário individual
* Atividade de usuário individual
* Relatório de atividade de grupos
* Relatório de atividade de registro de redefinição de senha
* Atividade de redefinição de senha

### Relatórios de auditoria
<a id="audit-reports" class="xliff"></a>
* Relatório de auditoria de diretório

> [!TIP]
> Para obter mais documentação sobre os Relatórios do AD do Azure, consulte [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md).
> 
> 

## Como ele funciona
<a id="how-it-works" class="xliff"></a>
### Pipeline de relatórios
<a id="reporting-pipeline" class="xliff"></a>
O pipeline de relatórios consiste em três etapas principais. Sempre que um usuário entrar ou uma autenticação for feita, acontecerá o seguinte:

* Primeiro, o usuário é autenticado (com êxito ou não), e o resultado é armazenado nos bancos de dados de serviço do Active Directory do Azure.
* Em intervalos regulares, todas as entradas recentes são processadas. Neste ponto, nossos algoritmos de atividade anômala e segurança estão procurando atividades suspeitas em todas as entradas recentes.
* Após o processamento, os relatórios são gravados, armazenados em cache e disponibilizados no portal clássico do Azure.

### Tempos de geração dos relatórios
<a id="report-generation-times" class="xliff"></a>
Devido ao grande volume de autenticações e entradas processadas pela plataforma do Azure AD, as entradas mais recentes processadas têm, em média, uma hora de duração. Em casos raros, pode levar até oito horas para as entradas mais recentes serem processadas.

É possível encontrar a entrada processada mais recente examinando o texto de ajuda na parte superior de cada relatório.

![Texto de ajuda na parte superior de cada relatório](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [!TIP]
> Para obter mais documentação sobre os Relatórios do AD do Azure, consulte [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md).
> 
> 

## Introdução
<a id="getting-started" class="xliff"></a>
### Entre no portal clássico do Azure
<a id="sign-into-the-azure-classic-portal" class="xliff"></a>
Primeiro, será necessário entrar no [Portal Clássico do Azure](https://manage.windowsazure.com) como administrador global ou de conformidade. Também será necessário ser um administrador ou coadministrador de serviço de assinatura do Azure ou usar a assinatura do Azure "Acesso ao Azure AD".

### Navegue até Relatórios
<a id="navigate-to-reports" class="xliff"></a>
Para exibir relatórios, navegue até a guia Relatórios na parte superior do diretório.

Se esta for a primeira vez que você exibe relatórios, será preciso aceitar uma caixa de diálogo antes de conseguir exibir os relatórios. Isso é para garantir que a exibição desses dados, que podem ser considerados informações particulares em alguns países, seja aceitável aos administradores em sua organização.

![Caixa de diálogo](./media/active-directory-reporting-getting-started/dialogBox.png)

### Explore cada relatório
<a id="explore-each-report" class="xliff"></a>
Navegue até cada relatório para ver os dados coletados e as entradas processadas. É possível encontrar uma [lista com todos os relatórios aqui](active-directory-reporting-guide.md).

![Todos os relatórios](./media/active-directory-reporting-getting-started/reportsMain.png)

### Baixe os relatórios como CSV
<a id="download-the-reports-as-csv" class="xliff"></a>
Cada relatório pode ser baixado como um arquivo CSV (valores separados por vírgula). É possível usar esses arquivos no Excel, no PowerBI ou em programas de análise de terceiros para analisar os dados ainda mais.

Para baixar qualquer relatório como CSV, navegue até o relatório e clique em "Baixar" na parte inferior.

![Botão Baixar](./media/active-directory-reporting-getting-started/downloadButton.png)

> [!TIP]
> Para obter mais documentação sobre os Relatórios do AD do Azure, consulte [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md).
> 
> 

## Próximas etapas
<a id="next-steps" class="xliff"></a>
### Personalizar alertas para atividade de entrada anômala
<a id="customize-alerts-for-anomalous-sign-in-activity" class="xliff"></a>
Navegue até a guia "Configurar" de seu diretório.

Role até a seção "Notificações".

Habilite ou desabilite a seção "Notificações por Email de Entradas Anômalas".

![Seção Notificações](./media/active-directory-reporting-getting-started/notificationsSection.png)

### Integrar com a API de Relatório do AD do Azure
<a id="integrate-with-the-azure-ad-reporting-api" class="xliff"></a>
Consulte [Introdução à API de Relatório](active-directory-reporting-api-getting-started.md).

### Acione Multi-Factor Authentication nos usuários
<a id="engage-multi-factor-authentication-on-users" class="xliff"></a>
Selecione um usuário em um relatório.

Clique no botão "Habilitar MFA" na parte inferior da tela.

![Botão Multi-Factor Authentication na parte inferior da tela](./media/active-directory-reporting-getting-started/mfaButton.png)

> [!TIP]
> Para obter mais documentação sobre os Relatórios do AD do Azure, consulte [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md).
> 
> 

## Saiba mais
<a id="learn-more" class="xliff"></a>
### Eventos de auditoria
<a id="audit-events" class="xliff"></a>
Saiba mais sobre quais eventos são auditados no diretório em [Eventos de auditoria de Relatórios do Active Directory do Azure](active-directory-reporting-audit-events.md).

### Integração da API
<a id="api-integration" class="xliff"></a>
Consulte [Introdução à API de Relatório](active-directory-reporting-api-getting-started.md) e a [documentação de referência da API](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### Entre em contato
<a id="get-in-touch" class="xliff"></a>
Envie um email para [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) para qualquer dúvida, ajuda ou comentário.

> [!TIP]
> Para obter mais documentação sobre os Relatórios do AD do Azure, consulte [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md).
> 
> 


