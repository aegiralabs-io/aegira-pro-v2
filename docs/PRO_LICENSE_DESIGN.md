# Aegira Pro licensing design

License enforcement is disabled in the current development/testing build.

Production flow:

1. Customer purchases an Aegira Pro subscription through the billing system.
2. The Aegira licensing service creates/activates a license entitlement for that subscription.
3. The customer receives a license key.
4. The customer enters the key in Aegira.
5. Aegira validates the key against the licensing service and receives an entitlement/expiry state.
6. The Pro entitlement remains active for the paid billing period, normally 30 days for a monthly plan.
7. The billing provider automatically charges the next billing period while the subscription remains active.
8. If the customer cancels, the billing service stops renewal and the entitlement expires at the end of the paid period.
9. Aegira should periodically revalidate entitlement so a revoked/cancelled subscription cannot remain permanently active offline.

Important: recurring payment collection must be handled by the billing backend/payment processor, not by the local Rust binary. A license key alone cannot securely deduct money from a customer's card.

For production, the client should receive only the minimum entitlement information needed to run Pro. Do not embed the license database, signing private key, payment secrets, or master API credentials in the binary.
