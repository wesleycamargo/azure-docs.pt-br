<properties
	pageTitle="Migrar dos Serviços Móveis para o Aplicativo Móvel do Serviço de Aplicativo"
	description="Saiba como migrar facilmente seu aplicativo de Serviços Móveis para um Aplicativo Móvel do Serviço de Aplicativo"
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="adrianhall"/>

# <a name="article-top"></a>Migrar seu Serviço Móvel do Azure existente para o Serviço de Aplicativo do Azure

Com a [disponibilidade geral do Serviço de Aplicativo do Azure], sites de Serviços Móveis do Azure podem ser migrados facilmente no local para aproveitar todos os recursos do Serviço de Aplicativo do Azure. Este documento explica o que esperar ao migrar seu site dos Serviços Móveis do Azure para o Serviço de Aplicativo do Azure.

## <a name="what-does-migration-do"></a>O que a migração faz ao seu site

A migração do Serviço Móvel do Azure tornará seu serviço móvel um aplicativo do [Serviço de Aplicativo do Azure] sem afetar o código de forma alguma. Os hubs de notificação, a conexão de dados do SQL, as configurações de autenticação, os trabalhos agendados e nome de domínio permanecerão inalterados. Os clientes móveis que estejam usando o serviço móvel do Azure continuarão funcionando normalmente. A migração reiniciará o serviço depois que ele for transferido para o Serviço de Aplicativo do Azure.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Por que você deve migrar seu site

A Microsoft está recomendando que você migre o serviço móvel do Azure para aproveitar os recursos do Serviço de Aplicativo do Azure, incluindo:

  *  Novos recursos de host, incluindo [WebJobs] e [nomes de domínio personalizados].
  *  Conectividade para seus recursos locais usando [VNet], além de [Conexões híbridas].
  *  Monitorando e solucionando problemas com o New Relic ou o [Application Insights].
  *  Ferramentas do DevOps internas, incluindo [slots de preparo], reversão e testes em produção.
  *  [Dimensionamento automático], balanceamento de carga e [monitoramento de desempenho].

Para obter mais informações sobre os benefícios do Serviço de Aplicativo do Azure, consulte o [Serviços Móveis vs. Serviço de Aplicativo].

## <a name="why-not-migrate"></a>Por que você não deve migrar seu site

Há alguns motivos pelos quais você não deve migrar seus Serviços Móveis agora:

  *  Você está atualmente em um período ocupado e não pode reiniciar um site no momento.
  *  Você não deseja afetar seu site de produção antes de testar o processo de migração.
  *  Você tem vários sites nos tipos de preços Básico ou Gratuito e não deseja migrar todos os sites ao mesmo tempo.

Você está em um período ocupado, assim, planeje a migração durante uma janela de manutenção programada. O processo de migração reinicia seu site como parte do processo e seus usuários podem observar essa interrupção momentânea de disponibilidade.

