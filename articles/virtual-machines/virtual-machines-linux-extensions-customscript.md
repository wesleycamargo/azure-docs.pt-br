<properties
   pageTitle="Scripts personalizados em VMs do Linux | Microsoft Azure"
   description="Automatizar tarefas de configuração de VM do Linux usando a extensão de script personalizado"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="nepeters"/>

# Uso da extensão de script personalizado do Azure com máquinas virtuais do Linux

A extensão de script personalizado baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Scripts podem ser baixados do armazenamento do Azure ou de outra localização acessível da Internet ou fornecidos para o tempo de execução da extensão. A extensão de script personalizado se integra com modelos do Azure Resource Manager e também pode ser executada usando a CLI do Azure, o PowerShell, o portal do Azure ou a API REST da máquina virtual do Azure.

Este documento fornece detalhes sobre como usar a extensão de script personalizado na CLI do Azure e um modelo do Azure Resource Manager e também fornece detalhes sobre as etapas de solução de problemas em sistemas Linux.

## Configuração da extensão

A configuração de extensão de script personalizado especifica itens como localização de script e o comando a ser executado. Essa configuração pode ser armazenada em arquivos de configuração, especificada na linha de comando ou em um modelo do Azure Resource Manager. Dados confidenciais podem ser armazenados em uma configuração protegida, que é criptografada e descriptografada somente dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

### Configuração pública

Esquema:

- **commandToExecute**: (obrigatório, cadeia de caracteres) o script de ponto de entrada a ser executado
- **fileUris**: (opcional, matriz de cadeia de caracteres) as URLs de arquivos a serem baixados.
- **timestamp** (opcional, inteiro) use este campo somente para disparar uma nova execução do script, alterando o valor desse campo.

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### Configuração protegida

Esquema:

- **commandToExecute**: (opcional, cadeia de caracteres) o script de ponto de entrada a ser executado. Use este campo se o comando tiver segredos, como senhas.
- **storageAccountName**: (opcional, cadeia de caracteres) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, todos os fileUris deverão ser URLs para Blobs do Azure.
- **storageAccountKey**: (opcional, cadeia de caracteres) a tecla de acesso da conta de armazenamento.


```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## CLI do Azure

Ao usar a CLI do Azure para executar a extensão de script personalizado, crie um arquivo de configuração ou arquivos que contêm pelo menos o URI do arquivo e o comando de execução do script.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

Como opção, o comando pode ser executado usando a opção `--public-config` e `--private-config`, que permite especificar a configuração durante a execução e sem um arquivo de configuração separado.

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### Exemplos de CLI do Azure

**Exemplo 1** – Configuração pública com o arquivo de script.

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Comando CLI do Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Exemplo 2** – Configuração pública sem nenhum arquivo de script.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando CLI do Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**Exemplo 3** – Um arquivo de configuração pública é usado para especificar o URI do arquivo de script, e um arquivo de configuração protegida é usado para especificar o comando a ser executado.

Arquivo de configuração pública:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

Arquivo de configuração protegida:

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Comando CLI do Azure:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## Modelo do Resource Manager

A extensão de script personalizado do Azure pode ser executada no momento da implantação de máquina virtual usando um modelo do Resource Manager. Para fazer isso, adicione JSON formatado corretamente ao modelo de implantação.

### Exemplos do Resource Manager

**Exemplo 1** – Configuração pública.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Exemplo 2** – Comando de execução na configuração protegida.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Consulte a Demonstração da Loja de Música do .Net Core para ver um exemplo completo – [Demonstração da Loja de Música](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## Solucionar problemas

Quando a extensão de script personalizado é executada, o script é criado ou baixado em um diretório semelhante ao exemplo a seguir. A saída do comando também é salva nesse diretório no arquivo `stdout` e `stderr`.

```none
/var/lib/azure/custom-script/download/0/
```

A extensão de script do Azure produz um log, que pode ser encontrado aqui.

```none
/var/log/azure/customscript/handler.log
```

O estado de execução da extensão de script personalizado também pode ser recuperado com a CLI do Azure.

```none
azure vm extension get <resource-group> <vm-name>
```

A saída se parece com o seguinte texto:

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## Próximas etapas

Para obter informações sobre outras extensões de script de VM, consulte [Visão geral da extensão de script do Azure para Linux](./virtual-machines-linux-extensions-features.md).

<!---HONumber=AcomDC_0928_2016-->