<properties
   pageTitle="Guia de início rápido da Central de Segurança do Azure | Microsoft Azure"
   description="Este documento ajuda você a se familiarizar rapidamente com a Central de Segurança do Azure ao orientá-lo através de componentes de gerenciamento de monitoramento e pela política de segurança e vinculação às próximas etapas."
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
   ms.date="04/26/2016"
   ms.author="terrylan"/>

# Guia de início rápido da Central de Segurança do Azure

Este documento ajuda você a se familiarizar rapidamente com a Central de Segurança do Azure ao orientá-lo através de componentes de gerenciamento de monitoramento e pela política de segurança e vinculação às próximas etapas.

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure. Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.

## O que é a Central de Segurança do Azure?
 A Central de Segurança ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## Pré-requisitos

Para começar a usar a Central de Segurança, você deve ter uma assinatura do Microsoft Azure. A Central de Segurança é habilitada com sua assinatura. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

 A Central de Segurança é acessada pelo [Portal do Azure](https://azure.microsoft.com/features/azure-portal/). Confira a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/) para saber mais.


## Acessar a Central de Segurança

No Portal, execute estas etapas para acessar a Central de Segurança:

1. Selecione **Navegar** e role até a opção **Central de segurança**. ![Acessar a Central de Segurança do Azure no portal][1]

2. Escolha **Central de Segurança**. Isso abre a folha **Central de Segurança**.
3. Para facilitar o acesso à folha da **Central de segurança** no futuro, selecione opção **Fixar folha no painel** (canto superior direito). ![Fixar a folha à opção de painel][2]

## Usar a Central de Segurança

Você pode configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure. Vamos configurar uma **política** de segurança para sua assinatura:

1. Clique no bloco **Política** na folha **Central de Segurança**. ![Central de Segurança][3]

2. Na folha **Política de segurança - definir política por assinatura ou grupo de recursos**, selecione uma assinatura. ![A folha da política de segurança na Central de Segurança do Azure][4]

3. Na folha **Política de segurança**, ative a opção **Coleta de dados** para coletar registros automaticamente. A ativação da opção **Coleta de dados** também provisionará a extensão de monitoramento em todas as máquinas virtuais atuais e novas na assinatura.
4. Selecione **Escolher uma conta de armazenamento por região**. Para cada região em que você tiver máquinas virtuais em execução, você deverá escolher a conta de armazenamento na qual os dados coletados dessas máquinas virtuais serão armazenados. Se você não escolher uma conta de armazenamento para cada região, ela será criada para você. Os dados coletados são isolados logicamente dos dados de outros clientes por motivos de segurança.

     > [AZURE.NOTE] É recomendável que você ative a coleta de dados e escolha uma conta de armazenamento no nível da assinatura primeiro. As políticas de segurança podem ser definidas no nível da assinatura do Azure e no nível do grupo de recursos, mas a configuração da conta de armazenamento e da coleta de dados ocorre apenas no nível da assinatura.

5. Ative as **Recomendações** que você gostaria de ver como parte de sua política de segurança. Exemplos:

 - A ativação das **Atualizações do sistema** verificará todas as máquinas virtuais com suporte para observar se faltam atualizações do sistema operacional.
 - A ativação das **Regras de linha de base** verificará as máquinas virtuais com suporte para identificar as configurações de sistema operacional que podem tornar a máquina virtual mais vulnerável a ataques.

**Recomendações** de endereço:

1. Volte para a folha da **Central de Segurança** e clique no bloco **Recomendações**. A Central de Segurança analisa periodicamente o estado de segurança de seus recursos do Azure. Quando possíveis vulnerabilidades de segurança são identificadas, uma recomendação é mostrada aqui.
2.	Selecione cada recomendação para exibir mais informações e/ou tomar medidas para resolver o problema. ![Recomendações na Central de Segurança do Azure][5]

Exiba o estado de integridade e segurança de seus recursos por meio da **integridade de segurança dos recursos**:

1.	Volte para a folha **Central de segurança**.
2.	O bloco **integridade de recursos** contém indicadores de estado de segurança para as **máquinas virtuais**, **rede**, **SQL** e **aplicativos**.
3.	Selecione **Máquinas virtuais** para exibir mais informações.
4.	A folha **Máquinas virtuais** exibe um resumo de status que mostra o status de programas antimalware, atualizações do sistema, reinicializações e as regras de linha de base das máquinas virtuais.
5.	Selecione um item em **RECOMENDAÇÕES DE MÁQUINA VIRTUAL** para exibir mais informações e/ou tomar medidas para configurar os controles necessários.
6.	Faça uma busca detalhada para exibir informações adicionais sobre máquinas virtuais específicas. ![O bloco de integridade de recursos na Central de Segurança do Azure][6]

Resolva os **Alertas de segurança**:

1.	Volte para a folha **Central de Segurança** e selecione o bloco **Alertas de segurança**. Na folha **Alertas de segurança**, uma lista de alertas é exibida. Os alertas são gerados pela análise da Central de Segurança de seus logs de segurança e pela atividade de rede. Os alertas das soluções de parceiro integradas também estão incluídos. ![Alertas na Central de Segurança do Azure][7]

2.	Selecione um alerta para exibir mais informações. ![Detalhes de alerta de segurança na Central de Segurança do Azure][8]

Exibir a integridade de suas **Soluções de parceiros**:

1. Volte para a folha **Central de segurança**. O bloco **Soluções de parceiros** permite monitorar rapidamente o status de integridade de suas soluções de parceiro integradas com sua assinatura do Azure.
2. Selecione o bloco **Soluções de parceiros**. Uma folha será aberta exibindo uma lista de suas soluções de parceiro conectadas à Central de Segurança. ![Soluções de parceiros][9]

3. Selecione uma solução de parceiro. Neste exemplo, permite selecionar a solução **WAF2 F5**. Uma folha será aberta mostrando o status da solução de parceiro e dos recursos associados a ela. Selecione **Console da solução** para abrir a experiência de gerenciamento do parceiro para essa solução. ![Detalhes da solução de parceiro][10]

## Próximas etapas
Neste documento, você foi apresentado para o monitoramento de segurança e para os componentes de gerenciamento de políticas na Central de Segurança. Para saber mais, consulte os seguintes:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciamento das recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança
- [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço
- [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – obtenha as últimas notícias de segurança e as informações do Azure.

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-center.png
[4]: ./media/security-center-get-started/security-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/partner-solutions-detail.png

<!---HONumber=AcomDC_0427_2016-->