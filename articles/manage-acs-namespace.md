<properties linkid="manage-services-manage-acs" urlDisplayName="Manage ACS" pageTitle="Access Control Service - Azure service management" metaKeywords="" description="Learn how to manage your Azure Access Control Service (ACS) using certificates and keys." metaCanonical="" services="active-directory" documentationCenter="" title="Managing Your ACS Namespace" authors="mbaldwin" solutions="" manager="mbaldwin" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mbaldwin" />

# Gerenciando seu namespace de ACS (a página pode estar em inglês)

Este tópico descreve as tarefas de gerenciamento que são recomendas executar regularmente para que seus aplicativos que usam o ACS (Access Control Service) do Azure continuem a funcionar corretamente e sem interrupções. Essas tarefas de gerenciamento são as seguintes:

1.  Importante: controle a expiração e execute a sobreposição de certificados, chaves e senhas usadas pelo namespace ACS, aplicativos de terceiros confiáveis, identidades de serviço, provedores de identidade e a conta do Serviço de Gerenciamento do ACS. Consulte Diretrizes de gerenciamento de certificados e chaves a seguir para obter mais informações.

2.  Revise seus provedores de identidade, identidades de serviço, regras e administradores de portal e remova os desatualizados.

Para obter mais informações sobre o ACS, consulte [Access Control Service 2.0][Access Control Service 2.0].

## Diretrizes de gerenciamento de certificados e chaves

Por motivos de segurança, certificados e chaves que são usadas no ACS expiram. É importante acompanhar as datas de expiração para que esses certificados e chaves possam ser renovados.

As etapas de alto nível para sobrepor um certificado de autenticação de token (chave simétrica ou certificado X.509) ou de descriptografia de token são:

1.  Configure o novo certificado ou chave no ACS como uma chave "secundária", juntamente com o certificado ou a chave existente que irá expirar.
2.  Notifique os parceiros que usam o serviço de que precisam atualizar suas chaves correspondentes antes de um determinado prazo.
3.  Os parceiros devem atualizar o certificado ou a chave correspondente para suas terceiras partes confiáveis ou provedores de identidade. Por exemplo, importe os metadados do WS-Federation atualizados para o namespace ACS que contém o novo certificado de validação de assinatura de token ou configure manualmente a chave simétrica na configuração do aplicativo.
4.  Depois que todos os aplicativos tiverem sido atualizados (ou depois que a data limite tiver decorrido), marque o novo certificado ou chave como primário na configuração do ACS.
5.  Depois de um período razoável, remova o antigo certificado ou chave da configuração do ACS.

As etapas de alto nível para sobreposição de certificados de criptografia de token são:

1.  Você (ou seus parceiros) atualiza o certificado ou a chave correspondente que é usada para descriptografia do token em aplicativos de terceiras partes confiáveis.
2.  Configure o novo certificado de criptografia no ACS, juntamente com o certificado existente que irá expirar.
3.  Remova o antigo certificado de criptografia.

As etapas de alto nível para sobreposição da identidade do serviço ou gerenciamento das chaves de serviço:

1.  Configure o novo certificado ou chave no ACS, juntamente com o certificado ou a chave existente que irá expirar.
2.  Você (ou seus parceiros) atualiza o certificado ou a chave correspondente que é usada para solicitações de token em aplicativos cliente.
3.  Depois que todos os clientes forem atualizados (ou depois de um período razoável), remova o certificado ou chave antiga.

Quando um certificado ou uma chave expira, o ACS não pode emitir tokens impedindo que sua terceira parte confiável opere normalmente. As chaves e certificados expirados serão ignorados pelo ACS, efetivamente provocando exceções, como se nenhum certificado ou chave estivesse configurado em primeiro lugar. Nas seções a seguir, você encontrará informações para cada certificado e chave gerenciado pelo ACS, como renová-lo e como reconhecer se expirou e precisa ser renovado.

