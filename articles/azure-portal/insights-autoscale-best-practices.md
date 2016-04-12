<properties
	pageTitle="Azure Insights: práticas recomendadas para dimensionamento automático do Azure Insights. | Microsoft Azure"
	description="Aprenda os princípios para usar efetivamente o dimensionamento automático no Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Práticas recomendadas para dimensionamento automático do Azure Insights

As seções a seguir neste documento lhe ajudarão a entender as práticas recomendadas para Dimensionamento Automático no Azure Insights. Depois de revisar essas informações, você poderá usar mais efetivamente o Dimensionamento Automático na sua infraestrutura do Azure.

## Conceitos de dimensionamento automático

- Um recurso pode ter apenas *uma* configuração de Dimensionamento Automático
- Uma configuração de Dimensionamento Automático pode ter um ou mais perfis e cada perfil pode ter uma ou mais regras de dimensionamento automático.
- Uma configuração de dimensionamento automático dimensiona instâncias horizontalmente, que é *escalar* ao aumentar as instâncias e *reduzir* ao diminuir o número de instâncias. Uma configuração de dimensionamento automático tem um valor máximo, mínimo e padrão de instâncias.
- Um trabalho de dimensionamento automático sempre lê a métrica associada para expandir, verificando se ele ultrapassou o limite configurado para escalar ou reduzir horizontalmente. Você pode exibir uma lista de métricas que o dimensionamento automático pode dimensionar em [métricas comuns de dimensionamento automático do Azure Insights](insights-autoscale-common-metrics.md).
- Todos os limites são calculados em um nível de instância. Por exemplo, "escalar horizontalmente por 1 instância quando a média da CPU > 80% quando a contagem de instâncias for 2" significa escalar horizontalmente quando a média da CPU em todas as instâncias for maior que 80%.
- Você sempre receberá notificações de falha via email. Especificamente o proprietário, o colaborador e os leitores do recurso de destino receberão email. Você também receberá um email de *recuperação* quando o dimensionamento automático se recuperar de uma falha e iniciar o funcionamento normalmente.
- Você pode optar por receber uma notificação de ação de escala com êxito por meio de email e ganchos.

## Práticas recomendadas de dimensionamento automático

Use as seguintes práticas recomendadas ao usar o Dimensionamento Automático.

### Verifique se os valores mínimo e máximo são diferentes e têm uma margem suficiente entre eles
Se você tiver uma configuração que tenha no máximo = 2, mínimo = 2 e atual contagem de instâncias for 2, nenhuma ação de escala poderá ocorrer. Uma configuração recomendada é manter uma margem suficiente entre as contagens de instância máxima e mínima. O dimensionamento automático sempre será dimensionado entre esses limites, que é inclusivo. No entanto, suponha que você queira dimensionar manualmente a contagem de instâncias (atualização) para um valor acima do máximo. Na próxima vez que um trabalho de dimensionamento automático é executado, ele verifica se a contagem de instância atual é maior que o máximo. Se sim, ele é dimensionado no máximo, independentemente do limite definido nas regras. Da mesma forma, se você manualmente chegar a uma instância contagem atual menor que o mínimo, na próxima vez que um trabalho de dimensionamento automático for executado, ele se expandirá para o número mínimo de instâncias.

### Sempre use uma combinação de regra de escala e redução horizontal que executa um aumento e uma redução

Se você usar apenas uma parte da combinação, o dimensionamento automático reduzirá horizontalmente a única redução ou escala, até que o máximo ou o mínimo seja alcançado.

### Não alternar entre o portal do Azure e o portal clássico do Azure ao gerenciar o dimensionamento automático
Para Serviços de Nuvem e Serviços de Aplicativos (Aplicativos Web), use o portal do Azure (portal.azure.com) para criar e gerenciar configurações de Dimensionamento Automático. Para Conjuntos de Escala de Máquina Virtual, use PoSH, CLI ou API REST para criar e gerenciar a configuração de dimensionamento automático. Não alterne entre o portal clássico do Azure (manage.windowsazure.com) e o portal do Azure (portal.azure.com) ao gerenciar as configurações de dimensionamento automático. O portal clássico do Azure e seu back-end subjacente têm limitações. Mova para o portal do Azure para gerenciar o dimensionamento automático usando a interface gráfica do usuário. As opções são usar Autoscale PowerShell, CLI ou API REST (por meio do Gerenciador de Recursos do Azure).

### Escolher a estatística apropriada para sua métrica de diagnóstico
Para métricas de diagnóstico, você pode escolher entre *Média*, *Mínimo*, *Máximo* e *Total* como uma métrica para expandir. A estatística mais comum é *Média*.

