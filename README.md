# Estudos Programação

Notas sobre o que estou estudando, em ordem aleatória.

### JWT

JWT é um método pra transmitir dados de forma segura entre dois endpoints.

Geralmente, é utilizado pra autenticação de usuários e trocas de informação com segurança.

Para fazer autenticação via JWT, é preciso seguir alguns passos:

1. No momento que o usuário loga no sistema, o backend emite um JWT para o usuário e manda para o `client-side`.

2. Esse token possui uma assinatura especial que valida se o token foi emitido pelo sistema ou não.

3. O cliente guarda esse token no browser e manda ele junto com todo request que fizer ao servidor, porque o token é usado pra verificar a autenticação do usuário.

Se você reparar no formato de um JWT, ele é composto por 3 partes separadas por um `.`, a primeira é o `header`, a segunda o `payload` e a terceira a `assinatura`.

**Header**

O header possui metadados sobre o token em formato de JSON. O primeiro é `alg` que é o algoritmo usado pra assinar o token e o segundo é `typ` que sempre é JWT que é o tipo de token utilizado.

**Payload**

O payload guarda informações sobre o token, por exemplo, o ID do usuário e a permissão. É comum usar alguns desses claims (informações):

- iss: quem emitiu o token.
- exp: o tempo que esse token é válido antes de expirar.
- iat: armazena o horário que o token foi emitido.

Não coloque nenhuma info sensível no payload porque ele não é criptografado, ele é só encodado com base no base64url.

**Assinatura**

A assinatura é usada pra verificar se o token foi emitido por um dos servidores/aplicações autorizadas e não gerada por algum sistema não-autorizado.

Ela é o resultado do `header` e do `payload` somado a um algoritmo de criptografia e um segredo que fica armazenado no servidor. Por isso é importante usar um segredo robusto pra criptografar os tokens e armazená-los de forma segura no servidor.

Assim que for validado que o token foi emitido pelo servidor da aplicação e aí serve o request feito pelo cliente.

**Vantagens do JWT**

A maior diferença entre autenticar com JWT e, por exemplo, autenticações `session-based` é que o método por JWT não guarda estado no servidor (é stateless).

Na prática, isso quer dizer que se a sua aplicação tiver mais de um servidor, o primeiro que emitiu o JWT não precisa necessariamente ser o mesmo que valida se o JWT é válido ou não.

**Cuidados com esse método**

Podemos mandar e receber JWTs entre o servidor e o cliente por meio de um `Authorization HTTP header`. Mas pra evitar ataques MITM (Man in the middle - intermediário) e tokens "roubados" é preciso usar conexões HTTP seguras.

Já pra evitar ataques XSS, você pode mandar o token dentro de um cookie ao invés do Authorization header e setar `HttpOnly` e proteger flags do cookie pra prevenir invasores de interceptar tokens.

Imaginemos que um token foi interceptado e quem interceptou consegue fazer tudo o que o usuário real conseguiria fazer. Uma das formas de mitigar o dano que o atacante possa fazer é setar um tempo baixo de expiração do token. O problema desse método é que o usuário real que não foi atacado vai ter que relogar a cada X tempo  se for um tempo muito curto, a experiência dessa pessoa não vai ser boa.

A solução pra esse problema do usuário ter que relogar várias vezes enquanto usa a aplicação é a `atualização de tokens`. Um token atualizado possui um período maior pra expirar e não passa pelo client-side, é armazenado num banco de dados. O banco de dados que possuem esses tokens devem possuir todas as medidas de segurança pra proteger esses tokens. Caso alguém consiga invadir esse banco de dados, é uma falha de segurança gravíssima.

Quando o token atualizado expira, aí realmente o usuário precisa logar novamente.

**Fonte**: [aqui](https://livecodestream.dev/post/2020-07-31-a-brief-introduction-to-securing-applications-with-jwt/)

