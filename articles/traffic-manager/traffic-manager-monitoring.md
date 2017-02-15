---
title: "Monitoramento e failover do ponto de extremidade do Gerenciador de Tráfego| Microsoft Docs"
description: "Este artigo pode ajudar você a entender como o Gerenciador de Tráfego usa o monitoramento de ponto de extremidade e o failover automático do ponto de extremidade para ajudar clientes do Azure a implantarem aplicativos de alta disponibilidade"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: fff25ac3-d13a-4af9-8916-7c72e3d64bc7
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 4df9f744c7dde9224157eca1f869c0c420036d76

---

# <a name="traffic-manager-endpoint-monitoring-and-failover"></a>Monitoramento e failover do ponto de extremidade do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure inclui monitoramento de ponto de extremidade interno e failover automático de ponto de extremidade. Este recurso ajuda você a fornecer aplicativos de alta disponibilidade resilientes a falhas de ponto de extremidade, incluindo falhas de região do Azure.

## <a name="configure-endpoint-monitoring"></a>Configurar o monitoramento de pontos de extremidade

Para configurar o monitoramento de ponto de extremidade, é necessário especificar as configurações a seguir em seu perfil do Gerenciador de Tráfego:

* **Protocolo**. Escolha HTTP ou HTTPS. É importante observar que o monitoramento HTTPS não verifica se o seu certificado SSL é válido, ele apenas verifica se o certificado está presente.
* **Porta**. Escolha a porta usada para a solicitação.
* **Caminho**. Forneça o caminho relativo e o nome da página da Web ou arquivo que o monitoramento acessa. Uma barra (/) é uma entrada válida para o caminho relativo. Esse valor indica que o arquivo está no diretório raiz (padrão).

Para verificar a integridade de cada ponto de extremidade, o Gerenciador de Tráfego faz uma solicitação GET ao ponto de extremidade usando o protocolo, a porta e o caminho relativo fornecido.

Uma prática comum é implementar uma página personalizada dentro de seu aplicativo, por exemplo, /health.aspx. Usando esse caminho para monitoramento, você pode executar verificações específicas do aplicativo, como verificação de contadores de desempenho ou da disponibilidade do banco de dados. Com base nessas verificações personalizadas, a página retorna um código de status HTTP apropriado.

