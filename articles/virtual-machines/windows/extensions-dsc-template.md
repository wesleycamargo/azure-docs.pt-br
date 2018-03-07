---
title: "Extensão de configuração de estado desejado com os modelos do Azure Resource Manager | Microsoft Docs"
description: "Saiba mais sobre a definição do modelo do Resource Manager para extensão da Configuração do Estado Desejado (DSC) no Azure."
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: 0f1c53c9eafcd96e49232b75d46ef34537a1160f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Extensão de configuração de estado desejado com os modelos do Azure Resource Manager

Este artigo descreve o modelo do Azure Resource Manager para o [manipulador de extensão da Configuração de Estado Desejado (DSC)](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

> [!NOTE]
> Você pode encontrar exemplos de esquema ligeiramente diferente. A alteração no esquema ocorreu na versão de outubro de 2016. Para obter detalhes, consulte [Atualização do formato anterior](#update-from-the-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Exemplo de modelo para uma VM do Windows

O trecho a seguir vai na seção **Recursos** do modelo. A extensão de DSC herda propriedades de extensão padrão. Para obter mais informações, consulte a [classe VirtualMachineExtension](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.).

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.72",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                    "configurationArguments": {
                        {
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        "RegistrationUrl" : "[parameters('registrationUrl1')]",
                        "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Exemplo de modelo para conjunto de dimensionamento de máquinas virtuais do Windows

Um nó de conjunto de dimensionamento de máquinas virtuais tem uma seção **propriedades** com um atributo **VirtualMachineProfile,** extensionProfile. Em **extensões**, adicione DSC.

A extensão de DSC herda propriedades de extensão padrão. Para obter mais informações, consulte a [classe VirtualMachineScaleSetExtension](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.72",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configurationArguments": {
                                {
                                    "Name": "RegistrationKey",
                                    "Value": {
                                        "UserName": "PLACEHOLDER_DONOTUSE",
                                        "Password": "PrivateSettingsRef:registrationKeyPrivate"
                                    },
                                },
                                "RegistrationUrl" : "[parameters('registrationUrl1')]",
                                "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
                ]
            }
        }
```

## <a name="detailed-settings-information"></a>Informações de configuração detalhadas

Use o esquema a seguir na seção **configurações** da extensão de DSC do Azure em um modelo do Resource Manager.

Para obter uma lista de argumentos disponíveis para o script de configuração padrão, consulte [Script de configuração padrão](#default-configuration-script).

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  }
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>Detalhes

| Nome da propriedade | type | DESCRIÇÃO |
| --- | --- | --- |
| settings.wmfVersion |string |Especifica a versão do Windows Management Framework (WMF) que deve ser instalada em sua VM. Configurar essa propriedade como **latest** instala a versão mais recente de WMF. Atualmente, os únicos valores possíveis para essa propriedade são **4.0**, **5.0**, **5.0PP** e **latest**. Esses valores possíveis estão sujeitos a atualizações. O valor padrão é **latest**. |
| settings.configuration.url |string |Especifica o local da URL de onde baixar seu arquivo .zip de configuração DSC. Se a URL fornecida exigir um token SAS para acesso, defina a propriedade **protectedSettings.configurationUrlSasToken** como o valor do seu token de SAS. Esta propriedade será necessária se **settings.configuration.script** ou **settings.configuration.function** estiverem definidas. Se nenhum valor for fornecido para essas propriedades, a extensão chama o script de configuração padrão para definir os metadados do LCM (Location Configuration Manager) e os argumentos devem ser fornecidos. |
| settings.configuration.script |string |Especifica o nome do arquivo do script que contém a definição de sua configuração DSC. Esse script deve estar na pasta raiz do arquivo .zip que é baixado da URL especificada pela propriedade **configuration.url**. Esta propriedade é necessária se **settings.configuration.url** ou **settings.configuration.script** estiverem definidas. Se nenhum valor for fornecido para essas propriedades, a extensão chama o script de configuração padrão para definir os metadados do LCM e os argumentos devem ser fornecidos. |
| settings.configuration.function |string |Especifica o nome da configuração DSC. A configuração que é denominada deve estar contida no script definido por **configuration.script**. Esta propriedade será necessária se **settings.configuration.script.url** ou **settings.configuration.function** estiverem definidas. Se nenhum valor for fornecido para essas propriedades, a extensão chama o script de configuração padrão para definir os metadados do LCM e os argumentos devem ser fornecidos. |
| settings.configurationArguments |Coleção |Define os parâmetros que você deseja passar para a configuração de DSC. Essa propriedade não é criptografada. |
| settings.configurationData.url |string |Especifica a URL de onde baixar o arquivo de dados de configuração (.psd1) para usar como entrada para a sua configuração de DSC. Se a URL fornecida exigir um token SAS para acesso, defina a propriedade **protectedSettings.configurationDataUrlSasToken** como o valor do seu token de SAS. |
| settings.privacy.dataEnabled |string |Habilita ou desabilita a coleta de telemetria. Os únicos valores possíveis para essa propriedade são **Enable**, **Disable**, **''** ou **$null**. Deixar esta propriedade em branco ou nulo permite telemetria. O valor padrão é **''**. Para obter mais informações, consulte [coleta de dados de extensão de DSC do Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Coleção |Define os locais alternativos de onde baixar o WMF. Para obter mais informações, consulte [extensão de DSC do Azure 2.8 e como mapear downloads das dependências de extensão para seu próprio local](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Coleção |Define os parâmetros que você deseja passar para a configuração de DSC. Essa propriedade é criptografada. |
| protectedSettings.configurationUrlSasToken |string |Especifica o token SAS para usar o acesso a URL definida por **configuration.url**. Essa propriedade é criptografada. |
| protectedSettings.configurationDataUrlSasToken |string |Especifica o token SAS para usar o acesso a URL definida por **configurationData.url**. Essa propriedade é criptografada. |

## <a name="default-configuration-script"></a>Script de configuração padrão

Para obter mais informações sobre os seguintes valores, consulte [Configurações Básicas do Local Configuration Manager](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#basic-settings). Você pode usar o script de configuração padrão de extensão DSC para configurar apenas as propriedades do LCM que são listadas na tabela a seguir.

| Nome da propriedade | type | DESCRIÇÃO |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |secureString |Propriedade exigida. Especifica a chave que é usada para um nó para registrar com o serviço de Automação do Azure como a senha de um objeto de credencial do PowerShell. Esse valor pode ser descoberto automaticamente usando o método **listkeys** em relação à conta de Automação. O valor deve ser protegido como uma configuração protegida. |
| settings.configurationArguments.RegistrationUrl |string |Propriedade exigida. Especifica a URL do ponto de extremidade de Automação em que o nó tenta registrar. Esse valor pode ser descoberto automaticamente usando o método de **referência** em relação à conta de Automação. |
| settings.configurationArguments.NodeConfigurationName |string |Propriedade exigida. Especifica a configuração de nó na conta de Automação para atribuir ao nó. |
| settings.configurationArguments.ConfigurationMode |string |Especifica o modo para o LCM. As opções válidas incluem **ApplyOny**, **ApplyandMonitior** e **ApplyandAutoCorrect**.  O valor padrão é **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Especifica com que frequência o LCM tenta verificar a conta de automação para atualizações.  O valor padrão é **30**.  O valor mínimo é **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Especifica com que frequência o LCM valida a configuração atual. O valor padrão é **15**. O valor mínimo é **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | booleano | Especifica se um nó pode ser reinicializado automaticamente se uma operação de DSC solicitar. O valor padrão é **false**. |
| settings.configurationArguments.ActionAfterReboot | string | Especifica o que acontece após uma reinicialização ao aplicar uma configuração. As opções válidas são **ContinueConfiguration** e **StopConfiguration**. O valor padrão é **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | booleano | Especifica se o LCM substitui os módulos existentes no nó. O valor padrão é **false**. |

## <a name="settings-vs-protectedsettings"></a>Settings versus ProtectedSettings

Todas as configurações foram salvas em um arquivo de texto de configurações na VM. Propriedades listadas em **Configurações** são propriedades públicas. Propriedades públicas não são criptografadas no arquivo de texto de configurações. Propriedades em **protectedSettings** são criptografadas com um certificado e não são mostradas em texto sem formatação no arquivo de configurações na VM.

Se a configuração precisar de credenciais, você pode incluir as credenciais em **protectedSettings**:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Exemplo de script de configuração

O exemplo a seguir mostra o comportamento padrão para a extensão de DSC, que é fornecer configurações de metadados para o LCM e registrar com o serviço de DSC de Automação. Os argumentos de configuração são necessários.  Os argumentos de configuração são passados para o script de configuração padrão para definir metadados do LCM.

```json
"settings": {
    "configurationArguments": {
        {
            "Name": "RegistrationKey",
            "Value": {
                "UserName": "PLACEHOLDER_DONOTUSE",
                "Password": "PrivateSettingsRef:registrationKeyPrivate"
            },
        },
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
  }
},
"protectedSettings": {
    "Items": {
                "registrationKeyPrivate": "[parameters('registrationKey1']"
            }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Exemplo usando o script de configuração no Armazenamento do Azure

O exemplo a seguir é da [visão geral de manipulador de extensão de DSC](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este exemplo usa modelos do Resource Manager, em vez de cmdlets para implantar a extensão. Salve a configuração de IisInstall.ps1, coloque-o em um arquivo .zip e carregue-o em uma URL acessível. Este exemplo usa o Armazenamento de Blobs do Azure, mas você pode baixar os arquivos .zip de qualquer local aleatório.

No modelo do Resource Manager, o código a seguir instrui a VM a baixar o arquivo correto e, em seguida, executar a função apropriada do PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="update-from-a-previous-format"></a>Atualizar de um formato anterior

Todas as configurações em um formato anterior da extensão (e que têm as propriedades públicas **ModulesUrl**, **ConfigurationFunction**, **SasToken** ou **Properties**) serão automaticamente adaptadas ao formato atual da extensão. Elas são executadas como antes.

O esquema a seguir mostra como se parecia o esquema de configurações anterior:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Veja como formato anterior se adapta ao formato atual:

| Nome da propriedade | Equivalente ao esquema anterior |
| --- | --- |
| settings.wmfVersion |settings.wmfVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Primeira parte de settings.ConfigurationFunction (antes de \\\\) |
| settings.configuration.function |Segunda parte de settings.ConfigurationFunction (depois de \\\\) |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (sem token SAS) |
| settings.privacy.dataEnabled |settings.privacy.dataEnabled |
| settings.advancedOptions.downloadMappings |settings.advancedOptions.downloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token SAS de protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Solução de problemas - Código de erro 1100

Código de erro 1100 indica um problema com a entrada do usuário para a extensão de DSC. O texto desses erros varia e pode ser alterado. Confira alguns erros que você pode encontrar e como corrigi-los.

### <a name="invalid-values"></a>Valores inválidos

"Privacy.dataCollection é '{0}'.
Os únicos valores possíveis são '', 'Enable' e 'Disable'".
"WmfVersion é '{0}'.
Únicos valores possíveis são... e 'latest'".

**Problema**: um valor fornecido não é permitido.

**Solução**: altere o valor inválido para um valor válido. Para saber mais, consulte a tabela em [Detalhes](#details).

### <a name="invalid-url"></a>URL inválida

"ConfigurationData.url é '{0}'. Essa não é uma URL válida" "DataBlobUri é '{0}'. Essa não é uma URL válida" "Configuration.url é '{0}'. Essa não é uma URL válida"

**Problema**: uma URL fornecida não é válida.

**Solução**: verifique todas as URLs fornecidas. Certifique-se de que todas as URLs resolvem para locais válidos que a extensão pode acessar no computador remoto.

### <a name="invalid-configurationargument-type"></a>Tipo ConfigurationArgument inválido

"Tipo configurationArguments inválido {0}"

**Problema**: a propriedade *ConfigurationArguments* não pode resolver um objeto **Hashtable**.

**Solução**: torne sua propriedade *ConfigurationArguments* uma **Hashtable**. Siga o formato fornecido no exemplo anterior. Fique atento às chaves, vírgulas e aspas.

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments duplicado

"Argumentos '{0}' duplicados encontrados em configurationArguments públicos e protegidos"

**Problema**: *ConfigurationArguments* nas configurações públicas, e *ConfigurationArguments* nas configurações protegidas têm propriedades com o mesmo nome.

**Solução**: remova uma das propriedades duplicadas.

### <a name="missing-properties"></a>Propriedades ausentes
"Configuration.function exige que configuration.url ou configuration.module seja especificado"

"Configuration.url exige que configuration.script seja especificado"

"Configuration.script exige que configuration.url seja especificado"

"Configuration.url exige que configuration.function seja especificado"

"ConfigurationUrlSasToken exige que configuration.url seja especificado"

"ConfigurationDataUrlSasToken exige que configurationData.url seja especificado"

**Problema**: uma propriedade definida precisa de outra propriedade que não está presente.

**Soluções**:

- Forneça a propriedade ausente.
- Remova a propriedade que precisa da propriedade ausente.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Uso de conjuntos de dimensionamento de máquinas virtuais com a extensão de DSC do Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
* Encontre mais detalhes sobre [Gerenciamento de credenciais seguras do DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Obtenha uma [introdução ao manipulador de extensões DSC do Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para saber mais sobre a DSC do PowerShell, acesse o [centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).
