---
title: Certificação de Módulo do IoT Edge | Microsoft Docs
description: Certifica um Módulo do IoT Edge para o Marketplace.
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
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c37ed908b61ca54957affed3f81526353bc3f53b
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389557"
---
# <a name="the-iot-edge-module-certification-process"></a>O processo de certificação do Módulo do IoT Edge

Este artigo descreve as etapas e os requisitos para certificar a um módulo do IoT Edge para publicação no Azure Marketplace. 

>[!Note]
>Este é um documento temporário. O Marketplace de módulo do IoT Edge está sendo compilado em paralelo e este artigo será substituído pela documentação pública.

## <a name="understanding-an-iot-edge-module"></a>Noções básicas sobre um módulo IoT Edge

Terminologia do módulo:

-   Uma **imagem de módulo** é um pacote contendo o software que define um módulo.

-   Uma **instância de módulo** é a unidade específica de computação que executa a imagem de módulo em um dispositivo com IoT Edge. A instância de módulo é iniciada pelo tempo de execução do IoT Edge.

Módulos também podem incluir o SDK do módulo do IoT, que usa a seguinte terminologia:

-   Uma **identidade de módulo** são informações (incluindo as credenciais de segurança) armazenadas no Hub IoT, que estão associadas a cada instância de módulo.

-   Um **módulo gêmeo** é um documento JSON armazenado no Hub IoT, e contém informações de estado para uma instância de módulo, incluindo metadados, configurações e condições.

-   **SDKs** são usados para desenvolver módulos personalizados em várias linguagens, como: C\#, C, Python, Java e Node.js.

## <a name="the-onboarding-process-for-an-iot-edge-module"></a>O processo de integração para um módulo do IoT Edge

A captura de tela a seguir mostra o processo para um módulo do IoT Edge se torne público no Azure Marketplace.

![Processo de certificação](./media/cloud-partner-portal-iot-edge-module-certification-process/onboarding-process.png)

### <a name="onboarding-step-details"></a>Detalhes da etapa de integração

