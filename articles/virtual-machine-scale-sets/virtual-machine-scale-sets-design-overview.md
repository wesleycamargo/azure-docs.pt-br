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
	ms.date="04/01/2016"
	ms.author="gatneil"/>

# Projetar conjuntos de escala de VM para dimensionamento

Este tópico discute considerações de design para Conjuntos de Escala de Máquina Virtual. Para obter informações sobre o que são Conjuntos de Escala de Máquina Virtual, consulte [Visão geral de Conjuntos de Escala de Máquina Virtual](virtual-machine-scale-sets-overview.md).


## Armazenamento

Um conjunto de escala usa contas de armazenamento para armazenar os discos do sistema operacional das VMs no conjunto. É recomendável usar uma taxa de 20 VMs ou menos por conta de armazenamento. Também recomendamos distribuir as iniciais dos nomes de conta de armazenamento pelo alfabeto. Isso ajuda a espalhar a carga entre diferentes sistemas internos. No modelo a seguir, por exemplo, usamos a função do Modelo ARM uniqueString para gerar hashes de prefixo que serão acrescentados ao início dos nomes de conta de armazenamento: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## Provisionamento em excesso

A partir da versão de API 2016-03-30, os Conjuntos de Escala de VM realizarão "provisionamento em excesso" de VMs por padrão. Isso significa que o conjunto de escala criará mais VMs do que o solicitado para então excluir as VMs desnecessárias. Isso melhora as taxas de êxito do provisionamento, pois, se até mesmo uma VM não for provisionada com êxito, toda a implantação será considerada "Com Falha" pelo Azure Resource Manager. Você não será cobrado por essas VMs extras e elas não contarão no seus limites de cota.

Embora melhore as taxas de sucesso de provisionamento, tal comportamento pode ser confuso para um aplicativo que não foi projetado para lidar com VMs que estão desaparecendo sem aviso. Para desativar o provisionamento em excesso, verifique se você tem a seguinte cadeia de caracteres no seu modelo: "overprovision": false.

Se desativar o provisionamento em excesso, você poderá acabar com uma taxa maior de VMs por conta de armazenamento, mas não recomendamos ir além de 40.


## Limites (Plataforma versus Personalizado)
Um conjunto de escala criado em uma imagem de plataforma é limitado a 100 VMs e recomendamos 5 contas de armazenamento para essa escala. No entanto, um conjunto de escala criado em uma imagem personalizada (criada por você) deve criar todos os vhds de disco de sistema operacional em uma conta de armazenamento. Desta forma, o número máximo de VMs em um conjunto de escala criado em uma imagem personalizada é 20. Se você desativar o provisionamento em excesso, será possível ir até 40.


## Grande Escala

Um conjunto de escala é limitado a 100 VMs para imagens de plataforma e 20 para imagens personalizadas (40 sem provisionamento em excesso). Para obter mais, você precisará implantar vários conjuntos de escala. Para ver um exemplo de como fazer isso, consulte este modelo: https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale.

<!---HONumber=AcomDC_0427_2016-->