---
title: Oferta de Serviço de Consultoria do Dynamics 365 e Azure | Microsoft Docs
description: Guia para definir e publicar uma oferta de serviço de consultoria do Dynamics 365 ou Azure no Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 7ea4a58f072071f60cf87d1e8817ae19658be5fb
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310412"
---
# <a name="azure-and-dynamics-365-consulting-service-offer"></a>Oferta de Serviço de Consultoria do Dynamics 365 e Azure

<table> <tr> <td>Esta seção explica como publicar um serviço de consultoria para o Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a> ou o <a href="https://appsource.microsoft.com">AppSource Marketplace</a>. Soluções baseadas no Microsoft <a href="https://dynamics.microsoft.com">Dynamics 365</a>, <a href="https://products.office.com">Office 365</a>, <a href="https://powerbi.microsoft.com">Power BI</a> e <a href="https://powerapps.microsoft.com">PowerApps</a> são qualificadas para serem listadas no AppSource. Outras ofertas com base em outros <a href="https://azure.microsoft.com/services">serviços do Microsoft Azure</a> são qualificadas para serem listadas no Azure Marketplace. </td> <td><img src="./media/consulting-services-publishing-offer/consulting-icon1.png"  alt="Microsoft consulting icon" /></td> </tr> </table>


## <a name="publishing-benefits"></a>Benefícios de publicação

Publicar em qualquer um dos marketplaces da Microsoft tem benefícios substanciais:

- Promova sua empresa, aproveitando a marca Microsoft.
- Alcance potencialmente mais de 100 milhões de usuários do Office 365 e Dynamics 365 no AppSource; alcance mais de 200 mil organizações por meio do Azure Marketplace.
- Obtenha clientes potenciais de alta qualidade desses mercados.
- Tenha seus serviços promovidos pelas equipes de campo e televendas da Microsoft.


## <a name="define-your-consulting-services-offer"></a>Definir sua oferta de serviços de consultoria

Defina sua oferta de serviços de consultoria em pacote. Concentre-se em escopo fixo, duração fixa, preço estimado, preço fixo (ou gratuito) e, principalmente, em ofertas orientadas à pré-venda para um único cliente. Selecione participações em pacotes repetíveis que foram populares e eficazes para gerar novos negócios para você.

## <a name="publish-a-consulting-service-offer"></a>Publicar uma oferta de serviço de consultoria

As seções a seguir descrevem o processo de publicação da sua oferta de serviços de consultoria

1.  Criar uma oferta
2.  Definir as configurações da oferta
3.  Insira os detalhes da vitrine e se deseja publicar no Azure Marketplace ou no AppSource.
4.  Publicar sua oferta

### <a name="create-a-new-offer"></a>Criar uma oferta

Para criar uma nova oferta, conclua as etapas a seguir:

1.  No menu principal do Portal do Cloud Partner, selecione **Nova oferta**.
2.  No menu Nova oferta, selecione **Serviço de consultoria**.

    ![Criação de uma nova oferta de serviços de consultoria](media/consulting-services-publishing-offer/cppselectnewconsultingoffer.png)

### <a name="define-offer-settings"></a>Definir as configurações da oferta

Na tela Nova oferta, a primeira etapa é criar a identidade da oferta.  A identidade da oferta consiste em três partes: **ID da oferta**, **ID do Fornecedor** e **Nome**.  Cada um desses blocos é abordado nas seções a seguir.

#### <a name="offer-id"></a>ID da oferta

Esse identificador é um nome exclusivo que você cria quando envia a oferta pela primeira vez. Deve conter apenas caracteres alfanuméricos minúsculos, traços ou sublinhados. A ID da oferta será visível na URL e afeta os resultados do mecanismo de pesquisa. Por exemplo, *yourcompanyname\_exampleservice*

Conforme mostrado no exemplo, a ID da oferta é anexado à ID do publicador para criar um identificador exclusivo. Ele é exposto como um link permanente que pode ser registrado e é indexado pelos mecanismos de pesquisa. 

*Depois que uma oferta estiver ativa, o identificador não pode ser atualizado*

#### <a name="publisher-id"></a>ID do fornecedor

