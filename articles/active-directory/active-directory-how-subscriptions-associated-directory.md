<properties 
	pageTitle="Como as assinaturas do Azure estão associadas ao Azure AD" 
	description="Um tópico sobre como entrar no Microsoft Azure e problemas relacionados, como a relação entre uma assinatura do Azure e o AD do Azure." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# Como as assinaturas do Azure estão associadas ao Azure AD

Este tópico aborda informações sobre como entrar no Microsoft Azure e problemas relacionados, como a relação entre uma assinatura do Azure e o AD (Active Directory) do Azure.

## Contas que você pode usar para entrar
Vamos começar com as contas que você pode usar para entrar. Há dois tipos: uma conta da Microsoft (anteriormente conhecida como Microsoft Live ID) e uma conta corporativa ou de estudante, que é uma conta armazenada no AD do Azure.

 Conta da Microsoft | Conta do AD do Azure
	------------- | -------------
O sistema de identidade de consumidor executado pela Microsoft | O sistema de identidade comercial executado pela Microsoft
Autenticação aos serviços que são voltados ao consumidor, como Hotmail e MSN | Autenticação aos serviços que são voltados a negócios, como o Office 365
Os consumidores criam suas próprias contas da Microsoft, como ocorre ao se inscreverem para email | Empresas e organizações criam e gerenciam suas próprias contas corporativas ou de estudante
As identidades são criadas e armazenadas no sistema de contas da Microsoft | As identidades são criadas usando o Azure ou outros serviços como o Office 365, e elas são armazenadas em uma instância do AD do Azure atribuída à organização

Embora o Azure originalmente permitisse acesso somente por usuários de contas da Microsoft, ele agora permite o acesso por usuários de *ambos* os sistemas. Isso foi feito fazendo com que todas as propriedades do Azure confiassem no AD do Azure para autenticação, tornando o AD do Azure responsável por autenticar usuários da organização e criando um relacionamento de federação em que o AD do Azure confia no sistema de identidade do consumidor de conta da Microsoft para autenticar usuários consumidores. Como resultado, o AD do Azure é capaz de autenticar contas de "convidado" da Microsoft e também contas "nativas" do AD do Azure.

Por exemplo, aqui um usuário com uma conta da Microsoft entra no Portal de Gerenciamento do Azure.

> [AZURE.NOTE]Para entrar no Portal de Gerenciamento do Azure, msmith@hotmail.com deve ter uma assinatura do Azure. A conta deve ser de um administrador de serviços ou de um coadministrador da assinatura.

![][1]

Como esse endereço de Hotmail é de uma conta de consumidor, a entrada é autenticada pelo sistema de identidade de consumidor da conta da Microsoft. O sistema de identidade do AD do Azure confia na autenticação feita pelo sistema de conta da Microsoft e emitirá um token para acessar os serviços do Azure.

## Como uma assinatura do Azure está relacionada ao AD do Azure

