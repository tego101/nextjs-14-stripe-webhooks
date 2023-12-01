<h1 class="lg:text-4xl font-bold">Integrating and Handling Stripe Webhooks in Next.js</h1>
<div class="w-128 h-128 my-4 bg-gradient-to-bl from-blue-400 to-blue-800 shadow-2xl rounded-xl"> 
	<img src="/peek.png">
</div>
<p class="text-slate-700">
Integrating Stripe webhooks into a Next.js application can be a game-changer, especially for e-commerce platforms. At the same time it can be a nightmare. In this blog post, I'll share a comprehensive guide on setting up a route to handle Stripe Webhooks in Next.js.
</p>
<br>
<h1 class="text-4xl font-bold">The Setup</h1>
<p class="text-slate-700">
Let's start by setting up the route file: <span class="bg-green-500/80 text-white p-1 text-sm px-2 border border-green-500 font-light rounded">src/app/api/webhooks/stripe/route.ts</span>. This is where we'll handle incoming Stripe Webhook events.
</p>
<br>
<h1 class="text-4xl font-bold">Importing Modules</h1>
<p class="text-slate-700">First, we need to import the necessary modules:</p>
<br>
<pre><div class="bg-black rounded-md"><div class="flex items-center relative text-gray-200 bg-gray-800 dark:bg-token-surface-primary px-4 py-2 text-xs font-sans justify-between rounded-t-md"><span>typescript</span><button class="flex gap-1 items-center"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon-sm"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 4C10.8954 4 10 4.89543 10 6H14C14 4.89543 13.1046 4 12 4ZM8.53513 4C9.22675 2.8044 10.5194 2 12 2C13.4806 2 14.7733 2.8044 15.4649 4H17C18.6569 4 20 5.34315 20 7V19C20 20.6569 18.6569 22 17 22H7C5.34315 22 4 20.6569 4 19V7C4 5.34315 5.34315 4 7 4H8.53513ZM8 6H7C6.44772 6 6 6.44772 6 7V19C6 19.5523 6.44772 20 7 20H17C17.5523 20 18 19.5523 18 19V7C18 6.44772 17.5523 6 17 6H16C16 7.10457 15.1046 8 14 8H10C8.89543 8 8 7.10457 8 6Z" fill="currentColor"></path></svg>Copy code</button></div><div class="p-4 overflow-y-auto"><code class="!whitespace-pre hljs  text-indigo-300 language-typescript">import Stripe from "stripe";
import { NextApiRequest, NextApiResponse } from "next";
</code></div></div></pre>
<br>
<h1 class="text-4xl font-bold">Defining Event Types</h1>
<p class="text-slate-700">It's essential to define the types of events we expect from Stripe:</p>
<br>
<pre><div class="bg-black rounded-md"><div class="flex items-center relative text-white bg-gray-800 dark:bg-token-surface-primary px-4 py-2 text-xs font-sans justify-between rounded-t-md"><span>typescript</span><button class="flex gap-1 items-center"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon-sm"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 4C10.8954 4 10 4.89543 10 6H14C14 4.89543 13.1046 4 12 4ZM8.53513 4C9.22675 2.8044 10.5194 2 12 2C13.4806 2 14.7733 2.8044 15.4649 4H17C18.6569 4 20 5.34315 20 7V19C20 20.6569 18.6569 22 17 22H7C5.34315 22 4 20.6569 4 19V7C4 5.34315 5.34315 4 7 4H8.53513ZM8 6H7C6.44772 6 6 6.44772 6 7V19C6 19.5523 6.44772 20 7 20H17C17.5523 20 18 19.5523 18 19V7C18 6.44772 17.5523 6 17 6H16C16 7.10457 15.1046 8 14 8H10C8.89543 8 8 7.10457 8 6Z" fill="currentColor"></path></svg>Copy code</button></div><div class="p-4 overflow-y-auto"><code class="!whitespace-pre hljs  text-indigo-300 text-indigo-300 language-typescript">type EventName =
    | "checkout.session.completed"
    | "charge.succeeded"
    // ...more event types
