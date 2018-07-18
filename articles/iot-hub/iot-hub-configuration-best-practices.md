---
title: Práticas recomendadas de configuração de dispositivo para o Azure IoT Hub | Microsoft Docs
description: Saiba mais sobre práticas recomendadas para configurar dispositivos IoT em escala
author: chrisgre
manager: briz
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 7314c5ec05bec61e5bbbc406b6a39a7c5a8f011f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034180"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Práticas recomendadas para configuração do dispositivo dentro de uma solução de IoT

O gerenciamento automático do dispositivo no Hub IoT do Azure automatiza muitas das tarefas repetitivas e complexas de gerenciamento de grande frotas de dispositivos sobre a totalidade de seu ciclo de vida. Este artigo define muitas das práticas recomendadas para várias funções envolvidas no desenvolvimento e operar uma solução de IoT.

* **Fabricante/integrador de hardware de IoT**: Fabricantes de hardware de IoT, os integradores montando hardware de vários fabricantes ou os fornecedores de hardware para uma implantação de IoT fabricado ou integrado por outros fornecedores. Envolvidas no desenvolvimento e a integração de firmware, sistemas operacionais incorporados e software incorporado.
* **Desenvolvedor de soluções IoT**: o desenvolvimento de uma solução de IoT normalmente é feito por um desenvolvedor de soluções. Esse desenvolvedor pode ser parte de uma equipe interna ou um SI (integrador de sistema) especializado nesta atividade. O desenvolvedor de soluções de IoT pode desenvolver vários componentes da solução IoT do zero, integrar vários componentes prontos ou de código aberto ou personalizar um [acelerador de solução IoT][lnk-solution].
* **Operador de solução IoT**: quando a solução IoT é implantada, ela requer operações, monitoramento, atualizações e manutenção de longo prazo. Essas tarefas podem ser feitas por uma equipe interna composta por especialistas em tecnologia da informação, equipes de operações e manutenção de hardware e especialistas em domínio que monitoram o comportamento correto da infraestrutura geral de IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Compreender o gerenciamento de dispositivo automático para configurar dispositivos IoT em escala

Gerenciamento de dispositivo automático inclui muitos benefícios de [dispositivo gêmeo][lnk-device-twins] e [módulo gêmeo][lnk-module-twins] sincronizar desejado e estados relatados entre os dispositivos e a nuvem.  [Configurações de dispositivo automático][ lnk-auto-device-config] automaticamente atualizar grandes conjuntos de gêmeo e resumir progresso e conformidade. As etapas de alto nível a seguir descrevem o gerenciamento de dispositivo como automático é desenvolvido e usado:

* O **IoT fabricante de hardware/integrador** implementa recursos de gerenciamento de dispositivos dentro de um aplicativo incorporado usando [dispositivo gêmeo][lnk-device-twins]. Esses recursos podem incluir a instalação de software, atualizações de firmware e atualização e gerenciamento de configurações.
* O **desenvolvedor de solução de IoT** implementa a camada de gerenciamento de operações de gerenciamento de dispositivo usando [dispositivo gêmeo][lnk-device-twins] e [automático do dispositivo configurações de][lnk-auto-device-config]. A solução deve incluir a definição de uma interface de operador para executar tarefas de gerenciamento de dispositivo.
* O **operador de solução de IoT** usa a solução de IoT para executar tarefas de gerenciamento de dispositivos, especialmente para agrupar dispositivos juntos, alterações de configuração, como atualizações de firmware de iniciar, monitorar o andamento e solucionar problemas surgem.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware de IoT

Seguem as práticas recomendadas para fabricantes de hardware e integradores de lidar com o desenvolvimento de software inserido:

* **Implementar [dispositivo gêmeos][lnk-device-twins]:** dispositivo gêmeos habilitam sincronização de configurações desejadas da nuvem e propriedades de dispositivo e a configuração atuais de relatórios.  É a melhor maneira de implementar dispositivo gêmeo incorporado aplicativos por meio de [SDKs do Azure IoT][lnk-azure-sdk].  Dispositivo gêmeos são mais adequados para a configuração, pois eles: um. suporte à comunicação bidirecional b. permitem para ambos conectados e disconectados estados de dispositivos c. seguem o princípio de consistência eventual d. são totalmente consultável na nuvem
* **Estrutura o dispositivo gêmeo para gerenciamento de dispositivo:** o dispositivo gêmeo devem ser estruturadas de modo que as propriedades de gerenciamento de dispositivo são agrupadas logicamente em seções. Isso permitirá que as alterações de configuração sejam isoladas sem afetar outras seções do gêmeo. Por exemplo, crie uma seção nas propriedades desejadas para firmware, outra seção para software e uma terceira seção para configurações de rede. 
* **Relate os atributos do dispositivo que são úteis para o gerenciamento de dispositivos:** Atributos como marca e modelo de dispositivos físicos, firmware, sistema operacional, número de série e outros identificadores são úteis para relatórios e como parâmetros para segmentar alterações de configuração.
* **Defina os estados principais para relatar status e progresso:** os estados de nível superior devem ser enumerados para que possam ser relatados ao operador. Por exemplo, uma atualização de firmware deveria reportar status como Atual, Baixando, Aplicando, Em Progresso, e Erro.  Defina campos adicionais para mais informação em cada estado.  

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções IoT

A seguir são melhores práticas para desenvolvedores de solução IoT que estão compilando sistemas baseados em Azure:

