---
title: Fazer o bake da acústica com Project Acoustics
titlesuffix: Azure Cognitive Services
description: Este documento descreve o processo de envio de um teste de acústica usando a extensão do editor Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: ce377234cceb22bc2c6979bedd92d800355e75da
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872077"
---
# <a name="bake-acoustics"></a>Inserir acústica

Este documento descreve o processo de envio de um teste de acústica usando a extensão do editor Unity. Para mais informações sobre acústica, consulte [O que é Acústica](what-is-acoustics.md). Para obter um guia de início rápido, consulte [Introdução ao](getting-started.md).

## <a name="import-the-plugin"></a>O plug-in de importação

Importe o pacote do plugin Acoustics para o seu projeto. Em seguida, abra a UI Acoustics escolhendo **Window> Acoustics** no menu Unity:

![Janela aberta acústica](media/WindowAcoustics.png)

## <a name="principles"></a>Princípios

A janela da ferramenta Acústica reúne as informações que o mecanismo de acústica precisa para calcular a acústica de sua cena. Há cinco etapas para fazer um assar:

1. Crie ou marque sua malha de navegação do player
2. Marque geometria acústica
3. Atribuir propriedades de materiais acústicos à geometria
4. Visualizar posicionamento do probe
5. Tortas

Depois que o cozimento estiver concluído, consulte [Visão Geral do Processo de Projeto para Acústica](design-process.md) para obter etapas de design pós-cozimento opcionais.

## <a name="create-or-mark-a-navigation-mesh"></a>Criar ou marcar uma malha de navegação

Uma malha de navegação é usada para colocar pontos de teste para simulação. Você pode usar o [fluxo de trabalho de malha de navegação](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) da Unity ou pode especificar sua própria malha de navegação. Malhas de navegação criadas com o fluxo de trabalho do Unity serão captadas pelo sistema acústica. Para usar suas próprias malhas, marque-as na guia **Objetos**, conforme descrito na próxima etapa.

## <a name="objects-tab"></a>Guia de objetos

