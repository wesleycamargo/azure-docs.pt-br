---

title: "Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory | Microsoft Docs"
description: "Mais cenários de licenciamento baseado em grupo do Azure Active Directory"
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dad9a176f5f60b9165c5a55628929460047e5fdd
ms.openlocfilehash: a1510240350d88ee140acb11b3f86fd7985b9427
ms.lasthandoff: 02/22/2017


---

# <a name="azure-active-directory-group-based-licensing-additional-scenarios"></a>Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory

## <a name="group-based-licensing-using-dynamic-groups"></a>Licenciamento baseado em grupo usando grupos dinâmicos

O licenciamento baseado em grupo pode ser usado com qualquer grupo de segurança, ou seja, pode ser combinado com Grupos Dinâmicos do Azure AD. Os Grupos Dinâmicos executam regras com base em atributos de objeto de usuário para adicionar e remover usuários automaticamente dos grupos.

Por exemplo, você pode criar vários grupos dinâmicos, um para cada conjunto de produtos que você deseja atribuir aos usuários. Cada grupo contém uma regra que observa um atributo específico em um usuário, descrevendo qual conjunto de licenças ele deve receber. O atributo pode ser atribuído localmente e sincronizado com o Azure AD, ou gerenciado diretamente na nuvem.

Quando o atributo é especificado, o usuário é adicionado a um ou mais desses grupos de licenciamento dinâmicos. As licenças serão atribuídas ao usuário logo depois disso. Quando o atributo é removido, o usuário deixa os grupos, e as licenças são removidas.

### <a name="example"></a>Exemplo

Nesse exemplo, tenho uma solução de gerenciamento de identidade local que decide quais usuários devem ter acesso a quais serviços do Microsoft Online. Ela usa extensionAttribute1 para armazenar um valor de cadeia de caracteres que representa as licenças que o usuário deve ter, e o Azure AD Connect sincroniza isso com o Azure AD. Quero distribuir licenças do Office 365 E5 e EMS aos meus usuários. No Azure AD, eu criei dois grupos dinâmicos, uma para cada produto, pois talvez alguns usuários precisem de um dos produtos, mas não de outro. Eu especifiquei as configurações de regra de associação e a licença dinâmica em cada grupo. Veja como ficam:

#### <a name="o365-e5--base-services"></a>O365 E5 – serviços base

