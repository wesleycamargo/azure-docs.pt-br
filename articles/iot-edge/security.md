---
title: "Segurança no Azure IoT Edge | Microsoft Docs"
description: "Segurança, autenticação e autorização de dispositivos IoT Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8a5bf1f35fcdd779cf27edeba7dfd5705cbae205
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="securing-azure-iot-edge---preview"></a>Segurança Azure IoT Edge - versão prévia

Protegendo a borda inteligente é necessário concede confiança na operação de uma solução de IoT de ponta a ponta. Azure IoT Edge destina-se a segurança é extensível para perfis de risco diferentes cenários de implantação, e oferece a mesma proteção que você espera de todos os serviços do Azure.

Azure IoT Edge é executado em um hardware diferente, dá suporte a Linux e Windows e é aplicável aos cenários de implantação diferentes.  Risco avaliado depende de muitas considerações, incluindo a propriedade da solução, geografia de implantação, confidencialidade de dados, privacidade, requisitos normativos e vertical do aplicativo.  Em vez de oferecer soluções concretas para cenários específicos, faz sentido criar uma estrutura de segurança extensível baseada em princípios com aterramento bem projetados para a escala. 
 
Este artigo fornece uma visão geral da segurança estrutural. Para obter mais informações, consulte [Protegendo borda inteligente][lnk-edge-blog].

>[!NOTE]
>A estrutura de segurança descrita abaixo é adicionar ao produto agora e estará disponível no momento do lançamento da disponibilidade geral do Azure IoT Edge. O produto está atualmente em visualização pública, uma versão pretende permitir o desenvolvimento e protótipos de soluções de borda, implantações de produção completo não precisam do framework de segurança completa.   

## <a name="standards"></a>Padrões

Padrões de promovem a facilidade de investigação e de implementação, que são a marca de segurança.  Uma solução de segurança bem projetado deve se prestam para investigação na avaliação para criar a relação de confiança e não deve ser um obstáculo para implantação.  O design do framework para proteger o Azure IoT Edge é proveniente dos ágeis e protocolos de segurança do setor comprovada aproveitar familiaridade e reutilização. 

## <a name="authentication"></a>Autenticação

Saber, sem dúvida, quais atores, dispositivos e componentes estão participando no fornecimento de valor por meio de uma solução de IoT de ponta a ponta é muito importante na criação de relação de confiança.  Esse conhecimento oferece accountability segura de participantes para habilitar a base de admissão.  Azure IoT Edge atinge esse conhecimento por meio da autenticação.  O mecanismo primário de autenticação para a plataforma Azure IoT Edge é a autenticação baseada em certificado.  Esse mecanismo é derivado de um conjunto de padrões que regem a infraestrutura de chave pública (PKiX) pelo Internet Engineering Task Force (IETF).     

A estrutura de segurança do Azure IoT Edge chamadas para identidades de certificado único para todos os dispositivos, módulos (contêineres que encapsulam a lógica do dispositivo) e atores interagir com o dispositivo de extremidade do Azure IoT seja fisicamente ou por meio de uma conexão de rede.  Nem todo cenário ou componente pode se prestar a autenticação baseada em certificado para o qual a extensibilidade do framework segurança oferece vias seguras efetuado. 

## <a name="authorization"></a>Autorização

A capacidade de delegar autoridade e controlar o acesso é fundamental para atingir um princípio de segurança – o princípio de menos privilégios.  Dispositivos, módulos e atores podem obter acesso apenas aos recursos e dados no seu escopo de permissão e somente quando é permitido em termos de arquitetura.  Isso significa que algumas permissões são configuráveis com privilégios suficientes e outros em termos de arquitetura imposta.  Por exemplo, enquanto um módulo pode ser autorizado por meio de configuração com privilégios para iniciar uma conexão ao Azure IoT Hub, não há nenhum motivo por que um módulo em um dispositivo de Azure IoT Edge deve acessar duas de um módulo em outro dispositivo de borda de IoT do Azure.  Por esse motivo, o último poderia ser arquitetonicamente impedia. 

Outros esquemas de autorização incluem direitos de assinatura de certificado e o controle de acesso baseado em função (RBAC).  A extensibilidade da estrutura de segurança permite adoção de outros esquemas de autorização madura. 

## <a name="attestation"></a>Atestado

Atestado garante a integridade de bits de software.  É importante para a detecção e prevenção de malware.  A estrutura de segurança do Azure IoT Edge classifica Atestado em três categorias principais:

* Atestado estático
* Atestado do tempo de execução
* Atestado do software

### <a name="static-attestation"></a>Atestado estático

Atestado estático é a verificação de integridade de todos os componentes de software, incluindo os sistemas operacionais, todos os tempos de execução, e informações de configuração no dispositivo de inicialização.  Ele é conhecido como a inicialização segura.  A estrutura de segurança para dispositivos Azure IoT Edge estende a fornecedores de silício e incorpora os recursos de hardware seguro bicicletas para garantir que os processos de Atestado estático. Esses processos incluem a inicialização segura e firmware seguro atualizar processos.  Trabalhando em estreita colaboração com fornecedores de silicon elimina as camadas de firmware supérfluas minimizando a superfície de ameaça. 

