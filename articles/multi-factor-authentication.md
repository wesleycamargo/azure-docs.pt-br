<properties urlDisplayName="" pageTitle="O que é o Azure Multi-Factor Authentication?" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="What is Azure Multi-Factor Authentication?" authors="billmath" solutions="" manager="terrylan" editor="lisatoft" />

<tags ms.service="multi-factor-authentication" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="billmath" />





<h1 id="whatiswamfa">O que é a Autenticação Multifator do Azure?</h1>

A autenticação multifator ou de dois fatores é um método de autenticação que exige o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança aos logons e às transações dos usuários. Ela funciona, exigindo dois ou mais dos métodos de verificação a seguir: 

* Algo que você sabe (normalmente, uma senha)
* Algo que você tem (um dispositivo confiável que não pode ser facilmente clonado, como um telefone)
* Algo seu (biometria)

A segurança da autenticação multifator baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga saber a senha do usuário, isso será inútil se ele também não tiver posse do dispositivo confiável. Por outro lado, se o usuário perder o dispositivo, quem encontrar esse dispositivo não conseguirá usá-lo, a menos que também saiba a senha do usuário.
O Azure Multi-Factor Authentication é o serviço Multi-Factor Authentication que exige que os usuários também verifiquem as entradas usando um aplicativo móvel, uma chamada telefônica ou uma mensagem de texto. Ela está disponível para uso com o Azure Active Directory, para proteger os recursos locais com o Servidor de Autenticação Multifator do Azure e com aplicativos personalizados e diretórios que usam o SDK. 

![Azure Multi-Factor Authentication](./media/multi-factor-authentication/WAMFA1.png)

<h3>Protegendo o Azure Active Directory na nuvem</h3>

Habilite o Multi-Factor Authentication para as identidades do AD do Azure, e os usuários receberão uma solicitação para configurar a verificação adicional na próxima vez que entrarem. Use o Multi-Factor Authentication para proteger o acesso ao Azure, ao Microsoft Online Services, como o Office 365, e ao Dynamics CRM Online, bem como aos serviços de nuvem de terceiros que integram o AD do Azure sem nenhuma configuração adicional. A autenticação multifator pode ser habilitada rapidamente para grandes números de usuários e aplicativos globais.  [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn249466.aspx)

<h3>Protegendo os recursos locais e o Active Directory</h3>

Habilite o Multi-Factor Authentication em seus recursos locais, como o IIS e o Active Directory, usando o Servidor Azure Multi-Factor Authentication.  O Servidor de Autenticação Multifator do Azure permite que o administrador seja integrado à autenticação do IIS para proteger aplicativos web do Microsoft IIS, a autenticação RADIUS, a autenticação LDAP e a autenticação do Windows.  [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn249467.aspx)
<h3>Protegendo aplicativos personalizados</h3>

	Um SDK permite a integração direta com os seus serviços de nuvem. Compile a verificação da chamada telefônica e da mensagem de texto do Active Authentication nos processos de entrada ou de transação do seu aplicativo e aproveite o banco de dados de usuário existente do seu aplicativo. [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn249464.aspx)

<h3>Autenticação Multifator para Office 365</h3>

O Multi-Factor Authentication para Office 365, do Azure Multi-Factor Authentication, funciona exclusivamente com aplicativos do Office 365 e é gerenciada no portal do Office 365. Portanto, os administradores agora podem proteger seus recursos do Office 365 com a autenticação multifator. [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn383636.aspx)

<h3>Autenticação Multifator para administradores do Azure</h3>

O mesmo subconjunto de funcionalidade do Multi-Factor Authentication para Office 365 estará disponível sem custo algum para todos os administradores do Azure. Cada conta administrativa de uma assinatura do Azure agora pode obter proteção adicional, habilitando essa funcionalidade essencial de autenticação multifator. Portanto, um administrador que queira acessar o portal do Azure para criar uma VM, um site, gerenciar armazenamento, serviços móveis ou qualquer outro serviço do Azure poderá adicionar a autenticação multifator à sua conta de administrador.  [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn249471.aspx)

<h3>Comparação dos recursos da Autenticação Multifator</h3>

A seguir são apresentadas as versões de autenticação multifator que estão disponíveis e um breve resumo dos recursos que elas oferecem. Você pode usar isso para determinar qual versão de autenticação multifator é mais adequada. [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn249471.aspx)

![Azure Multi-Factor Authentication Feature Comparison](./media/multi-factor-authentication/mfacomparison1.png)


**Recursos adicionais**

* [Inscrever-se no Azure como uma organização](/pt-br/manage/services/identity/organizational-account/)
* [Identidade do Azure](/pt-br/manage/windows/fundamentals/identity/)
* [Biblioteca de Autenticação Multifator do Azure](http://technet.microsoft.com/pt-br/library/dn249471.aspx)

