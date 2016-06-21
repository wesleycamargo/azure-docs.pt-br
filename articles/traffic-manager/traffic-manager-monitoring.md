<properties 
   pageTitle="Monitoramento e failover do ponto de extremidade do Gerenciador de Tráfego| Microsoft Azure"
   description="Este artigo ajudará você a entender como o Gerenciador de Tráfego usa o monitoramento de ponto de extremidade e o failover automático do ponto de extremidade para permitir que clientes do Azure implantem aplicativos de alta disponibilidade"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/04/2016"
   ms.author="jtuliani" />

# Monitoramento e failover do ponto de extremidade do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure inclui monitoramento de ponto de extremidade interno e failover automático de ponto de extremidade. Isso permite que você forneça aplicativos de alta disponibilidade resilientes a falha de ponto de extremidade, incluindo falhas de região do Azure.

Ele faz isso com solicitações regulares a cada ponto de extremidade e verificando a resposta. Se um ponto de extremidade falha em fornecer uma resposta válida, ele é marcado como “Degradado” e não é mais incluído nas respostas DNS que, por sua vez, retornam um ponto de extremidade alternativo disponível. O tráfego de usuário final é direcionado para longe dos pontos de extremidade com falha e em direção daqueles que estão disponíveis.

As verificações de integridade do ponto de extremidade continuam para pontos de extremidade “Degradados”, para que possam voltar ao rodízio automaticamente quando ficarem íntegros novamente.

## Configuração do monitoramento do ponto de extremidade

Para configurar o monitoramento de ponto de extremidade, é necessário especificar as configurações a seguir em seu perfil do Gerenciador de Tráfego:

- **Protocolo** – escolha HTTP ou HTTPS. É importante observar que o monitoramento HTTPS não verifica se o seu certificado SSL é válido, ele apenas verifica se tal certificado está presente.
- **Porta** – escolha a porta usada para a solicitação. Portas HTTP e HTTPS Padrão estão entre as opções.
- **Caminho** – Forneça o caminho relativo e o nome da página da Web ou arquivo que a verificação de integridade do monitoramento tentará acessar. Observe que a barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão).

Para verificar a integridade de cada ponto de extremidade, o Gerenciador de Tráfego fará uma solicitação GET ao ponto de extremidade usando o protocolo, a porta e o caminho relativo fornecido.

Uma prática comum é implementar uma página personalizada dentro de seu aplicativo, por exemplo, “/health.aspx”, e configurar essa página como o caminho de monitoramento de ponto de extremidade do Gerenciador de Tráfego. Dentro dessa página, você pode executar quaisquer verificações necessárias específicas ao aplicativo, como a verificação da disponibilidade de um banco de dados back-end, antes de retornar HTTP 200 (se o serviço estiver íntegro) ou, caso contrário, um código de status diferente.

As configurações de monitoramento de ponto de extremidade são definidas no nível do perfil do Gerenciador de Tráfego, não de acordo com o ponto de extremidade. Portanto, as mesmas configurações são usadas para verificar a integridade de todos os pontos de extremidade. Se você precisar usar configurações de monitoramento diferentes para pontos de extremidade diferentes, isso poderá ser alcançado usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## Status do ponto de extremidade e do perfil

Os perfis e pontos de extremidade do Gerenciador de Tráfego podem ser habilitados e desabilitados pelo usuário. As mudanças no status também podem ocorrer como resultado de verificações de integridade do ponto de extremidade. Os parâmetros a seguir descrevem o comportamento de forma detalhada.

### Status do ponto de extremidade

O Status do ponto de extremidade é uma configuração controlada pelo usuário, permitindo que um ponto de extremidade individual seja habilitado ou desabilitado com facilidade. Isso não afeta o status do serviço subjacente (que ainda pode estar em execução), em vez disso, ele controla a disponibilidade deste ponto de extremidade a partir de uma perspectiva do Gerenciador de Tráfego. Quando um ponto de extremidade estiver desabilitado, sua integridade não será verificada e ele não retornará em uma resposta DNS.

### Status do perfil