### <a name="runtime-attestation"></a>Atestado do tempo de execução

Depois que um sistema tiver concluído o processo de inicialização validado e está ativo e em execução, bem projetados sistemas seguros detectaria tenta injetar malware através de interfaces e portas de sistemas e tomar as contramedidas adequadas.  Para dispositivos de borda inteligente em custódia físico de atores mal-intencionado, é possível inserir malcontent através de meios diferentes interfaces de dispositivo como violações e ataques de canal lateral.   Tal malcontent, que pode ser na forma de malware ou alterações de configuração não autorizado, normalmente não seria detectada pelo processo de inicialização segura porque eles ocorrem após o processo de inicialização.  Medidas oferecido ou imposta pelo hardware do dispositivo muito contribui para a warding desativar essas ameaças.  A estrutura de segurança para o Azure IoT Edge explicitamente chama para extensões para combatting ameaças em tempo de execução.     

### <a name="software-attestation"></a>Atestado do software

Todos os sistemas íntegros, incluindo sistemas inteligentes de borda devem ser compatível para atualizações e patches.  A segurança é importante para os processos de atualização caso contrário, podem ser vetores de ameaças potenciais.  A estrutura de segurança para chamadas de Azure IoT Edge atualizações por meio de medida e pacotes para assegurar a integridade e autenticar a fonte dos pacotes assinados.  Isso é aplicável a todos os sistemas operacionais e os bits de software do aplicativo. 

## <a name="hardware-root-of-trust"></a>Raiz de hardware de confiança

Para muitas implantações de dispositivos inteligentes de borda, especialmente aqueles implantados em locais onde potenciais atores mal-intencionado tem acesso físico ao dispositivo, a segurança oferecida pelo hardware de dispositivo é a última defesa para proteção.  Por esse motivo, é mais importante para tais implantações ancoragem confiança em hardware resistente a adulterações.  A estrutura de segurança para o Azure IoT Edge envolve a colaboração segura silicon fornecedores de hardware para oferecer a diferentes tipos de raiz de hardware de confiança para acomodar vários perfis de risco e cenários de implantação. Eles incluem o uso de seguro silicon respeita os padrões de protocolo de segurança comuns como Trusted Platform Module (ISO/IEC 11889) e mecanismo de composição de identificador de dispositivo (dados do Trusted Computing Group).  Eles também incluem tecnologias enclave segura como TrustZones e extensões de proteção de Software (SGX). 

## <a name="certification"></a>Certificação

Para ajudar os clientes a tomar decisões informadas quando adquirir dispositivos de Azure IoT Edge para sua implantação, a estrutura de borda de IoT do Azure inclui os requisitos de certificação.  Base para esses requisitos são certificações pertencentes às declarações de segurança e certificações pertencente a validação da implementação de segurança.  Por exemplo, uma certificação de declaração de segurança informa que o dispositivo IoT Edge usa hardware seguro, conhecido como resistir a ataques de inicialização. Certificação uma validação informa se o hardware seguro foi corretamente implementado para oferecer esse valor no dispositivo.  Para manter o princípio de simplicidade, o framework oferece a visão de manter a carga de certificação mínimo.   

## <a name="extensibility"></a>Extensibilidade

Extensibilidade é cidadão de primeira classe na estrutura de segurança Azure IoT Edge.  Com a tecnologia de IoT orientando tipos diferentes de transformações de negócios, faz sentido que segurança perfeitamente deve evoluir ao mesmo tempo para endereço emergentes cenários.  A estrutura de segurança do Azure IoT Edge começa com uma base sólida na qual ele cria no extensibilidade em diferentes dimensões a serem incluídas: 

* Serviços de segurança de primeira como o serviço de provisionamento do dispositivo para o Azure Hub IoT.
* Serviços de terceiros, como serviços gerenciados de segurança para verticais do aplicativo diferente (como industrial ou serviços de saúde) ou o foco de tecnologia (como a segurança de monitoramento de malha redes ou serviços de certificação de hardware silicon) por meio de uma rede avançada do parceiros.
* Sistemas herdados para incluir o aperfeiçoamento com estratégias de segurança alternativas, como usando a tecnologia segura diferente de certificados para autenticação e gerenciamento de identidades.
* Proteger o hardware para a adoção contínua de novas tecnologias de hardware seguro e silicon contribuições de parceiro.

Estes são alguns exemplos de dimensões para extensibilidade e estrutura de segurança do Azure IoT Edge é projetada para ser seguro desde o início para dar suporte a essa extensibilidade. 

No final, o sucesso mais alto na proteção da borda inteligente resultados de colaboração contribuições da comunidade aberta acompanhando o interesse comum na proteção de IoT.  Essas contribuições podem ser na forma de tecnologias seguras ou serviços.  A estrutura de segurança Azure IoT Edge oferece uma base sólida para a segurança é extensível para a cobertura de máximo oferecer o mesmo nível de confiança e integridade na borda inteligente como com a nuvem do Azure.  

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre como o Azure IoT Edge é [protegendo a borda inteligente][lnk-edge-blog].

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 