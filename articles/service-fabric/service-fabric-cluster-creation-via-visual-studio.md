<properties
   pageTitle="Configurar um cluster do Service Fabric usando o Visual Studio | Microsoft Azure"
   description="Descreve como configurar um cluster do Service Fabric usando o modelo do ARM (Gerenciador de Recursos do Azure) criado por um projeto de Grupo de Recursos do Azure no Visual Studio."
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
   ms.date="04/04/2016"
   ms.author="karolz@microsoft.com"/>

# Configurar um cluster do Service Fabric usando o Visual Studio
Este artigo descreve como configurar um cluster do Service Fabric do Azure usando o Visual Studio e um modelo do ARM (Gerenciador de Recursos do Azure). Usaremos o projeto de grupo de recursos do Azure do Visual Studio para criar o modelo. Após a criação do modelo, ele pode ser implantado diretamente no Azure por meio do Visual Studio, mas também pode ser usado em um script ou como parte do recurso de CI (integração contínua).

## Criar um modelo de cluster do Service Fabric usando um projeto de grupo de recursos do Azure
Para começar, abra o Visual Studio e crie um projeto de grupo de recursos do Azure (ele está disponível na pasta **Nuvem**):

![Caixa de diálogo Novo Projeto com o projeto do Grupo de Recursos do Azure selecionado][1]

Você pode criar uma nova solução do Visual Studio para este projeto ou adicioná-lo a uma solução existente.

