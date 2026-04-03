# apfel-ecosystem - Project Instructions

## The Golden Goal

> **Document and coordinate the family of German-named CLI tools that expose
> Apple's on-device AI frameworks as usable UNIX tools and OpenAI-compatible servers.**

This is a meta-repository. It contains no code. It contains:
- Architecture documentation for how the tools compose
- The naming convention
- The shared principles
- The roadmap

## The Family

| Tool | German | Meaning | Apple Framework | OpenAI Endpoint | Status |
|------|--------|---------|-----------------|-----------------|--------|
| [apfel](https://github.com/Arthur-Ficial/apfel) | Apfel | Apple | FoundationModels (LLM) | `/v1/chat/completions` | Shipped |
| [ohr](https://github.com/Arthur-Ficial/ohr) | Ohr | Ear | SpeechAnalyzer (STT) | `/v1/audio/transcriptions` | Planned |
| [kern](https://github.com/Arthur-Ficial/kern) | Kern | Core | NLContextualEmbedding | `/v1/embeddings` | Planned |
| [auge](https://github.com/Arthur-Ficial/auge) | Auge | Eye | Vision (OCR) | CLI only | Planned |

## What We Vetted and Killed

| Framework | Why Not |
|-----------|---------|
| Translation | SwiftUI-only API - cannot build headless CLI |
| AVSpeechSynthesizer (TTS) | Cannot write audio files on macOS - only plays to speakers |
| ImageCreator (image gen) | No headless API - requires UI context |
| SoundAnalysis | Too niche - VAD belongs as a flag on ohr |
| CreateML | Training tool, not inference - different category |
| Core ML (generic) | Too generic - every model has different I/O |

## Naming Rules

- All names are German words
- **No umlauts** - plain a-z only
- Short (3-5 letters preferred)
- The name should evoke what the tool does (ohr = ear = listening, auge = eye = seeing)

## Shared Principles

Every tool in the ecosystem follows these rules:
- **100% on-device.** No cloud, no API keys, no network for inference.
- **Honest about limitations.** Say what works and what doesn't.
- **UNIX-first.** Stdin/stdout, pipe-friendly, correct exit codes, `--json` output.
- **Swift 6 strict concurrency.**
- **Zero dependencies.** Models ship with macOS.
- **OpenAI-compatible server** where a standard endpoint exists.

## Composability

### Unix pipes (works today)
```bash
ohr meeting.m4a | apfel "summarize this meeting"
auge --ocr screenshot.png | apfel "explain this code"
```

### Shared server (future)
```bash
apfel --serve --with ohr --with kern
# One port: /v1/chat/completions + /v1/audio/transcriptions + /v1/embeddings
```

The `--with` composability requires extracting ApfelCore as a shared Swift package. This is Phase 3 - build the individual tools first.
