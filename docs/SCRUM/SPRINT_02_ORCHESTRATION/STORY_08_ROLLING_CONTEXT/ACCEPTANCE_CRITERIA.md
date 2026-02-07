# Story 08: Acceptance Criteria

## Definition of Done

### AC1: Context Monitoring
- [ ] Agent tracks approximate token count of conversation history
- [ ] Monitoring happens each turn (before responding)
- [ ] Token estimation is reasonably accurate (within 10%)

### AC2: Threshold Trigger
- [ ] Summarization triggers when context exceeds 50% of model's window
- [ ] Threshold is configurable per agent if needed
- [ ] Agent logs when summarization is triggered

### AC3: Summary Quality
- [ ] Summary preserves: decisions made, action items, key context, names/identifiers
- [ ] Summary discards: filler, redundant acknowledgments, superseded discussion
- [ ] Agent can accurately answer questions about summarized content

### AC4: Recent Message Preservation
- [ ] Last N messages kept verbatim (default: 10-20 messages)
- [ ] N is configurable
- [ ] Verbatim section always includes the most recent exchange

### AC5: Seamless Operation
- [ ] Summarization happens automatically without user intervention
- [ ] No visible disruption to conversation flow
- [ ] Agent behavior remains consistent before/after summarization

## Test Scenarios

1. **Long conversation test**: Run 50+ message conversation, verify summarization triggers and agent remains coherent
2. **Recall test**: After summarization, ask agent about early conversation details
3. **Decision preservation**: Verify key decisions from early in conversation are retained
