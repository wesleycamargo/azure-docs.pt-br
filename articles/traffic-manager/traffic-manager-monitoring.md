<properties
   pageTitle="Monitoramento e failover do ponto de extremidade do Gerenciador de Tráfego| Microsoft Azure"
   description="Este artigo pode ajudar você a entender como o Gerenciador de Tráfego usa o monitoramento de ponto de extremidade e o failover automático do ponto de extremidade para ajudar clientes do Azure a implantarem aplicativos de alta disponibilidade"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/01/2016"
   ms.author="sewhee" />

# Monitoramento e failover do ponto de extremidade do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure inclui monitoramento de ponto de extremidade interno e failover automático de ponto de extremidade. Este recurso ajuda você a fornecer aplicativos de alta disponibilidade resilientes a falhas de ponto de extremidade, incluindo falhas de região do Azure.

O Gerenciador de Tráfego funciona fazendo solicitações regulares a cada ponto de extremidade e verificando a resposta. Se um ponto de extremidade não fornecer uma resposta válida, o Gerenciador de Tráfego mostrará seu status como Degradado. Ele deixará de ser incluído nas respostas DNS, que passarão a retornar um ponto de extremidade alternativo, disponível. Dessa forma, o tráfego de usuários é direcionado para longe dos pontos de extremidade com falha e na direção daqueles que estão disponíveis.

As verificações de integridade dos pontos de extremidade degradados continuam para que eles possam voltar ao rodízio automaticamente quando ficarem íntegros novamente.

## Configurar o monitoramento de pontos de extremidade

Para configurar o monitoramento de ponto de extremidade, é necessário especificar as configurações a seguir em seu perfil do Gerenciador de Tráfego:

- **Protocolo**. Escolha HTTP ou HTTPS. É importante observar que o monitoramento HTTPS não verifica se o seu certificado SSL é válido, ele apenas verifica se o certificado está presente.
- **Porta**. Escolha a porta usada para a solicitação. Portas HTTP e HTTPS Padrão estão entre as opções.
- **Caminho**. Forneça o caminho relativo e o nome da página da Web ou arquivo que a verificação de integridade do monitoramento tentará acessar. Observe que uma barra para frente (/) é uma entrada válida para o caminho relativo e isso implica que o arquivo está no diretório raiz (padrão).

Para verificar a integridade de cada ponto de extremidade, o Gerenciador de Tráfego faz uma solicitação GET ao ponto de extremidade usando o protocolo, a porta e o caminho relativo fornecido.

Uma prática comum é implementar uma página personalizada dentro de seu aplicativo, por exemplo, /health.aspx. Configure essa página para ser o caminho de monitoramento de pontos de extremidade do Gerenciador de Tráfego. Dentro dessa página, você pode executar quaisquer verificações necessárias específicas ao aplicativo, como a verificação da disponibilidade de um banco de dados, antes de retornar HTTP 200 (se o serviço estiver íntegro) ou, caso contrário, um código de status diferente.

As configurações de monitoramento de ponto de extremidade são definidas no nível do perfil do Gerenciador de Tráfego, não de acordo com o ponto de extremidade. Portanto, as mesmas configurações são usadas para verificar a integridade de todos os pontos de extremidade. Se precisar usar configurações de monitoramento diferentes para pontos de extremidade diferentes, você poderá fazer isso usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## Status do ponto de extremidade e do perfil

Você pode habilitar e desabilitar pontos de extremidade e perfis do Gerenciador de Tráfego. No entanto, uma alteração no status do ponto de extremidade também pode ocorrer como resultado das configurações e processos automatizados do Gerenciador de Tráfego. Os parâmetros a seguir descrevem como isso funciona.

### Status do ponto de extremidade

O status do ponto de extremidade é uma configuração controlada pelo usuário, segundo a qual você pode facilmente habilitar ou desabilitar um ponto de extremidade específico. Isso não afeta o status do serviço subjacente (que ainda pode estar em execução). Em vez disso, a disponibilidade de um ponto de extremidade específico é controlada da perspectiva do Gerenciador de Tráfego. Quando o status de um ponto de extremidade for Desabilitado, o Gerenciador de Tráfego não verificará sua integridade e o ponto de extremidade não será incluído em uma resposta DNS.

### Status do perfil

