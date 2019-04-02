---
title: Início Rápido do Projeto Acústico com o Unity
titlesuffix: Azure Cognitive Services
description: Ao usar o conteúdo de exemplo, experimente os controles de design do Projeto Acústico no Unity e implante o Windows Desktop.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 468c5584d21c226d6ffce55ff3981e629d872c56
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317180"
---
# <a name="project-acoustics-unity-quickstart"></a>Início Rápido do Unity do Projeto Acústico
Use o conteúdo de exemplo do Projeto Acústico para Unity para fazer experimento com controles de design com suporte de simulação.

Requisitos de software:
* [Unity 2018.2+](http://unity3d.com) para Windows
* [Pacote de conteúdo de exemplo do Projeto Acústico](https://www.microsoft.com/download/details.aspx?id=57346)

O que está incluído no pacote de exemplo?
* Cena do Unity com geometria, fontes de som e controles de jogo
* Plugin do Projeto Acústico 
* Ativos acústicos preparados para a cena

## <a name="import-the-sample-package"></a>Importe o pacote de exemplo
Importe o pacote de exemplo para um novo projeto do Unity. 
* No Unity, vá para **ativos > Importar pacote > pacote personalizado...**

    ![Captura de tela das opções Importar Pacote do Unity](media/import-package.png)  

* Escolha **ProjectAcoustics.unitypackage**

Se você estiver importando o pacote em um projeto existente, consulte [Integração ao Unity](unity-integration.md) para obter etapas e notas adicionais.

## <a name="restart-unity"></a>Reinicie o Unity
A parte de assar do kit de ferramentas de acústica requer a versão de tempo de execução de script do .NET 4.x. A importação de pacotes atualizará as configurações do player do Unity. Reinicialização do Unity para essa configuração tenha efeito.

Você pode verificar se essa configuração entrou em vigor abrindo as **Configurações do Player**:

![Captura de tela do painel Configurações do Player do Unity](media/player-settings.png)

![Captura de tela do painel Configurações do Player do Unity com .NET 4.5 selecionado](media/net45.png)

## <a name="experiment-with-design-controls"></a>Experimente com os controles de design
Abra a cena de exemplo na pasta **ProjectAcousticsSample** e clique no botão reproduzir no editor do Unity. Use W, A, S, D e o mouse para mover-se. Para comparar o som da cena com e sem acústica, pressione o botão **R** até que o texto de sobreposição fique vermelho e informe "Acústica: desabilitada". Para ver os atalhos de teclado de outros controles, pressione **F1**. Os controles também podem ser usados clicando com o botão direito do mouse para selecionar a ação a ser executada e clicando com o botão esquerdo para executar a ação.

O script **AcousticsAdjust** é anexado às fontes de som na cena de exemplo, que permite os parâmetros de design por origem. 

![Captura de tela do script AcousticsAdjust do Unity](media/acoustics-adjust.png)

O exemplo a seguir explora alguns dos efeitos que podem ser produzidos com os controles fornecidos. Para obter informações detalhadas sobre cada controle, consulte o [Tutorial de Design do Unity do Projeto Acústico](unreal-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modificar atenuação de distância
O DSP de áudio fornecido pelo plug-in spatializer do Unity do **Projeto Acústico** respeita a atenuação baseada em distância por origem incorporada no Editor do Unity. Controles de atenuação de distância estão na **Audio Source** componente encontrado na **Inspetor** fontes de painel de som, em **configurações de som 3D**:

![Captura de tela do painel de opções de atenuação de distância do Unity](media/distance-attenuation.png)

O Projeto Acústico executa o cálculo em uma caixa de “região da simulação” centralizada em torno do local do player. Uma vez que os ativos acústicos no pacote de exemplo foram preparados com um tamanho de região de simulação de 45 m em torno do player, a atenuação do som deve ser projetada para cair para 0 em cerca de 45 m.

### <a name="modify-occlusion-and-transmission"></a>Modificar oclusão e transmissão
* Se o multiplicador de **Oclusão** for maior do que 1 (o padrão é 1), a oclusão será exagerada. Defini-la para menos de 1 torna o efeito da oclusão mais sutil.

* Para habilitar a transmissão através da parede, mova o controle deslizante **Transmissão (dB)** para seu nível mais baixo. 

### <a name="modify-wetness-for-a-source"></a>Modificar umidade para uma fonte
* Para alterar como a umidade muda rapidamente com a distância, use a **Distorção de Distância Perceptiva**. O **Projeto Acústico** calcula os níveis de umidade em todo o espaço a partir da simulação, que variam levemente com a distância e fornecem dicas de distância perceptiva. Aumentar a distorção de distância exagera esse efeito aumentando os níveis de umidade relacionados à distância. Valores de distorção inferiores a 1 tornam a alteração à reverberação com base em distância mais sutil. Esse efeito também pode ser ajustado em detalhes mais refinados, ajustando a **Umidade (dB)**.

* Aumente o tempo de decaimento por todo o espaço, ajustando a **Escala de Tempo de Decaimento**. Se o resultado de simulação para um par de localização de ouvinte de origem específico for um tempo de decaimento de 1,5 s e a **Escala de Tempo de Decaimento** for definida como 2, o tempo de decaimento aplicado à fonte será de 3 s.

## <a name="next-steps"></a>Próximas etapas
* Leia os detalhes completos sobre os [controles de design do Projeto Acústico baseado no Unity](unity-workflow.md)
* Explore mais os conceitos relacionados ao [processo de design](design-process.md)
* [Crie uma conta do Azure](create-azure-account.md) para explorar os processos de pré-preparação e de preparação

