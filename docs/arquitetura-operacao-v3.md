# LLM IA | Core Multiprovider Textual v3.0

## Estado da versão

- Workflow publicado no n8n: **LLM IA | Core Multiprovider Textual v3.0**.
- Validação operacional informada em 25/11/2026: houve execuções com Gemini e execuções que desviaram para Cloudflare Workers AI, confirmando a troca de provedor.
- O export multiprovider e o workflow Cloudflare de referência estão versionados neste repositório.

## Objetivo

Manter o Gemini como provedor primário do Core Editorial M360 e usar Cloudflare Workers AI como contingência para falhas elegíveis, preservando o contrato de saída consumido pelos workflows editoriais.

## Caminho de processamento

1. **Entrada e preparação** — recebe `MSG_Prompt_System` e `MSG_Prompt_User`, seleciona a configuração Gemini no catálogo e prepara a chamada.
2. **Gemini** — envia a requisição pelo proxy existente. O node de interpretação lê a resposta e marca sucesso ou falha, incluindo resposta vazia ou inválida.
3. **Sucesso Gemini** — normaliza a resposta e retorna ao fluxo editorial.
4. **Falha Gemini** — abre dois caminhos:
   - envia o alerta de monitoramento;
   - congela temporariamente a configuração Gemini, atualiza o contador e encaminha o processamento para a seleção da configuração Cloudflare.
5. **Cloudflare Workers AI** — prepara a chamada usando a configuração selecionada no MySQL, envia o prompt e interpreta a resposta.
6. **Sucesso Cloudflare** — normaliza a resposta e retorna ao mesmo destino editorial usado pelo Gemini.
7. **Falha Cloudflare** — segue a condição de retry configurada no workflow. A nova tentativa de selecionar Gemini ocorre somente após falha na ramificação de contingência Cloudflare.

## Alerta de fallback

O node `Alerta | Monitora` é acionado quando a saída do Gemini indica falha. O alerta inclui modelo, status HTTP, motivo da falha, horário e ID da execução. O erro é escapado antes de ser inserido no HTML do e-mail.

O node de e-mail está configurado para continuar o workflow em caso de falha do próprio envio. A contingência segue pelo caminho de processamento do workflow, sem depender da saída do Gmail.

## Contrato de saída

O sucesso de qualquer provedor retorna um item com a propriedade `text`, mantendo os metadados:

```json
{
  "text": "Resultado textual processado",
  "modelo": "identificador-do-modelo",
  "tokens": null,
  "provedor": "gemini-ou-cloudflare"
}
```

O campo `provedor` identifica qual serviço concluiu a chamada. O campo `tokens` pode ser nulo se o provedor não informar a contagem.

## Critérios de validação já observados

- Execuções Gemini sem falha seguem o caminho normal.
- Falhas Gemini elegíveis acionam a ramificação Cloudflare.
- Respostas Cloudflare bem-sucedidas chegam ao normalizador compartilhado.
- A saída Cloudflare observada preservou `text`, `modelo`, `tokens` e `provedor: "cloudflare"`.

## Configuração segura do proxy Gemini

O export de produção tinha um valor fixo no header `X-Proxy-Key`. Esse valor foi removido da cópia versionada e substituído por `={{ $env.M360_GEMINI_PROXY_KEY }}`.

Antes de importar ou executar o JSON versionado, configure `M360_GEMINI_PROXY_KEY` no ambiente seguro do n8n e confirme que o ambiente permite acesso à variável em expressões. Nunca copie a chave para o workflow, para esta documentação ou para o GitHub.

O export Cloudflare de referência utiliza a expressão de autenticação existente e a seleção da chave via MySQL; não versionar credenciais, chaves, tokens ou senhas.

## Acompanhamento operacional

Ao observar uma execução de fallback, conferir:

- causa e status da falha Gemini;
- bloqueio temporário da configuração Gemini;
- confirmação de que a seleção Cloudflare ocorreu depois do erro Gemini;
- provedor e modelo na saída normalizada;
- entrega do alerta e ID da execução;
- se houver falha Cloudflare, comportamento do contador e condição de retorno para nova seleção Gemini;
- estrutura HTML final nas etapas editoriais de título, conteúdo e fiscalização HTML.

Não executar rotação de contas para contornar limites do provedor.

## Arquivos de referência

- Export multiprovider: [LLM IA _ Core Multiprovider Textual v3.0.json](../LLM%20IA%20_%20Core%20Multiprovider%20Textual%20v3.0.json).
- Workflow Cloudflare de referência: [LLM IA Cloudflare _ Workers AI Textual 24-11-2026.json](../references/LLM%20IA%20Cloudflare%20_%20Workers%20AI%20Textual%2024-11-2026.json).

Atualizar esta documentação se houver mudanças de nodes, conexões, política de retry, configuração de provedor ou contrato de saída.
