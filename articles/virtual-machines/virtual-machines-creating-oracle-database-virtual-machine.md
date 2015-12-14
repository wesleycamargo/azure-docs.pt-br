<properties
	pageTitle="Criar uma VM do Oracle Database com o portal clássico do Azure | Microsoft Azure"
	description="Saiba como criar uma máquina virtual com um Oracle Database usando o modelo de implantação clássico e o portal do Azure."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="Windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Criar uma máquina virtual do Oracle Database no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


O exemplo a seguir mostra como você pode criar uma máquina virtual (VM) com base em uma imagem do Banco de Dados Oracle fornecida pela Microsoft em execução no Windows Server 2012 no Azure. Há duas etapas. Primeiro, crie a VM e depois crie o Banco de Dados Oracle dentro da VM. O exemplo mostrado é a versão do Oracle Database 12c, mas as etapas são praticamente idênticas para a versão 11g.

##Para criar uma VM do Banco de Dados Oracle no Azure

1.	Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

2.	Clique em **Marketplace**, em **Computação** e digite **Oracle** na caixa de pesquisa.

3.	Selecione uma das imagens do Oracle Database disponíveis **versão 11g, versão 12c, Standard Edition, Enterprise Edition ou um dos pacotes de opções populares ou de opções avançadas.** Examine as informações sobre a imagem selecionada (como o tamanho mínimo recomendado) e clique em **Próximo**.

4.	Especifique um **Nome de Host** para a VM.

5.	Especifique um **Nome de Usuário** para a VM. Observe que esse usuário é para fazer logon remotamente na VM; não é o nome de usuário do banco de dados Oracle.

6.	Especifique e confirme uma senha para a máquina virtual ou forneça uma chave pública SSH.

7.	Escolha um **Tipo de Preço**. É importante lembrar que as Camadas de Preços Recomendadas são exibidas por padrão. Para ver todas as opções de configuração, clique em **Exibir tudo** no canto superior direito.

8.	Defina a configuração opcional conforme o necessário, levando em conta estas considerações:

	a. Deixe **Conta de Armazenamento** como está para criar uma nova conta de armazenamento com o nome da VM.

	b. Deixe **Conjunto de Disponibilidade** como "Não Configurado".

	c. Não adicione nenhum **ponto de extremidade** neste momento.

9.	Escolha ou crie um grupo de recursos.

10. Escolha uma **Assinatura**.

11. Escolha um **Local**.

12. Clique em **Criar** para iniciar o processo de criação de uma VM. Depois que a VM tiver o status de **Em execução**, continue para o próximo procedimento.


##Para criar o banco de dados usando a VM do Banco de Dados Oracle no Azure

1.	Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

2.	Clique em **Máquinas Virtuais**.

3.	Clique no nome da VM na qual você deseja fazer logon.

4.	Clique em **Conectar**.

5.	Responda às solicitações conforme necessário para se conectar à VM. Quando for solicitado o nome e a senha do administrador, use os valores que você forneceu quando criou a VM.

6.	Crie uma variável de ambiente chamada **ORACLE\_HOSTNAME** com seu valor definido para o nome do computador da VM. Você pode criar uma variável de ambiente usando as seguintes etapas:

	a. No Windows, clique em **Iniciar**, digite **Painel de Controle**, clique no ícone **Painel de Controle**, clique em **Sistema e Segurança**, clique em **Sistema** e, por fim, em **Configurações avançadas do sistema**.

	b. Clique na guia **Avançado** e em **Variáveis de Ambiente**.

	c. Na seção **Variáveis de sistema**, clique em **Novo** para criar a variável.

	d. Na caixa de diálogo **Nova variável de sistema**, digite **ORACLE\_HOSTNAME** para o nome da variável e digite o nome do computador da VM como o valor. Para determinar o nome do computador, abra um prompt de comando e execute **SET COMPUTERNAME** (a saída desse comando conterá o nome do computador).

	e. Clique em **OK** para salvar a nova variável de ambiente e feche a caixa de diálogo **Nova variável de sistema**.

	f. Feche as outras caixas de diálogo que foram abertas pelo Painel de Controle.

7.	No Windows, clique em **Iniciar** e digite **Assistente de Configuração do Banco de Dados**. Clique no ícone **Assistente de Configuração do Banco de Dados**.

8.	No **Assistente de Configuração do Banco de Dados**, forneça valores conforme necessário para cada etapa da caixa de diálogo:

	a. **Etapa 1:** clique em **Criar Banco de Dados** e em **Próximo**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

	b. **Etapa 2:** insira um valor para **Nome do Banco de Dados Global**. Insira e confirme um valor para **Senha Administrativa**. Essa senha é destinada ao usuário **SYSTEM** do seu banco de dados Oracle. Desmarque **Criar como Banco de Dados do Contêiner**. Clique em **Próximo**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

	c. **Etapa 3:** a verificação de pré-requisitos deve continuar automaticamente, avançando até a **Etapa 4**.

	d. **Etapa 4:** examine as opções **Criar Banco de Dados – Resumo** e clique em **Concluir**.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)
	e. **Etapa 5:** a **Página de Progresso** relatará o status da criação do banco de dados.

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)
	f. Depois de criar o banco de dados, você terá a opção de usar a caixa de diálogo **Gerenciamento de Senha**. Modifique as configurações de senha, se necessário, para atender aos seus requisitos e feche as caixas de diálogo para sair do **Assistente de Configuração do Banco de Dados**.

##Para confirmar que o banco de dados está instalado