Abra a guia **Objetos** da janela **Acústica**. Use esta guia para marcar objetos em sua cena, que simplesmente adiciona os componentes **AcousticsGeometry** ou **AcousticsNavigation** ao objeto. Você também pode usar o [fluxo de trabalho padrão do componente Unity](https://docs.unity3d.com/Manual/UsingComponents.html) para marcar ou desmarcar objetos.

Selecione um ou mais objetos na janela ou hierarquia da cena e, em seguida, marque ou desmarque-os para **AcousticsGeometry** ou **AcousticsNavigation** conforme descrito abaixo. Se nada for selecionado, você poderá marcar ou desmarcar tudo na cena de uma só vez.

Apenas Renderizadores e Terrenos Mesh podem ser marcados. Todos os outros tipos de objeto serão ignorados. As caixas de seleção marcarão ou desmarcarão todos os objetos afetados.

Se você não tiver selecionado nada em sua cena, será semelhante à seguinte imagem:

![Objetos de guia nenhuma seleção](media/ObjectsTabNoSelectionDetail.png)

Se você tiver algo selecionado em sua cena ou janela de hierarquia, será semelhante à seguinte imagem:

![Objetos de guia nenhuma seleção](media/ObjectsTabWithSelectionDetail.png)

### <a name="objects-tab-parts"></a>Partes do guia de objetos

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

Se alguns objetos são marcados e outros não, a caixa de seleção apropriada mostrará um valor "misto":

![Caixa de seleção valor misto](media/MixedObjectSelectionDetail.png)

Clicar na caixa de seleção forçará todos os objetos a serem marcados, e clicar novamente desmarcará todos os objetos.

Objetos podem ser marcados para geometria e navegação.

### <a name="guidelines-for-marking-objects"></a>Diretrizes para marcar objetos

Certifique-se de marcar quaisquer objetos como **Geometria Acústica** se eles devem ocluir, refletir ou absorver som. Geometria acústica pode incluir coisas como zero, paredes, preench, windows e Vidro da janela, baixo dos tapetes e móveis grandes. Não há problema em incluir objetos menores, como lâmpadas, objetos de decoração, luminárias, pois eles não aumentam sensivelmente o custo do forno. É importante não perder elementos importantes como o chão ou o teto. Além disso, não inclua coisas que não devem afetar a acústica, como malhas de colisão.

A transformação de um objeto no momento do cálculo do probe (por meio da guia **Probes**, abaixo) é fixada nos resultados do cozimento. Mover qualquer um dos objetos marcados na cena exigirá refazer o cálculo da sonda e reciclar a cena.

## <a name="materials-tab"></a>Guia de materiais

Quando seus objetos estiverem marcados, clique no botão **Materiais** para ir para a guia Materiais.

### <a name="parts-of-the-materials-tab"></a>Partes do guia de materiais

![Detalhe da guia de materiais](media/MaterialsTabDetail.png)

1. O botão da guia **Materiais**, usado para abrir esta página.
2. Uma breve descrição do que você precisa fazer usando esta página.
3. Quando marcado, somente os materiais usados pelos objetos marcados como **Geometria Acústica** serão listados. Caso contrário, todos os materiais usados na cena serão listados.
4. Use essas opções para alterar a ordem do menu suspenso exibido ao clicar em uma lista suspensa na coluna Acústica abaixo (nº 6). **Nome** classifica os materiais acústicos pelo nome. "Absorptivity" classifica em ordem de absorptivity de baixa a alta.
5. A lista de materiais usados na cena, classificados em ordem alfabética. Se a caixa de seleção **Show Marked Only** estiver marcada (#3), somente os materiais usados pelos objetos marcados como **Geometria Acústica** serão mostrados. Clicar em um material aqui selecionará todos os objetos na cena que usam esse material.
6. Mostra o material acústico ao qual o material da cena foi atribuído. Clique em um menu suspenso para reatribuir um material de cena a um material acústico diferente. Você pode alterar a ordem de classificação do menu mostrado ao clicar em um item aqui usando as opções **Classificar acústica por:** acima (# 4).
7. Mostra o coeficiente de absorção acústica do material selecionado na coluna anterior. Um valor de zero significa perfeitamente reflexivo (sem absorção), enquanto um valor de 1 significa perfeitamente absorvente (sem reflexão). O coeficiente de absorção não pode ser alterado a menos que o material selecionado seja "Personalizado".
8. Para um material atribuído a "Personalizado", o controle deslizante não está mais desativado e você pode escolher o coeficiente de absorção usando o controle deslizante ou digitando um valor. Para obter mais informações sobre as propriedades do material, consulte [Visão geral do processo de design para acústica](design-process.md).

### <a name="guidelines-for-assigning-materials-or-absorption-values"></a>Diretrizes para atribuição de materiais (ou valores de absorção)

Esta guia tenta adivinhar o que a absorção de seus materiais é baseada no nome. Por exemplo, se o nome do seu material de cena for LivingRoom_WoodTable, o material acústico inicial atribuído a ele será "wood". Materiais onde um material conhecido não pode ser determinado são atribuídos ao material "Padrão", que tem absorção semelhante ao concreto.

Você pode reatribuir materiais acústicos a cada material de cena. Por exemplo, se uma sala parece muito reverberante, mude o material acústico das paredes, do piso ou do teto para algo de maior capacidade de absorção. A atribuição de material acústico se aplica a todos os objetos que usam esse material de cena.

## <a name="probes-tab"></a>Guia de testes

Depois de atribuir os materiais, alterne para o **investigações** guia.

### <a name="parts-of-the-probes-tab"></a>Partes do guia de testes

![Detalhe da guia de testes](media/ProbesTabDetail.png)

1. O **investigações** botão guia usada para abrir essa página
2. Uma breve descrição do que você precisa fazer usando essa página
3. Usá-los para escolher uma resolução de simulação fino ou grosso. Grande é mais rápido, mas tem determinadas vantagens e desvantagens. Ver ["grosseiro vs Fine resolução"](#Coarse-vs-Fine-Resolution) abaixo para obter detalhes.
4. Escolha o local onde os arquivos de dados acústicos devem ser colocados usando este campo. Clique no botão "..." para usar um seletor de pastas. O padrão é **Assets / AcousticsData**. Uma subpasta **Editor** também será criada nesse local. Para mais informações sobre arquivos de dados, veja ["Arquivos de Dados"](#Data-Files) abaixo.
5. Os arquivos de dados para esta cena serão nomeados usando o prefixo fornecido aqui. O padrão é "Acústica_ [Nome da cena]".
6. Após as probes terem sido calculadas, os controles acima serão desativados. Clique no botão **Limpar** para apagar os cálculos e habilitar os controles para que você possa recalcular usando as novas configurações.
7. Clique no botão **Calcular...** para voxelizar a cena e calcular os locais dos pontos da sonda. Isso é feito localmente em sua máquina e deve ser feito antes de fazer um cozimento.

Nesta versão do Project Acoustics, os probes não podem ser colocados manualmente e devem ser colocados através do processo automatizado fornecido na guia **Probes**.

### <a name="what-the-calculate-button-calculates"></a>Calcula o que o botão "Calcular..."

O botão **Calcular...** pega todos os dados que você forneceu até agora (geometria, navegação, materiais e configuração Grosseiro / Fino) e passa por várias etapas:

1. Ele pega a geometria das malhas da cena e calcula um volume de voxel. O volume do voxel é um volume tridimensional que envolve toda a sua cena e é composto de pequenos "voxels" cúbicos. O tamanho dos voxels é determinado pela frequência de simulação, que é definida pela configuração **Simulation Resolution**. Cada voxel é marcado como "aberto" ou contendo geometria de cena. Se um voxel contém geometria, o voxel é marcado com o coeficiente de absorção do material atribuído a essa geometria.
2. Em seguida, ele usa os dados de navegação para calcular os locais acusticamente interessantes em que o jogador pode ir. Ele tenta encontrar um conjunto razoavelmente pequeno desses locais, que inclui áreas menores, como portas e corredores, e depois para os quartos, para espaços abertos. Para cenas pequenas, normalmente, são menos de 100 locais, enquanto cenas grandes podem ter até mil locais.
3. Para cada um dos locais finais dos ouvintes que calcula, ele determina um número de parâmetros como "aberto" é o espaço, o tamanho da sala em que está, etc.
4. Os resultados desses cálculos são armazenados em arquivos no local especificado (consulte ["Arquivos de dados"](#Data-Files) abaixo)

Dependendo do tamanho da sua cena e da velocidade da sua máquina, esses cálculos podem levar vários minutos.

Depois que esses cálculos forem concluídos, você poderá visualizar os dados de voxels e os locais dos pontos de sondas para ajudar a garantir que o assar lhe proporcione bons resultados. Coisas como uma malha de navegação ruim ou geometria extra / ausente normalmente serão rapidamente visíveis na visualização para que você possa corrigi-la.

### <a name="scene-rename"></a>Renomeação de cena

O nome da cena é usado para conectar a cena a arquivos que armazenam o posicionamento do ponto de teste e a voxelização. Se a cena for renomeada depois que os pontos da sonda forem calculados, os dados de atribuição e posicionamento do material serão perdidos e deverão ser executados novamente.

## <a name="debug-display-through-gizmos"></a>Depurar exibição por meio de utensílios

Por padrão, tanto a **investigações** e **Voxels** utensílios estão ativados. Eles mostrarão os voxels e os locais dos pontos de sondagem que foram calculados. Eles podem ser ativados ou desativados usando o menu Gizmos:

![Menu gizmos](media/GizmosMenu.png)

### <a name="voxels"></a>Voxels

Os voxels são mostrados na janela da cena como cubos verdes em torno da geometria participante. Voxels que contêm apenas ar não são mostrados. Há uma grande caixa verde em torno de sua cena inteira que indica o volume completo de voxels que será usado na simulação.
Movimente-se pela sua cena e verifique se tudo o que deveria ser geometria tem voxels. A câmera cena deve estar a cerca de 5 metros do objeto para os voxels mostrarem.

Se você comparar os voxels criados com resolução grosseira vs resolução Fina, você verá que os voxels grosseiros são duas vezes maiores.

![Visualização de Voxel](media/VoxelCubesPreview.png)

### <a name="probe-points"></a>Pontos de teste

Os pontos de teste são sinônimos de possíveis locais do player (ouvinte). Ao fazer um assar, a acústica de uma fonte de som em qualquer lugar da cena é calculada para cada um desses pontos de teste. Se o jogador não estiver diretamente em um local de ponto de teste, os dados dos pontos de teste mais próximos ao jogador serão usados para interpolar os parâmetros naquele local.

Portanto, é importante garantir que os pontos da sonda existam em qualquer lugar em que o jogador possa viajar na cena, e que as pequenas áreas e portas sejam adequadamente representadas. Os pontos de teste são colocados pelo mecanismo de Acústica do Projeto com base na interpretação da geometria da sua cena e não podem ser movidos ou editados nesta Visualização do Designer. Use-os para verificar a exatidão de sua marcação geométrica e dos dados da dica de navegação.

![Visualização de investigações](media/ProbesPreview.png)

### <a name="Coarse-vs-Fine-Resolution"></a>Resolução aproximada do vs

A única diferença entre as configurações de resolução Grosso e Fino é a frequência com que a simulação é executada. Fine usa uma frequência de duas vezes alto grande.
Embora isso possa parecer simples, tem várias implicações na simulação acústica:

* O comprimento de onda do Coarse é duas vezes mais longo que o Fine e, portanto, os voxels são duas vezes maiores.
* O tempo de simulação está diretamente relacionado ao tamanho do voxel, fazendo um Coarse bake cerca de 16 vezes mais rápido que um Fine bake.
* Portais (por exemplo, portas ou janelas) menores que o tamanho do voxel não podem ser simulados. O ajuste Grosso pode fazer com que alguns desses portais menores não sejam simulados; portanto, eles não passarão o som durante a execução. Você pode ver se isso está acontecendo, visualizando os voxels.
* A menor frequência de simulação resulta em menos difração ao redor dos cantos e bordas.
* Fontes de som não podem ser localizadas dentro de voxels "preenchidos", ou seja, voxels que contêm geometria - isso resulta em nenhum som. É mais difícil localizar fontes de som para que elas não estejam dentro dos voxels maiores do Coarse do que usando a configuração Fine.
* Os voxels maiores se intrometem mais nos portais, como mostrado abaixo. A primeira imagem foi criada usando o Coarse, enquanto a segunda é a mesma porta usando a resolução Fine. Como indicado pelas marcações vermelhas, há muito menos intrusão na entrada usando a configuração Fine. A linha azul é a porta conforme definida pela geometria, enquanto a linha vermelha é o portal acústico efetivo definido pelo tamanho do voxel. Como essa intrusão ocorre em uma determinada situação depende completamente de como os voxels se alinham com a geometria do portal, que é determinada pelo tamanho e localização de seus objetos na cena.

![Porta de entrada grosso](media/CoarseVoxelDoorway.png)

![Porta de entrada bem](media/FineVoxelDoorway.png)

## <a name="bake-tab"></a>Guia de tortas

Quando estiver satisfeito com os dados de visualização, use a guia **Bake** para enviar seu bolo para a nuvem.

### <a name="parts-of-the-bake-tab"></a>Partes da guia tortas

![Detalhe da guia tortas](media/BakeTabDetails.png)

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
14. Clique no botão Assar para enviar o assar para a nuvem. Enquanto um trabalho está em execução, isso mostra **Cancelar trabalho**.
15. Prepara para o processamento de simulação acústica em máquinas locais. Confira [Bake local](#Local-bake) para saber mais.  
16. Essa área mostra o status do tortas. Quando concluído, ele deve mostrar **Downloaded**.

Você sempre pode obter informações completas sobre os trabalhos ativos, pools de computação e armazenamento na [Portal do Azure](https://portal.azure.com).

Enquanto um trabalho está em execução, o botão **Bake** muda para **Cancelar trabalho**. Use este botão para cancelar o trabalho em andamento.Use esse botão para cancelar o trabalho em andamento. Você será solicitado a confirmar antes do trabalho é cancelado. O cancelamento de um trabalho não pode ser desfeito, nenhum resultado estará disponível e você ainda será cobrado por qualquer tempo de computação do Azure usado.

Depois de iniciar um assar, você pode fechar o Unity. Dependendo do projeto, do tipo de nó e do número de nós, um cozimento na nuvem pode levar várias horas. O status do trabalho de cozimento será atualizado quando você recarregar o projeto e abrir a janela Acústica. Se o trabalho foi concluído, o arquivo de saída será baixado.

As credenciais do Azure são armazenadas com segurança em sua máquina local e associadas ao seu editor do Unity. Eles são usados exclusivamente para estabelecer uma conexão segura com o Azure.

### <a name="Estimating-bake-cost"></a> Estimar o custo do bake do Azure

Para estimar o custo de um dado bake, pegue o valor mostrado para **Estimated Compute Cost**, que é uma duração, e multiplique-o pelo custo por hora em sua moeda local do **VM Node Type** você selecionou. O resultado não incluirá o tempo de nó necessárias para colocar os nós e em execução. Por exemplo, se você selecionar **Standard_F8s_v2** para seu tipo de nó, que tem um custo de US$ 0,40/h, e o custo estimado de computação for de 3 horas e 57 minutos, o custo estimado para executar o trabalho será de US$ 0,40 * ~ 4 horas = ~ US$ 1,60. O custo real provavelmente será um pouco maior devido ao tempo extra para iniciar os nós. Você pode encontrar o custo de nó por hora na página [Preço do lote do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (selecione "Computar otimizado" ou "Computação de alto desempenho" para a categoria).

### <a name="reviewing-the-bake-results"></a>Revendo os resultados do cozimento

Depois que o assar for concluído, verifique se os voxels e os pontos de teste estão em seus locais esperados, executando o plugin de tempo de execução. Mais informações estão no [Design Process Overview for Acoustics](design-process.md).

## <a name="Local-bake"></a>Bake local
O bake local executa a simulação acústica no seu próprio computador, em vez de transferi-la para o cluster de computação do Lote do Azure. Essa pode ser uma boa opção a ser testada com a acústica sem a necessidade de uma assinatura do Azure. A simulação acústica é mais exigente em termos de computação e pode levar muito tempo, dependendo do tamanho da cena, configuração de simulação e capacidade de computação bruta da máquina de processamento.

### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware
Processador Intel de 64 bits com pelo menos 8 núcleos e 32 GB ou mais de RAM.

Por exemplo, em uma máquina de 8 núcleos com Intel Xeon E5-1660 @ 3 GHz e 32 GB de RAM -
* Uma cena pequena com 100 testes leva cerca de 2 horas em um bake rústico e cerca de 32 horas em um bake de resolução refinada.
* Uma cena maior com 1.000 testes leva até 20 horas em uma resolução rústica e cerca de 21 dias em uma resolução refinada.

### <a name="setup-docker"></a>Configurar Docker
Instalar e configurar o Docker no PC que processará a simulação -
1. Instalar o [conjunto de ferramentas do Docker](https://www.docker.com/products/docker-desktop).
2. Inicie as configurações do Docker, navegue até as opções "Avançadas" e configure os recursos para terem pelo menos 8 GB de RAM. Quanto mais CPUs você puder alocar para o Docker, mais rápido será o bake. ![Exemplo de configurações do Docker](media/DockerSettings.png)
3. Navegue até "Unidades Compartilhadas" e ative o compartilhamento para a unidade usada no processamento.![DockerDriveSharing](media/DockerSharedDrives.png)

### <a name="run-local-bake"></a>Executar um bake local
1. Clique no botão "Preparar Bake Local" na guia Bake e selecione uma pasta onde os arquivos de entrada e scripts de execução serão salvos. É possível executar o bake em qualquer máquina, desde que ela atenda aos requisitos mínimos de hardware e tenha o Docker instalado, copiando a pasta para essa máquina.
2. Inicie a simulação usando o script “runlocalbake.bat”. Esse script buscará a imagem do Docker do Project Acoustics com o conjunto de ferramentas necessário para o processamento da simulação e dará início a ela. 
3. Quando a simulação terminar, copie o arquivo .ace resultante de volta para seu projeto Unity. Para garantir que o Unity reconheça o arquivo como binário, acrescente “.bytes” à extensão do arquivo (por exemplo, “Scene1.ace.bytes”). Os logs detalhados da simulação são armazenados em “AcousticsLog.txt”. Se você tiver algum problema, compartilhe esse arquivo para ajudar com o diagnóstico.

## <a name="Data-Files"></a>Arquivos de dados

Existem quatro arquivos de dados criados por este plugin em vários pontos. Apenas um deles é necessário em tempo de execução, portanto os outros três estão dentro de pastas com o nome "Editor" para que não sejam compilados em seu projeto.

* **Assets/Editor/[SceneName]\_AcousticsParameters.asset**: Esse arquivo armazena os dados inseridos nos campos da interface do usuário do Acoustics. O local e o nome deste arquivo não podem ser alterados. Existem outros valores armazenados nesse arquivo que afetam o bake, mas são para usuários avançados e não devem ser alterados.
* **Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes**: Esse arquivo é criado durante a simulação de bake e contém os dados de pesquisa usados pelo tempo de execução para renderizar a acústica da cena. O local e o nome desse arquivo podem ser alterados usando os campos na **investigações** guia.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: Esse arquivo armazena a geometria acústica voxelizada e as propriedades do material. Calculado usando o botão **Calcular...** na guia Probes. O local e o nome desse arquivo podem ser alterados usando os campos na **investigações** guia.
* **Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml**: Esse arquivo armazena os parâmetros calculados usando o botão **Calcular...** na Guia **Investigações**. A localização e o nome desse arquivo podem ser alterados usando os campos da guia **Sondas**.

Tome cuidado para não excluir o arquivo * .ace.bytes baixado do forno. Este arquivo não é recuperável, exceto por re-gravar a cena.

## <a name="next-steps"></a>Próximas etapas
* Aplique os resultados do cozimento às fontes de som em [Visão geral do processo de design para acústica](design-process.md).

