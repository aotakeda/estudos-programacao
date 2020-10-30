# Estudos Programação

Notas sobre o que estou estudando, em ordem aleatória.

# Designing Data-Intensive Applications

### Reliability (Confiabilidade)

Alguns dos fatores para um software ser considerado `confiável` (reliable) são:

- A aplicação performa sua função como o usuário espera;
- Consegue tolerar que o usuário cometa erros ou use o software de maneiras inesperadas;
- A performance é boa o suficiente pro caso de uso, suportando o volume esperado de dados;
- O sistema previne qualquer abuso ou acesso não-autorizado.

Resumindo, significa que o software continua a funcionar corretamente mesmo quando as coisas dão errado.

Essas coisas que podem dar errado são as chamadas `falhas` (faults), e sistemas que conseguem se antecipar a essas falhas são chamados de `tolerante a falhas` ou `resilientes`. É importante destacar que essas falhas não significam que o sistema pare de proporcionar o serviço ao usuário e que nenhum sistema consegue ser tolerante a todos os tipos de falhas, mas sim a alguns. Muitas dessas falhas são causadas por má gestão de erros.

Nesse quesito é importante aumentar a taxa de falhas de propósito, assim você garante que o sistema é tolerante a falhas e aumenta sua confiança que serão gerenciadas de forma correta quando ocorrerem de fato. [Ver Chaos Monkey](https://github.com/Netflix/chaosmonkey).

Tipos de falhas mais comuns são:

1. **Falha de hardware**

Exemplos: HD queimou, RAM não está funcionando direito.

Como mitigar: adicionar redundância nos componentes individuais pra reduzir a taxa de falhas no sistema.

Outra maneira de mitigar essas falhas são, adicionalmente ao aumento de redundância, utilizar técnicas de tolerância a falhas como `rolling upgrade` (fazer um patch de segurança, por exemplo, sem ser necessário o downtime).

2. **Falha de software**

Esse tipo de falha causa muito mais problemas do que as de hardware (que geralmente são pontuais) porque são menos previsíveis e por ter potencial muito maior de causar falha total nos sistemas. Um exemplo de falha de software é de um serviço que o sistema depende começa a ficar mais lento ou para de retornar o que é esperado, ou falhas em cascatas que uma pequena falha em um componente faz acontecer outra em outro componente e vira uma bola de neve.

As falhas de software são mais silenciosas e acontecem quando alguma suposição do sistema deixa de ser verdade (por algum motivo).

As maneiras de evitar esse problema são:

- pensar bem sobre as suposições e interações do sistema;
- bastante cuidado na parte de testes;
- isolamento de processos;
- permitir que os processos que caem sejam reiniciados;
- medir, monitorar e analisar o comportamento do sistema em produção.

3. **Falhas humanas**

Os sistemas são criados por pessoas e todas erram eventualmente. Pra evitar ao máximo que isso aconteça existem algumas formas:

- Desenhar sistemas de uma forma que minimize oportunidades pra erro. Por exemplo, desenhar abstrações/APIs/interfaces que facilitem fazer a coisa certa e desencoraje o erro. Mas tem a chance de se a interface for muito restritiva, as pessoas podem começar a procurar formas de evitá-la.

- Dissociar os lugares que as pessoas cometem a maior parte das falhas dos lugares que possam causá-las. Fornecer ambientes `sandbox` que as pessoas consigam explorar e experimentar sem qualquer perigo, usando dados reais sem afetar os usuários reais.

- Testar com muito cuidado em todos os níveis, desde testes unitários a testes de integração com outros sistemas, testes automatizados ajudam bastante a cobrir corner-cases, além dos testes manuais.

- Permitir que rollbacks sejam facilmente feitos.

- Monitorar de forma detalhada e clara métricas de perfomance e taxa de erros. Monitoramento pode nos avisar cedo quando alguma coisa tá dando errado e ajuda a diagnosticar o problema.

- Treinamento e gerenciamento de equipe.

### Escalabilidade

Mesmo o sistema sendo confiável hoje não quer dizer que ele será amanhã. A razão da degradação do sistema é um aumento de carga, digamos que o sistema antes tinha 10000 usuários concorrentes e agora possui 500000. Isso é um problema de escalabilidade.

Escalabilidade é o termo usado pra descrever a capacidade do sistema de suportar uma carga cada vez maior. Não faz sentido discutir se um sistema X é escalável e o Y não e sim fazer perguntas do tipo "se o sistema crescer de tal forma, quais são as opções pra aguentar o crescimento?", "como podemos adicionar recursos pra suportar a carga adicional?".

**Carga**

Carga pode ser descrita de forma numérica com base em alguns `parâmetros` dependendo do contexto da arquitetura do sistema. Por exemplo, se for um servidor web, esse parâmetro pode ser o número de requests por segundo, num banco de dados a taxa de leitura/escrita, número de usuários simultâneos num chat, a taxa de hit num cache...

**Performance**

A partir do momento que você definiu a carga no sistema, precisamos definir como seria a questão de performance.

Em sistemas de processamento em batches como Hadoop, o primeiro parâmetro seria `throughput` (número de registros processados por segundo ou o tempo que leva pra rodar um job num dataset de tal tamanho). Já em sistemas online, geralmente a performance é medida pelo tempo de resposta do sistema, ou seja, quanto tempo demora no intervalo entre o cliente enviar um request e receber uma resposta.

Mesmo fazendo o mesmo request várias vezes, o tempo de resposta vai sempre variar um pouco. Então, é necessário pensar não como um número único mas sim uma `distribuição` dos valores (outliers que demoram mais são porque processam mais dados?).

A média pode ser uma forma de medir o tempo de resposta esperado mas é melhor utilizar percentis ou medianas (50% demoram Y, os outros 50% demoram Z).

Para verificar quão ruim estão os outlier você pode olhar os percentis mais altos como 95 (p95), 99 (p99), 99.9 (p999). Por exemplo, se em p95 o tempo de resposta é de 1.5 segundos, isso significa que de 95 requests entre 100 duram menos que isso e 5 de 100 duram 1.5s ou mais.

Esses percentis altos de tempo de resposta são conhecidos como `tail latencies`.

**Latência e tempo de resposta**

Geralmente, são usados como sinônimos, mas não são. O tempo de resposta é o que o cliente "enxerga" incluindo o tempo pra processar o request (tempo de serviço) além de incluir atrasos de rede e de filas.

Latência é a duração que um request espera pra ser processado (esperando serviço).

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

