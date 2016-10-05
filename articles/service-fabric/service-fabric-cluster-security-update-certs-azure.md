<properties
   pageTitle="Adicionar, substituir e remover certificados usados em um cluster do Service Fabric no Azure | Microsoft Azure"
   description="Descreve como carregar um certificado de cluster secundário e substituir o antigo certificado primário."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="chackdan"/>

# Adicionar ou remover certificados para um cluster do Service Fabric no Azure

É recomendável que você se familiarize com o modo como o Service Fabric usa certificados X.509; leia [Cenários de segurança do cluster do Service Fabric](service-fabric-cluster-security.md). Você deve entender o que é um certificado de cluster e qual sua finalidade, antes de continuar.

O Service Fabric permite especificar dois certificados de cluster, um primário e um secundário, quando você configura a segurança do certificado durante a criação do cluster. Veja [Criar um cluster do Service Fabric no Portal do Azure](service-fabric-cluster-creation-via-portal.md) ou [Criando um cluster do Azure por meio do Azure Resource Manager](service-fabric-cluster-creation-via-Resource Manager.md) para obter detalhes. Se você estiver realizando a implantação por meio do Resource Manager e especificar apenas um certificado de cluster, ele será usado como o certificado primário. Após a criação do cluster, é possível adicionar um novo certificado como um secundário.

>[AZURE.NOTE] Para um cluster seguro, você sempre precisará de, pelo menos, um certificado (primário ou secundário) válido (não revogado ou expirado) implantado; caso contrário, o cluster vai parar de funcionar. Em 90 dias antes que todos os certificados válidos alcancem a expiração, o sistema gera um rastreamento de avisos e também um evento de integridade de aviso no nó. Atualmente, não há nenhum email nem qualquer outra notificação enviada pelo Service Fabric sobre esse tópico.


## Adicionar um certificado secundário usando o portal
Para adicionar outro certificado como um secundário, você deve carregar o certificado em um Cofre de chaves do Azure e implantá-lo nas máquinas virtuais no cluster. Para obter mais informações, veja [Deploy certificates to VMs from a customer-managed key vault](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) (Implantar certificados em VMs por meio de um cofre de chaves gerenciado pelo cliente).

1. Veja [Carregar o certificado X.509 no cofre de chaves](service-fabric-secure-azure-cluster-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault) e saiba como fazer isso.