### Escolha os limites cuidadosamente para todos os tipos de métrica
Recomendamos escolher cuidadosamente limites diferentes para escalar e reduzir horizontalmente com base em situações práticas.

*Não recomendamos* as configurações de dimensionamento automático como os exemplos abaixo com os valores de limite igual ou muito semelhante para condições de redução e escala:

- Aumentar as instâncias por contagem de 1 quando a Contagem de Threads < = 600
- Reduzir as instâncias por contagem de 1 quando a Contagem de Threads >= 600


Vejamos um exemplo de como o que pode levar a um comportamento que possa parecer confuso. Suponha que há 2 instâncias para começar e, em seguida, o número médio de threads por instância aumenta para 625. O dimensionamento automático escala horizontalmente uma terceira instância. Em seguida, suponha que a contagem média de threads entre a instância seja de 575. Antes de reduzir verticalmente, o dimensionamento automático tenta estimar como será o estado final de ele for reduzido horizontalmente. Por exemplo, 575 x 3 (contagem de instância atual) = 1.725 / 2 (número final de instâncias quando reduzido verticalmente) = 862.5 threads. Isso significa que o Dimensionamento Automático terá que imediatamente escalar horizontalmente novamente mesmo depois de ter sido reduzido horizontalmente, se a contagem média de threads permanecer a mesma ou tiver apenas uma pequena quantidade reduzida. No entanto, se escalado verticalmente novamente, todo o processo se repetiria, resultando em um loop infinito. Para evitar essa situação *flappy*, o Dimensionamento Automático não reduz verticalmente. Em vez disso, ele ignora e reavalia a condição novamente na próxima vez em que executa trabalho do serviço. Isso poderia confundir muitas pessoas porque o Dimensionamento Automático não funcionaria quando a contagem média de threads fosse 575.

Esse comportamento de estimativa durante uma redução horizontal destina-se a evitar uma situação flappy. Você deve ter esse comportamento em mente quando você escolher os mesmos limites de redução e escala horizontal.

Recomendamos escolher uma margem suficiente entre os limites de redução e escala horizontal. Por exemplo, considere a seguinte combinação de melhor regra.

- Aumentar as instâncias por contagem de 1 quando CPU% >= 80
- Reduzir as instâncias por contagem de 1 quando CPU% <= 60

Vamos examinar como esse exemplo funciona. Suponha que há 2 instâncias para começar. Se a porcentagem média de CPU entre instâncias for até 80, o dimensionamento automático escalará horizontalmente, adicionando uma terceira instância. Agora suponha que, ao longo do tempo, o percentual de CPU caia para 60. A regra de redução horizontal do dimensionamento automático estimaria o estado final se fosse necessário fazer a redução horizontal. Por exemplo, 60 x 3 (contagem de instância atual) = 180 / 2 (número final de instâncias quando reduzido verticalmente) = 90 threads. Portanto, o Dimensionamento Automático não reduz horizontalmente, pois ele teria que escalar horizontalmente novamente imediatamente. Em vez disso, ele ignora a redução vertical. Em seguida, suponha que a próxima vez que ele verificar, a CPU continua indo até 50 e, em seguida, estima novamente - instância de 50 x 3 = 150 / 2 instâncias = 75, que está abaixo do limite de escala horizontal de 80, portanto ele reduz horizontalmente para que ele é dimensionado em com êxito para 2 instâncias.

### Considerações sobre valores de dimensionamento de limite para métricas especiais
 Para métricas especiais, como a métrica do comprimento da Fila do Barramento de Serviço ou de Armazenamento, o limite é o número médio de mensagens disponíveis por número atual de instâncias. Opte por escolher cuidadosamente o valor de limite para esta métrica.

Vamos ilustrá-lo com um exemplo para que entender o comportamento melhor.

- Aumentar o número de instâncias por contagem de 1 quando a contagem de mensagem de Fila de Armazenamento > = 50
- Reduzir o número de instâncias por contagem 1 de contagem de mensagem de Fila de Armazenamento > = 10

Suponha que há 2 instâncias para começar. Em seguida, suponha que as mensagens continuam chegando e ao examinar a fila de armazenamento, a contagem total de leituras de 50. Você pode pressupor que o dimensionamento automático deve iniciar uma ação de escala horizontal. No entanto, observe que ainda é 50/2 = 25 mensagens por instância. Portanto, a escala horizontal não ocorrerá. Para a primeira escala horizontal acontecer, a contagem total de mensagens na fila de armazenamento deve ser de 100. Em seguida, suponha que a contagem total de mensagens chegue a 100. Uma terceira instância é adicionada devido a uma ação de escala horizontal. A ação de escala horizontal próxima não ocorrerá até que a contagem total de mensagens na fila atinja 300. Vamos examinar a ação de redução horizontal. Suponha que o número de instâncias seja 3. A primeira ação de escala horizontal acontece quando o total de mensagens na fila atingir 30, tornando-o 30/3 = 10 mensagens por instância, que é o limite de escala horizontal.

