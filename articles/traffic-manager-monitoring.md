<properties 
   pageTitle="Monitoramento do Gerenciador de Tráfego"
   description="Este artigo o ajudará a compreender e configurar o monitoramento do Gerenciador de Tráfego"
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Sobre o monitoramento do Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure monitora seus pontos de extremidade, incluindo serviços de nuvem e sites, para garantir que estejam disponíveis. Para que o monitoramento funcione corretamente, você deve configurá-lo do mesmo modo para cada ponto de extremidade que você especifica no perfil do Gerenciador de Tráfego. Depois de você configurar o monitoramento, o Gerenciador de Tráfego exibirá o status de seus pontos de extremidade e o perfil no Portal de Gerenciamento. Você pode configurar as definições de monitoramento no Portal de Gerenciamento, na página Configurar, para seu perfil do Gerenciador de Tráfego. Você pode especificar as configurações a seguir:

- **Protocolo** - escolha HTTP ou HTTPS. É importante observar que o monitoramento HTTPS não verifica se o seu certificado SSL é válido, ele apenas verifica se tal certificado está presente.

- **Porta** - escolha a porta usada para a solicitação. Portas HTTP e HTTPS Padrão estão entre as opções.

- **Caminho relativo e nome de arquivo** - forneça o caminho e o nome do arquivo que o sistema de monitoramento tentará acessar. Observe que a barra "/" é uma entrada válida para o caminho relativo e implica que o arquivo está no diretório raiz (padrão). 

## Sobre monitoramento do status de integridade

O Gerenciador de Tráfego do Azure exibe a integridade do serviço de ponto de extremidade e de perfil no Portal de Gerenciamento. A coluna de status tanto para o perfil quanto para o ponto de extremidade exibe o status do monitor mais recente. Você pode usar esse status para compreender a integridade dos perfis de acordo com suas configurações de monitoramento do Gerenciador de Tráfego. Quando seu perfil está íntegro, as consultas DNS serão distribuídas aos serviços com base nas configurações de balanceamento de carga para o perfil (Round Robin, Desempenho ou Failover). Depois que o sistema de monitoramento do Gerenciador de Tráfego detecta uma alteração no status do monitor, ele atualiza a entrada de status no Portal de Gerenciamento. Pode levar até cinco minutos para que a alteração de estado seja atualizada.

### Status do monitor de ponto de extremidade

O status do Monitor de ponto de extremidade na tabela abaixo é o resultado de uma combinação dos resultados da investigação de integridade do ponto de extremidade e suas configurações de perfil e de ponto de extremidade.

|Status de perfil|Status de ponto de extremidade|Status do monitor de ponto de extremidade (API e Portal)|Observações|
|---|---|---|---|
|Desabilitado|Habilitado|Inativo|Perfis desabilitados não estão sendo monitorados. No entanto, o status de ponto de extremidade em perfis desabilitados ainda pode ser gerenciado.|
|&lt;qualquer&gt;|Desabilitado|Desabilitado|Perfis desabilitados não estão sendo monitorados. No entanto, o status de ponto de extremidade em perfis desabilitados ainda pode ser gerenciado.|
|Habilitado|Habilitado|Online|O ponto de extremidade está sendo monitorado e está íntegro.|
|Habilitado|Habilitado|Degradado|O ponto de extremidade está sendo monitorado e não está íntegro.|
|Habilitado|Habilitado|Verificando ponto de extremidade|O ponto de extremidade está sendo monitorado, mas os resultados da primeira investigação ainda não foram recebidos. Esse estado é temporário, ocorre logo após você ter adicionado um novo ponto de extremidade ao perfil ou então logo após ter habilitado um ponto de extremidade ou perfil.|
|Habilitado|Habilitado|Parado|O serviço de nuvem ou site subjacente não está em execução.|

### Status do Monitor de perfil

O status do Monitor de perfil na tabela abaixo é o resultado da combinação do status do monitor de ponto de extremidade e do status de seu perfil configurado.

