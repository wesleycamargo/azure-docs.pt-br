<properties
   pageTitle="Introdução ao Centro de Segurança do Azure | Microsoft Azure"
   description="Este documento ajuda você a se familiarizar rapidamente com a Central de segurança do Azure ao orientá-lo através de componentes de gerenciamento de monitoramento e pela política de segurança e vinculação às próximas etapas."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="terrylan"/>

# Introdução à Central de segurança do Azure

Este documento ajuda você a se familiarizar rapidamente com a Central de segurança do Azure ao orientá-lo através de componentes de gerenciamento de monitoramento e pela política de segurança e vinculação às próximas etapas.

> [AZURE.NOTE]As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure. Esta é uma introdução ao serviço usando um exemplo de implantação. Isso não é um guia passo a passo.

## O que é a Central de segurança do Azure?
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## Pré-requisitos

Para começar com a Central de segurança do Azure, você deve ter uma assinatura do Microsoft Azure. A Central de segurança do Azure é habilitada com sua assinatura. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).

A Central de segurança do Azure é acessada do [portal de visualização do Microsoft Azure](http://azure.microsoft.com/features/azure-portal/). Consulte a [documentação do portal de visualização do Azure](https://azure.microsoft.com/documentation/services/azure-portal/) para saber mais.


## Como acessar a Central de segurança do Azure

No portal do Azure, siga estas etapas para acessar a Central de segurança do Azure:

1. Selecione **Navegar** e role até a opção **Central de segurança**. ![][1]

2. Selecione **Central de segurança**. Isso abre a folha **Central de segurança**.
3. Para facilitar o acesso à folha da **Central de segurança** no futuro, selecione opção **Fixar folha no painel** (canto superior direito). ![][2]

## Como usar a Central de Segurança do Azure

Configure uma **Política** de segurança para suas assinaturas:

4. Clique no bloco **Política de segurança** na folha **Central de segurança**.
5. Na folha **Política de segurança – definir política por assinatura**, selecione uma assinatura.
6. Na folha **Política de segurança**, ative a opção **Coleta de dados** para coletar registros automaticamente. A ativação da opção **Coleta de dados** também provisionará a extensão de monitoramento em todas as máquinas virtuais atuais e novas na assinatura.
7. Clique em **Escolher uma conta de armazenamento**. Para cada região em que você tiver máquinas virtuais em execução, você deverá escolher a conta de armazenamento na qual os dados coletados dessas máquinas virtuais serão armazenados. Se você não escolher uma conta de armazenamento para cada região, ela será criada para você. Os dados coletados são isolados logicamente dos dados de outros clientes por motivos de segurança.
8. Ative as **recomendações** que você gostaria de ver como parte de sua política de segurança. Exemplos:

  - A ativação das **atualizações do sistema** verificará todas as máquinas virtuais com suporte para observar se faltam atualizações do sistema operacional.
  - A ativação das **regras de linha de base** verificará as máquinas virtuais com suporte para identificar as configurações de sistema operacional que podem tornar a máquina virtual mais vulnerável a ataques.

![][3]

Recomendações de **endereço**:

9. Volte para a folha da **Central de segurança** e clique no bloco **recomendações**.
10.	A Central de Segurança do Azure analisa periodicamente o estado de segurança de seus recursos do Azure. Quando possíveis vulnerabilidades de segurança são identificadas, uma recomendação é mostrada aqui.
11.	Clique em cada recomendação para exibir mais informações e/ou tomar uma atitude para resolvê-la.

![][4]

Exiba o estado de integridade e segurança de seus recursos por meio da **integridade de recursos**:

12.	Volte para a folha **Central de segurança**.
13.	O bloco **integridade de recursos** contém indicadores de estado de segurança para as **máquinas virtuais**, **rede**, **SQL** e **aplicativos**.
14.	Selecione **Máquinas virtuais** para exibir mais informações.
15.	A folha **Máquinas virtuais** exibe um resumo de status, inclusive antimalware, atualizações do sistema, reinicialização e regras de linha de base das suas máquinas virtuais.
16.	Selecione um item em **ETAPAS DE PREVENÇÃO** para exibir mais informações e/ou tomar uma atitude para configurar os controles necessários.
17.	Faça uma busca detalhada para exibir informações adicionais sobre máquinas virtuais específicas.

![][5]

Alertas de segurança de **endereço**:

19.	Volte para a folha **Central de segurança** e clique no bloco **alertas de segurança**.
20.	Na folha **alertas de segurança**, uma lista de alertas é exibida. Os alertas foram detectados pela análise da Central de segurança do Azure de seus registros de segurança e pela atividade de rede. Os alertas das soluções de parceiro integradas também estão incluídos. ![][6]

21.	Clique em um alerta para exibir informações extras.

  ![][7]

## Próximas etapas
Neste documento, você foi apresentado para o monitoramento de segurança e para os componentes de gerenciamento de políticas na Central de segurança do Azure. Para saber mais, consulte os seguintes:

- [Definição de políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como definir as políticas de segurança
- [Implementação das recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como as recomendações ajudam a proteger os recursos do Azure
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre as perguntas frequentes sobre como usar o serviço de localização
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – obtenha as últimas notícias de segurança e informações do Azure

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/recommendations.png
[5]: ./media/security-center-get-started/resources-health.png
[6]: ./media/security-center-get-started/security-alert.png
[7]: ./media/security-center-get-started/security-alert-detail.png

<!---HONumber=AcomDC_1203_2015-->