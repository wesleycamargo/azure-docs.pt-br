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
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="terrylan"/>

# Introdução à Central de Segurança do Azure

Saiba mais sobre a Central de Segurança do Azure, seus principais recursos e como ela funciona.

> [AZURE.NOTE]As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure. Esta é uma introdução ao serviço usando um exemplo de implantação.

## O que é a Central de Segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

##	Principais recursos
A Central de segurança do Azure oferece uma prevenção contra ameaças eficiente, além de recursos de detecção e resposta que estão incorporados no Azure. Os principais recursos são:

| | |
|----- |-----|
| Evitar | Monitora o estado de segurança de seus recursos do Azure |
| | Define as políticas para as assinaturas do Azure com base em requisitos de segurança da sua empresa e do tipo de aplicativos ou da confidencialidade dos dados |
| | Usa recomendações de segurança orientadas por políticas para guiar seus proprietários através do processo de implementação dos controles necessários |
| | Implanta rapidamente aplicativos da Microsoft e de parceiros e serviços de segurança |
| Detectar | Coleta e analisa dados de segurança automaticamente de seus recursos do Azure, da rede e de soluções de parceiros, como firewalls e antimalware |
| | Aproveita a inteligência contra ameaças globais dos produtos e serviços da Microsoft, centros de resposta a crimes e incidentes e feeds externos |
| | Aplica a análise avançada, incluindo aprendizado de máquina e análise comportamental |
| Responder | Fornece alertas/incidentes de segurança priorizados |
| | Oferece informações sobre a origem do ataque e dos recursos afetados |
| | Sugere maneiras para interromper o ataque atual e ajudar a evitar ataques futuros |

## Passo a passo introdutório
A Central de Segurança do Azure é acessada pelo [Portal do Azure](http://azure.microsoft.com/features/azure-portal/). Para acessar, [entre no portal](https://portal.azure.com), selecione **Navegar** e role até a opção **Central de Segurança** ou escolha o bloco **Central de Segurança** fixado anteriormente ao painel do Portal.

![][1]

Na Central de segurança do Azure, defina as políticas de segurança, monitore as configurações de segurança e exiba alertas de segurança.

### Diretivas de segurança

Você pode definir as políticas para as assinaturas do Azure de acordo com os requisitos de segurança de sua empresa e personalizadas conforme os tipos de aplicativos ou a confidencialidade dos dados de cada assinatura. Por exemplo, os recursos usados para desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para aplicativos de produção. Da mesma forma, os aplicativos com dados regulamentados, como PII, podem exigir um nível mais alto de segurança.

> [AZURE.NOTE]Para editar uma política de segurança, você deve ser um proprietário ou colaborador da assinatura.

Clique no bloco **Política de segurança** para obter uma lista das suas assinaturas e escolher uma assinatura para exibir os detalhes da política.

![][2]

A opção **Coleta de dados** (veja acima) habilita a coleta de dados para uma política de segurança. A habilitação fornece: - Verificação diária de todas as máquinas virtuais com suporte para monitoramento de segurança e recomendações - coleta de eventos de segurança para a detecção e análise de ameaças

**Mostrar recomendações para:** (veja acima) permite que você escolha os controles de segurança que deseja monitorar e recomendar com base nas necessidades de segurança dos recursos na assinatura.

### Recomendações de segurança

A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Uma lista de recomendações orienta você no processo de configuração dos controles necessários. Os exemplos incluem:

- Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado
- Como configurar Grupos de segurança de rede e as regras para controlar o tráfego para máquinas virtuais
- Provisionamento de um Firewall do Aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web
- Como implantar atualizações de sistema ausentes
- Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

Clique no bloco **Recomendações** para obter uma lista de recomendações. Clique em cada recomendação para exibir informações adicionais ou para tomar uma atitude para resolvê-la.

![][3]

### Integridade dos recursos

O bloco **integridade de recursos** mostra a postura geral de segurança do ambiente por tipo de recurso - máquinas virtuais, aplicativos web e outros recursos.

Selecione um tipo de recurso no bloco **Integridade de recursos** para exibir mais informações (máquinas virtuais no exemplo abaixo), incluindo uma lista de possíveis vulnerabilidades de segurança que foram identificadas.

![][4]

### Alertas de segurança

A Central de segurança do Azure automaticamente coleta, analisa e integra os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros como antimalware e firewalls. Quando forem detectadas ameaças, é criado um alerta de segurança. Os exemplos abrangem a detecção de:

- As máquinas virtuais comprometidas se comunicam com os endereços IP mal-intencionados conhecidos
- Malware avançado detectado com o relatório de erros do Windows
- Ataques por força bruta contra máquinas virtuais
- Alertas de segurança dos firewalls e antimalware integrados

Clicar no bloco **alertas de segurança** exibe uma lista de alertas prioritários.

![][5]

Selecionar um alerta mostra mais informações sobre o ataque e sugestões sobre como corrigi-lo.

![][6]

## Introdução
Para começar com a Central de segurança do Azure, você deve ter uma assinatura do Microsoft Azure. A Central de segurança do Azure é habilitada com sua assinatura do Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).

A Central de Segurança do Azure é acessada pelo [Portal do Azure](http://azure.microsoft.com/features/azure-portal/). Confira a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/) para saber mais.

O [Introdução à Central de segurança do Azure](security-center-get-started.md) orienta você rapidamente através do monitoramento de segurança e dos componentes de gerenciamento da política da Central de Segurança do Azure.

## Próximas etapas
Neste documento, você foi apresentado à Central de segurança do Azure, seus principais recursos e como começar. Para saber mais, consulte os seguintes:

- [Definição de políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como definir as políticas de segurança
- [Gerenciamento das recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger os recursos do Azure
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre as perguntas frequentes sobre como usar o serviço de localização
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – obtenha as últimas notícias de segurança e informações do Azure

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/recommendations.png
[4]: ./media/security-center-intro/resources-health.png
[5]: ./media/security-center-intro/security-alert.png
[6]: ./media/security-center-intro/security-alert-detail.png

<!---HONumber=AcomDC_1217_2015-->