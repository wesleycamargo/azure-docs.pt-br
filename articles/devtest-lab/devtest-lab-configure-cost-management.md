<properties
	pageTitle="Gerenciar o gerenciamento de custo | Microsoft Azure"
	description="Aprenda a configurar os recursos de gerenciamento de custo dos Laboratórios de Desenvolvimento/Teste."
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
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Configurar o gerenciamento de custo

## Visão geral

O recurso de gerenciamento de custos dos Laboratórios de Desenvolvimento/Teste ajuda a rastrear o custo de laboratório. Este artigo ilustra como usar o gráfico **Tendência de custo estimado mensal** para exibir o custo estimado até a data do mês do calendário atual, bem como o custo projetado do final do mês para o mês do calendário atual.

## Habilitando o gráfico Tendência de custo estimado mensal

Para habilitar o gráfico Tendência de custo estimado mensal, siga estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Toque em **Procurar** e toque em **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, toque no laboratório desejado.

1. Toque em **Configurações**.

	![Configurações](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. Na folha **Configurações** do laboratório, em **Políticas de Custo**, toque em **Limite de Custo**.

	![Menu](./media/devtest-lab-configure-cost-management/menu.png)
 
1. Na folha **Limites de Custo**, toque em **Ativar** para habilitar esse recurso e em **Desativar** para desabilitá-lo.

1. Toque em **Salvar**.

Depois de habilitar esse recurso, pode levar várias horas para que o gráfico exiba seus custos projetados e estimados. Isso ocorre porque um serviço é executado a cada hora para coletar essas informações, mas é executado algumas horas atrasado em relação aos dados dinâmicos sendo coletados. Por exemplo, digamos que você inicia uma VM à 01h. O custo associado a essa VM provavelmente não será incorporado ao gráfico de custo por algumas horas.
 
A captura de tela a seguir mostra um exemplo de um gráfico de custo.

![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

O valor **Custo Estimado** é o custo até a data estimado do mês do calendário atual enquanto o **Custo do Projeto** é o custo estimado para todo o mês de calendário atual.

Como é indicado acima do gráfico, os custos vistos no gráfico são custos *estimados* usando as tarifas da oferta [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/). Além disso, o seguinte *não* está incluído no cálculo de custo:

- Suas tarifas da oferta. No momento, não é possível usar as tarifas de oferta (mostradas em sua assinatura) que você negociou com a Microsoft ou parceiros Microsoft. Estamos usando as tarifas pré-pagas.
- Seus impostos
- Seus descontos
- Sua moeda de cobrança. No momento, o custo do laboratório é exibido apenas na moeda USD.

## Próximas etapas

Eis aqui algumas coisas para experimentar a seguir:

- [Definir políticas de laboratório](./devtest-lab-set-lab-policy.md): saiba como definir as várias políticas usadas para controlar como seu laboratório e suas VMs são usados. 
- [Criar uma imagem personalizada](./devtest-lab-create-template.md): ao criar uma VM, você especifica uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
- [Configurar imagens do Marketplace](./devtest-lab-configure-marketplace-images.md): os Laboratórios de Desenvolvimento/Teste dão suporte à criação de novas VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens (se houver alguma) do Azure Marketplace podem ser usadas durante a criação de novas VMs em um laboratório.
- [Criar uma VM em um laboratório](./devtest-lab-add-vm-with-artifacts.md): ilustra como criar uma nova VM de uma imagem de base (personalizada ou do Marketplace) e como trabalhar com artefatos em sua VM.

<!---HONumber=AcomDC_0518_2016-->