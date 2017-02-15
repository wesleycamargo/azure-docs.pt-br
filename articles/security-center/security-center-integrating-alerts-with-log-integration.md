---
title: "Integrando alertas da Central de Segurança do Azure com a integração de log do Azure (Versão prévia) | Microsoft Docs"
description: "Este artigo ajuda você a integrar alertas da Central de Segurança com a integração de log do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: f946ef41f0fc5ab39351d85c37ed24715b92b882
ms.openlocfilehash: cd58f24c356e6d1ec0ebe77b1c336ee8370a4a9a


---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration-preview"></a>Integrando alertas da Central de Segurança do Azure com a integração de log do Azure (Versão prévia)
Muitas operações de segurança e equipes de resposta a incidentes contam com uma solução SIEM (Gerenciamento de eventos e informações de segurança) como ponto de partida para separação e investigação de alertas de segurança. Com a integração de log do Azure, os clientes podem sincronizar alertas da Central de Segurança e eventos de segurança da máquina virtual, coletados pelo Diagnóstico do Azure e pelos Logs de Auditoria do Azure, com suas soluções de análise de log e SIEM quase em tempo real.

A integração do log do Azure funciona com a HP ArcSight, Splunk, IBM Qradar e outros.

## <a name="what-logs-can-i-integrate"></a>Quais logs posso integrar?
Azure produz um log abrangente para cada serviço. Esses logs são categorizados como:

* **Logs de Controle/Gerenciamento**, que fornecem visibilidade nas operações CREATE, UPDATE e DELETE do Azure Resource Manager.
* **Logs de Plano de Dados** que fornecem visibilidade nos eventos gerados ao usar um recurso do Azure. Um exemplo é o Log de Eventos do Windows - os logs de segurança e do aplicativo em uma máquina virtual.

A integração do log do Azure atualmente suporta a integração de:

* Logs da VM do Azure
* Logs de Auditoria do Azure
* Alertas da Central de Segurança do Azure

## <a name="install-azure-log-integration"></a>Instalar a integração do log do Azure
Baixe a [integração do log do Azure](https://www.microsoft.com/download/details.aspx?id=53324).

O serviço de integração do log do Azure coleta os dados de telemetria do computador no qual está instalado.  Os dados de telemetria coletados são:

* Exceções que ocorrem durante a execução da integração do log do Azure
* Métricas sobre o número de consultas e eventos processados
* Estatísticas sobre quais opções da linha de comando do Azlog.exe estão sendo usadas

> [!NOTE]
> Você pode desativar a coleta dos dados de telemetria desmarcando essa opção.
>
>

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrar os Logs de Auditoria do Azure e os alertas da Central de Segurança
1. Abra o prompt de comando e **cd** em **c:\Program Files\Microsoft Azure Log Integration**.
2. Execute o comando **azlog createazureid** para criar uma [Entidade de serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md) nos locatários do Azure Active Directory (AD) que hospedam as assinaturas do Azure.

    Você deverá inserir seu logon do Azure.

   > [!NOTE]
   > Você deve ser o Proprietário da assinatura ou um Coadministrador da assinatura.
   >
   >

    A autenticação do Azure é feita por meio do AD do Azure.  A criação de uma entidade de serviço para a integração de log do Azure criará a identidade do Azure AD que receberá o acesso de leitura das assinaturas do Azure.
3. Execute o comando **azlog authorize <SubscriptionID>** para atribuir acesso de Leitor na assinatura à entidade de serviço criada na etapa 2. Se você não especificar uma **SubscriptionID**, a entidade de serviço será atribuída à função de Leitor para todas as assinaturas às quais você tem acesso.

   > [!NOTE]
   > Você poderá ver avisos se executar o comando **authorize** imediatamente após o comando **createazureid**. Há alguma latência entre quando a conta do Azure AD é criada e quando a conta está disponível para uso. Se você esperar cerca de 10 segundos depois de executar o comando **createazureid** para executar o comando **authorize**, esses avisos não serão exibidos.
   >
   >
4. Verifique as seguintes pastas para confirmar se os arquivos JSON do Log de auditoria existem:

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. Verifique as seguintes pastas para confirmar se os alertas da Central de Segurança existem:

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. Aponte o conector de encaminhamento de arquivos SIEM padrão para a devida pasta para enviar os dados para a instância SIEM. Consulte [Configurações do SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) na configuração do SIEM.

Se você tiver dúvidas sobre a Integração do Log do Azure, envie um email para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Logs de Auditoria do Azure e as definições das propriedades, consulte:

* [Operações de auditoria com o Gerenciador de Recursos](../resource-group-audit.md)
* [Lista os eventos de gerenciamento em uma assinatura](https://msdn.microsoft.com/library/azure/dn931934.aspx) : para recuperar os eventos do log de auditoria.

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e responder aos alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.



<!--HONumber=Dec16_HO2-->


