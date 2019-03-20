---
title: Parar de usar a extensão de VM de identidade gerenciada e começar a usar o ponto de extremidade de serviço de metadados de instância do Azure
description: Etapa instruções passo a passo para parar de usar a extensão de VM e começar a usar o serviço de metadados de instância (IMDS) do Azure para autenticação.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: priyamo
ms.openlocfilehash: c29eb22f2606fc44cfd7d948f2c363cb51c811dd
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227530"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Como parar de usar a máquina virtual gerenciados a extensão de identidades e começar a usar o serviço de metadados de instância do Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Extensão da máquina virtual para identidades gerenciadas

A extensão de máquina virtual para identidades gerenciadas é usada para solicitar tokens para uma identidade gerenciada dentro da máquina virtual. O fluxo de trabalho consiste as seguintes etapas:

1. Em primeiro lugar, a carga de trabalho no recurso chama o ponto de extremidade local `http://localhost/oauth2/token` para solicitar um token de acesso.
2. A extensão de máquina virtual, em seguida, usa as credenciais para a identidade gerenciada, para solicitar um token de acesso do AD do Azure... 
3. O token de acesso é retornado ao chamador e pode ser usado para autenticar os serviços que dão suporte à autenticação do AD do Azure, como o Azure Key Vault ou armazenamento do Azure.

Devido a várias limitações descritas na próxima seção, a extensão de VM de identidade gerenciada foi preterida em favor do uso do ponto de extremidade equivalente no serviço de metadados de instância para Azure (IMDS)

### <a name="provision-the-extension"></a>Provisionar a extensão 

Quando você configura uma máquina virtual ou definida para ter uma identidade gerenciada de dimensionamento de máquinas virtuais, você poderá opcional escolher, você pode optar por provisionar as identidades de gerenciado para a extensão VM de recursos do Azure usando o `-Type` parâmetro sobre o [ Conjunto AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) cmdlet. Você pode transmitir `ManagedIdentityExtensionForWindows` ou `ManagedIdentityExtensionForLinux`, dependendo do tipo de máquina virtual e nomeie-o usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Você também pode usar o modelo de implantação do Azure Resource Manager para provisionar a extensão de VM, adicionando o seguinte JSON para o `resources` seção para o modelo (usar `ManagedIdentityExtensionForLinux` para os elementos nome e tipo de versão do Linux).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
Se você estiver trabalhando com conjuntos de dimensionamento de máquina virtual, você também pode provisionar as identidades de gerenciado para conjunto de dimensionamento de máquina virtual de recursos do Azure extensão usando o [AzVmssExtension adicionar](/powershell/module/az.compute/add-azvmssextension) cmdlet. Você pode transmitir `ManagedIdentityExtensionForWindows` ou `ManagedIdentityExtensionForLinux`, dependendo do tipo de dimensionamento de máquinas virtuais definido e nomeie-o usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Para provisionar o dimensionamento de máquinas virtuais definir a extensão com o modelo de implantação do Azure Resource Manager, adicione o seguinte JSON para o `extensionpProfile` seção para o modelo (usar `ManagedIdentityExtensionForLinux` para os elementos nome e tipo de versão do Linux).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

Provisionamento de extensão da máquina virtual pode falhar devido a falhas de pesquisa de DNS. Se isso ocorrer, reinicie a máquina virtual e tente novamente. 

### <a name="remove-the-extension"></a>Remover a extensão 
Para remover a extensão, use `-n ManagedIdentityExtensionForWindows` ou `-n ManagedIdentityExtensionForLinux` alterne (dependendo do tipo de máquina virtual) com [exclusão de extensão de vm az](https://docs.microsoft.com/cli/azure/vm/), ou [exclusão de extensão az vmss](https://docs.microsoft.com/cli/azure/vmss) para dimensionamento de máquinas virtuais define usando a CLI do Azure, ou `Remove-AzVMExtension` do Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Adquirir um token usando a extensão de máquina virtual

A seguir está um exemplo de solicitação usando as identidades de gerenciado para recursos do Azure ponto de extremidade de extensão de VM:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | DESCRIÇÃO |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar os dados do ponto de extremidade. Neste caso, um token de acesso OAuth. | 
| `http://localhost:50342/oauth2/token` | As identidades gerenciadas do ponto de extremidade de recursos do Azure, em que 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta que indica o URI da ID do aplicativo do recurso de destino. Ele também aparece na declaração `aud` (público) do token emitido. Este exemplo solicita um token para acessar o Azure Resource Manager, que tem um URI de ID do aplicativo de https://management.azure.com/. |
| `Metadata` | Um campo de cabeçalho de solicitação HTTP, exigido por identidades gerenciadas para recursos do Azure como uma atenuação contra ataque SSRF (Server Side Request Forgery). Esse valor deve ser definido como "true", com todas as letras minúsculas.|
| `object_id` | (Opcional) Um parâmetro de string de consulta, indicando o object_id da identidade gerenciada para a qual você deseja o token. Obrigatório, se a VM tiver várias identidades gerenciadas atribuídas ao usuário.|
| `client_id` | (Opcional) Um parâmetro de cadeia de consulta, indicando o client_id da identidade gerenciada para a qual você deseja o token. Obrigatório, se a VM tiver várias identidades gerenciadas atribuídas ao usuário.|


Exemplo de resposta:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elemento | DESCRIÇÃO |
| ------- | ----------- |
| `access_token` | O token de acesso solicitado. Ao chamar uma API REST protegida, o token é inserido no campo de cabeçalho de solicitação `Authorization` como um token "portador", permitindo que a API autentique o chamador. | 
| `refresh_token` | Não usado por identidades gerenciadas para recursos do Azure. |
| `expires_in` | O número de segundos que o token de acesso continua sendo válido antes da expiração desde o momento da emissão. A hora da emissão pode ser encontrada na declaração `iat` do token. |
| `expires_on` | O período de expiração do token de acesso. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `exp` do token). |
| `not_before` | O período para o token de acesso entrar em vigor e poder ser aceito. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `nbf` do token). |
| `resource` | O recurso para o qual o token de acesso foi solicitado, que corresponde ao parâmetro de cadeia de consulta `resource` da solicitação. |
| `token_type` | O tipo de token, que é um token de acesso "Portador", o que significa que o recurso pode conceder acesso ao portador desse token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Solucionar problemas da extensão de máquina virtual 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Reinicie a extensão de máquina virtual após uma falha