O status do perfil é uma configuração controlada pelo usuário. Você pode usá-la para habilitar ou desabilitar um perfil facilmente. Embora o status do ponto de extremidade afete um único ponto de extremidade, o status do perfil afeta todo o perfil, incluindo todos os pontos de extremidade. Os pontos de extremidade de um perfil com o status Desabilitado não serão verificados quanto à integridade e nenhum ponto de extremidade será incluído em uma resposta DNS (o valor retornado é uma resposta NXDOMAIN).

### Status do monitor de ponto de extremidade

O status do monitor do ponto de extremidade é uma configuração gerada pelo Gerenciador de Tráfego que mostra o status atual do ponto de extremidade. Você não pode alterar essa configuração manualmente. O status do monitor do ponto de extremidade reflete o monitoramento contínuo do ponto de extremidade, além de outras informações como o status do ponto de extremidade. Os valores possíveis de status do monitor do ponto de extremidade estão na tabela abaixo. (Para obter detalhes sobre como o status do monitor de ponto de extremidade é calculado para pontos de extremidade aninhados, confira [Perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md).)

|Status do perfil|Status do ponto de extremidade|Status do monitor do ponto de extremidade (API e Portal)|Observações|
|---|---|---|---|
|Desabilitado|Habilitado|Inativo|O perfil foi desabilitado pelo usuário. Embora o status do ponto de extremidade ainda possa ser Habilitado, o status do perfil (Desabilitado) tem precedência. Os pontos de extremidade em perfis desabilitados não são monitorados, e nenhum ponto de extremidade é incluído nas respostas DNS (o valor retornado é uma resposta NXDOMAIN).|
|&lt;qualquer&gt;|Desabilitado|Desabilitado|O ponto de extremidade foi desabilitado pelo usuário. Um ponto de extremidade com um status Desabilitado não é monitorado. Ele não fica disponível para ser incluído em respostas DNS e, portanto, não recebe tráfego.|
|Habilitado|Habilitado|Online|O ponto de extremidade é monitorado e está íntegro. Ele fica disponível para ser incluído em respostas DNS e, portanto, pode receber tráfego.|
|Habilitado|Habilitado|Degradado|As verificações de integridade de monitoramento do ponto de extremidade estão falhando. O ponto de extremidade não fica disponível para ser incluído em respostas DNS e, portanto, não recebe tráfego.|
|Habilitado|Habilitado|Verificando ponto de extremidade|O ponto de extremidade é monitorado, mas os resultados da primeira investigação ainda não foram recebidos. Esse é um estado temporário que normalmente ocorre logo após você ter adicionado um novo ponto de extremidade ao perfil, ou logo após ter habilitado um ponto de extremidade ou perfil. Um ponto de extremidade nesse estado fica disponível para ser incluído em respostas DNS e, portanto, pode receber tráfego.|
|Habilitado|Habilitado|Parada|O serviço de nuvem ou aplicativo Web para o qual o ponto de extremidade aponta não está em execução. Verifique as configurações do serviço de nuvem ou aplicativo Web. Um ponto de extremidade com um status Parado não é monitorado. Ele não fica disponível para ser incluído em respostas DNS e, portanto, não recebe tráfego.|

### Status do monitor de perfil

O Status do monitor de perfil é uma combinação dos valores do status do monitor de ponto de extremidade para todos os pontos de extremidade no perfil, e do status de seu perfil configurado. Os valores possíveis estão descritos na tabela abaixo.

|Status do perfil (conforme configurado)|Status do monitor de ponto de extremidade|Status do monitor de perfil (API e Portal)|Observações|
|---|---|---|---|
|Desabilitado|&lt;qualquer&gt; ou um perfil sem pontos de extremidade definidos.|Desabilitado|O perfil foi desabilitado pelo usuário.|
|Habilitado|O status de pelo menos um ponto de extremidade é Degradado.|Degradado|Isso é um sinalizador de que ação do cliente é necessária. Revise os valores de status do ponto de extremidade individual para determinar quais pontos de extremidade exigem mais atenção.|
|Habilitado|O status de pelo menos um ponto de extremidade é Online. Nenhum ponto de extremidade tem o status Degradado.|Online|O serviço está aceitando tráfego e ação do cliente não é necessária.|
|Habilitado|O status de pelo menos um ponto de extremidade é CheckingEndpoint. Nenhum ponto de extremidade tem status Online ou Degradado.|Verificando pontos de extremidade|Estado de transição. Isso ocorre normalmente quando um perfil tiver acabado de ser criado ou habilitado, e a integridade do ponto de extremidade estiver sendo verificada pela primeira vez.|
|Habilitado|O status de todos os pontos de extremidade definidos no perfil é Desabilitado ou Parado, ou o perfil não tem nenhum ponto de extremidade definido.|Inativo|Nenhum ponto de extremidade está ativo, mas o perfil ainda está habilitado.|

