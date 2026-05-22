# Brownfield Bug Fix Example

This example walks through fixing a bug in an existing project using the harness.

## Scenario

The `payment-service` has a bug: orders with discount codes that have expired are still being processed, causing incorrect charges. The fix needs to validate discount code expiration before applying the discount.

## Step 1: Invoke the harness

```
/agentic-dev-harness in ./payment-service fix orders applying expired discount codes.
Start with brownfield discovery. Do not touch deployment config.
Success criteria: regression test passes, discount validation works, existing tests green.
```

## Step 2: Discovery (read-only)

The harness reads:

- `src/discount/validator.ts` — current discount validation logic
- `src/order/processor.ts` — order processing entry point
- `src/discount/__tests__/validator.test.ts` — existing discount tests
- `package.json` — test commands (`npm test -- --testPathPattern=discount`)

Findings:
- `validator.ts:42` checks discount code existence but not expiration.
- `processor.ts:128` calls `validator.applyDiscount()` without date context.
- The `Discount` model has an `expiresAt` field that is never checked in validation.
- Existing tests cover happy path but not expired codes.

## Step 3: Spec draft

The harness creates `.agentic/changes/fix-expired-discount/`:

**proposal.md:**
```markdown
# Proposal: fix-expired-discount

## Why
Orders with expired discount codes are being processed, causing incorrect charges and customer disputes.

## What changes
- Add expiration check in `src/discount/validator.ts`
- Update `src/order/processor.ts` to log and reject expired discounts
- Add characterization test for expired discount scenario

## Non-goals
- Discount code UI changes
- Batch cleanup of expired codes

## Impact
- All order processing paths that use discount codes
- Customer-facing: previously accepted expired codes will now be rejected

## Risks
- Legitimate use of grace-period discounts could be blocked
- Mitigation: check exact timestamp, not just date

## Rollback
- Revert commit. No data migration needed.
```

**design.md, tasks.md, specs/discount-validation/spec.md** follow the templates.

## Step 4: Challenge gate

The harness challenges:
1. **Business:** What about promotions that intentionally overlap expiration? -> Confirmed: no such promotions.
2. **Risk:** What if server clock is skewed? -> Use UTC comparison.
3. **Implementation:** Should this be in validator or processor? -> Validator, so all callers benefit.
4. **History:** Are there other places that check expiration? -> No, this is the single validation point.

## Step 5: Approval gate

The harness presents:

- Mode: brownfield
- Scope: 2 files changed, 1 test added
- Non-goals: UI, batch cleanup
- High-risk: discount validation logic (customer-facing)
- Verification: `npm test -- --testPathPattern=discount`
- Rollback: git revert

User approves.

## Step 6: Implementation

1. **Characterization test first:**
   ```typescript
   test('rejects expired discount code', () => {
     const expiredCode = { code: 'SAVE10', expiresAt: new Date('2020-01-01') };
     expect(() => validator.applyDiscount(order, expiredCode)).toThrow('Discount code has expired');
   });
   ```

2. **Minimal implementation:**
   ```typescript
   // In validator.ts, before applying discount:
   if (discount.expiresAt && new Date() > discount.expiresAt) {
     throw new DiscountExpiredError(discount.code);
   }
   ```

3. **Run tests:** `npm test -- --testPathPattern=discount` — all pass.

## Step 7: Review

```bash
git diff
```

Review confirms:
- Only `validator.ts` and `validator.test.ts` changed.
- No unrelated refactoring.
- Test covers both expired and valid discount paths.

## Step 8: Verify

```bash
npm test                           # Full suite: 142 passing
npm run lint                       # Zero warnings
npm run build                      # Clean build
```

Evidence recorded in `.evidence/fix-expired-discount.md`.

## Step 9: Archive

Change archived to `.agentic/changes/fix-expired-discount/`. Evidence preserved.

## Key takeaways

1. Discovery found the single validation point — a small change with broad impact.
2. Characterization test was written before the fix.
3. The harness caught a clock-skew risk at the challenge gate that the initial spec missed.
4. Total time: ~15 minutes from invocation to archive.
