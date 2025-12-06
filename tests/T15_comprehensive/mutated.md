# Project Alpha +alpha #team @lead status:active

## Sprint 1 +sprint1 @dev

### Backend +api

- [x] (A) Design REST API ~4h @alice #architecture due:2025-02-01 started:2025-01-20 done:2025-01-25
- [x] Implement auth endpoint @bob #security planned:2025-01-10 done:2025-01-12
- [ ] (B) Add rate limiting ~2h #performance
  - [ ] Research libraries ~30m
  - [x] Choose implementation @alice done:2025-01-15
  - Check existing rate limit in nginx #repeat
  - Consider Redis for distributed limiting
- [!] Database migration blocked #urgent
  - Waiting for DBA approval
  - Scheduled for next maintenance window

### Frontend +ui

- [ ] Build dashboard ~8h @charlie #feature priority:high
- [-] Old feature cancelled

## Sprint 2 +sprint2

### Mobile +mobile

- [ ] iOS app setup ~16h @dev repeat:weekly
- [ ] Android app ~20h env:"production" version:"2.0"

## Standalone Section

- [ ] Task without project inheritance #misc
- [ ] Task with escaped \@mention and \#hashtag

# Inheritance Override Test +override #base @base-user

- [ ] (C) Task overrides all #explicit @explicit-user +explicit-project
- [ ] Task inherits everything

# Edge Cases

- [ ] Very long title that goes on and on and on and on and on and on and on and on and on
- [ ] ~15m Quick task
- [ ] ~2d Multi-day task
- [ ] Multiple @user1 @user2 @user3 assignees
- [ ] Multiple #tag1 #tag2 #tag3 tags
