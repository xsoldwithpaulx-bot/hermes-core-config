---
name: hermes-stt-model-fix
description: Fix STT transcription failures caused by invalid model name (whisper-1) when using local provider
category: devops
---

# Problem
When Hermes gateway is configured with local STT provider (e.g., whisper.cpp), using model name "whisper-1" causes transcription to fail with error:

```
Invalid model size 'whisper-1', expected one of: tiny.en, tiny, base.en, base, small.en, small, medium.en, medium, large-v1, large-v2, large-v3, large, distil-large-v2, distil-medium.en, distil-small.en, distil-large-v3, distil-large-v3.5, large-v3-turbo, turbo
```

This happens because "whisper-1" is the OpenAI API model name, not a local model name.

# Solution
1. Locate the STT configuration: typically in `~/.hermes/hiroute.yaml` (or the appropriate config file).
2. Under the `stt` section, change the `model` field from "whisper-1" to a valid local model name such as:
   - `base` (recommended for balance of speed/accuracy)
   - `small.en` (if you want English-only)
   - `medium`, `large`, etc. (larger models are more accurate but slower)
3. Restart the hermes-gateway service:
   ```bash
   systemctl --user restart hermes-gateway.service
   ```
4. Test by sending a voice message to verify transcription works.

# Notes
- The exact valid model names depend on your local whisper installation. Check `whisper --help` or the whisper.cpp documentation for available models.
- If using a different local STT backend (not whisper), ensure the model name matches that backend's expectations.

# Verification
Send a voice message to the bot and confirm it transcribes correctly.