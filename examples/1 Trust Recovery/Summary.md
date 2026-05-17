# Trust Recovery Example

This example shows a small trust disruption and recovery.

The conversation begins as a normal service interaction. The user asks for a swimming pool, then a restaurant booking. The assistant says it will reserve the restaurant, but the user later complains that nothing happened after 55 minutes.

DriftTag marks this disruption with a negative trust_delta and an observed/confirmed inflection. The assistant then apologizes, gathers the missing booking details, completes the restaurant booking, arranges taxi information, and the trust curve recovers into positive territory.

Why this example matters:
- Shows DriftTag detecting a real service-flow failure.
- Shows trust drop and recovery across turns.
- Demonstrates trajectory analysis, not just single-turn labeling.
