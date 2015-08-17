<properties
	pageTitle="Guia do recurso Insights operacionais"
	description="Insights operacionais é um serviço de análise que permite aos administradores de TI obter uma visão mais profunda entre ambientes locais e na nuvem. Ele permite interagir com os dados históricos e em tempo real de computador para rapidamente desenvolver soluções personalizadas e fornecer padrões desenvolvidos pela comunidade e pela Microsoft para análise de dados."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="banders"/>

# Guia do recurso Insights Operacionais

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Este guia ajudará você a compreender quais problemas o Insights Operacionais pode ajudá-lo a resolver, no que ele consiste e como você pode criar uma conta do Insights Operacionais e entrar no serviço.

## Transformar dados de computador

Insights operacionais é um serviço de análise que permite aos administradores de TI obter uma visão mais profunda entre ambientes locais e na nuvem. Ele permite interagir com os dados históricos e em tempo real de computador para rapidamente desenvolver soluções personalizadas e fornecer padrões desenvolvidos pela comunidade e pela Microsoft para análise de dados.

Com o Insights Operacionais, você pode transformar dados do computador em inteligência operacional com os recursos a seguir.


|**ícone** | **O que usar** | **O que ele faz**|
|---|---|---|
|![](./media/operational-insights-feature-guide/cap-plan.png) | [Planejamento da capacidade](operational-insights-capacity.md) | Você pode usar a solução Gerenciamento de Capacidade no Insights Operacionais do Microsoft Azure para ajudá-lo a compreender a capacidade da sua infraestrutura de servidor. |
| ![](./media/operational-insights-feature-guide/update.png) | [Avaliação de atualização do sistema](operational-insights-updates.md) | Você pode usar a solução Atualizações do Sistema no Insights Operacionais do Microsoft Azure para ajudá-lo a aplicar as atualizações ausentes a servidores na sua infraestrutura. |
| ![](./media/operational-insights-feature-guide/log-mgt.png) | [Gerenciamento de Log](operational-insights-search.md) | Você pode usar a solução de Gerenciamento de Log para coletar eventos e logs do IIS para pesquisas de log no Insights Operacionais. |
| ![](./media/operational-insights-feature-guide/malware.png) | [Avaliação de malware](operational-insights-antimalware.md) | Você pode usar a solução Antimalware no Insights Operacionais do Microsoft Azure para ajudá-lo a proteger os servidores na infraestrutura contra malware. |
| ![](./media/operational-insights-feature-guide/sec-audit.png) | [Segurança e auditoria](operational-insights-security-audit.md) | Você pode usar a solução Segurança e Auditoria para obter uma visão abrangente da postura de segurança de TI da sua organização com consultas de pesquisa interna para problemas importantes que exigem atenção. |
| ![](./media/operational-insights-feature-guide/assessment.png) | [Avaliação de SQL e do Active Directory](operational-insights-assessment.md) | Você pode usar a solução Avaliação para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares. |
| ![](./media/operational-insights-feature-guide/alert.png) | [Gerenciamento de alertas](operational-insights-alerts.md) | Você pode usar a solução de Gerenciamento de Alertas para gerenciar alertas de servidores monitorados pelo Operations Manager. |


Você também pode:

- **Enviar dados do computador para o sistema usando ou não um agente ou em conjunto com o System Center Operations Manager**. Para obter mais informações, consulte:
	- [Conectar-se a Insights Operacionais do System Center Operations Manager](operational-insights-connect-scom.md)
	- [Conectar computadores diretamente a Insights Operacionais](operational-insights-direct-agent.md)
	- [Analisar dados de servidores no Microsoft Azure](operational-insights-analyze-data-azure.md)
- **Faça todas as opções acima em praticamente qualquer lugar com o aplicativo móvel**
	- Para saber mais sobre o aplicativo Windows Phone, confira [Aplicativo móvel Insights Operacionais](http://www.windowsphone.com/pt-br/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)

## Ambiente do Insights Operacionais

O ambiente do Insights operacionais é composto de:

- Espaços de trabalho hospedados no Microsoft Azure que são contêineres para contas do Azure
- o serviço Web do Insights operacionais, que está hospedado na nuvem
- qualquer um dos agentes separados que se conectam diretamente ao serviço Web
- ou, um serviço anexado ao System Center Operations Manager, mas não é necessário


Se você usou a versão anterior do Insights operacionais chamado System Center Advisor, você pode ter software Advisor instalado no seu ambiente local. No entanto, o software Advisor não tem suporte com o Insights Operacionais.

O uso do software Insights Operacionais como um serviço do Operations Manager consiste em um ou mais grupos de gerenciamento e pelo menos um agente por grupo de gerenciamento. Os agentes do Operations Manager coletam dados de seus servidores e os analisam usando soluções (semelhantes a um pacote de gerenciamento no System Center Operations Manager). Os dados são regularmente enviados do Operations Manager para o serviço Web do Insights Operacionais (se necessário, passando por um servidor proxy), e nada é armazenado em bancos de dados do Operations Manager; portanto, não há nenhuma carga adicional sobre eles.

Da mesma forma, os agentes instalados em computadores individuais podem se conectar diretamente ao serviço Web para enviar os dados coletados para processamento.

Os dados em cada solução são analisados e apresentados no portal do Insights Operacionais. Você pode exibir todos os alertas e diretrizes de correções associadas, avaliações de configuração, problemas de capacidade de infraestrutura, status de atualização do sistema, avisos de antimalware e dados de log. Você também pode executar pesquisas de log detalhado e explorações ad hoc.

![Imagem do diagrama de visão geral do Insights Operacionais](./media/operational-insights-feature-guide/environment.png)

### Onde o Insights Operacionais está disponível?
O Insights Operacionais do Microsoft Azure está hospedado nos Estados Unidos. Embora o idioma do Insights Operacionais seja o inglês, o serviço está disponível em vários outros mercados. Para saber mais, confira [Disponibilidade internacional](http://go.microsoft.com/fwlink/?LinkId=229842).

<!---HONumber=August15_HO6-->