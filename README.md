# CraftHost

Aplicação web instalável (PWA) para painel de pedidos de alojamento Minecraft.

## Publicar no GitHub Pages
O workflow `.github/workflows/pages.yml` publica a pasta raiz quando há push para `main`. Em **Settings → Pages**, confirma que a origem está definida como **GitHub Actions**.

## Instalar como app
Depois da publicação, abre o endereço HTTPS do GitHub Pages no Chrome e escolhe **Instalar CraftHost** ou **Adicionar ao ecrã inicial**.

## Limitações desta versão
Esta versão é apenas frontend demonstrativo. Não cria servidores Minecraft reais, não recebe pagamentos, não envia comprovativos ao administrador e não verifica transferências. Para produção, é necessário adicionar backend/API, base de dados, autenticação, alojamento de servidores e pagamentos por um processador com confirmação via webhook. Nunca aprovar pagamentos apenas pela imagem do comprovativo.

Email de contacto informado: luisotavionobregafermamdes157@gmail.com
