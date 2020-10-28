# Estudos Programação

Notas sobre o que estou estudando, em ordem aleatória.

# Designing Data-Intensive Applications

### Reliability (Confiabilidade)

Alguns dos fatores para um software ser considerado `confiável` (reliable) são:

- A aplicação performa sua função como o usuário espera;
- Consegue tolaerar que o usuário cometa erros ou use o software de maneiras inesperadas;
- A performance é boa o suficiente pro caso de uso, suportando o volume esperado de dados;
- O sistema previne qualquer abuso ou acesso não-autorizado.

Resumindo, significa que o software continua a funcionar corretamente mesmo quando as coisas dão errado.

Essas coisas que podem dar errado são chamado de `falhas` (faults), e sistemas que conseguem se antecipar a essas falhas são chamados de `tolerante a falhas` ou `resiliente`. É importante destacar que essas falhas não significa o sistema parar de proporcionar o serviço ao usuário e que nenhum sistema consegue ser tolerante a todos os tipos de falhas, mas sim a alguns tipos de falhas. Muitas dessas falhas são causadas por má gestão de erros.

Nesse quesito é importante aumentar a taxa de falhas de propósito, assim você garante que o sistema é tolerante a falhas e aumenta sua confiança que falhas serão gerenciadas de forma correta quando ocorrerem de fato. [Ver Chaos Monkey](https://github.com/Netflix/chaosmonkey).

Tipos de falhas mais comuns são:

1. **Falha de hardware**

Exemplos: HD queimou, RAM não está funcionando direito.

Como mitigar: adicionar redundância nos componentes individuais pra reduzir a taxa de falhas no sistema.

Outra maneira de mitigar essas falhas são, adicionalmente ao aumento de redundância, utilizar técnicas de tolerância a falhas como `rolling upgrade` (fazer um patch de segurança, por exemplo, sem ser necessário o downtime).

2. **Falha de software**

Esse tipo de falha causa muito mais problemas do que as de hardware que geralmente são pontuais porque são menos previsíveis e por ter potencial muito maior de causar falha total nos sistemas. Um exemplo de falha de software é de um serviço que o sistema depende começa a ficar mais lento ou não retornar o que é esperado, ou falhas em cascatas que uma pequena falha em um componente faz acontecer outra em outro componente e vira uma bola de neve.

As falhas de software são mais silenciosas e acontecem quando alguma suposição do sistema deixa de ser verdade (por algum motivo).

As maneiras de evitar esse problema são:

- pensar bem sobre as suposições e interações do sistema;
- bastante cuidado na parte de testes;
- isolamento de processos;
- permitir que os processos que caem sejam reiniciados;
- medir, monitorar e analisar o comportamento do sistema em produção.

3. **Falhas humanas**

Os sistemas são criados por pessoas e todas as pessoas erram eventualmente. Pra evitar ao máximo que isso aconteça existem algumas formas:

- Desenhar sistemas de uma forma que minimiza oportunidades pra erro. Por exemplo, desenhar abstrações/APIs/interfaces que facilitam fazer a coisa certa e desencoraja o erro. Mas tem a chance de se a interface for muito restritiva, as pessoas podem começar a procurar formas de evitá-la.

- Dissociar os lugares que as pessoas cometem maior parte das falhas dos lugares que podem causar falhas. Fornecer ambientes `sandbox` que as pessoas consigam explorar e experimentar sem qualquer perigo, usando dados reais sem afetar os usuários reais.

- Testar com muito cuidado em todos os níveis, desde testes unitários a testes de integração com outros sistemas, testes automatizados ajudam bastante a cobrir corner-cases, além dos testes manuais.

- Permitir que rollbacks sejam facilmente feitos.

- Monitorar de forma detalhada e clara métricas de perfomance e taxa de erros. Monitoramento pode nos avisar cedo quando alguma coisa tá dando errado e ajuda a diagnosticar o problema.

- Treinamento e gerenciamento de equipe.

# Cache

### Client-side (lado do cliente)

`Client-side` se refere a tudo que mostra ou acontece no lado do cliente (dispositivo do usuário final), geralmente é ligado ao `frontend` da aplicação. 

Isso inclui tudo o que o usuário vê (texto, imagens, toda a interface gráfica) assim como qualquer ação que uma aplicação faz dentro do browser do usuário.

HTML e CSS são interpretados pelo browser no `client-side`. Muitas pessoas engenheiras estão incluindo processos `client-side` na arquitetura da aplicação e não deixando tudo `server-side`. Páginas dinâmicas por exemplo geralmente rodam no cliente numa aplicação web moderna. Esses processos são quase sempre feitos em JavaScript.

Um exemplo de processo `client-side` é quando você passar o mouse em cima (`hover`) de uma série no Netflix: a imagem aumenta e as `thumbnails` (miniaturas de imagens) ao lado diminuem. Isso acontece no `client-side` já que o código que criou esse comportamento roda no browser sem qualquer interação com o servidor.

### Server-side (lado do servidor)

`Server-side` se refere a tudo que acontece no servidor, ao invés do cliente. Antigamente, quase toda a lógica de negócio rodava no `server-side` e isso incluía renderização de páginas dinâmicas, interação com banco de dados, autenticação de identidade e notificações push.

O lado negativo de fazer todos os processos `server-side` é que cada request tem que passar do `client-side` para o servidor, toda vez. Isso faz com que a latência aumente. Por isso, aplicações mais modernas rodam bastante código no `client-side`.

`Backend` é o termo geralmente ligado ao `server-side`, mas é importante lembrar que `backend` é o tipo de processo e `server-side` é onde os processos rodam.

[**Fonte**](https://www.cloudflare.com/learning/serverless/glossary/client-side-vs-server-side/)

# Autenticação

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

A solução pra esse problema do usuário ter que relogar várias vezes enquanto usa a aplicação é a `atualização de tokens`. Um token atualizado possui um período maior pra expirar e não passa pelo client-side, é armazenado num banco de dados. O banco de dados que possui esses tokens deve possuir todas as medidas de segurança pra protegê-los. Caso alguém consiga invadir esse banco de dados, é uma falha de segurança gravíssima.

Quando o token atualizado expira, aí realmente o usuário precisa logar novamente.

**Fonte**: [aqui](https://livecodestream.dev/post/2020-07-31-a-brief-introduction-to-securing-applications-with-jwt/)

