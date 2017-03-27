---
title: "Perguntas frequentes sobre conjuntos de dimensionamento de máquina virtual do Azure | Microsoft Docs"
description: "Obtenha respostas a perguntas frequentes sobre Conjuntos de Dimensionamento de Máquina Virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/08/2017
ms.author: negat
ms.custom: na
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8d0adfb995ecfb56ff33814e462a88f3d1102d58
ms.lasthandoff: 03/15/2017


---

# <a name="azure-virtual-machine-scale-sets-faq"></a>Perguntas frequentes sobre conjuntos de dimensionamento de máquina virtual do Azure

Este artigo contém respostas a perguntas frequentes sobre conjuntos de dimensionamento.

## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>Quais são as práticas recomendadas para o dimensionamento automático do Azure?

Sim. Confira https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices

### <a name="where-do-i-find-the-metric-names-for-autoscaling-using-host-based-metrics"></a>Onde localizar os nomes de métrica para dimensionamento automático usando métricas baseados em host?

https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/

### <a name="are-there-any-examples-of-autoscaling-based-on-a-service-bus-topic-and-queue-length"></a>Há exemplos de dimensionamento automático com base em um tópico de barramento do serviço e comprimento de fila?

Sim. Consulte:

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/

Para a fila do barramento de serviço:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Para filas de armazenamento:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Substitua esses valores de exemplo pelos URIs de recursos apropriados.


### <a name="should-we-autoscale-with-host-based-metrics-or-use-a-diagnostics-extension"></a>Devemos dimensionar automaticamente com métricas baseadas em host ou usar uma extensão de diagnóstico?

Você pode criar uma configuração de dimensionamento automático em uma VM para usar métricas de nível de host ou usar métricas com base em sistema operacional convidado.

Confira esta lista de métricas com suporte: https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics. Aqui está um exemplo completo de conjuntos de dimensionamento (nesse caso, usamos a métrica de CPU de nível de host e uma métrica de contagem de mensagens):

https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets

### <a name="how-can-i-set-alert-rules-on-a-scale-set"></a>Como configurar regras de alerta em um conjunto de dimensionamento?

Você pode criar alertas em métricas em conjuntos de dimensionamento por meio de PS ou CLI. Consulte:

https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules

https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts

a TargetResourceId do conjunto de dimensionamento é semelhante a: /subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Você pode escolher qualquer contador de desempenho de VM como a métrica para alertar sobre:

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-windows-vm-v2-as-a-guest-os

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-linux-vm-v2-as-a-guest-os

### <a name="how-can-i-set-up-autoscale-on-a-scale-set-using-powershell"></a>Como configurar o dimensionamento automático em um conjunto de dimensionamento usando o PowerShell?

Confira https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/




## <a name="certificates"></a>Certificados

### <a name="how-do-you-securely-ship-a-certificate-into-the-vm--is-there-an-example-of-provisioning-a-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration--the-common-certificate-rotation-operation-would-amount-to-a-configuration-update-operation"></a>Como enviar com segurança um certificado na VM?  Há um exemplo de provisionamento de um conjunto de dimensionamento para executar um site em que o SSL do site é fornecido com segurança de uma configuração de certificado?  A operação de rotação comum de certificados corresponderia a uma operação de atualização de configuração.

Damos suporte à instalação de certificados de cliente diretamente no repositório de certificados do Windows do cofre de chaves.

No contexto de conjuntos de dimensionamento...

https://msdn.microsoft.com/library/mt589035.aspx

```json
        "secrets": [ {
              "sourceVault": {
                      "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
          },
          "vaultCertificates": [ {
                      "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                  "certificateStore": "certificateStoreName"
          } ]
        } ]
```

Ele dá suporte a Windows e Linux.

### <a name="self-signed-certificate-example"></a>Exemplo de certificado autoassinado:

#### <a name="create-a-self-signed-cert-in-a-keyvault"></a>Criar um certificado autoassinado em um KeyVault

Uma maneira de criar um certificado autoassinado em um KeyVault é usar as instruções deste artigo do Service Fabric aqui: https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/

Os comandos do PowerShell:

```powershell
Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

Login-AzureRmAccount

Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
```

O comando anterior fornece a entrada para o modelo do Resource Manager.

