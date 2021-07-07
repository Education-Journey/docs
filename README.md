# Documentação Interface EJ e Parceiros

### Autenticação e Autorização
___
Sugerimos a criação de uma `API Key` única para a Education Journey para autorizar o uso da API do parceiro.

Também sugerimos que a autenticação seja performada por meio do HTTP Basic Auth. Nesse caso, a Education Journey fornece a API como o auth username e a senha não será utilizada.

A combinação username:password deverá ser codificada com Base64. O respectivo `header` da requisição deverá parecer com algo assim: `Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`.

### Cancelamento de Assinatura
___

Esse documento tem como finalidade sugerir o endpoint do webhook de cancelamento de assinatura na plataforma da Education Journey. 

#### Descrição do Cenário
O webhook é acionado pela Education Journey e mandando para a plataforma parceira. O evento ocorre quando a assinatura de determinado usuário é cancelada pelo mesmo ou por falta de pagamento. 

O parceiro deverá escrever um enpoint utilizando o método `DELETE` com o path `/subscriptions/{user_email}/suspend`

Como não há outro meio único de identificar o usuário, o e-mail servirá como tal.

#### Request
Método: `DELETE`

Path: `/subscriptions/{user_email}/suspend`

#### Response
A resposta deverá ser em formato `JSON`, seguindo o seguite template:

```json
{
  "suspended": true,
  "suspended_at": 1625569968
}
```


### Reativação de Assinatura
___
Há também a necessidade de reativação da assinatura. Como exemplo, podemos imaginar um usuário que deixou de pagar o plano na Education Journey e o mesmo foi revogado. Porém, o usuário passou a pagar novamente pelo plano depois de determinado tempo. O objetivo desse endpoint é de fazer com que a assinatura também seja reativada no parceiro.

#### Request
Método: `POST`

Path: `/subscriptions/{user_email}/renew`

#### Response
A resposta deverá ser em formato `JSON`, seguindo o seguite template:

```json
{
  "renewed": true,
  "renewed_at": 1625569968
}
```

Para que o webhook funcione com sucesso, o usuário deverá existir e estar com a assinatura suspensa no momento da chamada. 

### URL Parametrizada para acesso
___
A URL parametrizada poderá ser feita de duas maneiras: direto para o login e direto para o conteúdo.

#### Direto para o login
https://parceiro.com.br/login?redirect=https://parceiro.com.br/conteudos/curso-de-marketing&code=XPTOSM

`redirect`: essa é um URL que deve redirectionar o usuário após o login/cadastro.

`code`: pode ser um código, como um cupom que ativa a ferramenta com 100% de desconto, ou um `source` que identifica que o usuário vem da Education Journey para auxiliar no processo de aprovação do mesmo.

Caso o usuário já estiver logado, o mesmo deverá ser redirecionado para a página do curso. Apos o cadastro/login, o usuário deverá ser levado para a URL especificada no parâmetro `redirect`.

#### Direto para o conteúdo
https://parceiro.com.br/conteudos/curso-de-marketing?code=XPTOSM

`code`: pode ser um código, como um cupom que ativa a ferramenta com 100% de desconto, ou um `source` que identifica que o usuário vem da Education Journey para auxiliar no processo de aprovação do mesmo.

Caso o usuário não esteja logado ou cadastrado, o mesmo deve ser redirecionado para a tela de login com os mesmos parametros de URL que a sessão anterior.

#### Dados adicionais para preenchimento do login/cadastro
Além dos parâmetros de URL obrigatórios, ofereceremos parâmetros de URL opcionais sobre o usuário que servirão para facilitar a experiência do mesmo. Ofereceremos os seguintes parâmetros:

`first_name`: primeiro nome do usuário

`last_name`: sobrenome do usuário

`email`: e-mail do usuário

`company_name`: noma da empresa do usuário (nome fantasia)

`cpf`: CPF do usuário

Com esses dados, o parceiros poderá pré-preencher os campos de cadastro ou login.