</code></div></div></pre>
<br>
<h1 class="text-4xl font-bold">Handling Webhook Events</h1>
<p class="text-slate-500">The `handleStripeWebhook` function processes the webhook body and executes logic based on the event type:</p>
<br>
<pre><div class="bg-black rounded-md"><div class="flex items-center relative text-gray-200 bg-gray-800 dark:bg-token-surface-primary px-4 py-2 text-xs font-sans justify-between rounded-t-md"><span>typescript</span><button class="flex gap-1 items-center"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon-sm"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 4C10.8954 4 10 4.89543 10 6H14C14 4.89543 13.1046 4 12 4ZM8.53513 4C9.22675 2.8044 10.5194 2 12 2C13.4806 2 14.7733 2.8044 15.4649 4H17C18.6569 4 20 5.34315 20 7V19C20 20.6569 18.6569 22 17 22H7C5.34315 22 4 20.6569 4 19V7C4 5.34315 5.34315 4 7 4H8.53513ZM8 6H7C6.44772 6 6 6.44772 6 7V19C6 19.5523 6.44772 20 7 20H17C17.5523 20 18 19.5523 18 19V7C18 6.44772 17.5523 6 17 6H16C16 7.10457 15.1046 8 14 8H10C8.89543 8 8 7.10457 8 6Z" fill="currentColor"></path></svg>Copy code</button></div><div class="p-4 overflow-y-auto"><code class="!whitespace-pre hljs text-indigo-300 language-typescript">async function handleStripeWebhook(body: any) {
    const type = body.type;
    switch (type) {
        case "checkout.session.completed":
            // Logic for completed checkout session
            break;
        // ...other cases
    }
}
</code></div></div></pre>
<br><br>
<div class="flex flex-row items-center space-x-2 mx-10">
	<img src="https://cdn.worldvectorlogo.com/logos/postman.svg" class="h-24 w-24 mr-4">
	I've found it easier to build my methods in by executing request via Postman.
</div>
<br>
<h1 class="text-4xl font-bold">The POST Function</h1>
This function is designed to manage POST requests, which are typically webhook calls from Stripe:
<br><br>
<pre><div class="bg-black rounded-md"><div class="flex items-center relative text-gray-200 bg-gray-800 dark:bg-token-surface-primary px-4 py-2 text-xs font-sans justify-between rounded-t-md"><span>typescript</span><button class="flex gap-1 items-center"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon-sm"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 4C10.8954 4 10 4.89543 10 6H14C14 4.89543 13.1046 4 12 4ZM8.53513 4C9.22675 2.8044 10.5194 2 12 2C13.4806 2 14.7733 2.8044 15.4649 4H17C18.6569 4 20 5.34315 20 7V19C20 20.6569 18.6569 22 17 22H7C5.34315 22 4 20.6569 4 19V7C4 5.34315 5.34315 4 7 4H8.53513ZM8 6H7C6.44772 6 6 6.44772 6 7V19C6 19.5523 6.44772 20 7 20H17C17.5523 20 18 19.5523 18 19V7C18 6.44772 17.5523 6 17 6H16C16 7.10457 15.1046 8 14 8H10C8.89543 8 8 7.10457 8 6Z" fill="currentColor"></path></svg>Copy code</button></div><div class="p-4 overflow-y-auto"><code class="!whitespace-pre hljs text-indigo-300 language-typescript">async function POST(request: Request) {
    try {
        const rawBody = await request.text();
        const body = JSON.parse(rawBody);

        // Webhook signature verification
        const event = Stripe.webhooks.constructEvent(
            rawBody, request.headers.get("Stripe-Signature"), process.env.STRIPE_SECRET_KEY
        );

        return handleStripeWebhook(event);
    } catch (error) {
        console.error("Error in Stripe webhook handler:", error);
        return new Response(JSON.stringify({ error: "Webhook handler failed." }), {
            status: 500,
        });
    }
}
</code></div></div></pre>
<br>
<h1 class="text-4xl font-bold">The GET Function</h1>
<p class="text-slate-700">We handle GET requests simply by returning a 400 Bad Request, as these are not expected. Feel free to expand this endpoint if you need.:</p>
<br>
<pre><div class="bg-black rounded-md"><div class="flex items-center relative text-gray-200 bg-gray-800 dark:bg-token-surface-primary px-4 py-2 text-xs font-sans justify-between rounded-t-md"><span>typescript</span><button class="flex gap-1 items-center"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon-sm"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 4C10.8954 4 10 4.89543 10 6H14C14 4.89543 13.1046 4 12 4ZM8.53513 4C9.22675 2.8044 10.5194 2 12 2C13.4806 2 14.7733 2.8044 15.4649 4H17C18.6569 4 20 5.34315 20 7V19C20 20.6569 18.6569 22 17 22H7C5.34315 22 4 20.6569 4 19V7C4 5.34315 5.34315 4 7 4H8.53513ZM8 6H7C6.44772 6 6 6.44772 6 7V19C6 19.5523 6.44772 20 7 20H17C17.5523 20 18 19.5523 18 19V7C18 6.44772 17.5523 6 17 6H16C16 7.10457 15.1046 8 14 8H10C8.89543 8 8 7.10457 8 6Z" fill="currentColor"></path></svg>Copy code</button></div><div class="p-4 overflow-y-auto"><code class="!whitespace-pre hljs text-indigo-300 language-typescript">async function GET(request: NextApiRequest, response: NextApiResponse) {
    return response.status(400).json({ error: "Bad Request" });
}
</code></div></div></pre>
<br>
<h1 class="text-4xl font-bold">Wrapping Up</h1>
<br>
Integrating Stripe Webhooks into your Next.js application requires careful planning and execution. The above setup ensures that your application can respond appropriately to various payment-related events from Stripe, making it an invaluable asset for your e-commerce platform.
<br>
Remember, always test your implementation in a secure environment and handle sensitive data with care.
<br><br>
<p>
	Feel free to take a look at the source below and modify it to fit your project's needs.
