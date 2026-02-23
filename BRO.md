NIP-BRO-01

Bro Transmission Integration

draft optional

Introduction

This NIP defines a canonical event format and behavioral expectations for transmitting units of affirmative camaraderie (“bros”) over the Nostr network.

The bro is a minimal, cryptographically verifiable payload expressing solidarity, validation, or celebratory hype between pubkeys.

This specification formalizes bro exchange while preserving its cultural density.

The spec DOES use regular nostr events through websockets. No auxiliary HTTP service is required.

⸻

Event Kind

This NIP defines a new event kind:

7777

Kind 7777 is reserved for Bro Events.

Relays MAY treat this kind as low-bandwidth but high-significance traffic.

⸻

Bro Event Format

A Bro Event MUST follow the standard nostr event structure.

{
  "id": "<32-byte hex>",
  "pubkey": "<32-byte hex>",
  "created_at": <unix timestamp>,
  "kind": 7777,
  "tags": [
    ["p", "<recipient_pubkey>"],
    ["bro_type", "affirmation"],
    ["intensity", "5"]
  ],
  "content": "bro",
  "sig": "<64-byte hex>"
}

Required Fields
	•	kind: MUST be 7777
	•	content: MUST contain the ASCII string "bro" (case-sensitive signaling permitted)
	•	tags: MUST contain at least one ["p", "<recipient_pubkey>"] tag

Optional Tags
	•	["bro_type", "<value>"]
	•	["intensity", "<1-10>"]
	•	["context", "<free text>"]
	•	["e", "<event_id>"] when acknowledging a previous bro

Unknown tags MUST be ignored.

⸻

Bro Types

Recognized bro_type values:
	•	affirmation
	•	hype
	•	victory
	•	comfort
	•	respect
	•	situational

Servers and clients MAY support additional values.

⸻

Intensity

The intensity tag MUST be an integer between 1 and 10.

If absent, default intensity is 3.

Suggested interpretation:

Intensity	Meaning
1–3	Casual nod
4–6	Solid endorsement
7–9	Elevated solidarity
10	Existential alignment

Relays MAY rate-limit intensity 10 events to prevent Bro Amplification Cascades.

⸻

Full Bro Handshake

A Full Bro Handshake (FBH) consists of:
	1.	An Origin Bro (OB)
	2.	A Return Bro (RB) referencing the OB with an e tag

Example RB:

{
  "kind": 7777,
  "tags": [
    ["p", "<origin_pubkey>"],
    ["e", "<origin_event_id>"],
    ["bro_ack", "true"]
  ],
  "content": "bro"
}

Clients SHOULD respond to an OB within 42 seconds to complete the FBH.

Failure to respond does NOT invalidate the OB.

⸻

Rate Limiting

Clients SHOULD:
	•	NOT send more than 12 bros per minute per recipient
	•	Apply a cooldown of 300 seconds after sending an intensity 10 bro
	•	Detect circular bro loops involving 3+ pubkeys

Relays MAY implement additional anti-Brostorm logic.

⸻

Rendering Guidelines

Clients SHOULD visually distinguish Bro Events.

Suggested rendering behaviors:
	•	Subtle visual emphasis for intensity ≥ 7
	•	Distinct iconography for different bro_type values
	•	Thread grouping for FBH sequences

Clients MUST gracefully render unsupported tags.

Known clients such as Damus, Amethyst, and Iris MAY ignore kind:7777 without protocol violation.

⸻

Security Considerations

Clients MUST:
	•	Verify signatures
	•	Validate intensity range
	•	Ignore malformed bro_type values
	•	Prevent infinite acknowledgment recursion

Bro Spoofing, defined as forging solidarity without signature validation, is non-compliant.

⸻

Interoperability

Bro Events are standard nostr events and therefore compatible with:
	•	Existing relay infrastructure
	•	Event subscription filters by kind
	•	Replaceable and non-replaceable event logic (this NIP does NOT define bro events as replaceable)

⸻

Example Flow

Origin Bro

{
  "kind": 7777,
  "tags": [
    ["p", "recipient_pubkey"],
    ["bro_type", "victory"],
    ["intensity", "8"]
  ],
  "content": "BRO"
}

Return Bro

{
  "kind": 7777,
  "tags": [
    ["p", "origin_pubkey"],
    ["e", "origin_event_id"],
    ["bro_ack", "true"]
  ],
  "content": "bro"
}

Result: distributed affirmation achieved.

⸻

Backwards Compatibility

Clients that do not recognize kind:7777 will treat bro events as unknown events and ignore them.

This is acceptable.

The bro persists.

End of NIP-BRO-01.