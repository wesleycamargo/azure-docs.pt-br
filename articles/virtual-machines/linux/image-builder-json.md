---
title: Criar um modelo do construtor de imagens do Azure (visualização)
description: Saiba como criar um modelo a ser usado com o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b4646879eb7eeecf41852baab7ab64e4053b05e1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159594"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Visualização: Criar um modelo do construtor de imagens do Azure 

Construtor de imagens do Azure usa um arquivo. JSON para passar informações para o serviço do Image Builder. Neste artigo examinaremos as seções do arquivo json, para que você possa criar seus próprios. Para ver exemplos de arquivos. JSON completo, consulte o [Azure imagem construtor GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Este é o formato do modelo básico:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "<build timeout in minutes>": {}, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Versão de API e tipo

O `type` é o tipo de recurso, que deve ser `"Microsoft.VirtualMachineImages/imageTemplates"`. O `apiVersion` será alterado ao longo do tempo, como as alterações de API, mas deve ser `"2019-05-01-preview"` para visualização.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Local padrão

O local é a região onde a imagem personalizada será criada. Para a visualização do construtor de imagens, há suporte para as regiões a seguir:

- Leste dos EUA
- Leste dos EUA 2
- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>Depende (opcional)

Essa seção opcional pode ser usada para garantir que as dependências sejam concluídas antes de continuar. 

```json
    "dependsOn": [],
```

Para obter mais informações, consulte [definir as dependências de recurso](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identidade
Por padrão, dá suporte do Image Builder usando scripts ou copiar arquivos de vários locais, como o armazenamento do GitHub e o Azure. Para usá-los, eles devem ser acessíveis publicamente.

Você também pode usar uma identidade gerenciada de Azure User-Assigned, definido por você, para permitir o acesso ao construtor de imagem de armazenamento do Azure, desde que a identidade recebeu um mínimo de 'Leitor de dados do armazenamento de Blob' na conta de armazenamento do Azure. Isso significa que você não precisa tornar a blobs de armazenamento acessíveis externamente ou Tokens de SAS do programa de instalação.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Para obter um exemplo completo, consulte [ usar uma identidade gerenciada de Azure User-Assigned para acessar arquivos no armazenamento do Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Suporte de construtor para uma identidade atribuída pelo usuário da imagem: • dá suporte a uma única identidade somente • não oferece suporte a nomes de domínio personalizado

Para obter mais informações, consulte [What ' s identidades gerenciadas para recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Para obter mais informações sobre como implantar esse recurso, consulte [configurar gerenciadas identidades para recursos do Azure em uma VM do Azure usando a CLI do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Propriedades: código-fonte

O `source` seção contém informações sobre a imagem de origem que será usada pelo construtor de imagens.

A API requer um 'SourceType' que define a origem para o build de imagem, no momento, há três tipos:
- ISO - use essa opção quando a fonte for uma ISO RHEL.
- PlatformImage - indicado que a imagem de origem é uma imagem do Marketplace.
- ManagedImage - use essa opção quando a partir de uma imagem gerenciada normal.
- SharedImageVersion - isso é usado quando você estiver usando uma versão de imagem em uma galeria de imagem compartilhada como a origem.

### <a name="iso-source"></a>Fonte da ISO

Construtor de imagens do Azure dá suporte somente a publicado Red Hat Enterprise Linux 7.x binário DVD ISOs, para visualização. Construtor de imagens dá suporte a:
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Para obter o `sourceURI` e `sha256Checksum` valores, acesse `https://access.redhat.com/downloads` , em seguida, selecione o produto **Red Hat Enterprise Linux**e uma versão com suporte. 

Na lista de **instaladores e imagens para Red Hat Enterprise Linux Server**, você precisa copiar o link para o Red Hat Enterprise Linux 7.x binário DVD e a soma de verificação.

> [!NOTE]
> Os tokens de acesso dos links são atualizados em intervalos frequentes, portanto, sempre que você deseja enviar um modelo, você deve verificar se o RH vincular o endereço foi alterado.
 
### <a name="platformimage-source"></a>Origem PlatformImage 
Construtor de imagens do Azure dá suporte a imagens do Azure Marketplace a seguir:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


As propriedades aqui são as mesmas que são usados para criar a VM, usando a CLI AZ, execute o abaixo para obter as propriedades: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Versão não pode ser 'latest', você deve usar o comando acima para obter um número de versão. 

### <a name="managedimage-source"></a>Origem ManagedImage

Define a imagem de origem como uma imagem gerenciada existente de uma VM ou VHD generalizado. A imagem gerenciada de origem deve ser de um sistema operacional compatível e estar na mesma região que seu modelo do construtor de imagens do Azure. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

O `imageId` deve ser o ResourceId da imagem gerenciada. Use `az image list` para listar as imagens disponíveis.


### <a name="sharedimageversion-source"></a>Origem SharedImageVersion
Define a imagem de origem uma versão de imagem existente em uma galeria de imagens compartilhadas. A versão da imagem deve ser de um sistema operacional com suporte, e a imagem deve ser replicada para a mesma região que seu modelo do construtor de imagens do Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

O `imageVersionId` deve ser o ResourceId da versão da imagem. Use [lista de versão da imagem do az sig](/cli/azure/sig/image-version#az-sig-image-version-list) para listar versões da imagem.

## <a name="properties-customize"></a>Propriedades: Personalizar


Construtor de imagens dá suporte a vários 'personalizadores'. Personalizadores são funções que são usadas para personalizar a imagem, como execução de scripts ou reinicialização de servidores. 

Ao usar `customize`: 
- Você pode usar vários personalizadores, mas eles devem ter um único `name`.
- Executam personalizadores na ordem especificada no modelo.
- Se um personalizador falhar, o componente de personalização inteiro falhará e relatar um erro.
- Considere quanto tempo o build de imagem exigem e ajustar a propriedade 'buildTimeoutInMinutes' para permitir tempo suficiente para concluir o construtor de imagens.
- É altamente recomendável que testar o script detalhadamente antes de usá-lo em um modelo. Depurar o script em sua própria VM será mais fácil.
- Não coloque dados confidenciais nos scripts. 
- Os locais de script precisam ser acessível publicamente, a menos que você estiver usando [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
A seção personalizar é uma matriz. Construtor de imagens do Azure será executado por meio dos personalizadores em ordem sequencial. Qualquer falha em qualquer personalizador falhará o processo de compilação. 
 
 
### <a name="shell-customizer"></a>Personalizador do shell

O shell personalizador dá suporte à execução de scripts de shell, eles devem ser publicamente acessíveis para o IB para acessá-los.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Suporte a SO: Linux 
 
Personalize as propriedades:

- **tipo** – Shell 
- **nome** - nome para a personalização de acompanhamento 
- **scriptUri** -URI para o local do arquivo 
- **embutido** -matriz de comandos do shell, separados por vírgulas.
 
> [!NOTE]
> Ao executar personalizador do shell com código-fonte do RHEL ISO, você precisa garantir que seus identificadores de shell personalização primeiro registrar com um servidor de autorização do Red Hat antes que ocorra qualquer personalização. Depois que a personalização for concluída, o script deve cancelar o registro com o servidor de autorização.

### <a name="windows-restart-customizer"></a>Reinicie o Windows Personalizador 
O Restart personalizador permite que você reiniciar uma VM do Windows e aguarde até que ele volte a ficar online, isso permite que você instale o software que requer uma reinicialização.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Suporte a SO:  Windows
 
Personalize as propriedades:
- **Tipo**: WindowsRestart
- **restartCommand** -comando para executar a reinicialização (opcional). O padrão é `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – comando para verificar se a reinicialização bem-sucedida (opcional). 
- **restartTimeout** -reiniciar o tempo limite especificado como uma cadeia de caracteres de magnitude e a unidade. Por exemplo, `5m` (5 minutos) ou `2h` (2 horas). O padrão é: '5m'


### <a name="powershell-customizer"></a>Personalizador do PowerShell 
O shell personalizador dá suporte à execução em linha comando e scripts do PowerShell, os scripts devem ser publicamente acessíveis para o IB para acessá-los.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

Suporte a SO: Windows e Linux

Personalize as propriedades:

- **tipo** – PowerShell.
- **scriptUri** -URI para o local do arquivo de script do PowerShell. 
- **embutido** – comandos embutida a ser executado, separados por vírgulas.
- **valid_exit_codes** – opcional de códigos válidos que podem ser retornados da script/linha de comando, isso evitará uma falha relatada do comando de script/embutido.

### <a name="file-customizer"></a>Personalizador do arquivo

Personalizador do arquivo permite que o construtor de imagens baixar um arquivo do GitHub ou do armazenamento do Azure. Se você tiver um pipeline de build de imagem que se baseia em artefatos de compilação, pode, em seguida, defina personalizador do arquivo para fazer o download do compartilhamento de compilação e mover os artefatos para a imagem.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

Suporte a SO: Linux e Windows 

Propriedades de personalizador do arquivo:

- **sourceUri** -um ponto de extremidade de armazenamento acessíveis, isso pode ser o armazenamento do Azure ou do GitHub. Você só pode baixar um arquivo, não um diretório inteiro. Se você precisar baixar um diretório, use um arquivo compactado, em seguida, descompacte-o usando os personalizadores Shell ou o PowerShell. 
- **destino** – esse é o nome de arquivo e caminho completo do destino. Qualquer caminho de referência e subdiretórios deve existir, use os personalizadores Shell ou o PowerShell para configurá-los com antecedência. Você pode usar os personalizadores de script para criar o caminho. 

Isso é compatível com diretórios do Windows e caminhos de Linux, mas há algumas diferenças: 
- Sistema operacional Linux – somente imagem construtor pode gravar o caminho é /tmp.
- Windows – sem restrição de caminho, mas o caminho devem existir.
 
 
Se houver um erro tentando baixar o arquivo ou colocá-lo em um diretório especificado, a etapa de personalizar falhará e isso terá o customization.log.

Arquivos no personalizador do arquivo podem ser baixados do armazenamento do Azure usando o [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Generalizar 
Por padrão, o construtor de imagens do Azure também executará 'Cancelar ' o provisionamento de código no final de cada fase de personalização de imagem, para generalizar a imagem. Generalizando é um processo em que a imagem é configurada para que ele possa ser reutilizado para criar várias VMs. Para VMs do Windows, o construtor de imagens do Azure usa o Sysprep. Para o Linux, o construtor de imagens do Azure é executado ' waagent-deprovision'. 

Os comandos que os usuários do construtor de imagens para generalizar podem não ser adequados para cada situação, portanto, o construtor de imagens do Azure permitirá que você personalize esse comando, se necessário. 

Se você estiver migrando uma personalização existente e você estiver usando comandos de Sysprep/waagent diferentes, você pode usar os comandos genéricos do Image Builder e se a criação da VM falhar, use seus próprios comandos de Sysprep ou waagent.

Se o construtor de imagens do Azure cria uma imagem personalizada do Windows com êxito, e você cria uma VM de, em seguida, ele localizar que a criação da VM falha ou não for concluída com êxito, você precisará examinar a documentação do Windows Server Sysprep ou envie uma solicitação de suporte com o Equipe de suporte de serviços de cliente do Windows Server Sysprep, quem pode solucionar problemas e informar sobre o uso correto do Sysprep.


#### <a name="default-sysprep-command"></a>Padrão de comando do Sysprep
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Comando de desprovisionamento de Linux padrão

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Substituindo os comandos
Para substituir os comandos, use os provisionadores de script do PowerShell ou o Shell para criar os arquivos de comando com o nome exato do arquivo e colocá-los nos diretórios corretos:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder lerá esses comandos, eles são gravados nos logs de AIB, 'customization.log'. Ver [solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) sobre como coletar logs.
 
## <a name="properties-distribute"></a>Propriedades: distribuir

Construtor de imagens do Azure dá suporte a três destinos de distribuição: 

- **managedImage** – gerenciado imagem.
- **sharedImage** -Galeria de imagens compartilhadas.
- **VHD** -VHD em uma conta de armazenamento.

Você pode distribuir uma imagem para ambos os tipos de destino na mesma configuração, consulte [exemplos](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Como você pode ter mais de um destino para os quais distribuir, Image Builder mantém um estado para cada destino de distribuição que pode ser acessado por meio de consulta a `runOutputName`.  O `runOutputName` é um objeto que você pode consultar lançar a distribuição para obter informações sobre essa distribuição. Por exemplo, você pode consultar o local do VHD ou regiões em que a versão da imagem foram replicada para o. Esta é uma propriedade de cada destino de distribuição. O `runOutputName` deve ser exclusivo para cada destino de distribuição.
 
### <a name="distribute-managedimage"></a>Distribute: managedImage

A saída de imagem será um recurso de imagem gerenciada.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Distribua as propriedades:
- **tipo** – managedImage 
- **imageId** – a ID de recurso da imagem de destino, esperado um formato: /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **local** -localização da imagem gerenciada.  
- **runOutputName** – exclusivo nome para identificar a distribuição.  
- **artifactTags** -marcas do par de valor de chave especificado pelo usuário opcional.
 
 
> [!NOTE]
> O grupo de recursos de destino deve existir.
> Se você deseja que a imagem distribuída para uma região diferente, ele aumentará o tempo de implantação. 

### <a name="distribute-sharedimage"></a>Distribute: sharedImage 
Galeria de imagens do Azure compartilhado é um novo serviço de gerenciamento de imagens que permite o gerenciamento de replicação da região de imagem, controle de versão e o compartilhamento de imagens personalizadas. Construtor de imagens do Azure dá suporte à distribuição com esse serviço, portanto, você pode distribuir imagens para regiões com suporte pelo galerias de imagens compartilhadas. 
 
Uma galeria de imagens compartilhadas é composta de: 
 
- Galeria – contêiner para várias imagens compartilhadas. Uma galeria é implantada em uma região.
- Definições de imagem – um agrupamento conceitual para imagens. 
- Versões de imagem – Isso é um tipo de imagem usado para implantar um conjunto de escala ou de VM. Versões de imagem podem ser replicadas para outras regiões onde as VMs precisam ser implantados.
 
Antes de distribuir para a Galeria de imagens, você deve criar uma galeria e uma definição de imagem, consulte [compartilhado imagens](shared-images.md). 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Distribua as propriedades para galerias de imagens compartilhadas:

- **type** - sharedImage  
- **galleryImageId** – ID da Galeria de imagens compartilhadas. É o formato: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>.
- **runOutputName** – exclusivo nome para identificar a distribuição.  
- **artifactTags** -marcas do par de valor de chave especificado pelo usuário opcional.
- **replicationRegions** -matriz de regiões para replicação. Uma das regiões deve ser a região em que a Galeria é implantada.
 
> [!NOTE]
> Você pode usar o construtor de imagens do Azure em uma região diferente para a Galeria, mas o serviço Azure Image Builder será necessário transferir a imagem entre os data centers e levará mais tempo. Image Builder será automaticamente versão a imagem, com base em um inteiro monotônico, não é possível especificá-lo no momento. 

### <a name="distribute-vhd"></a>Distribua: VHD   
Você pode dar saída a um VHD. Você pode, em seguida, copie o VHD e usá-lo para publicar no Azure MarketPlace ou usar com o Azure Stack.  

```json
 { 
     "type": "VHD",
     "runOutputName": "<VHD name>",
     "tags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
 }
```
 
Suporte a SO: Windows e Linux

Distribua os parâmetros VHD:

- **tipo** -VHD.
- **runOutputName** – exclusivo nome para identificar a distribuição.  
- **marcas** -marcas do par de valor de chave especificado pelo usuário opcional.
 
Construtor de imagens do Azure não permite ao usuário especificar um local de conta de armazenamento, mas você pode consultar o status do `runOutputs` para obter o local.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Depois que o VHD foi criado, copie-o para um local diferente, assim que possível. O VHD está armazenado em uma conta de armazenamento no grupo de recursos temporário criado quando o modelo de imagem é enviado para o serviço de construtor de imagens do Azure. Se você excluir o modelo de imagem, você perderá o VHD. 
 
## <a name="next-steps"></a>Próximas etapas

Há arquivos. JSON de exemplo para diferentes cenários na [Azure imagem construtor GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
