---
title: Proteção Avançada contra Ameaças do Armazenamento do Azure
description: Configure a Proteção avançada contra ameaças do Armazenamento do Azure para detectar anomalias na atividade da conta e notificá-lo de eventuais tentativas prejudiciais de acessar sua conta.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: monhaber
ms.manager: shaik
ms.openlocfilehash: 78338ece1bc70d8410bd71183a34aaf1a52f2d1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484097"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Proteção Avançada contra Ameaças do Armazenamento do Azure

A Proteção Avançada contra Ameaças do Armazenamento do Azure oferece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Essa camada de proteção permite que você resolva as ameaças sem precisar ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança. 

Alertas de segurança são disparados quando ocorrem de anomalias na atividade.  Esses alertas de segurança são integradas [Central de segurança do Azure](https://azure.microsoft.com/services/security-center/)e também são enviados por email para os administradores de assinatura, com detalhes da atividade suspeita e recomendações sobre como investigar e corrigir ameaças.

> [!NOTE]
> * Proteção avançada contra ameaças do armazenamento do Azure está disponível atualmente apenas para o armazenamento de BLOBs.
> * Para obter detalhes, incluindo uma avaliação gratuita de 30 dias, preço, consulte o [página de preços do Azure Security Center]( https://azure.microsoft.com/en-us/pricing/details/security-center/).
> * ATP para o recurso de armazenamento do Azure atualmente não está disponível nas regiões de nuvem soberana e o Azure governamental.

Proteção avançada contra ameaças do armazenamento do Azure ingere os logs de diagnóstico de leitura, gravação e solicitações de exclusão para o armazenamento de Blob para detecção de ameaças. Para investigar os alertas de proteção avançada contra ameaças, você pode exibir a atividade de armazenamento relacionados usando o log de análise de armazenamento. Para obter mais informações, consulte como [configurar o log de análise de armazenamento](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Configurar a proteção avançada contra ameaças 

### <a name="using-the-portal"></a>Usando o portal

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

2. Navegue até a página de configuração da conta de Armazenamento do Azure que você deseja proteger. Na página **Configurações**, selecione **Proteção avançada contra ameaças**.

3. Na folha de configuração **Proteção avançada contra ameaças**
    * **ATIVAR** *Proteção avançada* contra ameaças
    * Clique em **Salvar** para salvar a política de Proteção avançada contra ameaças nova ou atualizada. (Os preços na imagem por exemplo são apenas para fins de).

![Ativar a proteção avançada contra ameaças do Armazenamento do Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Como usar a Central de Segurança do Azure
Quando você se inscreve para a camada Standard na Central de segurança do Azure, proteção avançada contra ameaças é definida em suas contas de armazenamento. Para obter mais informações, consulte [atualizar para a camada Standard da Central de segurança para melhorar a segurança](https://docs.microsoft.com/azure/security-center/security-center-pricing). (Os preços na imagem por exemplo são apenas para fins de).

![Camada Standard no ASC](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>Usando modelos do Gerenciador de Recursos do Azure

Use um modelo do Azure Resource Manager para implantar uma conta de armazenamento do Azure com proteção avançada contra ameaças habilitada.
Para obter mais informações, consulte [conta de armazenamento com proteção avançada contra ameaças](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Usando a política do Azure

Use uma política do Azure para habilitar a proteção avançada contra ameaças em contas de armazenamento em um grupo específico de recursos ou assinatura.

1. O Azure de almoço **política - definições** página.

1. Pesquise o **implantar proteção avançada contra ameaças em contas de armazenamento** política.

     ![Política de pesquisa](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Selecione um grupo de recursos ou assinatura do Azure.

    ![Selecione a assinatura ou grupo](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Atribua a política.

    ![Página de definições de política](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Usando a API REST
Use os comandos de API Rest para criar, atualizar ou obter a configuração de proteção avançada contra ameaças para uma conta de armazenamento específico.

* [Proteção avançada contra ameaças - criar](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Proteção avançada contra ameaças – Introdução](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Use os seguintes cmdlets do PowerShell:

  * [Habilitar a proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Obtenha proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Desabilitar a proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Explore anomalias de segurança

Quando ocorrerem anomalias na atividade de armazenamento, você receberá uma notificação por email com informações sobre o evento de segurança suspeito. Os detalhes do evento incluem:

* A natureza da anomalia
* o nome da conta de armazenamento
* A hora do evento
* O tipo de armazenamento
* As possíveis causas 
* As etapas de investigação
* As etapas de correção


O email também inclui detalhes sobre possíveis causas e ações recomendadas para investigar e atenuar a ameaça potencial.

![O Armazenamento do Azure aprimorou o email do alerta de proteção contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

É possível examinar e gerenciar seus alertas de segurança atuais no [bloco Alertas de segurança](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts) da Central de Segurança do Azure. Clicar em um alerta específico fornece detalhes e ações para investigar a ameaça atual e corrigir ameaças futuras.

![O Armazenamento do Azure aprimorou o email do alerta de proteção contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alertas de proteção

Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Esses eventos podem disparar os alertas a seguir:

### <a name="anomalous-access-pattern-alerts"></a>Alertas de padrão de acesso anormais

* **Acesso a partir de um local incomum**: Este alerta é disparado quando há uma alteração no padrão de acesso para uma conta de armazenamento. Por exemplo, quando alguém acessou uma conta de armazenamento de um local geográfico incomum.
Possíveis causas:
   * Um invasor tem acesso à sua conta de armazenamento
   * Um usuário legítimo acessou sua conta de armazenamento de um novo local
 
* **Aplicativo anomalias**: Esse alerta indica que um aplicativo incomum acessou esta conta de armazenamento. Possíveis causas:
   * Um invasor acessou sua conta de armazenamento usando um novo aplicativo.
   * Um usuário legítimo usou um novo aplicativo/navegador para acessar sua conta de armazenamento.

* **Acesso anônimo**: Esse alerta indica que há uma alteração no padrão de acesso para uma conta de armazenamento. Por exemplo, essa conta tiver sido acessado anonimamente (ou seja, sem qualquer autenticação), que é esperado em comparação com o padrão de acesso recente nessa conta.
Possíveis causas:
   * Um invasor explorar com acesso de leitura público para um contêiner.
   * Um usuário legítimo ou aplicativo tem usado o acesso de leitura público para um contêiner.

### <a name="anomalous-extractupload-alerts"></a>Alertas de extração/upload anormais

* **Extração de dados**: Esse alerta indica que uma quantidade excepcionalmente grande de dados foram extraída em comparação com a atividade recente neste contêiner de armazenamento. Possíveis causas:
   * Um invasor tem extraído uma grande quantidade de dados de um contêiner. (Por exemplo: violação/vazamento de dados, a transferência não autorizada de dados)
   * Um usuário legítimo ou o aplicativo tenha extraído uma quantidade incomum de dados de um contêiner. (Por exemplo: atividade de manutenção)

* **Excluir inesperado**: Esse alerta indica que uma ou mais operações de exclusão inesperado ocorreu em uma conta de armazenamento, em comparação com a atividade recente nessa conta. Possíveis causas:
   * Um invasor excluiu dados de sua conta de armazenamento.
   * Um usuário legítimo executou uma exclusão incomuns.

* **Carregar pacote de serviço de nuvem do Azure**: Esse alerta indica que um pacote de serviço de nuvem do Azure (arquivo. cspkg) foi carregado para uma conta de armazenamento de maneira incomum, em comparação comparada a atividade recente nessa conta. Possíveis causas: 
   * Um invasor Preparando-se para implantar um código mal-intencionado da sua conta de armazenamento para um serviço de nuvem do Azure.
   * Um usuário legítimo Preparando-se para uma implantação de serviço legítimos.

### <a name="suspicious-storage-activities-alerts"></a>Alertas de atividades suspeitas de armazenamento

* **Alteração de permissão de acesso**: Esse alerta indica que as permissões de acesso desse contêiner de armazenamento foram alteradas de maneira incomum. Possíveis causas: 
   * Um invasor mudou permissões de contêiner para diminuir o nível de sua segurança.
   * Um usuário legítimo mudou as permissões do contêiner.

* **Acessar inspeção**: Esse alerta indica que as permissões de acesso de uma conta de armazenamento tem sido inspecionadas de maneira incomum, em comparação comparada a atividade recente nessa conta. Possíveis causas: 
   * O invasor executou o reconhecimento de um ataque futuro.
   * Um usuário legítimo executou a manutenção na conta de armazenamento.

* **Exploração de dados**: Esse alerta indica que blobs ou contêineres em uma conta de armazenamento foram enumerados de maneira incomum, em comparação comparada a atividade recente nessa conta. Possíveis causas: 
   * O invasor executou o reconhecimento de um ataque futuro.
   * Um usuário legítimo ou lógica do aplicativo explorou os dados na conta de armazenamento.






## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [registra em log nas contas de armazenamento do Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Saiba mais sobre a [Central de Segurança do Azure](../../security-center/security-center-intro.md)
