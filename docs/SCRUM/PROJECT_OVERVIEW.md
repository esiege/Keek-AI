# Project Overview: Keek AI - Multi-Role AI Development Studio

**Last Updated**: February 4, 2026  
**Version**: 1.0  
**Status**: Foundation Setup & Initial Development

---

## 🎯 Project Vision

Build a single-machine, multi-role AI development studio that orchestrates logical personas (Producer, PM, Dev, QA, Ops) to collaboratively design, build, and deploy web applications. Prioritize clarity, state management, and extensibility while keeping the foundation simple and file-based.

---

## 📖 Current State Analysis

### Established Principles

**Core Design Constraints**:
```
✅ Single physical machine with multiple logical roles
✅ Files as the source of truth (persistent state)
✅ Discord as collaboration surface (signal and decisions)
✅ Turn-based orchestration (simulated parallelism)
✅ Dedicated Windows user account
✅ Clear filesystem boundaries and encapsulation
```

**Non-Goals**:
```
❌ Unlimited autonomous loops
❌ Network-exposed services
❌ Emotion simulation
❌ Replacing human judgment
```

### Migration Status (World → StoryPitch)

**✅ Completed**:
- TypeScript type definitions updated
- StoryPitch service layer created
- StoryPitch context provider implemented
- Backend API endpoints defined
- StoryPitchDetailPage created
- App routing updated with legacy support

**🔄 In Progress**:
- Component library (WorldsList → StoryPitchesList)
- GenreDetailPage integration
- EntityContext references

**❌ Not Started**:
- Region removal
- Complete documentation updates
- End-to-end testing

*Reference*: See [WORLD_TO_STORYPITCH_MIGRATION.md](../WORLD_TO_STORYPITCH_MIGRATION.md)

---

## 🎯 Target State

### New Entity Hierarchy

```
Genre
  └── StoryPitch (creative narrative premise)
      └── Entity (Character, Place, Item, Event)
```

**Benefits**:
- Simpler, clearer structure
- Focus on narrative creativity
- Better aligns with user mental model
- Easier to explain and use

### AI Prompt Management System

**Vision**: Centralized database-driven prompt management

**Features**:
1. **Admin UI** for CRUD operations on prompts
2. **Versioning** to track prompt iterations
3. **A/B testing** capability for prompt effectiveness
4. **Categorization** by function (character gen, world gen, scene gen, etc.)
5. **Template variables** for dynamic prompt construction
6. **Quality metrics** tracking (user feedback, regeneration rate)

**Benefits**:
- No more hard-coded prompts in source code
- Easy iteration and improvement
- Experimentation without deployments
- Data-driven prompt optimization
- Foundation for future fine-tuning

### AI Configuration System

**Vision**: Per-function AI provider and model selection

**Features**:
1. **Provider selection** (OpenAI, Anthropic, future: local models)
2. **Model selection** per function (GPT-4o, Claude Sonnet, etc.)
3. **Token cost tracking** per configuration
4. **Performance monitoring** (latency, success rate)
5. **Fallback configuration** for reliability
6. **Cost budgeting** per function

**Benefits**:
- Optimize cost vs quality per use case
- Experiment with different models
- Future-proof for new AI providers
- Granular control over AI usage
- Better cost management

---

## 🏗️ Architecture Design

### Frontend Architecture

```
┌─────────────────────────────────────────────────────┐
│                   Frontend (React)                  │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Pages                                              │
│  ├── GenreDetailPage                                │
│  ├── StoryPitchDetailPage (NEW)                     │
│  ├── EntityDetailPage                               │
│  ├── AdminPromptManagementPage (NEW)                │
│  └── AdminAIConfigPage (NEW)                        │
│                                                     │
│  Context Providers                                  │
│  ├── GenreContext                                   │
│  ├── StoryPitchContext (NEW)                        │
│  ├── EntityContext (updated)                        │
│  ├── PromptContext (NEW)                            │
│  └── AIConfigContext (NEW)                          │
│                                                     │
│  Services                                           │
│  ├── genreService.ts                                │
│  ├── storyPitchService.ts (NEW)                     │
│  ├── entityService.ts (updated)                     │
│  ├── promptService.ts (NEW)                         │
│  └── aiConfigService.ts (NEW)                       │
│                                                     │
└─────────────────────────────────────────────────────┘
```