-   **Etapa 1** – os parceiros enviam suas ofertas com o tipo de oferta **Módulo do IoT Edge** na ferramenta do Portal do Cloud Partner pela equipe do Azure Marketplace. Você deve ser um parceiro MS oficial para acessar a ferramenta Portal do Cloud Partner. Para obter mais informações, confira o [guia do distribuidor](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   **Etapa 2** – o Marketplace é executa automaticamente verificações antivírus e antimalware. A equipe de IoT pode executar seus testes de ingestão automatizados personalizados durante esse estágio.

-   **Etapa 3** – o módulo é público. Ele está listado no Marketplace e o contêiner pode ser extraído anonimamente de uma URL. Por exemplo, *marketplace.azurecr.io/module-identifier*.

## <a name="iot-edge-module-certification-criteria"></a>Critérios de certificação de módulo do IoT Edge

Abaixo estão os principais requisitos que devem ser atendidos para um módulo do IoT Edge ser certificado e publicado no Azure Marketplace.

>[!Note]
>Esses requisitos podem mudar. Você será notificado com antecedência e terá tempo para atualizar seus contêineres para que eles possam atender aos requisitos atualizados.

### <a name="technical-requirements"></a>Requisitos técnicos

**Ser um módulo do IoT Edge**

-   Somente os contêineres compatíveis com o docker têm suporte no momento como módulos do IoT Edge. O módulo deve estar em execução no [Moby](https://mobyproject.org/). 

    >[!Note]
    >Contêineres do docker funcionarão com Moby porque Moby é o projeto de software livre subjacente para o Docker.

-   O parceiro deve fornecer as seguintes configurações padrão: 

    -   Uma **tag** padrão (que não pode estar vazia).

    -   Um **createOptions** padrão (que pode estar vazio).

    -   Se estiver usando o SDK de módulo do IoT, um **gêmeo** padrão (que pode estar vazio).

    -   Se estiver usando o SDK de módulo do IoT, **rotas** padrão (que podem estar vazias).

**Suporte à plataforma**

-   É necessário suporte apenas para as plataformas que estão **Disponíveis ao Público Geral** na Camada 1 do IoT Edge (como registradas no [suporte do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Por exemplo, isso atualmente significa suporte para as seguintes combinações de sistemas operacionais e arquiteturas:

    -   Ubuntu Server 18.04 para x64

    -   Ubuntu Server 16.04 para x64

    -   Ampliação do Raspbian para arm32 (armhf)

**Dimensionamento do dispositivo**

-   Qualquer dispositivo com as dimensões equivalentes (CPU/RAM/armazenamento/GPU/etc.) de um Raspberry Pi ou superior pode ser um dispositivo IoT Edge. Se um módulo só funciona dentro das restrições de dimensão específica, essas restrições devem ser especificadas na descrição do módulo.

**Configurações padrão**

-   Um módulo deve começar com os parâmetros padrão prontos para uso para cada plataforma com suporte (sistema operacional + arquitetura).

-   As definições de configuração devem ser documentadas claramente (marcas, variáveis de ambiente, gêmeo e rotas.) Como parte da listagem, os parceiros podem definir uma descrição para o Módulo que dê suporte a uma marcação HTML básica ou aponte para uma página Web externa.

**Controle de versão**

-   Os clientes devem poder escolher se desejam atualizar automaticamente um módulo vindo do marketplace ou usar uma versão exata que testaram. Os módulos do marketplace devem seguir o mesmo padrão de controle de versão que o tempo de execução do IoT Edge. Por exemplo:

    -   Marcas sem interrupção, como "1.0", podem ser atualizadas.

    -   Marcas de versão secundária, como "1.3.24", não podem ser atualizadas.

**Telemetria**

-   Módulos usando o SDK do Módulo do IoT devem definir o identificador de módulo exclusivo atribuído pelo marketplace para fins de telemetria. Isso permite que o Azure Marketplace identifique o número de instâncias do módulo em execução. Esse identificador exclusivo é o nome do contêiner atribuído pelo marketplace na ingestão. Use os métodos dos SDKs a seguir para definir esse identificador:
    - [C\#](https://hub.docker.com/_/mysql/)
    - [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._product_info?view=azure-java-stable)

-   Para Módulos que não usam o SDK do Módulo do IoT, insights menos precisos estão disponíveis no Portal do Cloud Partner. Por exemplo, o número de downloads.

**Segurança**

-   Contêineres devem ter acesso com o mínimo possível de privilégios ao host. Contêineres "Privilegiados" devem ser extremamente raros.

-   Os parceiros devem manter seu módulo seguro como parte do suporte que oferecem.

**Atualizações**

-   Os parceiros devem manter o módulo atualizado e funcional. Ele serão notificados com antecedência se houver alterações significativas planejadas ao tempo de execução do IoT Edge.

**SDK do Módulo do IoT**

-   Incluir o SDK do Módulo IoT não é um pré-requisito para a certificação.
    No entanto, incluir o SDK do Módulo do IoT pode fornecer uma melhor experiência ao usuário. Por exemplo, para dar suporte ao roteamento, enviar mensagens para a nuvem etc. O SDK do módulo IoT é necessário para obter telemetria sobre o número de instâncias do módulo em execução.

**Requisito subjetivo**

-   Deve atender a pelo menos um caso de uso do IoT Edge do mundo real. Por exemplo, um contêiner do WordPress não deve ser integrado, a menos que um cliente esteja disposto a usá-lo do IoT Edge.

**Requisitos legais (da política de AMP)**

-   O módulo deve estar em conformidade com os contratos e as políticas do Microsoft Azure Marketplace. Para obter mais informações, veja o Contrato do Editor anexado e a [Política de Participação](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

-   Além dos contratos e das políticas do Azure Marketplace, pode haver exigências legais adicionais específicas para um tipo de oferta de Módulo do IoT Edge. Isso está sendo examinado.

-   O módulo deve ter *termos de uso* e uma *política de privacidade*.

-   O módulo também deverá ter um aviso de terceiros se usar quaisquer terceiros.

**Requisitos de suporte (da política de AMP)**

-   Os parceiros são responsáveis por dar suporte a seus módulos do IoT Edge. Eles garantirão que as opções de suporte fornecidas na descrição do módulo do IoT Edge permaneçam disponíveis e que pelo menos uma opção de suporte sempre esteja disponível. (Veja a Seção 4 do Contrato do Editor do AMP para obter mais detalhes).

**Categorização**

-   Todos os módulos do IoT Edge serão exibidos na categoria **Internet das Coisas \>módulo do IoT Edge**. Cabe ao parceiro escolher a melhor subcategoria para seus produtos.