Esse identificador está relacionado à sua conta. Quando você estiver conectado com sua conta organizacional, a ID do editor será exibida no menu suspenso.

#### <a name="name"></a>NOME

É essa cadeia de caracteres que será exibida como nome da oferta no AppSource ou no Azure Marketplace.

**Importante:** Insira aqui apenas o nome do serviço real. Não inclua a duração e o tipo de serviço.

O exemplo a seguir da Edgewater Fullscope mostra como o nome da oferta é montado. O nome da oferta aparece como:

![Criação de uma nova oferta de serviços de consultoria](media/consulting-services-publishing-offer/cppsampleconsultingoffer.png)

O nome da oferta é composto de quatro partes:

-   **Duração:** - definida na guia **Detalhes da vitrine** do editor. A duração pode ser expressa em horas, dias ou semanas.
-   **Tipo de serviço:** - definido na guia **Detalhes da vitrine** do editor. Os tipos de serviço são `Assessment`, `Briefing`, `Implementation`, `Proof of concept` e `Workshop`.
-   **Preposição:** - inserida pelo revisor
-   **Nome:** - definida na página **Configurações da oferta**.

A lista a seguir fornece vários nomes de oferta adequados:

-   Conceitos básicos para Serviços profissionais: resumo de uma hora
-   Plataforma de Migração na Nuvem: resumo de uma hora
-   PowerApps e Microsoft Flow: workshop de um dia
-   Serviços do Azure Machine Learning: prova de conceito de três semanas
-   Solução de varejo Brick and Click: resumo de uma hora
-   Traga seus próprios dados: workshop de uma semana
-   Análise de nuvem: workshop de três dias
-   Treinamento em Power BI: workshop de três dias
-   Solução de gerenciamento de vendas: implementação de uma semana
-   Início rápido para CRM: workshop de um dia
-   Dynamics 365 for Sales: avaliação de dois dias

Depois de concluir a guia **Configurações da oferta**, é possível salvar seu envio. Agora, o nome da oferta será exibido acima do editor e você poderá encontrá-lo em Todas as ofertas.

### <a name="enter-storefront-details"></a>Inserir os detalhes da vitrine

Em seguida, você precisa inserir os detalhes da sua vitrine. Os detalhes da vitrine consistem nestas seções:

-   Detalhes da oferta
-   Informações do publicador
-   Detalhes da listagem
-   Artefatos de marketing

#### <a name="offer-details"></a>Detalhes da oferta

A seção de detalhes da oferta contém os campos a seguir:

-   Resumo da oferta
-   Descrição da oferta

##### <a name="offer-summary"></a>Resumo da oferta

O resumo da oferta é uma breve descrição da oferta que aparece logo abaixo do nome dela. Use texto sem formatação ao inserir o resumo da oferta, que não deve ter quebras de linha. A seguir, há bons exemplos de resumos da oferta juntamente com os nomes da oferta correspondentes:

*Exemplo 1*

-   **Nome da oferta:** Análise de nuvem: workshop de três dias
-   **Resumo da oferta:** visão geral do Microsoft Azure e do Power BI, avaliação do ambiente atual e mini prova de conceito.

*Exemplo 2*

-   **Nome da oferta:** Azure IoT industrial: prova de conceito de 30 dias
-   **Resumo da oferta:** crie um produto industrial piloto conectado para vincular equipamentos em campo com segurança a uma solução de Hub IoT do Azure, com painéis, relatórios e notificações.

*Exemplo 3*

-   **Nome da oferta:** Serviços profissionais: resumo de uma hora
-   **Resumo da oferta:** visão geral e demonstração da solução Dynamics 365 for Operations estendida e pré-configurada, que oferece gerenciamento avançado de projetos, cobrança e recursos para serviços profissionais.

*Exemplo 4*

-   **Nome da oferta:** Power BI no seu mundo: workshop de quatro horas
-   **Resumo da oferta:** coloque seu primeiro painel para funcionar e aprenda as práticas recomendadas. Para até 12 alunos, realizado no local.

*Exemplo 5*

-   **Nome da oferta:** Dynamics e projetos: avaliação de três dias
-   **Resumo da oferta:** coleta de requisitos e avaliação para a solução de ERP elaborada para empresas de serviços profissionais e empresas orientadas a projetos.