>[AZURE.NOTE] Se você não vir o projeto do grupo de recursos do Azure sob o nó da Nuvem, o SDK do Azure não estará instalado. Inicie o Web Platform Installer ([instale-o agora](http://www.microsoft.com/web/downloads/platform.aspx) caso ainda não tenha feito isso), pesquise "SDK do Azure para .NET" e instale a versão compatível com sua versão do Visual Studio.

Depois que pressionar o botão OK, Visual Studio solicitará que você selecione o modelo do Gerenciador de Recursos que deseja criar:

![Selecione a caixa de diálogo do Modelo do Azure com o modelo de Cluster do Service Fabric selecionado][2]

Selecione o modelo Cluster do Service Fabric e pressione o botão OK novamente. O projeto e o modelo do Gerenciador de Recursos foram criados agora.

## Preparar o modelo para implantação
Antes da implantação do modelo para a criação do cluster, você deve fornecer valores para os parâmetros necessários do modelo. Esses valores de parâmetro são lidos no arquivo `ServiceFabricCluster.parameters.json`, que está na pasta `Templates` do projeto de grupo de recursos. Abra o arquivo e forneça os seguintes valores:

|Nome do parâmetro |Descrição|
|-----------------------  |--------------------------|
|certificateThumbprint |A impressão digital do certificado que protegerá o cluster.|
|sourceVaultResourceId |A *ID do recurso* do cofre da chave no qual o certificado que protege o cluster é armazenado.|
|certificateUrlValue |A URL do certificado de segurança do cluster.|

O modelo do Gerenciador de Recursos do Service Fabric do Visual Studio cria um cluster seguro que é protegido por um certificado. Esse certificado é identificado pelos últimos três parâmetros do modelo (`certificateThumbprint`, `sourceVaultValue` e `certificateUrlValue`) e deve existir em um **Cofre da Chave do Azure**. Para saber mais sobre como criar o certificado de segurança do cluster, confira o artigo [Como proteger um cluster do Service Fabric usando certificados](service-fabric-cluster-security.md#secure-a-service-fabric-cluster-by-using-certificates).

## Opcional: alterar o nome do cluster
Cada cluster do Service Fabric tem um nome. Quando um cluster do Fabric é criado no Azure, o nome do cluster determina (com a região do Azure) o nome DNS (Sistema de Nomes de Domínio) para o cluster. Por exemplo, se você nomear o cluster `myBigCluster`, e o parâmetro `clusterLocation` for definido para Leste dos EUA, o nome DNS do cluster será `myBigCluster.eastus.cloudapp.azure.com`.

Por padrão, o nome do cluster é gerado automaticamente e se torna exclusivo pelo acréscimo de um sufixo aleatório a um prefixo "cluster". Isso simplifica bastante o uso do modelo como parte de um sistema CI (**integração contínua**). Se quiser usar um nome específico para o cluster, um que seja significativo para você, defina o valor da variável `clusterName` no arquivo do Gerenciador de Recursos (`ServiceFabricCluster.json`) para seu nome escolhido. É a primeira variável definida nesse arquivo.

## Opcional: adicionar portas públicas do aplicativo
Talvez você queira alterar as portas públicas de aplicativos do cluster antes de implantá-lo. Por padrão, o modelo abre apenas duas portas TCP públicas (80 e 8081). Se você precisar de mais para seus aplicativos, modifique a definição de Balanceador de Carga do Azure no modelo. A definição é armazenada no arquivo de modelo principal (`ServiceFabricCluster.json`). Abra esse arquivo e procure `loadBalancedAppPort`. Você perceberá que cada porta está associada a três artefatos:

1. Uma variável de modelo que define o valor da porta TCP para a porta:

	```json
	"loadBalancedAppPort1": "80"
	```

2. Uma *investigação* que define com que frequência, e por quanto tempo, o balanceador de carga do Azure tentará usar um nó específico do Service Fabric antes de executar o failover para outro nó. As investigações fazem parte do recurso de Balanceador de Carga. Esta é a definição de investigação para a primeira porta de aplicativo padrão:

	```json
	{
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
	```

3. Um *regra de balanceamento de carga* que vincula a porta e a investigação, que permite balanceamento de carga em um conjunto de nós de cluster do Service Fabric:

    ```json
	{
	    "name": "AppPortLBRule1",
	    "properties": {
	        "backendAddressPool": {
	            "id": "[variables('lbPoolID0')]"
	        },
	        "backendPort": "[variables('loadBalancedAppPort1')]",
	        "enableFloatingIP": false,
	        "frontendIPConfiguration": {
	            "id": "[variables('lbIPConfig0')]"
	        },
	        "frontendPort": "[variables('loadBalancedAppPort1')]",
	        "idleTimeoutInMinutes": 5,
	        "probe": {
	            "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
	        },
	        "protocol": "Tcp"
	    }
	}
    ```
Se os aplicativos que planeja implantar no cluster precisarem de mais portas, você poderá adicioná-las criando mais definições de investigação e de regra de balanceamento de carga. Para saber mais sobre como trabalhar com o Balanceador de Carga do Azure por meio de modelos do Gerenciador de Recursos, confira [Introdução à criação de um balanceador de carga interno usando um modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## Implantar o modelo usando o Visual Studio
Após salvar todos os valores de parâmetro necessários no arquivo `ServiceFabricCluster.param.dev.json`, você estará pronto para implantar o modelo e criar o cluster do Service Fabric. Clique com o botão direito do mouse no projeto do grupo de recursos no Gerenciador de Soluções do Visual Studio e escolha **Implantar**. O Visual Studio mostrará a caixa de diálogo **Implantar no Grupo de Recursos**, solicitando a autenticação do Azure, se for necessário:

![Caixa de diálogo Implantar no Grupo de Recursos][3]

A caixa de diálogo permite que você escolha um grupo de recursos existente do Gerenciador de Recursos para o cluster e oferece uma opção para criar um novo. Normalmente, faz sentido usar um grupo de recursos separado para um cluster do Service Fabric.

Após pressionar o botão Implantar, o Visual Studio solicitará a confirmação dos valores de parâmetro do modelo. Clique no botão **Salvar**. Um parâmetro não tem um valor persistente: a senha da conta administrativa para o cluster. Você precisará fornecer um valor de senha quando o Visual Studio solicitar um.

>[AZURE.NOTE] Se o PowerShell nunca tiver sido usado para administrar o Azure no computador que você está usando no momento, será necessário fazer uma pequena limpeza.
>1. Habilite o script do PowerShell executando o comando [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx). Para os computadores de desenvolvimento a política "irrestrita" é geralmente aceitável.
>2. Decida se você deseja permitir a coleta de dados de diagnóstico dos comandos do Azure PowerShell e execute [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) ou [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx), conforme a necessidade. Isso evitará solicitações desnecessárias durante a implantação do modelo.

Você pode monitorar o andamento do processo de implantação na janela de saída do Visual Studio. Após a conclusão da implantação do modelo, o novo cluster estará pronto para uso!

Se houver algum erro, acesse o [Portal do Azure](https://portal.azure.com/) e abra o grupo de recursos no qual você o implantou. Clique em **Todas as configurações** e em **Implantações** na folha de configurações. Uma implantação com falha do grupo de recursos deixará informações detalhadas sobre o diagnóstico nesse local.

>[AZURE.NOTE] Os clusters de Service Fabric exigem um determinado número de nós que devem estar ativos em todos os momentos para manter a disponibilidade e preservar o estado - conhecido como "manter o quórum". Consequentemente, em geral não é seguro desligar todos os computadores no cluster, a menos que você tenha executado primeiro um [backup completo do estado](service-fabric-reliable-services-backup-restore.md).

## Próximas etapas
- [Saiba mais sobre como configurar o cluster do Service Fabric usando o portal do Azure](service-fabric-cluster-creation-via-portal.md)
- [Saiba mais sobre como gerenciar e implantar aplicativos do Service Fabric usando o Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=AcomDC_0511_2016-->