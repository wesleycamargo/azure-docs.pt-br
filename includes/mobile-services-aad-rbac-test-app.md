
As instruções e capturas de tela abaixo se aplicam ao teste de um cliente da Windows Store, mas isso pode ser testado em qualquer uma das outras plataformas compatíveis com os Serviços Móveis do Azure.

1. No Visual Studio, execute o aplicativo cliente e tente autenticar com a conta do usuário chamado Dave criada. 
   
    ![](./media/mobile-services-aad-rbac-test-app/dave-login.png)
2. Dave não está associado ao grupo Vendas. Portanto, a verificação de acesso baseada em função negará acesso às operações de tabela. Feche o aplicativo cliente.
   
    ![](./media/mobile-services-aad-rbac-test-app/unauthorized.png)
3. No Visual Studio, execute o aplicativo cliente novamente. Desta vez, autentique com a conta do usuário chamado Bob criada.
   
    ![](./media/mobile-services-aad-rbac-test-app/bob-login.png)
4. Bob está associado ao grupo Vendas. Portanto, a verificação de acesso baseada em função permitirá acesso às operações de tabela.
   
    ![](./media/mobile-services-aad-rbac-test-app/success.png)

<!---HONumber=Oct15_HO3-->