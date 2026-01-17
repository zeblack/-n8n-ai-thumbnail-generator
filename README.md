# n8n AI Thumbnail Generator

Gerador automatizado de capas/thumbnails cinematográficas usando IA (Gemini 2.5 Flash), com suporte a múltiplos estilos artísticos e integração de imagens de referência.

## 📋 Descrição

Sistema completo para geração de thumbnails profissionais estilo Netflix/HBO usando Inteligência Artificial. O workflow processa requisições via webhook, analisa parâmetros, gera prompts otimizados e cria 2 variações de capas em alta qualidade.

## ✨ Funcionalidades

### 🎨 Estilos Artísticos Disponíveis

- **Hiper-Realista**: Renderização fotorrealística com qualidade 8K
- **Anime**: Estilo manga/anime com cores vibrantes
- **Minimalista**: Design limpo com formas geométricas
- **Futurista**: Estética sci-fi com luzes neon
- **Vintage**: Retro nostálgico com texturas envelhecidas
- **Cyberpunk**: Distópico urbano com high-tech

### 🌈 Paletas de Cores por Categoria

| Categoria | Background | Accent | Mood |
|-----------|------------|--------|------|
| **Programação** | Deep navy blue | Electric cyan | Tech noir |
| **Design** | Rich purple gradient | Vibrant magenta | Creative luxe |
| **Marketing** | Warm charcoal | Energetic orange | Dynamic bold |
| **Business** | Elegant black | Premium gold | Executive luxury |
| **Saúde** | Fresh teal gradient | Vibrant green | Vital energy |
| **Educação** | Sophisticated slate | Sunset orange | Inspiring warmth |
| **Tecnologia** | Cyber black | Neon red | Futuristic edge |

### 🖼️ Recursos Avançados

- **Geração Dupla**: Cria 2 variações por requisição
- **Resoluções Customizadas**: Suporte a múltiplos formatos (16:9, 1:1, 9:16)
- **Integração de Referências**: Aceita até 3 imagens de referência
- **Download Automático**: Processa URLs de imagens
- **Preservação de Rostos**: Mantém pessoas exatamente como nas referências
- **Variação Automática**: Segunda imagem com estilo diferente

## 🚀 Requisitos

- n8n instalado (self-hosted ou cloud)
- API Key do OpenRouter (para GPT-5-mini)
- API Key do Google Gemini 2.5 Flash (para geração de imagens)

## 📦 Instalação

1. **Importar o Workflow**
   ```bash
   # No n8n, vá em Workflows > Import from File
   # Selecione o arquivo workflow.json
   ```

2. **Configurar Credenciais**
   
   Atualize as seguintes API keys no workflow:
   
   - **OpenRouter API Key**: No nó "HTTP Request3" (header Authorization)
   - **Google Gemini API Key**: No nó "Gemini RAG Search" (query parameter)

3. **Obter API Keys**
   
   - OpenRouter: https://openrouter.ai/keys
   - Google Gemini: https://ai.google.dev/

4. **Ativar o Webhook**
   
   - Copie a URL do webhook do nó "Webhook"
   - Use esta URL para fazer requisições POST

## 🎯 Como Usar

### Requisição via Webhook

Faça um POST para a URL do webhook com o seguinte JSON:

```json
{
  "title": "Curso Completo de Python",
  "description": "Aprenda Python do zero com projetos práticos",
  "category": "Programação",
  "resolutions": "1280x720",
  "styleCategory": "Hiper-Realista",
  "userMessage": "Quero uma capa moderna e chamativa",
  "userReferenceImages": [
    "https://exemplo.com/imagem1.jpg",
    "https://exemplo.com/imagem2.jpg"
  ]
}
```

### Parâmetros

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `title` | string | ✅ | Título do curso (será simplificado) |
| `description` | string | ❌ | Descrição para contexto do design |
| `category` | string | ✅ | Categoria (define paleta de cores) |
| `resolutions` | string | ✅ | Resolução (ex: "1920x1080", "1280x720") |
| `styleCategory` | string | ❌ | Estilo artístico |
| `userMessage` | string | ❌ | Instruções específicas |
| `userReferenceImages` | array | ❌ | URLs de até 3 imagens de referência |

### Resoluções Suportadas

- `1920x1080` (16:9) - Full HD
- `1280x720` (16:9) - HD
- `1080x1080` (1:1) - Quadrado
- `1080x1350` (4:5) - Instagram
- `1080x1920` (9:16) - Stories

