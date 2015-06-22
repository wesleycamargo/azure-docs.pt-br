<properties 
	pageTitle="Gerenciando o Azure AD Connect" 
	description="Aprenda a estender configuração padrão e tarefas operacionais para o Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Gerenciando o Azure AD Connect 


<div class="dev-center-tutorial-selector sublanding">
<a href="/pt-br/documentation/articles/active-directory-aadconnect/" title="O que é" class="current">O que é</a> <a href="/pt-br/documentation/articles/active-directory-aadconnect-how-it-works/" title="Como funciona">Como funciona</a> <a href="/pt-br/documentation/articles/active-directory-aadconnect-get-started/" title="Introdução">Introdução</a> <a href="/pt-br/documentation/articles/active-directory-aadconnect-whats-next/" title="O que vem a seguir">O que vem a seguir</a> <a href="/pt-br/documentation/articles/active-directory-aadconnect-learn-more/" title="Saiba mais">Saiba mais</a>
</div>

Os tópicos são tópicos operacionais avançados que permitem que você personalize o Azure Active Directory Connect para atender às necessidades e requisitos de sua organização.

## Alterando a configuração padrão
A configuração padrão do Azure AD Connect na maioria dos casos é suficiente para estender facilmente seus diretórios locais para a nuvem. No entanto, há determinadas instâncias em que talvez seja necessário modificar o padrão de acordo com a lógica de negócios das organizações. Nesses casos, você pode modificar a configuração padrão; no entanto, há algumas coisas que você precisa saber antes de fazer isso.

Se você estiver atualizando ou movendo do Azure AD Sync ou DirSync, esteja atento ao seguinte:

- Depois de atualizar o Azure AD Sync para uma versão mais recente, a maioria das configurações será redefinida de volta ao padrão.
- Alterações nas regras de sincronização integradas são perdidas após uma atualização.
- Regras de sincronização integrada excluídas são recriadas durante uma atualização para uma versão mais recente.
- Regras de sincronização personalizada criadas por você permanecem inalteradas quando uma atualização para uma versão mais recente é aplicada.

Quando você precisar alterar a configuração padrão, faça o seguinte:

- Quando você precisa modificar um fluxo de atributos de uma regra de sincronização integrada, não altere. Em vez disso, crie uma nova regra de sincronização com uma precedência mais alta (valor numérico menor) que contém o fluxo de atributo necessário.
- Exporte suas regras de sincronização personalizado usando o Editor de regras de sincronização. Isso fornece um script do PowerShell que você pode usar para recriá-las facilmente no caso de um cenário de recuperação de desastres.
- Se você precisar alterar o escopo ou a configuração de junção em uma regra de sincronização integrada, documente isso e reaplique a alteração após atualizar para uma versão mais recente do Azure AD Connect.






 

## Usando o editor de regras de sincronização

No Azure AD Connect, você pode configurar e ajustar o fluxo de atributos e objetos entre o AD do Azure e os diretórios locais configurando regras de sincronização. As regras de sincronização podem ser configuradas usando o Editor de regras de sincronização. O Editor de regras de sincronização é instalado quando você instala o Azure AD Connect. Para usar o Editor, é necessário ser um membro do grupo ADSyncAdmins ou o grupo de administradores especificado durante a instalação do Azure AD Connect.

Na captura de tela abaixo, você verá todas as regras de sincronização criadas para sua configuração, quando você instala o Azure AD Connect usando a instalação do Express. Cada linha na tabela é uma regra de sincronização. À esquerda, em Tipos de Regra, são listados os dois tipos diferentes: entrada e saída. Entrada e saída é da exibição do metaverso. Ou seja, trazemos informações de nossos diretórios para o metaverso. Saída refere-se às regras nas quais podemos enviar informações e atributos para nossos diretórios, como nosso Active Directory local ou AD do Azure.

<center>![Editor de regras de sincronização](./media/active-directory-aadconnect-whats-next/Synch_Rule.png) </center>

Para criar uma nova regra, selecione Adicionar nova regra e, em seguida, configure a regra. Por exemplo, vamos supor que desejamos criar uma regra de junção em que qualquer usuário no diretório local se unirá ao objeto metaverso que tem o mesmo número de telefone. Para fazer isso, crie a nova regra e especifique o sistema conectado, no nosso caso contoso.com, o tipo de objeto de sistema conectado, usuário, o tipo de objeto metaverso, pessoa e o tipo de junção do link.

<center>![Criar regra de sincronização](./media/active-directory-aadconnect-whats-next/synch2.png) </center>


Na tela de regras de junção, especifique telephoneNumber no atributo de origem e telephoneNumber no atributo de destino. E isso é tudo. Você agora criou com êxito uma regra de junção.

<center>![Regra de junção](./media/active-directory-aadconnect-whats-next/synch3.png) </center>

Você pode usar o Editor de regras de sincronização para aplicar uma lógica comercial adicional fora da configuração padrão e ajustá-la às necessidades da organização. Para obter informações adicionais sobre o Editor de regras de sincronização, consulte [Noções básicas sobre a configuração padrão](https://msdn.microsoft.com/library/azure/dn800963.aspx).


## Usando o provisionamento declarativo 
Provisionamento declarativo é provisionamento "sem código" e pode ser instalado e configurado usando o Editor de regras de sincronização. O Editor pode ser usado para a instalação e para criar suas próprias regras de provisionamento.

Uma parte essencial de provisionamento declarativo é a linguagem de expressão usada nos fluxos de atributo. A linguagem usada é um subconjunto de VBA (Visual Basic® for Applications) da Microsoft. Essa linguagem é usada no Microsoft Office e os usuários com experiência em VBScript também a reconhecerão. A Linguagem de expressão de provisionamento declarativo está apenas usando funções e não é uma linguagem estruturada; não existem métodos nem instruções. Em vez disso, as funções serão aninhadas no fluxo do programa expresso.

Para obter mais informações sobre linguagem de , consulte [Noções básicas sobre expressões de provisionamento declarativo](https://msdn.microsoft.com/library/azure/dn801048.aspx)

## Documentação adicional
Algumas das documentações criadas para o Azure AD Sync ainda são relevantes e aplicam-se ao Azure AD Connect. Embora todos os esforços estejam sendo feitos para trazer esta documentação para o Azure.com, uma parte dela ainda reside na biblioteca de escopo do MSDN. Para obter documentação adicional, consulte [Azure AD Connect no MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) e [Azure AD Sync no MSDN](https://msdn.microsoft.com/library/azure/dn790204.aspx).

**Recursos adicionais**

* [Usar sua infraestrutura de identidade local na nuvem](active-directory-aadconnect.md)
* [Como o Azure AD Connect funciona](active-directory-aadconnect-how-it-works.md)
* [Introdução ao Azure AD Connect](active-directory-aadconnect-get-started.md)
* [O que vem a seguir com o Azure AD Connect](active-directory-aadconnect-whats-next.md)
* [Saiba mais](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect no MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58--> 