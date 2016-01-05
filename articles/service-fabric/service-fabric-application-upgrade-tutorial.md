<properties
   pageTitle="Tutorial de atualização do aplicativo do Service Fabric | Microsoft Azure"
   description="Este artigo descreve a experiência de implantação de um Aplicativo do Service Fabric, alteração do código e distribuição de uma atualização usando o Visual Studio."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>



# Tutorial de atualização do aplicativo Service Fabric usando o Visual Studio

O Service Fabric simplifica o processo de atualização de aplicativos em nuvem, garantindo que apenas os serviços alterados sejam atualizados e que a integridade do aplicativo seja monitorada durante todo o processo de atualização. Além disso, ele reverte automaticamente o aplicativo para a versão anterior se encontrar qualquer problema. As atualizações de aplicativo do Service Fabric não apresentam *Nenhum tempo de inatividade*, pois o aplicativo pode ser atualizado sem qualquer tempo de inatividade. Este tutorial aborda como concluir uma atualização simples sem interrupção a partir do Visual Studio.


## Etapa 1: criar e implantar o exemplo do Visual Objects

Estas etapas podem ser executadas baixando o aplicativo do GitHub e adicionando os arquivos **webgl-utils.js** e **gl-matrix-min.js** ao projeto, conforme mencionado no arquivo Leiame do exemplo. Sem isso, o aplicativo não funcionará. Após adicioná-los ao projeto, crie e publique o aplicativo clicando com o botão direito do mouse no projeto do aplicativo, **VisualObjectsApplication**, e selecionando o comando Publicar no item de menu do Service Fabric da seguinte maneira.

![Menu de contexto para um aplicativo do Service Fabric][image1]

Isso abrirá outra janela pop-up e você poderá definir o **Ponto de Extremidade de Conexão** como **Cluster Local**. A janela deve parecer com a seguinte antes de clicar em **Publicar**.

![Publicar um aplicativo do Service Fabric][image2]

Agora, pressione publicar na caixa de diálogo. Você pode usar o [Gerenciador do Service Fabric para exibir o cluster e o aplicativo](service-fabric-visualizing-your-cluster.md). O aplicativo Visual Objects tem um serviço Web que pode ser acessado em seu navegador, digitando [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) na barra de endereços. Você deve ver dez objetos visuais flutuantes na tela.

## Etapa 2: atualizar o exemplo de Objetos Visuais

Você pode notar que a com a versão implantada na Etapa 1, os objetos visuais não giram. Vamos atualizar esse aplicativo para um onde os objetos visuais possam girar.

Selecione o projeto VisualObjects.ActorService dentro da solução VisualObjects e abra o arquivo StatefulVisualObjectActor.cs. Nesse arquivo, navegue até o método `MoveObject`, comente `this.State.Move()` e remova os comentários `this.State.Move(true)`. Essa alteração fará os objetos girarem após a atualização do serviço. Agora, você pode compilar a solução (não recompilar) que compilará os projetos modificados (se você selecionar Recompilar todos, terá que atualizar as versões de todos os projetos).

Também precisamos definir a versão do nosso aplicativo. Você pode usar a opção **Editar Arquivos de Manifesto** do Visual Studio depois de clicar com o botão direito na solução para fazer as alterações de versão. Isso abrirá a caixa de diálogo para edição de versões da seguinte maneira:

![Caixa de diálogo Controle de Versão][image3]

Selecione a guia **Editar Versões do Manifesto** e atualize as versões dos projetos modificados e seus pacotes de código, juntamente com o aplicativo, para a versão 2.0.0. Depois que as alterações forem feitas, o manifesto deverá parecer com o seguinte (as partes em negrito mostram as alterações):

![Atualizar versões][image4]

As ferramentas do Visual Studio podem fazer rollups automáticos das versões (selecionando **Atualizar automaticamente versões de aplicativos e de serviço**) se você usar [SemVer](http://www.semver.org); será necessário atualizar apenas o código e/ou a configuração da versão do pacote se essa opção for selecionada. Salve as alterações e marque a caixa **Atualizar o Aplicativo**.


## Etapa 3: atualizar seu aplicativo

Familiarize-se com os [parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md) e o [processo de atualização](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de atualização, o critério de tempos limite e integridade aplicados. Neste passo a passo, deixaremos o critério de avaliação de integridade do serviço definido com o valor padrão (Modo Sem Monitoramento). Você pode defini-los selecionando **Definir Configurações de Atualização** e modificando os parâmetros conforme o desejado.

Agora, estamos prontos para iniciar a atualização do aplicativo selecionando **Publicar**. Isso atualizará o aplicativo para a versão 2.0.0 no qual os objetos giram. Você perceberá que o Service Fabric atualiza um domínio de atualização por vez (alguns objetos serão atualizados primeiro e logo na sequência os outros) e o serviço estará acessível durante esse tempo, por meio de seu cliente (navegador).


Agora, à medida que a atualização do aplicativo continua, você poderá monitorá-lo usando o Gerenciador do Service Fabric (guia **Atualizações em Andamento** nos aplicativos).

Em alguns minutos, todos os domínios de atualização devem estar atualizados (concluídos), e a janela de saída do Visual Studio também deverá indicar que a atualização foi concluída. E você deverá notar que *todos* os objetos visuais na janela do navegador começarão a girar!

Convém tentar alterar as versões e mudar da versão 2.0.0 para a versão 3.0.0 como um exercício, ou até mesmo da versão 2.0.0 de volta para a versão 1.0.0. Teste as possibilidades com diretivas de integridade e os tempos limite para ficar familiarizado. Quando você estiver implantando um cluster do Azure, os parâmetros usados serão diferentes daqueles que funcionavam ao implantar um cluster local - é recomendável definir os tempos limites de forma prudente.


## Próximas etapas

[Atualizando seu aplicativo usando o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você uma atualização de aplicativo usando o PowerShell.

Controle como seu aplicativo é atualizado usando [Parâmetros de Atualização](service-fabric-application-upgrade-parameters.md).

Torne suas atualizações de aplicativo compatíveis aprendendo a usar a [Serialização de Dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando os [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativo consultando as etapas em [Solucionando problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).
 


[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png

<!---HONumber=Nov15_HO4-->