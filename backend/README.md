# Backend necessário para o CraftHost

O frontend está preparado para estas rotas, mas **estas rotas ainda não estão implementadas neste repositório**. Não publiques o serviço como se já criasse servidores reais.

## API esperada

- `POST /api/servers`: validar pedido e provisionar servidor num painel de alojamento. Recebe `edition=java|bedrock`, versão, software, jogadores, email e `plan=free`. Responde com `serverId`.
- `POST /api/servers/:id/files`: autenticar o dono, validar extensão/tamanho/malware/compatibilidade, instalar no diretório correto.
- `POST /api/admin/login`: autenticação do administrador com cookie HttpOnly, Secure e SameSite.
- `POST /api/admin/coupons`: apenas admin; guardar hash do cupão, duração e limite de utilizações na base de dados. Nunca guardar o código no HTML público.
- `POST /api/bot/redeem`: validar cupão no servidor, limitar tentativas, controlar utilizações e atribuir o plano pela duração configurada (730 dias = 2 anos).
- `POST /api/admin/logout`: invalidar sessão.

## Regras de segurança e funcionamento

1. O administrador introduz o código no painel privado do bot. O código fica apenas no servidor (idealmente armazenado como hash), nunca em `index.html`, JavaScript público, APK ou GitHub.
2. `bot-admin.html` é uma página separada, mas não fica protegida só por estar fora do menu. A API deve exigir sessão de administrador em todas as rotas privadas.
3. O plano gratuito aceita pedidos sem checkout. O funcionamento real depende de um fornecedor/painel de alojamento, recursos e limites.
4. Java: Paper/Purpur para plugins; Fabric/Forge/NeoForge para mods. Bedrock usa Bedrock Dedicated Server e add-ons, não plugins Java.
5. Validar ficheiros enviados, limitar tamanho, analisar malware e confirmar compatibilidade antes de instalar.
6. GitHub Pages publica páginas estáticas; não aloja servidores Minecraft nem executa esta API. Para 24/7 é necessária infraestrutura de servidor/cloud.
