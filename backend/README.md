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

## Chat em tempo real e instalação via bot

O frontend agora espera estes endpoints (ainda precisam de implementação no backend):

- `GET /api/chat/messages?serverId=...`: devolver `{ "messages": [{ "id": "msg-id", "sender": "bot|user|admin", "text": "texto", "attachment": { "name": "ficheiro.jar" } }] }`. Exigir autenticação e autorização do utilizador para aquele servidor. Usar cursor/ID para paginação em produção.
- `POST /api/chat/messages`: multipart/form-data com `serverId`, `text` e, opcionalmente, `attachment`. Guardar a mensagem e colocar o ficheiro numa fila de validação. Responder com `{ "message": { ... } }`.
- O frontend consulta as mensagens de 4 em 4 segundos. Para experiência instantânea em produção, preferir WebSocket ou Server-Sent Events com autenticação.

### Fluxo seguro de plugins/mods pelo chat

1. Verificar que a conta autenticada é proprietária/operadora do servidor indicado.
2. Guardar o upload em quarentena, com limite de tamanho, nome aleatório, análise antimalware e bloqueio de caminhos perigosos.
3. Inferir/confirmar a categoria: plugin Java (.jar) apenas em Paper/Purpur/Spigot; mod Java (.jar) apenas em Fabric/Forge/NeoForge; Bedrock aceita add-ons/resource/behavior packs (.mcaddon/.mcpack e formatos suportados).
4. Confirmar edição, versão e dependências; não executar o ficheiro no processo do bot nem aceitar comandos arbitrários enviados no chat.
5. Pedir confirmação antes de instalar/reiniciar; criar backup, instalar no diretório correto e registar auditoria.
6. Responder no chat com sucesso ou erro real da operação.

O chat não fica online apenas com GitHub Pages: é obrigatório implementar API, base de dados, autenticação e processo worker. Não afirmar que o ficheiro foi instalado até o worker confirmar a operação.