No Windows e em determinadas versões do Linux, se a extensão for interrompida, o cmdlet a seguir poderá ser usado para reiniciá-lo:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Em que: 
- Nome da extensão e o tipo para o Windows é: `ManagedIdentityExtensionForWindows`
- Nome da extensão e o tipo para o Linux é: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Script de automação" falha ao tentar exportar esquemas para identidades gerenciadas para a extensão de recursos do Azure

Quando as identidades gerenciadas para recursos do Azure está habilitada em uma máquina virtual, o erro a seguir é mostrado ao tentar usar o recurso "Script de automação" para a máquina virtual ou seu grupo de recursos:

![Identidades gerenciadas para erro de exportação de script de automação de recursos do Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

As identidades de gerenciado para a extensão de máquina virtual de recursos do Azure atualmente não oferece suporte para a capacidade de exportar seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra parâmetros de configuração para habilitar identidades gerenciadas para recursos do Azure no recurso. Essas seções podem ser adicionadas manualmente seguindo os exemplos [configurar gerenciadas identidades para recursos do Azure em uma máquina virtual do Azure usando modelos de](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquema está disponível para as identidades de gerenciado para a extensão de máquina virtual de recursos do Azure (planejada para substituição em janeiro de 2019), ele será listado no [exportar grupos de recursos que contêm extensões de VM ](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Limitações da extensão de máquina virtual 

Existem várias limitações importantes ao usar a extensão de máquina virtual. 

 * A limitação mais séria é o fato de que as credenciais usadas para solicitar tokens são armazenadas na máquina virtual. Um invasor que a máquina virtual de violações com êxito pode remover as credenciais. 
 * Além disso, a extensão da máquina virtual ainda sem suporte por várias distribuições do Linux, com um enorme custo para modificar, compilar e testar a extensão em cada um dessas distribuições de desenvolvimento. Atualmente, há suporte para as seguintes distribuições Linux: 
    * CoreOS Estável
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Há um impacto no desempenho à implantação de máquinas virtuais com identidades gerenciadas, como a extensão da máquina virtual também deve ser provisionado. 
 * Por fim, a extensão da máquina virtual suporta apenas ter 32 gerenciadas identidades atribuídas por máquina virtual. 

## <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure

O [serviço de metadados de instância (IMDS) do Azure](https://docs.microsoft.com/azure/virtual-machines/instance-metadata-service) é um ponto de extremidade REST que fornece informações sobre instâncias de máquina virtual que podem ser usadas para gerenciar e configurar suas máquinas virtuais em execução. O ponto de extremidade está disponível em um endereço IP não roteável conhecido (`169.254.169.254`) que podem ser acessados somente de dentro da máquina virtual.

Há diversas vantagens em usar o Azure IMDS para solicitar tokens. 

1. O serviço é externo à máquina virtual, portanto as credenciais usadas pelo identidades gerenciadas não estão mais presentes na máquina virtual. Em vez disso, eles são hospedados e protegidos no computador host de máquina virtual do Azure.   
2. Todos os sistemas operacionais Windows e Linux com suporte no Azure IaaS pode usar identidades gerenciadas.
3. Implantação é mais rápido e fácil, já que a extensão de VM não precisa ser provisionado.
4. Com o IMDS ponto de extremidade, até 1000 identidades gerenciados atribuída pelo usuário pode ser atribuído a uma única máquina virtual.
5. Não há nenhuma alteração significativa para as solicitações usando IMDS em vez de aqueles que usam a extensão de máquina virtual, portanto é bem simple para a porta em relação às implantações existentes que atualmente usam a extensão de máquina virtual.

Por esses motivos, o serviço de Azure IMDS será a maneira na escola ideal para solicitar tokens, depois que a extensão da máquina virtual foi preterido. 


## <a name="next-steps"></a>Próximas etapas

* [Como usar identidades gerenciadas para recursos do Azure em uma máquina virtual do Azure para adquirir um token de acesso](how-to-use-vm-token.md)
* [Serviço de metadados de instância do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
