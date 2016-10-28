<properties
	pageTitle="Perguntas frequentes sobre o Azure Multi-Factor Authentication"
	description="Fornece uma lista de perguntas frequentes e respostas relacionadas ao Azure Multi-Factor Authentication. O Multi-Factor Authentication é um método de verificar a identidade de um usuário que exige mais do que um nome de usuário e uma senha. Ele fornece uma camada adicional de segurança para conexões e transações do usuário."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="kgremban"/>

# Perguntas frequentes sobre o Azure Multi-Factor Authentication


Estas perguntas frequentes respondem às perguntas comuns sobre o Azure Multi-Factor Authentication e como usar o serviço Multi-Factor Authentication, incluindo perguntas sobre o modelo de cobrança e usabilidade.

## Geral

**P: como posso obter ajuda com o Azure Multi-Factor Authentication?**

- [Pesquise a Base de Dados de Conhecimento de Suporte da Microsoft](https://www.microsoft.com/pt-BR/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport)

  Pesquise a Base de Dados de Conhecimento em busca de soluções para problemas técnicos comuns.

- [Fóruns do Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

  Pesquise e procure perguntas e respostas técnicas da comunidade ou faça sua própria pergunta nos [fóruns do Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

- [Redefinição de senha](mailto:phonefactorsupport@microsoft.com)

  Se você for um cliente herdado do PhoneFactor e tiver perguntas ou precisar de ajuda para redefinir uma senha, use o link [redefinição de senha](mailto:phonefactorsupport@microsoft.com) para abrir um caso de suporte.

- [Suporte ao Servidor Azure Multi-Factor Authentication (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947)

  Use este link para contatar um profissional do suporte da Microsoft. Ele fará algumas perguntas para ajudar você a determinar suas opções de suporte, que podem incluir email, envio online ou suporte por telefone.



**P: Como o Servidor Azure Multi-Factor Authentication lida com os dados do usuário?**

Quando você usa o Servidor Multi-Factor Authentication, os dados do usuário são armazenados apenas nos servidores locais. Nenhum dado de usuário persistente é armazenado na nuvem. Quando o usuário executa a autenticação de dois fatores, o Servidor Multi-Factor Authentication envia dados ao serviço de nuvem do Azure Multi-Factor Authentication para autenticação. Quando as solicitações de autenticação são enviadas ao serviço de nuvem, os dados dos campos na lista a seguir são enviados na solicitação e nos logs para que estejam disponíveis nos relatórios de uso e autenticação do cliente. Alguns dos campos são opcionais e você pode configurá-los no Servidor Multi-Factor Authentication. A comunicação do Servidor Multi-Factor Authentication com o serviço de nuvem do Multi-Factor Authentication usa o protocolo SSL (Secure Sockets Layer) ou TLS (Transport Layer Security) pela porta de saída 443. Os campos de dados incluídos nos logs de autenticação de dois fatores são os seguintes:

- **ID Exclusiva** (ou o nome de usuário, ou a ID do Servidor Multi-Factor Authentication local)
- **Nome e Sobrenome** (opcional)
- **Endereço de Email** (opcional)
- **Número de Telefone** (ao usar uma chamada de voz ou autenticação SMS)
- **Token de Dispositivo** (ao usar a autenticação de aplicativos móveis)
- **Modo de autenticação**
- **Resultado da autenticação**
- **Nome do Servidor Multi-Factor Authentication**
- **IP do Servidor Multi-Factor Authentication**
- **IP do Cliente** (se disponível)



Além desses campos, o resultado da autenticação (êxito ou negação) e o motivo de todas as negações também são armazenados com os dados de autenticação e são disponibilizados nos relatórios de uso e autenticação.




## Cobrança

**P: minha organização será cobrada por ligações ou mensagens de texto usadas para autenticar meus usuários?**

Todos os custos são distribuídos no custo por usuário ou por autenticação do serviço. Uma organização não é cobrada por ligações individuais realizadas ou mensagens de texto enviadas aos usuários por meio do Azure Multi-Factor Authentication. Os proprietários do telefone podem ser cobrados em relação ao roaming ou pelas respectivas operadoras para receber ligações ou mensagens de texto.

**P: Como uma organização é cobrada pelo Azure Multi-Factor Authentication?**

O Azure Multi-Factor Authentication está disponível como um serviço autônomo com opções de cobrança por usuário e por autenticação, ou em conjunto com o Azure Active Directory Premium, Enterprise Mobility Suite ou Enterprise Cloud Suite. O serviço independente está disponível com base no consumo e é cobrado mensalmente em um compromisso monetário do Azure da organização, ou como uma licença anual por usuário disponível por meio de um Microsoft Enterprise Agreement, do Programa Microsoft Open License, do programa Provedor de Soluções na Nuvem da Microsoft, ou diretamente.

>[AZURE.IMPORTANT]
As regiões da Austrália estão disponíveis a clientes com presença comercial na Austrália ou na Nova Zelândia.

Modelo de cobrança | Preço
------------- | ------------- |
Consumo por usuário (compromisso monetário do Azure)| US$ 1,40 por mês (autenticações ilimitadas)
Consumo por autenticação (compromisso monetário do Azure)|$1,40 por 10 autenticações
Licença anual por usuário (direto)|US$ 1,40 por mês (autenticações ilimitadas)
Licença anual por usuário (licenciamento por volume)|Contate o [representante do Enterprise Agreement](https://www.microsoft.com/pt-BR/licensing/licensing-programs/enterprise.aspx)

**P: O modelo de cobrança por usuário se baseia no número de usuários que estão configurados para usar o Multi-Factor Authentication ou no número de usuários que executam verificações?**

A cobrança é baseada no número de usuários configurados para usar o Multi-Factor Authentication.

**P: Como funciona a cobrança do Multi-Factor Authentication?**

Quando uma organização usa o modelo de uso e cobrança de consumo por usuário ou por autenticação, o administrador da organização escolhe o tipo de uso quando o administrador cria um provedor do Multi-Factor Authentication no portal clássico do Azure. Trata-se de um recurso que é cobrado na assinatura do Azure da organização, assim como máquinas virtuais, sites e assim por diante. Ao usar o modelo de licença, as licenças do Azure Multi-Factor Authentication são compradas e atribuídas aos usuários, assim como o Office 365 e outros produtos por assinatura.

**P: Existe uma versão gratuita do Azure Multi-Factor Authentication para administradores?**

Um subconjunto dos recursos do Azure Multi-Factor Authentication, chamado Multi-Factor Authentication para Administradores do Azure, será oferecido gratuitamente aos membros do grupo Administradores Globais do Azure quando um provedor do Multi-Factor Authentication baseado em consumo não tiver sido vinculado à instância correspondente do Azure Active Directory. Usar um provedor do Multi-Factor Authentication atualiza todos os administradores e usuários no diretório que estão configurados para usar o Multi-Factor Authentication na versão completa do Azure Multi-Factor Authentication.

**P: Existe uma versão gratuita do Azure Multi-Factor Authentication para usuários do Office 365?**

Um subconjunto dos recursos do Azure Multi-Factor Authentication, chamado Multi-Factor Authentication para Office 365, será oferecido gratuitamente aos usuários que receberam uma licença do Office 365, quando um provedor do Azure Multi-Factor Authentication baseado em consumo não tiver sido vinculado à instância correspondente do Azure Active Directory. Usar o provedor do Multi-Factor Authentication atualiza todos os administradores e usuários no diretório que estão configurados para usar o Multi-Factor Authentication na versão completa do Azure Multi-Factor Authentication.

**P: Minha organização pode alternar entre os modelos de cobrança de consumo por usuário e por autenticação a qualquer momento?**

Sua organização escolhe um modelo de cobrança quando ela cria um recurso. Você não pode alterar um modelo de cobrança depois que o recurso é provisionado. No entanto, é possível criar um novo recurso Multi-Factor Authentication para substituir o original. Observe que as opções de configuração e definições do usuário não podem ser transferidas para o novo recurso.

**P: Minha organização pode alternar entre a cobrança de consumo e o modelo de licença a qualquer momento?**

Sua organização pode adquirir o Azure Multi-Factor Authentication, o Azure Active Directory Premium, o Enterprise Mobility Suite e licenças do Enterprise Cloud Suite a qualquer momento. Quando as licenças são adicionadas a um diretório que já tem um provedor do Azure Multi-Factor Authentication por usuário, a cobrança com base no consumo é subtraída pelo número de licenças possuídas. Se todos os usuários configurados para usar o Multi-Factor Authentication tiverem recebido licenças, o administrador poderá excluir o provedor do Azure Multi-Factor Authentication. Uma organização não pode combinar cobrança de consumo por autenticação com um modelo de licença. Quando um provedor do Multi-Factor Authentication por autenticação é vinculado a um diretório, a organização é cobrada por todas as solicitações de verificação de Multi-Factor Authentication, independentemente das licenças possuídas.

**P: Minha organização precisa usar e sincronizar identidades para usar o Multi-Factor Authentication?**

Quando uma organização usa um modelo de cobrança baseado em consumo, o Azure Active Directory não é necessário. A vinculação de um Provedor de Autenticação Multifator a um diretório é opcional. Se sua organização não estiver vinculada a um diretório, ela poderá implantar o Servidor Multi-Factor Authentication ou o SDK do Azure Multi-Factor Authentication local. O Azure Active Directory é necessário para o modelo de licença porque as licenças são adicionadas ao diretório quando você compra e as atribui aos usuários no diretório.


## Facilidade de uso

**P: O que um usuário deverá fazer se ele não receber uma resposta por telefone ou se o telefone não estiver disponível para ele?**

Se o usuário configurou anteriormente um telefone de backup, ele deverá tentar novamente e selecionar esse telefone quando solicitado na página de entrada. Se o usuário não tiver outro método configurado, ele deverá contatar o administrador da organização e pedir a este que atualize o número atribuído ao telefone principal do usuário, seja celular ou comercial.


**P: O que o administrador deverá fazer se um usuário contatá-lo para tratar de uma conta que ele não consegue mais acessar?**

O administrador pode redefinir a conta do usuário pedindo que ele refaça o processo de registro. Saiba mais sobre [como gerenciar configurações de usuário e dispositivo com o Azure Multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: O que um administrador deverá fazer se o telefone de um usuário que está usando senhas de aplicativo for perdido ou roubado?**

O administrador pode excluir todas as senhas de aplicativo do usuário para impedir acesso não autorizado. Depois que o usuário tiver outro dispositivo, ele poderá recriar as senhas. Saiba mais sobre [como gerenciar configurações de usuário e dispositivo com o Azure Multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: E se o usuário não conseguir entrar em aplicativos que não são acessados por navegador?**

- Um usuário que está configurado para usar o Multi-Factor Authentication requer uma senha de aplicativo para entrar em alguns aplicativos sem acesso por navegador.
- Um usuário precisar limpar (excluir) as informações de conexão, reiniciar o aplicativo e entrar usando seu nome de usuário e a senha do aplicativo.

Obtenha mais informações sobre como criar senhas de aplicativo e outra [ajuda com senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md).


>[AZURE.NOTE] Autenticação moderna para clientes do Office 2013
>
> Os clientes do Office 2013 (incluindo Outlook) são compatíveis com novos protocolos de autenticação. Você pode configurar o Office 2013 para permitir o Multi-Factor Authentication. Depois de configurar o Office 2013, as senhas de aplicativo não são necessárias para clientes do Office 2013. Para saber mais, confira [Office 2013 modern authentication public preview announcement (Anúncio da visualização pública da autenticação moderna do Office 2013)](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/) .

**P: O que um usuário deverá fazer se não receber uma mensagem de texto ou se responder a uma mensagem de texto bidirecional, mas o tempo de verificação se esgotar?**

O serviço Azure Multi-Factor Authentication envia mensagens de texto usando agregadores de SMS. Muitos fatores podem afetar a confiabilidade da entrega e do recebimento da mensagem de texto, incluindo o agregador que é usado, o país de destino, a operadora do celular do usuário e a intensidade do sinal. Por esse motivo, a entrega das mensagens de texto e o recebimento de respostas de SMS no SMS bidirecional não são garantidos. É recomendável usar o SMS unidirecional em vez de SMS bidirecional quando possível. O SMS unidirecional é mais confiável e impede os usuários de incorrerem em cobranças globais de SMS ao responderem a uma mensagem de texto que foi enviada de outro país.

As verificações de mensagem de texto também são mais confiáveis em alguns países ou regiões, como os Estados Unidos e o Canadá. Incentivamos os usuários que enfrentam dificuldade em receber mensagens de texto de maneira confiável ao usar o Azure Multi-Factor Authentication a selecionar o método de ligação ou aplicativo móvel. O método de autenticação do aplicativo móvel é excelente, pois o usuário pode receber notificações de aplicativo móvel por conexões celulares e Wi-Fi. Além disso, a senha de aplicativo móvel é exibida mesmo quando o dispositivo não tem qualquer sinal. O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

**P: Posso usar tokens de hardware com o Servidor Multi-Factor Authentication?**

Se estiver usando o Servidor Multi-Factor Authentication, você poderá importar tokens de terceiros com TOTP (senha de uso único) baseados no tempo OATH (Autenticação Aberta) e usá-los com o Multi-Factor Authentication. Damos suporte à importação de tokens TOTP OATH em um formato PSKC (Contêiner de Chaves Simétricas Portátil) mais antigo que a Gemalto pode produzir para seus tokens, assim como damos suporte à importação de tokens no formato CSV. Se você importar tokens no formato CSV, o arquivo CSV deverá conter um número de série, uma chave secreta no formato Base32 e um intervalo de tempo (geralmente 30 segundos).

Será possível usar tokens ActiveIdentity que sejam TOTP OATH se você colocar o arquivo de chave secreta em um arquivos CSV que possa ser importado para o Servidor Multi-Factor Authentication. É possível usar tokens OATH com o ADFS (Serviços de Federação do Active Directory); o RADIUS (Remote Authentication Dial-In User Service), quando o sistema cliente puder processar respostas de desafio de acesso; e com a autenticação baseada em formulários IIS (Servidor de Informações da Internet).

**P: Posso usar o Servidor Azure Multi-Factor Authentication para proteger os Serviços de Terminal?**

Sim, mas se estiver usando o Windows Server 2012 R2 ou uma versão posterior, você poderá fazer isso usando somente o RD Gateway (Gateway de Área de Trabalho Remota).

As alterações de segurança no Windows Server 2012 R2 mudaram a forma como o Servidor Azure Multi-Factor Authentication se conecta ao pacote de segurança LSA (Autoridade de Segurança Local) no Windows Server 2012 e versões anteriores. Para versões dos Serviços de Terminal no Windows Server 2012 ou anteriores, você pode simplesmente [proteger um aplicativo com a Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se estiver usando o Windows Server 2012 R2, você precisará do RD Gateway.

**P: Por que um usuário receberia uma ligação anônima do Multi-Factor Authentication depois de configurar uma ID de chamada?**

Quando as chamadas do Multi-Factor Authentication são feitas por meio de rede telefônica pública, às vezes, elas são roteadas por uma operadora que não é compatível com a ID de chamadas. Sendo assim, a ID de chamadas não é garantida, mesmo que o sistema do Multi-Factor Authentication sempre a envie.


## Erros

**P: O que um usuário deverá fazer caso visualize a mensagem de erro "A solicitação de autenticação não é para uma conta ativada" quando ele autenticar usando notificações de aplicativo móvel?**

Siga este procedimento:

1. Acesse o [seu perfil no portal do Azure](https://account.activedirectory.windowsazure.com/profile/) e entre com sua conta organizacional.
2. Se necessário, clique em **Outras opções de verificação** e clique em outra opção para usar na verificação de conta.
3. Clique em **Verificação de Segurança Adicional**.
4. Remova a conta existente do aplicativo móvel.
5. Clique em **Configurar** e siga as instruções para reconfigurar o aplicativo móvel.




**P: O que um usuário deverá fazer caso veja uma mensagem de erro 0x800434D4L quando tentar entrar usando um aplicativo que não pode ser acessado por navegador?**

Atualmente, um usuário pode usar a verificação de segurança adicional apenas com aplicativos e serviços que ele pode acessar por meio do navegador. Os aplicativos que não usam navegador (também conhecidos como *aplicativos cliente avançados*) instalados em um computador local, como o Windows PowerShell, não funcionarão com contas que exigem a verificação de segurança adicional. Nesse caso, é possível que o usuário veja o aplicativo gerar um erro 0x800434D4L.

Uma solução alternativa para isso é ter contas de usuário separadas para operações administrativas e não administrativas. Posteriormente, você pode vincular caixas de correio entre a conta administrativa e a conta não administrativa para que seja possível entrar no Outlook usando a conta não administrativa. Para obter mais detalhes sobre isso, saiba como [fornecer a um administrador a capacidade de abrir e exibir o conteúdo da caixa de correio de um usuário](http://help.outlook.com/141/gg709759.aspx?sl=1).

<!---HONumber=AcomDC_0921_2016-->