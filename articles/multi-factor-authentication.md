<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="O que é a Autenticação Multifator do Azure?" authors="billmath" solutions="" manager="terrylan" editor="lisatoft" />





<h1 id="whatiswamfa">O que é a Autenticação Multifator do Azure?</h1>

A autenticação multifator ou dois fatores é um método de autenticação que exige o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança aos logons e às transações dos usuários. Ela funciona, exigindo dois ou mais dos métodos de verificação a seguir: 

* Algo que você conhece (geralmente uma senha)
* Algo que você tem (um dispositivo confiável que não é facilmente duplicado, como um telefone)
* Algo seu (biometria)

A segurança da autenticação multifator baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga saber a senha do usuário, isso será inútil se ele também não tiver posse do dispositivo confiável. Por outro lado, se o usuário perder o dispositivo, quem encontrar esse dispositivo não conseguirá usá-lo, a menos que ele ou ela também saiba a senha do usuário.
A Autenticação Multifator do Azure é o serviço de autenticação multifator que exige que os usuários também verifiquem as entradas usando um aplicativo móvel, uma chamada telefônica ou uma mensagem de texto. Ela está disponível para uso com o Azure Active Directory, para proteger os recursos locais com o Servidor de Autenticação Multifator do Azure e com aplicativos personalizados e diretórios que usam o SDK. 

![Autenticação Multifator do Azure](./media/multi-factor-authentication/WAMFA1.png)

<h3>Protegendo o Azure Active Directory na nuvem</h3>

Habilite a Autenticação Multifator para as identidades do Azure AD, e os usuários receberão uma solicitação para configurar a verificação adicional na próxima vez que entrarem. Use a Autenticação Multifator para proteger o acesso ao Azure, ao Microsoft Online Services, como o Office 365, e ao Dynamics CRM Online, bem como aos serviços de nuvem de terceiros que integram o Azure AD sem nenhuma configuração adicional. A autenticação multifator pode ser habilitada rapidamente para grandes números de usuários e aplicativos globais.  [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn249466.aspx)

<h3>Protegendo os recursos locais e o Active Directory</h3>

Habilite a Autenticação Multifator para seus recursos locais, como o IIS e o Active Directory, usando o Servidor de Autenticação Multifator do Azure.  O Servidor de Autenticação Multifator do Azure permite que o administrador seja integrado à autenticação do IIS para proteger aplicativos web do Microsoft IIS, a autenticação RADIUS, a autenticação LDAP e a autenticação do Windows.  [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn249467.aspx)
<h3>Protegendo aplicativos personalizados</h3>

Um SDK permite a integração direta com os seus serviços de nuvem. Compile a verificação da chamada telefônica e da mensagem de texto do Active Authentication nos processos de entrada ou de transação do seu aplicativo e aproveite o banco de dados de usuário existente do seu aplicativo. [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn249464.aspx)

<h3>Autenticação Multifator para Office 365</h3>

A Autenticação Multifator para Office 365, da Autenticação Multifator do Azure, funciona exclusivamente com aplicativos do Office 365 e é gerenciado no portal do Office 365. Portanto, os administradores agora podem proteger seus recursos do Office 365 com a autenticação multifator. [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn383636.aspx)

<h3>Autenticação Multifator para administradores do Azure</h3>

O mesmo subconjunto de recursos da Autenticação Multifator para Office 365 estará disponível sem custo algum para todos os administradores do Azure. Cada conta administrativa de uma assinatura do Azure agora pode obter proteção adicional, habilitando essa funcionalidade essencial de autenticação multifator. Portanto, um administrador que queira acessar o portal do Azure para criar uma VM, um site, gerenciar armazenamento, serviços móveis ou qualquer outro serviço do Azure poderá adicionar a autenticação multifator à sua conta de administrador.  [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn249471.aspx)

<h3>Comparação dos recursos da Autenticação Multifator</h3>

A seguir são apresentadas as versões de autenticação multifator que estão disponíveis e um breve resumo dos recursos que elas oferecem. Você pode usar isso para determinar qual versão de autenticação multifator é mais adequada. [Saiba mais](http://msdn.microsoft.com/pt-br/library/dn249471.aspx)

![Comparação dos recursos da Autenticação Multifator](./media/multi-factor-authentication/mfacomparison1.png)


**Recursos adicionais**

* [Inscrever-se no Azure como uma organização](/pt-br/manage/services/identity/organizational-account/)
* [Identidade do Azure](/pt-br/manage/windows/fundamentals/identity/)
* [Biblioteca de Autenticação Multifator do Azure](http://technet.microsoft.com/pt-br/library/dn249471.aspx)


