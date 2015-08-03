<properties pageTitle="Criando uma Máquina Virtual Oracle WebLogic Server 12c e Oracle Database 12c no Azure" description="Veja um exemplo de como criar uma imagem do Oracle WebLogic Server 12c e do Oracle Database 12c em execução no Windows Server 2012 no Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Criando uma Máquina Virtual do Oracle WebLogic Server 11g no Azure
O exemplo a seguir mostra como você pode criar uma Máquina Virtual baseada em uma imagem Oracle WebLogic Server 11g fornecida pela Microsoft em execução no Windows Server 2008 R2 no Azure.

##Para criar uma Máquina Virtual do Oracle WebLogic Server 11g no Azure

1. Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

2. Clique em **Marketplace**, em Computação e digite **Oracle** na caixa de pesquisa.

3. Selecione ou examine as informações sobre essa imagem (como o tamanho mínimo recomendado) e depois clique.

4. Especifique um **Nome de Host** para a máquina virtual.

5. Especifique um **Nome de Usuário** para a máquina virtual. Observe que esse usuário é para fazer logon remotamente na VM; não é o nome de usuário do banco de dados Oracle.

6. Especifique e confirme uma senha para a máquina virtual ou forneça uma chave pública SSH.

7. Observe que as camadas de preço recomendadas são exibidas por padrão. Para ver todas as opções de configuração, clique em **Exibir tudo** no canto superior direito.

8. Defina [Configuração Opcional](https://msdn.microsoft.com/library/azure/dn763935.aspx) conforme necessário com estas considerações:

	1. Deixe **Conta de Armazenamento** como está para criar uma nova conta de armazenamento com o nome da VM.

	2. Deixe **Conjunto de disponibilidade** como "Não configurado".

	3. Não adicione nenhum **ponto de extremidade** neste momento.

9. Escolher ou criar um [Grupo de recursos](resource-group-portal.md)

10. Escolha uma **Assinatura**

11. Escolha um **Local**

##Para configurar a sua Máquina Virtual do Oracle WebLogic Server 11g no Azure

1. Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

2. Clique em **Máquinas Virtuais**.

3. Clique no nome da Máquina Virtual na qual você deseja fazer logon.

4. Clique em **Conectar**.

5. Responda às solicitações conforme necessário para se conectar à Máquina Virtual. Quando for solicitado o nome do administrador e a senha, use os valores que você forneceu quando criou a Máquina Virtual.

6. Clique em **Windows Iniciar**, **Todos os Programas**, **Oracle WebLogic**, depois em **WebLogic Server 11g R1**, **Ferramentas** e, em seguida, clique em **Assistente de Configuração**.

7. Na caixa de diálogo **Boas-vindas**, selecione **Criar um novo domínio WebLogic** e, em seguida, clique em **Avançar**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image30.png)

8. Na caixa de diálogo **Selecionar origem do domínio**, aceite os valores padrão e, em seguida, clique em **Avançar**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image31.png)

9. Na caixa de diálogo **Especificar nome de domínio e local**, aceite os valores padrão e, em seguida, clique em **Avançar**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image32.png)

10. Na caixa de diálogo **Configurar nome de usuário administrador e senha**:

	1. [Opcional] Altere o nome de usuário de **weblogic** para um valor de sua escolha.

	2. Especifique e confirme uma senha para o administrador do servidor WebLogic.

	3. Clique em **Próximo**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image33.png)

11. Na caixa de diálogo **Configurar modo de início do servidor e JDK**, selecione **Modo de produção**, selecione o JDK disponível (ou o navegador para um JDK, se desejado) e clique em **Avançar**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image34.png)

12.	Na caixa de diálogo **Selecionar configuração opcional**, não selecione nenhuma opção e, em seguida, clique em **Avançar**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image35.png)

13.	Na caixa de diálogo **Resumo da configuração**, clique em **Criar**.
	
	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image35.png)

14.	Na caixa de diálogo **Criar domínio**, marque **Iniciar Servidor de Administração** e, em seguida, clique em **Concluído**.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image37.png)

15.	Um prompt de comando para **startWebLogic.cmd** é iniciado. Quando solicitado, forneça o seu nome de usuário do WebLogic e a senha.

