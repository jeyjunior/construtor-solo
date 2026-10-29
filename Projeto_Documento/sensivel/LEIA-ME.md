# Projeto_Documento/sensivel

Chaves, certificados (.pfx, .pem, .key), credenciais reais, connection strings de produção e documentos sensíveis.

- Nunca copie nada daqui para `Projeto/`. O sistema lê segredos em tempo de execução: variável de ambiente, user-secrets ou cofre de segredos.
- O Claude não lê nem grava nesta pasta (bloqueado em `.claude/settings.json`). Se ele precisar de um valor, informe só o nome da variável onde ele vai estar.
- Esta pasta não entra em git, como todo o `Projeto_Documento/`.