1.	Ainda conectado à sua VM, inicie um prompt de comando do SQL Plus. No Windows, clique em *Iniciar** e digite **SQL Plus**. Clique no ícone do **SQL Plus**.

2.	Quando solicitado, faça logon usando o nome de usuário de **SYSTEM** e a senha que você especificou quando criou o banco de dados Oracle.

3.	Execute o comando a seguir no prompt de comando do SQL Plus.

		**select * from GLOBAL\_NAME;**

	O resultado deve ser o nome global do banco de dados que você criou.

	![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

##Permitir que o banco de dados seja acessado remotamente
Para permitir que o banco de dados seja acessado remotamente (por exemplo, de um computador cliente executando código Java), você precisa iniciar o ouvinte de banco de dados, abrir a porta 1521 no firewall da máquina virtual e criar um ponto de extremidade público para a porta 1521.

### Iniciar o ouvinte de banco de dados
1.	Faça logon na VM.

2.	Abra um prompt de comando.

3.	No prompt de comando, execute o comando a seguir.

		**lsnrctl start**

> [AZURE.NOTE]É possível executar **lsnrctl status** para verificar o status do ouvinte. Quando desejar interromper o ouvinte, é possível executar **lsnrctl stop**.

### Abrir a porta 1521 no firewall da máquina virtual

1.	Quando ainda estiver conectado à máquina virtual, no Windows, clique em **Iniciar**, digite **Firewall do Windows com Segurança Avançada** e clique no ícone **Firewall do Windows com Segurança Avançada**. Isso abre o console de gerenciamento do **Firewall do Windows com Segurança Avançada**.

2.	No console de gerenciamento do firewall, clique em **Regras de Entrada** no painel esquerdo (se você não vir **Regras de Entrada**, expanda o nó superior no painel à esquerda) e, em seguida, clique em **Nova Regra** no painel direito.

3.	Para o **Tipo de Regra**, selecione **Porta** e clique em **Próximo**.

4.	Para **Protocolo e Porta**, selecione **TCP**, selecione **Portas locais específicas**, digite **1521** para a porta e clique em **Próximo**.

5.	Selecione **Permitir a conexão** e clique em **Avançar**.

6.	Aceite os padrões para os perfis aos quais a regra se aplica e clique em **Próximo**.

7.	Especifique um nome para a regra e, opcionalmente, uma descrição e clique em **Concluir**.

### Criar um ponto de extremidade público para a porta 1521

1.	Faça logon no [Portal do Azure](https://ms.portal.azure.com/).

2.	Clique em **Procurar**.

3.  Clique em **Máquinas Virtuais**.

4.	Selecione a máquina virtual.

5.	Clique em **Configurações**.

6.	Clique em **Pontos de Extremidade**.

7.	Clique em **Adicionar**.

8.	Especifique um nome para o ponto de extremidade:

	a. Use **TCP** para o protocolo.

	b. Use **1521** para a porta pública.

	c. Use **1521** para a porta privada.

9.	Deixe o restante das opções como estão.

10. Clique em **OK**.

##Habilitando o acesso remoto do Oracle Database Enterprise Manager
Se você deseja habilitar o acesso remoto para o Oracle Database Enterprise Manager, abra a porta 5500 no firewall e crie um ponto de extremidade de máquina virtual para 5500 no portal clássico do Azure (usando as etapas anteriores para abrir a porta 1521 e criar um ponto de extremidade para 1521). Em seguida, para executar o Oracle Enterprise Manager do computador remoto, abra um navegador na URL na forma de `http://<<unique_domain_name>>:5500/em`.

> [AZURE.NOTE]Você pode determinar o valor de *<<nome\_exclusivo\_do\_domínio>>* no [portal clássico do Azure](https://ms.portal.azure.com/) clicando em **Máquinas Virtuais** e selecionando a máquina virtual que está sendo usada para executar o Oracle Database).

##Configurando opções populares e pacotes de opções avançadas
Se você escolheu o pacote **Oracle Database com Opções Populares** ou o **Oracle Database com Opções Avançadas**, a próxima etapa será configurar os recursos de complemento na instalação do Oracle. Consulte a documentação do Oracle para obter instruções sobre como configurá-los no Windows, já que as configurações podem variar amplamente com base nas necessidades de cada componente individual.

O **pacote do Oracle Database com Opções Populares** inclui o Oracle Database Enterprise Edition e instâncias incluídas na licença do [Partitioning](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), [Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), [Oracle Tuning Pack for Database](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), [Oracle Diagnostics Pack for Database](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ) e [Oracle Lifecycle Management Pack for Database](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html).

O **pacote do Oracle Database com Opções Avançadas** inclui instâncias incluídas na licença de todas as opções do pacote de Opções Populares, além da [Compactação Avançada](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), [Segurança Avançada](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), [Segurança de Rótulo](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), [Cofre do Banco de Dados](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), [Análise Avançada](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), [OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Espacial e Gráfico](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Cache de Banco de Dados em Memória](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), [Pacote de Mascaramento de Dados](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB) e o Oracle Test Data Management Pack (como parte do Pacote de Mascaramento de Dados).

##Recursos adicionais
Agora que você configurou sua máquina virtual e criou o banco de dados, consulte os seguintes tópicos para obter informações adicionais.

-	[Imagens de máquinas virtuais Oracle — diversas considerações](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Biblioteca de documentação do Oracle Database 12c](http://www.oracle.com/pls/db1211/homepage)

-	[Conexão ao Oracle Database de um aplicativo Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-	[Imagens de máquina virtual Oracle para Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

-	[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_1203_2015-->