O Status do perfil é uma configuração controlada pelo usuário, permitindo que o perfil seja habilitado ou desabilitado com facilidade. Enquanto o Status do Ponto de Extremidade afeta um único ponto de extremidade, o Status do Perfil afeta todo o perfil, incluindo todos os pontos de extremidade. Quando estão desabilitado, os pontos de extremidade não passam pela verificação de integridade e não retornam na resposta DNS (em vez disso, as consultas DNS recebem respostas “NXDOMAIN”).

### Status do monitor de ponto de extremidade

O Status do Monitor de Ponto de Extremidade é uma configuração gerada pelo Gerenciador de Tráfego e não pode ser definido pelo usuário. Ele mostra o status atual do ponto de extremidade, refletindo o monitoramento contínuo do ponto de extremidade e também outras informações como o Status do Ponto de Extremidade. Os valores possíveis para o Status do Monitor de Ponto de Extremidade estão na tabela abaixo. (Para obter detalhes de como o Status do Monitor de Ponto de Extremidade é calculado para pontos de extremidade aninhados, confira [Perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).)

|Status do perfil|Status do ponto de extremidade|Status do monitor de ponto de extremidade (API e Portal)|Observações|
|---|---|---|---|
|Desabilitado|Habilitado|Inativo|O perfil foi desabilitado pelo usuário. Embora o Status do Ponto de Extremidade ainda possa ser “Habilitado”, o Status do Perfil tem precedência. Pontos de extremidade em perfis Desabilitados não são monitorados. Nenhum ponto de extremidade retorna nas respostas DNS (em vez disso, a resposta “NXDOMAIN” é fornecida).|
|&lt;qualquer&gt;|Desabilitado|Desabilitado|O ponto de extremidade foi desabilitado pelo usuário. Pontos de extremidade desabilitados não são monitorados. Eles não ficam disponíveis para inclusão nas respostas DNS e, por isso, não recebem tráfego.|
|Habilitado|Habilitado|Online|O ponto de extremidade é monitorado e está íntegro. Ele está disponível para inclusão nas respostas DNS e, por isso, pode receber tráfego.|
|Habilitado|Habilitado|Degradado|As verificações de integridade de monitoramento do ponto de extremidade estão falhando. O ponto de extremidade não fica disponível para inclusão em respostas DNS e, por isso, não recebem tráfego.|
|Habilitado|Habilitado|Verificando ponto de extremidade|O ponto de extremidade é monitorado, mas os resultados da primeira investigação ainda não foram recebidos. Esse é um estado temporário que ocorre logo após você ter adicionado um novo ponto de extremidade ao perfil, ou logo após ter habilitado um ponto de extremidade ou perfil. Os pontos de extremidade nesse estado ficam disponíveis para inclusão nas respostas DNS e, por isso, podem receber tráfego.|
|Habilitado|Habilitado|Parada|O Serviço de Nuvem ou Aplicativo Web para o qual o ponto de extremidade aponta não está em execução. Verifique as configurações de Serviço de Nuvem ou Aplicativo Web. Pontos de extremidade parados não são monitorados. Eles não ficam disponíveis para inclusão nas respostas DNS e, por isso, não recebem tráfego.|

### Status do Monitor de Perfil

Status do Monitor de Perfil é uma combinação do status do monitor de ponto de extremidade de todos os pontos de extremidade no perfil, e do status de seu perfil configurado. Os valores possíveis estão descritos na tabela abaixo:

|Status do perfil (conforme configurado)|Status do monitor de ponto de extremidade|Status do Monitor de Perfil (API e Portal)|Observações|
|---|---|---|---|
|Desabilitado|&lt;qualquer&gt; ou um perfil sem pontos de extremidade definidos.|Desabilitado|O perfil foi desabilitado pelo usuário.|
|Habilitado|O status de pelo menos um ponto de extremidade é "Degradado".|Degradado|Isso é um sinalizador de que ação do cliente é necessária. Revise os valores de status do ponto de extremidade individual para determinar quais pontos de extremidade exigem mais atenção.|
|Habilitado|O status de pelo menos um ponto de extremidade é "Online". Nenhum ponto de extremidade está "Degradado".|Online|O serviço está aceitando tráfego e ação do cliente não é necessária.|
|Habilitado|O status de pelo menos um ponto de extremidade é "Verificando ponto de extremidade". Nenhum ponto de extremidade está "Online" ou "Degradado".|Verificando pontos de extremidade|Estado de transição. Isso ocorre normalmente quando um perfil tiver acabado de ser criado ou habilitado, e a integridade do ponto de extremidade estiver sendo verificada pela primeira vez.|
|Habilitado|O status de todos os pontos de extremidade definidos no perfil é "Desabilitado" ou "Parado", ou o perfil não tem nenhum ponto de extremidade definido.|Inativo|Nenhum ponto de extremidade está ativo, mas o perfil ainda está habilitado.|