##### <a name="offer-description"></a>Descrição da oferta

Descrição da oferta de serviço de consultoria. Uma boa descrição da oferta contém detalhes exatos de como será a participação e qual será o produto final para o cliente. Ela deve claramente ajudar o cliente a entender o que ele receberá.

Não inclua links de email ou números de telefone para entrar em contato com você na descrição da oferta. A oferta conterá um botão Entre em contato, que fará o upload de clientes potenciais no destino de gerenciamento de cliente potencial que você identificou na sua oferta.

Você inserirá a descrição da oferta no formato Markdown. Caso não esteja familiarizado com o Markdown ou com a formatação para HTML, pode examinar recursos no [site de documentos da Microsoft](https://docs.microsoft.com/contribute/how-to-write-use-markdown).

O uso desses formatos garantirá que sua oferta tenha o máximo de legibilidade para os clientes.

Mantenha a descrição da oferta breve e respeite o limite de caracteres, pois os usuários não gostam de ler textos longos. Você ainda tem a opção de fazer upload de folhetos de marketing, folhas de fatos e outros documentos que descrevem a oferta em mais detalhes.

O exemplo a seguir demonstra uma descrição de oferta composta adequadamente, além do nome e do resumo relacionados:

**Nome da oferta:** Análise de nuvem: workshop de três dias

**Resumo da oferta:** visão geral do Microsoft Azure e do Power BI, avaliação do ambiente atual e mini prova de conceito.

**Descrição da oferta:** este workshop de 3 dias é destinado a líderes técnicos e empresariais, sendo realizado no local, nas instalações do cliente.

***Agenda***

Dia 1

-   Explica como proteger, dimensionar e organizar os dados na nuvem da Microsoft usando o Azure Data Lake, o HDInsight ou o SQL Data Warehouse do Azure.

Dia 2

-   Explica como configurar e implantar soluções de análise avançada com o Microsoft R e o Azure Machine Learning.

Dia 3

-   Explica como produzir insights acionáveis e operacionalizar a análise com o Power BI, incluindo uma sessão colaborativa para o build conjunto de um painel do Power BI.

No final do workshop, o cliente será capaz de definir um plano de alto nível e um roteiro de implementação para soluções de análise e dados na nuvem da Microsoft.

*Arquivo markdown de exemplo para uma oferta seguindo este formato:*

    This 3-day workshop is for technical and business leaders and is held on-site at the client’s facility.

      ### Agenda

      **Day 1**

      * Focuses on how to secure, scale, and organize data within the Microsoft cloud using Azure Data Lake, HDInsight, or Azure SQL Data Warehouse

      **Day 2**

      * Covers how to configure and deploy advanced analytics solutions with Microsoft R and Azure Machine Learning

      **Day 3**

      * Covers how to draw actionable insights and operationalize analytics with Power BI, including a collaborative session to co-build a Power BI dashboard.


      ### Deliverables
      By the end of the workshop, the client will be able to define a high-level plan and an implementation roadmap for data and analytics solutions in the Microsoft cloud.


#### <a name="publisher-information"></a>**Informações do publicador**

**ID MPN**

A ID de 9 dígitos da MPN (Microsoft Partner Network). Caso não tenha uma ID MPN, pode acessar o Microsoft Partner Center para adquirir.

**ID do Partner Center**

Nova ID do Partner Center, caso tenha um.

**ID MPN**

Insira uma chave secreta para uma versão prévia da oferta no AppSource antes da ativação.
Esse identificador não é uma senha.

#### <a name="listing-details"></a>Detalhes da listagem

**Tipo de serviço de consultoria**

A Microsoft está com foco exclusivamente no escopo fixo, duração fixa, preço estimado ou fixo (ou gratuito) e, principalmente, ofertas de serviço de consultoria orientadas a pré-vendas para um único cliente, assim como para ofertas de avaliação, implementação, prova de conceito, resumo ou workshop, realizados no local ou virtualmente (os resumos devem ser feitos no local).

Os cinco tipos de ofertas a seguir estão incluídos:

-   **Avaliação:** uma avaliação do ambiente de um cliente para determinar a aplicabilidade de uma solução e fornecer uma estimativa de custo e tempo.
-   **Resumo:** uma introdução a uma solução ou um serviço de consultoria para atrair o interesse do cliente utilizando estruturas, demonstrações e exemplos de clientes. Os resumos devem ser feitos no local.
-   **Implementação:** uma instalação completa que resulta em uma solução totalmente funcional. Para este piloto, a Microsoft recomenda limitar a soluções que possam ser implementadas em uma semana ou menos.
-   **Prova de conceito:** uma implementação de escopo limitado para determinar se uma solução atenderá aos requisitos de um cliente.
-   **Workshop:** uma participação interativa realizada nas instalações de um cliente que pode envolver sessões de treinamento, resumos, avaliações ou demonstrações com base nos dados ou no ambiente do cliente.

**Disponibilidade por país/região**

Selecione o país e a região em que essa oferta de serviço de consultoria estará disponível. Não é possível publicar uma única oferta em vários países ou regiões. Uma nova oferta precisa ser criada para cada país ou região.

**Indústrias**

Selecione os setores aos quais sua oferta de serviço de consultoria mais se aplica.

**Duração**

Selecione um número (por exemplo, 3, 4 etc.) em Duração e, em seguida, selecione Hora, Dia ou Semana.

**Produtos primários**

Para publicar no Azure Marketplace, selecione **Azure** como o produto principal e, em seguida, selecione as Áreas de solução relevantes.

Para publicar no AppSource, selecione **Dynamics 365**, **Power BI** ou **PowerApps** como seu produto primário. Também é possível selecionar outros produtos aplicáveis relevantes; sua oferta de serviços de consultoria será exibida nas listagens que estão associadas a cada um desses produtos no AppSource.

**Competências relevantes**

Selecione as competências relevantes para esta oferta a fim de exibi-las juntamente com os detalhes da oferta.

#### <a name="marketing-artifacts"></a>Artefatos de marketing

**Logotipo da empresa (formato. png, 48 x 48 pixels)**

Faça upload de uma imagem que será exibida na peça da sua oferta, na página de exibição da galeria de ofertas. A imagem deve ser uma imagem .png com resolução de 48 x 48 pixels.

**Logotipo da empresa Logotipo da empresa (formato. png, 216 x 216 pixels)**

Faça upload de uma imagem que aparecerá na página de detalhes da sua oferta. A imagem deve ser uma imagem .png com resolução de 216 x 216 pixels.

**Vídeos (máximo de 4)**

Faça upload de até quatro vídeos de estudo de caso de cliente ou vídeos de referência de cliente. Caso não tenha nenhum, faça upload de um vídeo que explique o conhecimento da sua empresa em relação à oferta. Se você tiver uma demonstração da solução do Power BI ou do PowerApps, faça upload do vídeo de demonstração aqui. Os links de vídeo devem ser para o YouTube ou o Vimeo.

**Documentos (máximo de 3)**

Faça upload do folheto de marketing que descreve sua oferta de serviço de consultoria em detalhes. Como alternativa, você também pode fazer upload da visão geral da empresa, folhas de fatos ou estudos de caso.

**Capturas de tela (máximo de 5)**

Faça upload de até cinco imagens que fornecem mais informações sobre a oferta, produtos da oferta ou sua empresa. Por exemplo, um snippet do seu folheto de marketing, um slide relevante de uma apresentação ou uma imagem que mostre a força ou o conhecimento da empresa.

### <a name="publish-your-offer"></a>Publicar sua oferta

Depois de preencher as Configurações da oferta, os Detalhes da vitrine e os Contatos, selecione **Publicar** e forneça um endereço de email. Quando a Microsoft estiver pronta para publicar sua oferta, você receberá um email para ver uma versão prévia antes de entrar no ar. Pode retornar ao portal para verificar o status da sua oferta a qualquer momento.

As ofertas poderão aparecer com o status "Publicação cancelada" ou "Falha na publicação" durante o processo de publicação. Esse status é uma parte normal do processo e permite que a Microsoft faça edições em sua oferta. Se você vir sua oferta como "Publicação cancelada", deixe-a nesse status.