## Failover e failback do ponto de extremidade

Considere um cenário no qual um ponto de extremidade que era íntegro falha. O Gerenciador de Tráfego detecta a falha e o ponto de extremidade é retirado da rotação. Posteriormente, o ponto de extremidade se recupera. Gerenciador de Tráfego detecta a recuperação e o ponto de extremidade volta à rotação.

>[AZURE.NOTE] O Gerenciador de Tráfego só considerará um ponto de extremidade como online se a mensagem de retorno for 200 OK. Se qualquer uma das situações a seguir ocorrer, o Gerenciador de Tráfego considerará a verificação de integridade do ponto de extremidade com falha:

>- Uma resposta diferente de 200 for recebida (incluindo um código 2xx diferente, ou um redirecionamento 301/302)
>- Solicitar a autenticação do cliente
>- Tempo limite (o tempo limite é de 10 segundos)
>- Não é possível se conectar

>Para obter mais informações sobre como solucionar problemas de verificações com falha, consulte [Solução de problemas de status degradado no Gerenciador de Tráfego do Azure](traffic-manager-troubleshooting-degraded.md).

O cronograma a seguir descreve em detalhes a sequência de etapas.

![Sequência de failover e failback de ponto de extremidade do Gerenciador de Tráfego](./media/traffic-manager-monitoring/timeline.png)

1. **GET**. O sistema de monitoramento do Gerenciador de Tráfego executa uma solicitação GET no caminho e arquivo especificado nas configurações de monitoramento. Isso é repetido para cada ponto de extremidade.
2. **200 OK**. O sistema de monitoramento espera o retorno de uma mensagem HTTP 200 OK dentro de 10 segundos. Quando recebe essa resposta, ele reconhece que o serviço está disponível.
3. **30 segundos entre verificações**. A verificação de integridade do ponto de extremidade será repetida a cada 30 segundos.
4. **Serviço indisponível**. O serviço torna-se indisponível. O Gerenciador de Tráfego não saberá disso até a próxima verificação de integridade.
5. **Tentativas de acessar o arquivo de monitoramento (quatro tentativas)**. O sistema de monitoramento executa uma solicitação GET, mas não recebe uma resposta dentro do período de tempo limite de 10 segundos (outra opção é de uma resposta diferente de 200 ser recebida). Posteriormente, ele realiza mais três tentativas em intervalos de 30 segundos. Se uma das tentativas for bem-sucedida, o número de tentativas será redefinido.
6. **Status definido como Degradado**. Após a quarta falha consecutiva, o sistema de monitoramento marcará o status do ponto de extremidade não disponível como Degradado.
7. **Tráfego desviado para outros pontos de extremidade**. Os servidores de nome DNS do Gerenciador de Tráfego serão atualizados e o Gerenciador de Tráfego não retornará o ponto de extremidade em resposta às consultas DNS. Novas conexões serão direcionadas a outros pontos de extremidade disponíveis. No entanto, respostas DNS anteriores que incluem esse ponto de extremidade ainda poderão ser armazenadas em cache por servidores DNS recursivos e clientes DNS. Isso faria com que alguns clientes tentassem se conectar a esse ponto de extremidade. À medida que esses caches expirarem, os clientes farão novas consultas DNS e serão direcionados a pontos de extremidade diferentes. A duração do cache é controlada pela configuração de TTL no perfil do Gerenciador de Tráfego, por exemplo, 30 segundos.
8. **Continuação das verificações de integridade**. O Gerenciador de Tráfego continua verificando a integridade do ponto de extremidade enquanto ele está em estado Degradado. Isso ocorre para que ele possa detectar quando o ponto de extremidade retorna a um estado íntegro.
9. **Serviço volta a ficar online**. O serviço torna-se disponível. O ponto de extremidade mantém o status Degradado no Gerenciador de Tráfego até que o sistema de monitoramento execute sua próxima verificação de integridade.
10. **O tráfego para o serviço é retomado**. O Gerenciador de Tráfego envia uma solicitação GET e recebe uma resposta de status 200 OK. Isso indica que o serviço voltou ao estado íntegro. Os servidores de nome do Gerenciador de Tráfego são atualizados e começam a enviar o nome DNS do serviço nas respostas DNS. O tráfego retornará ao ponto de extremidade à medida que as respostas DNS em cache que retornam outros pontos de extremidade expirarem e suas conexões com outros pontos de extremidade forem encerradas.

