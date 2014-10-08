1.  No Visual Studio, execute o aplicativo cliente e tente autenticar com a conta do usuário chamado Dave criada.

    ![][1]

2.  Dave não está associado ao grupo Vendas. Portanto, a verificação de acesso baseada em função negará acesso às operações de tabela. Feche o aplicativo cliente.

    ![][2]

3.  No Visual Studio, execute o aplicativo cliente novamente. Desta vez, autentique com a conta do usuário chamado Bob criada.

    ![][3]

4.  Bob está associado ao grupo Vendas. Portanto, a verificação de acesso baseada em função permitirá acesso às operações de tabela.

    ![][4]

  [1]: ./media/mobile-services-aad-rbac-test-app/dave-login.png
  [2]: ./media/mobile-services-aad-rbac-test-app/unauthorized.png
  [3]: ./media/mobile-services-aad-rbac-test-app/bob-login.png
  [4]: ./media/mobile-services-aad-rbac-test-app/success.png
