**Encore Booking App**

Engineering requirements specification

Version: 1.0.0-draft.16 · Status: Draft, not approved

Author: Product Owner

Summary: This specification defines the first-release requirements for Encore, a responsive web platform for discovering events, booking tickets, completing payment, receiving digital entry tickets, and supporting organiser operations. It is intended for architects, engineers, testers, and security reviewers. It settles the end-to-end journey for individual and group booking, general-admission ticketing, organiser event and inventory tools, sales visibility, and one-time door check-in. Approved decisions establish one-ticket individual checkout, all-inclusive quoted prices, web QR tickets with signed-in and email-link retrieval, payment uncertainty handling, a uniform 15-minute unpaid hold, no fan-requested refunds, automatic refunds on event cancellation, and organiser-edited rather than dynamic pricing. Fan-to-fan resale remains open. Approved dashboard and gate targets establish sales data no more than 30 seconds old after a paid sale and results for 95% of gate scans within 2 seconds; lifecycle, performance, and recovery decisions are being incorporated section by section.

<!-- pie:section sec_4041bdf12508 -->
# 1 Scope and system context
Encore is a concert and festival booking platform for fans, promoters, and venues. The first release supports the journey from event discovery through payment and usable digital entry ticket for one event at a time, together with organiser event, inventory, sales, and check-in capabilities.
<!-- pie:section sec_79c809292eb6 -->
## 1.1 In-scope and out-of-scope capabilities
In scope are responsive web event browsing and search, event details with ticket types and prices, individual booking, group booking with shared invitations and separate member payment, digital ticket issuance, organiser event creation and publishing, general-admission ticket types with capacity and price, live sales visibility, and one-time door check-in. Multi-event bundles, season passes, seated maps and seat selection, loyalty or rewards, organiser payout or accounting tooling beyond the payment provider, a new identity system, owned payment rails, and a native mobile app are out of scope for the first release.
<!-- pie:section sec_cd166dd95da8 -->
## 1.2 System boundary and external dependencies
Production and isolated staging — rehearse before release

Current mainstream desktop and mobile browsers — broad web coverage
<!-- pie:section sec_4d25fc873241 -->
# 2 User and organiser functional requirements
Fans must be able to discover and book concert and festival tickets, either alone or as a group, without coordinating payment outside Encore. Organisers must be able to create and publish events, manage general-admission ticket inventory, observe sales, and check fans in.
<!-- pie:section sec_b9ac7bae5a20 -->
## 2.1 Event discovery and event details
Optional device location with manual fallback — fan chooses

Partial names and typo tolerance — broader matches

Core facts plus entry rules — include age/access restrictions

Keep visible and label sold out — fans can still find details
<!-- pie:section sec_354d60f652ea -->
## 2.2 Individual booking, payment, and ticket issuance
One ticket — individual checkout represents one attendee

All-inclusive ticket price — displayed price is the payable total

Signed-in list and email link — two retrieval routes

Pending screen; block a new payment — wait for resolution

Keep checkout and original hold deadline — retry within the existing window
<!-- pie:section sec_6635cf7aba1a -->
## 2.3 Group booking
At each member's checkout — invitation alone reserves nothing

Every unpaid checkout hold lasts 15 minutes, the same for every event. When that time ends, the seats go back on sale. The first release does not let an organiser choose a different hold.

Any signed-in link holder — freely shareable invitation

Any available type for the event — independent tier choices

Starter closes invitations — complete the shared invite manually

Leave and release any unpaid hold immediately — free inventory

Starter may disable new joins — existing memberships remain
<!-- pie:section sec_c9ee3734e333 -->
## 2.4 Organiser event and inventory management
No dynamic pricing — prices change only through organiser edits

Core facts, saleable types and entry rules — fuller readiness gate

Type capacities under an event ceiling — enforce both limits

Reduce no lower than sold plus held tickets — protect reservations

Keep the quoted price until checkout expires — protect active quotes
<!-- pie:section sec_be352c67bd5a -->
## 2.5 Sales dashboard and door check-in
Paid sales plus held/available inventory — operational stock view

Dashboard sales figures are at most 30 seconds old after a paid sale.

Distinct rejection reasons — staff know why entry failed

Retain pending scan for online retry — no admission until confirmed

Keep last figures with stale warning and timestamp — preserve context
<!-- pie:section sec_2b58a4727855 -->
# 3 Lifecycle, data, and consistency requirements
The first release must represent the progression from event discovery through booking and payment to digital ticket use at entry. Group booking must expose member participation and payment status to the group starter and organiser.
<!-- pie:section sec_5d3d389d2790 -->
## 3.1 Booking and ticket lifecycle
No fan-requested refunds — purchases are final

Invalidate tickets and refund automatically — return payment

Keep purchase paid and recover issuance automatically — no new payment