Todos os pontos de extremidade em um perfil do Gerenciador de Tráfego compartilham configurações de monitoramento. Se precisar usar configurações de monitoramento diferentes para pontos de extremidade diferentes, você pode criar [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Status do ponto de extremidade e do perfil

Você pode habilitar e desabilitar pontos de extremidade e perfis do Gerenciador de Tráfego. No entanto, uma alteração no status do ponto de extremidade também pode ocorrer como resultado das configurações e processos automatizados do Gerenciador de Tráfego.

### <a name="endpoint-status"></a>Status do ponto de extremidade

Você pode habilitar ou desabilitar um ponto de extremidade específico. O serviço subjacente, que pode ainda ser íntegro, não é afetado. A alteração do status do ponto de extremidade controla a disponibilidade do ponto de extremidade no perfil do Gerenciador de Tráfego. Quando o status de um ponto de extremidade for desabilitado, o Gerenciador de Tráfego não verificará sua integridade e o ponto de extremidade não será incluído em uma resposta DNS.

### <a name="profile-status"></a>Status do perfil

Usando a configuração de status de perfil, você pode habilitar ou desabilitar um perfil específico. Embora o status do ponto de extremidade afete um único ponto de extremidade, o status do perfil afeta todo o perfil, incluindo todos os pontos de extremidade. Quando você desabilita um perfil, os pontos de extremidade não são verificados quanto à integridade e nenhum ponto de extremidade é incluído em uma resposta DNS. Um código de resposta [NXDOMAIN](https://tools.ietf.org/html/rfc2308) é retornado para a consulta DNS.

### <a name="endpoint-monitor-status"></a>Status do monitor de ponto de extremidade

O status do monitor do ponto de extremidade é um valor gerado pelo Gerenciador de Tráfego que mostra o status do ponto de extremidade. Você não pode alterar essa configuração manualmente. O status do monitor de ponto de extremidade é uma combinação dos resultados do monitoramento de ponto de extremidade com o status de ponto de extremidade configurado. Os valores possíveis de status do monitor do ponto de extremidade estão na tabela abaixo:

| Status do perfil | Status do ponto de extremidade | Status do monitor de ponto de extremidade | Observações |
| --- | --- | --- | --- |
| Desabilitado |Habilitado |Inativo |O perfil foi desabilitado. Embora o status do ponto de extremidade seja Habilitado, o status do perfil (Desabilitado) tem precedência. Pontos de extremidade em perfis desabilitados não são monitorados. Um código de resposta NXDOMAIN é retornado para a consulta DNS. |
| &lt;qualquer&gt; |Desabilitado |Desabilitado |O ponto de extremidade foi desabilitado. Pontos de extremidade desabilitados não são monitorados. O ponto de extremidade não é incluído em respostas DNS e, portanto, não recebe tráfego. |
| Habilitado |Habilitado |Online |O ponto de extremidade é monitorado e está íntegro. Ele é incluído em respostas DNS e pode receber tráfego. |
| Habilitado |Habilitado |Degradado |As verificações de integridade de monitoramento do ponto de extremidade estão falhando. O ponto de extremidade não é incluído em respostas DNS e não recebe tráfego. |
| Habilitado |Habilitado |Verificando ponto de extremidade |O ponto de extremidade é monitorado, mas os resultados da primeira investigação ainda não foram recebidos. CheckingEndpoint é um estado temporário que geralmente ocorre imediatamente depois de adicionar ou habilitar um ponto de extremidade no perfil. Um ponto de extremidade nesse estado é incluído em respostas DNS e pode receber tráfego. |
| Habilitado |Habilitado |Parada |O serviço de nuvem ou aplicativo Web para o qual o ponto de extremidade aponta não está em execução. Verifique as configurações do serviço de nuvem ou aplicativo Web. Um ponto de extremidade com um status Parado não é monitorado. Ele não é incluído em respostas DNS e não recebe tráfego. |

Para obter detalhes sobre como o status do monitor de ponto de extremidade é calculado para pontos de extremidade aninhados, veja [Perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).

### <a name="profile-monitor-status"></a>Status do monitor de perfil

O status do monitor de perfil é uma combinação dos valores do status do perfil configurado e do status do monitor de ponto de extremidade para todos os pontos de extremidade. Os valores possíveis estão descritos na tabela abaixo:

| Status do perfil (conforme configurado) | Status do monitor de ponto de extremidade | Status do monitor de perfil | Observações |
| --- | --- | --- | --- |
| Desabilitado |&lt;qualquer&gt; ou um perfil sem pontos de extremidade definidos. |Desabilitado |O perfil foi desabilitado. |
| Habilitado |O status de pelo menos um ponto de extremidade é Degradado. |Degradado |Revise os valores de status do ponto de extremidade individual para determinar quais pontos de extremidade exigem mais atenção. |
| Habilitado |O status de pelo menos um ponto de extremidade é Online. Nenhum ponto de extremidade tem o status Degradado. |Online |O serviço está aceitando tráfego. Nenhuma ação adicional é necessária. |
| Habilitado |O status de pelo menos um ponto de extremidade é CheckingEndpoint. Nenhum ponto de extremidade tem status Online ou Degradado. |Verificando pontos de extremidade |Esse estado de transição ocorre quando um perfil é criado ou habilitado. A integridade do ponto de extremidade está sendo verificada pela primeira vez. |
| Habilitado |O status de todos os pontos de extremidade no perfil é Desabilitado ou Parado, ou o perfil não tem nenhum ponto de extremidade definido. |Inativo |Nenhum ponto de extremidade está ativo, mas o perfil ainda está habilitado. |

## <a name="endpoint-failover-and-recovery"></a>Failover e recuperação do ponto de extremidade

O Gerenciador de Tráfego verifica periodicamente a integridade de cada ponto de extremidade, incluindo pontos de extremidade não íntegros. O Gerenciador de Tráfego detecta quando um ponto de extremidade se torna íntegro e coloca-o de volta em rotação.

> [!NOTE]
> O Gerenciador de Tráfego só considerará um ponto de extremidade como online se a mensagem de retorno for 200 OK. Um ponto de extremidade não está íntegro quando qualquer um dos seguintes eventos ocorrem:
>
> * Uma resposta diferente de 200 for recebida (incluindo um código 2xx diferente, ou um redirecionamento 301/302)
> * Solicitar a autenticação do cliente
> * Tempo limite (o tempo limite é de 10 segundos)
> * Não é possível se conectar
>
> Para obter mais informações sobre como solucionar problemas de verificações com falha, consulte [Solução de problemas de status degradado no Gerenciador de Tráfego do Azure](traffic-manager-troubleshooting-degraded.md).

A linha do tempo a seguir é uma descrição detalhada do processo de monitoramento.

![Sequência de failover e failback de ponto de extremidade do Gerenciador de Tráfego](./media/traffic-manager-monitoring/timeline.png)

1. **GET**. Para cada ponto de extremidade, o sistema de monitoramento do Gerenciador de Tráfego executa uma solicitação GET no caminho e arquivo especificados nas configurações de monitoramento.
2. **200 OK**. O sistema de monitoramento espera o retorno de uma mensagem HTTP 200 OK dentro de 10 segundos. Quando recebe essa resposta, ele reconhece que o serviço está disponível.
3. **30 segundos entre verificações**. A verificação de integridade do ponto de extremidade é repetida a cada 30 segundos.
4. **Serviço indisponível**. O serviço torna-se indisponível. O Gerenciador de Tráfego não saberá disso até a próxima verificação de integridade.
5. **Tentativas de acessar o arquivo de monitoramento (quatro tentativas)**. O sistema de monitoramento executa uma solicitação GET, mas não recebe uma resposta dentro do período de tempo limite de 10 segundos (outra opção é de uma resposta diferente de 200 ser recebida). Posteriormente, ele realiza mais três tentativas em intervalos de 30 segundos. Se uma das tentativas for bem-sucedida, o número de tentativas será redefinido.
6. **Status definido como Degradado**. Após a quarta falha consecutiva, o sistema de monitoramento marcará o status do ponto de extremidade não disponível como Degradado.
7. **Tráfego desviado para outros pontos de extremidade**. Os servidores de nome DNS do Gerenciador de Tráfego serão atualizados e o Gerenciador de Tráfego não retornará o ponto de extremidade em resposta às consultas DNS. Novas conexões serão direcionadas a outros pontos de extremidade disponíveis. No entanto, respostas DNS anteriores que incluem esse ponto de extremidade ainda poderão ser armazenadas em cache por servidores DNS recursivos e clientes DNS. Os clientes continuam a usar o ponto de extremidade até que o cache de DNS expire. Na medida em que o cache de DNS expira, os clientes fazem novas consultas DNS e são direcionados a pontos de extremidade diferentes. A duração do cache é controlada pela configuração de TTL no perfil do Gerenciador de Tráfego, por exemplo, 30 segundos.
8. **Continuação das verificações de integridade**. O Gerenciador de Tráfego continua verificando a integridade do ponto de extremidade enquanto ele está em estado Degradado. O Gerenciador de Tráfego detecta quando o ponto de extremidade volta a ser íntegro.
9. **Serviço volta a ficar online**. O serviço torna-se disponível. O ponto de extremidade mantém o status Degradado no Gerenciador de Tráfego até que o sistema de monitoramento execute sua próxima verificação de integridade.
10. **O tráfego para o serviço é retomado**. O Gerenciador de Tráfego envia uma solicitação GET e recebe uma resposta de status 200 OK. O serviço voltou ao estado íntegro. Os servidores de nome do Gerenciador de Tráfego são atualizados e começam a enviar o nome DNS do serviço nas respostas DNS. O tráfego retornará ao ponto de extremidade à medida que as respostas DNS em cache que retornam outros pontos de extremidade expirarem e suas conexões com outros pontos de extremidade forem encerradas.

> [!NOTE]
> Como o Gerenciador de Tráfego funciona no nível do DNS, ele não consegue influenciar as conexões existentes com qualquer ponto de extremidade. Ao direcionar o tráfego entre os pontos de extremidade (alterando as configurações de perfil ou durante um failover ou failback), o Gerenciador de Tráfego direcionará novas conexões aos pontos de extremidade disponíveis. No entanto, outros pontos de extremidade podem continuar recebendo tráfego por meio das conexões existentes, até que essas sessões sejam encerradas. Para permitir que o tráfego saia das conexões existentes, os aplicativos devem limitar a duração da sessão usada com cada ponto de extremidade.

## <a name="traffic-routing-methods"></a>Métodos de roteamento de tráfego

Quando um ponto de extremidade tem um status Degradado, ele não é retornado em resposta a consultas DNS. Em vez disso, um ponto de extremidade alternativo será escolhido e retornado. O método de roteamento de tráfego configurado no perfil determina como o ponto de extremidade alternativo é escolhido.

* **Prioridade**. Os pontos de extremidade formam uma lista priorizada. O primeiro ponto de extremidade disponível na lista sempre retorna. Se o status de um ponto de extremidade for Degradado, o próximo ponto de extremidade disponível será retornado.
* **Ponderado**. Qualquer ponto de extremidade disponível é escolhido aleatoriamente com base em seus pesos atribuídos e nos pesos de outros pontos de extremidade disponíveis.
* **Desempenho**. O ponto de extremidade mais próximo ao usuário final é retornado. Se esse ponto de extremidade não estiver disponível, um ponto de extremidade será escolhido aleatoriamente dentre todos os outros pontos de extremidade disponíveis. Escolher um ponto de extremidade aleatório evita uma falha em cascata que pode ocorrer quando o ponto de extremidade mais próximo fica sobrecarregado. Você pode configurar planos de failover alternativos para o roteamento de tráfego de desempenho usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).

Para obter mais informações, consulte [Métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md).

> [!NOTE]
> Uma exceção para o comportamento normal de roteamento de tráfego ocorre quando todos os pontos de extremidade qualificados têm um estado degradado. O Gerenciador de Tráfego faz uma tentativa do tipo “esforço possível” e *responde como se todos os pontos de extremidade com status Degradado na verdade estivessem online*. Esse comportamento é preferível à opção alternativa, que seria não retornar nenhum ponto de extremidade na resposta DNS. Pontos de extremidade com status Parado ou Desabilitado não são monitorados e, portanto, não são considerados qualificados para o tráfego.
>
> Essa condição geralmente é causada por uma configuração incorreta do serviço, como:
>
> * Uma ACL (lista de controle de acesso) bloqueando as verificações de integridade do Gerenciador de Tráfego
> * Uma configuração incorreta do caminho de monitoramento no perfil do Gerenciador de Tráfego
>
> A consequência deste comportamento é que quando as verificações de integridade do Gerenciador de Tráfego não estiverem configuradas corretamente, o roteamento de tráfego pode fazer parecer que o Gerenciador de Tráfego *está* funcionando corretamente. No entanto, neste caso, o failover do ponto de extremidade não acontece e isso afeta a disponibilidade geral do aplicativo. É importante verificar se o perfil mostra um status Online em vez de Degradado. Um status Online indica que as verificações de integridade do Gerenciador de Tráfego estão funcionando corretamente.

Para obter mais informações sobre como solucionar problemas de verificações de integridade com falha, consulte [Solução de problemas de status Degradado no Gerenciador de Tráfego do Azure](traffic-manager-troubleshooting-degraded.md).

## <a name="faq"></a>Perguntas frequentes

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>O Gerenciador de Tráfego é resistente a falhas de região do Azure?

O Gerenciador de Tráfego é um componente fundamental da entrega de aplicativos altamente disponíveis no Azure.
Para entregar alta disponibilidade, o Gerenciador de Tráfego deve ter um nível muito alto de disponibilidade e ser resiliente a falhas de região.

Por design, os componentes do Gerenciador de Tráfego são resilientes a uma falha completa de qualquer região do Azure. Essa resiliência se aplica a todos os componentes do Gerenciador de Tráfego: os servidores de nome DNS, a API, a camada de armazenamento e o serviço de monitoramento de ponto de extremidade.

No caso improvável de uma falha de uma região do Azure inteira, Gerenciador de Tráfego deve continuar a funcionar normalmente. Os aplicativos implantados em várias regiões do Azure podem contar com o Gerenciador de Tráfego para direcionar o tráfego para uma instância disponível de seu aplicativo.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Como a escolha da localização do grupo de recursos afeta o Gerenciador de Tráfego?

O Gerenciador de Tráfego é um serviço global único. Não é regional. A escolha do local do grupo de recursos não faz diferença para perfis do Gerenciador de Tráfego implantados nesse grupo de recursos.

O Azure Resource Manager exige que todos os grupos de recursos especifiquem uma localização, que determina a localização padrão para os recursos implantados nesse grupo de recursos. Quando você cria um perfil do Gerenciador de Tráfego, ele é criado em um grupo de recursos. Todos os perfis do Gerenciador de Tráfego usam **global** como a localização, substituindo o padrão do grupo de recursos.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Como determinar a integridade atual de cada ponto de extremidade?

O status de monitoramento atual de cada ponto de extremidade, bem como o perfil geral, é exibido no portal do Azure. Essas informações também estão disponíveis por meio da [API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) do Traffic Monitor, dos [cmdlets do PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) e da [CLI do Azure entre plataformas](../xplat-cli-install.md).

O Azure não fornece informações históricas sobre dados passados referentes à integridade do ponto de extremidade ou à capacidade de emitir alertas sobre alterações na integridade do ponto de extremidade.

### <a name="can-i-monitor-https-endpoints"></a>Posso monitorar os pontos de extremidade HTTPS?

Sim. O Gerenciador de Tráfego oferece suporte à investigação por HTTPS. Configure **HTTPS** como o protocolo na configuração de monitoramento.

O Gerenciador de Tráfego não pode fornecer nenhuma validação de certificado, incluindo:

* Certificados no lado do servidor não estão validados
* Certificados no lado do servidor SNI não estão validados
* Não há suporte para certificados de cliente

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Qual cabeçalho host as verificações de integridade do ponto de extremidade usam?

O Gerenciador de Tráfego usa cabeçalhos de host em verificações de integridade HTTP e HTTPS. O cabeçalho de host usado pelo Gerenciador de Tráfego é o nome do ponto de extremidade de destino configurado no perfil. O valor usado no cabeçalho do host não pode ser especificado separadamente da propriedade “target”.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quais são os endereços IP dos quais as verificações de integridade se originam?

A lista a seguir contém os endereços IP dos quais as verificações de integridade do Gerenciador de Tráfego podem se originar. Você pode usar esta lista para garantir que as conexões de entrada desses endereços IP sejam permitidas nos pontos de extremidades para verificar o status de integridade.

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203

## <a name="next-steps"></a>Próximas etapas

Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-how-traffic-manager-works.md)

Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego

Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-manage-profiles.md)

[Solucionar problemas de status Degradado](traffic-manager-troubleshooting-degraded.md) em um ponto de extremidade do Gerenciador de Tráfego



<!--HONumber=Nov16_HO3-->


