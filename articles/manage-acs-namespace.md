<properties linkid="manage-services-manage-acs" urlDisplayName="Gerenciar o ACS" pageTitle="Access Control Service - Gerenciamento de serviços do Azure" metaKeywords="" description="Saiba como gerenciar o ACS (Serviço de Controle de Acesso) do Azure usando certificados e chaves." metaCanonical="" services="active-directory" documentationCenter="" title="Gerenciando seu namespace ACS" authors=""  solutions="" writer="" manager="" editor=""  />





# Gerenciando seu namespace de ACS (a página pode estar em inglês) #

Este tópico descreve as tarefas de gerenciamento que são recomendas executar regularmente para que seus aplicativos que usam o ACS (Access Control Service) do Azure continuem a funcionar corretamente e sem interrupções. Essas tarefas de gerenciamento são as seguintes:


1. Importante: controle a expiração e execute a sobreposição de certificados, chaves e senhas usadas pelo namespace ACS, aplicativos de terceiros confiáveis, identidades de serviço, provedores de identidade e a conta do Serviço de Gerenciamento do ACS. Consulte Diretrizes de gerenciamento de certificados e chaves a seguir para obter mais informações.

2. Revise seus provedores de identidade, identidades de serviço, regras e administradores de portal e remova os desatualizados. 

