# apfel-ecosystem - Project Instructions

> **apfel is the golden example.** All patterns, conventions, and guardrails from [apfel](https://github.com/Arthur-Ficial/apfel) apply to every tool in this ecosystem.

## The Golden Goal

> **Document and coordinate the family of German-named UNIX-style CLI tools that expose
> Apple's on-device AI frameworks as usable tools and OpenAI-compatible servers.**

This is a meta-repository. It contains no code. It contains documentation, architecture decisions, and the roadmap.

## The Family

| Tool | German | Meaning | Apple Framework | OpenAI Endpoint | Status |
|------|--------|---------|-----------------|-----------------|--------|
| [apfel](https://github.com/Arthur-Ficial/apfel) | Apfel | Apple | FoundationModels (LLM) | `/v1/chat/completions` | **Shipped** |
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

## Build Order

1. **ohr** - biggest gap in ecosystem, zero-dep whisper alternative
2. **kern** - embeddings for RAG pipelines
3. **auge** - OCR, smallest scope

## Naming Rules

- All names are German words
- **No umlauts** - plain a-z only
- Short (3-5 letters preferred)
- The name should evoke what the tool does

## Shared Principles (all tools)

Same as apfel:
- **100% on-device.** No cloud, no API keys, no network for inference. Ever.
- **Honest about limitations.** Say what works and what doesn't. No fake capabilities.
- **UNIX-first.** Stdin/stdout, pipe-friendly, correct exit codes, `--json` output.
- **Clean code, clean logic.** No hacks. Proper error types.
- **Swift 6 strict concurrency.** No data races.
- **Zero dependencies.** Models ship with macOS.
- **OpenAI-compatible server** where a standard endpoint exists.
- **TDD.** Tests first, then implementation.
- **Same build system.** `.version` file, Makefile with auto-bump, `make install`.
- **Same test pattern.** Pure Swift runner (`swift run <tool>-tests`), no XCTest.
- **Same issue handling.** Fetch, vet, fix (TDD), release, close with friendly comment.

## Composability

### Unix pipes (works now)
```bash
ohr meeting.m4a | apfel "summarize this meeting"
auge --ocr screenshot.png | apfel "explain this code"
kern "search query" | jq '.vector'
```

### Shared server (future - Phase 3)
```bash
apfel --serve --with ohr --with kern
# One port: /v1/chat/completions + /v1/audio/transcriptions + /v1/embeddings
```

Requires extracting ApfelCore as a shared Swift package. Build the individual tools first.

## OpenAI API Coverage

```
http://localhost:11434/v1/
  /chat/completions       apfel     (FoundationModels)     DONE
  /models                 apfel     (list all)             DONE
  /audio/transcriptions   ohr       (SpeechAnalyzer)       PLANNED
  /embeddings             kern      (NLContextualEmbedding) PLANNED
  /audio/translations     ---       IMPOSSIBLE (Translation framework is SwiftUI-only)
  /audio/speech           ---       IMPOSSIBLE (AVSpeechSynthesizer can't write files on macOS)
  /images/generations     ---       IMPOSSIBLE (ImageCreator is UI-only)
```

Honest coverage: 5 of 9 OpenAI endpoints. The 3 that can't be built are real Apple framework limitations.