-   Use a seção Certificados e chaves no Portal de Gerenciamento do ACS para gerenciar certificados e chaves relacionados ao namespace do serviço e aplicativos de terceiras partes confiáveis. Para obter mais informações sobre esses tipos de credenciais, consulte [Certificados e chaves][Certificados e chaves]
-   Use a seção de identidades de serviço no Portal de Gerenciamento do ACS para gerenciar credenciais (certificados, chaves ou senhas) relacionadas a identidades de serviço. Para obter mais informações sobre identidades de serviço, consulte [Identidades de serviço][Identidades de serviço].
-   Use a seção Serviço de gerenciamento no Portal de Gerenciamento do ACS para gerenciar credenciais (certificados, chaves ou senhas) relacionadas a contas do Serviço de Gerenciamento do ACS. Para obter mais informações sobre o Serviço de Gerenciamento ACS, consulte [Serviço de Gerenciamento ACS][Serviço de Gerenciamento ACS].

Existem alguns tipos de certificado e de chave que não estão visíveis no Portal de Gerenciamento do ACS. Especificamente para provedores de identidade do WS-Federation, como o AD FS, você deve verificar proativamente a validade dos certificados que usam os provedores de identidade. Atualmente, os certificados disponíveis por meio de metadados de provedores de identidade do WS-Federation não são visíveis no Portal de Gerenciamento do ACS. Para verificar a validade dos certificados, você deve usar o serviço de gerenciamento para inspecionar as datas de efetivação e de vencimento das propriedades StartDate e EndDate de [IdentityProviderKey][IdentityProviderKey]. Quando o certificado ou uma chave expira e, portanto, torna-se inválido, o ACS começará a gerar exceções [Códigos de erro do ACS][Códigos de erro do ACS] específicas ao certificado ou à chave. Consulte as seções a seguir para obter códigos de erro específicos.

Você pode atualizar os certificados e as chaves de forma programática usando o [Serviço de gerenciamento ACS][Serviço de Gerenciamento ACS]. Examine o exemplo de código KeyManagement disponível para download como parte do [Amostra do código: Serviço de gerenciamento][Amostra do código: Serviço de gerenciamento].

## Certificados e chaves disponíveis

A lista a seguir mostra os certificados e chaves usados no ACS e cujas datas de expiração devem ser acompanhadas:

-   Certificados de autenticação de tokens
-   Chaves de autenticação de tokens
-   Certificados de criptografia de tokens
-   Certificados de descriptografia de tokens
-   Credenciais de identidade de serviço
-   Credenciais de conta do Serviço de gerenciamento ACS.
-   Autenticação de provedor de identidade e certificados de criptografia.

O restante deste tópico aborda cada certificado e chave em detalhes.

## Certificados de autenticação de tokens

O ACS assina todos os tokens de segurança que emite. Os certificados X.509 são usados para autenticação quando você cria um aplicativo que consome tokens SAML emitidos pelo ACS.

Você pode gerenciar certificados de autenticação de tokens por meio da seção Certificados e Chave do Portal de Gerenciamento do ACS.

**Para gerenciar certificados de autenticação de tokens**

1.  Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3.  Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

    ![][0]

4.  Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página.

    ![][1]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][2]

5.  Clique em **Certificados e Chaves** na árvore à esquerda sob a seção Configurações do Serviço.

    ![][3]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][4]

6.  Na seção Autenticação de Tokens, use o botão Adicionar para configurar o novo certificado no ACS como uma chave "secundária", juntamente com o certificado existente que irá expirar.

7.  Notifique os parceiros que usam o serviço de que precisam atualizar suas chaves correspondentes antes de um determinado prazo.

8.  Os parceiros devem atualizar o certificado correspondente para suas terceiras partes confiáveis ou provedores de identidade. Por exemplo, importe os metadados do WS-Federation atualizados para o namespace ACS que contém o novo certificado de validação de assinatura de token ou configure manualmente a chave simétrica na configuração do aplicativo.

9.  Depois que todos os aplicativos tiverem sido atualizados (ou depois que a data limite tiver decorrido), marque o novo certificado como primário na configuração do ACS.

10. Depois de um período razoável, use o botão Excluir sob a seção Autenticação de Tokens da página Certificados e Chaves para remover o certificado antigo da configuração do ACS.

