# Azure Translator API - Notebook com Gerenciamento Seguro de Credenciais

Sistema robusto para tradução de textos utilizando Azure Translator API, com gerenciamento seguro e flexível de credenciais através de múltiplas fontes.

## 🚀 Características Principais

### ✅ Implementado

- **Sistema de Credenciais Multi-Source**
  - Suporte a Colab Secrets
  - Variáveis de ambiente (Environment Variables)
  - Input do usuário via getpass
  - Ordem de prioridade configurável
  - Validação de existência antes do uso

- **Tratamento Robusto de Erros**
  - Try-catch específicos para cada fonte de credencial
  - Mensagens de erro descritivas
  - Fallback automático entre fontes
  - Validação de valores vazios/nulos

- **Type Hints Completos**
  - Anotações de tipo em todas as funções
  - Uso de dataclasses para estruturas de dados
  - Enums para valores constantes
  - Melhor IDE support e documentação

- **Timeouts e Controles**
  - Timeout configurável para requisições
  - Tratamento de TimeoutException
  - Controle de tentativas (retry logic)

- **Feedback ao Usuário**
  - Mensagens claras sobre origem das credenciais
  - Indicadores visuais (✓, ⚠️, ❌)
  - Preview mascarado de secrets disponíveis
  - Logs informativos de cada etapa

- **Segurança**
  - Uso de `SecretStr` do Pydantic
  - Valores nunca expostos em logs completos
  - Credenciais não armazenadas em memória desnecessariamente

### 🔄 Melhorias Pendentes

- **Gerenciadores de Secrets Cloud**
  - [ ] Azure Key Vault integration
  - [ ] Google Secret Manager integration
  - [ ] AWS Secrets Manager
  - [ ] HashiCorp Vault

- **Cost Balancer**
  - [ ] Monitoramento de uso da API
  - [ ] Controle de custos por execução
  - [ ] Alertas de limite de gastos
  - [ ] Otimização de requests (batching)

- **Melhorias no Fluxo de Tradução**
  - [ ] Sistema de cache para traduções recorrentes
  - [ ] Detecção automática de idioma
  - [ ] Suporte a múltiplos idiomas destino
  - [ ] Processamento em lote (batch translation)
  - [ ] Retry com exponential backoff
  - [ ] Métricas de performance

## 📋 Pré-requisitos

```bash
pip install azure-ai-translation-text
pip install pydantic
pip install python-dotenv  # Opcional, para .env files

# Para Azure Key Vault (futuro)
pip install azure-keyvault-secrets azure-identity

# Para Google Secret Manager (futuro)
pip install google-cloud-secret-manager
```

## 🔧 Configuração

### Opção 1: Colab Secrets (Recomendado para Google Colab)

1. No Google Colab, clique no ícone de chave 🔑 na barra lateral
2. Adicione um novo secret:
   - Nome: `tlt_subscription_key_1`
   - Valor: Sua Azure Translator API Key

### Opção 2: Variáveis de Ambiente

```bash
export AZURE_TRANSLATOR_API_KEY="sua-chave-aqui"
export AZURE_TRANSLATOR_REGION="brazilsouth"  # ou sua região
```

Ou crie um arquivo `.env`:

```env
AZURE_TRANSLATOR_API_KEY=sua-chave-aqui
AZURE_TRANSLATOR_REGION=brazilsouth
```

### Opção 3: Input Interativo

O sistema solicitará a chave via `getpass` se nenhuma fonte anterior estiver disponível.

## 💻 Uso Básico

```python
from azure_translator_manager import get_secret_with_fallback
from pydantic import SecretStr

# Obtém a credencial automaticamente
secret_info = get_secret_with_fallback(
    secret_name="tlt_subscription_key_1",
    env_var_name="AZURE_TRANSLATOR_API_KEY"
)

# Converte para SecretStr (seguro)
api_key = SecretStr(secret_info.value)

# Usa na tradução
# ... seu código de tradução aqui
```

## 📊 Ordem de Prioridade Padrão