|Status de perfil (conforme configurado) |Status do Monitor de ponto de extremidade|Status do Monitor de perfil (API e Portal)|Observações|
|---|---|---|---|
|Desabilitado|&lt;qualquer&gt; ou um perfil sem pontos de extremidade definidos. |Desabilitado|Pontos de extremidade não estão sendo monitorados.|
|Habilitado|O status de pelo menos um ponto de extremidade é "Degradado".|Degradado|Este é um sinalizador de que é necessária uma ação do cliente.|
|Habilitado|O status de pelo menos um ponto de extremidade é "Online". Nenhum ponto de extremidade está "Degradado".|Online|O serviço está aceitando tráfego e não é necessária nenhuma ação do cliente.|
|Habilitado|O status de pelo menos um ponto de extremidade é "Verificando ponto de extremidade". Nenhum ponto de extremidade está com status "Online" ou "Degradado".|Verificando pontos de extremidade|Estado transicional. Isso ocorre normalmente quando um perfil acabou de ser habilitado e a integridade do ponto de extremidade está sendo investigada.|
|Habilitado|O status de todos os pontos de extremidade definidos no perfil é "Desabilitado" ou "Parado", ou então o perfil não tem nenhum ponto de extremidade definido.|Inativo|Nenhum ponto de extremidade está ativo, mas o perfil ainda está habilitado.|

## Como funciona o monitoramento

Um cronograma de exemplo que ilustra o processo de monitoramento com um único serviço de nuvem é exibido abaixo. Este cenário mostra o seguinte:

- O serviço de nuvem está disponível e recebendo tráfego EXCLUSIVAMENTE via esse perfil de Gerenciador de Tráfego.

- O serviço de nuvem torna-se indisponível.

- O serviço de nuvem permanece indisponível por um tempo muito maior do que o TTL (vida útil) do DNS.

- O serviço de nuvem torna-se disponível novamente.

- O serviço de nuvem volta a receber tráfego, EXCLUSIVAMENTE via esse perfil de Gerenciador de Tráfego.

![Traffic Manager Monitoring Sequence](./media/traffic-manager-monitoring/IC697947.jpg)

**Figura 1** - exemplo de sequência de monitoramento. Os números no diagrama correspondem à explicação numerada abaixo.

1. **GET** - o sistema de monitoramento do Gerenciador de Tráfego executa um GET no caminho e arquivo especificado, nas configurações de monitoramento.
2. **200 OK** - o sistema de monitoramento espera uma mensagem HTTP 200 OK novamente dentro de 10 segundos. Quando ele recebe essa resposta, pressupõe que o serviço de nuvem está disponível. 
>[AZURE.NOTE] O Gerenciador de Tráfego só considera um ponto de extremidade como estando Online se a mensagem de retorno for uma 200 OK. Se uma resposta diferente de 200 for recebida, ele presumirá que o ponto de extremidade não está disponível e considerará essa verificação como tendo resultado em falha.

3. **30 segundos entre verificações** - essa verificação será realizada a cada 30 segundos.
4. **Serviço de nuvem indisponível** - o serviço de nuvem torna-se indisponível. O Gerenciador de Tráfego não saberá disso até a próxima verificação de monitor.
5. **Tentativas de acessar o arquivo de monitoramento (4 tentativas)** - o sistema de monitoramento executa um GET, mas não receber uma resposta em 10 segundos ou menos. Então, ele executa mais três tentativas em intervalos de 30 segundos. Isso significa que leva no máximo um minuto e meio para o sistema de monitoramento detectar quando um serviço se torna indisponível. Se uma das tentativas for bem-sucedida, o número de tentativas será redefinido. Embora não mostrado no diagrama, se as mensagemns 200 OK voltarem após mais de 10 segundos do comando GET, o sistema de monitoramento ainda assim considerará essa verificação como tendo resultado em falha.
6. **Marcado como degradado** - após a quarta falha em uma linha, o sistema de monitoramento marcará o serviço de nuvem indisponível como Degradado.
7. **O tráfego para o serviço de nuvem diminui** - o tráfego pode continuar fluindo para o serviço de nuvem indisponível. Os clientes vão experienciar falhas porque o serviço está indisponível. Os clientes e servidores DNS secundários armazenaram em cache o registro DNS para o endereço IP do serviço de nuvem indisponível. Elas continuam a resolver o nome DNS do domínio da empresa para o endereço IP do serviço. Além disso, os servidores DNS secundários ainda podem distribuir as informações de DNS do serviço indisponível. Conforme os clientes e servidores DNS secundários são atualizados, o tráfego para o endereço IP do serviço indisponível diminuirá. O sistema de monitoramento continua realizando verificações em intervalos de 30 segundos. Neste exemplo, o serviço não responde e permanece indisponível.
8. **Tráfego para interrupções do serviço de nuvem** - nesse momento, a maioria dos servidores DNS e clientes deve ser atualizada e o tráfego para o serviço indisponível é interrompido. A quantidade máxima de tempo antes do tráfego ser completamente interrompido depende do tempo de TTL. O TTL padrão do DNS é 300 segundos (5 minutos). Usando esse valor, os clientes param de usar o serviço após 5 minutos. O sistema de monitoramento continua executando verificações em intervalos de 30 segundos e o serviço de nuvem não responde.
9. **O serviço de nuvem volta a ficar online e recebe tráfego** - o serviço torna-se disponível, mas o Gerenciador de Tráfego não sabe disso até o sistema de monitoramento executar uma verificação.
10. **O tráfego para o serviço volta a acontecer** - o Gerenciador de Tráfego envia um GET e recebe uma resposta 200 OK em menos de 10 segundos. Ele então começa a distribuir o nome DNS do serviço de nuvem para servidores DNS conforme eles solicitam atualizações. Como resultado, o tráfego começa a fluir novamente para o serviço.

