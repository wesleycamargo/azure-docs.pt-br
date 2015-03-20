<properties 
   pageTitle="Guia de solução de problemas do Mobile Engagement do Azure" 
   description="Guia de solução de problemas para Mobile Engagement do Azure" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Mobile Engagement do Azure - Guias de solução de problemas

## Introdução

Os guias de solução de problemas a seguir abordam os problemas mais comuns com o Móbile Engagement do Azure, com exceção de dúvidas de [cobrança][Link 11] assistência inscrevendo-se para a [visualização][Link 7]. Se você não for capaz de resolver seu problema com estas guias de solução de problemas, também é possível abrir uma solicitação de serviço do Azure - se você tiver um [Contrato de suporte do Azure](http://azure.microsoft.com/support/options/) - ou você pode solicitar assistência no [Fórum do MSDN][Link 8]. Certifique-se de incluir todas as informações de solução de problemas de <a href="#SR" title="SR">Informações SR</a> ao abrir uma solicitação de serviço para ajudar o suporte técnico a investigar o problema.

## <a name="#ANALYTICS">Análise, monitoramento, segmentação e painéis</a>

Problemas em como o Mobile Engagement do Azure reúne informações sobre aplicativos, dispositivos e usuários.

**Lista de sintomas:**

1. <a href="#ANALYTICS1">Informações ausentes/atrasadas</a>
2. <a href="#ANALYTICS2">Não é possível localizar itens na interface do usuário</a>
3. <a href="#ANALYTICS3">Solucionando problemas de falhas</a>
 
<a name="#ANALYTICS1">**Sintoma:**
1.    Informações ausentes/atrasadas:</a>

- As informações estão atrasadas ao aparecer na análise, segmentação ou painel.
- Faltam informações de monitoramento.
- Faltam informações de análise, segmentação ou painel.
- Atingindo os limites de segmentação.

**Causas:**

- Você pode usar a API de análise, API do Monitor e a API de segmentos para verificar se alguns dos dados que estão faltando na interface do usuário são visíveis através das APIs.
- Se o SDK do Mobile Engagement do Azure não estiver integrado corretamente em seu aplicativo, então você não poderá ver as informações de análise, segmentação, monitoramento ou dos painéis.
- Os segmentos não podem ser alterados depois que eles forem criados, os segmentos só podem ser "clonados" (copiados) ou "destruídos" (excluídos).
Os segmentos podem conter apenas 10 critérios.
- A melhor maneira de testar as informações ausentes do monitoramento (Configuração de um dispositivo de teste, desinstalação e/ou reinstalação do aplicativo no dispositivo de teste).
- As informações são atualizadas a cada 24 horas para análise, segmentação ou painéis.
- Informações em novos segmentos não podem ser exibidas até 24 horas após elas terem sido criadas (mesmo se o segmento estiver baseado nas informações anteriores).
- Filtrar seus dados de análise na interface do usuário mostrará todos os exemplos desse tipo, independentemente da versão do seu aplicativo (por exemplo, "falhas" filtradas por nome aparecerão a partir da versão 1 e 2 do seu aplicativo).
- O período de tempo para análise se baseia na data das configurações dos dispositivos dos usuários, de forma que um usuário cujo telefone tenha a data definida incorretamente possa exibir o período de tempo incorreto.
- Nenhum dado do lado do servidor é registrado quando você usa o botão "testar" envios, os dados são registrados apenas para campanhas de envio real.

**Consulte também:** 

- [Guia de solução de problemas - SDK][Link 2], [Documentação da API][Link 4], [Documentação de interface do usuário - segmentos][Link 1]

<a name="#ANALYTICS2">**Sintoma:**
2. Não é possível localizar itens na interface do usuário</a>

- Não é possível criar segmentos com base em critérios de marcas de informações de determinado aplicativo nativo ou personalizado.
- Não é possível localizar critérios de marcas de informações de determinado aplicativo nativo ou personalizado na análise, monitoramento ou painéis.
- Não é possível interpretar os dados de monitoramento, análise, segmentação ou painéis.

**Causas:**

- Alguns itens nativos e marcas de informações do aplicativo só estão disponíveis como critérios de envio, mas podem não ser adicionados a um segmento ou serem visíveis a partir da análise, monitoramento ou painel. 
- Para marcas de informações de aplicativos e itens nativos que não podem ser adicionadas a um segmento, você precisará configurar a lista de critérios de direcionamento em cada campanha para executar a mesma função com base no direcionamento em um segmento.
- Consulte os menus de contexto nas seções de análise, monitoramento, segmentação e painéis da interface gráfica do Mobile Engagement do Azure para obter mais ajuda e informações sobre como proceder.

**Consulte também:**

- [Documentação da interface do usuário - Alcançar um novo critério de envio para o direcionamento do público-alvo][Link 1]
 
<a name="#ANALYTICS3">**Sintoma:**
3. Solucionando problemas de falhas</a>

- Falha de aplicativo que aparecem na análise, monitoramento ou painel.

**Causas:**

- Para solucionar problemas de falha do aplicativo na análise, monitoramento ou painel, certifique-se em verificar as notas de versão para os problemas conhecidos com versões anteriores do SDK.
- Para solucionar falhas adicionais do aplicativo execute um evento de um dispositivo de teste com seu aplicativo instalado e procure o ID do dispositivo na seção "Monitor - eventos" da interface de usuário do Mobile Engagement do Azure. Em seguida, execute o mesmo que está causando a falha no seu aplicativo e pesquise por informações adicionais na seção "Monitoramento - falha" na interface do usuário do Engagement Mobile do Azure. 

**Consulte também:**

- [Conceitos - Perguntas frequentes][Link 6], [Conceitos - Glossário][Link 6], [Documentação de interface do usuário][Link 1], [Documentação do SDK - notas de versão][Link 5], [Documentação do SDK - guias de atualização][Link 5], [Manuais - Registro do ID do dispositivo][Link 3]

## <a name="#APIS">APIs</a>

Problemas em como os administradores interagem com o Mobile Engagement do Azure por meio das APIs.

**Lista de sintomas:**

1. <a href="#APIS1">Problemas de sintaxe</a>
2. <a href="#APIS2">Não é possível usar a API para executar a mesma ação disponível na interface de usuário do Mobile Engagement do Azure</a>
3. <a href="#APIS3">Mensagens de erro</a>
4. <a href="#APIS4">Falhas silenciosas</a>
 
<a name="#APIS1">**Sintoma:**
1. Problemas de sintaxe:</a>

- Erros de sintaxe usando a API (ou comportamento inesperado).

**Causas:**

- Problemas de sintaxe:
    - Certifique-se de verificar a sintaxe da API específica que você está usando para confirmar se a opção está disponível.
    - Um problema comum com o uso da API é confundir a API Reach e a API Push (a maioria das tarefas deve ser executada com a API Reach, em vez da API Push). 
    - Outro problema comum com integração do SDK e o uso da API é confundir a chave do SDK e a chave de API.
    - Os scripts que se conectam às APIs precisam enviar dados pelo menos a cada 10 minutos ou a conexão atingirá o tempo limite (especialmente comum em scripts de API do Monitor aguardando dados). Para evitar esgotamento do tempo limite, faça seu script enviar um ping XMPP a cada 10 minutos para manter a sessão ativa com o servidor.

**Consulte também:**
 
- [Conceitos - Glossário][Link 6], [Documentação da API][Link 4], [Informações do protocolo XMPP]( http://xmpp.org/extensions/xep-0199.html)
 
<a name="#APIS2">**Sintoma:**
2. Não é possível usar a API para executar a mesma ação disponível na interface de usuário do Mobile Engagement do Azure:</a>

- Uma ação que funciona a partir da interface do usuário do Mobile Engagement do Azure não funciona na API a partir da API relacionada ao Mobile Engagement do Azure.

**Causas:**

- A confirmação que você pode executar a mesma ação da interface do usuário do Mobile Engagement do Azure mostra que esse recurso do Mobile Engagement do Azure foi corretamente integrado com o SDK.

**Consulte também:**
 
- [Documentação de interface do usuário][Link 1]
 
<a name="#APIS3">**Sintoma:**
3. Mensagens de erro:</a>

- Códigos de erro usando a API exibida em tempo de execução ou nos logs.

**Causas:**

- Aqui está uma lista composta de números comuns de códigos de status da API para referência e solução de problemas preliminar:

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response's body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently "in progress", campaign must be "in progress" and the campaign's property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn't retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504         The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

**Consulte também:**

- [Documentação da API - erros detalhados sobre cada API específica][Link 4]
 
<a name="#APIS4">**Sintoma:**
4. Falhas silenciosas:</a>

- Ação de API falha sem nenhuma mensagem de erro exibida em tempo de execução ou nos logs.

**Causas:**

- Muitos itens serão desabilitados na interface do usuário do Mobile Engagement do Azure se eles não forem integrados corretamente, mas falharão de forma silenciosa a partir da API, assim lembre-se de testar a mesma funcionalidade da interface do usuário para ver se ela funciona.
- O Mobile Engagement do Azure e muitos recursos avançados do Mobile Engagement do Azure que você está tentando usar, precisam ser individualmente integrados em seu aplicativo com o SDK como etapas distintas antes que você possa usá-los.

**Consulte também:**

- [Guia de solução de problemas - SDK][Link 2], [Documentação do SDK][Link 5]
 
## <a name="#PUSH">Push/Reach</a>

Problemas em como o Mobile Engagement do Azure envia informações para seus usuários.
 
**Lista de sintomas:**

1. <a href="#PUSH1">Falhas de envio</a>
2. <a href="#PUSH2">Problemas de testes de envio</a>
3. <a href="#PUSH3">Problemas de personalização de envio</a>
4. <a href="#PUSH4">Problemas de direcionamento de envio</a>
5. <a href="#PUSH5">Agendamento de envio</a>
 
<a name="#PUSH1">**Sintoma:**
1. Falhas de envio:</a>

- Envios não funcionam (no aplicativo, fora do aplicativo ou ambos).

**Causas:**

- Falhas de envio:
    - Uma falha de envio muitas vezes é uma indicação de que o Mobile Engagement do Azure, Reach ou outro recurso avançado do Mobile Engagement do Azure não está corretamente integrado ou que uma atualização é necessária no SDK para corrigir um problema conhecido com um novo SO ou plataforma de dispositivo.
    - Teste apenas um envio no aplicativo e apenas um envio fora do aplicativo para determinar se algo é um problema no aplicativo ou de fora do aplicativo.
    - Teste a partir da interface do usuário e da API como uma etapa de solução de problemas para ver quais informações de erro adicionais estão disponíveis nos dois lugares.
    - O envio fora do aplicativo não funcionará a menos que o Mobile Engagement do Azure e Reach estejam integrados no SDK.
    - O envio fora do aplicativo não funcionará se certificados não forem válidos ou estiver usando PROD x DEV corretamente (apenas para iOS).
    - As contagens de envio fora do aplicativo são tratadas de maneira diferente em diferentes plataformas (o iOS mostra menos informações que o Android se o envio nativo estiver desabilitado em um dispositivo, a API pode fornecer mais informações do que a interface do usuário nas estatísticas de envio).
    - Os envios fora do aplicativo podem ser bloqueados por clientes no nível de sistema operacional (iOS e Android).
    - Envios fora do aplicativo serão mostrados como desabilitados na interface do usuário do Mobile Engagement do Azure se não estiverem integrados corretamente, mas podem falhar silenciosamente a partir da API.
    - O envio no aplicativo não funcionará a menos que o Mobile Engagement do Azure e Reach estejam integrados no SDK.
    - Envios GCM e ADM não funcionarão a menos que o Mobile Engagement do Azure e o servidor específico estejam integrados no SDK (somente Android).
    - Envios no aplicativo e de fora do aplicativo devem ser testados separadamente para determinar se é um problema de Push ou Reach.
    - O envio no aplicativo exige que o aplicativo esteja aberto para ser recebido.
    - O envio no aplicativo costuma ser configurado para ser filtrado por uma marca de informações de aplicativo de aceitação (opt-in) ou recusa (opt-out).
    - Se você usar uma categoria personalizada no Reach para exibir notificações no aplicativo, você deve seguir o ciclo de vida correto da notificação, caso contrário, a notificação não poderá ser apagada quando o usuário descartá-la.
    - Se você iniciar uma campanha sem data de término e um dispositivo receber a notificação no aplicativo, mas não exibi-la de imediato, o usuário ainda receberá a notificação na próxima vez que fizer logon no aplicativo, mesmo se você encerrar manualmente a campanha.
    - Para problemas com a API Push, confirme que você realmente deseja usar a API Push em vez da API Reach (já que a API Reach é usada com mais frequência) e que você não está confundindo os parâmetros "carga" e "notificação".
    - Teste sua campanha de envio com tanto com o dispositivo conectado por WiFI e quanto por 3G para eliminar a conexão de rede como uma possível fonte de problemas.

**Consulte também:**

- [Guia de solução de problemas - SDK][Link 2], [Guia de solução de problemas - Push][Link 2], [Documentação do SDK - iOS - Como preparar seu aplicativo para notificações por Push da Apple][Link 5]
 
<a name="#PUSH2">**Sintoma:**
2. Problemas de testes de envio:</a>

- Os envios podem ser enviados a um dispositivo específico com base em uma ID de dispositivo.

**Causas:**

- Dispositivos de teste são configurados de forma diferente para cada plataforma, mas ocasionando um evento em seu aplicativo em um dispositivo de teste e procurando por sua ID do dispositivo no portal deve funcionar para localizar sua ID de dispositivo em todas as plataformas.
- Dispositivos de teste funcionam de forma diferente com IDFA x IDFV (apenas iOS).

**Consulte também:**

- [Documentação da interface do usuário - Reach][Link 1]
 
<a name="#PUSH3">**Sintoma:**
3. Problemas de personalização de envio:</a>

- Item de conteúdo de envio avançado não funciona (notificação, toque, vibração, imagem, etc.).
- Links de envios não funcionam (fora do aplicativo, no aplicativo, em um site, em um local no aplicativo).
- Estatísticas de envio mostram que um envio não foi enviado para tantas pessoas conforme o esperado (muitas ou insuficiente).
- Envios duplicados e recebidos duas vezes.
- Não é possível registrar o dispositivo de teste para o Envio do Mobile Engagement do Azure (com seu próprio aplicativo Prod ou DEV).

**Causas:**

- São necessárias "categorias" para vincular a um local específico no aplicativo (somente Android).
- Servidores de imagem externa precisam ser capazes de usar HTTP "GET" e "HEAD" para o envio de imagens grandes para funcionarem (somente Android).
- Em seu código, você pode desabilitar o agente do Mobile Engagement do Azure quando o teclado estiver aberto e reativar seu código do agente do Mobile Engagement do Azure quando o teclado estiver fechado para que o teclado não afete a aparência de sua notificação (somente iOS).
- Alguns itens não funcionam em simulações de teste, mas somente em campanhas reais (notificação, toque, vibração, imagens, etc.).
- Nenhum dado do lado do servidor é registrado quando você usar o botão para "testar" envios. Somente os dados serão registrados para campanhas de envio reais.
- Para ajudar a isolar o problema, solucione problemas com: teste, simulação e uma campanha real, pois cada um deles funcionam de forma um pouco diferente.

**Consulte também:**

- [Manuais - Push][Link 3], [Guia de solução de problemas - Push][Link 2], [Informações de protocolo HTTP]( http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
 
<a name="#PUSH4">**Sintoma:**
4. Problemas de direcionamento de envio:</a>

- Direcionamento nativo não funciona conforme o esperado.
- Direcionamento de Marca de informações do aplicativo não funciona conforme o esperado.
- Direcionamento de localização geográfica não funciona conforme o esperado.
- Opções de idiomas não funcionam conforme o esperado.

**Causas:**

- Direcionamento de envio:
    - Certifique-se de que você carregou marcas de informações do aplicativo por meio da interface do usuário do Mobile Engagement do Azure Mobile ou pela API.
    - Limitar a velocidade de envio ou cota de envio no nível do aplicativo, ou limitar o público-alvo no nível da campanha pode impedir que uma pessoa receba um envio específico, mesmo que eles atendam aos outros critérios de direcionamento. 
    - Definir um "Idioma" é diferente do direcionamento com base no país ou localidade, que também é diferente de direcionamento com base na localização geográfica em uma localização por telefone ou GPS.
    - A mensagem no "idioma padrão" é enviada para qualquer cliente que não tenha o seu dispositivo definido para um dos idiomas alternativos que você especificar.

**Consulte também:**

- [Documentação da interface do usuário - Reach][Link 1], [Documentação de interface do usuário - Configurações][Link 1], [Documentação da API - Reach][Link 4], [Documentação da API - Push][Link 4], [Documentação da API - Dispositivo][Link 4]
 
<a name="#PUSH5">**Sintoma:**
5. Agendamento de envio:</a>

- Agendamento de envio não funciona conforme o esperado (enviado muito adiantado ou atrasado).

**Causas:**

- Fusos horários podem ter problemas com o agendamento, especialmente ao usar o fuso horário dos usuários finais.
- Recursos avançados de envio podem atrasar envios.
- Direcionamento com base nas configurações do telefone (em vez de marcas de informações de aplicativo) podem atrasar os envios uma vez que o Mobile Engagement do Azure pode ter que solicitar dados da hora real do telefone antes de realizar um envio.
- Campanhas criadas sem uma data de término armazenam o envio por push localmente no dispositivo e ele é exibido na próxima vez que o aplicativo é aberto, mesmo que a campanha esteja manualmente encerrada.
- Iniciar mais de uma campanha ao mesmo tempo pode levar mais tempo para verificar sua base de usuários (tente só iniciar uma campanha por vez com um máximo de quatro, também direcione apenas para os usuários ativos de forma que os usuários antigos não precisem ser verificados).
- Se você usar a opção "Ignorar público, envio será enviado aos usuários por meio da API" na seção "Campanha" de uma campanha Reach a campanha NÃO será enviada automaticamente, você precisará para enviá-la manualmente através da API Reach.
- Se você usar uma categoria personalizada no Reach para exibir notificações no aplicativo, você deve seguir o ciclo de vida correto de uma notificação, caso contrário, a notificação não poderá ser apagada quando o usuário descartá-la.

**Consulte também:**

- [Manuais - planejamento][Link 3], [Documentação de interface do usuário - Alcance nova campanha de envio][Link 1]
 
## <a name="#SERVICE">Serviço</a>

Problemas com a execução do Mobile Engagement do Azure.

**Lista de sintomas:**

1. <a href="#SERVICE1">Interrupções de serviço</a>
2. <a href="#SERVICE2">Conectividade e problemas de informações incorretas</a>
3. <a href="#SERVICE3">Solicitações de recursos</a>
 
<a name="#SERVICE1">**Sintoma:**
1. Interrupções de serviço:</a>

**Causas:**

- Problemas que parecem ser provocados por interrupções de serviços do Mobile Engagement do Azure podem ser causados por vários problemas diferentes:
    - Problemas isolados que originalmente aparecem sistêmicos para todos os Mobile Engagement do Azure
    - Problemas conhecidos causados por paralisações de servidor (nem sempre é exibido no status do servidor):
Atrasos de agendamento, erros de direcionamento, problemas de atualização de notificação, interrupção da coleta de estatísticas, Push para de funcionar, Interrupção do funcionamento da API, não é possível criar novos aplicativos ou usuários, erros de DNS e erros de tempo limite na interface do usuário, API ou aplicativos em um dispositivo.
1.    Interrupções de dependência de nuvem
<Status do serviço do Azure >< Status do Amazon Web Service (AWS)>
2.    Interrupções de dependência de Serviços de notificação de envio (PNS)
<Google - serviço><Apple - serviço><Android - GCM Google><Android - Amazon ADM><Apple - APNS><Windows Phone - WNS><Windows Phone - MPNS><Windows - WNS>
3.    Interrupções de loja de aplicativos
<GooglePlay><iTunes><Windows Phone Store><Windows Store>

    - Interrupções de dependência de nuvem
[Status do serviço do Azure]( http://azure.microsoft.com/status/), [Status de serviços Web da Amazon (AWS)]( http://status.aws.amazon.com/) 
    - Interrupções de dependência de Serviços de notificação de envio (PNS)
[Google - serviço](http://www.google.com/appsstatus#hl=en&v=status), [Apple - serviço]( http://www.apple.com/support/systemstatus/), [Android - GCM Google]( http://developer.android.com/google/gcm/index.html), [Android - Amazon ADM]( https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APNS]( https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)[Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone - MPNS](http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105). aspx), [Windows - WNS](https://developer.windows.com/)
    - Interrupções de loja de aplicativos
[GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone Store](http://www.windowsphone.com/), [Windows Store](http://windows.microsoft.com/)

*Para testar para ver se o problema é sistemático, você pode testar a mesma função de um serviço diferente:*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*Para testar se o problema afeta somente a interface do usuário ou a API:*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[Documentação da API][Link 4], [Documentação de interface do usuário][Link 1]
	
*Para testar se o problema é com a rede de telefone celular:*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*Para testar se o problema é com o dispositivo:*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[Documentação de interface do usuário - Configurações][Link 1]

*Para testar se o problema é com seu aplicativo:*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

Para testar se o problema é com as atualizações de sistema operacional para Dispositivos do usuário final, que exigem uma atualização do SDK para resolver:

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[Guia de solução de problemas - SDK][Link 2]
 
<a name="#SERVICE2">**Sintoma:**
2. Conectividade e problemas de informações incorretas:</a>

- Problemas de logon na interface de usuário do Mobile Engagement do Azure.
- Erros de conexão com a API do Mobile Engagement do Azure.
- Problemas ao carregar marcas de informações de aplicativo por meio da API do dispositivo.
- Problemas de download de logs ou dados exportados do Mobile Engagement do Azure.
- Informações incorretas mostradas na interface do usuário do Mobile Engagement do Azure.
- Informações incorretas mostradas nos logs do Mobile Engagement do Azure.

**Causas:**

- Para problemas de conectividade com o Mobile Engagement do Azure:
    - Confirme que sua conta de usuário tem permissões suficientes para executar a tarefa.
    - Confirme que o problema não está restrito a um computador ou rede local.
    - Confirme que o serviço do Mobile Engagement do Azure não tem nenhuma interrupção relatada.
    - Confirme que os arquivos de marca de informações do aplicativo sigam todas estas regras:
        - Usem apenas o conjunto de caracteres UTF8 (não há suporte para o conjunto de caracteres ANSI).
        - Usem uma vírgula "," como o caractere separador (você pode abrir um serviço de solicitação para solicitar a alteração do o caractere separador.csv de uma vírgula "," para um outro caractere, como um ponto e vírgula ";").
        - Usem letras maiúsculas para valores booleanos "TRUE" e "FALSE".
        - Usem um arquivo que seja menor do que o tamanho máximo de 35 MB.

**Consulte também:**

[Documentação da API][Link 4], [Documentação de interface do usuário - página inicial][Link 1]
 
<a name="#SERVICE3">**Sintoma:**
3. Solicitações de recursos:</a>

- O recurso que você deseja usar ainda não parece existir no Mobile Engagement do Azure.

**Causas:**

Para sugerir um novo recurso para o Mobile Engagement do Azure que ainda não existe:
    - Abra uma solicitação de serviço do Mobile Engagement do Azure com tantos detalhes quanto possível sobre o novo recurso que você gostaria de ver no Mobile Engagement do Azure.
 
## <a name="#SDK">SDK</a>

Problemas de como o Mobile Engagement do Azure se integra ao seu aplicativo.

**Lista de sintomas:**

1. <a href="#SDK1">Problemas com o SDK do Mobile Engagement do Azure descobertos por uma falha em outra área do aplicativo</a>
2. <a href="#SDK2">Problemas de codificação avançados</a>
3. <a href="#SDK3">Problemas de falhas de aplicativo</a>
4. <a href="#SDK4">Falhas de carregamento da loja de aplicativos</a>
 
<a name="#SDK1">**Sintoma:**
1. Problemas com o SDK do Mobile Engagement do Azure descobertos por uma falha em outra área do aplicativo:</a>

- Falha de coleta de dados da interface do usuário (em análise, monitoramento, segmentação ou painéis).
- Falhas nos envios (Envios não funcionam no aplicativo, fora do aplicativo, ou ambos).
- Falhas de recursos avançados (rastreamento, localização geográfica ou envios em plataformas específicas que não funcionam).
- Falhas de API (Falha de API geralmente silenciosa, sem mensagens de erro).
- Falhas de serviço (nenhum funcionamento do Mobile Engagement do Azure para seu aplicativo).

**Causas:**

- A maioria dos problemas que precisam ser resolvidos com o SDK do Mobile Engagement do Azure será descoberto por uma falha em seu aplicativo (por exemplo, uma falha de coleta de dados da interface do usuário, falha de envio, falha de recurso avançado, falha de API, falhas do aplicativo ou interrupção de serviço aparentes).  
- Se um recurso específico do Mobile Engagement do Azure nunca funcionou em seu aplicativo antes, você precisará concluir a integração. 
- Se um recurso específico do Azure Mobile contrato estava trabalhando e interrompido, talvez seja necessário atualizar para a última versão com o SDK do Mobile Engagement do Azure. Lembre-se de que há uma versão diferente do SDK do Mobile Engagement do Azure para cada plataforma suportada pelo Mobile Engagement do Azure (Android, iOS, Web, Windows e Windows Phone).

*Integração do SDK:* 

- O Mobile Engagement do Azure não está corretamente integrado no SDK (análise).
- O Reach não está corretamente integrado no SDK (Envio no aplicativo e fora do aplicativo).
- Certificado incorreto ou expirado PROD x DEV (somente iOS).
- GCM ou ADM não estão corretamente integrados no SDK (somente Android - Envio de serviço específico).
- Rastreamento não está corretamente integrado no SDK (Instalar rastreamento de loja).
- Localização lenta ou localização de GPS não estão corretamente integrados no SDK (direcionamento por localização geográfica).
[Documentação do SDK - Guias de integração][Link 5], [Guia de solução de problemas - Push][Link 2]

*Atualização do SDK:*

- Necessidade de atualizar o SDK para resolver problemas com versões anteriores do SDK (geralmente relacionadas às versões mais recentes do sistema operacional do dispositivo).
- Desinstale todas as versões anteriores do aplicativo do dispositivo e reinstale a versão mais recente do seu aplicativo, registre novamente o ID do dispositivo da interface do usuário do Mobile Engagement do Azure para confirmar que o dispositivo está usando a versão mais recente do seu aplicativo.
[Documentação do SDK - Notas de versão](http://go.microsoft.com/fwlink/?LinkId= 525554), [Documentação do SDK - Guias de atualização](http://go.microsoft.com/fwlink/?LinkId= 525554), [Documentação de interface do usuário - Configurações][Link 1]

*Outros problemas do SDK:*

- Erros nas seções de código relacionadas ao Mobile Engagement do Azure podem causar o não funcionamento do Mobile Engagement do Azure.
- Erros no manifesto do aplicativo "Androidmanifest.xml" podem causar o não funcionamento do Mobile Engagement do Azure (somente Android).
- Um problema comum com a integração do SDK e 
- o uso da API é confundir a chave do SDK e a chave de API.
[Conceitos - Glossário][Link 6]
 
<a name="#SDK2">**Sintoma:**
2. Problemas de codificação avançados:</a>

-  O código específico da plataforma não relacionado diretamente ao Mobile Engagement do Azure pode causar problemas no iOS, Android e Windows Phone.

**Causas:**

- Muitos problemas de codificação avançada com o Mobile Engagement do Azure são causados por código específico de plataforma escrito incorretamente, não diretamente relacionado ao Mobile Engagement do Azure. Você precisará consultar a documentação específica para a plataforma que você está desenvolvendo para além da documentação do Mobile Engagement do Azure (Android, iOS, Web, Windows e Windows Phone).
- "Categorias" não corretamente configuradas, impedem a vinculação de uma notificação para outro local dentro ou fora do aplicativo (somente Android). 
- "UIKit.framework" não definido para "opcional" em seu código do iOS, mostra um "Símbolo não encontrou o erro" e/ou falha em dispositivos mais antigos do iOS (somente iOS).
- Certificados expirados ou não corretamente usando a versão DEV ou PROD do cert, causam problemas de envio (somente iOS).
- Existem algumas limitações inerentes a uma plataforma que o Mobile Engagement do Azure não pode controlar (assim como o system center funciona para envio de fora do aplicativo no Android e iOS).
- Mobile Engagement do Azure publica uma lista completa dos pacotes internos usados pelo Mobile Engagement do Azure para iOS e Android para referência. Tenha em mente que alguns recursos do Mobile Engagement do Azure são específicos para a plataforma (Android, iOS, Web, Windows e Windows Phone).
- Os SDKs para cada plataforma são escritos nas seguintes linguagens de programação:
    -     SDK do Android escrito em Java
    -     SDK do iOS escrito em objective C
    -     SDK da web escrito em JavaScript
    -     SDK do Windows escrito em C# e JavaScript
    -     SDK do Windows Phone escrito em C# e JavaScript

**Consulte também:**

 -  [Guia de solução de problemas - Push][Link 2], [Documentação do SDK - Notas de versão][Link 5], [Documentação do SDK - Guias de atualização][Link 5], [Documentação do SDK - Android - Visão geral da documentação técnica do Mobile Engagement do Azure][Link 5], [Documentação do SDK - iOS - Visão geral da documentação técnica do Mobile Engagement do Azure][Link 5], [Documentação do SDK - iOS - Como preparar seu aplicativo para notificações por Push da Apple][Link 5], [Desenvolvedor Android](https://developer.android.com/), [Desenvolvedor iOS](https://developer.apple.com/), [Desenvolvedor Windows](https://developer.windows.com/) 
 
<a name="#SDK3">**Sintoma:**
3.    Problemas de falhas de aplicativo</a>

- O aplicativo falha no dispositivo dos usuários finais.

**Causas:**

- Informações de falha podem ser exibidas na interface do usuário de análise ou na API de análise.
- Você pode encontrar a ID de dispositivo do seu dispositivo de teste e executar a mesma ação que causou a falha do aplicativo para um usuário final para ajudar a identificar a causa de sua falha.
- Problemas conhecidos com o SDK do Mobile Engagement do Azure que causam falhas em aplicativos algumas vezes são resolvidos com a atualização para a versão mais recente do SDK, então verifique as notas de versão sobre a sua plataforma ao investigar falhas.

**Consulte também:**

- [Conceitos - Perguntas frequentes][Link 6], [Conceitos - Glossário][Link 6], [Documentação da API - API de análises - Falhas][Link 4], [Documentação de interface do usuário - Análise - Falhas][Link 1], [Documentação de interface do usuário - Configurações][Link 1], [Documentação do SDK - Notas de versão][Link 5], [Documentação do SDK - Guias de atualização][Link 5]

<a name="#SDK4">**Sintoma:**
4. Falhas de carregamento da loja de aplicativos</a>

- Erros relacionados ao carregamento da versão mais recente do seu aplicativo para as lojas do iTunes, GooglePlay, Windows ou Windows Phone.

**Causas:**

- As lojas de aplicativos algumas vezes bloqueiam os aplicativos com determinados recursos habilitados (iTunes store impede o uso de IDFV em aplicativos no repositório e a GooglePlay store impede o compartilhamento de informações do aplicativo entre os aplicativos). 
- Certifique-se de verificar as notas de versão sobre a plataforma e a versão atual do SDK se você tiver dificuldade para carregar um aplicativo para a loja.

**Consulte também:**

- [Documentação do SDK - Notas de versão][Link 5], [Documentação do SDK - Guias de atualização][Link 5] 

## <a name="#SR">Informações de solução de problemas de SR</a>

Forneça as informações a seguir quando você abre qualquer solicitação de serviço do Mobile Engagement do Azure:
 
**IDs: Forneça os identificadores aplicáveis relacionados ao seu problema:**

    - App ID
    - Campaign ID
    - Device ID
    - User ID
    - Username
    - App Info Tag
 
**Erros: Forneça qualquer informação de erro aplicável relacionada ao seu problema:**

    - The name of the API or UI section where the issue occurs
    - The text of any error message you receive
    - The results of any tests you have performed from the troubleshooting guides
 
-    [Guia de Solução de Problemas:](http://go.microsoft.com/fwlink/?LinkId=524382)


**Código: Forneça as informações de codificação aplicáveis relacionadas ao seu problema:**

    - The SDK version and platform of your app (Android SDK 2.4.1, iOS 1.16.2, etc.)
    - The download location of your production app (or the APK/TGZ files of your development app)
    - The "AndroidManifest.xml" and/or any code snippet from your app related to Azure Mobile Engagement (for advanced troubleshooting)

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