1. **Colab Secrets** - Mais seguro em ambiente Colab
2. **Variáveis de Ambiente** - Flexível para diferentes ambientes
3. **User Input** - Fallback interativo

A ordem pode ser customizada conforme necessário.

## 🔍 Verificar Secrets Disponíveis

```python
from azure_translator_manager import show_available_secrets

# Mostra quais secrets existem (com preview mascarado)
available = show_available_secrets(
    secret_name="tlt_subscription_key_1",
    env_var_name="AZURE_TRANSLATOR_API_KEY"
)
```

## 🏗️ Arquitetura

```
┌────────────────────────────────────────┐
│         SecretManager                  │
├────────────────────────────────────────┤
│  Priority Order:                       │
│  1. Colab Secrets      ────────────┐   │
│  2. Environment Vars   ────────┐   │   │
│  3. User Input (getpass) ───┐  │   │   │
│                             │  │   │   │
│  ┌──────────────────────┐   │  │   │   │
│  │  Validation Layer    │ ◄─┴──┴───┴───┤
│  ├──────────────────────┤              │
│  │ - Check existence    │              │
│  │ - Validate non-empty │              │
│  │ - Type safety        │              │
│  └──────────────────────┘              │
│                                        │
│  ┌──────────────────────┐              │
│  │   SecretInfo         │              │
│  ├──────────────────────┤              │
│  │ - value: str         │              │
│  │ - source: Enum       │              │
│  │ - metadata           │              │
│  └──────────────────────┘              │
└────────────────────────────────────────┘
```

## 🛡️ Segurança

- ✅ Credenciais nunca impressas em logs completos
- ✅ Uso de `SecretStr` para proteção em memória
- ✅ Preview mascarado quando necessário
- ✅ Validação de entrada antes do uso
- ✅ Timeout para operações de I/O
- ✅ Tratamento específico de exceções

## 📝 Exemplos Avançados

### Forçar Input e Atualizar Environment

```python
secret_info = get_secret_with_fallback(
    secret_name="tlt_subscription_key_1",
    env_var_name="AZURE_TRANSLATOR_API_KEY",
    force_input=True,          # Ignora valores existentes
    update_target="env"        # Salva em os.environ
)
```

### Workflow Interativo Completo

```python
from azure_translator_manager import configure_azure_translator

# Workflow guiado
api_key = configure_azure_translator()
```

## 🧪 Testes

```python
# TODO: Adicionar suite de testes
# - Test fallback chain
# - Test validation
# - Test error handling
# - Mock external services
```

## 🤝 Contribuindo

Contribuições são bem-vindas! Áreas de interesse:

1. Implementação dos gerenciadores cloud (Azure KV, Google SM)
2. Sistema de cost balancing
3. Cache de traduções
4. Testes unitários e de integração
5. Documentação adicional

## 📄 Licença

Apache-2.0

## 👤 Autor

@edsmcosta

## 📚 Documentação Relacionada

- [Azure Translator Documentation](https://docs.microsoft.com/azure/cognitive-services/translator/)
- [Google Colab Secrets](https://colab.research.google.com/notebooks/snippets/secrets.ipynb)
- [Pydantic SecretStr](https://docs.pydantic.dev/latest/api/types/#pydantic.types.SecretStr)
- [Langchain Azure OpenAI](https://docs.langchain.com/oss/python/integrations/chat/azure_chat_openai)
  
## 🐛 Issues Conhecidos

Nenhum no momento. Reporte problemas na aba Issues do GitHub.

## 📈 Roadmap

### v1.0 (Atual)
- ✅ Sistema básico de credenciais
- ✅ Suporte Colab + Env + Input
- ✅ Type hints completos
- ✅ Tratamento de erros

### v2.0 (Próximo)
- [ ] Azure Key Vault
- [ ] Google Secret Manager
- [ ] Cost balancer básico
- [ ] Cache de traduções

### v3.0 (Futuro)
- [ ] AWS Secrets Manager
- [ ] Dashboard de métricas
- [ ] API REST wrapper
- [ ] CLI tool

---
