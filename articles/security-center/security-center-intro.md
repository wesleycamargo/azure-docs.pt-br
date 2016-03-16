<properties
   pageTitle="Introdução à Central de Segurança do Azure | Microsoft Azure"
   description="Saiba mais sobre a Central de Segurança do Azure, seus principais recursos e como ela funciona."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="terrylan"/>

# Introdução à Central de Segurança do Azure

Saiba mais sobre a Central de Segurança do Azure, seus principais recursos e como ela funciona.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure. Este documento apresenta o serviço usando uma implantação de exemplo.

## O que é a Central de Segurança do Azure?
 A Central de Segurança ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

##	Principais recursos
 A Central de Segurança oferece uma prevenção contra ameaças eficiente e fácil de usar, além de recursos de detecção e resposta que são internos no Azure. Os principais recursos são:

| | |
|----- |-----|
| Evitar | Monitora o estado de segurança de seus recursos do Azure |
| | Define políticas de suas assinaturas do Azure com base nos requisitos de segurança de sua empresa, nos tipos de aplicativos que você usa e na confidencialidade dos dados |
| | Usa recomendações de segurança orientadas por políticas para guiar seus proprietários através do processo de implementação dos controles necessários |
| | Implanta rapidamente aplicativos da Microsoft e de parceiros e serviços de segurança |
| Detectar |Coleta e analisa dados de segurança automaticamente de seus recursos do Azure, da rede e de soluções de parceiros, como programas antimalware e firewalls |
| | Aproveita a inteligência contra ameaças globais dos produtos e serviços da Microsoft, centros de resposta a crimes e incidentes e feeds externos |
| | Aplica a análise avançada, incluindo aprendizado de máquina e análise comportamental |
| Responder | Fornece alertas/incidentes de segurança priorizados |
| | Oferece informações sobre a origem do ataque e dos recursos afetados |
| | Sugere maneiras para interromper o ataque atual e ajudar a evitar ataques futuros |

## Passo a passo introdutório
 Você acessar a Central de Segurança pelo [portal do Azure](https://azure.microsoft.com/features/azure-portal/). [Entre no portal](https://portal.azure.com), selecione **Navegar** e role até a opção **Central de Segurança** ou escolha o bloco **Central de Segurança** fixado anteriormente ao painel do Portal.

![Bloco de segurança no portal do Azure][1]

Na Central de Segurança, defina as políticas de segurança, monitore as configurações de segurança e exiba alertas de segurança.

### Diretivas de segurança

Você pode definir políticas para as assinaturas do Azure de acordo com os requisitos de segurança de sua empresa. Você também pode personalizar os tipos de aplicativos que está usando ou a confidencialidade dos dados em cada assinatura. Por exemplo, os recursos usados para desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para aplicativos de produção. Da mesma forma, os aplicativos com dados regulamentados, como PII, podem exigir um nível mais alto de segurança.

> [AZURE.NOTE] Para editar uma política de segurança, você precisa ser o proprietário de uma assinatura ou um colaborador dela.

Clique no bloco **Política de segurança** para obter uma lista das suas assinaturas e escolher uma assinatura para exibir os detalhes da política.

![Bloco de política de segurança][2]

A opção **Coleta de dados** (veja acima) habilita a coleta de dados para uma política de segurança. A habilitação fornece: - Verificação diária de todas as máquinas virtuais com suporte para monitoramento de segurança e recomendações - coleta de eventos de segurança para a detecção e análise de ameaças.

**Mostrar recomendações para:** (veja acima) permite que você escolha os controles de segurança que deseja monitorar e recomendar com base nas necessidades de segurança dos recursos na assinatura.

### Recomendações de segurança

 A Central de Segurança analisa o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Uma lista de recomendações orienta você no processo de configuração dos controles necessários. Os exemplos incluem:

- Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado
- Como configurar grupos de segurança de rede e regras para controlar o tráfego para máquinas virtuais
- Provisionamento de firewalls do aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web
- Como implantar atualizações de sistema ausentes
- Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

Clique no bloco **Recomendações** para obter uma lista de recomendações. Clique em cada recomendação para exibir informações adicionais ou tomar uma atitude para resolver o problema.

![Recomendações de segurança na Central de Segurança do Azure][3]

### Integridade de recursos

O bloco **integridade de recursos** mostra a postura geral de segurança do ambiente por tipo de recurso, incluindo máquinas virtuais, aplicativos web e outros recursos.

Selecione um tipo de recurso no bloco **Integridade de recursos** para exibir mais informações, incluindo uma lista de possíveis vulnerabilidades de segurança que foram identificadas. (**Máquinas Virtuais** é selecionado no exemplo a seguir.)

![Bloco de integridade de recursos][4]

### Alertas de segurança

 A Central de segurança automaticamente coleta, analisa e integra os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros como programas antimalware e firewalls. Quando forem detectadas ameaças, é criado um alerta de segurança. Os exemplos abrangem a detecção de:

- As máquinas virtuais comprometidas se comunicam com os endereços IP mal-intencionados conhecidos
- Malware avançado detectado usando o relatório de erros do Windows
- Ataques por força bruta contra máquinas virtuais
- Alertas de segurança dos firewalls e programas antimalware integrados

Clicar no bloco **alertas de segurança** exibe uma lista de alertas prioritários.

![Alertas de segurança][5]

Selecionar um alerta mostra mais informações sobre o ataque e sugestões para corrigi-lo.

![Detalhes do alerta de segurança][6]

## Introdução
Para começar a usar a Central de Segurança, você precisa ter uma assinatura do Microsoft Azure. A Central de segurança é habilitada com sua assinatura do Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

 Você acessar a Central de Segurança pelo [portal do Azure](https://azure.microsoft.com/features/azure-portal/). Confira a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/) para saber mais.

A [Introdução à Central de segurança do Azure](security-center-get-started.md) o orienta rapidamente quanto o monitoramento de segurança e aos componentes de gerenciamento da política da Central de Segurança.

## Próximas etapas
Neste documento, você foi apresentado à Central de Segurança, seus principais recursos e como começar. Para saber mais, consulte os seguintes:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como definir as políticas de segurança.
- [Gerenciamento das recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço.
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/recommendations.png
[4]: ./media/security-center-intro/resources-health.png
[5]: ./media/security-center-intro/security-alert.png
[6]: ./media/security-center-intro/security-alert-detail.png

<!---HONumber=AcomDC_0211_2016-->