![Serviços base do O365 E5](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="ems-e5--licensed-users"></a>EMS E5 – usuários licenciados

![Usuários licenciados do O365 E5](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

Localmente, eu modifiquei um usuário e defini o extensionAttribute1 dele com o valor de `EMS;E5_baseservices;`, pois quero que ele tenha as duas licenças. Após a sincronização da alteração com a nuvem, o usuário foi adicionado automaticamente aos dois grupos, e as licenças foram atribuídas.

![Definir extensionAttribute1 do usuário](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modifying-a-dynamic-group-membership-rule"></a>Como modificar uma regra de associação de grupo dinâmica

É necessária ter cuidado ao modificar a regra de associação de um grupo existente. Quando uma regra é alterada, todos os usuários são removidos do grupo, a regra é avaliada e os usuários são adicionados ao grupo com base nas novas condições.

Se o grupo tiver licenças atribuídas, todos os usuários terão essa licença removida durante o processo. Somente após a avaliação da nova regra e nova adição dos usuários, as novas licenças serão aplicadas. Isso significa que os usuários podem enfrentar perda de serviço ou, em alguns casos, perda de dados.

Recomendamos que você não altere a regra de associação em um grupo usado para atribuição de licença. Em vez disso, crie um novo grupo com a nova regra e especifique as mesmas configurações de licença do grupo original. Aguarde até que os membros tenham sido adicionados e as licenças tenham sido aplicadas a todos os usuários. Só então você poderá prosseguir e excluir o grupo original. Essa abordagem garante uma transição segura e em estágios para a nova regra de associação sem qualquer perda de acesso ou dados para os usuários.


## <a name="multiple-groups-and-multiple-licenses"></a>Vários grupos e várias licenças

Um usuário pode ser membro de vários grupos com licenças. Estas são algumas coisas que você deve considerar:

1. Várias licenças para o mesmo produto podem se sobrepor e resultarão na aplicação de todos os serviços habilitados ao usuário. Por exemplo, criamos dois grupos de licenciamento: *E3 – serviços base* contém os serviços base que desejamos implantar primeiro, a todos os usuários; *E3 – serviços estendidos* contém serviços adicionais (Sway e Planner) que queremos tentar com alguns usuários, enquanto o Yammer permanece desabilitado para implantações futuras. Neste exemplo, o usuário foi adicionado aos dois grupos:

  ![ver serviços habilitados](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  Como resultado, eles têm sete dos 12 serviços no produto habilitados, consumindo apenas uma licença para este produto.

2. A seleção da licença *E3* mostra mais detalhes, incluindo as informações sobre quais grupos causaram a habilitação de quais serviços para o usuário.

  ![ver serviços habilitados por grupo](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexisting-with-group-licenses"></a>Licenças diretas coexistindo com licenças do grupo

Quando um usuário herda uma licença de um grupo, não é possível remover ou modificar essa atribuição de licença diretamente no objeto do usuário. Em vez disso, qualquer alteração deve ser feita no grupo e, em seguida, propagada pelo sistema a todos os usuários.

No entanto, é possível atribuir a mesma licença de SKU diretamente ao usuário, além da licença herdada. Isso pode ser usado, por exemplo, para habilitar serviços adicionais do SKU apenas para um usuário, sem precisar afetar outros usuários.

Licenças atribuídas diretamente podem ser removidas, mas isso não afetará a licença herdada. Vamos considerar o seguinte usuário que herda uma licença *Office 365 Enterprise E3* de um grupo, o que habilita alguns dos serviços.

1. Inicialmente, o usuário herda a licença somente do grupo *E3 – serviços básicos*. Isso permite os quatro planos de serviço listados abaixo:

  ![Serviços habilitados do grupo E3](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Ao clicar no botão **Atribuir**, podemos atribuir diretamente uma licença E3 ao usuário, nesse caso, vamos desabilitar todos os planos de serviço com exceção do Enterprise Yammer.

  ![atribuir licença a um usuário](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Como resultado, o usuário ainda consome somente uma licença do produto E3; no entanto, a atribuição direta habilita o serviço *Yammer Enterprise* somente para esse usuário. Você pode ver na lista os serviços habilitados pela associação de grupo em comparação com a atribuição direta:

  ![atribuição herdada versus direta](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. As operações a seguir são permitidas usando a atribuição direta:

  a. *Yammer Enterprise* pode ser desativado diretamente no objeto do usuário. Observe que a opção Liga/Desliga está habilitada, ao contrário de outras opções de serviço. Isso ocorre porque esse serviço está habilitado diretamente no usuário e, portanto, pode ser modificado.

  b. Outros serviços também podem ser habilitados como parte da licença atribuída diretamente.

  c. O botão **Remover** pode ser usado para remover a licença direta do usuário. Veja que agora o usuário tem apenas a licença de grupo herdada, e somente os serviços originais permanecem habilitados.

    ![remover atribuição direta](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>Local de uso

Alguns serviços da Microsoft não estão disponíveis em todos os locais; antes de uma licença pode ser atribuída a um usuário, o administrador deve especificar a propriedade "Local de uso" ao usuário. Isso pode ser feito na seção Usuário-&gt;Perfil-&gt;Configurações no [Portal do Azure](https://portal.azure.com).

Ao usar a atribuição de grupo de licenças, qualquer usuário sem um local de uso especificado herdará o local do diretório. Se você tiver usuários em locais diferentes, certifique-se de refletir isso corretamente nos objetos do usuário antes de adicionar usuários a grupos de licenças.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Usar o PowerShell para ver quem tem licenças herdadas e diretas

Durante a visualização pública, o PowerShell não pode ser usado para controlar totalmente as atribuições de licenças de grupo. No entanto, ele pode ser usado para descobrir informações básicas sobre o estado do usuário e se as licenças são herdadas de um grupo ou atribuídas diretamente. O exemplo de código abaixo mostra como um administrador pode produzir um relatório básico sobre atribuições de licença.

1. Execute o cmdlet `connect-msolservice` para autenticar e conectar-se ao seu locatário.

2. `Get-MsolAccountSku` pode ser usado para descobrir todas as licenças de SKU provisionadas no locatário.

  ![usando o cmdlet Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Neste exemplo, queremos saber quais usuários têm a licença *EMS* diretamente atribuída, de um grupo, ou ambos. Usaremos um script do PowerShell que contém duas funções que podem responder a essa pergunta para um objeto de usuário e um SKU
  ```
  \# Returns TRUE if the user has the license assigned directly

  function UserHasLicenseAssignedDirectly
  {
      Param(\[Microsoft.Online.Administration.User\]\$user, \[string\]\$skuId)

      foreach(\$license in \$user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if (\$license.AccountSkuId -ieq \$skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              \# If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past

              if (\$license.GroupsAssigningLicense.Count -eq 0)
              {
                  return \$true
              }
              \# If the collection contains the ID of the user object, this means the license is assigned directly
              \# Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach (\$assignmentSource in \$license.GroupsAssigningLicense)
              {
                  if (\$assignmentSource -ieq \$user.ObjectId)
                  {
                      return \$true
                  }

              }
              return \$false
          }
      }
      return \$false
  }
  \# Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
      Param(\[Microsoft.Online.Administration.User\]\$user, \[string\]\$skuId)
      foreach(\$license in \$user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if (\$license.AccountSkuId -ieq \$skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              foreach (\$assignmentSource in \$license.GroupsAssigningLicense)
              {
                  \# If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  \# Note: the license may also be assigned directly in addition to being inherited
                  if (\$assignmentSource -ine \$user.ObjectId)
                  {
                      return \$true
                  }
              }
              return \$false
          }
      }
      return \$false
  }
  ```
4. O restante do script obtém todos os usuários, executa essas funções em cada usuário e, depois, formata a saída em uma tabela.

  ```
  \# the license SKU we are interested in
  \$skuId = "reseller-account:EMS"
  \# find all users that have the SKU license assigned
  Get-MsolUser -All | where {\$\_.isLicensed -eq \$true -and \$\_.Licenses.AccountSKUID -eq \$skuId} | select \`
      ObjectId, \`
      @{Name="SkuId";Expression={\$skuId}}, \`
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly \$\_ \$skuId)}}, \`
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup \$\_ \$skuId)}}
  ```

5. A saída do script completo aparece da seguinte forma:

  ![Saída do script do PowerShell](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

1. No momento, o licenciamento baseado em grupo não oferece suporte a "grupos aninhados" (grupos que contêm outros grupos). Se você aplicar uma licença a um grupo aninhado, somente os membros imediatos de primeiro nível do grupo terão as licenças aplicadas.

2. Atualmente, o licenciamento baseado em grupo é exposto apenas por meio do [Portal do Azure](https://portal.azure.com). No momento, não é possível usar o PowerShell para definir ou modificar licenças em grupos.

3. No momento, o [Portal de Administração do Office 365](https://portal.office.com ) não oferece suporte ao licenciamento baseado em grupo. Se um usuário herdar uma licença de um grupo, essa licença aparecerá no Portal de Administração do Office como uma licença de usuário comum. Se você tentar modificar essa licença (por exemplo, para desabilitar um serviço na licença, ou tentar remover a licença) o portal retornará uma mensagem de erro (pois licenças de grupo herdadas não podem ser modificadas diretamente em um usuário).

  `To assign a license that contains Azure Information Protection Plan 1, you must also assign one of the following service plans: Azure Rights Management.`

4. Quando um usuário é removido de um grupo e perde a licença, os planos de serviço dessa licença (por exemplo, Exchange Online ou SharePoint Online) são definidos como um estado "suspenso" em vez de um estado desabilitado final. Isso é feito como uma precaução para evitar a remoção acidental de dados do usuário se um administrador cometer um erro no gerenciamento de associação do grupo.

  Vamos implementar um fluxo de trabalho no qual o estado desses planos de serviço será, eventualmente, completamente desabilitado para esses usuários. Até que isso esteja disponível, alguns serviços podem continuar operando para usuários que foram removidos de um grupo e que não têm mais uma licença.

5. Quando as licenças são atribuídas ou modificadas em um grupo muito grande de usuários (por exemplo, 100.000 usuários), a grande quantidade de alterações geradas pela automação do Azure AD pode afetar negativamente o desempenho da sincronização de diretório entre o Azure AD e sistemas locais. Isso pode causar atrasos na sincronização de diretório em seu ambiente.

6. A automação de gerenciamento de licença não reage automaticamente a todos os tipos de alteração no ambiente. Por exemplo, você pode ter ficado sem licenças e alguns usuários estão com o estado de erro "Sem licenças suficientes". Você pode remover algumas licenças atribuídas diretamente de outros usuários para liberar a contagem de estações disponíveis. No entanto, o sistema não reagirá automaticamente a essa alteração e corrigirá os usuários nesse estado de erro.

  Como alternativa a esses tipos de limitações, vá até a folha do grupo no Azure AD e clique no botão **Reprocessar**. Isso processará todos os usuários nesse grupo e resolverá os estados de erro, se for possível.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licenças por meio de licenciamento baseado em grupo, leia

* [O que é o licenciamento baseado em grupo no Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Atribuição de licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)