#### <a name="change-resource-manager-template"></a>Alterar modelo do Azure Resource Manager

Adicione essa propriedade a "virtualMachineProfile" como parte do Recurso de conjunto de dimensionamento:

```json 
"osProfile": {
            "computerNamePrefix": "[variables('namingInfix')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]",
            "secrets": [
              {
                "sourceVault": {
                  "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                  {
                    "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                    "certificateStore": "My"
                  }
                ]
              }
            ]
          }
```
 

### <a name="is-there-a-way-to-specify-an-ssh-key-pair-that-i-want-to-use-for-ssh-authentication-with-a-linux-scale-set-from-a-resource-manager-template"></a>Há uma maneira de especificar um par de chaves SSH que desejo usar para autenticação SSH com um conjunto de dimensionamento Linux de um modelo do Resource Manager?  

A API REST para osProfile é semelhante ao caso de VM comum:
 
https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration
 
Inclua um `osProfile` em seu modelo, semelhante ao seguinte exemplo:

```json 
"osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUserName')]",
          "linuxConfiguration": {
            "disablePasswordAuthentication": "true",
            "ssh": {
              "publicKeys": [
                {
                  "path": "[variables('sshKeyPath')]",
                  "keyData": "[parameters('sshKeyData')]"
                }
              ]
            }
          }
        }
```
 
Esse bloco JSON é usado no seguinte modelo de início rápido:
 
https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
Confira também o Perfil do Sistema Operacional neste modelo:
 
https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json

### <a name="how-do-i-remove-deprecated-certificates"></a>Como remover certificados preteridos? 

Você deve remover o certificado antigo da lista de certificados do cofre, mas manter todos os certificados que deseja reter em seu computador. Isso não remove o certificado todas as VMs, mas também não adiciona o certificado a novas VMs que são criadas no conjunto de dimensionamento. Para remover o certificado de VMs existentes, você deve escrever uma extensão de script personalizado que remova os certificados do repositório de certificados manualmente.
 
### <a name="how-do-i-take-an-existing-ssh-public-key-and-inject-it-into-the-scale-set-ssh-layer-during-provisioning--i-would-like-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-utilize-them-in-my-resource-manager-template"></a>Como injetar uma chave pública SSH existente na camada SSH do conjunto de dimensionamento durante o provisionamento?  Gostaria de armazenar os valores de Chave Pública SSH no Azure Key Vault e utilizá-los no modelo do Resource Manager.

Se você estiver apenas fornecendo uma chave ssh pública às as VMs, não haverá motivo para colocar as chaves públicas no cofre de chaves, pois as chaves públicas não são secretas.
 
Você pode fornecer chaves públicas SSH em texto sem formatação ao criar uma VM do Linux.
Um exemplo pode ser encontrado aqui:

https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
Especificamente:

```json
"linuxConfiguration": {  
          "ssh": {  
            "publicKeys": [ {  
              "path": "path",
              "keyData": "publickey"
            } ]
          }
```
 
Nome do elemento linuxConfiguration | Obrigatório | Tipo | Descrição
--- | --- | --- | --- |  ---
ssh | Não | Coleção | Especifica a configuração de chave ssh para um sistema operacional Linux.
path | Sim | Cadeia de caracteres | Especifica o caminho do arquivo do Linux em que as chaves ssh ou o certificado devem ser colocados.
keyData | Sim | Cadeia de caracteres | Especifica uma chave pública ssh codificada em base64.
 
### <a name="when-i-run-update-azurermvmss-after-more-than-one-certificate-from-the-same-keyvault-i-get-the-following-error"></a>Ao executar Update-AzureRmVmss depois de mais de um certificado do mesmo KeyVault, obtenho o seguinte erro:
 
Atualização AzureRmVmss: listar segredos contém instâncias repetidas de /subscriptions/<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, o que não é permitido. Por que não posso adicionar dois certificados do mesmo KeyVault?
 
Esse comportamento poderá ocorrer se você estiver tentando adicionar o mesmo cofre duas vezes em vez de um novo vaultCertificate para o sourceVault existente. Add-AzureRmVmssSecret não funciona corretamente para adicionar segredos adicionais.
 
