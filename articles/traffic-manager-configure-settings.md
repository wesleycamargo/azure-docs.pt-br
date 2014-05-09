<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Como definir as configurações do Traffic Manager" authors="" solutions="" manager="" editor="" />


#Como definir as configurações do Traffic Manager#
O Traffic Manager do Azure permite que você controle a distribuição de tráfego de usuário para os serviços hospedados do Azure. 

O Traffic Manager funciona aplicando um mecanismo de políticas inteligente às consultas DNS em seu principal nome de domínio da empresa. Atualize os registros de recursos DNS que sua empresa possui para apontar para os domínios do Traffic Manager. As políticas do Traffic Manager que estão anexadas a esses domínios resolvem as consultas DNS em seu nome de domínio de empresa principal para os endereços IP de determinados serviços hospedados do Azure contidos nas políticas do Traffic Manager. Para obter mais informações, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/hh744833.aspx).

##Sumário

* [Como apontar um domínio de Internet da empresa para um domínio do Traffic Manager](#howto_point_company) 
* [Como testar uma política](#howto_test)
* [Como desabilitar temporariamente políticas e serviços hospedados](#howto_temp_disable)
* [Como editar uma política](#howto_edit_policy)
* [Como balancear a carga de tráfego igualmente em um conjunto de serviços hospedados](#howto_load_balance)
* [Como criar uma política de failover](#howto_create_failover)
* [Como direcionar o tráfego de entrada para os serviços hospedados com base no desempenho da rede](#howto_direct)


<h2><a id="howto_point_company"></a>Como apontar um domínio de Internet da empresa para um domínio do Traffic Manager</h2>

Para apontar o nome de domínio de sua empresa para um domínio do Traffic Manager, edite o registro de recurso DNS no servidor DNS usando um CNAME. 

Por exemplo, para apontar o domínio da empresa principal **www.contoso.com** para um domínio do Traffic Manager chamado **contoso.trafficmanager.net**, atualize o registro de recurso DNS conforme mostrado a seguir:
``www.contoso.com IN CNAME contoso.trafficmanager.net``

Todo o tráfego direcionado para *www.contoso.com* agora será redirecionado para *contoso.trafficmanager.net*. Certifique-se de que esteja usando um domínio em que você deseja todo o tráfego redirecionado para o Traffic Manager. 

<h2><a id="howto_test"></a>Como testar uma política</h2>

A melhor maneira de testar sua política é configurar vários clientes e, em seguida, desativar um serviço de cada vez em sua política. As dicas a seguir ajudarão você a testar sua política do Traffic Manager:

- **Defina o TTL DNS como muito baixo**, de forma que as alterações sejam propagadas rapidamente (30 segundos, por exemplo).

- **Saiba os endereços IP dos serviços hospedados do Azure** que estão na política que você está testando. Você pode obter essas informações no Portal de Gerenciamento do Azure. Clique na implantação em produção de seu serviço. No painel de propriedades à direita, a última entrada será o VIP, que é o endereço IP virtual desse serviço hospedado.

	![Local do IP do serviço hospedado][0]

	**Figura 1** -  Local do IP do serviço hospedado

- **Use ferramentas que permitam resolver um nome DNS para um endereço IP** e exibir esse endereço. Você está verificando se o nome de domínio de sua empresa resolve os endereços IP dos serviços hospedados em sua política. Eles devem resolver de maneira consistente com o método de balanceamento de carga de sua política do Traffic Manager. Se estiver em um sistema Windows, você poderá usar nslookup em uma janela de CMD (conforme explicado a seguir). Outras ferramentas disponíveis publicamente que permitem que você "se aprofunde" em um endereço IP também estão prontamente disponíveis na Internet.

- **Verifique a política do Traffic Manager** usando *nslookup*. 

> **Para verificar uma política do Traffic Manager usando nslookup** 

>1. Inicie uma janela de comando clicando em **Iniciar-Executar** e digitando **CMD**.

>2. Para liberar o cache do resolvedor de DNS, digite ``ipconfig /flushdns``.

>3. Digite o comando ``nslookup <your traffic manager domain>``.
 Por exemplo, o comando a seguir verifica o domínio com o prefixo *myapp.contoso* ``nslookup myapp.contoso.trafficmanager.net``

>>Um resultado típico mostrará o seguinte:

>> - O nome DNS e o endereço IP do servidor DNS que está sendo acessado para resolver o domínio do Traffic Manager.

>> - O nome de domínio do Traffic Manager digitado na linha de comando após "nslookup" e o endereço IP que é resolvido pelo domínio do Traffic Manager. 
O segundo endereço IP é o importante para a verificação. Ele deve corresponder a um VIP de um dos serviços hospedados na política do Traffic Manager que está sendo testado.

>>![exemplo de comando nslookup][1]

>>**Figura 2** - Exemplo de comando nslookup

- **Use um dos métodos de testes adicionais listados a seguir.** Selecione o método apropriado para o tipo de balanceamento de carga que está sendo testado.

###Políticas de desempenho###

Você precisará de clientes em diferentes partes do mundo para testar o seu domínio com eficácia. Você pode criar clientes no Azure que tentarão chamar seus serviços por meio do nome de domínio de sua empresa. Como alternativa, se a sua empresa for global, você poderá fazer logon remotamente em clientes em outras partes do país e realizar testes a partir desses clientes.

Existem serviços disponíveis de investigação e nslookup com base na Web gratuitos. Alguns deles possibilitam que você verifique a resolução de nome DNS a partir de vários locais. Faça uma pesquisa por nslookup para obter exemplos. Outra opção é usar uma solução de terceiros, como Gomez ou Keynote, para confirmar que as políticas estão distribuindo o tráfego conforme o esperado.

###Políticas de failover###

1. Deixar todos os serviços ativados.

2. Usar um único cliente.

3. Solicitar a resolução de DNS para o domínio de sua empresa usando ping ou um utilitário semelhante.

4. Certificar-se de que o endereço IP obtido seja para o seu serviço hospedado primário. 

5. Desativar seu serviço primário ou remover o arquivo de monitoramento, de forma que o Traffic Manager acredite que ele está desativado.

6. Aguardar pelo menos dois minutos. 

7. Solicitar a resolução de DNS.

8. Verificar se o endereço IP obtido é para o seu serviço hospedado secundário, conforme mostrado pela ordem dos serviços na caixa de diálogo Editar política do Traffic Manager.

9. Repetir o processo desativando o serviço secundário e, em seguida, o terciário e assim por diante. Lembrar sempre que a resolução de DNS retorna o endereço IP do próximo serviço na lista. Quando todos os serviços estão desativados, você deve obter o endereço IP do serviço hospedado primário novamente.

###Políticas de Round Robin###

1. Deixar todos os serviços ativados.

2. Usar um único cliente.

3. Solicitar a resolução de DNS para o domínio de nível superior.

4. Certificar-se de que o endereço IP obtido seja um dos incluídos em sua lista.

5. Repetir o processo permitindo que o TTL DNS expire para que você receba um novo endereço IP. Você deve ver os endereços IP retornados para cada um dos serviços hospedados. Em seguida, o processo será repetido.


<h2><a id="howto_temp_disable"></a>Como desabilitar temporariamente políticas e serviços hospedados</h2>

Você pode desabilitar as políticas do Traffic Manager criadas anteriormente para não rotearem o tráfego. Quando você desabilita uma política do Traffic Manager, as informações da política permanecem intactas e editáveis na interface do Traffic Manager. Você pode habilitar a política novamente com facilidade, e o roteamento será retomado. 

Você também pode desabilitar serviços hospedados individuais que fazem parte de uma política do Traffic Manager. Essa ação mantém efetivamente o serviço hospedado como parte da política, mas a política age como se o serviço hospedado não estivesse incluído a ela. Essa ação é muito útil para remover temporariamente um serviço hospedado que esteja no modo de manutenção ou sendo reimplantado e, por isso, instável. Após o serviço hospedado voltar ao funcionamento, ele poderá ser habilitado novamente. 

**Observação**  
A desativação de um serviço hospedado não tem nada a ver com o estado de implantação no Azure. Um serviço íntegro permanecerá ativo e capaz de receber tráfego mesmo quando desabilitado no Traffic Manager. Além disso, a desativação de um serviço hospedado em uma política não afeta seu status em outra política. 

###Para desabilitar uma política###

1. Selecione uma política habilitada na árvore de interface do Traffic Manager. 

2. Clique em **Desabilitar Política** na barra de ferramentas superior. Observe que o botão ficará esmaecido se você realçar uma política que já esteja desabilitada.

###Para habilitar uma política###

1. Selecione uma política desabilitada na árvore de interface do Traffic Manager. 

2. Clique em **Habilitar Política** na barra de ferramentas superior. Observe que o botão ficará esmaecido se você realçar uma política que já esteja desabilitada.

###Para desativar um serviço hospedado###

1. Selecione um serviço hospedado ativado em uma política na interface do Traffic Manager. Você deverá expandir uma política para ver os serviços hospedados que ela contém. 

2. Clique em **Desabilitar Política de Serviço** na barra de ferramentas superior. Observe que o botão ficará esmaecido se você realçar um serviço hospedado que já esteja desativado.

3. O tráfego parará de fluir para o serviço hospedado com base no tempo de TTL DNS definido para o domínio do Traffic Manager que faz parte da política. Para obter mais informações, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) e role para seção sobre monitoramento de serviços hospedados no Traffic Manager do Azure. 

###Para ativar um serviço hospedado###

1. Selecione um serviço hospedado desativado em uma política na interface do Traffic Manager. Você deverá expandir uma política para ver os serviços hospedados que ela contém. 

2. Clique em **Habilitar Política de Serviço** na barra de ferramentas superior. Observe que o botão ficará esmaecido se você realçar um serviço hospedado que já esteja ativado.

3. O tráfego começará a fluir para o serviço hospedado novamente, conforme orientado pela política.  

<h2><a id="howto_edit_policy"></a>Como editar uma política</h2>

Se precisar desabilitar temporariamente uma política ou determinados serviços hospedados na política, você poderá desabilitá-los temporariamente sem alterar a política.  

Para alterar uma política para um tipo diferente, use as seguintes etapas:

1. **Faça logon na área do Traffic Manager no Portal de Gerenciamento** em [http://manage.windowsazure.com](http://manage.windowsazure.com). Clique em **Rede Virtual** no canto inferior esquerdo das páginas do portal e, em seguida, escolha **Traffic Manager** nas opções do painel esquerdo.

2. **Selecione a política** que deseja alterar na tela do Traffic Manager no Portal de Gerenciamento.

3. **Clique em "Configurar"** na barra de menus superior.

4. **Faça as alterações desejadas na política.** Observe que, se você alterar o método de balanceamento de carga, dependendo da opção selecionada, a ordem dos serviços hospedados na lista de **serviços hospedados selecionados** poderá não ser importante.  

5. Clique em **Salvar**.

<h2><a id="howto_load_balance"></a>Como balancear a carga de tráfego igualmente em um conjunto de serviços hospedados</h2>

Um padrão comum de balanceamento de carga é fornecer um conjunto de serviços hospedados idênticos e enviar tráfego para cada um deles em um estilo round-robin. Este artigo descreve as etapas para configurar um domínio e uma política do Traffic Manager para executar esse tipo de balanceamento de carga.

Para obter mais informações sobre os diferentes métodos de balanceamento de carga fornecidos pelo Traffic Manager, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/hh744833.aspx) e role até a seção sobre métodos de balanceamento de carga no Traffic Manager do Azure.

1. **Implante seus serviços hospedados** no ambiente de produção. Para obter informações sobre desenvolvimento e implantação de serviços hospedados, consulte [serviços hospedados do Azure](http://msdn.microsoft.com/library/gg432967.aspx)

2. **Faça logon na área do Traffic Manager no Portal de Gerenciamento** em [http://manage.windowsazure.com](http://manage.windowsazure.com). Clique em **Rede Virtual** no canto inferior esquerdo das páginas do portal e, em seguida, escolha **Traffic Manager** nas opções do painel esquerdo.

3. **Escolha Políticas e clique em Criar.** Escolha a pasta **Políticas** na árvore de navegação à esquerda para ativar a opção **Criar** na barra de ferramentas superior. Escolha **Criar**. A caixa de diálogo **Criar política do Traffic Manager** será exibida.

	![Botão Criar para políticas][2]

	**Figura 1** - Botão Criar para políticas

4. **Escolha uma assinatura.** As políticas e os domínios são associados a uma única assinatura.

5. **Selecione o método de balanceamento de carga Round Robin.** Para obter mais informações sobre os diferentes métodos de balanceamento de carga fornecidos pelo Traffic Manager, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/hh744833.aspx) e role até a seção sobre métodos de balanceamento de carga no Traffic Manager do Azure.

6. **Localize os serviços hospedados e os adicione à política.** Use a caixa de filtragem para localizar serviços hospedados que contêm a cadeia de caracteres digitada na caixa. Desmarque a caixa para exibir todos os serviços hospedados em produção para a assinatura que você selecionou na etapa 4. Use os botões de seta para adicioná-los à política. A ordem na caixa **Nomes DNS selecionados** não é importante para esse método de balanceamento de carga.

7. **Configure o monitoramento.** O monitoramento garante que não seja enviado tráfego para os serviços hospedados que estão offline. Você deve ter um nome de arquivo e um caminho específico configurado.
Para obter mais informações, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) e role para seção sobre monitoramento de serviços hospedados no Traffic Manager do Azure.

8. **Atribua um nome ao domínio do Traffic Manager.** Defina um nome exclusivo para o seu domínio. Você pode especificar apenas o prefixo de seu domínio. Deixe a vida útil do DNS com o tempo padrão. 
Para obter mais informações sobre o efeito dessa configuração, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) e role até a seção sobre práticas recomendadas para serviços hospedados e políticas ao usar o Traffic Manager do Azure.


	A caixa de diálogo **Criar política do Traffic Manager** deve ser semelhante ao exemplo a seguir.

	![Caixa de diálogo do método round robin de balanceamento de carga][3] 

	**Figura 2** - Caixa de diálogo do método Round Robin de balanceamento de carga

9. **Teste o domínio e a política do Traffic Manager.** Para obter instruções, consulte [Como testar uma política do Traffic Manager do Azure](#howto_test). 

10. **Aponte seu servidor DNS para o domínio do Traffic Manager.** Depois que seu domínio do Traffic Manager estiver configurado e funcionando, edite o registro de DNS em seu servidor DNS autoritativo para apontar o domínio de sua empresa para o domínio do Traffic Manager. 
Por exemplo, o comando a seguir encaminha todo o tráfego direcionado para **www.contoso.com** para o domínio do Traffic Manager **contoso.trafficmanager.net**: 
`` www.contoso.com IN CNAME contoso.trafficmanager.net``
Para obter mais informações, consulte [Como apontar um domínio de Internet da empresa para um domínio do Traffic Manager do Azure](#howto_point_company).

<h2><a id="howto_create_failover"></a>Como criar uma política de failover</h2>

Geralmente, uma organização deseja fornecer confiabilidade para seus serviços. Ela faz isso fornecendo serviços de backup no caso de seu serviço principal falhar. Um padrão comum de failover de serviço é fornecer um conjunto de serviços hospedados idênticos e enviar tráfego para um serviço primário, com uma lista de um ou mais backups. Este artigo descreve as etapas para configurar uma política do Traffic Manager para executar esse tipo de backup de failover.

Para obter mais informações sobre os diferentes métodos de balanceamento de carga fornecidos pelo Traffic Manager, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/hh744833.aspx) e role até a seção sobre métodos de balanceamento de carga no Traffic Manager do Azure.

1. **Implante seus serviços hospedados** no ambiente de produção. Para obter informações sobre desenvolvimento e implantação de serviços hospedados, consulte [serviços hospedados do Azure](http://msdn.microsoft.com/library/gg432967.aspx).  

2. **Faça logon na área do Traffic Manager no Portal de Gerenciamento** em [http://manage.windowsazure.com](http://manage.windowsazure.com). Clique em **Rede Virtual** no canto inferior esquerdo das páginas do portal e, em seguida, escolha **Traffic Manager** nas opções do painel esquerdo.

3. **Escolha Políticas e clique em Criar.** Escolha a pasta **Políticas** na árvore de navegação à esquerda para ativar a opção **Criar** na barra de ferramentas superior. Escolha **Criar**. A caixa de diálogo **Criar política do Traffic Manager** será exibida.

	![Botão Criar para políticas][4]

	**Figura 1** - Botão Criar para políticas

4. **Escolha uma assinatura.** As políticas e os domínios são associados a uma única assinatura.

5. **Selecione o método de balanceamento de carga Política de Failover.** Para obter mais informações sobre os diferentes métodos de balanceamento de carga fornecidos pelo Traffic Manager, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/hh744833.aspx) e role até a seção sobre métodos de balanceamento de carga no Traffic Manager do Azure.

6. **Localize os serviços hospedados e os adicione à política.** Use a caixa de filtragem para localizar serviços hospedados que contêm a cadeia de caracteres digitada na caixa. Desmarque a caixa para exibir todos os serviços hospedados em produção para a assinatura que você selecionou na etapa 4. Use os botões de seta para adicioná-los à política. Quando você seleciona o método **Failover** de balanceamento de carga, a ordem dos serviços selecionados é importante. O serviço hospedado primário está na parte superior. Use as setas para cima e para baixo para alterar a ordem conforme o necessário.

7. O monitoramento garante que não seja enviado tráfego para os serviços hospedados que estão offline. O uso de uma política de failover sem configurar o monitoramento é inútil porque o tráfego será enviado para o serviço hospedado primário mesmo que ele seja exibido como offline. Para monitorar os serviços hospedados, você deve definir um nome de arquivo e um caminho específico.
Para obter mais informações, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) e role para seção sobre monitoramento de serviços hospedados no Traffic Manager do Azure.

8. **Atribua um nome ao domínio do Traffic Manager.** Defina um nome exclusivo para o seu domínio. Você pode especificar apenas o prefixo de seu domínio. Deixe a **TTL (vida útil) do DNS** com o tempo padrão.
Para obter mais informações sobre o efeito dessa configuração, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) e role até a seção sobre práticas recomendadas para serviços hospedados e políticas ao usar o Traffic Manager do Azure.
 
	A caixa de diálogo **Criar política do Traffic Manager** deve ser semelhante ao exemplo a seguir. 

	![Caixa de diálogo do método Failover de balanceamento de carga][5]

	**Figura 2** - Caixa de diálogo do método Failover de balanceamento de carga

9. **Teste o domínio e a política do Traffic Manager.** Para obter mais informações, consulte [Como testar uma política do Traffic Manager do Azure](#howto_test). 

10. **Aponte seu servidor DNS para o domínio do Traffic Manager.** Depois que seu domínio do Traffic Manager estiver configurado e funcionando, edite o registro de DNS em seu servidor DNS autoritativo para apontar o domínio de sua empresa para o domínio do Traffic Manager. 
Para obter mais informações, consulte [Como apontar um domínio de Internet da empresa para um domínio do Traffic Manager](#howto_point_company). 
Por exemplo, o comando a seguir encaminha todo o tráfego direcionado para **www.contoso.com** para o domínio do Traffic Manager **contoso.trafficmanager.net**
``www.contoso.com IN CNAME contoso.trafficmanager.net``

<h2><a id="howto_direct"></a>Como direcionar o tráfego de entrada para os serviços hospedados com base no desempenho da rede</h2>

Para balancear a carga do serviço hospedado localizado em diferentes datacenters em todo o mundo, você pode direcionar o tráfego de entrada para o serviço hospedado mais próximo. Embora "mais próximo" possa corresponder diretamente a uma grande distância geográfica, ele também pode corresponder ao local com a menor latência para atender à solicitação. O método de balanceamento de carga Desempenho permite que você faça a distribuição com base no local e na latência, mas não consegue levar em consideração as alterações em tempo real feitas na carga ou na configuração da rede. Para obter mais informações sobre os diferentes métodos de balanceamento de carga fornecidos pelo Traffic Manager, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/hh744833.aspx) e role até a seção sobre métodos de balanceamento de carga no Traffic Manager do Azure.

As etapas a seguir irão orientá-lo pelo processo:

1. **Implante seus serviços hospedados** no ambiente de produção. Para obter mais informações, consulte [Criando um serviço hospedado para o Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/gg432967.aspx). 
Consulte também as práticas recomendadas para serviços hospedados e políticas em [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring).

2. **Faça logon na área do Traffic Manager no Portal de Gerenciamento** em [http://manage.windowsazure.com](http://manage.windowsazure.com). Clique em **Rede Virtual** no canto inferior esquerdo das páginas do portal e, em seguida, escolha **Traffic Manager** nas opções do painel esquerdo.

3. **Escolha Políticas e clique em Criar.** Escolha a pasta **Políticas** na árvore de navegação à esquerda para ativar a opção **Criar** na barra de ferramentas superior. Escolha **Criar**. A caixa de diálogo **Criar política do Traffic Manager** será exibida. 

	![Botão Criar para políticas][6]
	
	**Figura 1** - Botão Criar para políticas

4. **Escolha uma assinatura.** As políticas e os domínios são associados a uma única assinatura. 

5. **Selecione o método de balanceamento de carga Desempenho.** Para obter mais informações sobre os diferentes métodos de balanceamento de carga fornecidos pelo Traffic Manager, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/hh744833.aspx) e role até a seção sobre métodos de balanceamento de carga no Traffic Manager do Azure.

6. **Localize os serviços hospedados e os adicione à política.** Use a caixa de filtragem para localizar serviços hospedados que contêm a cadeia de caracteres digitada na caixa. Desmarque a caixa para exibir todos os serviços hospedados em produção para a assinatura que você selecionou na etapa 4. Use os botões de seta para adicioná-los à política. A ordem de **Nomes DNS selecionados** não é importante para esse método de balanceamento de carga.

7. **Configure o monitoramento.** O monitoramento garante que não seja enviado tráfego para os serviços hospedados que estão offline. Você deve ter um nome de arquivo e um caminho específico configurado. 
Para obter mais informações, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) e role para seção sobre monitoramento de serviços hospedados no Traffic Manager do Azure.

8. **Atribua um nome ao domínio do Traffic Manager.** Defina um nome exclusivo para o seu domínio. Você pode especificar apenas o prefixo de seu domínio. Deixe a **TTL (vida útil) do DNS** com o tempo padrão. 
Para obter mais informações sobre o efeito dessa configuração, consulte [Visão geral do Traffic Manager do Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) e role até a seção sobre práticas recomendadas para serviços hospedados e políticas ao usar o Traffic Manager do Azure.
 
	A caixa de diálogo **Criar política do Traffic Manager** deve ser semelhante ao exemplo a seguir. 

	![Caixa de diálogo do método de balanceamento de carga Desempenho][7]

	**Figura 2** - Caixa de diálogo do método de balanceamento de carga Desempenho

9. **Teste o domínio e a política do Traffic Manager.** Para obter mais informações sobre testes, consulte [Como testar uma política do Traffic Manager do Azure](#howto_test).

10. **Aponte seu servidor DNS para o domínio do Traffic Manager.** Depois que seu domínio do Traffic Manager estiver configurado e funcionando, edite o registro de DNS em seu servidor DNS autoritativo para apontar o domínio de sua empresa para o domínio do Traffic Manager. 
Por exemplo, o comando a seguir encaminha todo o tráfego direcionado para **www.contoso.com** para o domínio do Traffic Manager **contoso.trafficmanager.net**. 
``www.contoso.com IN CNAME contoso.trafficmanager.net``
Para obter mais informações, consulte [Como apontar um domínio de Internet da empresa para um domínio do Traffic Manager do Azure](#howto_point_company).

[0]: ./media/traffic-manager-configure-settings/hosted_service_IP_location.png
[1]: ./media/traffic-manager-configure-settings/nslookup_command_example.png
[2]: ./media/traffic-manager-configure-settings/Create_button_for_policies.png
[3]: ./media/traffic-manager-configure-settings/Dialog_box_for_Round_Robin_load_balancing_method.png
[4]: ./media/traffic-manager-configure-settings/Create_button_for_policies.png
[5]: ./media/traffic-manager-configure-settings/Dialog_box_for_Failover_load_balancing_method.png
[6]: ./media/traffic-manager-configure-settings/Create_button_for_policies.png
[7]: ./media/traffic-manager-configure-settings/Dialog_box_for_Performance_load_balancing_method.png

