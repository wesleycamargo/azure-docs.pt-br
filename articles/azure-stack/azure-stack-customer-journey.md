---
title: Passo a passo integração do datacenter do pilha do Azure | Microsoft Docs
description: Saiba o que esperar para uma implantação bem-sucedida no local do Azure Stack em seu datacenter.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 491664067c87de8f94d2395cb2ffbdeade2595b0
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979540"
---
# <a name="azure-stack-datacenter-integration"></a>Integração de datacenter do Azure Stack

Este artigo descreve a experiência do cliente do Azure Stack de ponta a ponta da aquisição de um sistema integrado por meio de uma implantação bem-sucedida no local por um provedor de soluções. Use essas informações para facilitar a sua jornada de e para ajudar a definir as expectativas para você, um cliente do Azure Stack.

Como cliente do Azure Stack, você deverá prever as seguintes fases:

|     |Fase de planejamento|Processo de pedido|Pré-implantação|Processo de fábrica|Entrega de hardware|Implantação no local|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Envolva-se com parceiros para fornecer suporte de pré-vendas.|Prepare o licenciamento de software e contratos, conforme necessário.|Fornece as ferramentas necessárias para coletar os requisitos de integração do datacenter e a documentação para o cliente.|Fornece as compilações de linha de base e a ferramenta mais recentes atualizações de cadeia em uma cadência mensal.|N/D|Engenheiros de suporte da Microsoft é ajudar com problemas de implantação.|
|**Parceiro**|Recomende opções de solução com base nas necessidades do cliente.<br><br>Propor uma prova de conceito (VDC), se necessário.<br><br>Estabelece uma relação de negócios.<br><br>Escolha o nível de suporte.|Prepare os contratos necessários com o cliente.<br><br>Crie a ordem de compra do cliente.<br><br>Decida sobre a linha do tempo de entrega.<br><br>Conecte-se ao cliente com a Microsoft se necessário.|Forneça ao cliente com o treinamento necessário para garantir o entendimento de todos os pré-requisitos de implantação e datacenter as opções de integração.<br><br>Ajudar o cliente com a validação dos dados coletados para garantir a integridade e a precisão.|Aplicam-se a última compilação de linha de base validado.<br><br>Aplica o Microsoft deployment toolkit necessário.|Enviar o hardware para o site do cliente.|Implantação manipulada por um engenheiro no local.<br><br>Em rack.<br><br>Implantação de host (HLH) do ciclo de vida de hardware.<br><br>Implantação de pilha do Azure.<br><br>Libere a cliente.|
|**Cliente**|Descreva os casos de uso pretendido e especificar os requisitos.|Determine o modelo de cobrança para usar, revisar e aprovar os contratos.|Preencher a planilha de implantação e garantir que todos os pré-requisitos de implantação são atendidos e pronto para implantação.|N/D|Prepare o datacenter garantindo que todos os itens necessários de energia e resfriamento, conectividade de borda e outro requisito de integração do datacenter necessários estão em vigor.|Estar disponível durante a implantação para fornecer credenciais de assinatura e suporte se houver dúvidas nos dados fornecidos.|
| | | | | | | |


## <a name="planning-phase"></a>Fase de planejamento
A fase de planejamento é quando a Microsoft ou o parceiro de solução do Azure Stack, trabalhará com você para avaliar e compreender suas necessidades para determinar se o Azure Stack é a solução certa para você:

Eles ajudarão você a decidir o seguinte:

-   É o Azure Stack a solução certa para sua organização?

-   Qual solução de tamanho você precisará?

-   Que tipo de modelo de cobrança e licenciamento funcionará para sua organização?

-   Quais são os requisitos de energia e resfriamento?

