# Stripe Integration Reference

## Setup

```ts
import Stripe from 'stripe';
const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!, { apiVersion: '2024-12-18.acacia' });
```

## Checkout Sessions

```ts
const session = await stripe.checkout.sessions.create({
  mode: 'payment', // or 'subscription'
  line_items: [{ price: 'price_xxx', quantity: 1 }],
  success_url: `${BASE_URL}/success?session_id={CHECKOUT_SESSION_ID}`,
  cancel_url: `${BASE_URL}/pricing`,
  customer_email: user.email,
  metadata: { userId: user.id },
});
// Redirect to session.url
```

## Subscriptions

```ts
// Subscription checkout
const session = await stripe.checkout.sessions.create({
  mode: 'subscription',
  line_items: [{ price: 'price_xxx', quantity: 1 }],
  subscription_data: { trial_period_days: 14, metadata: { plan: 'pro' } },
  customer: customerId,
  success_url: `${BASE_URL}/dashboard`,
  cancel_url: `${BASE_URL}/pricing`,
});

// Cancel at period end
await stripe.subscriptions.update(subId, { cancel_at_period_end: true });

// Change plan
await stripe.subscriptions.update(subId, {
  items: [{ id: itemId, price: 'price_new' }],
  proration_behavior: 'create_prorations',
});
```

## Webhooks (Signature Verification)

```ts
export async function POST(req: Request) {
  const body = await req.text();
  const sig = req.headers.get('stripe-signature')!;
  let event: Stripe.Event;
  try {
    event = stripe.webhooks.constructEvent(body, sig, process.env.STRIPE_WEBHOOK_SECRET!);
  } catch { return new Response('Invalid signature', { status: 400 }); }

  switch (event.type) {
    case 'checkout.session.completed':
      await activateSubscription(event.data.object as Stripe.Checkout.Session);
      break;
    case 'invoice.payment_failed':
      await handleFailedPayment(event.data.object as Stripe.Invoice);
      break;
    case 'customer.subscription.deleted':
      await deactivateSubscription((event.data.object as Stripe.Subscription).id);
      break;
  }
  return new Response('ok');
}
```

## Customer Portal

```ts
const portal = await stripe.billingPortal.sessions.create({
  customer: customerId,
  return_url: `${BASE_URL}/dashboard`,
});
// Redirect to portal.url
```

## Payment Intents (Custom Flow)

```ts
const intent = await stripe.paymentIntents.create({
  amount: 2000, // $20.00 in cents
  currency: 'usd',
  customer: customerId,
  metadata: { orderId: '123' },
  idempotencyKey: `order-123-payment`,
});
// Return intent.client_secret to frontend
```

## Stripe Elements (Frontend)

```tsx
import { Elements, PaymentElement, useStripe, useElements } from '@stripe/react-stripe-js';
import { loadStripe } from '@stripe/stripe-js';

const stripePromise = loadStripe(process.env.NEXT_PUBLIC_STRIPE_KEY!);

function CheckoutForm() {
  const stripe = useStripe();
  const elements = useElements();
  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    const { error } = await stripe!.confirmPayment({
      elements: elements!,
      confirmParams: { return_url: `${location.origin}/success` },
    });
    if (error) { /* show error.message */ }
  };
  return <form onSubmit={handleSubmit}><PaymentElement /><button>Pay</button></form>;
}

// <Elements stripe={stripePromise} options={{ clientSecret }}><CheckoutForm /></Elements>
```

## Error Handling

```ts
try { await stripe.paymentIntents.create({ /* ... */ }); }
catch (err) {
  if (err instanceof Stripe.errors.StripeCardError) { /* show err.message */ }
  else if (err instanceof Stripe.errors.StripeRateLimitError) { /* retry */ }
  else if (err instanceof Stripe.errors.StripeInvalidRequestError) { /* bug */ }
}
```

## Testing

```bash
stripe listen --forward-to localhost:3000/api/webhooks/stripe
stripe trigger checkout.session.completed
```

Test cards: `4242424242424242` (success), `4000000000000002` (decline), `4000000000003220` (3DS).

```ts
// Test clocks: simulate subscription lifecycle
const clock = await stripe.testHelpers.testClocks.create({ frozen_time: now });
const customer = await stripe.customers.create({ test_clock: clock.id });
await stripe.testHelpers.testClocks.advance(clock.id, { frozen_time: now + 86400 * 32 });
```

## Key Conventions

- Store `stripe_customer_id` on your user record; create once, reuse
- Attach `metadata` to every object to link back to your system
- Handle webhooks idempotently: check if event was already processed
- Never trust client-side amounts; set prices server-side
- Use Checkout or Payment Elements for PCI compliance
- Always use idempotency keys on create operations to prevent duplicates