* **Implementar [dispositivo gêmeos][lnk-device-twins]:** dispositivo gêmeos habilitam sincronização de configurações desejadas da nuvem e propriedades de dispositivo e a configuração atuais de relatórios.  É a melhor maneira de implementar dispositivo gêmeo incorporado aplicativos por meio de [SDKs do Azure IoT][lnk-azure-sdk].  Dispositivo gêmeos são mais adequados para a configuração, pois eles: um. suporte à comunicação bidirecional b. permitem para ambos conectados e disconectados estados de dispositivos c. seguem o princípio de consistência eventual d. são totalmente consultável na nuvem
* **Organizar usando dispositivo duas marcas de dispositivos:** a solução deve permitir que o operador definir os anéis de qualidade ou outros conjuntos de dispositivos com base em várias estratégias de implantação, como canary. Organização de dispositivo pode ser implementada em sua solução usando dispositivo duas marcas e [consultas][lnk-queries].  Organização do dispositivo é necessária para permitir que as implantações de configuração com precisão e com segurança.
* **Implementar [configurações de dispositivo automático][lnk-auto-device-config]:** implantar configurações de dispositivo automático e alterações na configuração do monitor de grandes conjuntos de dispositivos IoT via dispositivo gêmeo.  As configurações automáticas de dispositivos segmentam conjuntos de dispositivos gêmeos por meio da **condição de destino** que é uma consulta em marcas de dispositivos gêmeos ou em propriedades relatadas. O **conteúdo alvo** é o conjunto de propriedades desejadas que serão configuradas dentro do dispositivo gêmeo alvo. O conteúdo alvo deve alinhar com a estrutura do dispositivo gêmeo definida pelo fabricante/integrador de hardware IoT.  As **métricas** são consultas em propriedades relatadas de dispositivos gêmeos e também devem ser alinhadas com a estrutura de dispositivo gêmeo definida pelo fabricante/integrador de hardware de IoT. As configurações automáticas de dispositivos também têm o benefício do IoT Hub executar operações de dispositivo gêmeo a uma taxa que nunca excederá os [limites de aceleração][lnk-throttling] para leituras e atualizações de dispositivos gêmeos.
* **Use[Serviço de provisionamento de dispositivos ][lnk-dps]:** Os desenvolvedores de soluções devem usar o Serviço de provisionamento de dispositivos para atribuir marcas a novos dispositivos gêmeos, de forma que eles sejam configurados automaticamente por **configurações automáticas do dispositivo** que são direcionadas a gêmeos com essa marca. 

## <a name="iot-solution-operator"></a>Operador de solução IoT

A seguir são melhores práticas para operadores de solução IoT que usam uma compilação de solução de IoT no Azure:

* **Organizar dispositivos para gerenciamento :** a solução deve permitir ou definir os anéis de qualidade ou outros conjuntos de dispositivos com base em várias estratégias de implantação, como canary. Os conjuntos de dispositivos serão usados para implementar alterações de configuração e executar outras operações de gerenciamento de dispositivos em escala.
* **Realize mudanças de configuração usando um lançamento em fases:** Um lançamento em etapas é um processo geral pelo qual um operador implementa alterações em um conjunto ampliado de dispositivos de IoT. O objetivo é fazer alterações gradualmente para reduzir o risco de fazer alterações significativas em larga escala.  O operador deve usar a interface da solução para criar uma [configuração de dispositivo automática][lnk-auto-device-config] e a condição de segmentação deve segmentar um conjunto inicial de dispositivos (como um grupo canário).  O operador deve então validar a mudança de configuração no conjunto inicial de dispositivos.  Uma vez que a validação esteja completa, o operador atualizará a configuração de dispositivos automáticos para incluir um conjunto maior de dispositivos. O operador também deve definir a prioridade para que a configuração seja maior do que as outras configurações atualmente destinadas a esses dispositivos.  A implementação pode ser monitorada usando as métricas relatadas pela configuração automática do dispositivo. 
* **Execute reversões em caso de erros ou configurações incorretas:** uma configuração automática de dispositivo que cause erros ou configurações incorretas pode ser revertida alterando a **condição de segmentação** para que os dispositivos não atendam mais à condição de segmentação.  Garanta que outra configuração de dispositivo automático de baixa prioridade ainda está destinada a esses dispositivos.  Verifique se a reversão foi bem-sucedida visualizando as métricas: a configuração revertida não deve mais mostrar o status de dispositivos não segmentados, e as métricas da segunda configuração agora devem incluir contagens para os dispositivos que ainda são segmentados.


## <a name="next-steps"></a>Próximas etapas

* Aprenda sobre como implementar dispositivos gêmeos em [Entender e usar dispositivos gêmeos no Hub IoT][lnk-device-twins]
* Percorra as etapas para criar, atualizar ou excluir uma configuração de dispositivo automática em [Configurar e monitorar dispositivos IoT na escala ][lnk-auto-device-config].
* Implemente um padrão de atualização de firmware usando dispositivos gêmeos e configurações automáticas de dispositivos em [ Tutorial: implemente um processo de atualização de firmware de dispositivo][lnk-firmware-update].

<!-- Links -->
[lnk-device-twins]: iot-hub-devguide-device-twins.md
[lnk-module-twins]: iot-hub-devguide-module-twins.md
[lnk-auto-device-config]: iot-hub-auto-device-config.md
[lnk-firmware-update]: tutorial-firmware-update.md
[lnk-throttling]: iot-hub-devguide-quotas-throttling.md
[lnk-queries]: iot-hub-devguide-query-language.md
[lnk-dps]: ../iot-dps/how-to-manage-enrollments.md
[lnk-azure-sdk]: https://github.com/Azure/azure-iot-sdks
[lnk-solution]: https://azure.microsoft.com/features/iot-accelerators/