### Backend Architecture (Required Changes)

```
┌─────────────────────────────────────────────────────┐
│               Backend (Azure Functions)             │
├─────────────────────────────────────────────────────┤
│                                                     │
│  API Endpoints                                      │
│  ├── GetStoryPitchesByParentId (exists)             │
│  │   GET /api/GetStoryPitchesByParentId             │
│  │   Query: ?parentId={genreId}&page={page}         │
│  │          &pageSize={pageSize}                    │
│  │                                                  │
│  ├── GetStoryPitchById (exists)                     │
│  │   GET /api/GetStoryPitchById?id={storyPitchId}   │
│  │                                                  │
│  ├── GenerateStoryPitch (exists)                    │
│  ├── GetEntitiesByStoryPitchId (NEW)                │
│  │                                                  │
│  ├── GetAllPrompts (NEW)                            │
│  ├── GetPromptById (NEW)                            │
│  ├── CreatePrompt (NEW)                             │
│  ├── UpdatePrompt (NEW)                             │
│  ├── DeletePrompt (NEW)                             │
│  ├── GetPromptsByCategory (NEW)                     │
│  │                                                  │
│  ├── GetAllAIConfigs (NEW)                          │
│  ├── GetAIConfigByFunction (NEW)                    │
│  ├── CreateAIConfig (NEW)                           │
│  ├── UpdateAIConfig (NEW)                           │
│  └── DeleteAIConfig (NEW)                           │
│                                                     │
│  Database Tables                                    │
│  ├── Genres (exists)                                │
│  ├── StoryPitches (exists, was Worlds)              │
│  ├── Entities (exists)                              │
│  ├── Prompts (NEW)                                  │
│  └── AIConfigs (NEW)                                │
│                                                     │
└─────────────────────────────────────────────────────┘
```

### Database Schema Design

#### Prompts Table

```sql
CREATE TABLE Prompts (
  Id UNIQUEIDENTIFIER PRIMARY KEY,
  Name NVARCHAR(200) NOT NULL,
  Category NVARCHAR(100) NOT NULL, -- 'CharacterGen', 'StoryPitchGen', etc.
  FunctionName NVARCHAR(200) NOT NULL, -- Which code function uses this
  PromptTemplate NVARCHAR(MAX) NOT NULL, -- The actual prompt text
  TemplateVariables NVARCHAR(MAX), -- JSON array of variable names
  Version INT NOT NULL DEFAULT 1,
  IsActive BIT NOT NULL DEFAULT 1,
  CreatedAt DATETIME2 NOT NULL DEFAULT GETUTCDATE(),
  UpdatedAt DATETIME2 NOT NULL DEFAULT GETUTCDATE(),
  CreatedBy NVARCHAR(100),
  Notes NVARCHAR(MAX), -- Admin notes about this prompt
  UsageCount INT DEFAULT 0, -- Track how often used
  SuccessRate DECIMAL(5,2), -- Track quality (% not regenerated)
  AverageTokenCost INT, -- Track cost per use
  
  INDEX IX_Prompts_Category (Category),
  INDEX IX_Prompts_FunctionName (FunctionName),
  INDEX IX_Prompts_IsActive (IsActive)
);
```

#### AIConfigs Table

