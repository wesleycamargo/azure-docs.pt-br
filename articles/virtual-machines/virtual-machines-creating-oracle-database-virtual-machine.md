<properties title="Creating an Oracle Database Virtual Machine in Azure" pageTitle="Criando uma máquina virtual do Oracle Database no Azure" description="Percorra um exemplo de criação de uma máquina virtual Oracle no Microsoft Azure e, em seguida, crie um banco de dados Oracle nela." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Criando uma máquina virtual do Oracle Database no Azure
O exemplo a seguir mostra como você pode criar uma máquina virtual baseada em uma imagem do Oracle Database fornecida pela Microsoft em execução no Windows Server 2012 no Azure. Há duas etapas: criar a máquina virtual e criar um Oracle Database dentro da máquina virtual. O exemplo mostrado é a versão do Oracle Database 12c, mas as etapas são praticamente idênticas para a versão 11g.

##Para criar uma máquina virtual do Oracle Database no Azure

1.	Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

2.	Clique em **Marketplace**, em **Computação** e digite **Oracle** na caixa de pesquisa.

3.	Selecione uma das imagens do Oracle Database disponíveis **– versão 11g, versão 12c, Standard Edition, Enterprise Edition ou uma das opções populares ou pacotes de opções avançadas.** Examine as informações sobre a imagem selecionada (como tamanho mínimo recomendado) e, em seguida, clique em **Avançar**.

4.	Especifique um **Nome de Host** para a máquina virtual.

5.	Especifique um **Nome de Usuário** para a máquina virtual. Observe que esse usuário é para fazer logon remotamente na VM; não é o nome de usuário do banco de dados Oracle.

6.	Especifique e confirme uma senha para a máquina virtual ou forneça uma chave pública SSH.

7.	Escolha uma **Camada de Preços**. Observe que as camadas de preço recomendadas são exibidas por padrão. Para ver todas as opções de configuração, clique em **Exibir tudo** no canto superior direito.

8.	Defina [Configuração Opcional](https://msdn.microsoft.com/library/azure/dn763935.aspx) conforme necessário com estas considerações:

	1. Deixe **Conta de Armazenamento** como está para criar uma nova conta de armazenamento com o nome da VM.

	2. Deixe **Conjunto de Disponibilidade** como "Não configurado".

	3. Não adicione nenhum **ponto de extremidade** neste momento.

9.	Escolher ou criar um Grupo de recursos

10. Escolha uma **Assinatura**

11. Escolha um **Local**

12. Clique em **Criar**, e o processo de criação de uma Máquina Virtual será iniciado. Depois que a máquina Virtual tiver o status de **Em execução**, vá para a próxima etapa.


##Para criar o banco de dados usando a máquina virtual do Oracle Database no Azure

1.	Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

2.	Clique em **Máquinas Virtuais**.

3.	Clique no nome da Máquina Virtual na qual você deseja fazer logon.

4.	Clique em **Conectar**.

5.	Responda às solicitações conforme necessário para se conectar à Máquina Virtual. Quando for solicitado o nome do administrador e a senha, use os valores que você forneceu quando criou a Máquina Virtual.

6.	Crie uma variável de ambiente denominada **ORACLE_HOSTNAME** com o valor definido para o nome do computador da máquina virtual. Você pode criar uma variável de ambiente usando as seguintes etapas:

	1.	Clique em **Windows Iniciar**, digite **Painel de Controle**, clique no ícone **Painel de Controle**, clique em **Sistema e Segurança**, clique em **Sistema** e, em seguida, clique em **Configurações avançadas do sistema**.

	2.	Clique na guia **Avançado** e, em seguida, clique em **Variáveis de Ambiente**.

	3.	Na seção **Variáveis do sistema**, clique em **Novo** para criar a variável.

	4.	No diálogo **Nova variável de sistema**, digite **ORACLE_HOSTNAME** para o nome da variável e digite o nome do computador da máquina virtual como o valor. Para determinar o nome do computador, abra um prompt de comando e execute **SET COMPUTERNAME** (a saída desse comando conterá o nome do computador).
	
	5.	Clique em **OK** para salvar a nova variável de ambiente e feche a caixa de diálogo **Nova variável do sistema**.

	6.	Feche as outras caixas de diálogo que foram abertas pelo painel de controle.

7.	Clique em **Windows Iniciar** e digite **Assistente de Configuração do Banco de Dados**. Clique no ícone **Assistente de Configuração do Banco de Dados**.

8.	No **Assistente de configuração do banco de dados**, forneça valores conforme necessário para cada etapa da caixa de diálogo:

	1.	**Etapa 1:** clique em **Criar Banco de Dados** e, em seguida, clique em **Avançar**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

	2. **Etapa 2:** insira um valor para **Nome do banco de dados global**. Insira e confirme um valor para **Senha Administrativa**; essa senha será para o usuário **SYSTEM** do banco de dados Oracle. Desmarque **Criar como Contêiner do Banco de Dados**. Clique em **Próximo**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

	3. **Etapa 3:** a verificação de pré-requisitos deve continuar automaticamente, avançando até a **E4**.
	
	4. **Etapa 4:** examine as opções **Criar banco de dados – resumo** e clique em **Concluir**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)
	5. **Etapa 5:** a **Página Progresso** relatará o status da criação do banco de dados.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)
	6. Depois que o banco de dados for criado, você terá a opção de usar a caixa de diálogo **Gerenciamento de Senha**. Modifique as configurações de senha, se necessário, para atender aos requisitos e, em seguida, feche as caixas de diálogo para sair do **Assistente de Configuração do Banco de Dados**.

