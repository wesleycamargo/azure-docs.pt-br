---
title: Criar e publicar um item do Marketplace no Azure Stack | Microsoft Docs
description: Criar e publicar um item do Marketplace no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: ac4366a2e90ea239c650e611b7c4e8dddf5d5106
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649655"
---
# <a name="create-and-publish-a-marketplace-item"></a>Criar e publicar um item do Marketplace

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

## <a name="create-a-marketplace-item"></a>Criar um item do Marketplace

1. Baixe o [ferramenta de Gerenciador de galeria do Azure](https://www.aka.ms/azurestackmarketplaceitem) e o item do Marketplace do Azure Stack de exemplo.
2. Abra o item do Marketplace de exemplo e renomeie o **SimpleVMTemplate** pasta. Use o mesmo nome que o item do Marketplace; Por exemplo, **Contoso.TodoList**. Esta pasta contém:

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Crie um modelo do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md) ou escolha um modelo do GitHub. O item do Marketplace usa esse modelo para criar um recurso.

    > [!Note]  
    > Nunca, codifique qualquer segredos, como chaves de produto, a senha ou quaisquer informações de identificação do cliente no modelo do Azure Resource Manager. Arquivos de JSON do modelo são acessíveis sem a necessidade de autenticação depois de publicado na Galeria. Store todos os segredos no [Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md) e chamá-las de dentro do modelo.