Para obter mais informações sobre o ACS, consulte [Access Control Service 2.0](http://msdn.microsoft.com/pt-br/library/gg429786.aspx).



## Diretrizes de gerenciamento de certificados e chaves ##

Por motivos de segurança, certificados e chaves que são usadas no ACS expiram. É importante acompanhar as datas de expiração para que esses certificados e chaves possam ser renovados.

As etapas de alto nível para sobrepor um certificado de autenticação de token (chave simétrica ou certificado X.509) ou de descriptografia de token são:

1.	Configure o novo certificado ou chave no ACS como uma chave "secundária", juntamente com o certificado ou a chave existente que irá expirar.
2.	Notifique os parceiros que usam o serviço de que precisam atualizar suas chaves correspondentes antes de um determinado prazo.
3.	Os parceiros devem atualizar o certificado ou a chave correspondente para suas terceiras partes confiáveis ou provedores de identidade. Por exemplo, importe os metadados do WS-Federation atualizados para o namespace ACS que contém o novo certificado de validação de assinatura de token ou configure manualmente a chave simétrica na configuração do aplicativo.
4.	Depois que todos os aplicativos tiverem sido atualizados (ou depois que a data limite tiver decorrido), marque o novo certificado ou chave como primário na configuração do ACS. 
5.	Depois de um período razoável, remova o antigo certificado ou chave da configuração do ACS.

As etapas de alto nível para sobreposição de certificados de criptografia de token são:

1.	Você (ou seus parceiros) atualiza o certificado ou a chave correspondente que é usada para descriptografia do token em aplicativos de terceiras partes confiáveis.
2.	Configure o novo certificado de criptografia no ACS, juntamente com o certificado existente que irá expirar.
3.	Remova o antigo certificado de criptografia.

As etapas de alto nível para sobreposição da identidade do serviço ou gerenciamento das chaves de serviço:

1.	Configure o novo certificado ou chave no ACS, juntamente com o certificado ou a chave existente que irá expirar.
2.	Você (ou seus parceiros) atualiza o certificado ou a chave correspondente que é usada para solicitações de token em aplicativos cliente.
3.	Depois que todos os clientes forem atualizados (ou depois de um período razoável), remova o certificado ou chave antiga.

 

Quando um certificado ou uma chave expira, o ACS não pode emitir tokens impedindo que sua terceira parte confiável opere normalmente. As chaves e certificados expirados serão ignorados pelo ACS, efetivamente provocando exceções, como se nenhum certificado ou chave estivesse configurado em primeiro lugar. Nas seções a seguir, você encontrará informações para cada certificado e chave gerenciado pelo ACS, como renová-lo e como reconhecer se expirou e precisa ser renovado. 

- Use a seção Certificados e chaves no Portal de Gerenciamento do ACS para gerenciar certificados e chaves relacionados ao namespace do serviço e aplicativos de terceiras partes confiáveis. Para obter mais informações sobre esses tipos de credenciais, consulte [Certificados e chaves](http://msdn.microsoft.com/pt-br/library/gg185932.aspx)
- Use a seção de identidades de serviço no Portal de Gerenciamento do ACS para gerenciar credenciais (certificados, chaves ou senhas) relacionadas a identidades de serviço. Para obter mais informações sobre identidades de serviço, consulte [Identidades de serviço](http://msdn.microsoft.com/pt-br/library/gg185945.aspx).
- Use a seção Serviço de gerenciamento no Portal de Gerenciamento do ACS para gerenciar credenciais (certificados, chaves ou senhas) relacionadas a contas do Serviço de Gerenciamento do ACS. Para obter mais informações sobre o Serviço de Gerenciamento ACS, consulte [Serviço de Gerenciamento ACS](http://msdn.microsoft.com/pt-br/library/gg185972.aspx).

Existem alguns tipos de certificado e de chave que não estão visíveis no Portal de Gerenciamento do ACS. Especificamente para provedores de identidade do WS-Federation, como o AD FS, você deve verificar proativamente a validade dos certificados que usam os provedores de identidade. Atualmente, os certificados disponíveis por meio de metadados de provedores de identidade do WS-Federation não são visíveis no Portal de Gerenciamento do ACS. Para verificar a validade dos certificados, você deve usar o serviço de gerenciamento para inspecionar as datas de efetivação e de vencimento das propriedades StartDate e EndDate de [IdentityProviderKey](http://msdn.microsoft.com/pt-br/library/hh124084.aspx). Quando o certificado ou uma chave expira e, portanto, torna-se inválido, o ACS começará a gerar exceções [Códigos de erro do ACS](http://msdn.microsoft.com/pt-br/library/gg185949.aspx) específicas ao certificado ou à chave. Consulte as seções a seguir para obter códigos de erro específicos.

Você pode atualizar os certificados e as chaves de forma programática usando o [Serviço de gerenciamento ACS](http://msdn.microsoft.com/pt-br/library/gg185972.aspx). Examine o exemplo de código KeyManagement disponível para download como parte do [Amostra do código: Serviço de gerenciamento](http://msdn.microsoft.com/pt-br/library/gg185970.aspx).

## Certificados e chaves disponíveis ##

A lista a seguir mostra os certificados e chaves usados no ACS e cujas datas de expiração devem ser acompanhadas:

- Certificados de autenticação de tokens
- Chaves de autenticação de tokens
- Certificados de criptografia de tokens
- Certificados de descriptografia de tokens
- Credenciais de identidade de serviço
- Credenciais de conta do Serviço de gerenciamento ACS.
- Autenticação de provedor de identidade e certificados de criptografia.

O restante deste tópico aborda cada certificado e chave em detalhes.

## Certificados de autenticação de tokens ##

O ACS assina todos os tokens de segurança que emite. Os certificados X.509 são usados para autenticação quando você cria um aplicativo que consome tokens SAML emitidos pelo ACS. 

Você pode gerenciar certificados de autenticação de tokens por meio da seção Certificados e Chave do Portal de Gerenciamento do ACS. 

**Para gerenciar certificados de autenticação de tokens**

1. Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([http://go.microsoft.com/fwlink/?LinkID=129428](http://go.microsoft.com/fwlink/?LinkID=129428)).

2. Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3. Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

	![][ACS1]

4. Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página. 

	![][ACS2]

	Neste ponto, sua tela deve ser semelhante a esta:

	![][ACS3]

5. Clique em **Certificados e Chaves** na árvore à esquerda sob a seção Configurações do Serviço.

    ![][ACS4]
   
    Neste ponto, sua tela deve ser semelhante a esta:

    ![][ACS5]

6. Na seção Autenticação de Tokens, use o botão Adicionar para configurar o novo certificado no ACS como uma chave "secundária", juntamente com o certificado existente que irá expirar.

7. Notifique os parceiros que usam o serviço de que precisam atualizar suas chaves correspondentes antes de um determinado prazo.

8. Os parceiros devem atualizar o certificado correspondente para suas terceiras partes confiáveis ou provedores de identidade. Por exemplo, importe os metadados do WS-Federation atualizados para o namespace ACS que contém o novo certificado de validação de assinatura de token ou configure manualmente a chave simétrica na configuração do aplicativo.

9. Depois que todos os aplicativos tiverem sido atualizados (ou depois que a data limite tiver decorrido), marque o novo certificado como primário na configuração do ACS. 

10. Depois de um período razoável, use o botão Excluir sob a seção Autenticação de Tokens da página Certificados e Chaves para remover o certificado antigo da configuração do ACS.


Para obter mais informações, consulte [Certificados e chaves](http://msdn.microsoft.com/pt-br/library/gg185932.aspx).

Quando os certificados de autenticação expirarem, você receberá os seguintes erros ao tentar solicitar um token:

<table><tr><td><b>Código do Erro</b></td>
<td><b>Mensagem</b></td>
<td><b>Ação necessária para corrigir a mensagem</b></td>
</tr>
<tr>
<td>ACS50004</td>
<td>Nenhum certificado de autenticação X.509 primário está configurado. Um certificado de autenticação é necessário para o SAML.</td>
<td>Se a terceira parte confiável escolhida usar SAML como seu tipo de token, verifique se um certificado X.509 válido está configurado para a terceira parte confiável ou o namespace do serviço. O certificado deve estar definido como primário e estar dentro do período de validade.</td></tr>
</table> 

## Chave de autenticação de tokens ##

O ACS assina todos os tokens de segurança que emite. Chaves de autenticação simétricas de 256 bits são usadas quando você cria um aplicativo que consome tokens SWT emitidos pelo ACS. 

Você pode gerenciar chaves de autenticação de tokens por meio da seção Certificados e Chave do Portal de Gerenciamento do ACS. 

**Para gerenciar chaves de autenticação de tokens**

1. Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([http://go.microsoft.com/fwlink/?LinkID=129428](http://go.microsoft.com/fwlink/?LinkID=129428)).

2. Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3. Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

	![][ACS1]

4. Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página. 

	![][ACS2]

	Neste ponto, sua tela deve ser semelhante a esta:

	![][ACS3]

5. Clique em **Certificados e Chaves** na árvore à esquerda sob a seção Configurações do Serviço.

    ![][ACS4]
    
    Neste ponto, sua tela deve ser semelhante a esta:

    ![][ACS5]

6. Na seção Autenticação de Tokens, use o botão Adicionar para configurar a nova chave no ACS como uma chave "secundária", juntamente com a chave existente que irá expirar.

7. Notifique os parceiros que usam o serviço de que precisam atualizar suas chaves correspondentes antes de um determinado prazo.

8. Os parceiros devem atualizar a chave correspondente para suas terceiras partes confiáveis ou provedores de identidade. Por exemplo, importe os metadados do WS-Federation atualizados para o namespace ACS que contém o novo certificado de validação de assinatura de token ou configure manualmente a chave simétrica na configuração do aplicativo.

9. Depois que todos os aplicativos tiverem sido atualizados (ou depois que a data limite tiver decorrido), marque a nova chave como primária na configuração do ACS. 

10. Depois de um período razoável, use o botão Excluir sob a seção Autenticação de Tokens da página Certificados e Chaves para remover a chave antiga da configuração do ACS.

Para obter mais informações, consulte [Certificados e chaves](http://msdn.microsoft.com/pt-br/library/gg185932.aspx).

Quando as chaves de autenticação expirarem, você receberá os seguintes erros ao tentar solicitar um token:

<table><tr><td><b>Código do Erro</b></td>
<td><b>Mensagem</b></td>
<td><b>Ação necessária para corrigir a mensagem</b></td>
</tr>
<tr>
<td>ACS50003</td>
<td>Nenhuma chave de autenticação simétrica primária está configurada. Uma chave de autenticação simétrica é necessária para o SWT.</td>
<td>Se a terceira parte confiável escolhida usar SWT como seu tipo de token, verifique se a chave simétrica está configurada para a terceira parte confiável ou o namespace de serviço e se a chave está definida como primária e dentro do período de validade.</td></tr>
</table> 

## Certificados de criptografia de tokens ##

A criptografia do tokens será necessária se um aplicativo de uma terceira parte confiável for um serviço web que usa tokens de prova de posse através do protocolo WS-Trust. Em outros casos, a criptografia do token é opcional.  

Você pode gerenciar certificados de criptografia de tokens por meio da seção Certificados e Chave do Portal de Gerenciamento do ACS. 

**Para gerenciar certificados de criptografia de tokens**

1. Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([http://go.microsoft.com/fwlink/?LinkID=129428](http://go.microsoft.com/fwlink/?LinkID=129428)).

2. Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3. Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

	![][ACS1]

4. Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página. 

	![][ACS2]

	Neste ponto, sua tela deve ser semelhante a esta:

	![][ACS3]

5. Clique em **Certificados e Chaves** na árvore à esquerda sob a seção Configurações do Serviço.

    ![][ACS4]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][ACS7]

6. Você (ou seus parceiros) atualiza o certificado ou a chave correspondente que é usada para descriptografia do token em aplicativos de terceiras partes confiáveis.
7. Use o botão Adicionar para configurar o novo certificado de criptografia no ACS, juntamente com o certificado existente que irá expirar.
8. Use Excluir para remover o antigo certificado de criptografia.



Para obter mais informações, consulte [Certificados e chaves](http://msdn.microsoft.com/pt-br/library/gg185932.aspx).

Quando os certificados de criptografia expirarem, você receberá os seguintes erros ao tentar solicitar um token:

<table><tr><td><b>Código do Erro</b></td>
<td><b>Mensagem</b></td>
<td><b>Ação necessária para corrigir a mensagem</b></td>
</tr>
<tr>
<td>ACS50005</td>
<td>A criptografia do token é necessária, mas nenhum certificado de criptografia está configurado para a terceira parte confiável.</td>
<td>Desabilite a criptografia de token para a terceira parte confiável escolhida ou carregue um certificado X.509 a ser usado para criptografia do token.</td></tr>
</table> 

## Certificados de descriptografia de tokens ##

O ACS pode aceitar tokens criptografados de provedores de identidade do WS-Federation (por exemplo, AD FS 2.0). Um certificado X.509 hospedado no ACS é usado na descriptografia. 

Você pode gerenciar certificados de descriptografia de tokens por meio da seção Certificados e Chave do Portal de Gerenciamento do ACS. 

**Para gerenciar certificados de descriptografia de tokens**

1. Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([http://go.microsoft.com/fwlink/?LinkID=129428](http://go.microsoft.com/fwlink/?LinkID=129428)).

2. Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3. Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

	![][ACS1]

4. Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página. 

	![][ACS2]

	Neste ponto, sua tela deve ser semelhante a esta:

	![][ACS3]

5. Clique em **Certificados e Chaves** na árvore à esquerda sob a seção Configurações do Serviço.

    ![][ACS4]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][ACS9]

6. Na seção Descriptografia de tokens, use o botão Adicionar para configurar o novo certificado no ACS como uma chave "secundária", juntamente com o certificado existente que irá expirar.

7. Notifique os parceiros que usam o serviço de que precisam atualizar suas chaves correspondentes antes de um determinado prazo.

8. Os parceiros devem atualizar o certificado correspondente para suas terceiras partes confiáveis ou provedores de identidade. Por exemplo, importe os metadados do WS-Federation atualizados para o namespace ACS que contém o novo certificado de validação de assinatura de token ou configure manualmente a chave simétrica na configuração do aplicativo.

9. Depois que todos os aplicativos tiverem sido atualizados (ou depois que a data limite tiver decorrido), marque o novo certificado como primário na configuração do ACS. 

10. Depois de um período razoável, use o botão Excluir sob a seção Autenticação de Tokens da página Certificados e Chaves para remover o certificado antigo da configuração do ACS.

Para obter mais informações, consulte [Certificados e chaves](http://msdn.microsoft.com/pt-br/library/gg185932.aspx).

Quando os certificados de descriptografia expirarem, você receberá os seguintes erros ao tentar solicitar um token:

<table><tr><td><b>Código do Erro</b></td>
<td><b>Mensagem</b></td>
</tr>
<tr>
<td>ACS10001</td>
<td>Ocorreu um erro ao processar o cabeçalho SOAP.</td>
</tr>
<tr><td>ACS20001</td>
<td>Ocorreu um erro ao processar uma resposta de autenticação do WS-Federation.</td></tr>
</table> 

## Credenciais de identidade de serviço ##

As identidades de serviço são credenciais que são configuradas globalmente para o namespace de ACS que permite que aplicativos ou clientes se autentiquem diretamente com o ACS e recebam um token. Existem três tipos de credenciais às quais uma identidade de serviço ACS pode ser associada: chave simétrica, senha e certificado X.509. 

Você pode gerenciar as credenciais de identidade de serviço por meio da página Identidades de serviço do Portal de Gerenciamento do ACS. 

**Para gerenciar credenciais de identidade de serviço**

1. Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([http://go.microsoft.com/fwlink/?LinkID=129428](http://go.microsoft.com/fwlink/?LinkID=129428)).

2. Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3. Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

	![][ACS1]

4. Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página. 

	![][ACS2]

	Neste ponto, sua tela deve ser semelhante a esta:

	![][ACS3]

5.  Clique em **Identidades de serviço** na árvore à esquerda sob a seção Configurações do Serviço.

    ![][ACS11]

6. Clique na identidade de serviço que você deseja editar.

    ![][ACS112]

7.	Na seção Credenciais, use o botão Adicionar para configurar o novo certificado ou chave no ACS, juntamente com o certificado ou chave existente que irá expirar.

8.	Você (ou seus parceiros) atualiza o certificado ou a chave correspondente que é usada para solicitações de token em aplicativos cliente.

9.	Depois que todos os clientes forem atualizados (ou depois de um período razoável), use o botão Excluir para remover o certificado ou chave antigo.


Para obter mais informações, consulte [Identidades do serviço](http://msdn.microsoft.com/pt-br/library/gg185945.aspx).

A seguir está a exceção que o ACS emitirá se as credenciais estiverem expiradas:

<table><tr><td><b>Credencial ></b></td><td><b>Código de erro</b></td>
<td><b>Mensagem</b></td><td><b>Ação necessária para corrigir a mensagem</b></td>
</tr>
<tr>
<td>Chave simétrica, senha</td>
<td>ACS50006</td>
<td>Falha na verificação da assinatura. (Pode haver mais detalhes na mensagem.)</td>
<td></td>
</tr>
<tr><td>Certificado X.509</td>
<td>ACS50016</td>
<td>X509Certificate com assunto '&lt;Nome do assunto do certificado&gt;' e impressão digital '&lt;Impressão digital do certificado&gt;' não correspondem a nenhum certificado configurado.</td>
<td>Verifique se o certificado solicitado foi carregado no ACS.</td>
</tr>
</table> 

Para verificar e atualizar as datas de validade das chaves simétricas ou senhas ou para carregar novo certificado como credenciais de identidade do serviço, siga as instruções descritas em [Como: Adicionar identidade de serviço com um certificado X.509, senha ou chave simétrica](http://msdn.microsoft.com/pt-br/library/gg185924.aspx). Lista de credenciais de identidade de serviço disponíveis na página Editar Identidade de Serviço.

## Credenciais do serviço de gerenciamento ##

O Serviço de Gerenciamento do ACS é um componente-chave do ACS que permite gerenciar e definir configurações de forma programática em um namespace ACS. Existem três tipos de credenciais aos quais a conta do Serviço de Gerenciamento do ACS pode estar associada. Os tipos são chave simétrica, senha e certificado X.509. 

Você pode gerenciar as credenciais do serviço de gerenciamento por meio da página Serviço de gerenciamento do Portal de Gerenciamento do ACS. 

**Para gerenciar as credenciais do Serviço de Gerenciamento do ACS.**

1. Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([http://go.microsoft.com/fwlink/?LinkID=129428](http://go.microsoft.com/fwlink/?LinkID=129428)).

2. Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3. Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

	![][ACS1]

4. Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página. 

	![][ACS2]

	Neste ponto, sua tela deve ser semelhante a esta:

	![][ACS3]

5. Clique em **Serviço de gerenciamento** na árvore à esquerda sob a seção Administração.

    ![][ACS14]
        
6. Clique na conta do serviço de gerenciamento.

    ![][ACS15]

7. Na seção Credenciais, use o botão Adicionar para configurar o novo certificado ou chave no ACS, juntamente com o certificado ou chave existente que irá expirar.

8.	Você (ou seus parceiros) atualiza o certificado ou a chave correspondente que é usada para solicitações de token em aplicativos cliente.

9.	Depois que todos os clientes forem atualizados (ou depois de um período razoável), use o botão Excluir para remover o certificado ou chave antigo.


Para obter mais informações, consulte [Serviço de Gerenciamento do ACS](http://msdn.microsoft.com/pt-br/library/gg185972.aspx).

O ACS emitirá as seguintes exceções se estas credenciais estiverem expiradas:

<table><tr><td><b>Credencial ></b></td><td><b>Código de erro</b></td>
<td><b>Mensagem</b></td><td><b>Ação necessária para corrigir a mensagem</b></td>
</tr>
<tr>
<td>Chave simétrica, senha</td>
<td>ACS50006</td>
<td>Falha na verificação da assinatura. (Pode haver mais detalhes na mensagem.)</td>
<td></td>
</tr>
<tr><td>Certificado X.509</td>
<td>ACS50016</td>
<td>X509Certificate com assunto '&lt;Nome do assunto do certificado&gt;' e impressão digital '&lt;Impressão digital do certificado&gt;' não correspondem a nenhum certificado configurado.</td>
<td>Verifique se o certificado solicitado foi carregado no ACS.</td>
</tr>
</table> 

A lista das credenciais de contas do Serviço de Gerenciamento do ACS está disponível na página Editar Conta do Serviço de Gerenciamento no Portal de Gerenciamento do ACS.

## Certificado de provedor de identidade do WS-Federation ##

O Certificado do provedor de identidade do WS-Federation está disponível por meio de seus metadados. Ao configurar o provedor de identidade do WS-Federation, como o AD FS, o certificado de autenticação do WS Federation é configurado por meio dos metadados do WS-Federation disponíveis por meio da URL ou como um arquivo, leia [Provedores de identidade WS-Federation](http://msdn.microsoft.com/pt-br/library/gg185933.aspx) e [Como: Configurar o AD FS 2.0 como um provedor de identidade](http://msdn.microsoft.com/pt-br/library/gg185961.aspx) para obter mais informações. Depois de configurar o provedor de identidade do WS-Federation no ACS, use o serviço de gerenciamento do ACS para consultar a validade de seus certificados. Observe que para cada carregamento consecutivo de metadados por meio do Portal de Gerenciamento do ACS ou do Serviço de Gerenciamento do ACS, as chaves serão substituídas. 

A seguir estão as exceções que o ACS emitirá se o certificado estiver expirado:

<table><tr><td><b>Código do Erro</b></td>
<td><b>Mensagem</b></td>
</tr>
<tr>
<td>ACS10001</td>
<td>Ocorreu um erro ao processar o cabeçalho SOAP.</td>
</tr>
<tr><td>ACS20001</td>
<td>Ocorreu um erro ao processar uma resposta de autenticação do WS-Federation.</td></tr>
<tr><td>ACS50006</td><td>Falha na verificação da assinatura. (Pode haver mais detalhes na mensagem.)</td></tr>
</table> 

[ACS1]:./media/manage-acs-namespace/ACS1.png
[ACS2]:./media/manage-acs-namespace/ACS2.png
[ACS3]:./media/manage-acs-namespace/ACS3.png
[ACS4]:./media/manage-acs-namespace/ACS4.png
[ACS5]:./media/manage-acs-namespace/ACS5.png
[ACS7]:./media/manage-acs-namespace/ACS7.png
[ACS9]:./media/manage-acs-namespace/ACS9.png
[ACS11]:./media/manage-acs-namespace/ACS11.png
[ACS112]:./media/manage-acs-namespace/ACS112.png
[ACS14]:./media/manage-acs-namespace/ACS14.png
[ACS15]:./media/manage-acs-namespace/ACS15.png

