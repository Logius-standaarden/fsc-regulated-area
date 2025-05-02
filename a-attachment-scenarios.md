# Attachments

## A scenarios

Various scenarios regarding Regulated Areas are detailed here for clarity.

### Scenario 1. Consuming a Service Happy flow

Both the consumer and provider are members of the same Regulated Area.
The consumer Peer has registered with Membership administrator A.
The provider Peer has registered with membership administrator B.

![scenario 1](diagrams/seq-scenario1-happy-flow.svg)

### Scenario 2. Consumer is not a Member

Only the provider is a member of the Regulated Area. 
The consumer tries to gain access to a Service in a Regulated Area by providing the properties `regulated_area` and `membership_administrator_peer_id`.

![scenario 2](diagrams/seq-scenario2-consumer-not-a-member.svg)

### Scenario 3. Consumer is a Member for a different Regulated Area

The consumer Peer is a member of Regulated Area "X" and tries to gain access to a Service of part "Y".

![scenario 3](diagrams/seq-scenario3-different-regulated-areas.svg)

### Scenario 4. Consumer is Member for a different Regulated Area governed by the same Membership Administrator

The consumer Peer is a member of regulated area 'X' and tries to gain access to a service part of regulated area 'Y'.
Both consumers are registered by Membership Administrator 'A' who governs both regulated areas 'x' and 'y'

![scenario 4](diagrams/seq-scenario4-same-membership-admin.svg)