# STORY_10_RELIABLE_BIO_UPDATE

**Status:** BACKLOG  
**Priority:** Medium  
**Estimate:** TBD  
**Assigned:** TBD  

## Description
Fix the Discord bio auto-update script to be reliable and dependable instead of the current flaky file-based approach that fails unpredictably.

## Current Problems
- Reads stale `sessions.json` file directly (race conditions)
- No error handling for missing data or API failures
- Silent failures when Discord API is down
- Assumes properties exist that might not be present
- No retry logic or graceful degradation

## Acceptance Criteria

1. **API-Based Data**: Replace file reading with `sessions_list` API calls for real-time session data

2. **Error Handling**: Graceful handling of missing session properties, API failures, and Discord rate limits

3. **Retry Logic**: Automatic retries with exponential backoff for transient failures

4. **Fallback Strategy**: Cache last-known-good bio state and use when APIs are unavailable

5. **Validation**: Verify session data completeness before attempting bio update

6. **Logging**: Proper error logging and success confirmation with troubleshooting info

7. **Rate Limiting**: Respect Discord API limits and avoid unnecessary update calls

8. **Testing**: Verify reliability across different failure scenarios (network down, OpenClaw restart, etc.)

## Technical Notes
- Use OpenClaw's internal session APIs instead of direct file access
- Consider debouncing to avoid spam during rapid session changes
- Bio character limits: Discord allows 190 chars max
- Current format works well, just needs reliable data source

## Definition of Done
- [ ] Script uses sessions_list API instead of file reads
- [ ] Error handling covers all failure modes
- [ ] Retry logic implemented with backoff
- [ ] Fallback caching system working
- [ ] Comprehensive logging added
- [ ] Rate limiting respected
- [ ] Testing completed across failure scenarios
- [ ] Bio updates are consistently reliable

## Dependencies
- OpenClaw session API documentation review

---
*Created: 2026-02-07 22:14 PST*  
*Requested by: Jesse*