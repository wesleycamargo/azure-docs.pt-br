<properties linkid="manage-services-sql-databases-premium" urlDisplayName="Banco de Dados SQL Premium" pageTitle="Inscreva-se para o Premium para Banco de Dados SQL do Azure" metaKeywords="" description="Descreve como inscrever-se para a visualização do Premium do Banco de Dados SQL, solicitar sua cota de banco de dados Premium e, em seguida, atualizar um banco de dados para o Premium no Banco de Dados SQL do Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="Inscrever-se para a visualização do Premium para Banco de Dados SQL do Azure" authors="karaman" solutions="" manager="" editor="tysonn" />





#Inscrever-se para a visualização do Premium para Banco de Dados SQL do Azure
Neste tutorial, você aprenderá as etapas necessárias para participar da visualização do Premium para Banco de Dados SQL.

O Banco de Dados SQL do Azure lançou uma visualização limitada de um novo serviço: o Premium para Banco de Dados SQL. Os bancos de dados Premium oferecem recursos reservados para um desempenho mais previsível para aplicativos na nuvem.

[O recurso descrito neste tópico está disponível apenas na visualização. Este tópico é uma documentação de pré-lançamento e está sujeito a alterações em versões futuras.]

##Sumário##

* [Etapa 1: Inscrever-se para a visualização do Premium para Banco de Dados SQL](#SignUp)
* [Etapa 2: Solicitar cota de banco de dados Premium](#Quota)
* [Etapa 3: Criar um banco de dados Premium ](#Upgrade)

<h2><a id="SignUp"></a>Etapa 1: Inscrever-se para a visualização do Premium para Banco de Dados SQL</h2>
A primeira etapa para aproveitar esse recurso é inscrever sua assinatura para a visualização do Premium para Banco de Dados SQL.

1. Entre na página [Recursos de visualização do Azure](http://account.windowsazure.com/PreviewFeatures) usando sua conta da Microsoft.

	**Observação** - Apenas os administradores da conta do Azure podem acessar o portal de contas. Se você não for o administrador da conta da sua assinatura, solicite que a pessoa conclua o processo de inscrição de sua assinatura. Para obter mais informações sobre contas e assinaturas do Azure, consulte [Opções de compra](http://account.windowsazure.com/PreviewFeatures).
 
	![Imagem1](./media/sql-database-premium-sign-up/AccountSignup-Figure1.png)

2. Localize o item **Premium para Banco de Dados SQL** na lista de recursos de visualização e clique no botão **teste agora** associado ao item.

	![Imagem2](./media/sql-database-premium-sign-up/AccountSignupButton-Figure2.png)


3. Escolha a assinatura que deseja inscrever para a visualização.

	![Imagem3](./media/sql-database-premium-sign-up/Subscription-Figure3.png)

	Apenas assinaturas ativas e pagas do Azure estão qualificadas para a visualização. Você pode inscrever várias assinaturas, mas cada assinatura pode ser inscrita apenas uma vez. 

	A inscrição de uma assinatura para a visualização não incorrerá em encargos adicionais, mas, uma vez ativada e concedida a cota Premium, a criação ou a atualização para um banco de dados Premium estará sujeita ao preço descrito na [página de preços do Banco de Dados SQL](http://www.windowsazure.com/pt-br/pricing/details/sql-database/)

	O status atual da solicitação de inscrição é refletido na lista de recursos de visualização.

	![Imagem4](./media/sql-database-premium-sign-up/Status-Figure4.png)

4. As solicitações serão aprovadas com base na capacidade e na demanda atuais. Você talvez tenha de aguardar até 2 dias para que sua assinatura seja ativada, com tempos de espera maiores indicando uma alta demanda ou uma capacidade de visualização pública preenchida.

5. Você receberá uma notificação por email quando sua assinatura for ativada para a visualização. 


<h2><a id="Quota"></a>Etapa 2: Solicitar cota de banco de dados Premium</h2>
Após sua assinatura ser ativada para a visualização, você deverá solicitar a cota de banco de dados Premium para cada servidor no qual pretende criar um banco de dados Premium. Como a capacidade é limitada, solicite cotas apenas para os servidores em que pretende criar um banco de dados Premium e cancele todas as solicitações pendentes desnecessárias.


1.	Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) usando sua conta da Microsoft.

	**Observação** - Os administradores da conta, os administradores de serviços e os coadministradores da assinatura poderão solicitar cotas após a assinatura ser inscrita para a visualização.

2.	Navegue até a lista de **servidores** na extensão **Banco de Dados SQL**.
3.	Selecione o servidor para o qual você pretende solicitar cota de banco de dados Premium.
4.	Navegue até **Início Rápido** do servidor selecionado clicando no ícone de raio na barra de navegação superior.
5.	Clique em **Solicitar Cota de Banco de Dados Premium** na seção **Banco de Dados Premium**.

	![Imagem6](./media/sql-database-premium-sign-up/RequestQuota-Figure6.png)


	Após sua solicitação ser enviada, você poderá ter de aguardar até 5 dias para receber a cota. Tempos de espera maiores podem indicar alta demanda ou capacidade de visualização preenchida.
	
	Algumas observações adicionais sobre as solicitações de cota de banco de dados Premium:
	
	- A cota não está disponível para clientes com assinaturas de avaliação gratuita.
	- A cota é limitada inicialmente e as solicitações serão concedidas com base na demanda atual e na capacidade disponível.
	- A Microsoft pode recuperar cotas não utilizadas após 15 dias.
	- Apenas uma solicitação de cota pode ser enviada para cada servidor lógico na assinatura.
	- Inicialmente, a cota é limitada a um banco de dados por servidor lógico.
	- A solicitação de cota de banco de dados é gratuita, entretanto, a criação de um banco de dados da edição Premium ou a atualização de um banco de dados da edição Web ou Business para a edição Premium aumentará o custo do banco de dados.
6.	Você pode ver o status de sua solicitação de cota na página **Início Rápido** do servidor.

	![Imagem7](./media/sql-database-premium-sign-up/PendingApproval-Figure7.png)
	

7.	Você receberá uma notificação por email quando sua solicitação de cota do banco de dados Premium for concedida e a cota estiver disponível para uso.
8.	Após a concessão, você poderá ver uma cota de banco de dados Premium remanescente do servidor na guia **Início Rápido** ou **Painel**.

	![Imagem8](./media/sql-database-premium-sign-up/QuotaApproved-Figure8.png)

<h2><a id="Upgrade"></a>Etapa 3: Criar um banco de dados Premium</h2>


Após receber a cota, você poderá criar um novo banco de dados da edição Premium ou atualizar um banco de dados Web ou Business existente para Premium a fim de aproveitar a capacidade reservada e o desempenho mais previsível. 

![Imagem9](./media/sql-database-premium-sign-up/SpecifyDBSettings-Figure9.png)


![Imagem10](./media/sql-database-premium-sign-up/PremiumDBSettings-Figure10.png)


Para obter mais informações, consulte [Gerenciando um banco de dados Premium (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=311927).

	
	
**Observação:** você pode alterar o status Premium ou o tamanho da reserva de seu banco de dados apenas uma vez em um período de 24 horas.
<h2><a id="NextSteps"></a>Próximas etapas</h2>
Para obter informações adicionais sobre bancos de dados Premium, consulte:

* [Gerenciando um Banco de Dados Premium](http://go.microsoft.com/fwlink/p/?LinkID=311927)
* [Diretrizes de Premium para Banco de Dados SQL](http://go.microsoft.com/fwlink/p/?LinkId=313650)


















