---
title: Diagrama de processamento de pagamento do Azure - Requisitos de monitoramento
description: Requisito 10 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5fa1d17e68ce04b1f67081479518279be6cca099
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>Requisitos de monitoramento para ambientes em conformidade com o PCI DSS 
## <a name="pci-dss-requirement-10"></a>Requisito 10 de PCI DSS

**Controlar e monitorar todo o acesso aos recursos da rede e aos dados do titular do cartão**

> [!NOTE]
> Esses requisitos são definidos pelo [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) como parte do [Padrão de Segurança de Dados PCI (PCI DSS), Versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre procedimentos de teste e diretrizes para cada requisito.

Os mecanismos de registro em log e a capacidade de monitorar as atividades do usuário são essenciais na prevenção, detecção ou redução do impacto de um comprometimento de dados. A presença de logs em todos os ambientes permite um controle e análise completos e alertas quando algo dá errado. Determinar a causa de um comprometimento é muito difícil, talvez até impossível, sem os logs de atividade do sistema.

## <a name="pci-dss-requirement-101"></a>Requisito 10.1 de PCI DSS

**10.1** Implementar trilhas de auditoria para vincular todo o acesso aos componentes do sistema a cada usuário individual.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure restringe o acesso a ferramentas administrativas e de diagnóstico a pessoal autorizado com responsabilidade em relação ao trabalho em questão. O Microsoft Azure restringe o acesso privilegiado às ferramentas usadas no ambiente de produção com base em princípios de privilégios mínimos. O Microsoft Azure registra e mantém um log de todo o acesso de usuário individual a componentes do sistema do Microsoft Azure no ambiente de plataforma.<br /><br />Os componentes da plataforma Microsoft Azure (incluindo o sistema operacional, o CloudNet, o Fabric e assim por diante) são configurados para registrar em log e coletar eventos de segurança. A atividade do administrador na plataforma Microsoft Azure é registrada. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore tem um registro em log extensivo de todo o sistema e as atividades de usuário (incluindo registro em log de CHD). Para saber mais, confira [Diretriz de PCI - registro em log](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-102"></a>Requisito 10.2 de PCI DSS

**10.2** Implementar trilhas de auditoria automatizadas para todos os componentes do sistema a fim de reconstruir os seguintes eventos:
- **10.2.1** Todos os acessos de usuário individual aos dados
- **10.2.2** Todas as ações realizadas por qualquer pessoa com privilégios de administrador ou raiz
- **10.2.3** Acesso a todos os registros de auditoria
- **10.2.4** Tentativas inválidas de acesso lógico
- **10.2.5** Uso e alterações em mecanismos de autenticação e identificação, incluindo, dentre outros, a criação de novas contas e a elevação de privilégios, e todas as alterações, adições ou exclusões em contas raiz ou com privilégios de administrador
- **10.2.6** Inicialização, interrupção ou pausa dos logs de auditoria
- **10.2.7** Criação e exclusão de objetos no nível do sistema

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure restringe o acesso a ferramentas administrativas e de diagnóstico a pessoal autorizado com responsabilidade em relação ao trabalho em questão. O Microsoft Azure restringe o acesso privilegiado às ferramentas usadas no ambiente de produção com base em princípios de privilégios mínimos. O Microsoft Azure registra e mantém um log de todo o acesso de usuário individual a componentes do sistema do Microsoft Azure no ambiente de plataforma.<br /><br />Os componentes da plataforma Microsoft Azure (incluindo o sistema operacional, o CloudNet, o Fabric e assim por diante) são configurados para registrar em log e coletar eventos de segurança. A atividade do administrador na plataforma Microsoft Azure é registrada. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore tem um registro em log extensivo de todo o sistema e atividades de usuário, incluindo registro em log de CHD. Para saber mais, confira [Diretriz de PCI - registro em log](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-103"></a>Requisito 10.3 de PCI DSS

**10.3** Registrar pelo menos as seguintes entradas de trilha de auditoria para todos os componentes do sistema a cada evento:
- **10.3.1** Identificação de usuário
- **10.3.2** Tipo de evento
- **10.3.3** Data e hora
- **10.3.4** Indicação de sucesso ou falha
- **10.3.5** Origem do evento
- **10.3.6** Identidade ou nome dos dados, do componente do sistema ou do recurso afetados.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure estabeleceu procedimentos para sincronizar servidores e dispositivos de rede no ambiente do Microsoft Azure com servidores de horário NTP Camada 1 sincronizados aos satélites de GPS (Sistema de Posicionamento Global). A sincronização é executada automaticamente a cada cinco minutos. O Microsoft Azure é responsável por fazer com que os hosts de serviço sincronizem o horário adequadamente. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore registra a identificação do usuário, o tipo de evento, o carimbo de data, os eventos de falha de sucesso, a origem do evento e o nome do recurso, conforme exigido pelo Controle 10.3.|



## <a name="pci-dss-requirement-104"></a>Requisito 10.4 de PCI DSS

**10.4** Usando a tecnologia de sincronização de hora, sincronizar todos os relógios e as horas do sistema crítico e verificar se os itens abaixo foram implementados para adquirir, distribuir e armazenar as horas. 
> [!NOTE]
> Um exemplo de tecnologia de sincronização de hora é o protocolo NTP (Network Time).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure estabeleceu procedimentos para sincronizar servidores e dispositivos de rede no ambiente do Microsoft Azure com servidores de horário NTP Camada 1 sincronizados aos satélites de GPS (Sistema de Posicionamento Global). A sincronização é executada automaticamente a cada cinco minutos. O Microsoft Azure é responsável por fazer com que os hosts de serviço sincronizem o horário adequadamente. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A sincronização de hora para o serviço PaaS é executada pelo Azure.|



### <a name="pci-dss-requirement-1041"></a>Requisito 10.4.1 de PCI DSS

**10.4.1** Os sistemas críticos estão com a hora correta e consistente.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A sincronização de hora para o serviço PaaS é executada pelo Azure.|



### <a name="pci-dss-requirement-1042"></a>Requisito 10.4.2 de PCI DSS

**10.4.2** Os dados de hora estão protegidos.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A sincronização de hora para o serviço PaaS é executada pelo Azure.|



### <a name="pci-dss-requirement-1043"></a>Requisito 10.4.3 de PCI DSS

**10.4.3** As configurações de hora são recebidas de fontes de horário aceitas pelo setor.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A sincronização de hora para o serviço PaaS é executada pelo Azure.|



## <a name="pci-dss-requirement-105"></a>Requisito 10.5 de PCI DSS

**10.5** Garantir trilhas de auditoria de segurança para que elas não possam ser alteradas.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | As ferramentas de IDS e FIM são implementadas no ambiente do Microsoft Azure. O Microsoft Azure usa EWS para dar suporte à análise de eventos em tempo real em seu ambiente operacional. MAs e AIMS geram alertas quase em tempo real sobre eventos que possam comprometer o sistema. <br /><br />O registro em log de eventos de serviço, usuário e segurança (logs do servidor Web, logs do servidor FTP e assim por diante) é habilitado e mantido centralmente. O Azure restringe o acesso a logs de auditoria ao pessoal autorizado com base nas responsabilidades em relação ao trabalho. Os logs de eventos são arquivados na infraestrutura de arquivamento segura do Azure e mantidos por 180 dias. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cuida da auditoria de todos os elementos do OMS. O backup em uma fonte externa pode ser executado pelo [Backup do Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1051"></a>Requisito 10.5.1 de PCI DSS

**10.5.1** Limitar a visualização das trilhas de auditoria para as pessoas que tenham essa necessidade de trabalho.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cuida da auditoria de todos os elementos do OMS. O backup em uma fonte externa pode ser executado pelo [Backup do Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1052"></a>Requisito 10.5.2 de PCI DSS

**10.5.2** Proteger arquivos de trilha de auditoria de modificações não autorizadas.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cuida da auditoria de todos os elementos do OMS. O backup em uma fonte externa pode ser executado pelo [Backup do Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1053"></a>Requisito 10.5.3 de PCI DSS

**10.5.3** Fazer backup de arquivos de trilha de auditoria imediatamente para um servidor de registro centralizado ou mídia que seja difícil de alterar.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cuida da auditoria de todos os elementos do OMS. O backup em uma fonte externa pode ser executado pelo [Backup do Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1054"></a>Requisito 10.5.4 de PCI DSS

**10.5.4** Gravar logs para tecnologias externas em um dispositivo de mídia ou servidor de log seguro, centralizado e interno.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cuida da auditoria de todos os elementos do OMS. O backup em uma fonte externa pode ser executado pelo [Backup do Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1055"></a>Requisito 10.5.5 de PCI DSS

**10.5.5** Usar software de monitoramento de integridade de arquivos ou detecção de mudanças em logs para fazer com que os dados de log existentes não possam ser alterados sem gerar alertas (embora a adição de novos dados não deva causar um alerta).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore cuida da auditoria de todos os elementos do OMS. O backup em uma fonte externa pode ser executado pelo [Backup do Azure](https://azure.microsoft.com/services/backup/).|



## <a name="pci-dss-requirement-106"></a>Requisito 10.6 de PCI DSS

**10.6** Examinar logs e eventos de segurança para todos os componentes do sistema a fim de identificar anomalias ou atividades suspeitas.
 
> [!NOTE]
> As ferramentas de coleta, análise e alertas de logs podem ser usadas para atender a esse requisito.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | As ferramentas de IDS e FIM são implementadas no ambiente do Microsoft Azure. O Microsoft Azure usa EWS para dar suporte à análise de eventos em tempo real em seu ambiente operacional. MAs e AIMS geram alertas quase em tempo real sobre eventos que possam comprometer o sistema. <br /><br />O registro em log de eventos de serviço, usuário e segurança (logs do servidor Web, logs do servidor FTP e assim por diante) é habilitado e mantido centralmente. O Azure restringe o acesso a logs de auditoria ao pessoal autorizado com base nas responsabilidades em relação ao trabalho. Os logs de eventos são arquivados na infraestrutura de arquivamento segura do Azure e mantidos por 180 dias. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) para monitorar, relatar e evitar anomalias. O [Assistente do Azure](/azure/advisor/advisor-security-recommendations) fornece uma exibição consistente e consolidada de recomendações para todos os recursos do Azure.|



### <a name="pci-dss-requirement-1061"></a>Requisito 10.6.1 de PCI DSS

**10.6.1** Examinar diariamente pelo menos o seguinte:
- Todos os eventos de segurança
- Logs de todos os componentes do sistema que armazenam, processam ou transmitem CHD e/ou SAD
- Logs de todos os componentes essenciais do sistema
- Logs de todos os servidores e componentes do sistema que executam funções de segurança (por exemplo, firewalls, IDS/IPS (sistemas de detecção de invasão/sistemas de prevenção de invasão), servidores de autenticação, servidores de redirecionamento de comércio eletrônico e assim por diante).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) para monitorar, relatar e evitar anomalias. O [Assistente do Azure](/azure/advisor/advisor-security-recommendations) fornece uma exibição consistente e consolidada de recomendações para todos os recursos do Azure.|



### <a name="pci-dss-requirement-1062"></a>Requisito 10.6.2 de PCI DSS

**10.6.2** Examinar os logs de todos os outros componentes do sistema periodicamente com base nas políticas da organização e na estratégia de gerenciamento de risco, conforme determinado pela avaliação de risco anual da organização.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) para monitorar, relatar e evitar anomalias. O [Assistente do Azure](/azure/advisor/advisor-security-recommendations) fornece uma exibição consistente e consolidada de recomendações para todos os recursos do Azure.|



### <a name="pci-dss-requirement-1063"></a>Requisito 10.6.3 de PCI DSS

**10.6.3** Acompanhar exceções e anomalias identificadas durante o processo de revisão.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Confira a seção "Microsoft Azure" para ver o [Requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore usa a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) para monitorar, relatar e evitar anomalias. O [Assistente do Azure](/azure/advisor/advisor-security-recommendations) fornece uma exibição consistente e consolidada de recomendações para todos os recursos do Azure.|



## <a name="pci-dss-requirement-107"></a>Requisito 10.7 de PCI DSS

**10.7** Guardar o histórico de trilha de auditoria por pelo menos um ano, com no mínimo três meses imediatamente disponíveis para análise (por exemplo, online, arquivados ou restauráveis do backup).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure mantém os logs de auditoria por um ano, com os últimos três meses imediatamente acessíveis em seu portal interno. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore tem um registro em log extensivo de todo o sistema e as atividades de usuário (incluindo registro em log de CHD). Para saber mais, confira [Diretriz de PCI - registro em log](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-108"></a>Requisito 10.8 de PCI DSS

**10.8** **Requisitos adicionais somente para provedores de serviço:** implementar um processo para a detecção oportuna e emissão de relatórios de falhas de sistemas de controle de segurança críticos, incluindo, dentre outros, a falha de:
- Firewalls
- IDS/IPS
- FIM
- Antivírus
- Controles de acesso físicos
- Controles de acesso lógicos
- Mecanismos de registro em log de auditoria
- Controles de segmentação (se usados) 

> [!NOTE]
> Esse requisito é uma melhor prática até 31 de janeiro de 2018 e, a partir desta data, ele se tornará um requisito obrigatório.



**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure usa EWS para dar suporte à análise de eventos em tempo real em seu ambiente operacional. MAs e AIMS geram alertas quase em tempo real sobre eventos que possam comprometer o sistema. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore tem um registro em log extensivo de todo o sistema e as atividades de usuário (incluindo registro em log de CHD). Para saber mais, confira [Diretriz de PCI - registro em log](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-1081"></a>Requisito 10.8.1 de PCI DSS

**10.8.1** **Requisitos adicionais somente para provedores de serviço:** responder a falhas de controles de segurança crítica de maneira oportuna. Os processos para responder a falhas de controles de segurança devem incluir:
- Restauração de funções de segurança
- Identificação e documentação da duração (data e hora do início ao fim) da falha de segurança
- Identificação e documentação das causas da falha, incluindo a causa raiz, e documentação das atualizações necessárias para tratar da causa raiz
- Identificação e tratamento de problemas de segurança ocorridos durante a falha
- Execução de uma avaliação de risco para determinar se outras ações são necessárias como resultado da falha de segurança
- Implementação de controles para impedir a ocorrência da causa da falha; retomada do monitoramento dos controles de segurança 

> [!NOTE]
> Esse requisito é uma melhor prática até 31 de janeiro de 2018 e, a partir desta data, ele se tornará um requisito obrigatório.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | O Microsoft Azure usa EWS para dar suporte à análise de eventos em tempo real em seu ambiente operacional. MAs e AIMS geram alertas quase em tempo real sobre eventos que possam comprometer o sistema. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore tem um registro em log extensivo de todo o sistema e as atividades de usuário (incluindo registro em log de CHD). Para saber mais, confira [Diretriz de PCI - registro em log](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-109"></a>Requisito 10.9 de PCI DSS

**10.9** Garantir que políticas de segurança e procedimentos operacionais para monitorar todo o acesso aos recursos de rede e aos dados do titular do cartão estejam documentados, em uso e sejam do conhecimento de todas as partes afetadas.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI&#8209;DSS&nbsp;Diagrama)** | A Contoso Webstore fornece um caso de uso e uma descrição de como o CHD é gerenciado e protegido.|




