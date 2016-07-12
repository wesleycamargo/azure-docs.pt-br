<properties
   	pageTitle="Publicar aplicativos HDInsight | Microsoft Azure"
   	description="Aprenda a criar e publicar aplicativos do HDInsight."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="06/29/2016"
   	ms.author="jgao"/>

# Publicar aplicativos do HDInsight no Azure Marketplace

Um aplicativo do HDInsight é um aplicativo que os usuários podem instalar em um cluster HDInsight baseado em Linux. Esses aplicativos podem ser desenvolvidos pela Microsoft, por ISVs (fornecedores independentes de software) ou por conta própria. Neste artigo, você aprenderá a publicar um aplicativo do HDInsight no Azure Marketplace. Para obter informações gerais sobre a publicação no Azure Marketplace, confira [Publicar uma oferta no Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Os aplicativos do HDInsight usam o modelo *BYOL (traga sua própria licença)*, em que o provedor do aplicativo é responsável por licenciar o aplicativo aos usuários finais e estes são cobrados pelo Azure apenas pelos recursos que criarem, como o cluster HDInsight e seus nós/VMs. Atualmente, a cobrança do aplicativo propriamente dito não é feita pelo Azure.

Outro artigo relacionado ao aplicativo do HDInsight:

- [Instalar aplicativos do HDInsight](hdinsight-apps-install-applications.md): saiba como instalar um aplicativo do HDInsight em seus clusters.
- [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como instalar e testar aplicativos personalizados do HDInsight.

 
## Pré-requisitos

Para enviar seu aplicativo personalizado ao marketplace, você deverá ter criado e testado seu aplicativo personalizado. Confira os seguintes artigos:

- [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como instalar e testar aplicativos personalizados do HDInsight.

Você também deve ter registrado sua conta de desenvolvedor. Confira [publicar uma oferta no Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) e [Criar uma conta do Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## Definir o aplicativo

Há duas etapas envolvidas na publicação de aplicativos no Azure Marketplace. Primeiro, defina um arquivo **createUiDef.json** para indicar com quais clusters seu aplicativo é compatível. Em seguida, publique o modelo no portal do Azure. A seguir temos um exemplo de arquivo createUiDef.json.

	{
		"handler": "Microsoft.HDInsight",
		"version": "0.0.1-preview",
		"clusterFilters": {
			"types": ["Hadoop", "HBase", "Storm", "Spark"],
			"tiers": ["Standard", "Premium"],
			"versions": ["3.4"]
		}
	}


|Campo | Descrição | Valores possíveis|
|-------|---------------|----------------|
|tipos |Os tipos de cluster com os quais o aplicativo é compatível. |Hadoop, HBase, Storm, Spark (ou qualquer combinação destes)|
|camadas |As camadas de cluster com as quais o aplicativo é compatível. |Standard, Premium (ou ambos)|
|versões|	Os tipos de cluster HDInsight com os quais o aplicativo é compatível. |3\.4|

## Aplicativo de pacote

Crie um arquivo zip que contém todos os arquivos necessários para instalar os aplicativos do HDInsight. Você precisará do arquivo zip no [aplicativo Publicar](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Confira um exemplo em [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

	>[AZURE.IMPORTANT] O nome dos nomes de script de instalação de aplicativo deve ser exclusivo para determinado cluster com o formato a seguir. Além disso, quaisquer ações de script de instalação ou de desinstalação devem ser idempotentes, o que significa que os scripts podem ser chamados repetidamente, produzindo o mesmo resultado.
	
	>	name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
		
	>Observe que há três partes no nome do script:
		
	>	1. A script name prefix, which shall include either the application name or a name relevant to the application.
	>	2. A "-" for readability.
	>	3. A unique string function with the application name as the parameter.

	>	An example is the above ends up becoming: hue-install-v0-4wkahss55hlas in the persisted script action list. For a sample JSON payload, see [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- Todos os scripts obrigatórios.

> [AZURE.NOTE] Os arquivos do aplicativo (incluindo arquivos de aplicativo da web, se houver algum) podem estar localizados em qualquer ponto de extremidade publicamente acessível.

## Publicar o aplicativo

Siga as etapas a seguir para publicar um aplicativo do HDInsight:

1. Entre no [Portal de Publicação do Azure](https://publish.windowsazure.com/).
2. Clique em **Modelos de solução** para criar um novo modelo de solução.
3. Clique em **Criar conta do Centro de Desenvolvimento e ingressar no programa do Azure** para registrar sua empresa, se ainda não tiver feito isso. Confira [Criar uma conta de Desenvolvedor da Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Clique em **Definir algumas topologias para começar**. Um modelo de solução é um "pai" para todas as suas topologias. Você pode definir várias topologias em uma oferta/modelo de solução. Quando uma oferta passa para teste, todas as suas topologias a acompanham.
5. Adicionar uma nova versão.
6. Carregue o arquivo zip preparado no [Aplicativo Empacotar](#package-application).
7. Clique em **Solicitar Certificação**. A equipe de certificação da Microsoft revisará os arquivos e certificará a topologia.

## Próximas etapas

- [Instalar aplicativos do HDInsight](hdinsight-apps-install-applications.md): saiba como instalar um aplicativo do HDInsight em seus clusters.
- [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md): saiba como implantar um aplicativo do HDInsight não publicado no HDInsight.
- [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
- [Personalizar clusters Hadoop baseados em Linux no HDInsight usando modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.

<!---HONumber=AcomDC_0706_2016-->