4. Para garantir que o recurso pode ser implantado com êxito, teste o modelo com as APIs do Microsoft Azure Stack.
5. Se seu modelo se baseia em uma imagem de máquina virtual, siga as instruções para [adicionar uma imagem de máquina virtual para o Azure Stack](azure-stack-add-vm-image.md).
6. Salve o modelo do Azure Resource Manager na **/Contoso.TodoList/DeploymentTemplates/** pasta.
7. Escolha os ícones e texto para o item do Marketplace. Adicionar ícones para o **ícones** pasta e adicionar texto a ser a **recursos** de arquivo no **cadeias de caracteres** pasta. Use o **pequena**, **médio**, **grande**, e **ampla** convenção de nomenclatura para ícones. Consulte a [item de referência de interface do usuário do Marketplace](#reference-marketplace-item-ui) para obter uma descrição detalhada desses tamanhos.

   > [!NOTE]
   > Todos os tamanhos de ícones de quatro (pequeno, médio, grande, wide) são necessários para compilar o item do Marketplace corretamente.
   >
   >
8. No **manifest** file, altere **nome** para o nome do item do Marketplace. Também altere **publicador** para sua empresa ou um nome.
9. Sob **artefatos**, altere **nome** e **caminho** para as informações corretas para o modelo do Azure Resource Manager que você incluiu:

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. Substitua **meus itens do Marketplace** com uma lista de categorias em que o item do Marketplace deve aparecer:

   ```json
   "categories":[
   "My Marketplace Items"
   ],
   ```

11. Para todas as edições adicionais à manifest. JSON, consulte [referência: Manifest de item do Marketplace](#reference-marketplace-item-manifestjson).

12. Para empacotar as pastas em um arquivo. azpkg, abra um prompt de comando e execute o seguinte comando:

   ```shell
   AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
   ```

    > [!NOTE]
    > O caminho completo para o pacote de saída deve existir. Por exemplo, se o caminho de saída for C:\MarketPlaceItem\yourpackage.azpkg, a pasta C:\MarketPlaceItem deve existir.
    >
    >

## <a name="publish-a-marketplace-item"></a>Publicar um item do marketplace

1. Use o PowerShell ou Gerenciador de armazenamento do Azure para carregar o item do Marketplace. (azpkg) para o armazenamento de BLOBs do Azure. Você pode carregar no armazenamento do Azure Stack local ou carregar no armazenamento do Azure; que é um local temporário para o pacote. Certifique-se de que o blob é acessível publicamente.
2. Na máquina virtual cliente no ambiente do Microsoft Azure Stack, certifique-se de que sua sessão do PowerShell está configurado com suas credenciais de administrador de serviço. Você pode encontrar instruções sobre como autenticar o PowerShell no Azure Stack em [implantar um modelo com o PowerShell](user/azure-stack-deploy-template-powershell.md).
3. Quando você usa [PowerShell 1.3.0](azure-stack-powershell-install.md) ou posterior, você pode usar o **AzsGalleryItem adicionar** cmdlet do PowerShell para publicar o item do Marketplace no Azure Stack. Antes de usar o PowerShell 1.3.0, use o cmdlet **Add-AzureRMGalleryitem** em vez de **Add-AzsGalleryItem**. Por exemplo, quando você usa o PowerShell 1.3.0 ou posterior:

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   ```

   | Parâmetro | DESCRIÇÃO |
   | --- | --- |
   | SubscriptionID |ID de assinatura do administrador. Você pode recuperá-lo usando o PowerShell. Se você preferir para obtê-lo no portal, vá para a assinatura do provedor e copie a ID da assinatura. |
   | GalleryItemUri |URI de blob para o pacote da Galeria que já foi carregado para o armazenamento. |
   | Apiversion |Defina como **2015-04-01**. |

4. Acesse o portal. Agora você pode ver o item do Marketplace no portal, como um operador ou como um usuário. O pacote pode levar vários minutos para aparecer.

5. O item do Marketplace no Azure Stack Marketplace agora foi salvo. Você pode optar por excluí-lo do seu local de armazenamento de BLOBs.
    > [!Caution]  
    > Todos os artefatos de galeria padrão e seus artefatos de galeria personalizados agora são acessíveis sem autenticação sob as seguintes URLs:  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. Você pode remover um item do Marketplace usando o **Remove-AzureRMGalleryItem** cmdlet. Por exemplo: 

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   ```

   > [!NOTE]
   > A interface do usuário do Marketplace pode mostrar um erro depois de remover um item. Para corrigir o erro, clique em **configurações** no portal. Em seguida, selecione **descartar modificações** sob **personalização do Portal**.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Referência: Manifest de item do Marketplace

### <a name="identity-information"></a>Informações de identidade

| NOME | Obrigatório | Type | Restrições | DESCRIÇÃO |
| --- | --- | --- | --- | --- |
| NOME |X |Cadeia de caracteres |[A-Za-z0-9]+ | |
| Publicador |X |Cadeia de caracteres |[A-Za-z0-9]+ | |
| Versão |X |Cadeia de caracteres |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metadados

| NOME | Obrigatório | Type | Restrições | DESCRIÇÃO |
| --- | --- | --- | --- | --- |
| DisplayName |X |Cadeia de caracteres |Recomendação de 80 caracteres |O portal não pode exibir o nome do item normalmente se ele tiver mais de 80 caracteres. |
| PublisherDisplayName |X |Cadeia de caracteres |Recomendação de 30 caracteres |O portal não pode exibir o nome do Editor normalmente se ele for maior que 30 caracteres. |
| PublisherLegalName |X |Cadeia de caracteres |Máximo de 256 caracteres | |
| Resumo |X |Cadeia de caracteres |60 a 100 caracteres | |
| LongSummary |X |Cadeia de caracteres |140 a 256 caracteres |Ainda não aplicável ao Azure Stack. |
| DESCRIÇÃO |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 a 5.000 caracteres | |

### <a name="images"></a>Imagens

O Marketplace usa os ícones a seguir:

| NOME | Largura | Altura | Observações |
| --- | --- | --- | --- |
| Ampla |255 px |115 px |Sempre obrigatório |
| grande |115 px |115 px |Sempre obrigatório |
| Média |90 px |90 px |Sempre obrigatório |
| Pequena |40 px |40 px |Sempre obrigatório |
| Captura de tela |533 px |32 px |Opcional |

### <a name="categories"></a>Categorias

Cada item do Marketplace deve ser marcada com uma categoria que identifica onde o item é exibido no portal da interface do usuário. Você pode escolher uma das categorias existentes no Azure Stack (computação, dados + armazenamento, etc.) ou escolha um novo.

### <a name="links"></a>Links

Cada item do Marketplace pode incluir vários links para conteúdo adicional. Os links são especificados como uma lista de nomes e URIs:

| NOME | Obrigatório | Type | Restrições | DESCRIÇÃO |
| --- | --- | --- | --- | --- |
| DisplayName |X |Cadeia de caracteres |Máximo de 64 caracteres | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>Propriedades adicionais

Além dos metadados anteriores, os autores do Marketplace podem fornecer dados de par chave/valor personalizado da seguinte forma:

| NOME | Obrigatório | Type | Restrições | DESCRIÇÃO |
| --- | --- | --- | --- | --- |
| DisplayName |X |Cadeia de caracteres |Máximo de 25 caracteres | |
| Valor |X |Cadeia de caracteres |Máximo de 30 caracteres | |

### <a name="html-sanitization"></a>Limpeza de HTML

Para qualquer campo que permite HTML, os seguintes elementos e atributos são permitidos:

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Referência: Item do Marketplace da interface do usuário

Ícones e texto para itens do Marketplace como visto no portal do Azure Stack são da seguinte maneira.

### <a name="create-blade"></a>Folha Criar

![Folha Criar](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Folha de detalhes do item do Marketplace

![Folha de detalhes do item do Marketplace](media/azure-stack-create-and-publish-marketplace-item/image3.png)
