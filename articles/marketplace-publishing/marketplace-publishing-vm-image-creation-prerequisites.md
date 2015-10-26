<properties
   pageTitle="Pré-requisitos técnicos para criar uma imagem de máquina virtual para o Azure Marketplace | Microsoft Azure"
   description="Entenda os requisitos para criar e implantar uma imagem de máquina virtual no Azure Marketplace para outras pessoas comprarem."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace-publishing"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="10/09/2015"
  ms.author="hascipio; v-divte"/>

# Pré-requisitos técnicos para criar uma imagem de máquina virtual para o Azure Marketplace
Leia o processo completamente antes de começar e compreender onde e por que cada etapa é executada. O quanto for possível, você deve preparar informações da sua empresa e outros dados, baixar as ferramentas necessárias e criar componentes técnicos antes de iniciar o processo de criação de oferta. Esses itens devem estar claros para revisão d artigo.

## Baixe as ferramentas e aplicativos necessários
Você deve ter os seguintes itens prontos antes de iniciar o processo:

- Dependendo de qual sistema operacional você está buscando, instale os cmdlets do Azure PowerShell ou uma ferramenta de interface de linha de comando do Linux na página Downloads do Azure.
- Instale o Gerenciador do Armazenamento do Azure no CodePlex.
- Baixe e instale a "Ferramenta do teste de certificação para o Certificado Azure”:
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Você precisará de um computador baseado no Windows para executar a ferramenta de certificação. Se você não tiver um computador baseado no Windows disponível, você pode executar a ferramenta usando uma VM com base em Windows no Azure.

## Plataformas com suporte
Você pode desenvolver VMs do Azure baseadas em Windows ou Linux. Alguns elementos do processo de publicação — como a criação de um VHD compatível com o Azure — usam diversas ferramentas e etapas dependendo de qual sistema operacional você está usando.

- Se você estiver usando o Linux, consulte a seção "Criar um VHD compatível com o Azure (baseado em Linux)" do [guia de publicação de imagem de Máquina Virtual](marketplace-publishing-vm-image-creation.md).
- Se você estiver usando o Windows, consulte a seção "Criar um VHD compatível com o Azure (baseado em Windows)" do [guia de publicação de imagem de Máquina Virtual](marketplace-publishing-vm-image-creation.md).

> [AZURE.NOTE]Você precisará de acesso a um computador Windows - executar a ferramenta de validação de certificação - criar a URL de SAS do VHD para envio de certificação de VHD

## Desenvolvendo seu VHD
É possível desenvolver VHDs do Azure na **nuvem** ou **local**.

- Desenvolvimento baseado em nuvem significa que todas as etapas de desenvolvimento são executadas remotamente em um VHD residente no Azure.
- O desenvolvimento local requer o download de um VHD e desenvolvê-lo usando a infraestrutura local. Mesmo sendo possível, nós não recomendamos. Observe que desenvolver para o Windows ou SQL localmente requer que você tenha as chaves de licença locais relevante. Você não pode incluir ou instalar o SQL Server depois de criar uma máquina virtual, e você deve basear sua oferta em uma imagem SQL aprovada do Portal do Azure. Se você optar por desenvolver localmente, execute algumas etapas diferentes do que se você estivesse desenvolvendo na nuvem. Você pode encontrar informações relevantes ao [criar uma VM no local da imagem](marketplace-publishing-vm-image-creation-on-premise.md).

## Próximas etapas
Agora você revisou os pré-requisitos e concluiu a tarefa necessária, pode prosseguir com a criação de sua oferta de imagem da Máquina Virtual, conforme detalhado no [Guia de publicação de imagem de Máquina Virtual](marketplace-publishing-vm-image-creation.md)

## Consulte também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)
- [Criar uma máquina virtual que executa o Windows no Portal de Visualização do Azure](../virtual-machines/virtual-machines-windows-tutorial/)


[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=Oct15_HO3-->