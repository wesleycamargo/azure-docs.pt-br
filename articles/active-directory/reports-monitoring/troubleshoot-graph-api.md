---
title: Solucionar erros na API de relatório do Azure Active Directory | Microsoft Docs
description: Fornece uma resolução de erros ao chamar APIs de relatórios do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2f40cca78db3d026de1c253f8694d553d88670dd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155137"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Solucionar erros na API de relatório do Azure Active Directory

Este artigo lista as mensagens de erro comuns que você pode encontrar ao acessar relatórios de atividades usando a API do MS Graph e as etapas para a resolução.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 Erro interno do servidor HTTP ao acessar o ponto de extremidade do Microsoft Graph V2

No momento, não há suporte para o ponto de extremidade do Microsoft Graph v2 – acesse os logs de atividades usando o ponto de extremidade do Microsoft Graph v1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Erro: Falha ao obter as funções de usuário do AD Graph

Você pode receber essa mensagem de erro ao tentar acessar entradas usando o Explorador do Graph. Verifique se que você está conectado à sua conta usando os dois botões de conexão na interface do usuário do Explorador do Graph, conforme é mostrado na imagem a seguir. 

![Explorador do Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Erro: Falha ao fazer a verificação da licença Premium do AD Graph 

Se você encontrar essa mensagem de erro ao tentar acessar entradas usando o Explorador do Graph, escolha **Modificar permissões** em sua conta na barra de navegação à esquerda e selecione **Tasks.ReadWrite** e **Directory.Read.All**. 

![Modificar a interface do usuário de permissões](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Erro: O locatário não é B2C ou não tem uma licença Premium

O acesso a relatórios de entrada requer uma licença do Azure Active Directory Premium 1 (P1). Se essa mensagem de erro for exibida quando você acessar as entradas, verifique se o locatário está licenciado com uma licença do Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Erro: O usuário não está nas funções permitidas 

Se essa mensagem de erro for exibida quando você tentar acessar os logs de auditoria ou as entradas usando a API, verifique se sua conta faz parte da função **Leitor de segurança** ou **Leitor de relatório** no locatário do Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Erro: O aplicativo não tem a permissão 'Ler dados do diretório' do AAD 

Siga as etapas nos [Pré-requisitos para acessar a API de relatório do Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) para garantir que seu aplicativo esteja em execução com o conjunto certo de permissões. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Erro: O aplicativo não tem a permissão 'Ler todos os dados do log de auditoria' da API do MSGraph

Siga as etapas nos [Pré-requisitos para acessar a API de relatório do Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) para garantir que seu aplicativo esteja em execução com o conjunto certo de permissões. 

## <a name="next-steps"></a>Próximas etapas

[Usar a referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[Usar a referência de API de relatório de atividades de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