</p>

**github**: <span class="text-indigo-500">[https://github.com/tego101/nextjs-14-stripe-webhooks](url)
<br><br>
<pre><div class="bg-black rounded-md"><div class="flex items-center relative text-gray-200 bg-gray-800 dark:bg-token-surface-primary px-4 py-2 text-xs font-sans justify-between rounded-t-md"><span>typescript</span><button class="flex gap-1 items-center"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon-sm"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 4C10.8954 4 10 4.89543 10 6H14C14 4.89543 13.1046 4 12 4ZM8.53513 4C9.22675 2.8044 10.5194 2 12 2C13.4806 2 14.7733 2.8044 15.4649 4H17C18.6569 4 20 5.34315 20 7V19C20 20.6569 18.6569 22 17 22H7C5.34315 22 4 20.6569 4 19V7C4 5.34315 5.34315 4 7 4H8.53513ZM8 6H7C6.44772 6 6 6.44772 6 7V19C6 19.5523 6.44772 20 7 20H17C17.5523 20 18 19.5523 18 19V7C18 6.44772 17.5523 6 17 6H16C16 7.10457 15.1046 8 14 8H10C8.89543 8 8 7.10457 8 6Z" fill="currentColor"></path></svg>Copy code</button></div><div class="p-4 overflow-y-auto"><code class="!whitespace-pre hljs text-indigo-300 language-typescript">/*
 * app/api/webhooks/billing/stripe/route.ts
 *
 *=================================================
 * Stripe Webhook Route.
 *=================================================
 * This route is used to handle Stripe Webhooks.
 * - https://stripe.com/docs/webhooks
 * @author gh/tego101
 * @version 1.0.0
 * @url https://github.com/tego101/nextjs-14-stripe-webhooks
 */
import Stripe from "stripe";
import { NextApiRequest, NextApiResponse } from "next";
 

type EventName =
	// Checkout: https://stripe.com/docs/payments/checkout
	| "checkout.session.completed"
	| "checkout.session.async_payment_succeeded"
	| "checkout.session.async_payment_failed"
	| "checkout.session.expired"
	// Charge: https://stripe.com/docs/api/charges
	| "charge.succeeded"
	| "charge.failed"
	| "charge.refunded"
	| "charge.expired"
	// Disputes: https://stripe.com/docs/disputes
	| "charge.dispute.created"
	| "charge.dispute.updated"
	| "charge.dispute.funds_reinstated"
	| "charge.dispute.funds_withdrawn"
	| "charge.dispute.closed"
	// Customer: https://stripe.com/docs/api/customers
	| "customer.created"
	| "customer.updated"
	| "customer.deleted"
	| "customer.subscription.created"
	| "customer.subscription.updated"
	| "customer.subscription.deleted"
	| "customer.subscription.paused"
	| "customer.subscription.resumed";

async function handleStripeWebhook(body: any) {
	const mode = body.data?.object?.mode;
	const id = body.data?.object?.id;
	const obj = body.data?.object?.object;
	const stat = body.data?.object?.status;
	const status = body.data?.object?.payment_status;
	const payment_intent = body.data?.object?.payment_intent;
	const subId = body.data?.object?.subscription;
	const stripeInvoiceId = body.data?.object?.invoice;
	const user = body.data?.object?.metadata?.userId;
	const meta = body.data?.object?.metadata;
	const stripe_invoice = body.data?.object?.invoice;
	const type = body.type;

	// console.log everything above REMOVE BEFORE PRODUCTION.
	console.log("mode --->", mode);
	console.log("webhook type --->", type);
	console.log("id --->", id);
	console.log("obj --->", obj);
	console.log("stat --->", stat);
	console.log("status --->", status);
	console.log("payment_intent --->", payment_intent);
	console.log("subId --->", subId);
	console.log("stripeInvoiceId --->", stripeInvoiceId);
	console.log("user --->", user);
	console.log("meta --->", meta);
	console.log("stripe_invoice --->", stripe_invoice);

	// Switch on the event type.
	switch (type) {
		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * Session Expired.
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a session expires.
		 */
		case "checkout.session.expired":
			// logic to handle expired sessions.

			return new Response(
				JSON.stringify({ message: "Payments marked canceled!" }),
				{
					status: 200,
				}
			);
		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * Charge: Succeeded.
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a payment is successful.
		 */
		case "charge.succeeded":
			// logic to handle successful charges.

			return new Response(JSON.stringify({ message: "Payment completed!" }), {
				status: 200,
			});
		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * Charge: Refunded.
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a refund is completed.
		 */
		case "charge.refunded":
			// logic to handle refunded charges.

			return new Response(JSON.stringify({ message: "Refund completed!" }), {
				status: 200,
			});
		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * Charge: Refunded.
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a charge fails.
		 */
		case "charge.failed":
			// logic to handle failed charges.

			return new Response(JSON.stringify({ message: "Payment Updated!" }), {
				status: 200,
			});
		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * Charge: Expired.
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a charge expires.
		 */
		case "charge.expired":
			// logic to handle expired charges.

			return new Response(JSON.stringify({ message: "Payment Updated!" }), {
				status: 200,
			});
		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * Charge Dispute: Created.
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a dispute is created.
		 */
		case "charge.dispute.created":
			// logic here...

			return new Response(
				JSON.stringify({ message: "Dispute details added!" }),
				{
					status: 200,
				}
			);
		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * Charge Dispute: Updated.
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a dispute is created.
		 */
		case "charge.dispute.updated":
			// logic here...

			return new Response(
				JSON.stringify({ message: "Dispute details updated!" }),
				{
					status: 200,
				}
			);
		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * Charge Dispute: Funds re-instated.
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a dispute\'s funds are re-instated.
		 */
		case "charge.dispute.funds_reinstated":
			// logic here..

			return new Response(
				JSON.stringify({ message: "Dispute details updated!" }),
				{
					status: 200,
				}
			);
		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * Charge Dispute: Funds withdrawn.
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a dispute\'s funds are withdrawn.
		 */
		case "charge.dispute.funds_withdrawn":
			// logic here...

			return new Response(
				JSON.stringify({ message: "Dispute details updated!" }),
				{
					status: 200,
				}
			);
		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * Customer: Created
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a new customer is created.
		 */
		case "customer.created":
			// Add logic for handling customer creation
			return new Response(JSON.stringify({ message: "Customer created!" }), {
				status: 200,
			});

		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * Customer: Updated
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a customer's details are updated.
		 */
		case "customer.updated":
			// Add logic for handling customer updates
			return new Response(JSON.stringify({ message: "Customer updated!" }), {
				status: 200,
			});

		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * Customer: Deleted
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a customer is deleted.
		 */
		case "customer.deleted":
			// Add logic for handling customer deletion
			return new Response(JSON.stringify({ message: "Customer deleted!" }), {
				status: 200,
			});

		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * Customer Subscription: Created
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a customer's subscription is created.
		 */
		case "customer.subscription.created":
			// Add logic for handling the creation of a customer subscription
			return new Response(
				JSON.stringify({ message: "Customer subscription created!" }),
				{
					status: 200,
				}
			);

		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * Customer Subscription: Updated
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a customer's subscription is updated.
		 */
		case "customer.subscription.updated":
			// Add logic for handling updates to a customer's subscription
			return new Response(
				JSON.stringify({ message: "Customer subscription updated!" }),
				{
					status: 200,
				}
			);

		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * Customer Subscription: Deleted
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a customer's subscription is deleted.
		 */
		case "customer.subscription.deleted":
			// Add logic for handling the deletion of a customer's subscription
			return new Response(
				JSON.stringify({ message: "Customer subscription deleted!" }),
				{
					status: 200,
				}
			);

		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * Customer Subscription: Paused
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a customer's subscription is paused.
		 */
		case "customer.subscription.paused":
			// Add logic for handling the pausing of a customer's subscription
			return new Response(
				JSON.stringify({ message: "Customer subscription paused!" }),
				{
					status: 200,
				}
			);

		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * Customer Subscription: Resumed
		 * =~~~~~~~~~~~~~~~~~~~~~~~~=
		 * This is the webhook that is fired when a customer's subscription is resumed.
		 */
		case "customer.subscription.resumed":
			// Add logic for handling the resumption of a customer's subscription
			return new Response(
				JSON.stringify({ message: "Customer subscription resumed!" }),
				{
					status: 200,
				}
			);

		/*
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 * Default
		 * =~~~~~~~~~~~~~~~~~~~~~~~=
		 */
		default:
			return new Response(JSON.stringify({ error: "Invalid event type" }), {
				status: 400,
			});
	}
}

async function POST(request: Request) {
	try {
		// Request Body.
		const rawBody = await request.text();
		const body = JSON.parse(rawBody);

		let event;

		// Verify the webhook signature
		try {
			const stripeWebhookSecret = process.env.STRIPE_SECRET_KEY;
			if (!stripeWebhookSecret) {
				throw new Error("STRIPE_WEBHOOK_SECRET not set");
			}

			const sig = request.headers.get("Stripe-Signature");
			if (!sig) {
				throw new Error("Stripe Signature missing");
			}

			// Assuming you have a Stripe instance configured
			event = Stripe.webhooks.constructEvent(rawBody, sig, stripeWebhookSecret);
		} catch (err) {
			console.error(`⚠️  Webhook signature verification failed.`, err.message);
			return new Response(
				JSON.stringify({ error: "Webhook signature verification failed" }),
				{
					status: 400,
				}
			);
		}

		const webhookResponse = await handleStripeWebhook(event); // Ensure handleStripeWebhook is properly implemented

		return new Response(webhookResponse?.body, {
			status: webhookResponse?.status || 200,
		});
	} catch (error) {
		console.error("Error in Stripe webhook handler:", error);
		return new Response(JSON.stringify({ error: "Webhook handler failed." }), {
			status: 500, // Changed to 500, indicating a server error
		});
	}
}

async function GET(request: NextApiRequest, response: NextApiResponse) {
	// Bad Request or how ever you want to respond.
	return response.status(400).json({ error: "Bad Request" });
}

export { POST, GET };

</code></div></div></pre>

---
<br><br>
**-Tego**<br>
*I hope this helps someone.*
