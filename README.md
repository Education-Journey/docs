# Documentação Interface EJ e Parceiros

> **Disclaimer**: Os _endpoints_ descritos abaixo são apenas sugestões de implementação. Se a sua empresa já possuir _endpoints_ que cumprem o mesmo objetivo, esses serão preferidos. Ademais, sinta-se a vontade para sugerir mudanças e melhorias na implementação da documentação abaixo.

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

## Criação de Assinatura
Num primeiro momento, a Education Journey decidiu em fazer a integração para a criação de usuários/assinatura da forma mais simples possível. Chegamos em dois modelos principais para atingir tal objetivo: criação por URL parametrizada e criação via _webhook_.

### Criação de Assinatura via _webhook_
___
Reconhecemos que muitos parceiros rodam seus cursos em plataformas de terceiros que possuem um suporte para integração via _webhooks_, como o Kajabi. Para esses parceiros, os links dos _webhooks_ e de uma documentação são o suficiente para a Education Journey. Se esse não for o caso, a criação desse _endpoint_ deverá ser realizada conforme especificado abaixo.

#### Request
Método: `POST`

Path: `/subscriptions/create`

#### Request Body

```json
{
  "user_external_id": "5637",
  "user_first_name": "Artur",
  "user_last_name": "Barbosa",
  "user_full_name": "Artur Barbosa",
  "user_email": "artur@educationjourney.com"
}
```

Lembrando que esse _endpoint_ pode e deve ser customizado de acordo com as necessidades do parceiro. Os _request body_ acima representa as informações que a Education Journey **pode** enviar para o parceiro. Em quase todos os casos, será necessário que o parceiro envie para o time de tecnologia da Education Journey as especificações de como o _endpoint_ foi implementado, incluindo informações adicionais (como identificador da empresa origem, ex: "educationjourney"), dados que não serão utilizados, como o `user_external_id`, e dados que precisarão ser transformados, como `full_name`, `first_name`, `last_name`.

#### Response
A resposta deverá ser em formato `JSON`, seguindo o seguite template:

```json
{
  "created": true,
  "created_at": 1625569968
}
```

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