```sql
CREATE TABLE AIConfigs (
  Id UNIQUEIDENTIFIER PRIMARY KEY,
  FunctionName NVARCHAR(200) NOT NULL UNIQUE, -- 'generateCharacter', etc.
  Provider NVARCHAR(50) NOT NULL, -- 'openai', 'anthropic'
  Model NVARCHAR(100) NOT NULL, -- 'gpt-4o', 'claude-3-5-sonnet-20241022'
  Temperature DECIMAL(3,2) DEFAULT 0.7,
  MaxTokens INT DEFAULT 4000,
  TopP DECIMAL(3,2) DEFAULT 1.0,
  FrequencyPenalty DECIMAL(3,2) DEFAULT 0.0,
  PresencePenalty DECIMAL(3,2) DEFAULT 0.0,
  IsActive BIT NOT NULL DEFAULT 1,
  FallbackConfigId UNIQUEIDENTIFIER, -- Fallback if primary fails
  EstimatedTokenCost INT, -- Estimated tokens per request
  CreatedAt DATETIME2 NOT NULL DEFAULT GETUTCDATE(),
  UpdatedAt DATETIME2 NOT NULL DEFAULT GETUTCDATE(),
  
  FOREIGN KEY (FallbackConfigId) REFERENCES AIConfigs(Id),
  INDEX IX_AIConfigs_FunctionName (FunctionName),
  INDEX IX_AIConfigs_IsActive (IsActive)
);
```

#### Prompt Usage Tracking (Optional, Future)

```sql
CREATE TABLE PromptUsageLog (
  Id UNIQUEIDENTIFIER PRIMARY KEY,
  PromptId UNIQUEIDENTIFIER NOT NULL,
  AIConfigId UNIQUEIDENTIFIER NOT NULL,
  UserId NVARCHAR(100),
  RequestedAt DATETIME2 NOT NULL,
  CompletedAt DATETIME2,
  TokensUsed INT,
  WasRegenerated BIT DEFAULT 0, -- User clicked regenerate?
  UserRating INT, -- 1-5 stars (optional)
  ErrorMessage NVARCHAR(MAX),
  
  FOREIGN KEY (PromptId) REFERENCES Prompts(Id),
  FOREIGN KEY (AIConfigId) REFERENCES AIConfigs(Id),
  INDEX IX_PromptUsage_Requested (RequestedAt),
  INDEX IX_PromptUsage_PromptId (PromptId)
);
```

---

## 🔄 Migration Strategy

### Phase 1: Complete StoryPitch Migration (Sprint 1)

1. ✅ Finish component migration
2. ✅ Remove all Region references
3. ✅ Update Entity relationships (storyPitchId instead of regionId)
4. ✅ Update all documentation
5. ✅ Test end-to-end flows
6. ✅ Keep World code for backward compatibility (temp)

### Phase 2: Prompt Management System (Sprint 2)

1. ✅ Design and implement Prompts database schema
2. ✅ Create backend API endpoints
3. ✅ Build frontend service layer
4. ✅ Create admin UI for prompt management
5. ✅ Migrate first prompt (e.g., character generation)
6. ✅ Test and validate
7. ✅ Migrate remaining prompts iteratively

### Phase 3: AI Config System (Sprint 3)

1. ✅ Design and implement AIConfigs database schema
2. ✅ Create backend configuration API
3. ✅ Build frontend config service
4. ✅ Create admin UI for AI configuration
5. ✅ Integrate with existing AI calls
6. ✅ Update token cost tracking
7. ✅ Test provider switching

### Phase 4: Integration & Testing (Sprint 4)

1. ✅ End-to-end integration testing
2. ✅ Performance testing and optimization
3. ✅ Documentation completion
4. ✅ Migration scripts for production
5. ✅ Deployment and monitoring

---

## 🛠️ Technical Challenges & Solutions

### Challenge 1: Hard-Coded Prompts

**Current State**:
- Prompts scattered across service files
- Difficult to track and update
- No versioning or testing

**Solution**:
- Extract all prompts to database
- Create prompt template system with variables
- Version control for prompt iterations
- A/B testing infrastructure

**Example Current Code**:
```typescript
// Bad: Hard-coded in service file
const prompt = `Generate a character for a ${genre} story. 
The character should be interesting and unique...`;
```

**Target Code**:
```typescript
// Good: Retrieved from database with variables
const promptTemplate = await promptService.getPrompt('generateCharacter');
const prompt = promptTemplate.render({ genre: genreName });
```

---

### Challenge 2: AI Provider Lock-In

**Current State**:
- Some code assumes OpenAI
- Switching providers requires code changes
- No easy way to test different models

**Solution**:
- Abstract AI calls behind common interface
- Configuration-driven provider selection
- Easy model switching per function
- Fallback mechanisms