Para obter mais informações, consulte [Certificados e chaves][Certificados e chaves].

Quando os certificados de autenticação expirarem, você receberá os seguintes erros ao tentar solicitar um token:

|--------------------|------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Código do Erro** | **Mensagem**                                                                                                                 | **Ação necessária para corrigir a mensagem**                                                                                                                                                                                                                                          |
| ACS50004           | Nenhum certificado de autenticação X.509 primário está configurado. Um certificado de autenticação é necessário para o SAML. | Se a terceira parte confiável escolhida usar SAML como seu tipo de token, verifique se um certificado X.509 válido está configurado para a terceira parte confiável ou o namespace do serviço. O certificado deve estar definido como primário e estar dentro do período de validade. |

## Chave de autenticação de tokens

O ACS assina todos os tokens de segurança que emite. Chaves de autenticação simétricas de 256 bits são usadas quando você cria um aplicativo que consome tokens SWT emitidos pelo ACS.

Você pode gerenciar chaves de autenticação de tokens por meio da seção Certificados e Chave do Portal de Gerenciamento do ACS.

**Para gerenciar chaves de autenticação de tokens**

1.  Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3.  Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

    ![][0]

4.  Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página.

    ![][1]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][2]

5.  Clique em **Certificados e Chaves** na árvore à esquerda sob a seção Configurações do Serviço.

    ![][3]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][4]

6.  Na seção Autenticação de Tokens, use o botão Adicionar para configurar a nova chave no ACS como uma chave "secundária", juntamente com a chave existente que irá expirar.

7.  Notifique os parceiros que usam o serviço de que precisam atualizar suas chaves correspondentes antes de um determinado prazo.

8.  Os parceiros devem atualizar a chave correspondente para suas terceiras partes confiáveis ou provedores de identidade. Por exemplo, importe os metadados do WS-Federation atualizados para o namespace ACS que contém o novo certificado de validação de assinatura de token ou configure manualmente a chave simétrica na configuração do aplicativo.

9.  Depois que todos os aplicativos tiverem sido atualizados (ou depois que a data limite tiver decorrido), marque a nova chave como primária na configuração do ACS.

10. Depois de um período razoável, use o botão Excluir sob a seção Autenticação de Tokens da página Certificados e Chaves para remover a chave antiga da configuração do ACS.

Para obter mais informações, consulte [Certificados e chaves][Certificados e chaves].

Quando as chaves de autenticação expirarem, você receberá os seguintes erros ao tentar solicitar um token:

|--------------------|---------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Código do Erro** | **Mensagem**                                                                                                                    | **Ação necessária para corrigir a mensagem**                                                                                                                                                                                                                 |
| ACS50003           | Nenhuma chave de autenticação simétrica primária está configurada. Uma chave de autenticação simétrica é necessária para o SWT. | Se a terceira parte confiável escolhida usar SWT como seu tipo de token, verifique se a chave simétrica está configurada para a terceira parte confiável ou o namespace de serviço e se a chave está definida como primária e dentro do período de validade. |

## Certificados de criptografia de tokens

A criptografia do tokens será necessária se um aplicativo de uma terceira parte confiável for um serviço web que usa tokens de prova de posse através do protocolo WS-Trust. Em outros casos, a criptografia do token é opcional.

Você pode gerenciar certificados de criptografia de tokens por meio da seção Certificados e Chave do Portal de Gerenciamento do ACS.

**Para gerenciar certificados de criptografia de tokens**

1.  Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3.  Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

    ![][0]

4.  Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página.

    ![][1]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][2]

5.  Clique em **Certificados e Chaves** na árvore à esquerda sob a seção Configurações do Serviço.

    ![][3]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][5]

6.  Você (ou seus parceiros) atualiza o certificado ou a chave correspondente que é usada para descriptografia do token em aplicativos de terceiras partes confiáveis.
7.  Use o botão Adicionar para configurar o novo certificado de criptografia no ACS, juntamente com o certificado existente que irá expirar.
8.  Use Excluir para remover o antigo certificado de criptografia.