### Considerações sobre dimensionamento quando vários perfis são configurados em uma configuração de dimensionamento automático

Em uma configuração de dimensionamento automático, você pode escolher um perfil padrão, que é sempre aplicado sem qualquer dependência de agenda ou hora, ou você pode escolher um perfil recorrente ou um perfil para um período fixo com um intervalo de data e hora.

Quando o serviço de Dimensionamento Automático o processar, ele sempre verifica na seguinte ordem:

1. Perfil de Data Fixa
2. Perfil recorrente
3. Perfil ("Sempre") Padrão

Se uma condição de perfil for atendida, o dimensionamento automático não verificará a próxima condição de perfil abaixo dele. O dimensionamento automático só processa um perfil por vez. Isso significa que, se você quiser incluir também uma condição de processamento de um perfil de nível inferior, você deverá incluir essas regras bem no perfil atual.

Vamos examinar isso usando um exemplo:

A imagem abaixo mostra uma configuração de dimensionamento automático com um perfil padrão de instâncias mínimas = 2 e máximo de instâncias = 10. Neste exemplo, as regras são configuradas para escalar horizontalmente quando a contagem de mensagens na fila for maior que 10 e reduzir horizontalmente de quando a contagem de mensagens na fila for menor que 3. Agora, o recurso pode ser dimensionado entre 2 e 10 instâncias.

Além disso, há um perfil recorrente para segunda-feira. Ele é definido para instâncias mínimas = 2 e máximo de instâncias = 12. Isso significa que na segunda-feira, na primeira vez que o dimensionamento automático verificar essa condição, a contagem de instâncias era de 2, e ela será dimensionada para o novo mínimo de 3. Enquanto o dimensionamento automático continuar para encontrar essa condição de perfil correspondido (segunda-feira), ele só processará as regras de redução/escala horizontal configuradas para esse perfil. Neste momento, ele não verificará o comprimento da fila. No entanto, se você deseja que a condição de comprimento de fila seja verificada, você deve incluir as regras do perfil padrão também em seu perfil de segunda-feira.

Da mesma forma, quando o dimensionamento automático alternar de volta para o perfil padrão, ele primeiro verifica se as condições de mínimas e máxima são atendidas. Se o número de instâncias no momento é 12, ele é dimensionado em 10, o máximo permitido para o perfil padrão.

![configurações de dimensionamento automático](./media/insights-autoscale-best-practices/insights-autoscale-best-practices.png)

### Considerações sobre dimensionamento quando várias regras são configuradas em um perfil
Há casos em que talvez você precise definir várias regras em um perfil. O seguinte conjunto de regras de dimensionamento automático é usado por serviços quando várias regras são definidas.

Em *escalar horizontalmente*, o dimensionamento automático será executado se nenhuma regra for atendida. Em *reduzir horizontalmente*, o dimensionamento automático exige que todas as regras sejam atendidas.
 
Para ilustrar, suponha que você tenha as seguintes 4 regras de dimensionamento automático:
 
- Se CPU < 30%, reduza horizontalmente por 1
- ​Se Memória < 50%, reduza horizontalmente por 1
- ​Se CPU > 75%, escalehorizontalmente por 1
- ​Se Memória < 75, escale horizontalmente por 1

Em seguida, ocorrerá o seguinte:
- Se a CPU for 76% e a Memória 50%, escalaremos horizontalmente.
- Se a CPU for 50% e a Memória 76%, escalaremos horizontalmente.
 
Por outro lado, se a CPU é 25% e a memória é 51%, dimensionamento automático **não** será reduzido horizontalmente. Para reduzir horizontalmente, a CPU deve ser 29% e memória 49%.

### Sempre selecione uma contagem de instância de segurança padrão
A contagem de instâncias padrão é importante porque ele é a contagem de instâncias que o dimensionamento automático dimensiona o serviço quando métricas não estão disponíveis. Portanto, selecione uma contagem de instância padrão que seja segura para suas cargas de trabalho.

### Configurar notificações de dimensionamento automático
O dimensionamento automático notificará os administradores e os colaboradores do recurso por email se ocorrer qualquer uma das seguintes condições:
- O serviço de dimensionamento automático não pode executar uma ação.
- As métricas não estão disponíveis para o serviço de dimensionamento automático tomar uma decisão de escala.
- As métricas estão disponíveis (recuperação) novamente para tomar uma decisão de escala. Além das condições acima, você pode configurar notificações por email ou webhook para obter notificações de ações de dimensionamento bem-sucedido.

<!-----------HONumber=AcomDC_0330_2016-->