Se quiser adicionar mais segredos do mesmo cofre de chaves, você deverá atualizar a lista $vmss.properties.osProfile.secrets[0].vaultCertificates
 
Você pode ver a estrutura de entrada esperada aqui: https://msdn.microsoft.com/library/azure/mt589035.aspx
 
Você precisa localizar o segredo no objeto de conjunto de dimensionamento que tem o mesmo cofre de chaves. Em seguida, você deve adicionar a referência de certificado (a URL, juntamente com o nome do repositório secreto) na lista associada ao cofre.

Observação: atualmente não há suporte para remover certificados de VMs por meio de APIs de conjunto de dimensionamento.
 
Novas VMs não terão o certificado antigo, mas aquelas que tinham o certificado já implantado ainda terão o certificado antigo.
 
### <a name="is-there-a-way-to-get-certificates-pushed-to-the-scale-set-without-providing-the-password-when-the-certificate-is-in-secretstore-currently"></a>Há uma maneira de enviar certificados por push para o conjunto de dimensionamento sem fornecer a senha quando o certificado está no SecretStore atualmente?

Você não precisa codificar senhas em scripts. É possível recuperá-las dinamicamente com as permissões com as quais o script de implantação é executado. Se você tiver um script que está movendo um certificado do repositório secreto para o cofre de chaves, o comando get certificate do repositório secreto também gerará a senha do arquivo pfx.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-of-a-scale-set-work-why-do-you-need-sourcevault-when-you-have-to-specify-the-absolute-uri-to-a-certificate-with-certificateurl"></a>Como funciona a propriedade secrets de virtualMachineProfile.osProfile de um conjunto de dimensionamento? Por que você precisa de sourceVault quando tem que especificar o URI absoluto para um certificado com certificateUrl? 

Uma referência de certificado Win RM deve estar presente na propriedade secrets do perfil do sistema operacional. 

A finalidade de indicar o cofre de origem é para poder impor políticas de ACL que existem no CSM. Sem especificar o cofre de origem, os usuários que não têm permissões para implantar/acessar segredos em um cofre de chaves poderiam fazê-lo por meio de CRP. As ACLs existem até mesmo para recursos que não existem.

Se você fornecer uma id de sourceVault incorreta, mas uma URL válida do cofre de chaves, relataremos um erro quando você sondar a operação
 
### <a name="if-i-add-secrets-to-an-existing-scale-set-does-it-inject-them-in-existing-instances-or-only-new-ones"></a>Se eu adicionar segredos a um conjunto de dimensionamento existente, ele os injetará em instâncias existentes ou apenas em novas? 

Os certificados são adicionados à todas as VMs, mesmo as pré-existentes. Se a propriedade de upgradePolicy do conjunto de dimensionamento for definida como "Manual", o certificado será adicionado à VM quando você executar uma atualização manual na VM.
 
### <a name="where-do-certificates-go-for-linux-vms"></a>Para VMs do Linux, para onde vão os certificados?

Confira https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/
  
### <a name="how-do-you-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Como adicionar um novo certificado de cofre para um novo objeto de certificado?

Se quiser adicionar um certificado de cofre a um segredo existente, que deverá ser o único objeto de segredo, você poderá fazer isso como no seguinte exemplo do PowerShell:
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>O que acontece com os certificados se você recria a imagem de uma VM?

Se você recriar a imagem de uma VM, os certificados desaparecerão, pois a nova imagem exclui o disco do sistema operacional em sua totalidade. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>O que acontece se você exclui um certificado do cofre de chaves?

Se o segredo for excluído do cofre de chaves e você parar de desalocar todas as VMs e as reiniciar, encontrará uma falha. Essa falha ocorre porque o CRP precisa recuperar os segredos do Cofre de Chaves, mas não consegue. Nesse cenário, você pode excluir os certificados do modelo de conjunto de dimensionamento. 

O componente CRP não persiste segredos do cliente. Se você deixar de desalocar todas as VMs no conjunto de dimensionamento, o cache será excluído. Nesse cenário, os segredos são recuperados do cofre de chaves.

Esse problema não ocorrerá na expansão porque há uma cópia em cache do segredo no fabric (pelo menos no modelo de locatário de fabric único).
 