Para obter mais informações, consulte [Certificados e chaves][Certificados e chaves].

Quando os certificados de criptografia expirarem, você receberá os seguintes erros ao tentar solicitar um token:

|--------------------|--------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Código do Erro** | **Mensagem**                                                                                                                   | **Ação necessária para corrigir a mensagem**                                                                                                          |
| ACS50005           | A criptografia do token é necessária, mas nenhum certificado de criptografia está configurado para a terceira parte confiável. | Desabilite a criptografia de token para a terceira parte confiável escolhida ou carregue um certificado X.509 a ser usado para criptografia do token. |

## Certificados de descriptografia de tokens

O ACS pode aceitar tokens criptografados de provedores de identidade do WS-Federation (por exemplo, AD FS 2.0). Um certificado X.509 hospedado no ACS é usado na descriptografia.

Você pode gerenciar certificados de descriptografia de tokens por meio da seção Certificados e Chave do Portal de Gerenciamento do ACS.

**Para gerenciar certificados de descriptografia de tokens**

1.  Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3.  Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

    ![][0]

4.  Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página.

    ![][1]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][2]

5.  Clique em **Certificados e Chaves** na árvore à esquerda sob a seção Configurações do Serviço.

    ![][3]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][6]

6.  Na seção Descriptografia de tokens, use o botão Adicionar para configurar o novo certificado no ACS como uma chave "secundária", juntamente com o certificado existente que irá expirar.

7.  Notifique os parceiros que usam o serviço de que precisam atualizar suas chaves correspondentes antes de um determinado prazo.

8.  Os parceiros devem atualizar o certificado correspondente para suas terceiras partes confiáveis ou provedores de identidade. Por exemplo, importe os metadados do WS-Federation atualizados para o namespace ACS que contém o novo certificado de validação de assinatura de token ou configure manualmente a chave simétrica na configuração do aplicativo.

9.  Depois que todos os aplicativos tiverem sido atualizados (ou depois que a data limite tiver decorrido), marque o novo certificado como primário na configuração do ACS.

10. Depois de um período razoável, use o botão Excluir sob a seção Autenticação de Tokens da página Certificados e Chaves para remover o certificado antigo da configuração do ACS.

Para obter mais informações, consulte [Certificados e chaves][Certificados e chaves].

Quando os certificados de descriptografia expirarem, você receberá os seguintes erros ao tentar solicitar um token:

|--------------------|-----------------------------------------------------------------------------|
| **Código do Erro** | **Mensagem**                                                                |
| ACS10001           | Ocorreu um erro ao processar o cabeçalho SOAP.                              |
| ACS20001           | Ocorreu um erro ao processar uma resposta de autenticação do WS-Federation. |

## Credenciais de identidade de serviço

As identidades de serviço são credenciais que são configuradas globalmente para o namespace de ACS que permite que aplicativos ou clientes se autentiquem diretamente com o ACS e recebam um token. Existem três tipos de credenciais às quais uma identidade de serviço ACS pode ser associada: chave simétrica, senha e certificado X.509.

Você pode gerenciar as credenciais de identidade de serviço por meio da página Identidades de serviço do Portal de Gerenciamento do ACS.

**Para gerenciar credenciais de identidade de serviço**

1.  Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3.  Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

    ![][0]

4.  Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página.

    ![][1]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][2]

5.  Clique em **Identidades de serviço** na árvore à esquerda sob a seção Configurações do Serviço.

    ![][7]

6.  Clique na identidade de serviço que você deseja editar.

    ![][8]

7.  Na seção Credenciais, use o botão Adicionar para configurar o novo certificado ou chave no ACS, juntamente com o certificado ou chave existente que irá expirar.

8.  Você (ou seus parceiros) atualiza o certificado ou a chave correspondente que é usada para solicitações de token em aplicativos cliente.

9.  Depois que todos os clientes forem atualizados (ou depois de um período razoável), use o botão Excluir para remover o certificado ou chave antigo.

Para obter mais informações, consulte [Identidades do serviço][Identidades de serviço].

