<properties
	pageTitle="Tendência de custo estimado mensal | Microsoft Azure"
	description="Saiba mais sobre o gráfico Tendência de custo estimado mensal do DevTest Labs."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# Tendência de custo estimado mensal

## Visão geral

O recurso de gerenciamento de custos dos Laboratórios de Desenvolvimento/Teste ajuda a rastrear o custo de laboratório. Este artigo ilustra como usar o gráfico **Tendência de custo estimado mensal** para exibir o custo estimado até a data do mês do calendário atual, bem como o custo projetado do final do mês para o mês do calendário atual.

## Visualizando o gráfico Tendência de custo estimado mensal

Para visualizar o gráfico Tendência de custo estimado mensal, siga estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Procurar**, e, em seguida, **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. Escolha a opção **Configurações**.

	![Configurações](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. Na folha **Configurações** do laboratório, em **Gerenciamento de Custos**, selecione **Limites**.

	![Menu](./media/devtest-lab-configure-cost-management/menu.png)
 
1. A captura de tela a seguir mostra um exemplo de um gráfico de custo.

    ![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

O valor **Custo estimado** é o custo até a data estimado do mês do calendário atual enquanto o **Custo do Projeto** é o custo estimado para todo o mês de calendário atual, calculado usando o custo de laboratório dos últimos cinco dias.
 
Observe que os valores de custo são arredondados para o próximo número inteiro. Por exemplo:

- 5\.01 arredonda até 6 
- 5\.50 arredonda até 6
- 5\.99 arredonda até 6

Como é indicado acima do gráfico, os custos vistos no gráfico são custos *estimados* usando as tarifas da oferta [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/). Além disso, o seguinte *não* está incluído no cálculo de custo:

- Assinaturas CSP e Dreamspark não têm suporte atualmente, já que o DevTest Labs usa as [APIs de cobrança do Azure](../billing-usage-rate-card-overview.md) para calcular o custo do laboratório e as APIs de cobranças do Azure não dão suporte a assinaturas de Dreamspark ou CSP.
- Suas tarifas da oferta. No momento, não é possível usar as tarifas de oferta (mostradas em sua assinatura) que você negociou com a Microsoft ou parceiros Microsoft. Estamos usando as tarifas pré-pagas.
- Seus impostos
- Seus descontos
- Sua moeda de cobrança. No momento, o custo do laboratório é exibido apenas na moeda USD.

## Próximas etapas

Eis aqui algumas coisas para experimentar a seguir:

- [Definir políticas de laboratório](./devtest-lab-set-lab-policy.md) – saiba como definir as várias políticas usadas para controlar como seu laboratório e suas VMs são usados. 
- [Criar imagem personalizada](./devtest-lab-create-template.md) - durante a criação de uma VM, você especifica uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
- [Configurar imagens do Marketplace](./devtest-lab-configure-marketplace-images.md) - os DevTest Labs dão suporte à criação de novas VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens (se houver alguma) do Azure Marketplace podem ser usadas durante a criação de novas VMs em um laboratório.
- [Criar uma VM em um laboratório](./devtest-lab-add-vm-with-artifacts.md) – ilustra como criar uma nova VM de uma imagem de base (personalizada ou do Marketplace) e como trabalhar com artefatos em sua VM.

<!---HONumber=AcomDC_0608_2016-->