### <a name="why-do-we-have-to-specify-the-exact-location-for-the-certificate-url-as-referenced-here-per-httpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Por que precisamos especificar o local exato para a URL de Certificado, conforme mencionado aqui: https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/, 
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
 
Segundo a documentação de KeyVault, a API REST de get-secret deverá retornar a versão mais recente do segredo se a versão não for especificada:
 
Método | URL
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Substitua {secret-name} pelo nome e {secret-version} pela versão do segredo que você deseja recuperar. A versão do segredo pode ser excluída e, nesse caso, a versão atual é recuperada.
  
### <a name="why-does-certificate-version-have-to-be-specified-when-using-key-vault"></a>Por que a versão de certificado precisa ser especificada ao usar o cofre de chaves?

O motivo para esse requisito é para deixar claro para o usuário qual certificado é implantado em suas VMs.

Se você criar uma VM e atualizar o segredo no cofre de chaves, o novo certificado não será baixado para suas VMs. Porém, suas VMs parecerão fazer referência a ele, e novas VMs obterão o novo segredo. Para evitar essa confusão, é necessário que você faça referência a uma versão do segredo explícita.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-is-for-deployment-of-these-certs-to-a-scale-set"></a>Minha equipe trabalha com vários certificados que são distribuídos para nós como chaves públicas .cer. Qual é a abordagem recomendada para a implantação desses certificados em um conjunto de dimensionamento?

Você pode gerar um arquivo pfx que contém somente arquivos .cer, com X509ContentType = Pfx. Por exemplo, carregue o arquivo .cer como um objeto x509Certificate2 em C# ou no PowerShell e chame este método: https://msdn.microsoft.com/library/24ww6yzk(v=vs.110).aspx

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-that-most-other-resource-providers-provide"></a>Não vejo uma opção para que os usuários passem certificados como cadeias de caracteres base64 que a maioria dos outros provedores de recursos fornece.

Você pode extrair a URL de versão mais recente em um modelo do Resource Manager para emular o comportamento que descreve. Você pode incluir a seguinte propriedade JSON em seu modelo do Resource Manager:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-we-have-to-wrap-certs-in-json-objects-in-keyvaults"></a>É preciso encapsular certificados em objetos JSON em keyvaults?

Esse é um requisito de conjunto de dimensionamento/VM. Também damos suporte ao aplicativo de tipo de conteúdo/x-pkcs12. As instruções estão aqui: http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/
 
Atualmente não há suporte para arquivos .cer. Você deverá exportar os arquivos .cer em contêineres pfx.





## <a name="compliance"></a>Conformidade

### <a name="are-scale-sets-pci-compliant"></a>Conjuntos de dimensionamento estão em conformidade com PCI?

Os Conjuntos de Dimensionamento são uma camada de API fina sobre o Provedor de Recursos de Computação, que faz parte da área de "Plataforma de Computação" na Árvore de Serviço do Azure.

Portanto, de uma perspectiva de conformidade, os conjuntos de dimensionamento são uma parte fundamental da Plataforma de Computação do Azure. Assim, eles compartilham a mesma equipe, ferramentas, processos, metodologia de implantação, controles de segurança, JIT, monitoramento, alertas etc. que o CRP (Provedor de Recursos de Computação) em si.  Os conjuntos de dimensionamento estão em conformidade com PCI, pois o Provedor de Recursos de Computação faz parte do atestado PCI DSS atual:

