
<properties 
   pageTitle="Notas de versão do SDK do Azure para .NET 2.7" 
   description="Notas de versão do SDK do Azure para .NET 2.7" 
   services="app-service/web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/20/2015"
   ms.author="juliako"/>


# Notas de versão do SDK do Azure para .NET 2.7

Este documento contém as notas de versão do SDK do Azure para .NET 2.7.

O SDK 2.7 do Azure tem suporte apenas no Visual Studio 2015 e Visual Studio 2013. [O SDK 2.6 do Azure](http://azure.microsoft.com/downloads/) é o último SDK com suporte para Visual Studio 2012.

Para obter informações detalhadas sobre esta versão, consulte [Postagem de anúncio do SDK 2.7 do Azure](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/).

##Aprimoramentos de inscrição do Visual Studio 2015

O SDK 2.7 do Azure para Visual Studio 2015 dá suporte aos novos recursos de gerenciamento de identidade do Visual Studio 2015. Isso inclui suporte para contas que acessam o Azure por meio do controle de acesso com base em função, provedores de soluções de nuvem, DreamSpark e outros tipos de conta e assinatura.

Esses aprimoramentos de inscrição só estão disponíveis no Visual Studio 2015. O suporte para Visual Studio 2013 será incluído em uma atualização futura.


##SDK móvel

Modelos de **aplicativos móveis** atualizados para refletir o [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) e o processo de instalação mais recentes.

##Barramento de Serviço 

Correções de bugs e aprimoramentos gerais. Para obter detalhes sobre atualizações e recursos, consulte as notas de versão do [NuGet do Barramento de Serviço](http://www.nuget.org/packages/WindowsAzure.ServiceBus/) mais recente.

##Ferramentas do HDInsight 

Nesta versão, as seguintes atualizações foram feitas. Essas atualizações estão no modo de visualização. Para obter mais informações, confira [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Gráficos de hive para Hive em trabalhos Tez
- Suporte total para IntelliSense do DML do Hive
- Suporte de modelo do pig
- Modelos de Storm para serviços do Azure

###Alterações de última hora

- O antigo projeto do **Storm** deve ser atualizado ao usar esta versão das ferramentas. Para obter mais informações, confira [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Não há mais suporte para o Visual Studio Web Express. Para obter mais informações, confira [este blog](http://go.microsoft.com/fwlink/?LinkId=619108).

##Ferramentas do Serviço de Aplicativo do Azure

Nesta versão, as seguintes atualizações foram feitas às extensões de ferramentas da Web. Para obter mais informações, confira [este blog](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/).

- Suporte para contas do DreamSpark adicionado
- Total de alterações das ferramentas do Azure feitas para oferecer suporte às novas APIs de gerenciamento de recursos do Azure
- Adicionado suporte para o Serviço de Aplicativo do Azure para [Gerenciador de Nuvem](azure-sdk-dot-net-release-notes-2_7.md#cloud_explorer)

###Problemas conhecidos

Nós de slot de implantação de aplicativo Web não aparecem sob o nó de Slots no Gerenciador de Servidores, e nós filho de slots de implantação de aplicativo Web não carregaram no Gerenciador de nuvem. Esse problema foi resolvido e preparado para a próxima versão do SDK.


##<a id="cloud_explorer"></a>Gerenciador de nuvem para Visual Studio 2015

O SDK 2.7 do Azure inclui o Gerenciador de Nuvem para Visual Studio 2015, que permite exibir os recursos do Azure, inspecionar suas propriedades e executar ações chave do desenvolvedor de dentro do Visual Studio.

O Gerenciador de Nuvem dá suporte a:

- Exibições de grupo de recursos e tipo de recurso de seus recursos do Azure 
- Pesquisar recursos por nome (disponível no modo de exibição do tipo de recurso)
- Suporte para assinaturas e recursos que têm o controle de acesso com base em função (RBAC) aplicado 
- Painel de ação integrada que mostra ações voltadas para desenvolvedores específicas para os recursos selecionados. Por exemplo: anexe o depurador remoto para máquinas virtuais criadas usando a pilha do Gerenciador de Recursos, exiba dados de diagnóstico para máquinas virtuais, etc.
- Painel de propriedades integradas que mostra as propriedades voltadas para desenvolvedores comumente necessárias durante o desenvolvimento e teste 
- A alternância rápida da conta a ser usada ao enumerar recursos (use o comando Configurações na barra de ferramentas) 
- Filtragem de assinaturas a serem usadas ao enumerar recursos (use o comando Configurações na barra de ferramentas) 
- Links profundos para o portal de visualização do Azure para gerenciamento de recursos e grupos de recursos 
 
 
##Ferramentas do gerenciador de recursos do Azure 

As ferramentas do Gerenciador de Recursos do Azure foram atualizadas para funcionar com controle de acesso com base em função (RBAC) e os novos tipos de assinatura. A capacidade de usar novas contas de armazenamento, além do armazenamento clássico, para armazenar os artefatos durante a implantação está incluída nessas alterações.

Se você estiver usando um projeto do grupo de recursos do Azure de uma versão anterior do SDK com o SDK 2.7, um novo script de implantação é necessário para implantar usando uma nova conta de armazenamento em vez do armazenamento clássico. Você será avisado antes que as alterações sejam feitas ao seu projeto para adicionar o novo script. O script antigo será renomeado e você precisará fazer modificações ao novo script manualmente.
 
 
##Ferramentas do Gerenciador de Armazenamento 

- Suporte à exibição de Blobs acrescentados. Mais informações disponíveis [nesta postagem do blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
- Suporte à exibição de contas de armazenamento Premium por meio do Gerenciador de Servidores. O Gerenciador de Servidores só exibe os blobs de página para contas de armazenamento premium, uma vez que eles são o único tipo com suporte para contas de armazenamento premium.

##Ferramentas da Azure Data Factory para o Visual Studio 

Introdução às **Ferramentas da Azure Data Factory** para o Visual Studio. Abaixo estão os recursos habilitados. Para obter mais informações, confira [este blog](http://go.microsoft.com/fwlink/?LinkId=617530).

- **Criação com base em modelo**: Selecione modelos com base em caso de uso, modelos de movimentação de dados ou modelos de processamento de dados para implantar uma solução de integração de dados de ponta a ponta e começar rapidamente a utilizar Data Factory. 
- **Integração com o Gerenciador de Soluções para criar e implantar as entidades de Data Factory**: crie e implante pipelines e entidades relacionadas como projetos do Visual Studio. 
- **Integração com o modo de exibição de diagrama de interação visual durante a criação**: crie visualmente pipelines e conjuntos de dados com o auxílio da exibição de diagrama. 
- **Integração com o Gerenciador de Servidores para navegação e interação com entidades já implantadas**: use o Gerenciador de Servidores para procurar Data factories já implantados e entidades correspondentes. Importe um Data Factory implantado ou qualquer entidade (Pipeline, serviço vinculado, conjuntos de dados) no seu projeto. 
- **Edição de JSON com validação de esquema e IntelliSense avançado**: configure de forma eficiente e edite documentos JSON de entidades de Data Factory com IntelliSense avançado e validação de esquema 
- **Publicação em vários ambientes**: publique pipelines criados em ambientes de desenvolvimento, teste ou produção criando arquivos de configuração separados para cada ambiente.
- **Suporte ao processamento de dados com base em Pig, Hive e .Net**: suporte para scripts Pig e Hive no projeto de Data Factory. Suporte para referenciar o Projeto C# para gerenciar atividade do .Net.

##Consulte também

[Postagem de anúncio do SDK 2.7 do Azure](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

<!---HONumber=July15_HO4-->