## Failover e failback do ponto de extremidade

Considere o cenário no qual ocorre a falha de um ponto de extremidade do Gerenciador de Tráfego que estava íntegro. Essa falha é detectada pelo Gerenciador de Tráfego e o ponto de extremidade é retirado do rodízio. Depois, o ponto de extremidade se recupera, isso também é detectado pelo Gerenciador de Tráfego, e o ponto de extremidade volta para o rodízio.

>[AZURE.NOTE] O Gerenciador de Tráfego só considera um ponto de extremidade como estando Online se a mensagem de retorno for uma 200 OK. Se qualquer uma das situações a seguir ocorrer, isso será contado como uma falha de verificação:

>- Uma resposta diferente de 200 for recebida (incluindo um código 2xx diferente, ou um redirecionamento 301/302)
>- Solicitar a autenticação do cliente
>- Tempo limite (o tempo limite é de 10 segundos)
>- Não é possível se conectar

>Para obter mais detalhes sobre como solucionar problemas de verificações com falha, consulte [Solução de problemas de status degradado no Gerenciador de Tráfego do Azure](traffic-manager-troubleshooting-degraded.md).

O cronograma a seguir descreve em detalhes a sequência de etapas.

![Sequência de failover e failback de ponto de extremidade do Gerenciador de Tráfego](./media/traffic-manager-monitoring/timeline.png)

1. **GET** – o sistema de monitoramento do Gerenciador de Tráfego executa um GET no caminho e arquivo especificado nas configurações de monitoramento. Isso é repetido para cada ponto de extremidade.
2. **200 OK** – O sistema de monitoramento espera o retorno de uma mensagem HTTP 200 OK dentro de 10 segundos. Quando ele recebe essa resposta, entende que o serviço está disponível.
3. **30 segundos entre verificações** – A verificação de integridade do ponto de extremidade será repetida a cada 30 segundos.
4. **Serviço indisponível** – O serviço torna-se indisponível. O Gerenciador de Tráfego não saberá disso até a próxima verificação de integridade.
5. **Tentativas de acessar o arquivo de monitoramento (quatro tentativas)** – O sistema de monitoramento executa uma operação GET, mas não recebe uma resposta dentro do período de tempo limite de 10 segundos (Talvez uma resposta 200 seja recebida). Então, ele executa mais três tentativas em intervalos de 30 segundos. Se uma das tentativas for bem-sucedida, o número de tentativas será redefinido.
6. **Marcado como degradado** – Após a quarta falha consecutiva, o sistema de monitoramento marcará o ponto de extremidade indisponível como Degradado. 
7. **O tráfego é desviado para outros pontos de extremidade** – Os servidores de nome DNS do Gerenciador de Tráfego são atualizados, e o ponto de extremidade não retornará mais no Gerenciador de tráfego em resposta às consultas DNS. Portanto, novas conexões serão direcionadas a outros pontos de extremidade disponíveis. No entanto, as respostas DNS anteriores que incluem esse ponto de extremidade ainda podem ser armazenadas em cache por servidores DNS recursivos e clientes DNS, fazendo com que alguns clientes tentem se conectar a esse ponto de extremidade. À medida que esses caches expirarem, os clientes farão novas consultas DNS e serão direcionados a pontos de extremidade diferentes. A duração do cache é controlada pela configuração de TTL no perfil do Gerenciador de Tráfego, por exemplo, 30 segundos. 
8. **Continuação das verificações de integridade** - O Gerenciador de Tráfego continua a verificar a integridade do ponto de extremidade enquanto ele está em estado Degradado. Isso ocorre para que ele possa detectar quando o ponto de extremidade retorna a um estado íntegro.
9. **O serviço volta a ficar online** – O serviço fica disponível. O ponto de extremidade permanece Degradado no Gerenciador de Tráfego até que o sistema de monitoramento execute sua próxima verificação de integridade.
10. **O tráfego é retomado no serviço** - O Gerenciador de Tráfego envia uma operação GET e recebe um 200 OK, indicando que o serviço retornou a um estado íntegro. Os servidores de nome do Gerenciador de Tráfego são atualizados novamente, e começam a enviar o nome DNS do serviço nas respostas DNS. O tráfego retornará ao ponto de extremidade novamente à medida que as respostas de DNS em cache que retornam outros pontos de extremidade expirarem, e suas conexões com outros pontos de extremidade sejam encerradas.

