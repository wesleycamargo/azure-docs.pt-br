<properties 
	pageTitle="Perguntas frequentes sobre o Azure Multi-Factor Authentication" 
	description="Fornece uma lista de perguntas frequentes e respostas relacionadas à MFA (Azure Multi-Factor Authentication). A MFA é um método de verificar quem você é e que requer o uso de mais do que apenas um nome de usuário e uma senha. Ela fornece uma camada adicional de segurança para logons de usuário e transações." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2016" 
	ms.author="billmath"/>

# Perguntas frequentes sobre o Azure Multi-Factor Authentication


Essas Perguntas frequentes respondem a perguntas sobre o Azure Multi-Factor Authentication. Essas Perguntas frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança e a usabilidade.

## Geral

**P: como posso obter ajuda com o Azure Multi-Factor Authentication?**

[Pesquise a Base de Dados de Conhecimento (KB) Microsoft](http://search.microsoft.com/supportresults.aspx?form=mssupport&q=phonefactor)

- Pesquise a Base de Dados de Conhecimento (KB) Microsoft em busca de soluções técnicas para problemas comuns de conserto sobre Suporte para o Servidor Microsoft Azure Multi-Factor Authentication (PhoneFactor).

[Fóruns do Active Directory do Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

- Você pode pesquisar e procurar perguntas e respostas técnicas na comunidade ou fazer sua própria pergunta clicando [aqui](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Redefinição de senha](mailto:phonefactorsupport@microsoft.com)

- Clientes herdados do Phonefactor que tenham consultas sobre redefinir sua senha ou obter ajuda obter a redefinição da senha, usem o link abaixo para abrir um caso de suporte.

[Suporte ao cliente para o Servidor Microsoft Azure Multi-Factor Authentication (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947)

- Use este link para contatar um profissional do suporte da Microsoft. Faremos a você algumas perguntas que nos ajudarão a determinar quais são suas opções de suporte. As opções de suporte podem incluir email, envio online ou suporte por telefone.



**P: Como o Servidor Azure Multi-Factor Authentication lida com os dados do usuário?**

Quando você usa o servidor MFA (Multi-Factor Authentication) local, os dados do usuário são armazenados nos servidores locais. Nenhum dado de usuário persistente é armazenado na nuvem. Quando o usuário executa a autenticação de dois fatores, o servidor MFA envia dados para o serviço de nuvem do Azure MFA para realizar a autenticação. Quando essas solicitações de autenticação são enviadas ao serviço de nuvem, os campos a seguir são enviados na solicitação e nos logs para que eles fiquem disponíveis em relatórios de uso/autenticação do cliente. Alguns dos campos são opcionais; portanto, podem ser habilitados ou desabilitados no servidor Multi-Factor Authentication. A comunicação do servidor MFA para o serviço de nuvem MFA usa saída SSL/TLS pela porta 443. Esses campos são:

- ID exclusiva: nome de usuário ou ID interna do servidor MFA
- Nome e sobrenome: opcional
- Endereço de email: opcional
- Número de telefone: ao fazer uma chamada de voz ou SMS de autenticação
- Token de dispositivo: ao fazer autenticação de aplicativos móveis
- Modo de autenticação 
- Resultado da autenticação 
- Nome do servidor MFA 
- Servidor IP MFA 
- Cliente IP: se disponível



Além dos campos acima, o resultado da autenticação (êxito/negação) e o motivo de uma possível recusa também são armazenados com os dados de autenticação e ficam disponíveis por meio de relatórios de autenticação/uso.




## Cobrança

**P: minha organização será cobrada por ligações ou mensagens de texto usadas para autenticar meus usuários?**

Todos os custos são distribuídos no custo por usuário ou por autenticação do serviço. As organizações não são cobradas pelas ligações individuais feitas ou mensagens de texto enviadas ao usuários finais ao usar o Azure Multi-Factor Authentication. Os proprietários do telefone podem incorrer em custos relacionados ao roaming ou outros custos das respectivas operadoras para receber as ligações ou as mensagens de texto.

**P: como a organização é cobrada pelo Multi-Factor Authentication?**

O Azure Multi-Factor Authentication está disponível como um serviço autônomo com opções de cobrança por usuário e por autenticação, ou em conjunto com o Active Directory Premium do Azure e o Enterprise Mobility Suite. O serviço independente está disponível com base no consumo e é cobrado mensalmente no compromisso monetário do Azure, ou como uma licença anual por usuário disponível por meio de um Microsoft Enterprise Agreement, do Programa de Licença de Volume Aberto, do programa de Fornecedores de Soluções na Nuvem e Diretamente.

>[AZURE.IMPORTANT]
As regiões da Austrália estão disponíveis a clientes com presença comercial na Austrália ou na Nova Zelândia.

Modelos de cobrança | Preço
------------- | ------------- |
Consumo por usuário (compromisso monetário do Azure)| US$ 1,40 por mês (autenticações ilimitadas)
Consumo por autenticação (compromisso monetário do Azure)|$1,40 por 10 autenticações
Licença anual por usuário (Direto)|US$ 1,40 por mês (autenticações ilimitadas)
Licença anual por usuário (Licenciamento por Volume)|Entre em contato com seu representante do [Enterprise Agreement.](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx)

**P: o modelo de cobrança ‘por usuário’ se baseia no número de usuários habilitados para o Multi-Factor Authentication ou no número de usuários que executam as verificações?**

A cobrança é baseada no número de usuários habilitados para o Multi-Factor Authentication.

**P: Como funciona a cobrança da Multi-Factor Authentication?**

Ao usar o modelo de uso/cobrança de consumo por usuário ou por autenticação, os administradores escolhem o tipo de uso ao criar um Provedor de autenticação multifator no Portal Clássico do Microsoft Azure. Trata-se de um recurso cobrado da assinatura do Azure da organização, assim como as máquinas virtuais, sites etc. Ao usar o modelo de licença, as licenças do Azure Multi-Factor Authentication são compradas e então atribuídas aos usuários, assim como o Office 365 e outros produtos de assinatura.

**P: Existe uma versão gratuita do Azure Multi-Factor Authentication para administradores?**

Um subconjunto de recursos do Azure Multi-Factor Authentication conhecido como “MFA para Administradores do Azure” é oferecido sem custos aos Administradores Globais do Azure quando um Provedor de Azure Multi-Factor Authentication baseado em consumo não tiver sido associado ao Active Directory do Azure correspondente. O uso do Provedor de Multi-Factor Authentication atualiza todos os administradores e usuários no diretório que são habilitados para MFA para a versão completa do Azure Multi-Factor Authentication.

**P: Existe uma versão gratuita do Azure Multi-Factor Authentication para usuários do Office 365?**

Um subconjunto de recursos do Azure Multi-Factor Authentication conhecido como “MFA para o Office 365” é oferecido sem custos aos usuários que possuem uma licença do Office 365 atribuída quando um Provedor de Azure Multi-Factor Authentication baseado em consumo não tiver sido associado ao Active Directory do Azure correspondente. O uso do Provedor de Multi-Factor Authentication atualiza todos os administradores e usuários no diretório que são habilitados para MFA para a versão completa do Azure Multi-Factor Authentication.

**P: Posso alternar entre os modelos de cobrança de consumo por usuário e por autenticação a qualquer momento?**

O modelo de cobrança é selecionado durante a criação do recurso e não pode ser alterado após o provisionamento do recurso. No entanto, é possível criar um novo recurso de Multi-Factor Authentication para substituir o original. Observe que as opções de configuração e definições do usuário não podem ser transferidas para o novo recurso.

**P: Posso alternar entre a cobrança de consumo e os modelos de licença a qualquer momento?**

Você pode adquirir o Azure Multi-Factor Authentication, o Active Directory Premium do Azure, o Enterprise Mobility Suite e licenças do Enterprise Cloud Suite a qualquer momento. Quando as licenças são adicionadas a um diretório que já tem um provedor do Azure Multi-Factor Auth por usuário, a cobrança com base no consumo é subtraída pelo número de licenças possuídas. Se todos os usuários habilitados para MFA tiverem licenças atribuídas, o administrador poderá excluir o Provedor de Autenticação Multifator. As organizações não podem misturar cobrança de consumo por autenticação com o modelo de licença. Quando um Provedor de Autenticação Multifator for vinculado a um diretório, a organização será cobrada por todas as solicitações de verificação de MFA, independentemente das licenças possuídas.

**P: Minha organização precisa usar e sincronizar identidades para usar a Multi-Factor Authentication?**

Ao usar os modelos de cobranças com base no consumo, o Active Directory do Azure não é necessário. A vinculação de um Provedor de Autenticação Multifator a um diretório é opcional. Quando ele não for vinculado a um diretório, as organizações poderão usar o servidor do Azure Multi-Factor Authentication ou o SDK no local. Quando você usar o modelo de licença, o Active Directory do Azure será necessário, pois as licenças são adicionadas ao diretório quando adquiridas e atribuídas aos usuários no diretório.


## Facilidade de uso

**P: o que devo fazer se eu não receber uma resposta no meu telefone ou se esquecer do meu telefone?**

Se, anteriormente, você configurou um telefone de backup, tente novamente selecionando esse telefone quando solicitado na página de entrada. Se você não tiver outro método configurado, contate seu administrador e peça a ele para atualizar o número atribuído ao seu telefone principal – celular ou comercial.


**P: o que fazer se um usuário contatar você, o administrador, com uma conta que foi bloqueada?**

Você pode redefinir o usuário, forçando-o a passar pelo processo de registro novamente. Para fazer isso, confira [Gerenciando as configurações de dispositivo e usuário com o Azure Multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md)

**P: o que fazer se um usuário tiver perdido ou teve um dispositivo roubado que está usando senhas de aplicativo?**

Você pode excluir todas as senhas de aplicativo dos usuários para impedir qualquer acesso não autorizado. Assim que tiver um dispositivo de substituição, o usuário poderá recriá-las. Para fazer isso, confira [Gerenciando as configurações de dispositivo e usuário com o Azure Multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md)

**P: e se o usuário não conseguir entrar nos aplicativos que não usam navegador?**

- Os usuários habilitados para autenticação multifator exigirão a senha de aplicativo para entrar em alguns aplicativos que não usam navegador.
- Os usuários precisarão limpar as informações de entrada (excluir as informações para conexão), reiniciar o aplicativo e entrar com os respectivos nome de usuário e senha de aplicativo. 

Para obter informações sobre como criar senhas de aplicativo, confira [Ajuda com senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md)


>[AZURE.NOTE] Autenticação moderna para os clientes do Office 2013
>
> Os clientes do Office 2013 (incluindo o Outlook) agora têm suporte a novos protocolos de autenticação e podem ser habilitados para oferecer suporte ao Multi-Factor Authentication. Isso significa que, uma vez habilitadas, as senhas de aplicativo não são necessárias para usar com os clientes do Office 2013. Para saber mais, confira [Anúncio da visualização pública da autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**P: o que devo fazer se eu não receber uma mensagem de texto ou responder a uma mensagem de texto bidirecional, mas o tempo limite da verificação se esgotar?**

O serviço Azure Multi-Factor Authentication envia mensagens de texto usando agregadores de SMS. Muitos fatores podem afetar a confiabilidade da entrega e do recebimento da mensagem de texto, incluindo o agregador usado, o país de destino, a operadora do celular e a intensidade do sinal. Portanto, a entrega de mensagens de texto e o recebimento de respostas SMS ao executar o SMS bidirecional não são garantidos. É recomendável usar SMS unidirecional em vez de SMS bidirecional sempre que possível, pois além de ser mais confiável, evita que os usuários incorram em cobranças globais de SMS geradas por responder a uma mensagem de texto que foi enviada de outro país.

As verificações de mensagem de texto também são mais confiáveis em alguns países, como os Estados Unidos e o Canadá. Os usuários que enfrentam dificuldades para receber mensagens de texto de modo confiável ao usar o Azure Multi-Factor Authentication são estimulados a selecionar os métodos de ligação ou aplicativo móvel. O aplicativo móvel é ótimo porque as notificações de aplicativo móvel podem ser recebidas por conexões celulares e Wi-Fi, e o código de acesso do aplicativo móvel é exibido mesmo quando o dispositivo não tem nenhum sinal. O aplicativo Azure Authenticator está disponível para [Windows Phone](http://www.windowsphone.com/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

**P: Posso usar tokens de hardware com o servidor do Azure MFA?**

Se você estiver usando o Servidor do Azure MFA, tokens OATH TOTP de terceiros podem ser importados e usados para a MFA. Atualmente, há suporte para a importação de tokens OATH TOTP de terceiros em um formato PSKC anterior que a Gemalto pode produzir para seus tokens e suporte à importação de tokens no formato CSV. Ao importar tokens no formato CSV, o arquivo CSV deve conter um número de série, uma chave secreta no formato Base32 e um intervalo de tempo (normalmente de 30 segundos).

Portanto, se os tokens de ActiveIdentity forem tokens OATH TOTP e você conseguir obter o arquivo de chave secreta em um arquivo CSV que pode ser importado para o Servidor do Azure MFA, você poderá usá-los. Os tokens OATH podem ser usados com o AD FS, com o RADIUS quando o sistema de cliente pode processar as respostas do Access Challenge e com a autenticação baseada em Formulários do IIS.

**P: Posso usar o servidor Azure MFA para proteger os Serviços de Terminal**

Sim, mas se você estiver usando o Windows Server 2012 R2 ou posterior, isso só poderá ser feito usando um Gateway de Área de Trabalho Remota.

A maneira como o servidor Azure MFA conectava-se ao pacote de segurança LSA no Windows Server 2012 e em versões anteriores não funciona mais no Windows Server 2012 R2 devido a algumas alterações de segurança. Portanto, para versões dos Serviços de Terminal no Windows 2012 ou versões anteriores, basta seguir [Para proteger um aplicativo com a Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)

No entanto, se você estiver usando o Windows Server 2012 R2, você precisará de um Gateway de Área de Trabalho Remota.

**P: Por que estou recebendo uma chamada MFA de um chamador anônimo depois de configurar a identificação de chamadas?**

Às vezes, quando as chamadas MFA são feitas pela rede telefônica pública, elas são roteadas por meio de uma operadora que não oferece suporte à identificação de chamadas. Portanto, a ID de chamadas não é garantida, embora o sistema MFA sempre a envie.


## Erros

**P: o que devo fazer quando vejo o erro “A solicitação de autenticação não é para uma conta ativada” quando autentico usando as notificações do aplicativo móvel?**

- Acesse [https://account.activedirectory.windowsazure.com/profile/](https://account.activedirectory.windowsazure.com/profile/) e entre com sua conta organizacional.
- Se necessário, clique em Outras opções de verificação e selecione outra opção para concluir a verificação da conta.
- Clique em Verificação de Segurança Adicional.
- Remova a conta existente do seu aplicativo móvel.
- Clique em Configurar e siga as instruções para reconfigurar o aplicativo móvel.




**P: o que devo fazer quando vejo o erro 0x800434D4L ao tentar me conectar usando um aplicativo que não usa navegador?**

Atualmente, a verificação de segurança adicional pode ser usada somente com aplicativos/serviços que você pode acessar pelo navegador. Os aplicativos que não usam navegador (também conhecidos como aplicativos cliente avançados) que são instalados no computador local, como o Windows PowerShell, não funcionarão com contas que exigem a verificação de segurança adicional. Nesse caso, o aplicativo pode gerar o erro 0x800434D4L.

Uma solução alternativa para isso é ter uma conta de usuário para as operações administrativas e outra para as operações não administrativas. Posteriormente, você pode vincular caixas de correio entre a conta administrativa e a conta não administrativa para que seja possível entrar no Outlook usando a conta não administrativa. Para obter mais detalhes sobre isso, veja [Fornecer a um administrador a capacidade de abrir e exibir o conteúdo da caixa de correio de um usuário] (http://help.outlook.com/141/gg709759(d=loband).aspx?sl=1)).

<!---HONumber=AcomDC_0413_2016-->