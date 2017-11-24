---
title: "Implantar módulos no Azure IoT Edge | Microsoft Docs"
description: "Saiba mais sobre como os módulos são implantados em dispositivos de borda"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d8688ab2daefd400e9c0948853459dd238fa0d43
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="understand-iot-edge-deployments-for-single-devices-or-at-scale---preview"></a>Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala – versão prévia

Os dispositivos do IoT Edge seguem um [ciclo de vida do dispositivo] [ lnk-lifecycle] que é semelhante a outros tipos de dispositivos IoT:

1. Os dispositivos IoT Edge são provisionados, o que envolve a geração de imagem de um dispositivo com um sistema operacional e a instalação do [tempo de execução do IoT Edge][lnk-runtime].
1. Os dispositivos estão configurados para executar [módulos do IoT Edge][lnk-modules]e, em seguida, têm sua integridade monitorada. 
1. Por fim, os dispositivos podem ser desativados quando são substituídos ou ficam obsoletos.  

O Azure IoT Edge fornece duas maneiras de configurar os módulos para execução nos dispositivos IoT Edge: uma para desenvolvimento e iterações rápidas em um único dispositivo (que é usado nos tutoriais do Azure IoT Edge) e outro para gerenciar grandes frotas de dispositivos IoT Edge. Essas duas abordagens estão disponíveis no Portal do Azure e programaticamente.

Este artigo se concentra nos estágios de configuração e monitoramento para frotas de dispositivos, coletivamente chamados de implantações do IoT Edge. As etapas de implantação geral são as seguintes:   

1. Um operador define uma implantação que descreve um conjunto de módulos e os dispositivos de destino. Cada implantação tem um manifesto de implantação que reflete essa informação. 
1. O serviço Hub IoT se comunica com todos os dispositivos de destino para configurá-los com os módulos desejados. 
1. O serviço Hub IoT recupera o status dos dispositivos IoT Edge e revela os que devem ser monitorados pelo operador.  Por exemplo, um operador pode ver quando um dispositivo de borda não foi configurado com êxito ou se um módulo falha durante o tempo de execução. 
1. A qualquer momento, novos dispositivos IoT Edge que atendem às condições de destino são configurados para a implantação. Por exemplo, uma implantação que se destina a todos os dispositivos IoT Edge em Washington configura automaticamente um novo dispositivo IoT Edge assim que ele é configurado e adicionado ao grupo de dispositivos de Washington. 
 
Este artigo o orienta por meio de cada componente envolvido na configuração e no monitoramento de uma implantação. Para obter instruções de criação e atualização de uma implantação, confira [Implantar e monitorar os módulos do IoT Edge em larga escala][lnk-howto].

## <a name="deployment"></a>Implantação

Uma implantação atribui ao módulo do IoT Edge imagens para execução como instâncias em um conjunto de dispositivos IoT Edge de destino. Ele funciona com a configuração de um manifesto de implantação do IoT Edge para incluir uma lista de módulos com os parâmetros de inicialização. Uma implantação pode ser atribuída a um único dispositivo (normalmente com base na ID de dispositivo) ou a um grupo de dispositivos (com base em marcas). Depois que um dispositivo IoT Edge recebe um manifesto de implantação, ele baixa e instala as imagens de contêiner do módulo dos repositórios do respectivo contêiner e os configura adequadamente. Depois de criar uma implantação, um operador pode monitorar o status da implantação para ver se os dispositivos de destino estão configurados corretamente.   

Os dispositivos precisam ser provisionados como dispositivos IoT Edge para serem configurados com uma implantação. Os itens abaixo são pré-requisitos e não estão incluídos na implantação:
* O sistema operacional base
* Docker 
* Provisionamento do tempo de execução do IoT Edge 

### <a name="deployment-manifest"></a>Manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve os módulos a serem configurados nos dispositivos IoT Edge de destino. Ele contém os metadados de configuração para todos os módulos, incluindo os módulos do sistema necessários (especificamente o agente do IoT Edge e o hub IoT Edge).  

Os metadados de configuração para cada módulo incluem: 
* Versão 
* Tipo 
* Status (por exemplo, em execução ou parado) 
* Política de reinicialização 
* Repositório de imagem e contêiner 
* Rotas para dados de entrada e saída 

### <a name="target-condition"></a>Condição de destino

As condições especificam se um dispositivo IoT Edge deve estar no escopo de uma implantação. As condições de destino se baseiam em marcas do gêmeo do dispositivo. 

### <a name="priority"></a>Prioridade

