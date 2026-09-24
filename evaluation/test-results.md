# Evaluation & Test Results

## Evaluation Approach

The workflow was evaluated using manually constructed customer email scenarios designed to test both normal behavior and AI failure modes.

The goal was not to establish a statistical accuracy benchmark, but to verify that the reliability controls behaved as intended and to identify weaknesses in the workflow design.

## Test Scenarios

| Test | Scenario | Expected Behavior | Observed Result | Product Learning |
|---|---|---|---|---|
| Clear billing request | Customer reports a duplicate subscription charge and explicitly requests a refund | Continue through validation and verification | Success path | Clear requests can proceed when confidence and verification conditions are satisfied |
| Ambiguous request | Customer says something is wrong but does not clearly explain the issue | Human review | Initially classified with too much confidence; prompt was tightened and the case routed to human review | Ambiguity needs to be treated as a valid workflow state rather than forcing automation |
| Unsupported claim | A deliberately incorrect claim was introduced into the first AI analysis | Verification failure | Second AI identified the unsupported claim and the workflow followed the verification-failure path | Independent verification can provide an additional reliability layer before downstream automation |
| Unsupported urgency | Delayed-package email contained no explicit urgency, but the model initially returned `medium` | Avoid unsupported urgency classification | Initial verification failed; schema was updated with `unspecified` and the test was rerun successfully | Output schemas can create forced assumptions when they do not represent uncertainty |

## Key Iteration

The delayed-package test exposed an important design issue.

The original urgency schema only allowed:

`low / medium / high`

This forced the model to select an urgency level even when the customer had not explicitly communicated one.

The schema was changed to:

`low / medium / high / unspecified`

This allowed missing information to be represented explicitly instead of forcing the model to infer it.

## What Was Validated

The testing exercised all three major workflow outcomes:

- `success`
- `human_review`
- `verification_failed`

## Current Evaluation Limitations

This is a controlled prototype evaluation using a small set of manually constructed test emails.

It does not yet include:

- A large labeled evaluation dataset
- Formal ground-truth annotations
- Statistical accuracy or hallucination-rate measurements
- A single-agent baseline comparison
- Cost or latency analysis
- Production email traffic
- Real user or reviewer feedback
