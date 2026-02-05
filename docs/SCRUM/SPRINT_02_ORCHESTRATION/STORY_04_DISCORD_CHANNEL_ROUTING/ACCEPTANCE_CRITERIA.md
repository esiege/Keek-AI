# Acceptance Criteria: Story 04 - Discord Channel Routing

## Functional Requirements

### AC-1: Channel Configuration
**GIVEN** the need to assign agents to channels
**WHEN** the channel configuration is complete
**THEN** it must:
- [ ] Map channel IDs to channel names
- [ ] List which agents are assigned to each channel
- [ ] Specify routing mode for each channel
- [ ] Be loadable at startup

### AC-2: Single-Agent Routing
**GIVEN** a message arrives in a single-agent channel
**WHEN** the routing logic processes it
**THEN** it must:
- [ ] Identify the assigned agent
- [ ] Route message to that agent
- [ ] Agent responds in the same channel
- [ ] No turn management needed

### AC-3: Multi-Agent Turn-Based Routing
**GIVEN** a message arrives in a turn-based channel
**WHEN** the routing logic processes it
**THEN** it must:
- [ ] Check which agents are assigned
- [ ] Query turn manager for active agent
- [ ] Route message only to active agent
- [ ] Other agents do not respond

### AC-4: Unassigned Channel Handling
**GIVEN** a message arrives in a channel with no agents
**WHEN** the routing logic processes it
**THEN** it must:
- [ ] Ignore the message (no response)
- [ ] OR route to default handler (orchestrator)
- [ ] Behavior is configurable

### AC-5: Channel Context
**GIVEN** an agent receives a routed message
**WHEN** the agent processes the message
**THEN** the agent must:
- [ ] Know which channel the message came from
- [ ] Know their role in that channel
- [ ] Respond in the correct channel

---

## Test Scenarios

### Scenario 1: PM in #planning
1. User posts in #planning
2. PM receives message
3. PM responds in #planning
4. Dev does NOT respond

### Scenario 2: Turn-based in #reviews
1. PM and Dev are assigned to #reviews
2. Turn manager says PM is active
3. User posts in #reviews
4. PM receives and responds
5. PM signals turn complete
6. User posts again
7. Now Dev receives and responds

### Scenario 3: Message in #random (unassigned)
1. User posts in #random
2. No agent responds
3. System logs "no agent for channel"

---

## Definition of Done

- [ ] Channel configuration file created
- [ ] Single-agent routing working
- [ ] Multi-agent turn-based routing working
- [ ] Unassigned channels handled gracefully
- [ ] At least 3 channels configured for testing
- [ ] Routing logic documented