Best-effort cancel; committed success remains valid — reconcile final result
<!-- pie:section sec_81d27b125af1 -->
## 3.2 Inventory and check-in consistency
First committed scan wins — other requests return already used

First committed reservation wins — later requests fail availability

Reallocate if stock remains; otherwise reverse payment — preserve capacity

Paid booking and stock atomically; ticket uses explicit pending state — staged fulfilment
<!-- pie:section sec_c99304c2473e -->
## 3.3 Data retention and recovery
Keep ticket and payment records for 7 years so payment disputes can be checked. Keep gate admission records for 90 days after the event, then delete them. Identity stays with the existing identity provider and is not copied into a second store.

Automatically reconcile from provider evidence — isolate only unresolved cases

No loss of committed purchase/admission records — durable core state
<!-- pie:section sec_4bba7d52c31e -->
# 4 Interfaces and delegated authority
Encore delegates sign-in and payment to fixed external providers while retaining responsibility for booking, group coordination, ticket presentation, organiser tooling, and check-in behavior.
<!-- pie:section sec_de06e8217062 -->
## 4.1 Identity and role permissions
Fan, organiser and gate staff — separate event-scoped entry authority

Existing provider-managed groups — reuse established grants

Next request uses current permissions — immediate effective revocation

Recheck before commit — reject newly unauthorised work

Event organisers for non-financial recovery — platform handles payment corrections
<!-- pie:section sec_f682734bcc8a -->
## 4.2 Payment and ticket interfaces
Web QR ticket — no wallet integration required

The first release uses the payment provider Encore already integrates with. That provider's current contract is the one Encore follows. Encore does not add a second contract and does not change the adapter.

Callback or verified server query — either authoritative evidence suffices
<!-- pie:section sec_731da12cd8be -->
## 4.3 Client and organiser-facing contracts
Stable typed errors — distinguish validation, access, conflict and outage

Deploy only compatible additive changes — avoid breaking first-release clients
<!-- pie:section sec_b302cbf5d0b9 -->
# 5 Security and privacy controls
Minimal starter view, richer organiser view — audience-specific fields

Explicit acknowledgement before joining — separate consent step

References, amounts and statuses only — exclude payment-method details

Opaque unguessable reference with server lookup — no ticket data in code

All privileged changes plus admission decisions — full event-day trail
<!-- pie:section sec_09e91bf114fc -->
# 6 Performance, capacity, and reliability
Failure rates plus stuck-state age/backlog — detect silent lifecycle failures

At most 10 background recovery jobs run at once.
<!-- pie:section sec_0a2b7a4ee47c -->
## 6.1 Entry and booking performance
One event at a time: 2,000 fans browsing, 200 checkouts in a minute, and 20 gate scans a second.

95% of scans show a result within 2 seconds. A scan that takes longer stays pending and is not treated as admitted.

95% of confirmed payments show a usable ticket within 10 seconds. The fan can leave and open the same ticket from the signed-in list or the email link.

Reject new checkout promptly with retry guidance — no hidden queue

Protected capacity for gate work — booking load cannot consume it
<!-- pie:section sec_abcb34b60793 -->
## 6.2 Failure recovery and idempotency
Stable checkout-attempt identifier — retries reuse one purchase

Periodic reconciliation of persisted deadlines — recover missed cleanup

Transient network/service failures only — permanent errors stop

Stop a recovery job after 5 attempts or 2 minutes, whichever comes first.

Pause and preserve unresolved state — await operator resolution

Booking and gate scan are usable again within 15 minutes after an outage. A scan during the outage is not treated as admitted.

Replay by scan-request identifier — recover original result

Preserve and resume all active state — compatible migrations required
<!-- pie:section sec_69a5e3307e09 -->
# 7 Verification and acceptance
Instrumented client-to-result tests — measure user-visible waits

Every mandatory criterion blocks — release only with complete passing evidence

Product Owner with Risk Manager review — joint risk consideration
<!-- pie:section sec_e92bc7eef078 -->
## 7.1 End-to-end acceptance journeys
Core journeys plus linked event-day scenario — demonstrate their interaction

Emulated failures plus sandbox normal flow — deterministic fault coverage
<!-- pie:section sec_dafb3edf8db2 -->
## 7.2 Negative and fault-condition verification
Boundary tests plus restart/concurrency integration tests — broader lifecycle proof
<!-- pie:section sec_a0b7877c693f -->
# 8 Risks and unresolved decisions
The unresolved decisions are the refund and cancellation policy, whether fan-to-fan ticket resale belongs in the first release, how long an unfinished group booking holds tickets before expiry, and whether dynamic or demand-based pricing is in scope. These decisions affect booking and ticket lifecycle behavior, group inventory handling, and organiser operations. No default policy is specified here.
