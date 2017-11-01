Com o Azure Resource Manager, você define parâmetros para os valores que deseja especificar ao implantar o modelo. O modelo inclui uma seção `parameters`, que contém todos os valores de parâmetro. Cada valor de parâmetro é usado pelo modelo para definir os recursos que você deseja implantar.

> [!NOTE]
> Não defina parâmetros para valores que permanecem sempre os mesmos. Defina os parâmetros apenas para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre.

Quando definir parâmetros:

* Para especificar os valores permitidos que um usuário pode fornecer durante a implantação, use o campo **allowedValues**.

* Use o campo **defaultValue** para atribuir um valor a um parâmetro se nenhum valor for indicado durante a implantação. 