>[AZURE.NOTE] Como o Gerenciador de Tráfego funciona no nível do DNS, ele não consegue influenciar as conexões existentes com qualquer ponto de extremidade. Ao direcionar o tráfego entre os pontos de extremidade (alterando as configurações de perfil ou durante um failover ou failback), o Gerenciador de Tráfego direcionará novas conexões aos pontos de extremidade disponíveis. No entanto, outros pontos de extremidade podem continuar recebendo tráfego por meio das conexões existentes, até que essas sessões sejam encerradas. Para permitir que o tráfego saia das conexões existentes, os aplicativos devem limitar a duração da sessão usada com cada ponto de extremidade.

## Pontos de extremidade degradados

Quando um ponto de extremidade tiver o status Degradado, ele deixará de ser retornado na resposta às consultas DNS (para ver uma exceção a essa regra, confira a nota no final desta seção). Em vez disso, um ponto de extremidade alternativo será escolhido e retornado. A opção de ponto de extremidade alternativo depende do método de roteamento de tráfego configurado:

- **Prioridade**. Os pontos de extremidade formam uma lista priorizada. O primeiro ponto de extremidade disponível na lista sempre retorna. Se o status de um ponto de extremidade for Degradado, o próximo ponto de extremidade disponível será retornado.
- **Ponderado**. Qualquer um dos pontos de extremidade disponíveis podem ser retornado e são escolhidos aleatoriamente com base em seus pesos atribuídos e nos pesos de outros pontos de extremidade disponíveis. Se o status de um ponto de extremidade for Degradado, um ponto de extremidade será escolhido do grupo restante de pontos de extremidade disponíveis.
- **Desempenho**. O ponto de extremidade mais próximo ao usuário final será retornado (excluindo os pontos de extremidade com status Desabilitado ou Parado). Se esse ponto de extremidade ficar indisponível, o ponto de extremidade retornado será escolhido aleatoriamente dentre todos os outros pontos de extremidade disponíveis. (Isso é para ajudar a evitar uma potencial falha em cascata que pode ocorrer se o ponto de extremidade mais próximo ficar sobrecarregado. Você pode configurar planos de failover alternativos para o roteamento de tráfego de desempenho usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).)

Para obter mais informações, consulte [Métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md).

>[AZURE.NOTE] O que acontecerá se todos os pontos de extremidade do Gerenciador de Tráfego (excluindo pontos de extremidade com status Desabilitado ou Parado) estiverem sendo reprovados nas verificações de integridade e exibindo o status Degradado?

>Isso normalmente ocorre devido a um erro na configuração do serviço (como uma ACL [lista de controle de acesso] bloqueando as verificações de integridade do Gerenciador de Tráfego) ou a um erro na configuração do perfil do Gerenciador de Tráfego (como um caminho de monitoramento incorreto).

>Nesse caso, o Gerenciador de Tráfego faz uma tentativa do tipo “esforço possível” e *responde como se todos os pontos de extremidade com status Degradado na verdade estivessem online*. Isso é preferível à opção alternativa, que seria não retornar nenhum ponto de extremidade na resposta DNS.

>Uma consequência deste comportamento é que quando as verificações de integridade do Gerenciador de Tráfego não estiverem configuradas corretamente, o roteamento de tráfego pode fazer parecer que o Gerenciador de Tráfego *está* funcionando corretamente. No entanto, neste caso, o failover do ponto de extremidade não acontecerá se um ponto de extremidade falhar, o que afetará a disponibilidade geral do aplicativo. Para garantir que isso não ocorra, é importante verificar se o perfil mostra um status “Online” em vez de “Degradado”. O status Online mostra que as verificações de integridade do Gerenciador de Tráfego estão funcionando corretamente.

Para obter mais detalhes sobre como solucionar problemas de verificações de integridade com falha, consulte [Solução de problemas de status Degradado no Gerenciador de Tráfego do Azure](traffic-manager-troubleshooting-degraded.md).

## Perguntas frequentes

### O Gerenciador de tráfego é resistente a falhas de região do Azure?

Com verificações de integridade internas e failover automático entre regiões, o Gerenciador de Tráfego é um componente fundamental no fornecimento de aplicativos altamente disponíveis no Azure, incluindo aplicativos resilientes a uma falha do Azure em toda região.