2. Entre no [portal do Azure](https://portal.azure.com/) e navegue até o recurso de cluster ao qual você deseja adicionar esse certificado.
3. Em **CONFIGURAÇÕES**, clique em **Segurança** para abrir a Folha Segurança do Cluster.
4. Clique no botão **“+ Certificado”** na parte superior da folha para ir para a folha **“Adicionar Certificado”**.
5. Selecione “Impressão digital do certificado secundário” no menu suspenso e preencha a impressão digital do certificado secundário que você carregou no cofre de chaves.

>[AZURE.NOTE]
Ao contrário do que ocorre durante o fluxo de trabalho de criação do cluster, não usamos os detalhes sobre as informações do cofre de chaves aqui, porque, presume-se que, quando você estiver nessa folha, o certificado das VMs já terá sido implantado e o certificado já estará disponível no repositório de certificados local na instância do VMSS.

Clique em **Certificado**. Uma implantação será iniciada, e uma barra de Status azul aparecerá na Folha Segurança do Cluster.

![Captura de tela das impressões digitais do certificado no portal][SecurityConfigurations_02]

Após a conclusão bem-sucedida da implantação, você poderá usar o certificado primário ou secundário para realizar operações de gerenciamento no cluster.

![Screen shot of certificate deployment in progress][SecurityConfigurations_03]

Esta é uma imagem da aparência da folha de segurança após a conclusão da implantação.

![Screen shot of certificate thumbprints after deployment][SecurityConfigurations_08]


Agora você pode usar o novo certificado que acabou de adicionar para se conectar e realizar operações no cluster.

>[AZURE.NOTE]
Atualmente, não há nenhuma maneira de alternar os certificados primário e secundário no portal, porém, esse recurso está em andamento. Desde que haja um certificado válido do cluster, o cluster funcionará corretamente.

## Adicione um certificado secundário e alterne-o para ser o primário usando o PowerShell do Resource Manager

Essas etapas presumem que você esteja familiarizado com o funcionamento do Resource Manager e tenha implantado, pelo menos, um cluster do Service Fabric usando um modelo do Resource Manager e que o modelo usado para configurar o cluster para uso. Presume-se também que você esteja familiarizado com o uso do JSON.

>[AZURE.NOTE]
Se você estiver procurando um modelo de exemplo e parâmetros que você pode usar para acompanhar ou como um ponto de partida, baixe-o neste [git-repo]. (https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

#### Editar o modelo do Resource Manager 

Se você estiver usando a amostra do [git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) para acompanhar, você encontrará essas alterações na amostra 5-VM-1-NodeTypes-Secure\_Step2.JSON. Usar o 5-VM-1-NodeTypes-Secure\_Step1.JSON para implantar um cluster seguro

1. Abra o modelo do Resource Manager que foi usado para implantar o cluster.
2. Adicione um novo parâmetro “secCertificateThumbprint” do tipo “string”. Caso você esteja usando o modelo do Resource Manager que você baixou no portal durante o momento de criação ou nos modelos de início rápido, basta procurar esse parâmetro, que você já deve encontrar definido.
3. Localize a definição de Recurso “Microsoft.ServiceFabric/clusters”. Em propriedades, você encontrará a marcação do JSON “Certificate”, que deve ser parecida com o trecho do JSON a seguir.
```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
        }
``` 

4. Adicione uma nova marcação “thumbprintSecondary” e atribua a ela um valor de “[parameters('secCertificateThumbprint')]”.

Agora a definição de recurso deve ser parecida com esta (dependendo da fonte do modelo, ela poderá não ser exatamente como o trecho abaixo). Como você pode ver abaixo, o que você está fazendo aqui é especificar um novo certificado como primário e mover o primário atual como secundário. Isso resulta na substituição do certificado atual pelo novo certificado em uma etapa de implantação.

```JSON

      "properties": {
        "certificate": {
            "thumbprint": "[parameters('certificateThumbprint')]",
            "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },

```

#### Edite o arquivo de modelo para refletir os novos parâmetros adicionados acima

Se você estiver usando a amostra do [git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) para acompanhar, poderá começar a fazer alterações na amostra 5-VM-1-NodeTypes-Secure.paramters\_Step2.JSON


Edite o arquivo de parâmetro do modelo do Resource Manager, adicione os novos parâmetros ao secCertificate e alterne os detalhes do certificado primário existente pelo secundário e substitua os detalhes do certificado primário pelos detalhes do novo certificado.

```JSON
    "secCertificateThumbprint": {
      "value": "OLD Primary Certificate Thumbprint"
    },
   "secSourceVaultValue": {
      "value": "OLD Primary Certificate Key Vault location"
    },
    "secCertificateUrlValue": {
      "value": "OLD Primary Certificate location in the key vault"
     },
    "certificateThumbprint": {
      "value": "New Certificate Thumbprint"
    },
    "sourceVaultValue": {
      "value": "New Certificate Key Vault location"
    },
    "certificateUrlValue": {
      "value": "New Certificate location in the key vault"
     },

```

### Implantar o modelo no Azure

1. Agora você está pronto para implantar o modelo no Azure. Abra um prompt de comando do Azure PS versão 1+.
2. Faça logon em sua Conta do Azure e selecione a assinatura do Azure específica. Essa é uma etapa importante para pessoas que têm acesso a mais de uma assinatura do Azure.


```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Teste o modelo antes de implantá-lo. Use o mesmo Grupo de Recursos no qual o cluster está atualmente implantado.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Implante o modelo no grupo de recursos. Use o mesmo Grupo de Recursos no qual o cluster está atualmente implantado. Execute o comando New-AzureRmResourceGroupDeployment. Você não precisa especificar o modo, já que o valor padrão é **incremental**.

>[AZURE.NOTE]
Se você definir o Modo como Concluído, será possível excluir inadvertidamente os recursos que não estão no modelo. Portanto, não o use neste cenário.
   

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Veja um exemplo preenchido do mesmo comando powershell.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

Após a conclusão da implantação, conecte-se ao cluster usando o novo Certificado e realize algumas consultas. Faça isso se conseguir. Em seguida, você pode excluir o antigo certificado primário.

Se estiver usando um certificado autoassinado, não se esqueça de importá-lo para seu repositório de certificados local TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Para referência rápida, este é o comando para se conectar a um cluster seguro
```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Para referência rápida, este é o comando para obter a integridade do cluster
```powershell
Get-ServiceFabricClusterHealth 
```
 
## Remover o antigo certificado usando o portal
Este é o processo para remover um certificado antigo para que o cluster não o use:

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até as configurações de segurança do cluster.
2. Clique com o botão direito do mouse no certificado que deseja remover
3. Selecione Excluir e siga os avisos.

[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png


## Próximas etapas
Leia estes artigos para obter mais informações sobre gerenciamento de cluster:

- [Processo de atualização de Cluster de Malha do Serviço e as suas expectativas](service-fabric-cluster-upgrade.md)
- [Configurar o acesso baseado em funções para clientes](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png
[SecurityConfigurations_03]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_03.png
[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png
[SecurityConfigurations_08]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_08.png

<!---HONumber=AcomDC_0817_2016-->

