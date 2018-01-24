---
title: Solucionar problemas do agente do Linux para o Azure Log Analytics | Microsoft Docs
description: "Descreva os sintomas, as causas e resolução dos problemas mais comuns com o agente do Linux para o Log Analytics."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2017
ms.author: magoedte
ms.openlocfilehash: 5f598da9b82b4425ca509a26a2e6e366ba4c3394
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2017
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>Como solucionar problemas com o agente do Linux para o Log Analytics

Este artigo fornece ajuda para solucionar erros que podem ser encontrados no agente do Linux para o Log Analytics e sugere possíveis soluções para resolvê-los.

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problema: não é possível se conectar por meio do proxy ao Log Analytics

### <a name="probable-causes"></a>Causas prováveis
* O proxy especificado durante a integração estava incorreto
* Os pontos de extremidade de serviço do Log Analytics e da Automação do Azure não estão na lista de permissões em seu datacenter 

### <a name="resolutions"></a>Resoluções
1. Reintegre-se ao serviço Log Analytics com o Agente do OMS para Linux usando o comando a seguir com a opção `-v` habilitada. Isso permite a saída detalhada do agente que está se conectando por meio do proxy ao Serviço OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Examine a seção [Configurando o agente para ser usado com um servidor proxy ou um gateway do OMS](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway) para verificar se você configurou corretamente o agente para se comunicar por meio de um servidor proxy.    
* Verifique uma segunda vez se os seguintes pontos de extremidade do serviço Log Analytics estão na lista de permissões:

    |Recurso de agente| Portas |  
    |------|---------|  
    |*.ods.opinsights.azure.com | Porta 443|   
    |*.oms.opinsights.azure.com | Porta 443|   
    |ods.systemcenteradvisor.com | Porta 443|   
    |*.blob.core.windows.net/ | Porta 443|   

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: você recebe um erro 403 quando tentar integrar

### <a name="probable-causes"></a>Causas prováveis
* A data e a hora estão incorretas no servidor Linux 
* A ID e a Chave do Espaço de Trabalho usadas estão incorretas

### <a name="resolution"></a>Resolução

1. Verifique a hora no servidor Linux com o comando date. Se a hora for +/-15 minutos da hora atual, a integração falhará. Para corrigir esse problema, atualize a data e/ou o fuso horário do servidor Linux. 
2. Verifique se você instalou a última versão do Agente do OMS para Linux.  A versão mais recente agora notifica se a distorção de horário está causando a falha de integração.
3. Reintegre usando a ID do Espaço de Trabalho e a Chave do Espaço de Trabalho corretas seguindo as instruções de instalação descritas anteriormente neste tópico.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Você vê um erro 404 e 500 no arquivo de log logo após a integração
Esse é um problema conhecido que ocorre durante o primeiro upload de dados do Linux em um espaço de trabalho do Log Analytics. Isso não afeta os dados sendo enviados ou a experiência do serviço.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problema: você não está vendo nenhum dado no Portal do Azure

### <a name="probable-causes"></a>Causas prováveis

- Falha na integração com o serviço Log Analytics
- A conexão com o serviço Log Analytics está bloqueada
- Os dados do Agente do OMS para Linux têm o backup realizado

### <a name="resolutions"></a>Resoluções
1. Verifique se a integração do serviço Log Analytics foi bem-sucedida verificando se os seguintes arquivos existem: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reintegração usando as instruções de linha de comando do `omsadmin.sh`
3. Se estiver usando um proxy, consulte as etapas de resolução de proxy fornecidas anteriormente.
4. Em alguns casos, quando o Agente do OMS para Linux não pode se comunicar com o serviço, os dados no agente são enfileirados até todo o tamanho do buffer, que é 50 MB. O Agente do OMS para Linux deve ser reiniciado executando o seguinte comando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Esse problema foi corrigido nas versões 1.1.0-28 e posteriores do Agente.

