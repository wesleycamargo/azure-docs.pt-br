---
title: Monitorando ameaças no Armazenamento do Azure
description: Configure a Proteção avançada contra ameaças do Armazenamento do Azure para detectar anomalias na atividade da conta e notificá-lo de eventuais tentativas prejudiciais de acessar sua conta.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 09/24/2018
ms.author: ronmat
ms.manager: shaik
ms.openlocfilehash: 8b2ca2d5d6418d68cab847df80fc437e468249ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995628"
---
# <a name="azure-storage-advanced-threat-protection"></a>Proteção avançada contra ameaças do Armazenamento do Azure

A Proteção avançada contra ameaças do Armazenamento do Azure detecta anomalias na atividade da conta e notificá-lo de eventuais tentativas prejudiciais de acessar sua conta. Essa camada de proteção permite que você resolva as ameaças sem precisar ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança.

As ameaças são exibidas definindo alertas de segurança disparados quando ocorrem anomalias na atividade. Esses alertas integram-se à [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) que incluem detalhes de atividades suspeitas e recomendações sobre como investigar e corrigir ameaças. 

> [!NOTE]
> A Proteção avançada contra ameaças do Armazenamento do Azure está disponível no momento apenas para o serviço Blob. Os alertas de segurança são integrados à Central de Segurança do Azure e enviados por email a administradores de assinatura.

A Proteção avançada contra ameaças do Armazenamento do Azure ingere logs de diagnóstico de solicitações de leitura, gravação e exclusão para o serviço Blob para detecção de ameaças. Para investigar os alertas da Proteção avançada contra ameaças, é necessário [configurar logs de diagnóstico](storage-monitor-storage-account.md#configure-logging) para habilitar todos os níveis de logs para o serviço Blob.

## <a name="set-up-advanced-threat-protection-in-the-portal"></a>Configurar a proteção avançada contra ameaças no portal

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

2. Navegue até a página de configuração da conta de Armazenamento do Azure que você deseja proteger. Na página **Configurações**, selecione **Proteção avançada contra ameaças**.

3. Na folha de configuração **Proteção avançada contra ameaças**
    * **ATIVAR** *Proteção avançada* contra ameaças
    * Clique em **Salvar** para salvar a política de Proteção avançada contra ameaças nova ou atualizada.

![Ativar a proteção avançada contra ameaças do Armazenamento do Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## <a name="explore-anomalies"></a>Explorar anomalias

Quando ocorrerem anomalias na atividade de armazenamento, você receberá uma notificação por email com informações sobre o evento de segurança suspeito. Os detalhes do evento incluem:

* natureza da anomalia
* nome da conta de armazenamento
* tipo de armazenamento
* horário do evento

O email também inclui detalhes sobre possíveis causas e ações recomendadas para investigar e atenuar a ameaça potencial.

![O Armazenamento do Azure aprimorou o email do alerta de proteção contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

É possível examinar e gerenciar seus alertas de segurança atuais no [bloco Alertas de segurança](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts) da Central de Segurança do Azure. Clicar em um alerta específico fornece detalhes e ações para investigar a ameaça atual e corrigir ameaças futuras.

![O Armazenamento do Azure aprimorou o email do alerta de proteção contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alertas de proteção

Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Esses eventos podem disparar os alertas a seguir:

* **Acesso de um local incomum**: este alerta é disparado quando há uma alteração no padrão de acesso para uma conta de armazenamento. Por exemplo, quando alguém acessou uma conta de armazenamento de um local geográfico incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou operação de manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (ex-funcionário, invasor externo, etc.).

* **Extração de dados incomum**: este alerta é disparado quando há uma alteração no padrão de extração de dados de uma conta de armazenamento. Por exemplo, se alguém tiver acessado uma quantidade incomum de dados em uma conta de armazenamento. Em alguns casos, o alerta detecta uma ação legítima (atividade de manutenção). Em outros casos, o alerta detecta uma ação mal-intencionada (violação/vazamento de dados, transferência não autorizada de dados).

* **Acesso anônimo incomum**: este alerta é disparado quando há uma alteração no padrão de acesso para uma conta de armazenamento. Por exemplo, suponha que alguém acessou uma conta de armazenamento anonimamente. Em alguns casos, o alerta detecta um acesso legítimo que usa o acesso de leitura público. Em outros casos, o alerta detecta o acesso não autorizado que explora o acesso de leitura público para um contêiner e seus blobs.

* **Exclusão inesperada:** este alerta é disparado quando uma ou mais operações de exclusão inesperadas ocorrem em uma conta de armazenamento, com base na análise histórica dessa conta. Por exemplo, suponha que alguém executou uma operação *DeleteBlob* usando um novo aplicativo e de um novo endereço IP. Em alguns casos, o alerta detecta uma ação legítima (o administrador usou um navegador diferente quando estava em uma viagem de negócios). Em outros casos, o alerta detecta uma ação mal-intencionada (um invasor que exclui dados). 
 
* **Alteração na permissão de acesso:** este alerta é disparado quando há uma alteração inesperada de permissão de acesso para uma conta de armazenamento. Por exemplo suponha que alguém alterou a permissão de acesso para uma conta de armazenamento usando um novo aplicativo e de um novo endereço IP. Em alguns casos, o alerta detecta uma ação legítima (o administrador usou um navegador diferente quando estava em uma viagem de negócios). Em outros casos, o alerta detecta uma ação mal-intencionada (por exemplo, um invasor que aumenta os privilégios de uma conta à qual eles obtiveram acesso). 

* **Carregar pacote do Serviço de Nuvem do Azure:** este alerta é disparado quando há um upload inesperado de um pacote do Serviço de Nuvem do Azure (arquivo *.cspkg*) para uma conta de armazenamento. Por exemplo, suponha que um arquivo *.cspkg* foi carregado de um novo endereço IP. Em algum caso, o alerta detecta uma ação legítima. Em outros casos, o alerta detecta uma ação mal-intencionada (por exemplo, um pacote de Serviço de Nuvem foi carregado na preparação de uma implantação de um serviço mal-intencionado).    
   

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Logs em contas de Armazenamento do Azure ](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Saiba mais sobre a [Central de Segurança do Azure](../../security-center/security-center-intro.md)