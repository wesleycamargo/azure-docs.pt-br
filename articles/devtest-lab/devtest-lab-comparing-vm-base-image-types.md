<properties
	pageTitle="Comparando imagens personalizadas e fórmulas em Laboratórios de Desenvolvimento/Teste | Microsoft Azure"
	description="Saiba mais sobre as diferenças entre imagens personalizadas e fórmulas como bases de VM para que você possa decidir qual é mais adequada para seu ambiente."
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

# Comparando imagens personalizadas e fórmulas em Laboratórios de Desenvolvimento/Teste

## Visão geral
É possível usar [imagens personalizadas](./devtest-lab-create-template.md) e [fórmulas](./devtest-lab-manage-formulas.md) como bases para [novas VMs criadas](./devtest-lab-add-vm-with-artifacts.md). No entanto, a principal diferença entre fórmulas e imagens personalizadas é que uma imagem personalizada é simplesmente uma imagem com base em um VHD, enquanto uma fórmula é uma imagem com base em um VHD *além de* configurações pré-configuradas, como o tamanho da VM, rede virtual e sub-rede, artefatos e assim por diante. Essas configurações pré-configuradas são definidas com valores padrão que podem ser substituídos no momento da criação da VM. Este artigo explica algumas das vantagens (prós) e desvantagens (contras) de usar imagens personalizadas versus usar fórmulas.
 
## Prós e contras das imagens personalizadas
As imagens personalizadas fornecem uma maneira estática e imutável para criar VMs de um ambiente desejado.

**Prós**
- O provisionamento da VM de uma imagem personalizada é rápido, uma vez que nada muda depois que a VM é criada por meio da imagem. Em outras palavras, não há nenhuma configuração a ser aplicada, uma vez que a imagem personalizada é apenas uma imagem sem configurações.
- VMs criadas por meio de uma única imagem personalizada são idênticas.

**Contras**
- Se você precisar atualizar algum aspecto da imagem personalizada, a imagem precisará ser recriada.

## Prós e contras da fórmula
As fórmulas fornecem uma maneira dinâmica de criar VMs por meio das configurações desejadas.

**Prós**
- As alterações no ambiente podem ser capturadas em tempo real por meio de artefatos. Por exemplo, se você deseja uma VM instalada com os bits mais recentes do seu pipeline de lançamento ou inscrever o código mais recente do seu repositório, pode especificar simplesmente um artefato que implanta os bits mais recentes ou inscreve o código mais recente na fórmula juntamente com uma imagem base de destino. Sempre que essa fórmula é usada para criar VMs, os bits/códigos mais recentes são implantados/inscritos na VM.
- As fórmulas podem definir as configurações padrão que as imagens personalizadas não podem fornecer, como configurações de rede virtual e tamanhos de VM.
- As configurações salvas em uma fórmula são exibidas como valores padrão, mas podem ser modificadas quando a VM é criada.

**Contras**
- Criar uma VM por meio de uma fórmula pode levar mais tempo do que criar uma VM por meio de uma imagem personalizada.

## Postagens de blogs relacionadas

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

<!---HONumber=AcomDC_0803_2016-->