>[AZURE.NOTE] Como o Gerenciador de Tráfego funciona no nível do DNS, ele não consegue influenciar as conexões existentes com qualquer ponto de extremidade. Ao direcionar o tráfego entre os pontos de extremidade (alterando as configurações de perfil ou durante um failover ou failback), o Gerenciador de Tráfego direcionará novas conexões aos pontos de extremidade disponíveis. No entanto, outros pontos de extremidade podem continuar recebendo tráfego por meio das conexões existentes, até que essas sessões sejam encerradas. Para permitir que o tráfego saia das conexões existentes, os aplicativos devem limitar a duração da sessão usada com cada ponto de extremidade.

## Pontos de extremidade degradados

Quando um ponto de extremidade fica degradado, ele não retorna mais na resposta às consultas DNS (para conhecer uma exceção a essa regra, confira a Nota abaixo). Em vez disso, um ponto de extremidade alternativa é escolhida e retornada. A opção de ponto de extremidade alternativo depende do método de roteamento de tráfego configurado:

- **Prioridade** – Os pontos de extremidade formam uma lista de prioridade. O primeiro ponto de extremidade disponível na lista sempre retorna. Se ele ficar Degradado, o próximo ponto de extremidade disponível retornará.
- **Ponderado** – Qualquer um dos pontos de extremidade disponíveis podem retornar, e são escolhidos aleatoriamente com base em seus pesos atribuídos e nos pesos de outros pontos de extremidade disponíveis. Se um ponto de extremidade fica degradado, um ponto de extremidade é escolhido do grupo restante de pontos de extremidade disponíveis.
- **Desempenho** – O ponto de extremidade “mais próximo” ao usuário final será retornado (excluindo os pontos de extremidade desativados/parados). Se esse ponto de extremidade ficar indisponível, o ponto de extremidade retornado será escolhido aleatoriamente dentre todos os outros pontos de extremidade disponíveis. (Isso é para ajudar a evitar uma potencial falha em cascata que pode ocorrer se o ponto de extremidade mais próximo ficar sobrecarregado. Os planos de failover alternativos para o roteamento de tráfego de Desempenho podem ser configurados usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).)

Para saber mais, confira [Métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md).

>[AZURE.NOTE] O que acontece se todos os pontos de extremidade do Gerenciador de Tráfego (excluindo pontos de extremidade “Desabilitados” ou “Parados”) estiverem sendo reprovados nas verificações de integridade e marcados como “Degradados”?

>Isso ocorre normalmente devido a um erro na configuração do serviço (como um ACL bloqueando as verificações de integridade do Gerenciador de Tráfego) ou a um erro na configuração do perfil do Gerenciador de Tráfego (como um caminho de monitoramento incorreto).

>Nesse caso, o Gerenciador de Tráfego faz um “esforço possível” e *responde como se todos os pontos de extremidade degradados estivessem “Online”*. Isso é preferível à opção alternativa, que seria não retornar qualquer ponto de extremidade na resposta DNS.

>Uma consequência deste comportamento é que quando as verificações de integridade do Gerenciador de Tráfego não são configuradas corretamente, o Gerenciador de Tráfego é visto no roteamento de tráfego como se estivesse funcionando corretamente. No entanto, o failover do ponto de extremidade não acontecerá se um ponto de extremidade falhar, afetando a disponibilidade geral do aplicativo. Para garantir que isso não ocorra, é importante assegurar que o perfil mostre o estado “Online” em vez de o estado “Degradado”. Um estado “Online” mostra que as verificações de integridade do Gerenciador de Tráfego estão funcionando corretamente.

