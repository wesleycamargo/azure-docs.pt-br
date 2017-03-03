---
title: "Segurança de Dados da Central de Segurança do Azure | Microsoft Docs"
description: "Este documento explica como os dados são gerenciados e protegidos na Central de Segurança do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 9852981e530cd147c2d34ac2ede251b58a167a0a
ms.openlocfilehash: 5c030f463b21284c15752cf95aa1f9a75f17ffb0
ms.lasthandoff: 02/07/2017


---
# <a name="azure-security-center-data-security"></a>Segurança dos Dados da Central de Segurança do Azure
Para ajudar os clientes a evitarem, detectarem e responderem às ameaças, a Central de Segurança do Azure coleta e processa dados relacionados à segurança, incluindo informações da configuração, metadados, logs de eventos, arquivos de despejo corrompidos e mais. A Microsoft obedece às diretrizes rígidas de conformidade e segurança — da codificação à operação de um serviço.

Este artigo explica como os dados são gerenciados e protegidos na Central de Segurança do Azure.


## <a name="data-sources"></a>Fontes de dados
A Central de Segurança do Azure analisa os dados das seguintes fontes para fornecer visibilidade sobre o estado da segurança, identificar as vulnerabilidades e recomendar atenuações, e detectar as ameaças ativas:

- Serviços do Azure: usa as informações sobre a configuração dos serviços do Azure que você implantou comunicando-se com o provedor de recursos do serviço.
- Tráfego da Rede: usa os metadados do tráfego da rede de exemplo a partir da infraestrutura da Microsoft, como a origem/IP de destino/porta, tamanho do pacote e protocolo da rede.
- Soluções de Parceiros: usa alertas de segurança das soluções de parceiros integradas, como firewalls e soluções antimalware.
- Suas Máquinas Virtuais: usa as informações da configuração e informações sobre os eventos de segurança, como eventos do Windows e logs de auditoria, logs do IIS, mensagens do syslog e arquivos de despejo corrompidos de suas máquinas virtuais.


## <a name="data-protection"></a>Proteção de dados
**Segregação dos dados**: os dados são mantidos separados logicamente em cada componente em todo o serviço. Todos os dados são marcados por organização. Essa marcação persiste em todo o ciclo de vida dos dados e é imposta em cada camada do serviço.

**Acesso a dados**: para fornecer recomendações de segurança e investigar as possíveis ameaças de segurança, os funcionários da Microsoft podem acessar as informações coletadas ou analisadas pelos serviços do Azure, incluindo arquivos de despejo de falha, eventos de criação do processo, instantâneos de disco da VM e artefatos, que podem incluir, sem querer, Dados do Cliente ou dados pessoais de suas máquinas virtuais. Seguimos os [Termos de Serviços e Política de Privacidade do Microsoft Online](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), que determinam que a Microsoft não usará os Dados do Cliente nem obterá as informações para fins comerciais ou de propaganda semelhantes. Somente usamos os Dados do Cliente conforme o necessário para fornecer os serviços do Azure, inclusive para fins compatíveis com o fornecimento desses serviços. Você mantém todos os direitos dos Dados do Cliente.

**Uso dos dados**: A Microsoft usa os padrões e a inteligência de ameaças vistos em vários locatários para aprimorar os recursos de detecção e prevenção. Fazemos isso de acordo com os compromissos de privacidade descritos em nossa [Política de Privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Local dos dados
**Sua(s) Conta(s) de Armazenamento**: uma conta de armazenamento é especificada para cada região onde as máquinas virtuais estão em execução. Isso permite que você armazene os dados na mesma região da máquina virtual na qual os dados são coletados. Esses dados, incluindo os arquivos de despejo corrompidos, serão armazenados de modo permanente em sua conta de armazenamento. Os instantâneos de disco da VM são armazenados na mesma conta de armazenamento do disco da VM.

**Armazenamento da Central de Segurança do Azure**: informações sobre os alertas de segurança, incluindo alertas de parceiros, recomendações e status de integridade da segurança são armazenadas de modo central atualmente nos Estados Unidos. Essas informações podem incluir informações de configuração relacionadas e eventos de segurança coletados de suas máquinas virtuais conforme o necessário para fornecer um alerta de segurança, recomendação ou status de integridade da segurança.

A Central de Segurança do Azure coleta as cópias transitórias dos seus arquivos de despejo corrompidos e analisa-as para obter evidências das tentativas de exploração e comprometimentos bem-sucedidos. A Central de Segurança do Azure executa essa análise na mesma área geográfica do espaço de trabalho e exclui as cópias transitórias quando a análise é concluída.

Os artefatos da máquina são armazenados de modo central na mesma região da VM.


## <a name="managing-data-collection-from-virtual-machines"></a>Gerenciar a coleta de dados das máquinas virtuais
Quando você escolhe habilitar a Central de Segurança do Azure, a coleta de dados é ativada para cada uma de suas assinaturas. Você também pode ativar a coleta de dados na seção Política de Segurança da Central de Segurança do Azure. Quando a Coleta de dados é ativada, a Central de Segurança do Azure provisiona o Agente de Monitoramento do Azure em todas as máquinas virtuais existentes com suporte e as novas criadas. A extensão do Monitoramento de Segurança do Azure examina várias configurações e eventos relacionados à segurança nos rastreamentos [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (Rastreamento de Eventos para Windows). Além disso, o sistema operacional irá gerar eventos do log de eventos no decorrer da execução da máquina. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP, usuário registrado e ID do locatário. O agente de monitoramento do Azure lê as entradas do registro de eventos e os vestígios de ETW e os copia para sua conta de armazenamento para análise.

Você pode desabilitar a coleta de dados das máquinas virtuais a qualquer momento, o que removerá os Agentes de Monitoramento instalados anteriormente pela Central de Segurança do Azure. Os instantâneos de disco da VM e a coleção de artefatos ainda serão habilitados mesmo que a coleta de dados tenha sido desabilitada.


## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu como os dados são gerenciados e protegidos na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, consulte:

* [Guia de Operações e Planejamento da Central de Segurança do Azure](security-center-planning-and-operations-guide.md) : saiba como planejar e entender as considerações de design para adotar a Central de Segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e responder aos alertas de segurança
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre as perguntas frequentes sobre como usar o serviço
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure

