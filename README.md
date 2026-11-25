# LLM IA | Core Multiprovider Textual v3.0

Workflow n8n do Core Editorial M360 com Gemini como provedor primário e Cloudflare Workers AI como contingência.

A validação operacional confirmou execuções pelo Gemini e o desvio para Cloudflare em falhas do provedor principal. O sucesso dos dois provedores retorna ao normalizador compartilhado e preserva `text`, `modelo`, `tokens` e `provedor`.

## Arquivos

- [Export do Core Multiprovider v3.0](LLM%20IA%20_%20Core%20Multiprovider%20Textual%20v3.0.json)
- [Workflow Cloudflare Workers AI de referência](references/LLM%20IA%20Cloudflare%20_%20Workers%20AI%20Textual%2024-11-2026.json)
- [Arquitetura, comportamento e acompanhamento operacional](docs/arquitetura-operacao-v3.md)

## Configuração segura

No export versionado, o valor fixo do header `X-Proxy-Key` foi substituído pela expressão `$env.M360_GEMINI_PROXY_KEY`. Configure essa variável no ambiente seguro do n8n antes de executar o fluxo. Não grave a chave no JSON, na documentação ou no repositório.