## Para instalar um aplicativo em uma Máquina Virtual no Oracle WebLogic Server 11g no Azure

1. Ainda conectado à sua Máquina Virtual, copie localmente o exemplo de shoppingcart.war disponível em [http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war](http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war). Por exemplo, crie uma pasta chamada **C:\\mywar** e salve o WAR em [http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war](http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war) para **C:\\mywar**.

2. Abra o **Console de administração do WebLogic Server**, [http://localhost:7001/console](http://localhost:7001/console). Quando solicitado, forneça o seu nome de usuário do WebLogic e a senha.

3. Dentro do **Console de administração do WebLogic Server**, clique em **Bloquear e Editar**, **Implantações** e, em seguida, clique em **Instalar**.

4. Para **Caminho**, digite `c:\mywar\shoppingcart.war`.

	![](media/virtual-machines-creating-oracle-webLogic-server-11g-virtual-machine/image38.png)

	Clique em **Próximo**.

5. Selecione **Instalar essa implantação como um aplicativo** e, em seguida, clique em **Avançar**.

6. Clique em **Concluir**.

7. Dentro do **Console de Administração do Servidor WebLogic**, clique em **Ativar alterações**.

8. Clique em **Implantações**, selecione **carrinho de compras**, clique em **Iniciar** e, em seguida, clique em **Atendendo todas as solicitações**. Quando solicitado a confirmar, clique em **Sim**.

9. Para ver o aplicativo de carrinho de compras em execução localmente, abra um navegador para [http://localhost:7001/shoppingcart](http://localhost:7001/shoppingcart)

10.  Permitir uma conexão de entrada por meio do firewall na porta 7001.

	1. Enquanto você ainda estiver conectado à sua **Máquina Virtual**, clique em **Windows Iniciar** > **Painel de controle** > **Sistema e segurança**, depois em **Firewall do Windows** e, em seguida, clique em **Configurações avançadas**. Isso abre o console de gerenciamento do **Firewall do Windows com Segurança Avançada**.

	2. No console de gerenciamento do firewall, clique em **Regras de Entrada** no painel esquerdo (se você não vir **Regras de Entrada**, expanda o nó superior no painel à esquerda) e, em seguida, clique em **Nova Regra** no painel direito.

	3. Para **Tipo de Regra**, selecione **Porta** e clique em **Avançar**.

	4. Para **Protocolo e Porta**, selecione **TCP**, selecione **Portas locais específicas**, digite **7001** para a porta e, em seguida, clique em **Avançar**.

	5. Selecione **Permitir a conexão** e clique em **Avançar**.

	6. Aceite os padrões para os perfis aos quais a regra se aplica e clique em **Avançar**.

	7. Especifique um nome para a regra e, opcionalmente, uma descrição e depois clique em **Concluir**.

11. Crie um ponto de extremidade para a sua Máquina Virtual:

	1. Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

    2. Clique em **Procurar**

    3. Clique em **Máquinas Virtuais**

    4. Selecione a Máquina Virtual

	5. Clique em **Configurações**

    6. Clique em **Pontos de Extremidade**.

    7. Clique em **Adicionar**.

    8. Especifique um nome para o ponto de extremidade

		1. Use **TCP** para o protocolo

        2. Use **80** para a porta pública

        3. Use **7001** para a porta privada.

    9. Deixe o restante das opções como estão.

	10. Clique em **OK**

12. Para ver o aplicativo de carrinho de compras em execução na Internet, abra um navegador para a URL na forma de `http://<<unique_domain_name>>/shoppingcart`. (Você pode determinar o valor de `<<unique_domain_name>>` dentro do [Portal do Azure](https://ms.portal.azure.com/) clicando em M**áquinas Virtuais** e, em seguida, selecionando a Máquina Virtual que você está usando para executar o Oracle WebLogic Server).

## Recursos adicionais

Agora que você configurou a Máquina Virtual executando o Oracle WebLogic Server, consulte os seguintes tópicos para obter informações adicionais.

- [Imagens de Máquinas Virtuais Oracle – diversas considerações](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

- [Documentação do produto Oracle WebLogic Server](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

- [Imagens de Máquina Virtual Oracle para Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=July15_HO4-->