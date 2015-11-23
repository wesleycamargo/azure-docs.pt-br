<properties
   pageTitle="Guia para a criação de um Modelo de Solução para o Marketplace | Microsoft Azure"
   description="Instruções detalhadas sobre como criar, certificar e implantar um Modelo de Solução de imagem com múltiplas VMs para compra no Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="10/28/2015"
      ms.author="hascipio; v-divte" />

# Guia para criar um Modelo de Solução para o Azure Marketplace
Após concluir a etapa 1, [Criação da conta e registro][link-acct-creation], falamos sobre a criação de um Modelo de Solução compatível com o Azure em [Pré-requisitos técnicos para criar um Modelo de Solução](marketplace-publishing-solution-template-creation-prerequisites.md). Agora, orientaremos você pelas etapas de criação de um Modelo de Solução com múltiplas VMs no [Portal de Publicação][link-pubportal] para o Azure Marketplace.

## Criar sua oferta de Modelo de Solução no Portal de Publicação
Acesse [https://publish.windowsazure.com](http://publish.windowsazure.com). **Ao fazer logon pela primeira vez no [Portal de Publicação](https://publish.windowsazure.com/), use a mesma conta utilizada para o registro de Perfil de Vendedor de sua empresa.** Posteriormente, você poderá adicionar qualquer funcionário de sua empresa como um coadministrador no Portal de Publicação.

### 1\. Selecione "Solution Templates"

  ![desenho][img-pubportal-menu-sol-templ]

### 2\. Crie um novo Modelo de Solução

  ![desenho][img-pubportal-sol-templ-new]

### 3\. Comece com as topologias
Um modelo de solução é um "pai" para todas as suas topologias. Você pode definir várias topologias em uma oferta/modelo de solução. Quando uma oferta passa para teste, todas as suas topologias a acompanham. Veja abaixo as etapas para definir sua oferta. - Criar uma Topologia – Normalmente **"Topology Identifier"** (Identificador de Topologia) é o nome da Topologia para o modelo de solução. O Topology Identifier será usado na URL, conforme mostrado abaixo:

  Azure Marketplace : http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Portal de Visualização do Azure: https://ms.portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}

- Adicionar uma nova versão  

### 4\. Certifique as versões de sua topologia
Carregue um arquivo zip contendo todos os arquivos necessários para provisionar essa versão específica da topologia. Esse arquivo zip deve conter o seguinte: arquivos *mainTemplate.json* e *createUiDefinition.json* em sua raiz - Quaisquer modelos vinculados e todos os scripts necessários.

Depois de carregar o arquivo zip, clique em **Request Certification** (Solicitar certificação). A equipe de certificação da Microsoft revisará os arquivos e certificará a topologia.

> [AZURE.TIP]Enquanto seus desenvolvedores trabalham na criação e certificação de topologias do Modelo de Solução, o departamento Comercial/Jurídico/Marketing de sua empresa pode trabalhar no conteúdo jurídico e de marketing.

## Próximas etapas
Agora que você criou o Modelo de Solução e enviou o arquivo zip com os arquivos exigidos para certificação, continue e siga as instruções em [Guia de conteúdo de marketing do Marketplace](marketplace-publishing-push-to-staging.md) antes de preparar sua oferta para teste no ambiente de preparo.

## Consulte também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

**Imagens de VM** – [Sobre imagens da máquina virtual no Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

**Extensões de VM** – [Visão geral sobre Agente de VM e Extensões de VM](https://msdn.microsoft.com/library/azure/dn832621.aspx) – [Extensões de VM e recursos do Azure](https://msdn.microsoft.com/library/azure/dn606311.aspx)

**ARM** – [Criar modelos de ARM do Azure](../resource-group-authoring-templates/) – [Exemplos de modelos simples de ARM](https://github.com/rjmax/ArmExamples)

**Limites da conta de armazenamento** – [Como monitorar o limite da conta de armazenamento](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) – [Armazenamento Premium](../storage/storage-premium-storage-preview-portal/#scalability-and-performance-targets-whpt-BRing-premium-storage)

[img-pubportal-menu-sol-templ]: media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]: media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]: marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=Nov15_HO3-->