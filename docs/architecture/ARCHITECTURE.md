# VOICE COMPANION Architecture

## Runtime boundary

The system is divided into six runtime stages and four state domains.

### Runtime stages

1. **Input** — capture and normalize audio.
2. **Recognition** — convert speech into timestamped transcript events.
3. **Orchestration** — construct the turn context and coordinate state/providers.
4. **Generation** — obtain a response from the selected LLM provider.
5. **Policy** — apply response constraints, persona style and output normalization.
6. **Synthesis** — convert the final response into playable audio.

### State domains

- **Session state** — ephemeral state for the active interaction.
- **Memory state** — retrievable conversational/personal knowledge.
- **Persona state** — stable identity and behavioral configuration.
- **Relationship state** — derived continuity and interaction state.

These domains must not be collapsed into one undifferentiated prompt or database record.

## Event model

The preferred internal representation is event-oriented. Examples:

```text
AudioStarted
AudioChunkReceived
SpeechStarted
TranscriptPartial
TranscriptFinal
TurnStarted
ContextResolved
MemoryRetrieved
GenerationStarted
GenerationDelta
GenerationCompleted
PolicyApplied
SynthesisStarted
AudioOutputStarted
AudioOutputCompleted
TurnCompleted
TurnCancelled
ProviderError
```

Each event should carry a correlation/session identifier and monotonic timestamp where available.

## Turn lifecycle

```text
IDLE
  ↓
LISTENING
  ↓
TRANSCRIBING
  ↓
CONTEXT_BUILDING
  ↓
GENERATING
  ↓
SYNTHESIZING
  ↓
PLAYING
  ↓
IDLE
```

Cancellation/interruption must be legal from every active state and must return the system to a consistent state without losing already-committed memory.

## Provider boundary

STT, LLM and TTS implementations expose adapters behind stable internal interfaces. Provider-specific credentials, SDK objects and response formats must never leak into the orchestrator.

## Persistence boundary

Persistent state must be versioned. Every serialized persona, memory export and relationship-state snapshot should include a schema version so future migrations are explicit.