A seguir está a exceção que o ACS emitirá se as credenciais estiverem expiradas:

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Credencial&gt;</strong></td>
<td align="left"><strong>Código do Erro</strong></td>
<td align="left"><strong>Mensagem</strong></td>
<td align="left"><strong>Ação necessária para corrigir a mensagem</strong></td>
</tr>
<tr class="even">
<td align="left">Chave simétrica, senha</td>
<td align="left">ACS50006</td>
<td align="left">Falha na verificação da assinatura. (Pode haver mais detalhes na mensagem.)</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Certificado X.509</td>
<td align="left">ACS50016</td>
<td align="left">X509Certificate com assunto '&lt;Nome do assunto do certificado&gt;' e impressão digital '&lt;Impressão digital do certificado&gt;' não correspondem a nenhum certificado configurado.</td>
<td align="left">Verifique se o certificado solicitado foi carregado no ACS.</td>
</tr>
</tbody>
</table>

Para verificar e atualizar as datas de validade das chaves simétricas ou senhas ou para carregar novo certificado como credenciais de identidade do serviço, siga as instruções descritas em [Como: Adicionar identidade de serviço com um certificado X.509, senha ou chave simétrica][Como: Adicionar identidade de serviço com um certificado X.509, senha ou chave simétrica]. Lista de credenciais de identidade de serviço disponíveis na página Editar Identidade de Serviço.

## Credenciais do serviço de gerenciamento

O Serviço de Gerenciamento do ACS é um componente-chave do ACS que permite gerenciar e definir configurações de forma programática em um namespace ACS. Existem três tipos de credenciais aos quais a conta do Serviço de Gerenciamento do ACS pode estar associada. Os tipos são chave simétrica, senha e certificado X.509.

Você pode gerenciar as credenciais do serviço de gerenciamento por meio da página Serviço de gerenciamento do Portal de Gerenciamento do ACS.

**Para gerenciar as credenciais do Serviço de Gerenciamento do ACS.**

