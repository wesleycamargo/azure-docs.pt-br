<properties
   pageTitle="Solucionando problemas de falhas de extensões de VM do Azure | Microsoft Azure"
   description="Saiba mais sobre como solucionar falhas da Extensão de VM do Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Solucionando problemas de falhas da Extensão da VM.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda o uso do modelo de implantação do Gerenciador de Recursos.

## Visão geral dos Modelos do Gerenciador de Recursos do Azure.

O Modelo do Gerenciador de Recursos do Azure permite que você especifique declarativamente a infraestrutura IaaS do Azure em linguagem Json definindo as dependências entre os recursos.


Clique no artigo [Criando modelos de extensão](virtual-machines-extensions-authoring-templates.md) para saber mais sobre como criar modelos para uso de extensões.

Neste artigo, aprenderemos a solucionar algumas das falhas comuns da Extensão de VM.

## Exibindo o status da Extensão:
Os modelos do Gerenciador de Recursos do Azure podem ser executados no Azure Powershell ou na CLI do Azure. Depois que o modelo for executado, o status da extensão poderá ser exibido no Gerenciador de Recursos do Azure ou nas ferramentas de linha de comando. Estes são alguns exemplos:

CLI do Azure:

      azure vm get-instance-view

Azure Powershell:

      Get-AzureVM -ResourceGroupName $RGName -Name $vmName -Status

Veja o exemplo de saída:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## Solucionando falhas de Extensão:

### Executando novamente a extensão na VM:

Se estiver executando scripts na VM usando a Extensão de Script Personalizado, às vezes, você poderá se deparar com um erro em que a VM foi criada com êxito, mas o script falhou. Nessas condições, a maneira recomendada de se recuperar desse erro é remover a extensão e executar o modelo novamente. Observação: no futuro, essa funcionalidade será aprimorada para eliminar a necessidade de desinstalar a extensão.

#### Remova a extensão pela CLI do Azure:

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Onde "publsher-name" corresponde ao tipo de extensão da saída de "azure vm get-instance-view" e name é o nome do recurso de extensão do modelo

#### Remova a extensão do Azure PowerShell:

    Remove-AzureVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Depois que a extensão tiver sido removida, o modelo poderá ser executado novamente para executar os scripts na VM.

<!---HONumber=Oct15_HO1-->