Há soluções alternativas para a maioria dos itens na lista. Consulte a seção [Antes de começar a](#before-you-begin) abaixo para obter detalhes.

## <a name="before-you-begin"></a>Antes de começar

Siga as etapas apresentadas antes de migrar seu site:

  *  [Fazer backup dos scripts do serviço móvel] e do Banco de Dados SQL
  *  (Opcional) Elevar o tipo de Serviço Móvel para Standard

Se você quiser testar o processo de migração antes de migrar seu site de produção, duplique seu serviço móvel do Azure de produção (completo com uma cópia da fonte de dados) e teste a migração em relação à nova URL. Você também precisará de uma implementação de cliente de teste que aponte para o site de teste para testar adequadamente o site migrado.

### <a name="opt-raise-service-tier"></a>(Opcional) Elevar o tipo de Serviço Móvel para Standard

Todos os sites de Serviços Móveis que compartilham um plano de hospedagem são migrados ao mesmo tempo. Os Serviços Móveis nos tipos de preços Básico ou Gratuito compartilham um plano de hospedagem com outros serviços no mesmo tipo de preço e [Região do Azure]. Se seu Serviço Móvel estiver funcionando no tipo de preço Standard, ele estará localizado em seu próprio plano de hospedagem. Se você quiser migrar individualmente sites nos tipos de preços Básico ou Gratuito, atualize temporariamente o tipo de preço do serviço móvel para Standard. Você pode fazer isso no menu ESCALA para seu serviço móvel.

  1.  Faça logon no [Portal clássico do Azure].
  2.  Selecione o seu serviço móvel.
  3.  Selecione a guia **ESCALAR VERTICALMENTE**.
  4.  Em **Tipo de Serviço Móvel**, clique no tipo **STANDARD**. Clique no ícone **SALVAR** na parte inferior da página.

Lembre-se de definir o tipo de preço para uma configuração apropriada após a migração.

## <a name="migrating-site"></a>Migrando seu site

Para migrar seu site:

  1.  Faça logon no [Portal clássico do Azure].
  2.  Selecione o seu serviço móvel.
  3.  Clique no botão **Migrar para o Serviço de Aplicativo**.

    ![O botão Migração][0]

  4.  Leia o diálogo Migrar para o Serviço de Aplicativo.
  5.  Insira o nome do serviço móvel na caixa fornecida. Por exemplo, se seu nome de domínio for contoso.azure-mobile.net, digite _contoso_ na caixa fornecida.
  6.  Clique no botão de escala.

Se você estiver migrando um serviço móvel nos tipos de preços Básico ou Gratuito, todos os serviços móveis nesse tipo de preço serão migrados ao mesmo tempo. Você pode evitar isso por [elevando o serviço móvel que você está migrando](#opt-raise-service-tier) para Standard durante a migração.

Você pode monitorar o status da migração no monitor de atividade e seu site será listado como *migrando* no Portal clássico do Azure.

  ![Monitor de atividade de migração][1]

Cada migração pode levar de 3 a 15 minutos por serviço móvel que está sendo migrado. Seu site permanecerá disponível durante a migração, mas será reinicializado no final do processo de migração. O site estará disponível durante o processo de reinicialização, o que pode durar alguns segundos.

## <a name="finalizing-migration"></a>Finalizando a migração

Você deve planejar testar seu site de um cliente móvel ao final do processo de migração. Certifique-se de poder executar todas as ações comuns do cliente sem alterações ao cliente móvel. Além disso, você deve garantir que as alterações feitas para realizar a migração (como alterar o tipo de preço) sejam revertidas, se necessário.

### <a name="update-app-service-tier"></a>Selecione um tipo de preço de Serviço de Aplicativo apropriado

Você tem mais flexibilidade nos preços depois de migrar para o Serviço de Aplicativo do Azure.

  1.  Faça logon no [Portal do Azure].
  2.  Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
  3.  A folha Configurações será aberta por padrão; se não abrir, clique em **Configurações**.
  4.  Clique em **Plano de Serviço de Aplicativo** no menu Configurações.
  5.  Clique no bloco **Tipo de Preço**.
  6.  Clique no bloco apropriado aos seus requisitos, então clique em **Selecione**. Talvez seja necessário clicar em **Exibir tudo** para ver os tipos de preços disponíveis.

Como ponto de partida, recomendamos o seguinte:

| Tipo de Preço do Serviço Móvel | Tipo de Preço do Serviço de Aplicativo |
| :-------------------------- | :----------------------- |
| Grátis | F1 Gratuito |
| Basic | B1 Básico |
| Standard | S1 Standard |

Observe que há uma considerável flexibilidade na escolha do tipo de preço certo para o seu aplicativo. Consulte [Preços do Serviço de Aplicativo] para obter detalhes completos sobre os preços de seu novo Serviço de Aplicativo.

> [AZURE.TIP] O tipo Standard do Serviço de Aplicativo contém acesso a vários recursos que você talvez queira usar, incluindo [slots de preparo], backups automáticos e dimensionamento automático. Confira os novos recursos lá!

### <a name="review-migration-scheduler-jobs"></a>Examine os trabalhos do agendador migrados

Os trabalhos do agendador não estarão visíveis até cerca de 30 minutos após a migração. Os trabalhos agendados continuarão sendo executados em segundo plano. Para exibir os trabalhos agendados:

  1.  Faça logon no [Portal do Azure].
  2.  Selecione **Procurar>**, digite **Agenda** na caixa _Filtro_ e, em seguida, selecione **Coleções do Agendador**.

Há um número limitado de trabalhos do agendador gratuito disponíveis após a migração. Você deve examinar seu uso e os [Planos do Agendador do Azure].

### <a name="configure-cors"></a>Configure o CORS, se necessário

Compartilhamento de recursos entre origens é uma técnica para permitir que um site acesse uma API da Web em um domínio diferente. Se você estiver usando os Serviços Móveis do Azure com um site associado, precisará configurar CORS como parte da migração. Se você estivesse acessando os Serviços Móveis do Azure exclusivamente a partir de dispositivos móveis, não é necessário configurar o CORS, exceto em casos raros.

As configurações de CORS migradas estão disponíveis como a configuração do aplicativo **MS\_CrossDomainWhitelist**. Para migrar o site para o recurso CORS do Serviço de Aplicativo:

  1.  Faça logon no [Portal do Azure].
  2.  Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
  3.  A folha Configurações será aberta por padrão; se não abrir, clique em **Configurações**.
  4.  Clique em **CORS** no menu de API.
  5.  Insira qualquer origem permitida na caixa fornecida, pressionando Enter depois de cada uma.
  6.  Quando sua lista de origens permitidas estiver correta, clique no botão Salvar.

Essa é uma tarefa opcional, mas fornece uma melhor experiência de gerenciamento para o futuro.

> [AZURE.TIP]  Uma das vantagens de usar um Serviço de Aplicativo do Azure é que você pode executar seu site da web e serviço móvel no mesmo site. Consulte a seção de [próximas etapas](#next-steps) para obter mais informações.

### <a name="download-publish-profile"></a>Baixar um novo Perfil de publicação

O perfil de publicação do seu site é alterado durante a migração para o Serviço de Aplicativo do Azure. Você precisará de um novo perfil de publicação se pretende publicar o site de dentro do Visual Studio. Para baixar um novo perfil de publicação:

  1.  Faça logon no [Portal do Azure].
  2.  Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
  3.  Clique em **Obter perfil de publicação**.

O arquivo PublishSettings será baixado para o seu computador. Ele normalmente será chamado _sitename_. PublishSettings. Você pode importar as configurações de publicação para o seu projeto existente:

  1.  Abra o Visual Studio e o seu projeto de Serviço móvel do Azure.
  2.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar...**
  3.  Clique em **Importar**
  4.  Clique em **Procurar** e selecione seu arquivo de configurações de publicação baixado. Clique em **OK**
  5.  Clique em **Validar Conexão** para garantir que as configurações de publicação funcionem.
  6.  Clique em **Publicar** para publicar seu site.


## <a name="working-with-your-site"></a>Trabalhando com seu site após a migração

Você começará a trabalhar com o novo Serviço de Aplicativo no [Portal do Azure] após a migração. A seguir estão algumas observações sobre operações específicas que você usou para executar o [Portal Clássico do Azure], juntamente com seus equivalentes do Serviço de Aplicativo.

### <a name="publishing-your-site"></a>Baixar e publicar seu site migrado

O site está disponível via git ou ftp e pode ser publicado novamente com um número de mecanismos diferentes, incluindo WebDeploy, TFS, Mercurial, GitHub e FTP. As credenciais de implantação são migradas com o restante do site. Se você não definir suas credenciais de implantação ou não se lembrar delas, poderá redefini-las:

  1. Faça logon no [Portal do Azure].
  2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
  3. A folha Configurações será aberta por padrão; se não abrir, clique em **Configurações**.
  4. Clique em **Credenciais de implantação** no menu PUBLICAÇÃO.
  5. Insira as novas credenciais de implantação nas caixas fornecidas e clique no botão Salvar.

Você pode usar essas credenciais para clonar o site com o git ou configurar implantações automatizadas do GitHub, do TFS ou do Mercurial. Para obter mais informações, consulte a [Documentação de implantação do Serviço de Aplicativo do Azure].

### <a name="appsettings"></a>Configurações do aplicativo

A maioria das configurações para um serviço móvel migrado está disponível por meio de Configurações do Aplicativo. Você pode obter uma lista de configurações do aplicativo no [Portal do Azure]. Para exibir ou alterar as configurações do aplicativo:

  1. Faça logon no [Portal do Azure].
  2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
  3. A folha Configurações será aberta por padrão; se não abrir, clique em **Configurações**.
  4. Clique em **Configurações do aplicativo** no menu GERAL.
  5. Role até a seção Configurações do Aplicativo e localize sua configuração de aplicativo.
  6. Clique no valor da configuração do aplicativo para editar o valor. Clique em **Salvar** para salvar o valor.

Você pode atualizar várias configurações de aplicativo ao mesmo tempo.

> [AZURE.TIP]  Você observará que existem duas Configurações de Aplicativo com o mesmo valor. Por exemplo, você pode ver _ApplicationKey_ e _MS\_ApplicationKey_. Você só precisa alterar a configuração de aplicativo prefixada com **MS \_**. No entanto, é uma boa ideia atualizar ambas as configurações do aplicativo ao mesmo tempo.

### <a name="authentication"></a>Autenticação

Todas as configurações de autenticação estão disponíveis como configurações de aplicativo no site migrado. Para atualizar as configurações de autenticação, é preciso alterar as configurações do aplicativo apropriadas. A tabela a seguir mostra as configurações do aplicativo apropriadas para o seu provedor de autenticação:

| Provedor | ID do cliente | Segredo do cliente | Outras configurações |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Conta da Microsoft | **MS\_MicrosoftClientID** | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook | **MS\_FacebookAppID** | **MS\_FacebookAppSecret** | |
| Twitter | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** | |
| Google | **MS\_GoogleClientID** | **MS\_GoogleClientSecret** | |
| AD do Azure | **MS\_AadClientID** | | **MS\_AadTenants** |

Observação: **MS\_AadTenants** é armazenado como uma lista separada por vírgulas dos domínios de locatário (os campos "Locatários Permitidos" no portal de Serviços Móveis).

> [AZURE.WARNING] **Não use os mecanismos de autenticação no menu Configurações**
>
> O Serviço de aplicativo do Azure fornece um sistema de autenticação e autorização "sem código" separado no menu de Configurações _Autenticação/Autorização_ e a opção (preterida) _Autenticação Móvel_ no menu Configurações. Essas opções são incompatíveis com um Serviço Móvel do Azure migrado. Você pode [atualizar seu site] para aproveitar a autenticação do Serviço de Aplicativo do Azure.

### <a name="easytables"></a>Dados

A guia _Dados_ nos Serviços Móveis foi substituída pelas _Tabelas Fáceis_ no Portal do Azure. Para acessar tabelas fáceis:

  1. Faça logon no [Portal do Azure].
  2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
  3. A folha Configurações será aberta por padrão; se não abrir, clique em **Configurações**.
  4. Clique em **Tabelas fáceis** no menu MÓVEL.

Você pode adicionar uma nova tabela clicando no botão **Adicionar** ou acessar suas tabelas existentes clicando em um nome de tabela. Há uma variedade de operações que você realizar a partir dessa folha, incluindo:

  * Alterando as permissões de tabela
  * Editando os scripts operacionais
  * Gerenciando o esquema da tabela
  * Excluindo a tabela
  * Limpando o conteúdo da tabela
  * Excluindo linhas específicas da tabela

### <a name="easyapis"></a>API

A guia _API_ nos Serviços Móveis foi substituída pelas _APIs Fáceis_ no Portal do Azure. Para acessar APIs fáceis:

  1. Faça logon no [Portal do Azure].
  2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
  3. A folha Configurações será aberta por padrão; se não abrir, clique em **Configurações**.
  4. Clique em **APIs fáceis** no menu MÓVEL.

Suas APIs migradas já estarão listadas na folha. Você também pode adicionar uma nova API a partir dessa folha. Para gerenciar uma API específica, clique na API. Na nova folha, você pode ajustar as permissões e editar os scripts para a API.

### <a name="on-demand-jobs"></a>Trabalhos do Agendador

Todos os trabalhos do Agendador estão disponíveis por meio da seção de Coleções de trabalhos do Agendador. Para acessar seus trabalhos do Agendador:

  1. Faça logon no [Portal do Azure].
  2. Selecione **Procurar>**, digite **Agenda** na caixa _Filtro_ e, em seguida, selecione **Coleções do Agendador**.
  3. Selecione a Coleção de trabalhos para seu site. Ela será nomeada _sitename_-Jobs.
  4. Clique em **Configurações**.
  5. Clique em **Trabalhos do Agendador** em GERENCIAR.

Trabalhos agendados serão listados com a frequência especificada antes da migração. Trabalhos sob demanda serão desabilitados. Para executar um trabalho sob demanda:

  1. Selecione o trabalho que você deseja executar.
  2. Se necessário, clique em **Habilitar** para habilitar o trabalho.
  3. Clique em **Configurações** e, depois, **Agenda**.
  4. Selecione uma Recorrência de **Uma vez** e clique em **Salvar**

Os trabalhos sob demanda estão localizados em `App_Data/config/scripts/scheduler post-migration`. É recomendável converter todos os trabalhos sob demanda para [WebJobs]. Você deve escrever novos trabalhos do Agendador como [WebJobs].

### <a name="notification-hubs"></a>Hubs de Notificação

Os Serviços Móveis usam os Hubs de Notificação para notificações por push. As seguintes configurações de aplicativo são usadas para vincular o Hub de Notificação ao Serviço Móvel após a migração:

| Configuração de aplicativo | Descrição |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace** | O namespace do Hub de notificação |
| **MS\_NotificationHubName** | O nome do hub de notificação |
| **MS\_NotificationHubConnectionString** | A cadeia de conexão do hub de notificação |
| **MS\_NamespaceName** | Um alias para MS\_PushEntityNamespace |

O Hub de notificação será gerenciado por meio de [Portal do Azure]. Observe o nome do hub de notificação (você pode descobri-lo usando as Configurações do Aplicativo):

  1. Faça logon no [Portal do Azure].
  2. Selecione **Procurar**> e depois selecione **Hubs de Notificação**
  3. Clique no nome do hub de notificação associado ao serviço móvel.

> [AZURE.NOTE] O hub de notificação não estará visível se ele tiver um tipo “Misto”. Hubs de notificação do tipo "misto” utilizam Hubs de notificação e recursos do barramento de serviço herdados. Você precisará [Converter seus namespaces mistos]. Quando a conversão for concluída, o hub de notificação será exibido no [Portal do Azure].

Para obter mais informações, consulte a documentação [Hubs de Notificação].

> [AZURE.TIP] Recursos de gerenciamento de Hubs de notificação no [Portal do Azure] ainda estão em visualização. O [Portal Clássico do Azure] permanece disponível para gerenciar os hubs de notificação.

### <a name="app-settings"></a>Outras configurações de aplicativo

As seguintes configurações de aplicativo adicionais são migradas do seu serviço móvel e estão disponíveis em *Configurações* > *Configurações do Aplicativo*:

| Configuração de aplicativo | Descrição |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName** | O nome do seu aplicativo |
| **MS\_MobileServiceDomainSuffix** | O prefixo do domínio, ou seja, azure-mobile.net |
| **MS\_ApplicationKey** | A chave do seu aplicativo |
| **MS\_MasterKey** | A chave mestre do aplicativo |

A chave do aplicativo e a chave mestra devem ser idênticas às chaves de aplicativo do serviço móvel original. Em particular, a chave de aplicativo é enviada por clientes móveis para validar o uso da API móvel.

### <a name="cliequivalents"></a>Equivalentes de linha de comando

Você não poderá utilizar o comando _azure mobile_ para gerenciar o site de Serviços Móveis do Azure. Em vez disso, muitas funções foram substituídas pelo comando _azure site_. Use a tabela a seguir para localizar equivalentes para comandos comuns:

| Comando _Azure Mobile_ | Comando _Site do Azure_ equivalente |
| :----------------------------------------- | :----------------------------------------- |
| locais móveis | lista do local do site |
| lista móvel | lista de sites |
| _nome_ de exibição móvel | _nome_ de exibição do site |
| _nome_ de reinicialização móvel | _nome_ de reinicialização do site |
| _nome_ de reimplantação móvel | site deployment redeploy _commitId_ _name_ |
| mobile key set _name_ _type_ _value_ | site appsetting delete _key_ _name_ <br/> site appsetting add _key_=\_value\_ _name_ |
| mobile config list _name_ | site appsetting list _name_ |
| mobile config get _name_ _key_ | site appsetting show _key_ _name_ |
| mobile config set _name_ _key_ | site appsetting delete _key_ _name_ <br/> site appsetting add _key_=\_value\_ _name_ |
| mobile domain list _name_ | site domain list _name_ |
| mobile domain add _name_ _domain_ | site domain add _domain_ _name_ |
| mobile domain delete _name_ | site domain delete _domain_ _name_ |
| mobile scale show _name_ | _nome_ de exibição do site |
| mobile scale change _name_ | site scale mode _mode_ _name_ <br /> site scale instances _instances_ _name_ |
| mobile appsetting list _name_ | site appsetting list _name_ |
| mobile appsetting add _name_ _key_ _value_ | site appsetting add _key_=\_value\_ _name_ |
| mobile appsetting delete _name_ _key_ | site appsetting delete _key_ _name_ |
| mobile appsetting show _name_ _key_ | site appsetting delete _key_ _name_ |

Atualize configurações de notificação por push ou de autenticação atualizando a configuração apropriada do aplicativo. Edite arquivos e publique seu site via ftp ou git.

### <a name="diagnostics"></a>Diagnóstico e registro em log

Normalmente, o registro em log de diagnóstico está desabilitado em um Serviço de Aplicativo do Azure. Para habilitar registro em log de diagnóstico:

  1. Faça logon no [Portal do Azure].
  2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
  3. A folha Configurações será aberta por padrão; se não abrir, clique em **Configurações**.
  4. Selecione **Logs de diagnóstico** no menu RECURSOS.
  5. Clique em **LIGAR** para os seguintes logs: **Registro em log de aplicativo (sistema de arquivos)**, **Mensagens de erro detalhadas** e **Rastreamento de solicitação com falha**
  6. Clique em **sistema de arquivos** para registro em log do servidor Web
  7. Clique em **Salvar**.

Para exibir os logs:

  1. Faça logon no [Portal do Azure].
  2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do Serviço Móvel migrado.
  3. Clique no botão **Ferramentas**
  4. Selecione **Transmissão de Log** no menu OBSERVE.

Os logs serão transmitidos para a janela fornecida como eles são gerados. Você também pode baixar os logs para análise posterior usando suas credenciais de implantação. Consulte a documentação de [Registro em log] para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

Note que seu aplicativo é migrado para o serviço de aplicativo, há ainda mais recursos você pode aproveitar:

  * [Slots de preparo] de implantação permitem testar alterações ao seu site e executar um teste A/B.
  * [WebJobs] fornecem uma substituição para trabalhos agendados sob demanda.
  * Você pode [implantar continuamente] seu site vinculando seu site a GitHub, TFS ou Mercurial.
  * Você pode usar o [Application Insights] para monitorar seu site.
  * Atenda a um site e a uma API móvel com o mesmo código.

### <a name="upgrading-your-site"></a>Atualizar o site de serviços móveis para o SDK de aplicativos móveis do Azure

  * Para projetos de servidor baseados em Node.js, o novo [SDK do Node.js de Aplicativos Móveis] oferece vários recursos novos. Por exemplo, você pode agora fazer desenvolvimento local e a depuração, usar qualquer versão do Node. js acima de 0,10 e personalizar com qualquer middleware Express. js.

  * Para projetos de servidor baseados em .NET, os novos [pacotes NuGet de SDK de aplicativos móveis](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) têm mais flexibilidade em dependências do NuGet, dão suporte a novos recursos de autenticação do serviço de aplicativo e compõem com qualquer projeto ASP.NET, inclusive o MVC. Para saber mais sobre a atualização, consulte [Atualizar o serviço de celular do .NET existente para o Serviço de Aplicativo](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Preços do Serviço de Aplicativo]: https://azure.microsoft.com/pt-BR/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[Dimensionamento automático]: ../app-service-web/web-sites-scale.md
[Serviço de Aplicativo do Azure]: ../app-service/app-service-value-prop-what-is.md
[Documentação de implantação do Serviço de Aplicativo do Azure]: ../app-service-web/web-sites-deploy.md
[Portal clássico do Azure]: https://manage.windowsazure.com
[Portal do Azure]: https://portal.azure.com
[Região do Azure]: https://azure.microsoft.com/pt-BR/regions/
[Planos do Agendador do Azure]: ../scheduler/scheduler-plans-billing.md
[implantar continuamente]: ../app-service-web/web-sites-publish-source-control.md
[Converter seus namespaces mistos]: https://azure.microsoft.com/pt-BR/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[nomes de domínio personalizados]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilidade geral do Serviço de Aplicativo do Azure]: /blog/announcing-general-availability-of-app-service-mobile-apps/
[Conexões híbridas]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[Registro em log]: ../app-service-web/web-sites-enable-diagnostic-log.md
[SDK do Node.js de Aplicativos Móveis]: https://github.com/azure/azure-mobile-apps-node
[Serviços Móveis vs. Serviço de Aplicativo]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Hubs de Notificação]: ../notification-hubs/notification-hubs-overview.md
[monitoramento de desempenho]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[Fazer backup dos scripts do serviço móvel]: ../mobile-services/mobile-services-disaster-recovery.md
[slots de preparo]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md

<!---HONumber=AcomDC_0211_2016-->