1.  Abra um navegador de Internet e visite o Portal de Gerenciamento do Azure ([][]<http://go.microsoft.com/fwlink/?LinkID=129428></a>).

2.  Faça logon no site usando um Windows Live ID. Se você não tiver um Windows Live ID, clique em Inscrever-se para criar um.

3.  Depois que você tiver entrado com seu Windows Live ID, você será redirecionado para a página do Portal de Gerenciamento. No canto esquerdo inferior dessa página, clique em **Barramento de Serviço e Controle de Acesso**.

    ![][0]

4.  Para iniciar o Portal de Gerenciamento do ACS, clique em **Controle de Acesso** na árvore à esquerda, selecione o namespace do serviço ACS que deseja configurar e clique no botão **Serviço de Controle de Acesso** na barra de ferramentas na parte superior da página.

    ![][1]

    Neste ponto, sua tela deve ser semelhante a esta:

    ![][2]

5.  Clique em **Serviço de gerenciamento** na árvore à esquerda sob a seção Administração.

    ![][9]

6.  Clique na conta do serviço de gerenciamento.

    ![][10]

7.  Na seção Credenciais, use o botão Adicionar para configurar o novo certificado ou chave no ACS, juntamente com o certificado ou chave existente que irá expirar.

8.  Você (ou seus parceiros) atualiza o certificado ou a chave correspondente que é usada para solicitações de token em aplicativos cliente.

9.  Depois que todos os clientes forem atualizados (ou depois de um período razoável), use o botão Excluir para remover o certificado ou chave antigo.

Para obter mais informações, consulte [Serviço de Gerenciamento do ACS][Serviço de Gerenciamento ACS].

O ACS emitirá as seguintes exceções se estas credenciais estiverem expiradas:

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Credencial&gt;</strong></td>
<td align="left"><strong>Código do Erro</strong></td>
<td align="left"><strong>Mensagem</strong></td>
<td align="left"><strong>Ação necessária para corrigir a mensagem</strong></td>
</tr>
<tr class="even">
<td align="left">Chave simétrica, senha</td>
<td align="left">ACS50006</td>
<td align="left">Falha na verificação da assinatura. (Pode haver mais detalhes na mensagem.)</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Certificado X.509</td>
<td align="left">ACS50016</td>
<td align="left">X509Certificate com assunto '&lt;Nome do assunto do certificado&gt;' e impressão digital '&lt;Impressão digital do certificado&gt;' não correspondem a nenhum certificado configurado.</td>
<td align="left">Verifique se o certificado solicitado foi carregado no ACS.</td>
</tr>
</tbody>
</table>

A lista das credenciais de contas do Serviço de Gerenciamento do ACS está disponível na página Editar Conta do Serviço de Gerenciamento no Portal de Gerenciamento do ACS.

## Certificado de provedor de identidade do WS-Federation

O Certificado do provedor de identidade do WS-Federation está disponível por meio de seus metadados. Ao configurar o provedor de identidade do WS-Federation, como o AD FS, o certificado de assinatura do WS-Federation é configurado por meio de metadados do WS-Federation disponíveis via URL ou como um arquivo, leia [Provedores de Identidade do WS-Federation][Provedores de Identidade do WS-Federation] e [Como: configurar o AD FS 2.0 como um Provedor de Identidade][Como: configurar o AD FS 2.0 como um Provedor de Identidade] para obter mais informações. Depois de configurar o provedor de identidade do WS-Federation no ACS, use o serviço de gerenciamento do ACS para consultar a validade de seus certificados. Observe que para cada carregamento consecutivo de metadados por meio do Portal de Gerenciamento do ACS ou do Serviço de Gerenciamento do ACS, as chaves serão substituídas.

A seguir estão as exceções que o ACS emitirá se o certificado estiver expirado:

|--------------------|-----------------------------------------------------------------------------|
| **Código do Erro** | **Mensagem**                                                                |
| ACS10001           | Ocorreu um erro ao processar o cabeçalho SOAP.                              |
| ACS20001           | Ocorreu um erro ao processar uma resposta de autenticação do WS-Federation. |
| ACS50006           | Falha na verificação da assinatura. (Pode haver mais detalhes na mensagem.) |

  [Access Control Service 2.0]: http://msdn.microsoft.com/library/azure/hh147631.aspx
  [Certificados e chaves]: http://msdn.microsoft.com/pt-br/library/gg185932.aspx
  [Identidades de serviço]: http://msdn.microsoft.com/pt-br/library/gg185945.aspx
  [Serviço de Gerenciamento ACS]: http://msdn.microsoft.com/pt-br/library/gg185972.aspx
  [IdentityProviderKey]: http://msdn.microsoft.com/pt-br/library/hh124084.aspx
  [Códigos de erro do ACS]: http://msdn.microsoft.com/pt-br/library/gg185949.aspx
  [Amostra do código: Serviço de gerenciamento]: http://msdn.microsoft.com/pt-br/library/gg185970.aspx
  [0]: http://go.microsoft.com/fwlink/?LinkID=129428
  [0]: ./media/manage-acs-namespace/ACS1.png
  [1]: ./media/manage-acs-namespace/ACS2.png
  [2]: ./media/manage-acs-namespace/ACS3.png
  [3]: ./media/manage-acs-namespace/ACS4.png
  [4]: ./media/manage-acs-namespace/ACS5.png
  [5]: ./media/manage-acs-namespace/ACS7.png
  [6]: ./media/manage-acs-namespace/ACS9.png
  [7]: ./media/manage-acs-namespace/ACS11.png
  [8]: ./media/manage-acs-namespace/ACS112.png
  [Como: Adicionar identidade de serviço com um certificado X.509, senha ou chave simétrica]: http://msdn.microsoft.com/pt-br/library/gg185924.aspx
  [9]: ./media/manage-acs-namespace/ACS14.png
  [10]: ./media/manage-acs-namespace/ACS15.png
  [Provedores de Identidade do WS-Federation]: http://msdn.microsoft.com/pt-br/library/gg185933.aspx
  [Como: configurar o AD FS 2.0 como um Provedor de Identidade]: http://msdn.microsoft.com/pt-br/library/gg185961.aspx
