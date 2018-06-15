---
title: Extensão de script personalizado do Azure para Windows | Microsoft Docs
description: Automatizar tarefas de configuração de VM do Windows usando a Extensão de Script Personalizado
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: danis
ms.openlocfilehash: 80f9ecd40c5b9504a6554b95bf374046d8253933
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809770"
---
# <a name="custom-script-extension-for-windows"></a>Extensão de script personalizado para o Windows

A extensão de script personalizado baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão. A extensão de script personalizado se integra com modelos do Azure Resource Manager e também pode ser executada usando a CLI do Azure, o PowerShell, o portal do Azure ou a API REST da máquina virtual do Azure.

Este documento detalha como usar a Extensão de Script Personalizado usando o módulo do Azure PowerShell e modelos do Azure Resource Manager, além de detalhar as etapas da solução de problemas em sistemas Windows.

## <a name="prerequisites"></a>pré-requisitos

> [!NOTE]  
> Não use a Extensão de Script Personalizado para executar Update-AzureRmVM com a mesma VM que seu parâmetro, pois ela aguardará por si própria.  
>   
> 

### <a name="operating-system"></a>Sistema operacional

A Extensão de Script Personalizado para Linux será executada nos SOs de extensão com suporte. Para obter mais informações, confira este [artigo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Local do script

Você pode utilizar a extensão para usar suas credenciais do Armazenamento de Blobs do Azure para acessar esse armazenamento. Como alternativa, o local do script pode ser qualquer lugar, desde que a VM possa rotear para esse ponto de extremidade, como GitHub, servidor de arquivos interno, etc.


### <a name="internet-connectivity"></a>Conectividade com a Internet
Se você precisar fazer o download um script externamente, como do GitHub ou do Armazenamento do Azure, será necessário abrir portas adicionais do firewall ou do Grupo de Segurança de Rede. Por exemplo, se o script estiver localizado no Armazenamento do Azure, você poderá permitir acesso usando Marcas de Serviço do NSG do Azure para [Armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Se o script estiver em um servidor local, ainda poderá ser necessário abrir portas adicionais do firewall ou do Grupo de Segurança de Rede.

### <a name="tips-and-tricks"></a>Dicas e truques
* A taxa de falha mais alta para esta extensão acontece devido a erros de sintaxe no script. Teste as execuções de script sem erros e também insira um registro em log adicional no script para facilitar a localização da falha.
* Escreva scripts idempotentes, para que se forem executados mais de uma vez por acidente, eles não causem alterações no sistema.
* Verifique se os scripts não exigem entrada do usuário quando são executados.
* É permitido que o script seja executado em até 90 minutos. Um período mais longo resultará em falha na provisão da extensão.
* Não coloque reinicializações no script, pois isso causará problemas com outras extensões que estão sendo instaladas e, após a reinicialização, a extensão será interrompida. 
* Se você tiver um script que causará uma reinicialização, instale aplicativos, execute scripts, etc. Você deve agendar a reinicialização, usando uma Tarefa Agendada do Windows ou usando ferramentas como o DSC ou extensões do Chef ou do Puppet.
* A extensão executará um script somente uma vez. Se você quiser executar um script em cada inicialização, use a extensão pra criar uma Tarefa Agendada do Windows.
* Se você quiser agendar quando um script será executado, use a extensão para criar uma Tarefa Agendada do Windows. 
* Quando o script for executado, apenas será exibido o status da extensão 'em transição' no portal do Azure ou na CLI. Se você quiser atualizações de status mais frequentes de um script em execução, será necessário criar sua própria solução.
* A extensão de script personalizado não dá suporte nativo para servidores proxy. No entanto, é possível usar uma ferramenta de transferência de arquivos que dá suporte a servidores proxy no script, como a *Curl* 
* Lembre-se de locais de diretório não padrão nos quais seus scripts ou comandos podem confiar e ter lógica para lidar com isso.


## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica itens como localização de script e o comando a ser executado. Você pode armazenar essa configuração em arquivos de configuração, especificá-la na linha de comando ou especificá-la em um modelo do Azure Resource Manager. 

Você pode armazenar dados confidenciais em uma configuração protegida, que é criptografada e descriptografada somente dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de definição protegida por extensões. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```
**Observação** – somente uma única versão de uma extensão pode ser instalada em uma VM em um determinado momento. A especificação de um script personalizado duas vezes no mesmo modelo do Resource Manager para a mesma VM falhará. 

### <a name="property-values"></a>Valores de propriedade

| NOME | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| publicador | Microsoft.Compute | string |
| Tipo | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (por exemplo) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matriz |
| commandToExecute (por exemplo) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (por exemplo) | examplestorageacct | string |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Esses nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implantação, use os nomes conforme mostrado aqui.

#### <a name="property-value-details"></a>Detalhes de valor de propriedade
 * `commandToExecute`: (**necessária**, cadeia de caracteres) o script de ponto de entrada a ser executado. Use esse campo se o comando contiver segredos, como senhas, ou se os fileUris diferenciarem maiúsculas de minúsculas.
* `fileUris`: (opcional, matriz de cadeia de caracteres) as URLs dos arquivos a serem baixados.
* `storageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, todos os `fileUris` deverão ser URLs para Blobs do Azure.
* `storageAccountKey`: (opcional, cadeia de caracteres) a chave de acesso da conta de armazenamento

Os valores a seguir podem ser definidos nas configurações públicas ou protegidas. A extensão rejeitará qualquer configuração em que os valores abaixo estejam definidos tanto nas configurações protegidas quanto nas públicas.
* `commandToExecute`

Usar as configurações públicas pode ser útil para depuração, mas é altamente recomendável usar as configurações protegidas.

As configurações públicas são enviadas em texto não criptografado para a VM na qual o script será executado.  As configurações protegidas são criptografadas usando uma chave conhecida apenas pelo Azure e pela VM. As configurações são salvas na VM no estado em que foram enviadas, ou seja, se foram criptografadas, elas serão salvas criptografadas na VM. O certificado usado para descriptografar os valores criptografados é armazenado na VM e usado para descriptografar as configurações (se necessário) no tempo de execução.

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a Extensão de Script Personalizado durante uma implantação de modelo do Azure Resource Manager. Um modelo de exemplo que inclui a Extensão de Script Personalizado pode ser encontrado aqui, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Implantação do PowerShell

O comando `Set-AzureRmVMCustomScriptExtension` pode ser usado para adicionar a Extensão de Script Personalizado a uma máquina virtual existente. Para saber mais, confira [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>Exemplos adicionais

### <a name="using-multiple-script"></a>Usando vários scripts
Neste exemplo, há três scripts que são usados para criar o servidor. O 'commandToExecute' chama o primeiro script e, em seguida, há opções para chamar os outros. Por exemplo, é possível usar um script mestre que controla a execução, com as definições certas de tratamento de erro, de registro em log e de gerenciamento de estado.

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzureRmVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Executando scripts de um compartilhamento local
Neste exemplo, é possível usar um servidor SMB local como o local do script. Observe que você não precisa passar outras configurações, exceto a *commandToExecute*.

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzureRmVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Como executar o script personalizado mais de uma vez com a CLI
Se você desejar executar a extensão do script personalizado mais de uma vez, isso somente poderá ser feito sob estas condições:
1. O parâmetro 'Name' da extensão é o mesmo que o da implantação anterior da extensão.
2. Você precisa atualizar a configuração, caso contrário, o comando não será executado novamente, por exemplo, você pode adicionar uma propriedade dinâmica ao comando, como um carimbo de hora. 

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados no Portal do Azure usando o módulo do Azure PowerShell. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A saída de execução da extensão é registrada nos arquivos localizados no diretório a seguir da máquina virtual de destino.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os arquivos especificados são baixados no diretório a seguir da máquina virtual de destino.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
em que `<n>` é um inteiro decimal que pode ser alterado entre as execuções da extensão.  O valor `1.*` corresponde ao valor `typeHandlerVersion` atual e real da extensão.  Por exemplo, o diretório real pode ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Ao executar o comando `commandToExecute`, a extensão configura esse diretório (por exemplo, `...\Downloads\2`) como o diretório de trabalho atual. Isso permite o uso de caminhos relativos para localizar os arquivos baixados por meio da propriedade `fileURIs`. Veja a tabela abaixo para obter exemplos.

Como o caminho absoluto do download pode variar ao longo do tempo, é melhor optar por caminhos de arquivo/script relativos na cadeia de caracteres `commandToExecute` sempre que possível. Por exemplo: 
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

As informações de caminho após o primeiro segmento do URI são retidas para os arquivos baixados por meio da lista de propriedades `fileUris`.  Conforme mostrado na tabela a seguir, os arquivos baixados são mapeados em subdiretórios de download para refletir a estrutura dos valores `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de Arquivos Baixados

| URI no fileUris | Localização baixada relativa | Localização baixada absoluta * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Conforme indicado acima, os caminhos de diretório absolutos são alterados durante o tempo de vida da VM, mas não em uma única execução da extensão CustomScript.

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