**Example Implementation**:
```typescript
// aiService.ts
export async function callAI(
  functionName: string, 
  promptTemplate: string, 
  variables: Record<string, any>
) {
  const config = await aiConfigService.getConfig(functionName);
  const prompt = renderPrompt(promptTemplate, variables);
  
  switch (config.provider) {
    case 'openai':
      return await callOpenAI(prompt, config);
    case 'anthropic':
      return await callAnthropic(prompt, config);
    default:
      throw new Error(`Unknown provider: ${config.provider}`);
  }
}
```

---

### Challenge 3: Region Removal

**Current State**:
- Regions are intermediate layer between StoryPitch and Entity
- Some code may depend on Region concept
- Database has Region foreign keys

**Solution**:
- Update Entity table to reference StoryPitchId directly
- Migrate existing data (Entities point to their Region's parent StoryPitch)
- Remove Region table after migration
- Update all code references

**Migration Steps**:
1. Add `StoryPitchId` column to Entities table
2. Populate with `SELECT StoryPitchId FROM Regions WHERE Id = Entity.RegionId`
3. Update foreign key constraints
4. Remove `RegionId` column
5. Drop Regions table
6. Update frontend code

---

## 📊 Success Metrics

### Technical Metrics

**Performance**:
- ✅ Page load time < 2 seconds
- ✅ API response time < 500ms (excluding AI generation)
- ✅ AI generation time < 30 seconds

**Quality**:
- ✅ Zero critical bugs in production
- ✅ < 5% error rate on AI calls
- ✅ Test coverage > 70%

**Maintainability**:
- ✅ All AI prompts in database
- ✅ All documentation up to date
- ✅ Code follows established patterns

### Business Metrics

**User Experience**:
- ✅ Reduced regeneration rate (better prompts)
- ✅ Faster content generation
- ✅ More creative/unique outputs

**Cost**:
- ✅ 20% reduction in token costs (via optimization)
- ✅ Better cost visibility per function
- ✅ Budget controls prevent overruns

**Flexibility**:
- ✅ Can change AI provider in < 1 hour
- ✅ Can update prompts without deployment
- ✅ A/B test new prompts easily

---

## 🔒 Security Considerations

### API Key Management

**Current**: API keys in environment variables  
**Maintain**: Keep keys server-side only  
**Add**: Key rotation capability

### Admin Access

**Requirement**: Only authorized users can access admin pages  
**Implementation**: 
- Role-based access control (RBAC)
- Admin role in Auth0
- Frontend route guards
- Backend permission checks

### Prompt Injection Prevention

**Risk**: User input in prompts could manipulate AI  
**Mitigation**:
- Sanitize all user inputs
- Use template variables (not string interpolation)
- Rate limiting on AI calls
- Content filtering on outputs

---

## 💰 Cost Optimization Strategy

### Token Cost Tracking

**Implement**:
1. Track tokens per function call
2. Calculate cost based on provider/model rates
3. Display to admins in dashboard
4. Alert on unusual spikes

### Optimization Opportunities

**Prompt Engineering**:
- Shorter prompts where possible
- Reuse system prompts
- Cache common generations

**Model Selection**:
- Use cheaper models for simple tasks
- Use expensive models only when quality critical
- A/B test to find minimum viable model

**Caching**:
- Cache generated content
- Reuse similar generations
- Deduplicate requests

---

## 📚 Documentation Requirements

### Code Documentation

**Required**:
- JSDoc comments on all public functions
- Type definitions for all interfaces
- Examples in service layer docs
- README in each major folder

### System Documentation

**Required**:
- Architecture diagrams
- Data flow diagrams
- API specifications
- Database schema docs

### User Documentation

**Required**:
- Admin user guide
- Prompt template syntax guide
- AI configuration best practices
- Troubleshooting guide

---

## 🚀 Deployment Strategy

### Frontend Deployment

**Current**: Lovable deployment  
**Process**:
1. Build production bundle
2. Run tests
3. Deploy to Lovable
4. Smoke test in production

### Backend Deployment

**Current**: Azure Functions  
**Process**:
1. Update database schema (if needed)
2. Run migration scripts
3. Deploy functions
4. Verify endpoints
5. Monitor for errors

### Database Migration

**Critical Steps**:
1. Backup production database
2. Test migration on staging
3. Run migration during low-traffic window
4. Verify data integrity
5. Monitor for issues
6. Have rollback plan ready

---

## 🔮 Future Enhancements

### Phase 5: Fine-Tuning System (Future)

**Goal**: Train custom models on user's generated content

**Features**:
- Collect successful generations
- Label high-quality outputs
- Fine-tune models per genre
- Compare against base models
- Cost/benefit analysis

### Phase 6: Advanced Prompt Engineering (Future)

**Goal**: Automatic prompt optimization

**Features**:
- Evolutionary prompt generation
- Multi-armed bandit testing
- Quality scoring system
- Automatic A/B tests
- Learning from user feedback

### Phase 7: Multi-Modal Support (Future)

**Goal**: Images, audio, video generation

**Features**:
- Image generation for characters/places
- Voice synthesis for narration
- Music generation for scenes
- Map generation for worlds

---

## 🤔 Open Questions

### Technical Questions

1. **Prompt Versioning**: How to handle prompt versions in production?
   - Keep all versions? Archive old ones? Migration path?

2. **AI Config Fallbacks**: What's the cascade strategy?
   - Model fails → try different model?
   - Provider fails → try different provider?

3. **Caching Strategy**: How long to cache AI generations?
   - By user? By prompt? Time-based? LRU?

4. **Rate Limiting**: How to prevent abuse?
   - Per user? Per function? Per day?

5. **Error Handling**: What to show users when AI fails?
   - Generic error? Specific reason? Retry automatically?

### Business Questions

1. **Cost Management**: Budget per user? Per tier?
   - Free tier limits?
   - Paid tier quotas?

2. **Quality Metrics**: How to measure prompt effectiveness?
   - User ratings? Regeneration rate? Engagement?

3. **Admin Access**: Who can manage prompts/configs?
   - Devs only? Trust select users?

4. **Beta Testing**: How to roll out new prompts/configs?
   - Gradual rollout? A/B test?

---

## 📞 Stakeholder Communication

### Key Stakeholders

**Development Team**:
- Needs technical specifications
- Needs clear architecture decisions
- Needs implementation timelines

**Product Owner** (if applicable):
- Needs feature descriptions
- Needs business justification
- Needs success metrics

**End Users**:
- Needs clear benefits explanation
- Needs migration communication
- Needs support documentation

---

## 🎯 Next Steps

### Immediate (This Week)

1. ✅ Review and approve this project overview
2. ✅ Answer open questions
3. ✅ Create Sprint 1 folder and stories
4. ✅ Begin Sprint 1 execution

### Short Term (Next 2 Weeks)

1. ✅ Complete Sprint 1 (StoryPitch migration)
2. ✅ Design database schemas for Sprint 2
3. ✅ Create API specifications for Sprint 2
4. ✅ Plan Sprint 2 stories

### Medium Term (Next 4-6 Weeks)

1. ✅ Complete Sprint 2 (Prompt Management)
2. ✅ Complete Sprint 3 (AI Config System)
3. ✅ Begin Sprint 4 (Integration Testing)

### Long Term (Next 2-3 Months)

1. ✅ Production deployment
2. ✅ User feedback collection
3. ✅ Iteration and improvement
4. ✅ Plan Phase 5 (Fine-Tuning)

---

## 📖 Related Documentation

- [Scrum Process Guide](./SCRUM_PROCESS.md)
- [Sprint 1 README](./SPRINT_1_COMPLETE_STORYPITCH/README.md) *(to be created)*
- [World→StoryPitch Migration Status](../WORLD_TO_STORYPITCH_MIGRATION.md)
- [Documentation Guide](../docs/DOCUMENTATION_GUIDE.md)
- [AI Config Documentation](../src/config/README.md)
- [Backend Specification](../docs/authentication/BACKEND_SPECIFICATION.md)

---

**Status**: Ready for review and sprint planning 🚀

