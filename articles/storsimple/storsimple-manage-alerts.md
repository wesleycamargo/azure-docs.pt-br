<properties 
   pageTitle="Exibir e gerenciar alertas do StorSimple | Microsoft Azure"
   description="Descreve as condições de alerta e a gravidade do StorSimple, como configurar notificações de alerta e como usar o serviço StorSimple Manager para gerenciar alertas."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/15/2015"
   ms.author="v-sharos" />

# Use o serviço StorSimple Manager para exibir e gerenciar alertas do StorSimple

## Visão geral

A guia **Alertas** no serviço StorSimple Manager fornece uma maneira para analisar e limpar os alertas relacionados ao dispositivo StorSimple em tempo real. Nessa guia, você pode monitorar centralmente a integridade de seus dispositivos StorSimple e a solução geral do Microsoft Azure StorSimple.

Este tutorial descreve as condições de alerta comuns, níveis de severidade do alerta e como configurar notificações de alerta. Além disso, ele inclui tabelas de referência rápida de alerta, que permitem que você localize um alerta específico rapidamente e responda o mesmo adequadamente.

![Página de alertas](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## Condições comuns de alerta

Seu dispositivo StorSimple gera alertas em resposta a uma variedade de condições. A seguir estão os tipos mais comuns de condições de alerta:

- **Problemas de hardware**– Esses alertas informam sobre a integridade de seu hardware. Eles permitem saber se as atualizações de firmware são necessárias, se uma interface de rede está com problemas ou se há um problema com uma de suas unidades de dados.

- **Problemas de conectividade**– Esses alertas ocorrem quando há dificuldade para transferir dados entre fontes de dados. Podem ocorrer problemas de comunicação durante a transferência de dados de e para a conta de armazenamento do Azure ou devido à falta de conectividade entre os dispositivos e o serviço StorSimple Manager. Podem ocorrer durante a configuração, backup ou os processos de recuperação. Problemas de comunicação são alguns dos mais difíceis de corrigir porque há muitos pontos de falha. Você deve sempre verificar primeiro se a conectividade de rede e o acesso à Internet estão disponíveis antes de continuar com a solução de problemas mais avançados.

- **Problemas de desempenho**– Esses alertas são causados quando o sistema não está sendo executado corretamente, como quando ele está sob uma carga pesada.

Além disso, você pode ver alertas relacionados à segurança, atualizações ou falhas no trabalho.

## Níveis de severidade do alerta

Alertas têm diferentes níveis de severidade, dependendo do impacto que terá a situação de alerta e a necessidade de uma resposta ao alerta. Os níveis de severidade são:

- **Crítico**– Este alerta ocorre em resposta a uma condição que está afetando o desempenho correto do seu sistema. Uma ação é necessária para garantir que o serviço StorSimple não seja interrompido.

- **Aviso**– Essa condição pode se tornar crítica se não for resolvida. Você deve investigar a situação e realizar qualquer ação necessária para resolver o problema.

- **Informações**– Esse alerta contém informações que podem ser úteis para controlar e gerenciar seu sistema.

## Definição de configurações de alerta

Você pode escolher se deseja ser notificado por email das condições de alerta para cada um dos dispositivos StorSimple. Além disso, você pode identificar outros destinatários da notificação de alerta, digitando seus endereços de email na caixa **OUTROS DESTINATÁRIOS DE EMAIL**, separados por ponto e vírgula.

>[AZURE.NOTE]Você pode inserir um máximo de 20 endereços de email por dispositivo.

Depois de habilitar a notificação por email para um dispositivo, os membros da lista de notificação receberão uma mensagem de email sempre que um alerta crítico ocorrer. As mensagens serão enviadas por **storsimple-alerts-noreply@mail.windowsazure.com* e descreverão a condição de alerta. Os destinatários podem clicar em Cancelar inscrição para serem removidos da lista de notificação de email.

#### Para habilitar a notificação por email de alertas para um dispositivo

1. Vá em **Dispositivos** > **Configurar** para o dispositivo.

2. Em **Configurações de alerta**, defina o seguinte:

    1. No campo **ENVIAR NOTIFICAÇÃO POR EMAIL**, selecione **SIM**.

    2. No campo **ADMINISTRADORES DE SERVIÇO DE EMAIL**, selecione **SIM** se desejar que o administrador de serviço e todos os coadministradores recebam as notificações de alerta.

    3. No campo **OUTROS DESTINATÁRIOS DE EMAIL** insira os endereços de email de todos os outros destinatários que devem receber as notificações de alerta. Insira os nomes no formato **someone@somewhere.com*. Use ponto e vírgula para separar os endereços de email. Você pode configurar um máximo de 20 endereços de email por dispositivo.

    ![Página de configuração de notificação de alertas](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig.png)

3. Para enviar uma notificação de email de teste, clique no ícone de seta ao lado de **ENVIAR EMAIL DE TESTE**. O serviço StorSimple Manager exibirá mensagens de status conforme ele encaminha a notificação de teste.

4. Quando a seguinte mensagem for exibida, clique em **OK**.

    ![Email de notificação de teste de alertas enviado](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE]Se a mensagem de notificação de teste não pode ser enviada, o serviço StorSimple Manager exibirá uma mensagem apropriada. Isso pode ocorrer devido ao tráfego ou outros problemas de rede. Clique em **OK**, aguarde alguns minutos e, em seguida, tente enviar a mensagem de notificação de teste novamente.

## Exibir e controlar os alertas

O painel do serviço StorSimple Manager fornece uma visão rápida do número de alertas em seus dispositivos, divididos por nível de severidade.

![Painel de alertas](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Clicar no nível de severidade abre a guia **Alertas**. Os resultados incluem apenas os alertas que correspondem a esse nível de severidade.

![Relatórios de alerta limitados ao tipo de alerta](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Clicar em um alerta na lista fornece detalhes adicionais para o mesmo, incluindo a última vez em que o alerta foi relatado, o número de ocorrências do alerta no dispositivo e a ação recomendada para resolver o alerta. Se for um alerta de hardware, ele também identificará o componente de hardware.

![Exemplo de alerta de hardware](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Você pode copiar os detalhes do alerta para um arquivo de texto se desejar controlar a resolução do alerta em um arquivo separado ou se você precisar enviar as informações ao Suporte da Microsoft. Depois de resolver a condição de alerta, você deve limpar o alerta do dispositivo selecionando o alerta na guia **Alertas** e clicando em **Limpar**. Para limpar vários alertas, pressione a tecla Ctrl ao selecionar os alertas e, em seguida, clique em **Limpar**. Observe que alguns alertas são removidos automaticamente quando o problema é resolvido ou quando o sistema atualiza o alerta com novas informações.

Ao clicar em **Limpar**, você terá a oportunidade de fornecer comentários sobre o alerta e as etapas que você seguiu para resolver o problema. Alguns eventos serão removidos pelo sistema se outro evento for disparado com novas informações. Nesse caso, você verá a seguinte mensagem de erro.

![Limpar mensagem de alerta](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## Classificação e análise de alertas

Talvez seja mais eficiente executar relatórios sobre alertas, para que você possa analisá-los e limpá-los em grupos. Além disso, a guia **Alertas** pode exibir até 250 alertas. Se você excedeu o número de alertas, nem todos os alertas serão exibidos no modo de exibição padrão. Você pode combinar os campos a seguir para personalizar quais alertas são exibidos:

- **Status**– Você pode exibir alertas **Ativos** ou **Removidos**. Alertas ativos ainda estão sendo disparados em seu sistema, embora os alertas removidos tenham sido limpos manualmente por um administrador ou por meio de programação porque o sistema atualizou a condição de alerta com novas informações.

- **Severidade**– Você pode exibir alertas de todos os níveis de severidade (críticos, avisos, informações) ou apenas uma determinada severidade, como, por exemplo, somente alertas críticos.

- **Fonte**– Você pode exibir alertas de todas as fontes ou limitar os alertas para aqueles que vêm de serviço ou de um ou todos os dispositivos.

- **Intervalo de tempo**– Especificando os carimbos de data e hora **De** e **Para**, você pode examinar os alertas durante o período de tempo desejado.

## Referência rápida de alertas

As tabelas a seguir listam alguns dos alertas do Microsoft Azure StorSimple que você pode encontrar, bem como recomendações e informações adicionais quando disponíveis. Os alertas do dispositivo StorSimple se encaixam em uma das seguintes categorias:

- [Alertas de conectividade de nuvem](#cloud-connectivity-alerts)

- [Alertas de cluster](#cluster-alerts)

- [Alertas de recuperação de desastres](#disaster-recovery-alerts)

- [Alertas de hardware](#hardware-alerts)

- [Alertas de falha nos trabalhos](#job-failure-alerts)

- [Alertas de desempenho](#performance-alerts)

- [Alertas de segurança](#security-alerts)

- [Alertas do pacote de suporte](#support-package-alerts)

- [Alertas de teste](#test-alerts)

- [Alertas de atualização](#update-alerts)

### Alertas de conectividade de nuvem

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|A conectividade com <*nome de credencial da nuvem*> não pode ser estabelecida.|Não é possível conectar-se à conta de armazenamento.|Parece que pode haver um problema de conectividade com o seu dispositivo. Execute o cmdlet `Test-HcsmConnection` na Interface do Windows PowerShell para StorSimple em seu dispositivo para identificar e corrigir o problema. Se as configurações estiverem corretas, o problema pode ser com as credenciais da conta de armazenamento para o qual o alerta foi gerado. Nesse caso, use o cmdlet `Test-HcsStorageAccountCredential` para determinar se há problemas que você possa resolver.<ul><li>Verifique suas configurações de rede.</li><li>Verifique suas credenciais de conta de armazenamento.</li></ul>|
|Não recebemos uma pulsação do dispositivo para os últimos <*número*> minutos.|Não é possível conectar-se ao dispositivo.|Parece que há um problema de conectividade com o seu dispositivo. Use o cmdlet `Test-HcsmConnection` na Interface do Windows PowerShell para StorSimple em seu dispositivo para identificar e corrigir o problema, ou entre em contato com o seu administrador de rede.|

### Comportamento de StorSimple quando a conectividade de nuvem falha

O que acontece se a conectividade de nuvem falhar para o dispositivo StorSimple que está em execução na produção?

Se a conectividade de nuvem falhar em seu dispositivo de produção do StorSimple, dependendo do estado do seu dispositivo, poderá ocorrer o seguinte:

- **Para os dados locais em seu dispositivo**: não haverá interrupção e as leituras continuarão a ser atendidas. No entanto, conforme o número de E/Ss pendentes aumenta e excede um limite, as leituras podem começar a falhar. 

	Dependendo da quantidade de dados nas camadas locais do seu dispositivo, as gravações também continuarão a ocorrer para as primeiras horas após a interrupção na conectividade de nuvem. As gravações então desacelerarão e acabarão por começar a falhar se a conectividade de nuvem for interrompida por várias horas.

 
- **Para os dados na nuvem**: para a maioria dos erros de conectividade de nuvem, é retornado um erro. Quando a conectividade for restaurada, as E/Ss são reiniciadas sem que o usuário precise colocar o volume online. Em casos raros, pode haver necessidade de intervenção do usuário para deixar o volume online novamente no Portal do Azure.
 
- **Para instantâneos de nuvem em andamento**: a operação é repetida algumas vezes dentro de 4 a 5 horas e, se a conectividade não for restaurada, os instantâneos de nuvem falharão.


### Alertas de cluster

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Dispositivo realizou failover para <*nome do dispositivo*>.|Dispositivo em modo de manutenção.|Dispositivo falhou após entrar ou sair do modo de manutenção. Isso é normal e nenhuma ação é necessária. Após a confirmação deste alerta, exclua-o da página de alertas.|
|Dispositivo realizou failover para <*nome do dispositivo*>.|Firmware ou software do dispositivo atualizado.|Ocorreu um failover de cluster devido a uma atualização. Isso é normal e nenhuma ação é necessária. Após a confirmação deste alerta, exclua-o da página de alertas.|
|Dispositivo realizou failover para <*nome do dispositivo*>.|Controlador desligado ou reiniciado.|Dispositivo falhou porque o controlador ativo foi desligado ou reiniciado por um administrador. Nenhuma ação é necessária. Após a confirmação deste alerta, exclua-o da página de alertas.|
|Dispositivo realizou failover para <*nome do dispositivo*>.|Failover planejado.|Verifique se esse foi um failover planejado. Após a execução da ação adequada, exclua este alerta da página de alertas.|
|Dispositivo realizou failover para <*nome do dispositivo*>.|Failover não planejado.|O StorSimple foi criado para se recuperar automaticamente de failovers não planejados. Se você vir um grande número desses alertas, entre em contato com o Suporte da Microsoft.|
|Dispositivo realizou failover para <*nome do dispositivo*>.|Outro/causa desconhecida.|Se você vir um grande número desses alertas, entre em contato com o Suporte da Microsoft. Depois que o problema for resolvido, exclua este alerta da página de alertas.|

### Alertas de recuperação de desastres

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|As operações de recuperação não puderam restaurar todas as configurações para esse serviço. Os dados de configuração do dispositivo estão em um estado inconsistente para alguns dispositivos.|Inconsistência de dados detectada após a recuperação de desastres.|Os dados criptografados no serviço não estão sincronizados com os do dispositivo. Autorizar o dispositivo <*nome do dispositivo*> do StorSimple Manager a iniciar o processo de sincronização. Use a Interface do Windows PowerShell para StorSimple para executar o cmdlet `Restore-HcsmEncryptedServiceData` no dispositivo <*nome do dispositivo*>, fornecendo a senha antiga como entrada para este cmdlet para restaurar o perfil de segurança. Em seguida, execute o cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` para atualizar a chave de criptografia de dados de serviço. Após a execução da ação adequada, exclua este alerta da página de alertas.|
|O serviço falhou para um data center secundário devido a uma falha inesperada.|Outro/causa desconhecida.|É necessário verificar as configurações no Gerenciador do StorSimple para continuar. Após a execução da ação adequada, exclua este alerta da página de alertas. Para obter mais informações sobre o StorSimple Manager, consulte [Usar o serviço StorSimple Manager para administrar o dispositivo StorSimple](storsimple-manager-service-administration.md).|

### Alertas de hardware

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|O componente de hardware <*ID do componente*> relata o status como <*status*>.||Condições temporárias, às vezes, podem causar esses alertas. Nesse caso, esse alerta será automaticamente removido após algum tempo. Se o problema persistir, contate o Suporte da Microsoft.|
|Controlador passivo funcionando incorretamente.|O controlador (secundário) passivo não está funcionando.|O dispositivo está funcionando, mas um dos controladores está funcionando incorretamente. Tente reiniciar o controlador em questão. Se o problema não for resolvido, contate o Suporte da Microsoft.|

### Alertas de falha nos trabalhos

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|O backup de <*ID do grupo de volumes de origem*> falhou.|Falha no trabalho de backup.|Problemas de conectividade podem estar impedindo a operação de backup de ser concluída com êxito. Se não houver nenhum problema de conectividade, talvez você tenha atingido o número máximo de backups. Exclua todos os backups que não são mais necessários e repita a operação. Após a execução da ação adequada, exclua este alerta da página de alertas.|
|A clonagem de <*IDs do elemento de backup da fonte*> para <*números de série do volume de destino*> falhou.|Falha no trabalho de clone.|Atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido, é possível que problemas de conectividade de nuvem estejam impedindo a operação de clonagem de ser concluída com êxito. Se não houver nenhum problema de conectividade, talvez você tenha atingido o limite de armazenamento. Exclua todos os backups que não são mais necessários e repita a operação. Depois que você executou a ação apropriada para resolver o problema, exclua este alerta da página de alertas.|
|A restauração de <*IDs de elemento de backup da fonte*> falhou.|Falha no trabalho de restauração.|Atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido, é possível que problemas de conectividade de nuvem estejam impedindo a operação de restauração de ser concluída com êxito. Se não houver nenhum problema de conectividade, talvez você tenha atingido o limite de armazenamento. Exclua todos os backups que não são mais necessários e repita a operação. Depois que você executou a ação apropriada para resolver o problema, exclua este alerta da página de alertas.|

### Alertas de desempenho

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|A carga do dispositivo excedeu o <*limite*>.|Mais lento do que os tempos de resposta esperados.|O dispositivo está relatando a utilização sob uma pesada carga de entrada/saída. Isso pode fazer com que o dispositivo não funcione tão bem quanto deveria. Examine as cargas de trabalho que você atribuiu ao dispositivo e determine se há alguma que possa ser movida para outro dispositivo ou que não são mais necessárias.|

### Alertas de segurança

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Sessão do Suporte da Microsoft iniciada.|Terceiros acessaram a sessão de suporte.|Confirme se este acesso é autorizado. Após a execução da ação adequada, exclua este alerta da página de alertas.|
|A senha para <*elemento*> expirará em <*período de tempo*>.||Altere sua senha antes que ela expire.|
|Informações de configuração de segurança ausentes para <*ID do elemento*>.||Os volumes associados a esse contêiner de volume não podem ser usados para replicar a configuração do StorSimple. Para garantir que seus dados são armazenados com segurança, recomendamos que você exclua o contêiner de volume e todos os volumes associados ao contêiner de volume. Após a execução da ação adequada, exclua este alerta da página de alertas.|
|<*número*> tentativas de logon falharam para <*ID do elemento*>.|Falha após várias tentativas de logon.|O dispositivo pode estar sendo atacado ou um usuário autorizado está tentando se conectar com uma senha incorreta.<ul><li>Entre em contato com os usuários autorizados e verifique se essas tentativas foram de uma origem legítima. Se você continuar vendo um grande número de falhas nas tentativas de logon, considere desabilitar o gerenciamento remoto e entrar em contato com o administrador da rede. Depois de tomar as medidas adequadas, exclua este alerta da página de alertas.</li><li>Verifique se as instâncias do Snapshot Manager estão configuradas com a senha correta. Após a execução da ação adequada, exclua este alerta da página de alertas.</li></ul>|
|Uma ou mais falhas ocorreram ao alterar a chave de criptografia de dados de serviço.||Ocorreram erros durante a alteração da chave de criptografia de dados de serviço. Depois de resolver as condições de erro, execute o cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` da Interface do Windows PowerShell para StorSimple no dispositivo para atualizar o serviço. Se esse problema persistir, entre em contato com o suporte da Microsoft. Depois de resolver o problema, exclua este alerta da página de alertas.|

### Alertas do pacote de suporte

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Falha na criação do pacote de suporte.|O StorSimple não pôde gerar o pacote.|Repita a operação. Se o problema persistir, contate o Suporte da Microsoft. Depois de resolver o problema, exclua este alerta da página de alertas.|

### Alertas de teste

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Esta é uma mensagem de teste enviada do seu dispositivo StorSimple. O administrador do StorSimple adicionou você como um destinatário de notificações de alerta para o dispositivo: <*nome do dispositivo*>.|Email de notificação de alerta de teste.|Se você achar que recebeu esta mensagem por engano, contate o administrador do StorSimple.|

### Alertas de atualização

|Texto de alerta|Evento|Mais informações / ações recomendadas|
|:---|:---|:---|
|Hotfix instalado.|Atualização de software/firmware concluída.|O hotfix foi instalado com êxito em seu dispositivo.|
|Atualizações manuais disponíveis.|Notificação de atualizações disponíveis.|Use a Interface do Windows PowerShell para StorSimple em seu dispositivo para instalar essas atualizações.|
|Novas atualizações disponíveis.|Notificação de atualizações disponíveis.|Você pode instalar essas atualizações na página **Manutenção** ou usando a Interface do Windows PowerShell para StorSimple no dispositivo.|
|Falha ao instalar atualizações.|As atualizações não foram instaladas com êxito.|O sistema não pôde instalar as atualizações. Você pode instalar essas atualizações na página **Manutenção** ou usando a Interface do Windows PowerShell para StorSimple no dispositivo. Se o problema persistir, contate o Suporte da Microsoft.|
|Não é possível verificar novas atualizações automaticamente.|Falha na verificação automática.|Você pode verificar manualmente se há novas atualizações na página **Manutenção**.|
|Novo agente WUA disponível.|Notificação de atualização disponível.|Baixe o Windows Update Agent mais recente e instale-o a partir da interface do Windows PowerShell.|
|A versão do componente de firmware <*ID do componente*> não corresponde ao hardware.|As atualizações de firmware não foram instaladas com êxito.|Entre em contato com o Suporte da Microsoft.|

## Próximas etapas

- Saiba mais sobre [erros do StorSimple](storsimple-troubleshoot-operational-device.md).
- Saiba mais sobre o [uso do serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=Sept15_HO3-->