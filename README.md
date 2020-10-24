# Estudos Programação

Notas sobre o que estou estudando, em ordem aleatória.

### JWT

JWT é um método pra transmitir dados de forma segura entre dois endpoints.

Geralmente, é utilizado pra autenticação de usuários e trocas de informação com segurança.

Para fazer autenticação via JWT, é preciso seguir alguns passos:

1. No momento que o usuário loga no sistema, o backend emite um JWT para o usuário e manda para o `client-side`.

2. Esse token possui uma assinatura especial que valida como um token que foi emitido pelo sistema.

3. O cliente guarda esse token no browser e manda ele junto com todo request que fizer ao servidor, porque o token é usado pra verificar a autenticação do usuário.

Se você reparar no formato de um JWT, ele é composto por 3 partes separadas por um `.`, a primeira é o `header`, a segunda o `payload` e a terceira a `assinatura`.

**Header**

O header possui metadados sobre o token em formato de JSON. O primeiro é `alg` que é o algoritmo usado pra assinar o token quando for gerar a assinatura e o segundo é `typ` que sempre é JWT que é o tipo de token utilizado.

**Payload**

O payload guarda informações sobre o token, por exemplo, o ID do usuário e a permissão. É comum usar alguns desses claims:

- iss: quem emitiu o token.
- exp: o tempo que esse token é válido antes de expirar.
- iat: armazena o horário que o token foi emitido.

Não coloque nenhuma info sensível no payload porque ele não é criptografado, ele é só encodado com base no base64url.

**Assinatura**

A assinatura é usada pra verificar se o token foi emitido por um dos servidores/aplicações autorizadas e não gerada por algum sistema não-autorizado.

Ela é o resultado do `header` e do `payload` somado a um algoritmo de criptografia e um segredo que fica armazenado no servidor. Por isso é importante usar um segredo robusto pra criptografar os tokens e armazená-los de forma segura no servidor.

