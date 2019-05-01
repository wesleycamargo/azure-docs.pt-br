---
title: Considerações de segurança de instâncias de contêiner do Azure
description: Recomendações para proteger imagens e segredos para instâncias de contêiner do Azure e considerações gerais de segurança para qualquer plataforma de contêiner
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943990"
---
# <a name="security-considerations-for-azure-container-instances"></a>Considerações de segurança para instâncias de contêiner do Azure

Este artigo apresenta considerações de segurança para usar instâncias de contêiner do Azure para executar aplicativos de contêiner. Os tópicos incluem:

> [!div class="checklist"]
> * **Recomendações de segurança** para gerenciar imagens e segredos para instâncias de contêiner do Azure
> * **Considerações para o ecossistema de contêiner** em todo o ciclo de vida do contêiner, para qualquer plataforma de contêiner

## <a name="security-recommendations-for-azure-container-instances"></a>Recomendações de segurança para instâncias de contêiner do Azure

### <a name="use-a-private-registry"></a>Usar um registro privado

Os contêineres são criados a partir de imagens que são armazenadas em um ou mais repositórios. Esses repositórios podem pertencer a um registro público, como [Hub do Docker](https://hub.docker.com), ou para um registro privado. Um exemplo de um registro privado é o [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), que pode ser instalado localmente ou em uma nuvem privada virtual. Você também pode usar os serviços de registro de contêiner privado baseados em nuvem, incluindo [registro de contêiner do Azure](../container-registry/container-registry-intro.md). 

Uma imagem de contêiner disponível publicamente não garante a segurança. Imagens de contêiner consistem em várias camadas de software, e cada camada de software pode ter vulnerabilidades. Para ajudar a reduzir a ameaça de ataques, você deve armazenar e recuperar imagens de um registro privado, como o registro de contêiner do Azure ou Docker Trusted Registry. Além de fornecer um registro privado gerenciado, o registro de contêiner do Azure dá suporte a [autenticação baseada em entidade de serviço](../container-registry/container-registry-authentication.md) por meio do Azure Active Directory para fluxos de autenticação básica. Essa autenticação inclui acesso baseado em função (pull) somente leitura, gravação (push) e permissões de proprietário.

### <a name="monitor-and-scan-container-images"></a>Monitorar e examinar imagens de contêiner

Monitoramento de segurança e soluções de verificação, como [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) e [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) estão disponíveis por meio do Marketplace do Azure. Você pode usá-los para verificar as imagens de contêiner em um registro privado e identificar possíveis vulnerabilidades. É importante entender a profundidade da verificação de que as diferentes soluções fornecem. 

### <a name="protect-credentials"></a>Proteger credenciais

Contêineres podem ser distribuídos entre vários clusters e regiões do Azure. Assim, você deve proteger as credenciais necessárias para logons ou acesso à API, como senhas ou tokens. Certifique-se de que somente usuários privilegiados podem acessar esses contêineres em trânsito e em repouso. Inventariar todos os segredos de credencial e, em seguida, exigem que os desenvolvedores usem ferramentas de gerenciamento de segredos emergentes que são projetadas para plataformas de contêiner.  Certifique-se de que sua solução inclui bancos de dados criptografados, criptografia de TLS para dados de segredos em trânsito e com menos privilégios [controle de acesso baseado em função](../role-based-access-control/overview.md). [O Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) é um serviço de nuvem que protege as chaves de criptografia e segredos (como certificados, cadeias de caracteres de conexão e senhas) para aplicativos em contêineres. Como esses dados são confidenciais e críticos de negócios, acesso seguro a sua chave de cofres para que apenas usuários e aplicativos autorizados possam acessá-los.

## <a name="considerations-for-the-container-ecosystem"></a>Considerações para o ecossistema de contêiner

As seguintes medidas de segurança, implementados bem e gerenciados com eficiência, podem ajudar a proteger e seu ecossistema de contêiner. Aplicam essas medidas em todo o ciclo de vida do contêiner, desde o desenvolvimento por meio da implantação de produção e a uma variedade de plataformas, hosts e orquestradores de contêiner. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Use o gerenciamento de vulnerabilidade como parte de seu ciclo de vida de desenvolvimento de contêiner 

Usando o gerenciamento de vulnerabilidades em vigor em todo o ciclo de vida de desenvolvimento de contêiner, você melhorar as chances de que você identifica e resolver questões de segurança antes que se tornem um problema mais sério. 

### <a name="scan-for-vulnerabilities"></a>Verificação de vulnerabilidades 

Novas vulnerabilidades são descobertas o tempo todo, portanto, procurando e identificação de vulnerabilidades são um processo contínuo. Incorpore em todo o ciclo de vida do contêiner de verificação de vulnerabilidade:

* Como uma última verificação em seu pipeline de desenvolvimento, você deve executar uma verificação de vulnerabilidade em contêineres antes de enviar por push as imagens em um registro público ou privado. 
* Continue a verificação de imagens de contêiner no registro para identificar qualquer falha que alguma forma foram perdida durante o desenvolvimento e para resolver qualquer vulnerabilidades descobertas recentemente que podem existir no código usado nas imagens de contêiner.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Vulnerabilidades de imagem do mapa para contêineres em execução 

Você precisa ter um meio de vulnerabilidades de mapeamento identificado em imagens de contêiner para executar contêineres, para que problemas de segurança podem ser atenuados ou resolvidos.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Certifique-se de que apenas imagens aprovadas são usadas em seu ambiente 

Há suficiente alteração e a volatilidade em um ecossistema de contêiner sem permitir também contêineres desconhecidos. Permitir imagens de contêiner aprovados única. Ter as ferramentas e processos em vigor para monitorar e impeça o uso de imagens de contêiner não aprovados. 

É uma maneira eficiente de reduzir a superfície de ataque e impedindo que os desenvolvedores cometerem críticas de segurança controlar o fluxo de imagens de contêiner em seu ambiente de desenvolvimento. Por exemplo, você pode sancionar uma única distribuição do Linux como uma imagem base, preferencialmente um que seja mais enxuto (Alpine ou CoreOS em vez de Ubuntu), para minimizar a superfície para ataques potenciais. 

Imagem de assinatura ou impressão digital pode fornecer uma cadeia de custódia que lhe permite verificar a integridade dos contêineres. Por exemplo, o registro de contêiner do Azure dá suporte ao Docker [relação de confiança de conteúdo](https://docs.docker.com/engine/security/trust/content_trust) de modelo, que permite que os editores de imagem assinar as imagens que são enviados por push para um registro e os consumidores de imagem para efetuar pull assinado apenas imagens.

### <a name="permit-only-approved-registries"></a>Permitir somente os registros aprovados 

É uma extensão de garantir que seu ambiente usa apenas imagens aprovadas permitir que somente o uso de registros de contêiner aprovados. Exigir o uso de registros de contêiner aprovados reduz a exposição a risco limitando o potencial para a introdução de vulnerabilidades desconhecidas ou problemas de segurança. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Garantir a integridade das imagens em todo o ciclo de vida 

Parte do gerenciamento de segurança em todo o ciclo de vida do contêiner é garantir a integridade das imagens de contêiner no registro e como eles são alterados ou implantados em produção. 

* Imagens com vulnerabilidades, até mesmo pequenas, não devem ser permitidas para execução em um ambiente de produção. O ideal é que todas as imagens implantadas em produção devem ser salvo em um registro privado acessível para um seleto grupo. Mantenha o número de imagens de produção de pequeno para garantir que eles possam ser gerenciados com eficiência.

* Porque é difícil identificar a origem do software de uma imagem de contêiner disponível publicamente, crie imagens da origem para garantir que os dados de Conhecimento da origem da camada. Quando surge uma vulnerabilidade em uma imagem de contêiner criada automaticamente, os clientes podem encontrar um caminho mais rápido para uma resolução. Com uma imagem pública, os clientes precisam encontrar a raiz de uma imagem pública para corrigi-lo ou obter outra imagem segura do publicador. 

* Uma imagem digitalizada totalmente implantada em produção não é garantida para ser atualizado para o tempo de vida do aplicativo. As vulnerabilidades de segurança podem ser reportadas para camadas da imagem que não eram conhecidas anteriormente ou que foram introduzidas após a implantação em produção. 

  Auditar periodicamente as imagens implantadas em produção para identificar as imagens que estão desatualizados ou não foram atualizadas em instantes. Você pode usar as metodologias de implantação "blue-green" e sem interrupção de mecanismos de atualização para atualizar as imagens de contêiner sem tempo de inatividade. Você pode digitalizar imagens usando as ferramentas descritas na seção anterior. 

* Use um pipeline de CI (integração contínua) com segurança integrada varredura para compilar imagens seguras e enviá-las ao seu registro privado. A verificação de vulnerabilidade incorporada à solução de CI garante que as imagens que passam em todos os testes sejam enviadas para o registro privado a partir do qual as cargas de trabalho de produção são implantadas. 

  Uma falha de pipeline de CI garante que as imagens vulneráveis não sejam enviadas para o registro privado que é usado para implantações de carga de trabalho de produção. Ele também automatiza a verificação se não houver um número significativo de imagens de segurança de imagem. Caso contrário, a auditoria manual de imagens em busca de vulnerabilidades de segurança pode ser muito demorada e sujeita a erros. 

### <a name="enforce-least-privileges-in-runtime"></a>Impor o mínimo de privilégios em tempo de execução 

O conceito de privilégios mínimos é uma prática recomendada de segurança básica que também se aplica aos contêineres. Quando uma vulnerabilidade é explorada, ele geralmente fornece ao invasor o acesso e privilégios iguais do aplicativo comprometido ou processo. Garantir que contêineres funcionam com os privilégios mais baixos e o acesso necessário para realizar o trabalho reduz a exposição a risco. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Reduzir a superfície de ataque do contêiner, removendo privilégios desnecessários 

Você também pode minimizar a superfície de ataque potencial, removendo qualquer processos não utilizados ou desnecessários ou privilégios de tempo de execução do contêiner. Contêineres com privilégios execute como raiz. Se um usuário mal-intencionado ou carga de trabalho realiza o escape em um contêiner com privilégios, o contêiner será executado como raiz no sistema.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Arquivos de lista de permissões e executáveis que o contêiner tem permissão para acessar ou executar 

Reduzindo o número de variáveis ou desconhecidos ajuda a manter um ambiente estável e confiável. A limitação de contêineres para que possam acessar ou executar somente pré-aprovados ou na lista de permissões arquivos e executáveis é um método comprovado de limitar a exposição ao risco.  

É muito mais fáceis de gerenciar uma lista de permissões quando ele for implementado desde o início. Uma lista de permissões fornece uma medida de controle e capacidade de gerenciamento que você saiba quais arquivos e executáveis são necessários para o aplicativo funcione corretamente. 

Uma lista de permissões não só reduz a superfície de ataque, mas pode também fornecer uma linha de base quanto a anomalias e impedir que os casos de uso do "vizinho barulhento" e cenários de debates do contêiner. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Impor a segmentação de rede em contêineres em execução  

Para ajudar a proteger os contêineres em uma sub-rede de riscos de segurança em outra sub-rede, manter a segmentação de rede (ou segmentação nano) ou de diferenciação entre contêineres em execução. Manter a segmentação de rede também pode ser necessária para o uso de contêineres em setores que são necessários para atender às exigências de conformidade.  

Por exemplo, a ferramenta de parceiro [azul-piscina](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) fornece uma abordagem automatizada para segmentação nano. Azul-piscina monitora as atividades de rede de contêiner no tempo de execução. Ele identifica todas as conexões de rede de entrada e saída de/para a internet pública, serviços, endereços IP e outros contêineres. Segmentação Nano é criada automaticamente com base no tráfego monitorado. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorar o acesso de usuário e atividade de contêiner 

Assim como acontece com qualquer ambiente de TI, você deve monitorar consistentemente atividade e acesso do usuário ao seu ecossistema de contêiner para identificar rapidamente qualquer atividade suspeita ou mal-intencionado. O Azure fornece um contêiner de monitoramento de soluções, incluindo:

* [O Azure Monitor para contêineres](../azure-monitor/insights/container-insights-overview.md) para monitorar o desempenho das cargas de trabalho implantados em ambientes Kubernetes hospedados no serviço de Kubernetes do Azure (AKS). O Azure Monitor para contêineres oferece visibilidade de desempenho coletando métricas de processador e memória de controladores, nós e contêineres disponíveis no Kubernetes por meio da API de Métricas. 

* O [solução de monitoramento de contêiner do Azure](../azure-monitor/insights/containers.md) ajuda você a exibe e gerenciar outros Docker e Windows hosts de contêiner em um único local. Por exemplo: 

  * Exiba informações detalhadas de auditoria que mostra os comandos usados com contêineres. 
  * Solucionar problemas de contêineres exibindo e pesquisando logs centralizados sem precisar exibir remotamente os hosts do Docker ou do Windows.  
  * Encontre contêineres que podem estar com ruídos e consumindo recursos em excesso em um host.
  * Exiba centralizadas de CPU, memória, armazenamento e informações de uso e desempenho de rede para contêineres.  

  A solução dá suporte aos orquestradores de contêiner, incluindo Docker Swarm, DC/OS, Kubernetes, Service Fabric e não Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorar a atividade de recurso de contêiner 

Monitore sua atividade de recurso, como arquivos, rede e outros recursos que acessam seus contêineres. O monitoramento de consumo e a atividade de recurso é útil para monitorar o desempenho e como uma medida de segurança. 

O [Azure Monitor](../azure-monitor/overview.md) habilita o monitoramento principal dos serviços do Azure, permitindo a coleta de métricas, logs de atividades e logs de diagnóstico. Por exemplo, o log de atividades informará quando novos recursos forem criados ou modificados. 

Métricas disponíveis que fornecem estatísticas de desempenho para diferentes recursos e até mesmo o sistema operacional dentro de uma máquina virtual. É possível exibir esses dados com um dos gerenciadores no Portal do Azure e criar alertas com base nessas métricas. O Azure Monitor fornece que métricas mais rápido de pipeline (5 minutos, 1 minuto), portanto, você deve usá-lo para notificações e alertas críticos em termos de tempo. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Todo acesso de usuário administrativo de contêiner para auditoria de log 

Manter uma trilha de auditoria precisa de acesso administrativo ao seu ecossistema de contêiner, registro de contêiner e imagens de contêiner. Esses logs podem ser necessários para fins de auditoria e serão útil como evidências forenses após qualquer incidente de segurança. Você pode usar o [solução de monitoramento de contêiner do Azure](../azure-monitor/insights/containers.md) para atingir esse objetivo. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o gerenciamento de vulnerabilidades de contêiner com as soluções da [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) e [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).

* Saiba mais sobre [segurança de contêiner no Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).