### Resposta

O workflow retorna um JSON com as imagens geradas:

```json
{
  "success": true,
  "count": 2,
  "images": [
    {
      "id": 1,
      "fileName": "thumbnail-1.png",
      "mimeType": "image/png",
      "dataUri": "data:image/png;base64,...",
      "sizeEstimateKB": 450
    },
    {
      "id": 2,
      "fileName": "thumbnail-2.png",
      "mimeType": "image/png",
      "dataUri": "data:image/png;base64,...",
      "sizeEstimateKB": 480
    }
  ],
  "metadata": {
    "totalRequests": 2,
    "imagesExtracted": 2,
    "totalSizeKB": 930,
    "timestamp": "2026-01-17T15:00:00.000Z"
  }
}
```

## 📊 Fluxo de Processamento

```
Webhook (recebe requisição)
    ↓
AI Agent (analisa e cria prompt otimizado)
    ↓
Code1 (extrai JSON do AI)
    ↓
Gera o body + url (cria 2 requisições)
    ↓
Explode refs (separa imagens de referência)
    ↓
If (verifica se precisa download)
    ↓
HTTP Request (baixa imagens de referência)
    ↓
Agrupa & injeta no body (monta requisição final)
    ↓
HTTP Request3 (chama Gemini 2.5 Flash) [2x]
    ↓
Code4 (extrai imagens base64)
    ↓
EXTRAI DADOS DE USO DAS APIS (formata resposta)
    ↓
Respond to Webhook (retorna JSON)
```

## ⚙️ Configuração Avançada

### Personalizar Prompts

Edite o nó "AI Agent" para ajustar:
- Estrutura do prompt base
- Descrições de estilos
- Paletas de cores
- Instruções de tipografia

### Adicionar Novo Estilo

No nó "Gera o body + url", adicione ao objeto `STYLE_DEFINITIONS`:

```javascript
'Meu Estilo': {
  description: 'Descrição do estilo',
  keywords: 'palavras-chave, características',
  mood: 'atmosfera desejada'
}
```

### Adicionar Nova Categoria

No objeto `PALETTES`:

```javascript
'Minha Categoria': {
  bg: 'cor de fundo',
  accent: 'cor de destaque',
  mood: 'atmosfera'
}
```

### Ajustar Número de Imagens

No nó "Gera o body + url", altere:

```javascript
const CONFIG = {
  imageCount: 3, // Gera 3 imagens ao invés de 2
  // ...
};
```

## 🔍 Debug e Logs

O workflow inclui logs detalhados em cada etapa:

- **Code4**: Extração de imagens com contadores
- **Explode refs**: Validação de índices
- **Agrupa & injeta**: Merge de referências

Monitore execuções no n8n para troubleshooting.

## 🐛 Troubleshooting

### Nenhuma imagem gerada
- Verifique se API keys estão corretas
- Confirme que modelo Gemini suporta geração de imagens
- Veja logs do Code4 para erros de extração

### Apenas 1 imagem ao invés de 2
- Verifique logs do "Explode refs"
- Confirme que `imageCount: 2` está configurado
- Veja se ambas requisições foram enviadas ao Gemini

### Imagens de referência não aparecem
- Confirme que URLs são acessíveis
- Verifique logs do "HTTP Request" (download)
- Veja se merge foi feito corretamente no "Agrupa & injeta"

### Estilo não aplicado
- Verifique se `styleCategory` está escrito corretamente
- Confirme que estilo existe em `STYLE_DEFINITIONS`
- Veja logs de detecção de estilo

## 🔒 Segurança

⚠️ **IMPORTANTE**:

- **Nunca** exponha API keys no código
- Implemente rate limiting no webhook
- Valide tamanho de imagens de referência
- Limite número de requisições por IP

## 🤝 Contribuindo

Contribuições são bem-vindas! Áreas para melhoria:
- Adicionar mais estilos artísticos
- Suporte a mais modelos de IA
- Otimização de prompts
- Cache de imagens geradas

## 📄 Licença

MIT License - veja o arquivo LICENSE para detalhes.

## 🆘 Suporte

- [Documentação n8n](https://docs.n8n.io/)
- [OpenRouter Docs](https://openrouter.ai/docs)
- [Google Gemini API](https://ai.google.dev/docs)

---

**Desenvolvido com ❤️ usando n8n e IA**
