---
title: "Integração de Log do Azure (AZLOG) com os logs de auditoria do Active Directory | Microsoft Docs"
description: "Saiba como instalar o serviço de integração de log do Azure e integre os registros de logs de auditoria do Azure"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/09/2017
ms.author: barclayn
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5e135be1e21173add3236f851609f1df0a5b0dee
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---

#<a name="integrate-azure-active-directory-audit-logs"></a>Integrar o Azure Active Directory a logs de auditoria

Eventos de auditoria do Azure Active Directory ajudam a identificar ações privilegiadas que ocorreram no Azure Active Directory. Você pode ver os tipos de eventos que você pode acompanhar revisando os [eventos de relatório de auditoria do Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md)

>[!NOTE]
Consulte o artigo [Introdução](security-azure-log-integration-get-started.md) e conclua todas as etapas até a etapa 3 da seção **Pós-instalação e validação** antes de tentar realizar as etapas deste artigo.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Etapas para integrar os logs de auditoria do Azure Active Directory

1. Abra o prompt de comando e cd em **c:\Program Files\Microsoft Azure Log Integration**
2. Execute o comando:

 ``azlog createazureid``

 Esse comando solicitará o logon do Azure. O comando cria uma Entidade de Serviço do Azure Active Directory em locatários do Azure AD que hospedam as assinaturas do Azure nas quais o usuário conectado é um Administrador, um Coadministrador ou um Proprietário. O comando falhará se o usuário conectado for apenas um usuário Convidado no Locatário do Azure AD. A autenticação do Azure é feita por meio do Azure Active Directory (AD). Criar uma entidade de serviço para a Integração do Azlog criará a identidade do Azure AD que terá o acesso de leitura a partir das assinaturas do Azure.

3. Execute o comando fornecendo seu tenantID. Você precisa ser membro da função de administrador de locatário para executar o comando.

``Azlog.exe authorizedirectoryreader tenantId``

Exemplo

``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

Verifique as seguintes pastas para confirmar se os arquivos JSON do Log de Auditoria do Azure Active Directory são criados em:

* **C:\Users\azlog\AzureActiveDirectoryJson**
* **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Aponte o conector de encaminhamento de arquivos SIEM padrão para a devida pasta para enviar os dados para a instância SIEM. Talvez sejam necessários alguns mapeamentos de campo com base no produto SIEM que você está usando.

Ajuda da comunidade está disponível por meio de [Fórum do MSDN de integração de Log do Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Este fórum fornece para a comunidade AzLog a capacidade de apoiar uns aos outros com perguntas, respostas, dicas e truques sobre como aproveitar ao máximo a integração de log do Azure. Além disso, a equipe de Integração do Log do Azure monitora esse fórum e ajuda sempre que possível.

Você também pode abrir uma [solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md). Para fazer isso, selecione **integração de log** como o serviço para o qual você está solicitando suporte.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Integração do Log do Azure, consulte os seguintes documentos:

* [Integração de log do Microsoft Azure para os logs do Azure](https://www.microsoft.com/download/details.aspx?id=53324) – visite o Centro de Download para obter os detalhes, os requisitos de sistema e as instruções de instalação da integração de log do Azure.
* [Introdução à integração de log do Azure](security-azure-log-integration-overview.md) – este documento apresenta a integração do registro do Azure, seus principais recursos e como ele funciona.
* [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta postagem de blog mostra a você como configurar a integração de log do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
* [Perguntas frequentes sobre o log de integração do Azure](security-azure-log-integration-faq.md) – encontre as respostas para as perguntas frequentes sobre a integração de log do Azure.
* [Integração dos alertas da Central de Segurança com a Integração de Log do Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) – este documento mostra como sincronizar os alertas da Central de Segurança, juntamente com os eventos de segurança de máquina virtual coletados pelo Diagnóstico do Azure e pelos Logs de Auditoria do Azure, com o Log Analytics ou com a solução SIEM.
* [Novos recursos para o Diagnóstico do Azure e para os Logs de Auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – esta postagem de blog apresenta os Logs de Auditoria do Azure e outros recursos que ajudam você a obter ideias sobre as operações de seus recursos do Azure.

