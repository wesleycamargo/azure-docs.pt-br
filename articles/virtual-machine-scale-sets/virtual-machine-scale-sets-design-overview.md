<properties
	pageTitle="Projetar Conjuntos de Escala de Máquina Virtual para Escala | Microsoft Azure"
	description="Saiba mais sobre como criar os Conjuntos de Escala de Máquina Virtual para escala"
	keywords="máquina virtual linux, conjuntos de dimensionamento de máquina virtual" 
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="gatneil"/>

# Projetar conjuntos de escala de VM para dimensionamento

Este tópico discute considerações de design para Conjuntos de Escala de Máquina Virtual. Para obter informações sobre o que são Conjuntos de Escala de Máquina Virtual, confira [Conjuntos de Dimensionamento de Máquina Virtual - Visão Geral](virtual-machine-scale-sets-overview.md).


## Armazenamento

Um conjunto de escala usa contas de armazenamento para armazenar os discos do sistema operacional das VMs no conjunto. É recomendável usar uma taxa de 20 VMs ou menos por conta de armazenamento. Também recomendamos distribuir as iniciais dos nomes de conta de armazenamento pelo alfabeto. Isso ajuda a distribuir a carga entre diferentes sistemas internos. No modelo a seguir, por exemplo, usamos a função uniqueString do modelo do Resource Manager para gerar hashes de prefixo que são acrescentados ao início dos nomes de conta de armazenamento: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## Provisionamento em excesso

Da versão de API "2016-03-30", os Conjuntos de Escala de VM realizam "provisionamento em excesso" de VMs por padrão. Com o provisionamento em excesso ativado, a escala definida, na verdade, rotaciona mais VMs do que o solicitado e exclui as VMs extras recém-rotacionadas. O provisionamento em excesso melhora as taxas de sucesso do provisionamento. Você não é cobrado por essas VMs extras e elas não contam nos seus limites de cota.

Embora o provisionamento em excesso melhore as taxas de sucesso de provisionamento, tal comportamento pode ser confuso para um aplicativo que não foi projetado para lidar com VMs que estão desaparecendo sem aviso. Para desativar o provisionamento em excesso, verifique se você tem a seguinte cadeia de caracteres no seu modelo: "overprovision": "false". Mais detalhes podem ser encontrados na [documentação da API REST do conjunto de escala da VM](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Se desativar o provisionamento em excesso, você poderá acabar com uma taxa maior de VMs por conta de armazenamento, mas não recomendamos ir além de 40.


## Limites
Um conjunto de escala criado com base em uma imagem personalizada (criada por você) deve criar todos os VHDs de disco do sistema operacional em uma conta de armazenamento. Como resultado, o número máximo recomendado de VMs em um conjunto de dimensionamento compilado em uma imagem personalizada é 20. Se você desativar o provisionamento em excesso, será possível ir até 40.

Um conjunto de escala criado em uma imagem da plataforma atualmente é limitado a 100 VMs (recomendamos 5 contas de armazenamento para esse dimensionamento).

Para ter mais VMs do que esses limites permitem, você precisará implantar vários conjuntos de dimensionamento, conforme mostrado [neste modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

<!---HONumber=AcomDC_0817_2016-->