##Para confirmar que o banco de dados está instalado

1.	Ainda conectado à sua máquina virtual, inicie um prompt de comando SQL Plus: clique em **Windows Iniciar**, digite **SQL Plus**. Clique no ícone **SQL Plus**.

2.	Quando solicitado, faça logon usando o nome de usuário **SYSTEM** e a senha especificada ao criar o banco de dados Oracle.

3.	Execute o seguinte comando no prompt de comando SQL Plus:

		select * from GLOBAL_NAME;

	O resultado deve ser o nome global do banco de dados que você criou.

	![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

##Permitir que o banco de dados seja acessado remotamente
Para permitir que o banco de dados a ser acessado remotamente (por exemplo, de um computador cliente executando o código Java), você precisa iniciar o ouvinte de banco de dados, abrir a porta 1521 no firewall da máquina virtual e criar um ponto de extremidade público para a porta 1521.

### Iniciar o ouvinte de banco de dados
1.	Faça logon na Máquina Virtual.

2.	Abra um prompt de comando.

3.	Ao prompt de comando, execute o seguinte:

		lsnrctl start

(Você pode executar `lsnrctl status` para verificar o status do ouvinte. Quando desejar interromper o ouvinte, você pode executar `lsnrctl stop`.)

### Abra a porta 1521 no firewall da Máquina Virtual

1.	Enquanto você ainda estiver conectado à sua máquina Virtual. Clique em **Windows Iniciar**, digite **Firewall do Windows com Segurança Avançada** e clique no ícone **Firewall do Windows com Segurança avançada**. Isso abre o console de gerenciamento do **Firewall do Windows com Segurança Avançada**.

2.	No console de gerenciamento do firewall, clique em **Regras de Entrada** no painel esquerdo (se você não vir **Regras de Entrada**, expanda o nó superior no painel à esquerda) e, em seguida, clique em **Nova Regra** no painel direito.

3.	Para **Tipo de Regra**, selecione **Porta** e clique em **Avançar**.

4.	Para **Protocolo e Porta**, selecione **TCP**, selecione **Portas locais específicas**, digite **1521** para a porta e, em seguida, clique em Avançar.

5.	Selecione **Permitir a conexão** e clique em **Avançar**.

6.	Aceite os padrões para os perfis aos quais a regra se aplica e clique em **Avançar**.

7.	Especifique um nome para a regra e, opcionalmente, uma descrição e depois clique em **Concluir**.

### Criar um ponto de extremidade público para a porta 1521

1.	Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

2.	Clique em **Procurar**

3.    Clique em **Máquinas Virtuais**

4.	Selecione a Máquina Virtual

5.	Clique em **Configurações**

6.	Clique em **Pontos de Extremidade**.

7.	Clique em **Adicionar**.

8.	Especifique um nome para o ponto de extremidade

	1. Use **TCP** para o protocolo
	
	2. Use **1521** para a porta pública
	
	3. Use **1521** para a porta privada.

9.	Deixe o restante das opções como estão.

10. Clique em **OK**

##Habilitando o acesso remoto do Oracle Database Enterprise Manager
Se você deseja habilitar o acesso remoto para o Oracle Database Enterprise Manager, abra a porta 5500 no firewall e crie um ponto de extremidade de máquina virtual para 5500 no Portal do Azure (usando as etapas acima para abrir a porta 1521 e criar um ponto de extremidade para 1521). Em seguida, para executar o Oracle Enterprise Manager do computador remoto, abra um navegador para a URL na forma de `http://<<unique_domain_name>>:5500/em`. (Você pode determinar o valor de *<<unique_domain_name >>* dentro do [Portal do Azure](https://ms.portal.azure.com/) clicando em **Máquinas Virtuais** e, em seguida, selecionando a máquina virtual que você está usando para executar o Oracle Database).

##Configurando opções populares e pacotes de opções avançadas
Se você escolheu o **Oracle Database com Opções Populares** ou **pacote Oracle Database com Opções Avançadas**, a próxima etapa será configurar os recursos de complemento na instalação do Oracle. Consulte a documentação do Oracle para obter instruções sobre como configurá-los no Windows, já que as configurações podem variar amplamente com base nas necessidades de cada componente individual.

O **pacote Oracle Database com Opções Populares** inclui o Oracle Database Enterprise Edition e instâncias incluídas na licença do [Partitioning](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), [Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), [Oracle Tuning Pack for Database](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), [Oracle Diagnostics Pack for Database](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ) e [Oracle Lifecycle Management Pack for Database](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html).

O **pacote Oracle Database Opções Avançadas** inclui instâncias incluídas na licença de todas as opções do conjunto de opções populares, mais [Advanced Compression](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), [Advanced Security](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), [Label Security](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), [Database Vault](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), [Advanced Analytics](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), [OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Spatial and Graph](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [In-Memory Database Cache](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), [Data Masking Pack](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB) e o Oracle Test Data Management Pack (como parte do Data Masking Pack).

##Recursos adicionais
Agora que você configurou sua máquina virtual e criou o banco de dados, consulte os seguintes tópicos para obter informações adicionais.

-	[Imagens de Máquinas Virtuais Oracle – diversas considerações](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Biblioteca de documentação do Oracle Database 12c](http://www.oracle.com/pls/db1211/homepage)

-	[Conectando-se ao Oracle Database de um aplicativo Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-	[Imagens de Máquina Virtual Oracle para Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

-	[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=July15_HO2-->