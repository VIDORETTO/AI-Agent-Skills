![Skills](https://img.shields.io/badge/Skills-AI%20Agents-blue)
![Version](https://img.shields.io/badge/version-2.0.0-green)
![License](https://img.shields.io/badge/license-MIT-orange)
![Platform](https://img.shields.io/badge/platform-Codex%20%7C%20Claude%20Code%20%7C%20Antigravity-purple)

# 🚀 AI Skills Collection

Uma coleção premium de **skills especializadas** para agentes de IA, projetadas para transformar interações genéricas em execuções profissionais e de alta qualidade.

## 📖 Sobre o Projeto

Este repositório contém skills cuidadosamente elaboradas para agentes de IA como Cline, Claude Code, Antigravity e outros. Cada skill encapsula conhecimento especializado, melhores práticas e workflows estruturados que transformam prompts simples em resultados profissionais.

### 💡 Por que usar Skills?

- **Consistência**: Mesmos resultados independentemente da complexidade do prompt
- **Qualidade**: Cada skill segue padrões profissionais e best practices
- **Eficiência**: Reduz o número de interações necessárias para obter resultados
- **Especialização**: Skills específicas para domínios específicos

## 🎯 Skills Disponíveis

### 🌐 Landing Page & Site Institucional Builder
Cria landing pages e sites institucionais completos, incluindo brief consolidado, estratégia de mensagem, arquitetura, copy completo, design system e QA.

**Uso típico:**
- Landing pages de alta conversão
- Sites institucionais e corporativos
- Páginas de vendas e captura de leads
- Portfólios de serviços

**Entregáveis:**
- Brief consolidado
- Estratégia de mensagem
- Arquitetura de páginas
- Copy completo por seção
- Design system (paleta, tipografia, componentes)
- Código (HTML/CSS ou React/Next)
- QA com rubrica 0-5

### 🛡️ VulnerabilityHunter
Realiza auditorias de segurança completas como red teamer/pentester ético. Analisa código, configurações e dependências para encontrar vulnerabilidades antes que atacantes reais encontrem.

**Uso típico:**
- Auditorias de segurança
- Pentest e red teaming
- Análise de código seguro
- Hardening de aplicações
- Bug bounty hunting

**Entregáveis:**
- Relatório completo ordenado por severidade
- PoC para cada vulnerabilidade
- Correções sugeridas
- Checklist OWASP Top 10
- Análise de ameaças

### 🔍 Abuse Pattern Detector
Detecta padrões de abuso e comportamentos maliciosos em código e aplicações. Identifica potenciais vulnerabilidades de segurança e anti-patterns.

**Uso típico:**
- Análise de código para padrões suspeitos
- Detecção de abuso em APIs
- Revisão de lógica de autenticação
- Identificação de anti-patterns

### 📊 Optimized Effective RAG
Implementa sistemas de Retrieval-Augmented Generation otimizados, incluindo multimodal, eval harness e técnicas avançadas de chunking.

**Uso típico:**
- Sistemas RAG para chatbots
- Busca semântica
- QA com contexto
- Assistentes de conhecimento

## 🔧 Como Funciona

Cada skill segue um padrão estruturado:

```yaml
---
name: skill-name
description: >
  Descrição detalhada de quando usar esta skill
metadata:
  version: "1.0.0"
  owner: "user"
---

# Skill Title

## Processo de Execução
1. Passo 1: Descrição
2. Passo 2: Descrição
3. ...

## Referências
- Arquivos complementares quando necessário
```

### Estrutura de uma Skill

```
skill-name/
├── SKILL.md              # Arquivo principal da skill
└── references/           # Documentação de apoio
    ├── checklist.md      # Checklists específicos
    ├── template.md       # Templates de documentos
    └── guide.md          # Guias de implementação
```

## 📦 Instalação

### Claude Code

1. Clone o repositório:
```bash
git clone https://github.com/VIDORETTO/AI-Agent-Skills.git ~/.claude-code/skills
```

2. Ou copie skills específicas para a pasta de skills do Claude Code:
```bash
cp -r skills/landing-page-builder ~/.claude-code/skills/
```

3. Reinicie o Claude Code

4. Ative a skill no chat:
```
/activate landing-site-builder
```

### Cline (VS Code Extension)

1. Clone o repositório:
```bash
git clone https://github.com/VIDORETTO/AI-Agent-Skills.git ~/cline-skills
```

2. Configure o Cline para usar a pasta:
- Abra Configurações no VS Code
- Procure por "cline.skillsPath"
- Defina o caminho para `~/cline-skills`

3. Reinicie o VS Code

4. Use no chat:
```
@landing-site-builder
```

### Antigravity

1. Clone o repositório:
```bash
git clone https://github.com/VIDORETTO/AI-Agent-Skills.git ~/.antigravity/skills
```

2. Configure no arquivo `~/.antigravity/config.yaml`:
```yaml
skills:
  path: ~/.antigravity/skills
  autoload: true
```

3. Reinicie o Antigravity

4. Ative a skill:
```
/skill landing-site-builder
```

### Continue.dev

1. Clone o repositório:
```bash
git clone https://github.com/VIDORETTO/AI-Agent-Skills.git ~/.continue/skills
```

2. Configure no arquivo `~/.continue/config.json`:
```json
{
  "skillsPath": "~/.continue/skills"
}
```

3. Reinicie a extensão Continue

### Cursor

1. Clone o repositório:
```bash
git clone https://github.com/VIDORETTO/AI-Agent-Skills.git ~/cursor-skills
```

2. No Cursor, vá para Settings → Skills → Add Skill Directory
3. Selecione a pasta `~/cursor-skills`
4. Reinicie o Cursor

## 💼 Exemplos de Uso

### Criando uma Landing Page

```
@landing-site-builder

Quero criar uma landing page para meu curso de programação.
Público-alvo: profissionais que querem migrar para tecnologia.
Objetivo: vender o curso de R$ 997.
Benefício principal: aprender programação em 3 meses.
```

### Auditoria de Segurança

```
@vulnerability-hunter

Analise o projeto atual seguindo o checklist completo.
Gere um relatório detalhado com todas as vulnerabilidades encontradas,
incluindo PoC e correções sugeridas.
```

### Sistema RAG

```
@optimized-effective-rag

Preciso implementar um sistema RAG para meu chatbot de suporte.
A base de conhecimento tem 5000 documentos PDF sobre produtos.
Quero alta precisão e tempo de resposta rápido.
```

## 🎨 Personalização

Você pode criar suas próprias skills seguindo o padrão:

1. Crie uma pasta para sua skill
2. Crie o arquivo `SKILL.md` com o frontmatter YAML
3. Adicione arquivos de referência se necessário
4. Siga a estrutura das skills existentes

**Template básico:**

```yaml
---
name: minha-skill
description: >
  Descrição detalhada de quando usar esta skill
metadata:
  version: "1.0.0"
  owner: "user"
---

# Minha Skill

## Descrição
Explique o que esta skill faz.

## Processo de Execução
1. Passo 1
2. Passo 2
3. Passo 3

## Referências
- Lista de arquivos de apoio
```

## 🤝 Contribuindo

Contribuições são bem-vindas! Por favor:

1. Fork o projeto
2. Crie uma branch para sua feature (`git checkout -b feature/minha-nova-skill`)
3. Commit suas mudanças (`git commit -m 'Adiciona skill XYZ'`)
4. Push para a branch (`git push origin feature/minha-nova-skill`)
5. Abra um Pull Request

**Diretrizes:**
- Siga o padrão de nomenclatura existente
- Inclua exemplos de uso
- Documente bem o processo de execução
- Adicione referências quando necessário

## 📄 Licença

Este projeto está licenciado sob a Licença MIT - veja o arquivo LICENSE para detalhes.

## 🙏 Agradecimentos

- Claude e Anthropic pela inspiração
- Comunidade de AI Agents
- Todos os contribuidores que melhoram as skills diariamente

## 📞 Contato

- GitHub: [seu-usuario](https://github.com/seu-usuario)
- Issues: [Report bugs](https://github.com/seu-usuario/skills/issues)

---

**Dica:** Para obter melhores resultados, seja específico em seus prompts e deixe que a skill guie o processo. As skills foram projetadas para serem inteligentes e adaptativas!

⭐ **Se este projeto ajudou você, considere dar uma estrela!**