Para obter mais informações, confira: [https://www.microsoft.com/TrustCenter/Compliance/PCI](https://www.microsoft.com/TrustCenter/Compliance/PCI).






## <a name="extensions"></a>Extensões

### <a name="how-do-you-delete-a-scale-set-extension"></a>Como excluir uma extensão de conjunto de dimensionamento?

Aqui está um exemplo de uso do PowerShell:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
extensionName pode ser encontrado em `$vmss`.
   
### <a name="is-there-a-scale-set-template-example-that-integrates-with-oms"></a>Há um exemplo de modelo de conjunto de dimensionamento que se integra ao OMS?

Observe o segundo exemplo:

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric
   
### <a name="extensions-seem-to-run-in-parallel-on-scale-sets-causing-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this-behavior"></a>As extensões parecem ser executadas em paralelo em conjuntos de dimensionamento, fazendo com que a extensão de Script Personalizado falhe. O que fazer para corrigir esse comportamento?

Confira https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/ 
 
 
### <a name="how-do-i-reset-the-password-for-scale-set-vms"></a>Como redefinir a senha para VMs de conjunto de dimensionamento?

Usar Extensões de Acesso a VM

Aqui está um exemplo de uso do PowerShell:

```powershell
$vmssName = "myvmss"
$vmssResourceGroup = "myvmssrg"
$publicConfig = @{"UserName" = "newuser"}
$privateConfig = @{"Password" = "********"}
 
$extName = "VMAccessAgent"
$publisher = "Microsoft.Compute"
$vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
$vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName VirtualMachineScaleSet $vmss
```
 
 
### <a name="how-do-i-add-an-extension-to-all-vms-in-my-scale-set"></a>Como adicionar uma extensão a todas as VMs no conjunto de dimensionamento?

- Se a política de atualização for definida como automática, reimplantar o modelo com as novas propriedades de extensão atualizará todas as VMs.
- Se a política de atualização for definida como manual, você deverá atualizar a extensão e executar um manualUpdate em todas as instâncias.
  
### <a name="if-the-extensions-associated-with-an-existing-scale-set-are-updated-would-they-affect-already-existing-vms-that-is-would-the-vms-show-up-as-not-matching-the-scale-set-model-or-would-they-be-ignored-when-an-existing-machine-is-service-healed--reimaged--etc-would-the-scripts-that-are-currently-configured-on-the-scale-set-be-executed-or-would-the-ones-that-were-configured-when-the-machine-was-first-created-be-used"></a>Se as extensões associadas a um conjunto de dimensionamento existente forem atualizadas, isso afetará as VMs já existentes? (ou seja, as VMs apareceriam como não correspondentes ao modelo do conjunto de dimensionamento)? Ou seriam ignorados? Quando uma máquina existente tem o serviço reparado/a imagem recriada/etc., os scripts que estão configurados atualmente no conjunto de dimensionamento são executados ou aqueles que foram configurados quando a máquina foi criada são usados?

- Se a definição de extensão no modelo do conjunto de dimensionamento for atualizada, isso atualizará as VMs se upgradePolicy for definido como automático, e elas serão sinalizadas como não correspondentes ao modelo se upgradePolicy for definido como manual. 

- Se uma VM existente tiver o serviço reparado, parecerá ser uma reinicialização, e as extensões não serão executadas novamente. Se uma nova imagem for criada, será como a substituição da unidade do sistema operacional pela imagem de origem, e qualquer especialização do modelo mais recente, como extensões, será executada.
 
### <a name="how-do-i-get-a-scale-set-to-join-an-ad-domain"></a>Como obter um conjunto de dimensionamento para ingressar em um Domínio do AD?

Você pode definir uma extensão como essa usando JsonADDomainExtension, por exemplo:
```json
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "joindomain",
                                "properties": {
                                    "publisher": "Microsoft.Compute",
                                    "type": "JsonADDomainExtension",
                                    "typeHandlerVersion": "1.0",
                                    "settings": {
                                        "Name": "[parameters('domainName')]",
                                        "OUPath": "[variables('ouPath')]",
                                        "User": "[variables('domainAndUsername')]",
                                        "Restart": "true",
                                        "Options": "[variables('domainJoinOptions')]"
                                    },
                                    "protectedsettings": {
                                        "Password": "[parameters('domainJoinPassword')]"
                                    }
                                }
                            }
                        ]
                    }
```
 
### <a name="my-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-instance-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Minha extensão de conjunto de dimensionamento está tentando instalar algo que requer uma reinicialização; por exemplo: "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools"

Você pode usar a extensão DSC. Se o sistema operacional for 2012 R2, o Azure obterá a configuração WMF5.0, será reinicializado e continuará com a configuração. 
 
### <a name="how-can-i-enable-antimalware-on-my-scale-set"></a>Como habilitar o Antimalware no conjunto de dimensionamento?

Aqui está um exemplo do PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# retrieve the most recent version number of the extension
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-hosted-on-a-private-storage-account-i-have-no-problems-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signaturesas-it-fails-with-the-error-missing-mandatory-parameters-for-valid-shared-access-signature-i-know-that-linksas-works-fine-from-my-local-browser"></a>Preciso executar um script personalizado hospedado em uma conta de armazenamento privado. Não tenho problemas quando o armazenamento é público, mas quando tento usar uma SAS (Assinatura de Acesso Compartilhado), ela falha com o erro: "Parâmetros obrigatórios ausentes para Assinatura de Acesso Compartilhado válida". Sei que o link +SAS funciona bem no navegador local.

Você deve definir configurações protegidas com a chave da conta de armazenamento e o nome para que este cenário funcione. Confira https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings







## <a name="networking"></a>Rede
 
### <a name="how-do-i-do-vip-swap-for-scale-sets-in-the-same-subscription-and-same-region"></a>Como fazer a permuta de VIP para conjuntos de dimensionamento na mesma assinatura e na mesma região?

Confira: https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/ 
 
  
### <a name="what-is-the-resourceguid-property-on-a-nic-for-its-not-documented-here"></a>Para que serve a propriedade resourceGuid em uma NIC? Não está documentado aqui.

É uma ID exclusiva. As camadas inferiores registrarão essa ID em algum momento no futuro. 
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-for-static-private-ip-address-allocation"></a>Como especificar um intervalo de endereços IP privados para alocação de endereço IP privado estático?

IPs são selecionados de uma sub-rede que você especifica. 

O método de alocação de IPs de conjunto de dimensionamento é sempre "Dinâmico". Isso não significa que esses IPs podem ser alterados. Isso significa apenas que você não especifica o IP na solicitação PUT. Em outras palavras, você pode especificar o conjunto estático usando a sub-rede. 
    
### <a name="how-do-i-deploy-a-scale-set-into-an-existing-vnet"></a>Como implantar um conjunto de dimensionamento em uma VNET existente? 

Confira https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet 




## <a name="scale"></a>Escala

### <a name="why-would-you-ever-create-a-scale-set-with-fewer-than-2-vms"></a>Por que criar um conjunto de dimensionamento com menos de duas VMs?

Um motivo seria usar as propriedades elásticas de um conjunto de dimensionamento. Por exemplo, você pode implantar um conjunto de dimensionamento com zero VM para definir a infraestrutura sem pagar os custos de execução da VM. Em seguida, quando estiver pronto para implantar VMs, você fará isso aumentando a "capacidade" do conjunto de dimensionamento para a contagem de instâncias de produção.

Outro motivo é quando você está fazendo algo com o conjunto de dimensionamento em que não se importa com a disponibilidade no mesmo sentido que o uso de um conjunto de disponibilidade com VMs distintas. Os conjuntos de dimensionamento adicionam uma maneira de trabalhar com unidades de computação não diferenciadas fungíveis. Essa uniformidade é um diferencial importante para conjuntos de dimensionamento versus conjuntos de disponibilidade. Muitas cargas de trabalho sem monitoração de estado não se importam com unidades individuais e podem ser reduzidas verticalmente para uma unidade de computação, se a carga de trabalho diminui, e voltar para muitas quando a carga de trabalho aumenta.

### <a name="how-do-you-change-the-number-of-vms-in-a-scale-set"></a>Como alterar o número de VMs em um conjunto de dimensionamento?

Confira: https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/

### <a name="how-can-you-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Como definir alertas personalizados para quando determinados limites forem atingidos?

Há alguma flexibilidade na maneira de lidar com alertas. Por exemplo, você pode definir webhooks personalizados como neste exemplo de um modelo do Resource Manager:
```json
   {
         "type": "Microsoft.Insights/autoscaleSettings",
           "apiVersion": "[variables('insightsApi')]",
                 "name": "autoscale",
                   "location": "[parameters('resourceLocation')]",
                     "dependsOn": [
                         "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
                 ],
                 "properties": {
                         "name": "autoscale",
                     "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                     "enabled": true,
                     "notifications": [{
                               "operation": "Scale",
                              "email": {
                                     "sendToSubscriptionAdministrator": true,
                                     "sendToSubscriptionCoAdministrators": true,
                                     "customEmails": [
                                        "youremail@address.com"
                                     ]},
                              "webhooks": [{
                                    "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                                    "properties": {
                                        "key1": "custommetric",
                                        "key2": "scalevmss"
                                    }
                                    }
                              ]}],
```

Neste exemplo, um alerta é emitido para Pagerduty quando um limite é atingido.



## <a name="troubleshooting"></a>Solucionar problemas

### <a name="how-do-i-enable-boot-diagnostics"></a>Como habilitar o diagnóstico de inicialização?

Crie uma conta de armazenamento, coloque este bloco JSON no virtualMachineProfile do conjunto de dimensionamento e atualize o conjunto de dimensionamento:
```json
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "http://yourstorageaccount.blob.core.windows.net"
        }
      }
```

Em seguida, quando uma nova VM é criada, o InstanceView da VM mostra os detalhes para a captura de tela etc. Por exemplo:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```

 

## <a name="updates"></a>Atualizações

### <a name="how-to-i-update-my-scale-set-to-a-new-image-and-manage-patching"></a>Como atualizar meu conjunto de dimensionamento como uma nova imagem e gerenciar patches?

Confira: https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set

### <a name="can-you-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Você pode usar a operação de refazer a imagem para redefinir uma VM sem alterar a imagem? (ou seja, redefinir uma VM para as configurações de fábrica em vez de uma nova imagem)?

Sim. Confira: https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set

No entanto, se o conjunto de dimensionamento fizer referência a uma imagem da plataforma com version = "latest", a VM poderá ser atualizada para uma imagem do sistema operacional posterior quando você chamar a nova imagem.







## <a name="vm-properties"></a>Propriedades de VM

### <a name="how-do-i-get-property-information-for-each-vm-without-having-to-make-multiple-calls-for-example-getting-the-fault-domain-for-each-vm-in-my-100-scale-set"></a>Como obter informações de propriedade para cada VM sem ter que fazer várias chamadas? Por exemplo: obtendo o Domínio de Falha para cada VM em meu conjunto de dimensionamento 100?

Você pode chamar ListVMInstanceViews executando um `GET` de API REST no seguinte URI de recurso:

`/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView`

### <a name="are-there-ways-to-pass-different-extension-arguments-to-different-vms-in-a-scale-set"></a>Existem maneiras de passar argumentos de extensão diferentes para VMs diferentes em um conjunto de dimensionamento?

Não, mas as extensões podem agir com base em propriedades exclusivas da VM em que estão em execução, como o nome do computador. Além disso, as extensões podem consultar metadados de instância em http://169.254.169.254 para obter mais informações.

### <a name="why-are-there-gaps-between-my-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Por que existem lacunas entre os nomes de computador de VM de conjunto de dimensionamento e as IDs de VM? Por exemplo: 0, 1, 3...

Há lacunas porque a propriedade de provisionamento excessivo do conjunto de dimensionamento é definida com o valor padrão de true. Com o excesso de provisionamento, são criadas mais VMs do que o número solicitado, e as VMs adicionais são excluídas posteriormente. O que você obtém é maior confiabilidade de implantação, às custas de regras de NAT contígua e nomeação contígua. Você pode definir essa propriedade como false e, para conjuntos de pequena escala, isso não fará muita diferença para a confiabilidade de implantação.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-scale-set-vs-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Qual é a diferença entre excluir uma VM em um Conjunto de Dimensionamento versus desalocar a VM? Quando devo escolher um ou o outro?

A principal diferença é que desalocar não exclui os VHDs. Portanto, há custos de armazenamento associados à interrupção da desalocação. Os motivos pelos quais você pode usar um deles em vez do outro incluem:

- Você deseja parar de pagar pela computação, mas manter o estado de disco das VMs.
- Você deseja iniciar um conjunto de VMs mais rapidamente do que a expansão de um conjunto de dimensionamento.
  - em relação a esse cenário, você criou seu próprio mecanismo de dimensionamento automático e deseja que o dimensionamento de ponta a ponta seja mais rápido.
  - Você tem um conjunto de dimensionamento distribuído de forma desigual entre FD/UDs (devido à exclusão seletiva de VMs ou porque as VMs estão sendo excluídas após provisionamento excessivo). Parar a desalocação, seguida da inicialização no conjunto de dimensionamento distribuirá uniformemente as VMs entre FD/UDs.








 
   

