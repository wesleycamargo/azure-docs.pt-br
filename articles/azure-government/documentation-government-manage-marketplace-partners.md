---
title: Azure Marketplace para parceiros do Azure Governamental | Microsoft Docs
description: "Este artigo oferece uma comparação de funcionalidades e diretrizes de como desenvolver aplicativos para o Azure Governamental."
services: azure-government
cloud: gov
documentationcenter: 
author: tsingh
manager: asimm
ms.assetid: 7790d3c5-d0fa-4662-b4f0-a174cb7d6c9f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: zakramer;tsingh;divacc
translationtype: Human Translation
ms.sourcegitcommit: 0839e8e57b2149e50d4512d28b1e57e6592be458
ms.openlocfilehash: 70821864520ff18ba8c64be0ea66376bccee7148


---
# <a name="azure-marketplace-for-azure-government"></a>Azure Marketplace para o Azure Governamental
Se você for um parceiro Azure Governamental com interesse em publicar ofertas no Azure Marketplace, veja os detalhes neste artigo.

Atualmente, Imagens de VM de BYOL e Modelos de solução têm suporte no Marketplace do Azure Governamental. Como melhor prática, os Modelos de solução devem ser revisados antes da publicação no Azure Governamental para garantir que funcionarão no nas nuvens públicas do Azure e no Azure Governamental. Se estiver publicando apenas uma Imagem de VM, você poderá prosseguir para as etapas de publicação mais abaixo.

## <a name="pre-publishing-validation-for-solution-templates"></a>Validação pré-publicação para Modelos de solução

Antes de publicar seu modelo de solução no Azure Governamental, é recomendável verificar algumas das áreas comuns de melhores práticas para garantir que o Modelo funcionará na Nuvem pública do Azure e no Azure Governamental.

1.  Verifique se os pontos de extremidade não estão embutidos no código de seu modelo de solução para a Nuvem pública do Azure. Eles não serão válidos para nenhum dos outros Ambientes do Azure. Em vez disso, modifique o modelo de solução para solicitar uma chamada à API para obter o ponto de extremidade válido:  

  Exemplo:

  URI de VHD incorreto (embutido no código) "uri": "[concat('https://', variables('storageAccountName'), '.blob.core.windows.net/',  '/osdisk.vhd')]",

  URI de VHD correto (referenciado)

  "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'osdisk.vhd')]",

  A sintaxe corrigida garantirá o funcionamento do modelo em qualquer nuvem (Governamental, pública do Azure, AzureStack, China etc.)

2.  Verifique se os recursos usados no modelo de solução estão disponíveis na nuvem principal em você está publicando.
RPs no Azure e versão da API

    Identifique a disponibilidade no Azure Governamental usando o Gerenciador de Recursos no portal:

  1.    Faça logon no Portal do Azure Governamental
  2.    Inicie o Gerenciador de Recursos, seguindo as etapas listadas aqui https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-supported-services#supported-regions

  Extensões mais usadas; Disponibilidade em Fairfax  

  | Distribuidor/tipo | Versões disponíveis em Fairfax |
  | --- | --- |
  | Microsoft.OSTCExtensions/CustomScriptForLinux | 1; 1.1; 1.2.2.0; 1.3.0.2; 1.4.1.0; 1.5.2.0 |
  | Microsoft.Compute/CustomScriptExtension | 1.2; 1.3; 1.4; 1.7; 1.8 |
  | Microsoft.Azure.Extensions/DockerExtension | Não disponível |
  | Microsoft.Azure.Extensions/CustomScript | 2.0.2 |
  | Microsoft.OSTCExtensions/LinuxDiagnostic | 2.0.9005; 2.1.9005; 2.2.9005; 2.3.9011 |
  | Microsoft.Powershell/DSC | 2.19.0.0 |

> [!NOTE]
> Se colocar os locais para obter uma lista de valores permitidos, você precisará atualizar periodicamente à medida que novas regiões forem adicionadas.  


## <a name="publishing"></a>Publicação
> [!NOTE]
> Se você não for um parceiro do Azure Certified Marketplace existente, conclua as etapas que mostram como [publicar e gerenciar uma oferta](../marketplace-publishing/marketplace-publishing-getting-started.md) antes de continuar.
>
>

### <a name="step-1"></a>Etapa 1
Entre no [Azure Publishing](https://publish.windowsazure.com).

### <a name="step-2"></a>Etapa 2
Clique na oferta que deseja publicar.

### <a name="step-3"></a>Etapa 3
Clique em **SKUS** e selecione a caixa **Nuvem do Azure Governamental**.

> [!NOTE]
> As SKUs do BYOL (Traga sua Própria Licença) têm suporte.  Essa opção não está disponível para SKUs pré-pagas (PayG).
>
>

![Página da oferta em que se encontram as SKUS e opções de nuvem do Azure Governamental](./media/government-manage-marketplace-partner-1.png)

### <a name="step-4"></a>Etapa 4
Clique no link **+ Adicionar Certificação** para adicionar links para as certificações de sua oferta.

![Página da oferta com o link Adicionar Certificação](./media/government-manage-marketplace-partner-2.png)

### <a name="step-5"></a>Etapa 5
Para testar sua imagem no portal de publicação, solicite uma [conta de avaliação](https://azuregov.microsoft.com/trial/azuregovtrial) do Microsoft Azure Governamental.

Sua qualificação como parceiro que atua em entidades do governo federal, estadual, local ou entidades tribais dos EUA é verificada e a confirmação será fornecida por email.  Sua conta de avaliação estará disponível em três a cinco dias úteis.

### <a name="step-6"></a>Etapa 6
Clique em **Publicar** e clique em **Enviar por Push para Preparo**.

![Opções de Publicar e Enviar por Push para Preparo](./media/government-manage-marketplace-partner-3.png)

Você deverá inserir uma assinatura na lista branca que tenha acesso à oferta preparada. Insira a ID da assinatura da sua conta de avaliação do Azure Governamental recém-adquirida.

![Prompt em que você especifica as IDs de assinatura que podem acessar a oferta](./media/government-manage-marketplace-partner-4.png)

### <a name="step-7"></a>Etapa 7
Depois que a oferta for preparada com êxito, você poderá testar sua imagem entrando no [portal do Azure](https://portal.azure.us) usando sua conta de avaliação do Azure Governamental.

### <a name="step-8"></a>Etapa 8
Depois de validar a imagem usando a assinatura de avaliação, você poderá disponibilizar a oferta online clicando em **Publicar** e solicitando aprovação para passar para a produção.

![Comando para Solicitar aprovação para ir para produção](./media/government-manage-marketplace-partner-5.png)

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o [Blog do Microsoft Azure Governamental](https://blogs.msdn.microsoft.com/azuregov/).



<!--HONumber=Dec16_HO3-->


