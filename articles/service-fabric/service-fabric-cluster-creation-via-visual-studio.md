<properties
   pageTitle="Configurar um cluster do Service Fabric usando o Visual Studio | Microsoft Azure"
   description="Descreve como configurar um cluster do Service Fabric usando o modelo do Gerenciador de Recursos do Azure (ARM) criado por um projeto de Grupo de Recursos do Azure no Visual Studio."
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="karolz@microsoft.com"/>

# Configurar um cluster do Service Fabric usando o Visual Studio
Este artigo descreve como configurar um cluster do [**Service Fabric**](http://azure.microsoft.com/documentation/services/service-fabric/) usando o **Visual Studio** e um modelo do [** Gerenciador de Recursos do Azure (ARM)**](https://azure.microsoft.com/documentation/articles/resource-group-overview/). Usaremos o **projeto de Grupo de Recursos do Azure** do Visual Studio para criar o modelo. Após a criação do modelo, ele pode ser implantado diretamente no Azure a partir do Visual Studio, mas também pode ser usado em um script ou como parte do recurso **integração contínua (CI)**.

## Criar um modelo de cluster do Service Fabric com um projeto de Grupo de Recursos do Azure
Para começar, abra o Visual Studio e crie um projeto de Grupo de Recursos do Azure (ele está disponível na pasta "Nuvem"):

![Caixa de diálogo Novo Projeto com o projeto do Grupo de Recursos do Azure selecionado][1]

Você pode criar uma nova solução do Visual Studio para este projeto, ou adicioná-lo a uma solução existente.

>[AZURE.NOTE]Se você não vir o projeto do Grupo de Recursos do Azure sob o nó da Nuvem, o SDK do Azure não estará instalado. Inicie o Web Platform Installer ([instale-o aqui](http://www.microsoft.com/web/downloads/platform.aspx) caso ainda não tenha feito isso), procure por "SDK do Azure para .NET" e instale a versão compatível com sua versão do Visual Studio.

Depois de pressionar o botão OK, o Visual Studio solicitará a seleção do modelo ARM que será criado:

![Selecione a caixa de diálogo do Modelo do Azure com o modelo de Cluster do Service Fabric selecionado][2]

Selecione o modelo "Cluster do Service Fabric" e pressionar o botão OK novamente. O projeto e o modelo ARM serão criados.

## Preparar o modelo para implantação
Antes da implantação do modelo para a criação do cluster, você deve fornecer valores para os parâmetros de modelo exigidos. Esses valores de parâmetro são lidos do arquivo `ServiceFabricCluster.param.dev.json`, localizado na pasta `Templates` do projeto do Grupo de Recursos. Abra o arquivo e forneça os seguintes valores:

|Nome do Parâmetro |Descrição|
|-----------------------  |--------------------------|
|clusterLocation |O nome da **região do Azure** na qual o cluster do Service Fabric estará localizado. Por exemplo, ‘Leste dos EUA’.|
|clusterName |O nome (DNS) do cluster do Service Fabric que será criado pelo modelo. <br /><br /> Por exemplo, se você definir esse parâmetro como 'myBigCluster' e o parâmetro `clusterLocation` estiver definido como Leste dos EUA, o nome do cluster será `myBigCluster.eastus.cloudapp.azure.com`.|
|certificateThumbprint |A impressão digital do certificado que protegerá o cluster.|
|sourceVaultValue |A *ID do recurso* do cofre da chave no qual o certificado que protege o cluster é armazenado.|
|certificateUrlValue |A URL do certificado de segurança do cluster.|

O modelo ARM do Service Fabric do Visual Studio cria um cluster seguro, protegido por um certificado. Esse certificado é identificado pelos últimos três parâmetros do modelo (`certificateThumbprint`, `sourceVaultValue` e `certificateUrlValue`) e deve existir em um **Cofre da Chave do Azure**. Para saber mais sobre como criar o certificado de segurança do cluster, confira o artigo [Como proteger um cluster do Service Fabric usando certificados](service-fabric-cluster-security.md).

## Opcional: adicionar portas públicas do aplicativo
Outro aspecto do modelo que convém alterar antes de implantá-lo são as **portas públicas do aplicativo** para o cluster. Por padrão, o modelo abre apenas duas portas TCP públicas (80 e 8081); se você precisar de mais portas para seus aplicativos, será necessário modificar a definição do balanceador de carga do Azure no modelo. A definição é armazenada no arquivo de modelo principal (`SecureFabricCluster.json`). Abra esse arquivo e procure por "loadBalancedAppPort". Você perceberá que cada porta está associada a três artefatos:

1. Um parâmetro de modelo que define o valor da porta TCP para a porta: ```json
	"loadBalancedAppPort1": {
	    "type": "int",
	    "defaultValue": 80
	}
	```

2. Uma *investigação* que define com que frequência, e por quanto tempo, o balanceador de carga do Azure tentará usar um nó específico do Service Fabric antes de executar o failover para outro nó. As investigações fazem parte do recurso de balanceador de carga. Esta é a definição de investigação para a primeira porta de aplicativo padrão: ```json
	{
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[parameters('loadBalancedAppPort1')]",
            "protocol": "tcp"
        }
    }
	```

3. Uma *regra de balanceamento de carga* que vincula a porta e a investigação e habilita o balanceamento de carga em um conjunto de nós de cluster do Service Fabric: ```json
	{
	    "name": "AppPortLBRule1",
	    "properties": {
	        "backendAddressPool": {
	            "id": "[variables('lbPoolID0')]"
	        },
	        "backendPort": "[parameters('loadBalancedAppPort1')]",
	        "enableFloatingIP": false,
	        "frontendIPConfiguration": {
	            "id": "[variables('lbIPConfig0')]"
	        },
	        "frontendPort": "[parameters('loadBalancedAppPort1')]",
	        "idleTimeoutInMinutes": 5,
	        "probe": {
	            "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
	        },
	        "protocol": "tcp"
	    }
	}
    ``` Se os aplicativos que você planeja implantar on cluster precisarem de mais portas, será necessário adicioná-las criando definições adicionais de regra de balanceamento de carga e de investigação. Para saber mais sobre como trabalhar com o balanceador de carga do Azure por meio de modelos ARM, confira o artigo [Introdução à configuração de um balanceador de carga interno usando o Gerenciador de Recursos do Azure](https://azure.microsoft.com/documentation/articles/load-balancer-internal-arm-powershell/).

## Implantar o modelo usando o Visual Studio
Após salvar todos os valores de parâmetro necessários no arquivo `ServiceFabricCluster.param.dev.json`, você estará pronto para implantar o modelo e criar o cluster do Service Fabric. Clique com o botão direito no projeto do Grupo de Recursos no Gerenciador de Soluções do Visual Studio e escolha “Implantar...”. O Visual Studio mostrará a caixa de diálogo "Implantar no Grupo de Recursos", solicitando a autenticação do Azure, se for necessário:

![Caixa de diálogo Implantar no Grupo de Recursos][3]

A caixa de diálogo permite que você escolha um grupo de recursos existente do Azure RM para o cluster e oferece uma opção para criar um novo. Normalmente, faz sentido usar um grupo de recursos separado para um cluster do Service Fabric.

Após pressionar o botão "Implantar", Visual Studio solicitará a confirmação dos valores de parâmetro do modelo. Clique no botão Salvar. Existe um parâmetro que não tem um valor persistente,que é a senha da conta administrativa do cluster. Forneça um valor de senha quando o Visual Studio solicitar uma.

>[AZURE.NOTE]Se o PowerShell nunca tiver sido usado para administrar o Azure a partir do computador que você está usando no momento, será necessário fazer uma pequena limpeza: 1. Habilite o script do PowerShell executando o comando [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx). Para os computadores de desenvolvimento a política "Irrestrita" é geralmente aceitável. 2. Decida se você deseja permitir a coleta de dados de diagnóstico dos comandos do Azure PowerShell e execute [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) ou [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx), conforme o necessário. Se você estiver usando o Azure PowerShell versão 0.9.8 ou mais antiga, esses comandos serão denominados `Enable-AzureDataCollection` e `Discable-AzureDataCollection`, respectivamente. Isso evitará solicitações desnecessárias durante a implantação do modelo.

Você pode monitorar o andamento do processo de implantação na janela de Saída do Visual Studio. Após a conclusão da implantação do modelo, o novo cluster estará pronto para uso!

Se houver erros, acesse o [Portal do Azure](https://portal.azure.com/) e verifique as Notificações. Uma implantação com falha do grupo de recursos deixará nesse local informações detalhadas sobre o diagnóstico.

## Próximas etapas
- [Saiba mais sobre como configurar o cluster do Service Fabric usando o portal do Azure](service-fabric-cluster-creation-via-portal.md)
- [Saiba mais sobre como gerenciar e implantar aplicativos do Service Fabric usando o Visual Studio](service-fabric-manage-applications-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=Nov15_HO4-->