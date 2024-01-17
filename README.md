# NextJS 14: Stripe Webhook.
##### _Integrating and Handling Stripe Webhooks in Next.js_
**by** [Tego](https://x.com/tegocodes)

### Links
**Twitter/X** → [@tegocodes](https://x.com/tegocodes)
**Portfolio** → [tego.dev](https://tego.dev)

#nextjs, #stripe, #stripe-checkout, #subscription, #api, #template, #webhook

![stripe_logo](https://camo.githubusercontent.com/1e61b8e2403fb738f3ebac57c2e6003f164ee15a5b66a39c229ce51ddbbeb186/68747470733a2f2f6c6f676f732d776f726c642e6e65742f77702d636f6e74656e742f75706c6f6164732f323032312f30332f5374726970652d53796d626f6c2e706e67)

## Integrating Stripe webhooks into a Next.js application can be a game-changer, especially for e-commerce platforms. At the same time it can be a nightmare. In this blog post, I'll share a comprehensive guide on setting up a route to handle Stripe Webhooks in Next.js.

# The Setup
Let's start by cloning the webhook file to your project. Using curl and a few other commands we can quickly get the file cloned over into your project.

### MacOS/Unix:
#### Terminal:
```
mkdir -p src/app/api/webhooks/billing/stripe
chmod -R 755 src/app/api/webhooks/billing/stripe
curl -o src/app/api/webhooks/billing/stripe/route.ts https://raw.githubusercontent.com/tego101/nextjs-14-stripe-webhooks/main/stripe-webhooks.ts
```

### Windows PowerShell 😬:
```
New-Item -ItemType Directory -Force -Path .\src\app\api\webhooks\billing\stripe
curl -o .\src\app\api\webhooks\billing\stripe\route.ts https://raw.githubusercontent.com/tego101/nextjs-14-stripe-webhooks/main/stripe-webhooks.ts
```

## Defining Event Types

Here we will look over the event types in the file and apply our logic. 

### Here are the event types in our webhook file:
Stripe uses webhooks to notify your server about events that happen in your account, such as successful payments, customer updates, and more. These webhooks payloads have event identifiers, take a look below.

You can click on them to find out more about each event type.
- [Checkout Session Completed](https://stripe.com/docs/payments/checkout#checkout-session-completed)
- [Checkout Session Async Payment Succeeded](https://stripe.com/docs/payments/checkout#checkout-session-async-payment-succeeded)
- [Checkout Session Async Payment Failed](https://stripe.com/docs/payments/checkout#checkout-session-async-payment-failed)
- [Checkout Session Expired](https://stripe.com/docs/payments/checkout#checkout-session-expired)
- [Charge Succeeded](https://stripe.com/docs/api/charges#charge-succeeded)
- [Charge Failed](https://stripe.com/docs/api/charges#charge-failed)
- [Charge Refunded](https://stripe.com/docs/api/charges#charge-refunded)
- [Charge Expired](https://stripe.com/docs/api/charges#charge-expired)
- [Charge Dispute Created](https://stripe.com/docs/disputes#charge-dispute-created)
- [Charge Dispute Updated](https://stripe.com/docs/disputes#charge-dispute-updated)
- [Charge Dispute Funds Reinstated](https://stripe.com/docs/disputes#charge-dispute-funds-reinstated)
- [Charge Dispute Funds Withdrawn](https://stripe.com/docs/disputes#charge-dispute-funds-withdrawn)
- [Charge Dispute Closed](https://stripe.com/docs/disputes#charge-dispute-closed)
- [Customer Created](https://stripe.com/docs/api/customers#customer-created)
- [Customer Updated](https://stripe.com/docs/api/customers#customer-updated)
- [Customer Deleted](https://stripe.com/docs/api/customers#customer-deleted)
- [Customer Subscription Created](https://stripe.com/docs/api/customers#customer-subscription-created)
- [Customer Subscription Updated](https://stripe.com/docs/api/customers#customer-subscription-updated)
- [Customer Subscription Deleted](https://stripe.com/docs/api/customers#customer-subscription-deleted)
- [Customer Subscription Paused](https://stripe.com/docs/api/customers#customer-subscription-paused)
- [Customer Subscription Resumed](https://stripe.com/docs/api/customers#customer-subscription-resumed)

## Handling Events.
For this I highly recommend using [NextJS Server Actions](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations). These serverside functions are game changing and allow you to securely do things like handle billing without exposing routes or info.

In my case this is how I handled the **"checkout.session.completed"** event. I found the event case in the webhook file and I added my logic to it using [Server Actions](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations).

```
import {
    getInvoice,
    addInvoicePayment,
    markInvoicePaid,
    getInvoicePayment,
    getInvoicePaymentByStripeID,
    updateInvoicePayment,
    
} from '@/actions/invoice-actions.ts';

import {
    renewOrCreateService,
} from '@/actions/subscriber-service-actions.ts';

case "checkout.session.completed":
    if (status !== "paid") {
        // TODO: Failed Payment.
        
        return new Response(
            JSON.stringify({ error: "Payment not completed!" }),
            {
                status: 500,
            }
        );
    }

    if (meta?.is_service_renewal) {
        // TODO: Handle service renewal.
    }

    // Find invoicePayment by Stripe Checkout Session ID.
    const invoicePayment = await getInvoicePaymentByStripeID(body.data?.object?.client_reference_id.toString())

    if (!invoicePayment) {
        return new Response(JSON.stringify({ error: "Payment not found!" }), {
            status: 500,
        });
    }

    // Add payment_intent to invoicePayment.meta
    const updatedInvoicePayment = await addInvoicePayment({
            status: "PAID",
            stripeCheckoutSessionId: id,
            stripeCheckoutPaymentIntentId: body.data?.object?.payment_intent ?? null,
            stripeCheckoutInvoiceId: stripe_invoice ?? null,
    });
    
    // Update invoice status to paid
    const updateInvoice = await markInvoicePaid(
        invoicePayment.invoiceId,
        {
            stripeCheckoutSessionId: id,
            stripeCheckoutInvoiceId: stripe_invoice ?? payment_intent,
        },
    });

    // Activate service and set expiresAt to the plan.duration & plan.durationUnit + now
    // If mode is set to subscription, set the stripeSubscriptionId to the subId
    // Finally, set the status to ACTIVE
    const service = await renewOrCreateService(meta?.serviceId,
        {
            expiresAt: calculateExpirationDate(invoice?.plan as Plan),
            status: "ACTIVE",
            stripeSubscriptionId: mode !== "payment" ? subId : null,
            meta: {
                stripe_invoice: stripe_invoice,
                stripe_checkout_session_id: id,
                stripe_checkout_invoice_id: stripe_invoice,
                stripe_payment_intent: payment_intent,
            },
        },
    });

    // Email the user to let them know their subscription is active.
    // Resend sendSubWelcomeEmail() server action here.
    return new Response(JSON.stringify({ message: "Payment completed!" }), {
        status: 200,
    });
```

In this snippet I update the invoice with the new stripe payment id. I also create a new Invoice Payment and store that aswell, right before I renew or create the subscription for the subscriber.

## Go ahead and Explore!

Inspect the webhook file and add in your server actions or logic you have for your app. I have included a few console.log events in the file so you may get a visual of what data is coming in when the webhook is triggered.

### Also, Don't forget to set your environment values:
**STRIPE_WEBHOOK_SECRET** : The secret key ensures the security and integrity of the webhook payloads.


**Hope this helps someone,**
-- Tego