Para obter mais detalhes sobre como solucionar problemas de verificações de integridade com falha, consulte [Solução de problemas de status degradado no Gerenciador de Tráfego do Azure](traffic-manager-troubleshooting-degraded.md)
 
## Perguntas frequentes

### O Gerenciador de tráfego é resistente a falhas de região do Azure?

Com verificações de integridade internas e failover automático entre regiões, o Gerenciador de Tráfego do Azure é um componente fundamental no fornecimento de aplicativos altamente disponíveis no Azure, incluindo aplicativos resilientes a uma falha do Azure em toda região.

Dessa forma, reconhecemos que o próprio Gerenciador de Tráfego deve oferecer um nível excepcionalmente alto de disponibilidade, incluindo resiliência à falha regional.

Embora algumas partes do Gerenciador de Tráfego sejam executadas no Azure, elas são distribuídas entre regiões e foram desenvolvidas a fim de suportarem uma falha completa de qualquer região do Azure. Essa resiliência se aplica a todos os componentes do Gerenciador de Tráfego: os servidores de nome DNS, a API, a camada de armazenamento e o serviço de monitoramento de ponto de extremidade.

Portanto, mesmo no evento improvável de uma falha total de toda uma região do Azure, o Gerenciador de Tráfego deve continuar funcionando normalmente, e os clientes com aplicativos implantados em várias regiões do Azure podem depender do Gerenciador de Tráfego a fim de direcionar o tráfego para uma instância disponível de seus aplicativos.

### Como a escolha do local do Grupo de Recursos afeta o Gerenciador de Tráfego?

O Gerenciador de Tráfego é um serviço global único. Não é regional. A escolha do local do grupo de recursos não faz diferença para perfis do Gerenciador de Tráfego implantados nesse grupo de recursos.

O Azure Resource Manager exige que todos os grupos de recursos especifiquem um “local”, que determina o local padrão para recursos implantados nesse grupo de recursos. Você receberá uma solicitação por esse local se criar um novo grupo de recursos durante a criação de um perfil do Gerenciador de Tráfego por meio do Portal do Azure.

Todos os perfis do Gerenciador de Tráfego usam "global" como o local. Como esse é o único valor aceito, ele não será exposto das experiências do Portal do Azure, do PowerShell ou da CLI. Isso substitui o grupo de recursos padrão.

### Como determinar a integridade atual de cada ponto de extremidade?

O status de monitoramento atual de cada ponto de extremidade, e o perfil geral, é exibido no Portal de Gerenciamento do Azure. Essas informações também estão disponíveis por meio da [API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) do Gerenciador de Tráfego, dos [cmdlets do PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) e da [CLI do Azure entre plataformas](../xplat-cli-install.md).

No momento, não é possível exibir informações históricas sobre a integridade anterior do ponto de extremidade, nem é possível configurar alertas sobre alterações na integridade do ponto de extremidade.

### Posso monitorar os pontos de extremidade HTTPS?

Sim. O Gerenciador de Tráfego oferece suporte à investigação por HTTPS. Basta configurar “HTTPS” como o protocolo na configuração de monitoramento. Observe que:

- O certificado no lado do servidor não está validado
- Certificados no lado do servidor SNI não estão validados
- Não há suporte para certificados de cliente

### Qual cabeçalho “Host” é usado para verificações de integridade do ponto de extremidade?

O cabeçalho “host” usado nas verificações de integridade HTTP/S é o nome DNS associado a cada ponto de extremidade. Isso é exposto como o ponto de extremidade de “destino” no Portal do Azure, nos [cmdlets do PowerShell](https://msdn.microsoft.com/library/mt125941.aspx), na [CLI do Azure entre plataformas](../xplat-cli-install.md) e na [API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx).

Este valor faz parte da configuração do ponto de extremidade. O valor usado no cabeçalho do Host não pode ser especificado separadamente da propriedade “target”.


## Próximas etapas

Saiba [Como funciona o Gerenciador de Tráfego](traffic-manager-how-traffic-manager-works.md).

Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) com suporte do Gerenciador de Tráfego.

Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-manage-profiles.md).

[Solucionar problemas de status “Degradado”](traffic-manager-troubleshooting-degraded.md) em um ponto de extremidade do Gerenciador de Tráfego.
 

<!---HONumber=AcomDC_0608_2016-->