Por isso, o Gerenciador de Tráfego deve oferecer um nível excepcionalmente alto de disponibilidade, incluindo resiliência à falha regional.

Embora algumas partes do Gerenciador de Tráfego sejam executadas no Azure, esses componentes são distribuídos entre regiões e foram desenvolvidos para serem resilientes a uma falha completa de qualquer região do Azure. Essa resiliência se aplica a todos os componentes do Gerenciador de Tráfego: os servidores de nome DNS, a API, a camada de armazenamento e o serviço de monitoramento de ponto de extremidade.

Portanto, mesmo no evento improvável de uma falha total de toda uma região do Azure, o Gerenciador de Tráfego deve continuar funcionando normalmente, e os clientes com aplicativos implantados em várias regiões do Azure podem depender do Gerenciador de Tráfego a fim de direcionar o tráfego para uma instância disponível de seus aplicativos.

### Como a escolha da localização do grupo de recursos afeta o Gerenciador de Tráfego?

O Gerenciador de Tráfego é um serviço global único. Não é regional. A escolha do local do grupo de recursos não faz diferença para perfis do Gerenciador de Tráfego implantados nesse grupo de recursos.

O Azure Resource Manager exige que todos os grupos de recursos especifiquem uma localização, que determina a localização padrão para os recursos implantados nesse grupo de recursos. Essa localização lhe será solicitada se você criar um novo grupo de recursos quando criar um perfil do Gerenciador de Tráfego por meio do portal do Azure.

Todos os perfis do Gerenciador de Tráfego usam **global** como a localização. Como esse é o único valor aceito, a configuração não fica acessível no portal do Azure, no PowerShell ou na CLI do Azure. Isso substitui o grupo de recursos padrão.

### Como determinar a integridade atual de cada ponto de extremidade?

O status de monitoramento atual de cada ponto de extremidade, bem como o perfil geral, é exibido no portal do Azure. Essas informações também estão disponíveis por meio da [API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) do Traffic Monitor, dos [cmdlets do PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) e da [CLI do Azure entre plataformas](../xplat-cli-install.md).

Não é possível exibir informações históricas sobre a integridade anterior do ponto de extremidade, nem é possível configurar alertas sobre alterações na integridade do ponto de extremidade.

### Posso monitorar os pontos de extremidade HTTPS?

Sim. O Gerenciador de Tráfego oferece suporte à investigação por HTTPS. Basta configurar **HTTPS** como o protocolo na configuração de monitoramento. Observe que:

- O certificado no lado do servidor não está validado
- Certificados no lado do servidor SNI não estão validados
- Não há suporte para certificados de cliente

### Qual cabeçalho host as verificações de integridade do ponto de extremidade usam?

O cabeçalho host usado nas verificações de integridade HTTP e HTTPS é o nome DNS associado a cada ponto de extremidade. Isso é exposto como o ponto de extremidade de destino no portal do Azure, nos [cmdlets do PowerShell](https://msdn.microsoft.com/library/mt125941.aspx), na [CLI do Azure entre plataformas](../xplat-cli-install.md) e na [API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx).

Este valor faz parte da configuração do ponto de extremidade. O valor usado no cabeçalho do host não pode ser especificado separadamente da propriedade “target”.

### Quais são os endereços IP dos quais as verificações de integridade se originam?

A lista abaixo contém os endereços IP dos quais você pode esperar que as verificações de integridade do Gerenciador de Tráfego se originem. Você pode usar esta lista para garantir que as conexões de entrada desses endereços IP sejam permitidas nos pontos de extremidades para verificar o status de integridade.



- 13\.75.153.124
- 13\.75.152.253
- 191\.232.214.62
- 191\.232.208.52
- 52\.172.155.168
- 52\.172.158.37
- 13\.75.124.254
- 13\.75.127.63
- 137\.135.82.249
- 137\.135.80.149
- 104\.41.190.203
- 104\.41.187.209
- 65\.52.217.19
- 23\.96.236.252
- 40\.87.147.10
- 40\.87.151.34
- 104\.215.91.84
- 13\.84.222.37
- 40\.68.30.66
- 40\.68.31.178
- 137\.135.47.215
- 137\.135.46.163

## Próximas etapas

Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-how-traffic-manager-works.md)

Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego

Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-manage-profiles.md)

[Solucionar problemas de status Degradado](traffic-manager-troubleshooting-degraded.md) em um ponto de extremidade do Gerenciador de Tráfego

<!---HONumber=AcomDC_0928_2016-->