Para garantir que a solução de hardware melhor atenderá às suas necessidades, o [Planejador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pode ser usado para auxiliar no planejamento para determinar a capacidade apropriada e a configuração para o hardware do Azure Stack de pré-aquisição solução.

A planilha está *não* se destina a ser usado como um substituto para a sua investigação e análise das soluções de hardware que melhor atender às suas necessidades. Ao planejar uma implantação do Azure Stack, você também deve examinar a [considerações sobre a integração geral de datacenter](azure-stack-datacenter-integration.md) para o Azure Stack sistemas integrados.

## <a name="order-process-phase"></a>Fase do processo de ordem
Nesse estágio, muitas das suas dúvidas em relação a viabilidade seriam ter sido respondidas. Agora que você está pronto para confirmar a compra do Azure Stack e, depois de assinar todos os contratos e ordens de compra, você será solicitado a fornecer os dados de requisitos de integração para seu provedor de soluções.

## <a name="pre-deployment-phase"></a>Fase de pré-implantação
Durante essa fase, você precisará decidir como você deseja integrar o Azure Stack em seu datacenter. Para facilitar esse processo, Microsoft, em colaboração com os provedores de soluções, criar um modelo de requisitos para ajudar você a reunir as informações necessárias para planejar uma implantação de sistema integrado em seu ambiente.

O [considerações sobre a integração geral de datacenter](azure-stack-datacenter-integration.md) artigo fornece informações que ajudam você a concluir o modelo, conhecido como a planilha de implantação. 

> [!IMPORTANT]
> Durante esse estágio é importante que todas as informações de pré-requisito são investigadas e decidiu usar antes de ordenação da solução. Lembre-se de que esta etapa é demorada e requer a coordenação e a coleta de dados de várias disciplinas dentro da sua organização. Informações incorretas ou incompletas podem resultar em uma implantação maior. 

Na fase de pré-implantação, você precisará decidir o seguinte:

- **Azure Stack conexão modelo de provedor de identidade e**. Você pode optar por implantar Azure Stack [conectado à internet (e para o Azure) ou desconectado](azure-stack-connection-models.md). Para tirar o máximo proveito da pilha do Azure, incluindo cenários híbridos, você pode querer implantar conectado ao Azure. Escolher os serviços de Federação do Active Directory (AD FS) ou Azure Active Directory (Azure AD) é uma decisão única que você precisa fazer no momento da implantação. **Você não pode alterar isso posteriormente sem reimplantar todo o sistema**.

- **Modelo de licenciamento**. As opções de modelo de licenciamento para sua escolha dependem do tipo de implantação, que você terá. Sua escolha de provedor de identidade não tem nenhuma relevância em máquinas virtuais de locatário ou o sistema de identidade e as contas que eles usam.
    - Os clientes que estão em um [desconectado implantação](azure-stack-disconnected-deployment.md) tem apenas uma opção: cobrança baseada em capacidade.

    - Os clientes que estão em um [conectados implantação](azure-stack-connected-deployment.md) pode escolher entre o pagamento-como-uso e de cobrança com base em capacidade. A cobrança baseada em capacidade exigirá uma assinatura do Azure Enterprise Agreement (EA) para o registro. Isso é necessário para o registro, que fornece a disponibilidade de itens no Marketplace por meio de uma assinatura do Azure.

- **Integração de rede**. [Integração de rede](azure-stack-network.md) é essencial para a implantação, operação e gerenciamento de sistemas do Azure Stack. Há várias considerações que vão para garantir que a solução do Azure Stack é resiliente e tem uma infraestrutura física altamente disponível para dar suporte a suas operações.

- **Integração do firewall**. É recomendável que você [usar um firewall](azure-stack-firewall.md) para ajudar a proteger o Azure Stack. Firewalls podem ajudar a impedir ataques de DDOS, detecção de intrusão e inspeção de conteúdo. No entanto, deve-se observar que ele pode se tornar um gargalo de produtividade para os serviços de armazenamento do Azure.


- **Requisitos de certificado**. É fundamental que todos os [os certificados necessários](azure-stack-pki-certs.md) estão disponíveis *anterior* até um engenheiro no local que chegam ao seu datacenter para a implantação.

Depois que todas as informações de pré-requisito são reunidas por meio da planilha de implantação, o provedor de solução iniciará o processo de fábrica com base nos dados coletados para garantir uma integração bem-sucedida do Azure Stack no seu datacenter.

## <a name="hardware-delivery-phase"></a>Fase de fornecimento de hardware
Seu provedor de soluções trabalharão com você sobre a programação de quando chegarão a solução para suas instalações. Depois de receber e colocadas em vigor, você precisará agendar tempo com o provedor de soluções para ter um engenheiro vêm no local para executar a implantação do Azure Stack.

Vale **crucial** que todos os dados de pré-requisito está bloqueado e está disponível *antes do no local engenheiro chega para implantar a solução*.

-   Todos os certificados devem ser adquiridos e pronto.

-   Nome da região deve ser decidido no.

-   Todos os parâmetros de integração de rede são finalizados e coincidirem com o que você compartilhou com seu provedor de soluções.

> [!TIP]
> Se alguma dessas informações foi alterado, certifique-se comunicar as alterações com o provedor de soluções antes de você agendar a implantação real.

## <a name="onsite-deployment-phase"></a>Fase de implantação no local
Para implantar o Azure Stack, um engenheiro de seu provedor de soluções de hardware precisará estar presente para iniciar a implantação. Para garantir uma implantação bem-sucedida, certifique-se de que todas as informações fornecidas por meio da planilha de implantação não foi alterado. 

Este é o que você deve esperar o engenheiro durante a experiência de implantação:

- Verifique todos os cabos e conectividade de borda para garantir que a solução corretamente é reunida e atenda às suas necessidades.
- Configure a solução HLH (Host de ciclo de vida de Hardware), se estiver presente.
- Verifique se que todos os BMC, BIOS, configurações de rede e estão corretas.
- Verifique se que o firmware de todos os componentes é a versão mais recente aprovados pela solução.
- Inicie a implantação.

> [!NOTE]
> Um procedimento de implantação, o engenheiro pode levar aproximadamente uma semana de negócios para ser concluída.

## <a name="post-deployment-phase"></a>Fase de pós-implantação
Várias etapas que devem ser executadas pelo parceiro antes que a solução é transferida para o cliente na fase de pós-integração. Nesta fase, validação é importante para garantir que a implantação do sistema e executando corretamente. 

As ações que devem ser realizadas pelo parceiro OEM são:

-   [Executar teste azurestack](azure-stack-diagnostic-test.md#run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix)

-   [Registro com o Azure](azure-stack-registration.md)

-   [Sindicalização do Marketplace](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

-   Arquivos de configuração do comutador e HLH configuração de backup

-   Remover DVM

-   Preparar um resumo do cliente para a implantação

-   [Verificar atualizações para garantir que o software de solução é atualizado para a versão mais recente](.\azure-stack-updates.md)

Há várias etapas que são obrigatórios ou opcionais, dependendo do tipo de instalação.

-   Se a implantação foi concluída usando [do AD FS](azure-stack-integrate-identity.md), em seguida, o Azure Stack carimbo precisará ser integrado com o cliente do próprio AD FS.

  > [!NOTE]
  > Esta etapa é responsabilidade do cliente, embora o parceiro pode optar por oferecer serviços para fazer isso.

-   Integração com um sistema de monitoramento existente do respectivo parceiro.

    -   [Integração do System Center Operations Manager](azure-stack-integrate-monitor.md) também dá suporte a recursos de gerenciamento de frota.

    -   [Integração do Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>Cronograma geral

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Suporte
O Azure Stack permite uma experiência de suporte integrado que abrange o ciclo de vida completo do sistema, consistente com o Azure. Para dar total suporte sistemas integrados do Azure Stack, os clientes precisam de dois contratos de suporte; um com a Microsoft (ou seu provedor de solução de nuvem) para suporte de serviços do Azure e um com o provedor de suporte do sistema de hardware. A experiência de suporte integrado fornece escalonamento coordenado e a resolução, para que os clientes obtêm uma experiência de suporte consistente, independentemente de quem eles chamam pela primeira vez. Para clientes que já têm Premier, do Azure - Standard / suporte ProDirect ou parceiro com a Microsoft, suporte de software do Azure Stack está incluído.

A experiência de suporte integrado faz uso de uma troca de caso de mecanismo para transferência de bi-direcional de casos de suporte e atualizações de casos entre os parceiros de hardware e da Microsoft. O Microsoft Azure Stack seguirá os [política de ciclo de vida moderno](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [considerações sobre a integração geral de datacenter](azure-stack-datacenter-integration.md).