Cada assinatura do Azure tem uma relação de confiança com uma instância do AD do Azure. Isso significa que ela confia que esse diretório autentique usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas uma única assinatura confia em apenas um diretório. Você pode ver qual diretório é confiável para sua assinatura na guia Configurações. Você pode [editar as configurações de assinatura](https://msdn.microsoft.com/library/azure/dn584083.aspx) para alterar o diretório no qual ela confia.

Essa relação de confiança que uma assinatura tem com um diretório é diferente da relação que uma assinatura tem com todos os outros recursos no Azure (sites, bancos de dados e assim por diante), que são mais similares a recursos filho de uma assinatura. Se uma assinatura expira, o acesso a esses outros recursos associados à assinatura também pára. Mas o diretório permanece no Azure e você pode associar outra assinatura a ele, além de continuar a gerenciar os usuários do diretório.

Do mesmo modo, a extensão do AD do Azure que você pode ver na sua assinatura não funciona como outras extensões no Portal de Gerenciamento do Azure. Outras extensões no Portal de Gerenciamento limitam-se à assinatura do Azure. O que você vê na extensão do AD não varia com base na assinatura – mostra somente diretórios com base no usuário que entrou.

Todos os usuários têm um único diretório inicial que os autentica, mas eles também podem ser convidados em outros diretórios. Na extensão do AD, você verá todos os diretórios dos quais sua conta de usuário é membro. Diretórios dos quais sua conta não é membro não aparecerão. Um diretório pode emitir tokens para contas corporativas ou de estudante no AD do Azure ou para usuários de contas da Microsoft (porque o AD do Azure é federado com o sistema de contas da Microsoft).

Este diagrama mostra uma assinatura de Michael Smith depois que ele se inscreveu usando uma conta corporativa da Contoso.

![][2]

## Como gerenciar uma assinatura e um diretório
As funções administrativas para uma assinatura do Azure gerenciam recursos vinculados à assinatura do Azure. Essas funções e as práticas recomendadas para gerenciar sua assinatura estão descritas em [Gerenciar contas, assinaturas e funções administrativas](https://msdn.microsoft.com/library/azure/hh531793.aspx).

Por padrão, a função de administrador de serviços é atribuída a você quando você se inscreve. Se outros usuários precisarem entrar e acessar serviços usando a mesma assinatura, você pode adicioná-los como coadministradores. O administrador de serviços e os coadministradores podem ser contas da Microsoft ou contas corporativas ou de estudante do diretório ao qual a assinatura do Azure está associada.

O AD do Azure tem um conjunto diferente de funções administrativas para gerenciar o diretório e os recursos relacionados à identidade. Por exemplo, o administrador global de um diretório pode adicionar usuários e grupos ao diretório ou exigir autenticação multifator para usuários. A função de administrador global é atribuída a um usuário que cria um diretório e ele pode atribuir funções de administrador a outros usuários.

Assim como acontece com os administradores de assinatura, as funções administrativas do AD do Azure podem ser contas da Microsoft ou contas corporativas ou de estudante. Funções administrativas do AD do Azure também são usadas por outros serviços, como o Office 365 e Microsoft Intune. Para obter mais informações, consulte [Atribuindo funções de administrador](https://msdn.microsoft.com/library/azure/dn468213.aspx).

Mas o ponto importante aqui é que os administradores de assinatura do Azure e administradores de diretório do AD do Azure são dois conceitos separados. Os administradores de assinatura do Azure podem gerenciar recursos no Azure e podem exibir a extensão do Active Directory no Portal de Gerenciamento (porque o Portal de Gerenciamento é um recurso do Azure). Os administradores de diretório podem gerenciar propriedades no diretório.

Uma pessoa pode desempenhar ambas as funções, mas isso não é obrigatório. A função de administrador global do diretório pode ser atribuída a um usuário, mas não é possível atribuir a esse usuário as funções de administrador ou coadministrador de serviços de uma assinatura do Azure. Sem ser um administrador da assinatura, esse usuário não pode entrar no Portal de Gerenciamento. Porém, o usuário pode executar tarefas de administração de diretório usando outras ferramentas, como o PowerShell do AD do Azure ou o Admin Center do Office 365.

### Por que não é possível gerenciar o diretório à minha conta de usuário atual?

Às vezes, um usuário pode tentar entrar no Portal de Gerenciamento usando uma conta corporativa ou de estudante antes de inscrever-se em uma assinatura do Azure. Nesse caso, o usuário receberá uma mensagem que não há nenhuma assinatura para essa conta. A mensagem incluirá um link para iniciar uma assinatura de avaliação gratuita.

Após a inscrição para a versão de avaliação gratuita, o usuário verá o diretório para a organização no Portal de Gerenciamento mas não poderá gerenciá-lo (ou seja, não será possível adicionar usuários nem editar as propriedades de quaisquer usuários existentes) porque o usuário não é um administrador global do diretório. A assinatura permite ao usuário usar o Portal de Gerenciamento e ver a extensão do Active Directory, mas as permissões adicionais de um administrador global são necessárias para gerenciar o diretório.

## Usando sua conta de trabalho ou escolar para gerenciar uma assinatura do Azure que foi criada usando uma conta da Microsoft

Como prática recomendada, você deve [inscrever-se no Azure como uma organização](sign-up-organization.md) e usar uma conta corporativa ou de estudante para gerenciar recursos no Azure. Contas corporativas ou de estudante são preferenciais porque podem ser gerenciadas centralmente pela organização que as emitiu, têm mais recursos do que as contas da Microsoft e são autenticadas diretamente pelo AD do Azure. A mesma conta fornece acesso a outros serviços online da Microsoft que são oferecidos a empresas e organizações, como Office 365 ou Microsoft Intune. Se você já tiver uma conta que você usa com essas outras propriedades, você provavelmente desejará usar essa mesma conta com o Azure. Você também já terá uma instância do Active Directory dando suporte a essas propriedades nas quais você deseja que sua assinatura do Azure confie.

Contas corporativas ou de estudante também podem ser gerenciadas de mais maneiras do que uma conta da Microsoft. Por exemplo, um administrador pode redefinir a senha de uma conta corporativas ou de estudante, ou exigir a autenticação multifator para ela.

Em alguns casos, você pode querer que um usuário de sua organização possa gerenciar recursos associados a uma assinatura do Azure para uma conta de consumidor da Microsoft. Para obter mais informações sobre como fazer a transição para ter contas diferentes gerenciando assinaturas ou diretórios, consulte [Gerenciar o diretório para sua assinatura do Office 365 no Azure](#manage-the-directory-for-your-office-365-subscription-in-azure).


## Entrando após ter utilizado seu email de trabalho para sua conta da Microsoft

Se em algum momento no passado você criou uma conta de consumidor Microsoft usando seu email de trabalho como um identificador de usuário, você poderá ver uma página solicitando que você selecione o sistema de conta do Microsoft Azure ou o sistema de conta da Microsoft.

![][3]

Você tem contas de usuário com o mesmo nome, uma no AD do Azure e outra no sistema de conta de consumidor da Microsoft. Você deve selecionar a conta que está associada à assinatura do Azure que você deseja usar. Se você receber um erro informando que não existe uma assinatura para este usuário, provavelmente você escolheu a opção errada. Saia e tente novamente. Para obter mais informações sobre erros que podem impedir que você entre, consulte [Solucionando erros do tipo "Não foi possível encontrar nenhuma assinatura associada à sua conta"](https://social.msdn.microsoft.com/Forums/en-US/f952f398-f700-41a1-8729-be49599dd7e2/troubleshooting-we-were-unable-to-find-any-subscriptions-associated-with-your-account-errors-in?forum=windowsazuremanagement).

## Gerenciar o diretório para sua assinatura do Office 365 no Azure

Digamos que você tenha se inscrito no Office 365 antes de se inscrever no Azure. Agora você deseja gerenciar o diretório para a assinatura do Office 365 no Portal de Gerenciamento. Há duas maneiras de fazer isso, dependendo se você ter se inscrito no Azure ou não.

### Eu não tenho uma assinatura do Azure

Nesse caso, apenas [inscreva-se no Azure](sign-up-organization.md) usando a mesma conta corporativa ou de estudante que você usa para entrar no Office 365. As informações relevantes da conta do Office 365 serão preenchidas previamente no formulário de inscrição do Azure. Sua conta será atribuída à função de administrador de serviços da assinatura.

### Eu tenho uma assinatura do Azure usando minha conta da Microsoft

Se você se inscreveu no Office 365 usando uma conta corporativa ou de estudante e, em seguida, inscreveu-se no Azure usando uma conta da Microsoft, você tem dois diretórios: um para uso corporativo ou de estudante e um diretório padrão, que foi criado durante a inscrição no Azure.

Para gerenciar ambos os diretórios no Portal de Gerenciamento, execute essas etapas.

> [AZURE.NOTE]Essas etapas só podem ser realizadas enquanto o usuário está conectado com uma conta da Microsoft. Se o usuário estiver conectado com uma conta corporativa ou de estudante, a opção **Usar diretório existente** não estará disponível porque uma conta corporativa ou de estudante pode ser autenticada apenas por seu diretório-base (ou seja, o diretório em que a conta corporativa ou de estudante é armazenada, e que é propriedade da corporação ou instituição educacional).

1. Entre no Portal de Gerenciamento usando sua conta da Microsoft.
2. Clique em **Novo** > **Serviços de Aplicativos** > **Active Directory** > **Diretório** > **Criação Personalizada**.
3. Clique em **Usar diretório existente** e marque **Estou pronto para sair agora**, então clique na marca de seleção para concluir a ação.
4. Entrar no Portal de Gerenciamento usando a conta de administrador global da corporação ou instituição educacional.
5. Quando for solicitado a **Usar diretório Contoso com o Azure?**, clique em **continuar**.
6. Clique em **Sair agora**.
7. Entre novamente no Portal de Gerenciamento usando sua conta da Microsoft. Ambos os diretórios aparecerão na extensão do Active Directory.


## O que vem a seguir
[Inscrever-se no Azure como uma organização](sign-up-organization.md)


<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

 

<!---HONumber=August15_HO6-->