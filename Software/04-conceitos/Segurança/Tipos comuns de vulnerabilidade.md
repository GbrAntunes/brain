Verificar no OWASP #10 as principais voltadas para web e vibe coding

# CSRF
## Cross-site Request Forgery
Um usuário logado em, por exemplo, https://banco.com ao clicar em um link malicioso, pode ter seu cookie de sessão utilizado em uma requisição maliciosa.
Essa vulnerabilidade explora o mecanismo padrão do navegador, que anexa automaticamente os cookies de banco.com a qualquer requisição destinada a essa url. Portanto, se um site promo-gratis.com tiver um form com action para banco.com, os cookies de banco.com serão enviados automaticamente.
É exatamente esse mecanismo que o atributo `SameSite` evita., ele faz com que o navegador deixe de anexar o cookie quando a requisição parte de outro site.

### Como se proteger
#### Tokens Anti-CSRF
O servidor gera um token único, secreto e temporário para a sessão do usuário. Em cada requisição que altere dados, esse token é enviado e validado. Como o site do atacante não tem acesso a esse token (pois fica armazenado na página, num campo oculto ou meta-tag), o servidor, ao comparar o token enviado e o token configurado, rejeita a requisição.
#### Atributo SameSite
Configuração dos cookies de sesão `SameSite=Strict` ou `SameSite=Lax` impede que o navegador envie o cookie em requisições que partem de sites de terceiros, que é justamente a mecânica do CSRF

# IDOR
## Insecure Direct Object Reference
Falha de segurança em que é possível acessar dados de entidades do sistema através de seu identificador.
Exemplos de falhas IDOR:
- Acessar diretamente informações de outros usuários passando seu identificador em uma rota GET `/users/:id`
- Acessar pedidos de outros clientes
- Acessar ou baixar documentos de outros usuários
- Alterar dados de outros usuários
### Como se proteger
#### Validação no backend
Antes de aceitar a solicitação, verifique se o usuário que está requisitando está autenticado e se ele possui permissão para acessar aquele dado. Sobre permissão, podemos validar sua role ou sua propriedade sobre aquele dado.
#### Identificadores aleatórios
Não evita o IDOR, mas ajuda a tornar o acesso um pouco mais difícil. Identificadores sequenciais quase sempre são má ideia pois fornece ao atacante uma área de ataque mais ampla. Se eu me autentico e recebo o ID 53, então eu imagino que exista um usuário 52, um 51, um 50, etc.
IDs aleatórios - como o UUID - não deixam essa sugestão para o atacante.

# Broken Access Control
Top #1 no ranking OWASP TOP 10, o BAC acontece quando as permissões dos usuários não estão bem definidas. É como permitir que um paciente acesse qualquer setor de um hospital sem que os seguranças o impeçam. Como exemplos, podemos citar:
- Acesso direto a URLs restritos como `/admin/dashboard`
- Alterar dados que não seriam permitidos como um user alterando a própria role
- Interface que esconde links devido a permissão do usuário mas o servidor não valida aquela ação
### Como se proteger
#### Validação no servidor
- O backend deve validar todas as requisições, ainda que o front tenha "escondido a opção"
- Além disso, podemos também trabalhar com princípios de menor privilégio, ou seja, fornecer acessos diante da necessidade da aplicação, e não o contrário, bloqueando a medida em que a aplicação vive. Usar allowlist e não blocklist.
- Utilizar modelos maduros de autenticação como o RBAC (role-based access control) ou o ABAC (attribute-based access control) que definem grupos de permissões e esses grupos são atribuidos aos usuários.
- Ter rotinas de testes de segurança como SAST, DAST e Pentest na aplicação.

# Sequestro de Cookies de sessão
Falha de segurança em que um invasor consegue acesso ao cookie de sessão de um usuário e pode se passar por ele enquanto interage com a aplicação. Como funciona:
1. Em uma autenticação, o token de sessão do usuário é armazenado em um cookie para que não seja necessário digitar a senha todas as vezes;
2. O invasor obtém esse cookie através de diferentes vetores como malwares do tipo infostealer, falhas de XSS ou redes WIFI inseguras como redes públicas;
3. Uma vez que o invasor tem o cookie em mãos, ele injeta esse cookie no próprio navegador e acessa a plataforma como o dono daquela sessão sem precisar de senha ou códigos, pois o sistema acredita que a sessão está validada.
### Como se proteger
Boa parte das recomendações são mais para os usuários do que para a aplicação, tais como:
- Evitar redes públicas
- Manter o dispositivo (celular ou computador) atualizados
- Utilizar navegadores maduros como o Chrome ou Firefox

Para os desenvolvedores, o ideal é disponibilizar autenticação em dois fatores (2FA) na aplicação e se possível, utilizar tecnologias modernas de defesa como o **Device Bound Session Credentials (DBSC)** que atrela a sessão ao hardware do dispositivo.

# Cross-site Scripting
O XSS é uma vulnerabilidade que permite que códigos maliciosos (JS na maior parte dos casos) seja injetado em aplicações web confiáveis e executado no navegador de outros usuários. Os riscos incluem roubo de cookies de sessão, envio de informações sigilosas para a base do atacante entre outros.

## XSS Refletido (não persistente)
O script é injetado via requisição HTTP (como um parâmetro em uma URL) e é devolvido imediatamente pelo servidor na resposta para a vítima. Um exemplo seria um sistema que recebe um nome como parâmetro e exibe o nome que recebeu, se o nome contém código malicioso, ele será processado pela aplicação.

## XSS Armazenado (persistente)
O código malicioso é gravado permanentemente no servidor, em comentários, fóruns ou perfis e é executado automaticamente para qualquer pessoa que abrir a página.

## XSS Baseado em DOM
A vulnerabilidade acontece inteiramente no lado do cliente, quando o JS da página lê dados da URL ou de elementos da página de forma insegura e os manipula.

### Como se proteger
