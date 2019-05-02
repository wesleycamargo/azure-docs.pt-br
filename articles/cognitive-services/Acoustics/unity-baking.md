---
title: Tutorial de bake do Projeto Acústico do Unity
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve a preparação de acústica com o Projeto Acústico no Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 2f0fcdcdf781c86179b67eeef0223d46da0fc65b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916996"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Tutorial de bake do Projeto Acústico do Unity
Este tutorial descreve a preparação de acústica com o Projeto Acústico no Unity.

Requisitos de software:
* [Unity 2018.2+](https://unity3d.com) para Windows
* [Plugin do Projeto Acústico integrado em seu projeto do Unity](unity-integration.md) ou o [conteúdo de exemplo do Projeto Acústico do Unity](unity-quickstart.md)
* Opcional: Uma [conta do Lote do Azure](create-azure-account.md) para acelerar os bakes usando a computação em nuvem

## <a name="open-the-project-acoustics-bake-window"></a>Abra a janela do bake do Projeto Acústico
Escolha **Window > Acústica** no menu Unity:

![Captura de tela do editor do Unity com a opção de menu da janela Acústico realçada](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Criar uma malha de navegação
O Projeto Acústico usa uma malha de navegação para colocar pontos de investigação do ouvinte para simulação. Você pode usar o [fluxo de trabalho da malha de navegação](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) do Unity, ou usar outro pacote de modelagem 3D para criar sua própria malha. 

## <a name="mark-acoustic-scene-objects"></a>Marque os objetos da cena acústica
O Projeto Acústico se baseia em dois tipos de objetos de cena para simulação: os objetos que refletem e ocluem o som na simulação e a malha de navegação do player que restringe os pontos de investigação do ouvinte na simulação. Ambos os tipos de objeto são marcados usando a guia **Objetos**. 

Uma vez simplesmente marcar os objetos adiciona os componentes **AcousticsGeometry** ou **AcousticsNavigation** ao objeto, você também pode usar o [fluxo de trabalho do componente padrão do Unity](https://docs.unity3d.com/Manual/UsingComponents.html)para marcar ou desmarcar os objetos. Apenas Renderizadores e Terrenos Mesh podem ser marcados. Todos os outros tipos de objeto serão ignorados. As caixas de seleção marcarão ou desmarcarão todos os objetos afetados.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Marcar oclusão acústica e geometria de reflexão
Abra a guia **Objetos** da janela **Acústica**. Marque todos os objetos como **Geometria Acústica**, se eles devem ocluir, refletir ou absorver som. Geometria acústica pode incluir coisas como zero, paredes, preench, janelas e vidro da janela, tapetes e móveis grandes. Você pode usar qualquer nível arbitrário de complexidade para esses objetos. Como a cena é voxelizada antes da simulação, malhas altamente detalhadas, como árvores com muitas folhas pequenas, não são mais onerosas para bake do que objetos simplificados.

Não inclua elementos que não devem afetar a acústica, como malhas de colisão invisíveis.

A transformação de um objeto no momento do cálculo do probe (por meio da guia **Probes**, abaixo) é fixada nos resultados do cozimento. Mover qualquer um dos objetos marcados na cena exigirá refazer o cálculo da sonda e reciclar a cena.

### <a name="mark-the-navigation-mesh"></a>Marcar a malha de navegação
Malhas de navegação criadas com o fluxo de trabalho do Unity serão captadas pelo sistema acústica. Para usar suas próprias malhas, marque-as na guia **Objetos**.

### <a name="for-reference-the-objects-tab-parts"></a>Para referência: As partes da guia Objetos
As partes da página da guia são:

1. Botões de seleção da guia (**objetos** guia selecionada). Use esses botões para percorrer as várias etapas de execução de acústica, da esquerda para a direita.
2. Uma breve descrição do que você precisa fazer usando esta página.
3. Filtros disponíveis para a janela de hierarquia. Use isso para filtrar a janela de hierarquia para objetos do tipo especificado, para que você possa marcá-los com mais facilidade. Se você ainda não marcou nada para acústica, selecionar as duas últimas opções não mostrará nada. No entanto, eles podem ser úteis para encontrar objetos marcados depois de ter feito isso.
4. Quando nenhum objeto é selecionado, esta seção mostra o status de todos os objetos na cena:
    * Total - O número total de objetos ativos e não ocultos na cena.
    * Ignorada - O número de objetos que não são Renderizadores de Malha ou Terrenos.
    * Mesh - O número de objetos do Mesh Renderer na cena
    * Terrain - O número de objetos Terrain na cena
    * Geometry - O número de objetos Mesh ou Terrain na cena marcada como "Geometria Acústica"
    * Navegação - O número de objetos Mesh ou Terrain na cena marcada como "Navegação acústica". Esse número não inclui NavMesh do Unity.
5. Mostra o número total de objetos 'marcáveis' na cena, que são apenas Mesh Renderers e Terrains. Mostra caixas de seleção que você pode usar para marcar (adicionar o componente apropriado a) esses objetos como geometria ou navegação para acústica
6. Quando nada é selecionado, esta nota lembra-lhe para selecionar objetos para marcação, se necessário. Você também pode marcar uma ou ambas as caixas de seleção para marcar todos os objetos na cena sem selecionar nada.
7. Quando objetos são selecionados, esta seção mostra o status apenas dos objetos selecionados.
8. Mostra o número total de objetos selecionados 'marcáveis'. Marcar ou desmarcar as caixas de seleção marcará ou desmarcará apenas os objetos selecionados.

Se você não tiver selecionado nada em sua cena, a guia Objetos será semelhante à seguinte imagem:

![Captura de tela da guia Objetos Acústicos sem nenhuma seleção](media/objects-tab-no-selection-detail.png)

Se você tiver algo selecionado em sua cena ou janela de hierarquia, será semelhante à seguinte imagem:

![Captura de tela da guia Objetos Acústicos com seleção mostrada](media/objects-tab-selection-detail.png)

Se alguns objetos são marcados e outros não, a caixa de seleção apropriada mostrará um valor "misto":

![Captura de tela da guia Objetos Acústicos com um ícone de seleção mista realçado](media/mixed-object-selection-detail.png)

Clicar na caixa de seleção forçará todos os objetos a serem marcados, e clicar novamente desmarcará todos os objetos.

Objetos podem ser marcados para geometria e navegação.

## <a name="select-acoustic-materials"></a>Selecionar materiais acústicos
Quando seus objetos estiverem marcados, clique no botão **Materiais** e atribua os materiais acústicos. O sistema dos materiais do Projeto Acústico é vinculado ao sistema de materiais visuais do Unity: para dois objetos terem materiais acústicos separados, devem ter materiais visuais separados.

Os materiais acústicos controlam a quantidade de energia sonora refletida de volta de cada superfície. O material acústico padrão tem absorção semelhante ao concreto. O Projeto Acústico sugere materiais com base no nome do material visual. Você pode atribuir o material acústico “Personalizado” para um material para habilitar um controle deslizante de coeficiente de absorção.

O tempo de reverberação de um determinado material em uma sala é inversamente relacionado ao seu coeficiente de absorção, com a maioria dos materiais tendo valores de absorção na faixa de 0,01 a 0,20. Materiais com coeficientes de absorção fora desta faixa são muito absorventes.

![Grafo mostrando a correlação negativa do tempo de reverberação com coeficiente de absorção](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Para referência: Partes do guia de materiais
![Captura de tela da guia Materiais Acústicos no Unity](media/materials-tab-detail.png)

1. O botão da guia **Materiais**, usado para abrir esta página.
2. Uma breve descrição do que você precisa fazer usando esta página.
3. Quando marcado, somente os materiais usados pelos objetos marcados como **Geometria Acústica** serão listados. Caso contrário, todos os materiais usados na cena serão listados.
4. Use essas opções para alterar a ordem do menu suspenso exibido ao clicar em uma lista suspensa na coluna Acústica abaixo (nº 6). **Nome** classifica os materiais acústicos pelo nome. "Absorptivity" classifica em ordem de absorptivity de baixa a alta.
5. A lista de materiais usados na cena, classificados em ordem alfabética. Se a caixa de seleção **Show Marked Only** estiver marcada (#3), somente os materiais usados pelos objetos marcados como **Geometria Acústica** serão mostrados. Clicar em um material aqui selecionará todos os objetos na cena que usam esse material.
6. Mostra o material acústico ao qual o material da cena foi atribuído. Clique em um menu suspenso para reatribuir um material de cena a um material acústico diferente. Você pode alterar a ordem de classificação do menu mostrado ao clicar em um item aqui usando as opções **Classificar acústica por:** acima (# 4).
7. Mostra o coeficiente de absorção acústica do material selecionado na coluna anterior. Um valor de zero significa perfeitamente reflexivo (sem absorção), enquanto um valor de 1 significa perfeitamente absorvente (sem reflexão). O coeficiente de absorção não pode ser alterado a menos que o material selecionado seja "Personalizado".
8. Para um material atribuído a "Personalizado", o controle deslizante não está mais desativado e você pode escolher o coeficiente de absorção usando o controle deslizante ou digitando um valor.

## <a name="calculate-and-review-listener-probe-locations"></a>Calcular e revisar os locais de investigação do ouvinte
Depois de atribuir os materiais, alterne para a guia **Investigações** e clique em **Calcular** para colocar pontos de investigação do ouvinte para simulação.

### <a name="what-the-calculate-button-calculates"></a>Calcula o que o botão "Calcular..."
O botão **Calcular...**  usa sua geometria da cena acústica selecionada para preparar sua cena de simulação:

1. Ele pega a geometria das malhas da cena e calcula um volume de voxel. O volume do voxel é um volume tridimensional que envolve toda a sua cena e é composto de pequenos "voxels" cúbicos. O tamanho dos voxels é determinado pela frequência de simulação, que é definida pela configuração **Simulation Resolution**. Cada voxel é marcado como "aberto" ou contendo geometria de cena. Se um voxel contém geometria, o voxel é marcado com o coeficiente de absorção do material atribuído a essa geometria.
2. Ele usa a malha de navegação para colocar os pontos de investigação do ouvinte. O algoritmo equilibra as questões concorrentes de cobertura espacial e tempo de simulação e tamanho do arquivo, garantindo que restrinjam os corredores e espaços pequenos sempre que houver alguma quantidade de cobertura. As contagens do ponto de investigação típico variam de 100 para cenas pequenas a alguns milhares de cenas grandes.

Dependendo do tamanho da sua cena e da velocidade da sua máquina, esses cálculos podem levar vários minutos.

### <a name="review-voxel-and-probe-placement"></a>Voxel de revisão e posicionamento da investigação
Visualize os dados voxel e os locais de ponto de investigação para garantir que você está pronto para implementar sua cena. Uma malha de navegação incompleta ou ausente ou geometria acústica extra geralmente estará rapidamente visível na versão prévia. O posicionamento do Voxel e da investigação pode ser habilitado ou desabilitado usando o menu Gizmos:

![Captura de tela do menu Gizmos no Unity](media/gizmos-menu.png)

Voxels contendo geometria acústica são mostrados como cubos verdes. Explore sua cena e verifique se tudo o que deveria ser geometria tem voxels. A câmera cena deve estar a cerca de 5 metros do objeto para os voxels mostrarem.

Se você comparar os voxels criados com resolução grossa vs resolução fina, verá que os voxels grossos serão duas vezes maiores.

![Captura de tela da versão prévia de voxels grossos no editor do Unity](media/voxel-cubes-preview.png)

Os resultados da simulação são interpolados entre os locais de ponto de investigação do ouvinte em tempo de execução. Verifique se existem pontos de investigação perto de qualquer lugar onde o player deve viajar na cena.

![Captura de tela da versão prévia de investigação no editor do Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Tenha cuidado com renomeações de cena
O nome da cena é usado para conectar a cena a arquivos que armazenam o posicionamento do ponto de teste e a voxelização. Se a cena for renomeada depois que os pontos da sonda forem calculados, os dados de atribuição e posicionamento do material serão perdidos e deverão ser executados novamente.

### <a name="for-reference-parts-of-the-probes-tab"></a>Para referência: Partes do guia de testes
![Captura de tela da guia Investigação Acústica no Unity](media/probes-tab-detail.png)

1. O **investigações** botão guia usada para abrir essa página
2. Uma breve descrição do que você precisa fazer usando essa página
3. Usá-los para escolher uma resolução de simulação fino ou grosso. Grande é mais rápido, mas tem determinadas vantagens e desvantagens. Veja [Fazer o Bake da Resolução](bake-resolution.md) abaixo para obter detalhes.
4. Escolha o local onde os arquivos de dados acústicos devem ser colocados usando este campo. Clique no botão "..." para usar um seletor de pastas. O padrão é **Assets / AcousticsData**. Uma subpasta **Editor** também será criada nesse local. Para mais informações sobre arquivos de dados, consulte [Arquivos de Dados](#Data-Files) abaixo.
5. Os arquivos de dados para esta cena serão nomeados usando o prefixo fornecido aqui. O padrão é "Acústica_ [Nome da cena]".
6. Após as probes terem sido calculadas, os controles acima serão desativados. Clique no botão **Limpar** para apagar os cálculos e habilitar os controles para que você possa recalcular usando as novas configurações.
7. Clique no botão **Calcular...** para voxelizar a cena e calcular os locais dos pontos da sonda. Isso é feito localmente em sua máquina e deve ser feito antes de fazer um cozimento.

Nesta versão do Project Acoustics, os probes não podem ser colocados manualmente e devem ser colocados através do processo automatizado fornecido na guia **Probes**.

Veja [Fazer o Bake da Resolução](bake-resolution.md) para obter mais detalhes sobre resolução bruta vs. fina.

## <a name="bake-your-scene-using-azure-batch"></a>Fazer sua cena usando o Lote do Azure
É possível fazer o bake da cena com um cluster de cálculo na nuvem usando o serviço do Lote do Azure. O plugin do Unity do Projeto Acústico conecta-se diretamente ao Lote do Azure para instanciar, gerenciar e desativar um cluster do Lote do Azure para cada bake. Na guia **Bake**, insira as credenciais do Azure, selecione um tipo e tamanho de computador de cluster e clique em **Bake**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Para referência: Partes da guia Bake
![Captura de tela da guia Bake da Acústica no Unity](media/bake-tab-details.png)

1. O botão de incorporar essa guia usado para abrir essa página.
2. Uma breve descrição do que fazer nesta página.
3. Campos para inserir suas credenciais do Azure assim que sua conta do Azure for criada. Para obter mais informações, consulte [criar uma conta do lote do Azure](create-azure-account.md).
4. Marca de imagem do docker para o conjunto de ferramentas acústicas.
5. Inicie o portal do Azure para gerenciar suas assinaturas, monitorar o uso e exibir informações de cobrança etc. 
6. Tipo de nó de computação em lote do Azure a ser usado para o cálculo. O tipo de nó deve ser suportado pelo seu local do datacenter do Azure. Se não souber, deixe **Standard_F8s_v2**.
7. Número de nós a serem usados para este cálculo. O número digitado aqui afeta o tempo para concluir o cozimento e é limitado pela alocação principal do Lote do Azure. A alocação padrão permite apenas dois nós principais de 8 ou um nó de 16 núcleos, mas pode ser expandida. Para obter mais informações sobre restrições de alocação de core, consulte [criar uma conta do lote do Azure](create-azure-account.md).
8. Marque essa caixa de seleção para configurar seu pool de computação para usar [nós de baixa prioridade](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Os nós de computação de baixa prioridade têm um custo muito inferior, mas eles podem não estar sempre disponíveis ou podem admitir preempção a qualquer momento.
9. A contagem de sondas para sua cena, conforme calculada na guia **Sondas**. O número de probes determina o número de simulações que precisam ser executadas na nuvem. Você não pode especificar mais nós do que probes.
10. A quantidade de tempo decorrido esperada para que seu trabalho seja executado na nuvem. Isso não inclui o tempo de inicialização do nó. Uma vez que o trabalho comece a funcionar, isso é quanto tempo deve ser antes de você retornar os resultados. Observe que isso é apenas uma estimativa.
11. A quantidade total de tempo de computação necessária para executar as simulações. Essa é a quantidade total de tempo de computação do nó que será usado no Azure. Ver [estimativa tortas custo](#Estimating-bake-cost) abaixo para obter mais informações sobre como usar esse valor.
12. Esta mensagem informa onde os resultados do cozimento serão salvos assim que o trabalho for concluído.
13. (Somente para uso avançado) Se por algum motivo você precisar forçar o Unity a esquecer um assado que você enviou (por exemplo, você baixou os resultados usando outra máquina), clique no botão **Limpar Estado** para esquecer o trabalho que estava submetido. Note que isso significa que o arquivo de resultado, quando pronto, **não** será baixado, e **isso não é o mesmo que cancelar o trabalho**. O trabalho, se em execução, continuará a ser executado na nuvem.
14. Clique no botão **Bake** para enviar o bake para a nuvem. Enquanto um trabalho está em execução, isso mostra **Cancelar trabalho**.
15. Prepara para a simulação acústica de processamento[ em seu computador](#Local-bake).
16. Essa área mostra o status do tortas. Quando concluído, ele deve mostrar **Downloaded**.

Você sempre pode obter informações completas sobre os trabalhos ativos, pools de computação e armazenamento no [portal do Azure](https://portal.azure.com).

Enquanto um trabalho está em execução, o botão **Bake** muda para **Cancelar trabalho**. Use este botão para cancelar o trabalho em andamento.Use esse botão para cancelar o trabalho em andamento. Você será solicitado a confirmar antes do trabalho é cancelado. O cancelamento de um trabalho não pode ser desfeito, nenhum resultado estará disponível e você ainda será cobrado por qualquer tempo de computação do Azure usado.

Depois de iniciar um assar, você pode fechar o Unity. Dependendo do projeto, do tipo de nó e do número de nós, um cozimento na nuvem pode levar várias horas. O status do trabalho de cozimento será atualizado quando você recarregar o projeto e abrir a janela Acústica. Se o trabalho foi concluído, o arquivo de saída será baixado.

As credenciais do Azure são armazenadas com segurança em sua máquina local e associadas ao seu editor do Unity. Eles são usados exclusivamente para estabelecer uma conexão segura com o Azure.

### <a name="Estimating-bake-cost"></a> Estimar o custo do bake do Azure

Para estimar o custo de um dado bake, pegue o valor mostrado para **Estimated Compute Cost**, que é uma duração, e multiplique-o pelo custo por hora em sua moeda local do **VM Node Type** você selecionou. O resultado não incluirá o tempo de nó necessárias para colocar os nós e em execução. Por exemplo, se você selecionar **Standard_F8s_v2** para seu tipo de nó, que tem um custo de US$ 0,40/h, e o custo estimado de computação for de 3 horas e 57 minutos, o custo estimado para executar o trabalho será de US$ 0,40 * ~ 4 horas = ~ US$ 1,60. O custo real provavelmente será um pouco maior devido ao tempo extra para iniciar os nós. Você pode encontrar o custo de nó por hora na página [Preço do lote do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (selecione "Computar otimizado" ou "Computação de alto desempenho" para a categoria).

## <a name="Local-bake"></a> Prepare sua cena no seu PC
Você pode preparar sua cena em seu próprio computador. Isso pode ser útil para testar com a acústica com cenas pequenas antes de criar uma conta do Lote do Microsoft Azure. Observe que a simulação acústica pode levar muito tempo, dependendo do tamanho da cena.

### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware
* Um processador x86-64 com pelo menos 8 núcleos e 32 GB de RAM

Por exemplo, em nosso teste em uma máquina de 8 núcleos com Intel Xeon E5-1660 @ 3 GHz e 32 GB RAM -
* Uma cena pequena com 100 investigações leva cerca de 2 horas em um bake grosso e cerca de 32 horas em um bake fino.
* Uma cena média com 1000 investigações leva cerca de 20 horas em um bake grosso e cerca de 21 dias em um bake fino.

### <a name="setup-docker"></a>Configurar Docker
Instalar e configurar o Docker no PC que processará a simulação -
1. Instalar o [conjunto de ferramentas do Docker](https://www.docker.com/products/docker-desktop).
2. Inicie as configurações do Docker, navegue até as opções "Avançadas" e configure os recursos para terem pelo menos 8 GB de RAM. Quanto mais CPUs você puder alocar para o Docker, mais rápido será o bake. ![Captura de tela do exemplo de configurações do Docker](media/docker-settings.png)
3. Navegue até "Unidades Compartilhadas" e ative o compartilhamento para a unidade usada no processamento.![Captura de tela de opções do drive compartilhado do Docker](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Executar um bake local
1. Clique no botão "Preparar Bake Local" na guia **Bake** e selecione uma pasta onde os arquivos de entrada e scripts de execução serão salvos. É possível executar o bake em qualquer máquina, desde que ela atenda aos requisitos mínimos de hardware e tenha o Docker instalado, copiando a pasta para essa máquina.
2. Inicie a simulação usando o script “runlocalbake.bat”. Esse script buscará a imagem do Docker do Project Acoustics com o conjunto de ferramentas necessário para o processamento da simulação e dará início a ela. 
3. Quando a simulação terminar, copie o arquivo .ace resultante de volta para seu projeto Unity. Para garantir que o Unity reconheça o arquivo como binário, acrescente “.bytes” à extensão do arquivo (por exemplo, “Scene1.ace.bytes”). Os logs detalhados da simulação são armazenados em “AcousticsLog.txt”. Se você tiver algum problema, compartilhe esse arquivo para ajudar com o diagnóstico.

## <a name="Data-Files"></a> Arquivos de dados adicionados pelo processo de bake

Há quatro arquivos de dados criados durante o processo de bake. Um contém os resultados da simulação e é fornecido com o seu título. Os outros armazenam dados relacionados ao Editor do Unity.

Resultado da simulação:
* **Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes**: Essa é a tabela de pesquisa de tempo de execução e contém os resultados de simulação e os elementos da cena acústica voxelizada. O local e o nome desse arquivo podem ser alterados usando os campos na **investigações** guia.

Tome cuidado para não excluir o arquivo de resultado da simulação. Isso não é recuperável, exceto por re-gravar a cena.

Arquivos de dados do editor:
* **Assets/Editor/[SceneName]\_AcousticsParameters.asset**: Esse arquivo armazena os dados inseridos nos campos da interface do usuário do Acoustics. O local e o nome deste arquivo não podem ser alterados.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: Esse arquivo armazena a geometria de acústica voxelizada e as propriedades de material que são calculadas usando o botão **Calcular...**  na guia Investigações. A localização e o nome desse arquivo podem ser alterados usando os campos da guia **Sondas**.
* **Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml**: Esse arquivo armazena os parâmetros de simulação calculados usando o botão **Calcular...** na Guia **Investigações**. A localização e o nome desse arquivo podem ser alterados usando os campos da guia **Sondas**.

## <a name="set-up-the-acoustics-lookup-table"></a>Configurar a tabela de pesquisa acústica
Arraste e solte o **pré-fabricado do Projeto Acústico** do painel do projeto em sua cena:

![Captura de tela do pré-fabricado Acústica no Unity](media/acoustics-prefab.png)

Clique no **ProjectAcoustics** Game Object e vá até o painel de inspetores. Especifique a localização do seu resultado de cozimento (o arquivo .ACE, em **Assets / AcousticsData**) arrastando-o e soltando-o no script do Acoustics Manager, ou clicando no botão de círculo ao lado da caixa de texto.

![Captura de tela do pré-fabricado Gerenciador Acústico no Unity](media/acoustics-manager.png)  

## <a name="next-steps"></a>Próximas etapas
* Explore os [controles de design do Unity](unity-workflow.md)
* Explore os [conceitos de design do Projeto Acústico](design-process.md)