Uma prioridade define se uma implantação deve ser aplicada a um dispositivo de destino em relação a outras implantações. Uma prioridade de implantação é um inteiro positivo, e os maiores números indicam as prioridades mais altas. Se um dispositivo IoT Edge é afetado por mais de uma implantação, a implantação com a prioridade mais alta será aplicada.  As implantações com prioridades inferiores não são aplicadas nem mescladas.  Se um dispositivo é afetado com duas ou mais implantações com a mesma prioridade, a implantação criada mais recentemente (determinada pelo carimbo de hora de criação) se aplica.

### <a name="labels"></a>Rótulos 

Rótulos são pares de chave/valor de cadeia de caracteres que você pode usar para filtrar e agrupar as implantações. Uma implantação pode ter vários rótulos. Os rótulos são opcionais e não afetam a configuração real dos dispositivos IoT Edge. 

### <a name="deployment-status"></a>Status da Implantação

Uma implantação pode ser monitorada para determinar se ela foi aplicada com êxito a um dispositivo IoT Edge de destino.  Um dispositivo Edge de destino será exibido em uma ou mais das seguintes categorias de status: 
* **Destino** mostra os dispositivos IoT Edge que correspondem à implantação da condição de destino.
* **Real** mostra os dispositivos IoT Edge de destino que não são o destino de outra implantação de prioridade mais alta.
* **Íntegro** mostra os dispositivos IoT Edge que indicaram ao serviço que os módulos foram implantados com êxito. 
* **Não Íntegro** mostra os dispositivos IoT Edge que indicaram ao serviço que um ou mais módulos não foram implantados com êxito. Para investigar o erro mais detalhadamente, conecte-se remotamente a esses dispositivos e exiba os arquivos de log.
* **Desconhecido** mostra os dispositivos IoT Edge que não indicaram status referentes a essa implantação. Para investigar melhor, exiba arquivos de log e informações de serviço.

## <a name="phased-rollout"></a>Distribuição em fases 

Uma distribuição em fases é um processo geral no qual um operador implanta alterações em um conjunto maior de dispositivos IoT Edge. O objetivo é fazer alterações gradualmente para reduzir o risco de fazer alterações significativas em larga escala.  

Uma distribuição em fases é executada nas seguintes fases e etapas: 
1. Estabeleça um ambiente de teste de dispositivos IoT Edge provisionando-os e definindo uma marca de gêmeo de dispositivo como `tag.environment='test'`. O ambiente de teste deve espelhar o ambiente de produção que a que se destinará a implantação. 
1. Crie uma implantação, incluindo as configurações e os módulos desejados. A condição de destino deve ter como destino o ambiente de teste do dispositivo IoT Edge.   
1. Valide a nova configuração do módulo no ambiente de teste.
1. Atualize a implantação para incluir um subconjunto de dispositivos IoT Edge de produção, adicionando uma nova marca à condição de destino. Além disso, verifique se a prioridade da implantação é maior do que a de outras implantações atualmente destinadas a esses dispositivos 
1. Verifique se a implantação foi bem-sucedida nos dispositivos IoT de destino exibindo o status da implantação.
1. Atualize a implantação para ter como destino todos os dispositivos IoT Edge de produção restantes.

## <a name="rollback"></a>Reversão

As implantações podem ser revertidas em caso de erros ou problemas de configuração.  Como uma implantação define a configuração absoluta do módulo para um dispositivo do IoT Edge, outras implantações também devem estar voltadas para o mesmo dispositivo em uma prioridade mais baixa, mesmo se o objetivo é remover todos os módulos.  

Execute as reversões na sequência abaixo: 
1. Confirme que uma segunda implantação também tem como destino o mesmo conjunto de dispositivos. Se a meta da reversão é remover todos os módulos, a segunda implantação não deve incluir todos os módulos. 
1. Modifique ou remova a expressão de condição de destino da implantação que você deseja reverter para que os dispositivos não atendam à condição de destino.
1. Verifique se a reversão teve êxito exibindo o status da implantação.
   * A implantação revertida não deve mostrar o status dos dispositivos que foram revertidos.
   * A segunda implantação agora deve incluir o status da implantação dos dispositivos que foram revertidos.


## <a name="next-steps"></a>Próximas etapas

* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala][lnk-howto].
* Saiba mais sobre outros conceitos de IoT Edge, como o [tempo de execução do IoT Edge] [ lnk-runtime] e os [módulos do IoT Edge][lnk-modules].

<!-- Links -->
[lnk-lifecycle]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-modules]: iot-edge-modules.md
[lnk-howto]: how-to-deploy-monitor.md