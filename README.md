# LLM IA | Core Multiprovider Textual v3.0

Workflow n8n do Core Editorial M360 com Gemini como provedor primário e Cloudflare Workers AI como contingência.

A validação operacional confirmou execuções pelo Gemini e o desvio para Cloudflare em falhas do provedor principal. O caminho de sucesso de ambos retorna ao normalizador compartilhado e mantém a propriedade `text` e os metadados de provedor e modelo.

## Documentação

- [Arquitetura, comportamento e acompanhamento operacional](docs/arquitetura-operacao-v3.md)

## Exportação do workflow

Arquivo esperado: `LLM IA _ Core Multiprovider Textual v3.0.json`.

A exportação JSON ainda precisa ser adicionada. Não versionar credenciais, chaves, tokens ou senhas junto ao workflow.
