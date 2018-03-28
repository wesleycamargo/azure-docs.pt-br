---
title: GDPR no proxy de aplicativo do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre o GDPR no proxy de aplicativo do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5e99fba2f226e1e9b401bd8ef5b3b85a5e8c6a7c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>GDPR no proxy de aplicativo do Azure Active Directory  

O Proxy de Aplicativo do Microsoft Azure AD (Active Directory) é compatível com GDPR em conjunto com todos os outros recursos e serviços da Microsoft. Para saber mais sobre o suporte de GDPR da Microsoft, consulte a [Documentação e Termos de Licenciamento](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Como esse recurso inclui conectores nos computadores, há alguns eventos que você precisa monitorar para permanecer compatível com GDPR. O Proxy de Aplicativo cria os tipos de log a seguir que podem conter EUII:

- Logs de eventos do conector
- Logs de eventos do Windows

Há duas maneiras de permanecer compatível com GDPR:

- Excluir e exportar solicitações conforme surgem

- Desativar registro em log

Para:

- Informações sobre como configurar a retenção de dados para os logs de eventos do Windows, consulte [Configurações dos logs de eventos](https://technet.microsoft.com/library/cc952132.aspx). 
- Informações gerais no log de eventos do Windows, consulte [Usar o log de eventos do Windows](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).


É possível localizar os logs de eventos de conexão em `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. As seções a seguir fornecem as etapas relacionadas para os logs de eventos do conector. É necessário concluir estas etapas em todos os computadores conectores.
 

## <a name="processing-requests"></a>Solicitações de processamento

Há três tipos diferentes de solicitações pelas quais você é responsável: 

- Excluir
- Visualizar
- Exportação
 
Para exibir processo/solicitações de exportação, é necessário percorrer cada um dos arquivos de log para pesquisar entradas relacionadas. 

Como os logs são arquivos de texto, é possível pesquisar, por exemplo, utilizando o comando `findstr` para localizar entradas relacionadas ao usuário. Pesquise os campos a seguir porque podem estar nos logs: 

- UserId
- O tipo de nome de usuário configurado para qualquer aplicativo usando a Delegação Restrita de Kerberos:
    - Nome UPN
    - UPN local
    - Nome de usuário que faz parte do UPN
    - Nome de usuário que faz parte do UPN local
    - Nome da conta SAM local 

 
Você pode então coletar esses campos e compartilhá-los com o usuário.
Para processar solicitações de exclusão, é necessário excluir os logs relevantes. É possível reiniciar o serviço do conector (Conector do Proxy de Aplicativo do Azure Active Directory) para gerar um novo arquivo de log. O novo arquivo de log permite excluir os arquivos de log antigos. Em seguida, você poderá seguir o processo de exibição/exportação para localizar todos os logs relevantes e excluir seletivamente esses campos ou arquivos. Também é possível excluir somente todos os arquivos de log antigos, caso não sejam mais necessários.


## <a name="turn-off-connector-logs"></a>Desativar os logs do conector

Para desativar os logs do conector, é necessário ajustar `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` removendo a linha destacada: 


![Configuração](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do proxy de aplicativo do Microsoft Azure AD, consulte [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md).