## Status de pontos de extremidade pai e filho para perfis aninhados

A tabela a seguir descreve o comportamento de monitoramento do Gerenciador de Tráfego para perfis pai e filho de um perfil aninhado e a configuração minChildEndpoints. Para obter mais informações, consulte [Visão geral do Gerenciador de Tráfego](traffic-manager-overview.md).

|Status do monitor de perfis filho|Status do monitor de ponto de extremidade pai|Observações|
|---|---|---|
|Desabilitado|Isso se deve ao perfil ser desabilitado por você.|Interrompido|O estado do ponto de extremidade pai é Interrompido, e não Desabilitado. O estado Desabilitado é reservado para indicar se você desabilitou o ponto de extremidade no perfil pai.|
|Degradado|Pelo menos um ponto de extremidade filho está em estado Degradado.|No estado Online, se o número de pontos de extremidade Online no perfil filho é pelo menos igual ao valor do estado minChildEndpoints. No estado Verificando Ponto de Extremidade, se o número de pontos de extremidade Online mais o valor da variável "CheckingEndpoint" é igual a pelo menos o valor de "minChildEndpoints". Caso contrário, o estado será Degradado.|O tráfego é roteado para um ponto de extremidade de status Verificando Ponto de Extremidade. Se a variável "minChildEndpoints" estiver definida com um valor alto demais, o ponto de extremidade pai será sempre Degradado.|
|Online|Pelo menos um filho está em estado Online e nenhum está no estado Degradado.|O mesmo que acima.||
|Verificando Pontos de Extremidade|Pelo menos um é 'Verificando ponto de extremidade'; nenhum é 'Online' nem 'Degradado'|O mesmo que acima.||
|Inativo|Todos os pontos de extremidade estão com status Desabilitado ou Parado, ou então esse é um perfil sem nenhum ponto de extremidade|Parado||

## Para configurar o monitoramento para um caminho e nome de arquivo específicos

1. Crie um arquivo com o mesmo nome em cada ponto de extremidade que você planeja incluir em seu perfil.
2. Para cada ponto de extremidade, use um navegador da Web para testar o acesso ao arquivo. A URL consiste no nome de domínio do ponto de extremidade específico (o serviço de nuvem ou site), o caminho para o arquivo e o nome do arquivo. 
3. No Portal de Gerenciamento, em **Configurações de Monitoramento**, no **Caminho Relativo e Nome de Arquivo**, especifique o caminho e o nome de arquivo.
4. Quando você terminar de fazer suas alterações de configuração, clique em **Salvar** na parte inferior da página.

## Consulte também

[Visão geral do Gerenciador de Tráfego](traffic-manager-overview.md)

[Sobre métodos de balanceamento de carga do Gerenciador de Tráfego](traffic-manager-load-balancing-methods.md)

[Serviços de Nuvem](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Sites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Publicação no blog - Solucionando problemas de status degradado do Gerenciador de Tráfego](http://go.microsoft.com/fwlink/p/?LinkId=328689)

<!--HONumber=49-->