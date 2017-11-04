---
title: Criar e publicar um item do Marketplace na pilha do Azure | Microsoft Docs
description: Criar e publicar um item do Marketplace na pilha do Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 77e5f60c-a86e-4d54-aa8d-288e9a889386
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: erikje
ms.openlocfilehash: 64203ce186665aada98fbe8daed971164a650399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-publish-a-marketplace-item"></a>Criar e publicar um item do Marketplace

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

## <a name="create-a-marketplace-item"></a>Criar um item do Marketplace
1. [Baixar](http://www.aka.ms/azurestackmarketplaceitem) a ferramenta Gerenciador de galeria do Azure e o item do Azure Marketplace de pilha de exemplo.
2. Abra o item do Marketplace de exemplo e renomeie o **SimpleVMTemplate** pasta. (Use o mesmo nome que o item do Marketplace – por exemplo, **Contoso.TodoList**.) Esta pasta contém:
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Criar um modelo do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) ou escolha um modelo do GitHub. O item do Marketplace usa esse modelo para criar um recurso.
4. Para certificar-se de que o recurso pode ser implantado com êxito, teste o modelo com as APIs de pilha do Microsoft Azure.
5. Se o modelo se baseia em uma imagem de máquina virtual, siga as instruções para [adicionar uma imagem de máquina virtual com a pilha do Azure](azure-stack-add-vm-image.md).
6. Salvar o modelo do Gerenciador de recursos do Azure no **/Contoso.TodoList/DeploymentTemplates/** pasta.
7. Escolha os ícones e o texto para o item do Marketplace. Adicionar ícones para o **ícones** pasta e adicione o texto para o **recursos** do arquivo no **cadeias de caracteres** pasta. Use a convenção de nomenclatura Pequeno, Médio, Grande e Largo para os ícones. Consulte [item do Marketplace referência de interface do usuário](#reference-marketplace-item-ui) para obter uma descrição detalhada.
   
   > [!NOTE]
   > Todos os tamanhos de quatro ícone (pequeno, médio, grande, todo) são necessários para criar o item do Marketplace corretamente.
   > 
   > 
8. No **manifest.json** de arquivo, altere **nome** para o nome do seu item do Marketplace. Também alterar **publicador** ao seu nome ou da empresa.
9. Em **artefatos**, alterar **nome** e **caminho** para as informações corretas para o modelo do Azure Resource Manager incluídas.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. Substituir **meus itens do Marketplace** com uma lista de categorias de onde o item do Marketplace deve aparecer.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. Para as edições adicionais para manifest.json, consulte [referência: Marketplace item manifest.json](#reference-marketplace-item-manifestjson).
12. Para empacotar as pastas em um arquivo .azpkg, abra um prompt de comando e execute o seguinte comando:
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > O caminho completo para o pacote de saída deve existir. Por exemplo, se o caminho de saída for C:\MarketPlaceItem\yourpackage.azpkg, a pasta C:\MarketPlaceItem deve existir.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Publicar um item do marketplace
1. Use PowerShell ou Gerenciador de armazenamento do Azure para carregar o item do Marketplace (.azpkg) para o armazenamento de BLOBs do Azure. Você pode carregar no armazenamento do Azure pilha local ou carregar no armazenamento do Azure. (É um local temporário para o pacote.) Certifique-se de que o blob é publicamente acessível.
2. Na máquina virtual cliente no ambiente de pilha do Microsoft Azure, certifique-se de que sua sessão do PowerShell está configurado com as credenciais de administrador de serviço. Você pode encontrar instruções sobre como autenticar PowerShell na pilha do Azure no [implantar um modelo com o PowerShell](user/azure-stack-deploy-template-powershell.md).
3. Use o **AzureRMGalleryItem adicionar** cmdlet do PowerShell para publicar o item do Marketplace a pilha do Azure. Por exemplo:
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | Parâmetro | Descrição |
   | --- | --- |
   | SubscriptionID |ID de assinatura do administrador. Você pode recuperá-la usando o PowerShell. Se você preferir obtê-lo no portal, vá para a assinatura do provedor e copie a ID de assinatura. |
   | GalleryItemUri |URI do blob para seu pacote de galeria já foi carregado no armazenamento. |
   | Apiversion |Definir como **2015-04-01**. |
4. Acesse o portal. Agora você pode ver o item do Marketplace no portal – como um operador ou como um usuário.
   
   > [!NOTE]
   > O pacote pode levar vários minutos para aparecer.
   > 
   > 
5. O item do Marketplace agora foi salvo para a pilha do Azure Marketplace. Você pode optar por excluí-lo do seu local de armazenamento de Blob.
6. Você pode remover um item do Marketplace usando o **AzureRMGalleryItem remover** cmdlet. Exemplo:
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > A interface do usuário do Marketplace pode mostrar um erro depois de remover um item. Para corrigir o erro, clique em **configurações** no portal. Em seguida, selecione **descartar modificações** em **personalização do Portal**.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>Referência: Manifest.json de item do Marketplace
### <a name="identity-information"></a>Informações de identidade
| Nome | Obrigatório | Tipo | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| Nome |X |Cadeia de caracteres |[A-Za-z0-9]+ | |
| Publicador |X |Cadeia de caracteres |[A-Za-z0-9]+ | |
| Versão |X |Cadeia de caracteres |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadados
| Nome | Obrigatório | Tipo | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| DisplayName |X |Cadeia de caracteres |Recomendação de 80 caracteres |O portal não pode exibir o nome do item normalmente se ele tem mais de 80 caracteres. |
| PublisherDisplayName |X |Cadeia de caracteres |Recomendação de 30 caracteres |O portal não pode exibir o nome do publicador normalmente se for maior que 30 caracteres. |
| PublisherLegalName |X |Cadeia de caracteres |Máximo de 256 caracteres | |
| Resumo |X |Cadeia de caracteres |60 a 100 caracteres | |
| LongSummary |X |Cadeia de caracteres |140 a 256 caracteres |Ainda aplicável na pilha do Azure. |
| Descrição |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |caracteres de 500 a 5.000 | |

### <a name="images"></a>Imagens
O Marketplace usa os ícones a seguir:

| Nome | Largura | Altura | Observações |
| --- | --- | --- | --- |
| Ampla |255 px |115 px |Sempre necessário |
| Grande |115 px |115 px |Sempre necessário |
| Média |90 px |90 px |Sempre necessário |
| Pequena |40 px |40 px |Sempre necessário |
| Captura de tela |533 px |32 px |Opcional |

### <a name="categories"></a>Categorias
Cada item do Marketplace deve ser marcada com uma categoria que identifica onde o item é exibido no portal de interface do usuário. Você pode escolher uma das categorias existentes na pilha do Azure (computação, dados + armazenamento, etc.) ou escolha uma nova.

### <a name="links"></a>Links
Cada item do Marketplace pode incluir vários links para conteúdo adicional. Os links são especificados como uma lista de nomes e URIs.

| Nome | Obrigatório | Tipo | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| DisplayName |X |Cadeia de caracteres |Máximo de 64 caracteres | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>Propriedades adicionais
Além de metadados anteriores, os autores do Marketplace podem fornecer dados de par chave/valor personalizado no seguinte formato:

| Nome | Obrigatório | Tipo | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| DisplayName |X |Cadeia de caracteres |Máximo de 25 caracteres | |
| Valor |X |Cadeia de caracteres |Máximo de 30 caracteres | |

### <a name="html-sanitization"></a>Limpeza de HTML
Para qualquer campo que permite que o HTML, os seguintes elementos e atributos são permitidos:

S1, S2, h3, h4, h5, p, ol, ul, li, um [destino | href], br, forte, em, b,

## <a name="reference-marketplace-item-ui"></a>Referência: Item do Marketplace interface do usuário
Ícones e texto de itens do Marketplace como visto no portal do Azure pilha são da seguinte maneira.

### <a name="create-blade"></a>Folha Criar
![Folha Criar](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Folha de detalhes do item do Marketplace
![Folha de detalhes do item do Marketplace](media/azure-stack-marketplace-item-ui-reference/image3.png)

