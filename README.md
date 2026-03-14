# Assistente de Produtividade por Voz — Colab (Whisper + ChatGPT + gTTS)

[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/SEU_USUARIO/SEU_REPO/blob/main/assistente_voz.ipynb)

## 📌 Introdução
Este projeto foi desenvolvido como parte de um desafio de aprendizagem ativa do **bootcamp Bradesco – GenAI & Dados (DIO)**.  
Ele roda **100% no Google Colab** — sem instalar nada no seu computador. O notebook captura voz no navegador, transcreve com **Whisper local (GPU do Colab)**, interpreta com **NLU resiliente** (tenta ChatGPT; se sem cota, usa **fallback offline**), e fala a resposta com **gTTS**.

---

## ✨ Destaques
- 🎤 **Gravação no navegador** (MediaRecorder API) direto no Colab.
- 🧠 **STT**: Whisper local acelerado por **GPU do Colab**.
- 🔎 **NLU resiliente**: ChatGPT (quando houver cota) **ou** heurística offline (PT‑BR).
- 🔁 **Follow‑up por voz**: se faltar horário no evento, o notebook pergunta, escuta e completa.
- 🔊 **TTS**: gTTS para confirmação falada.
- 🔐 **Segurança**: a OPENAI_API_KEY é digitada via `getpass()` (não fica no código nem no Git).

---

## 🧩 Fluxo do sistema
1. **Gravar** (JS no Colab) → `request_audio.wav`  
2. **STT (Whisper local)** → `transcription_text`  
3. **NLU** → JSON canônico `{ intent, entities, natural_response }`  
   - API ChatGPT (com cota) **ou** fallback offline (regex + dateparser)  
4. **Executor (MVP em memória)** → tarefas, eventos, notas  
5. **Follow‑up de horário (voz)** quando necessário  
6. **TTS (gTTS)** → áudio de confirmação

---

## 🚀 Como usar (no Colab)
1. Clique no badge **Abrir no Colab** (atualize a URL com `SEU_USUARIO/SEU_REPO` e o nome do notebook).  
2. **Ambiente de execução → Alterar tipo de ambiente de execução → Acelerador de hardware = GPU**.  
3. Execute as células na ordem:
   - **Bloco 0** – Instalação (Whisper/gTTS/OpenAI)  
   - **Bloco 1** – Idioma base (`language = 'pt'`)  
   - **Bloco 2** – Gravação de áudio (Start/Stop)  
   - **Bloco 3** – **OPENAI_API_KEY** (opcional; via `getpass()`)  
   - **Bloco 4** – STT (Whisper local, GPU)  
   - **Bloco 5** – NLU resiliente (**com título normalizado**)  
   - **Bloco 6** – Executor + **follow‑up por voz** + TTS  
4. Fale comandos como:
   - “**Criar tarefa pagar boleto amanhã às 9h**”
   - “**Agendar reunião para terça‑feira**” → responda o follow‑up: “**quatro da tarde**”/“**16:00**”
   - “**Anote: ideia de app de logística**”
   - “**O que eu tenho hoje?**”

> ⚠️ Sem cota na OpenAI? O NLU entra em **modo offline** automaticamente; o fluxo segue normalmente (Executor + TTS).

---

## 🔐 Sobre a chave da OpenAI (opcional)
A célula pede a chave com `getpass()`:
```python
import os
from getpass import getpass
if not os.environ.get("OPENAI_API_KEY"):
    os.environ["OPENAI_API_KEY"] = getpass("Digite sua OPENAI_API_KEY: ")
