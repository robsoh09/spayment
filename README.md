Assignment: Rob Soh Ming Yang 

This document explains how I approach a take-home payment assignment from a presales perspective. My focus is to keep the solution torwards the business needs.

1. Start With the Goal and functional requirements 

Before proceed I first clarify:
What the business is trying to do  
What a successful outcome (in this case, customer payment) 

This helps me avoid overbuilding a demo when its required and keeps the solution straightforard.

2. Business needs to Business logic 
I define a flow and map it accordingly. In the assignment: 
Customer -> Purchase -> Submits Payment -> Return Success / Failure  

This makes it easy for both the business and tech  to follow what is happening.

3. Show only what is required
   
I only set up what is required for the use case:
One-time payments which may include basic success and simple failure handling  
confirmation of payment status  

This keeps the demo realistic enough

4. Build a Working Demo

My priority is to deliver outcomes mapped to requirements:

One successful payment  
A confirmation outcome   
A smooth user experience  

5. Explain simply

I make sure I explain:
What happens at each step and why it is used for
A visual representation at each step aligned to the business outcome 


6.  Next Steps
   
my focus is to ensure customer can have the best understanding and seeing is believing 
A working demo provides this approach. Once this is confirmed, i will present the common risks(if any) and will proceed to the next step with the customer.

Solution learning process:

1. The requirement is to build a payment service using Stripe Elements for Stripe Press. 
Acting as a newly onboarded Solution Engineer, I went through [docs.stripe.com ](https://docs.stripe.com/get-started)
Steps I have taken to familarize with the Stripe account onboarding: 
created a Stripe Account and login to the Dashboard
Enabled payments on the Dashboard
Enabled API keys for dev work 


2. API understanding
Docs.stripe.com provided a starter guide 
https://docs.stripe.com/get-started/development-environment. I used this to evaluate using the nodejs sdk and API endpoints response. 
I also started a mockup of the Stripe Press Assiignment architecture as i went along with the sdk learning. This was done using Stripe Customer Case Studies. 

The next progression was to use online payments as most of the e-commerce payment requirements. 
Stripe has a series of quick-starts and dev pages which shortened the learning curve: 

https://docs.stripe.com/payments/quickstart 
https://docs.stripe.com/payments/online-payments#compare-features-and-availability
https://docs.stripe.com/payments/use-cases/get-started 
PaymentIntent was required and has the most flexibility in terms of integration but tradeoff in more dev work. 
Stripe is also very focused on webhook integration (Stripe CLI webhook local forwarding is a cool function), which is what i used in my current job to automate business workflows. 

With this I was able to start work on the assignment Stripe Press Integration. I have also attached a Customer Solution Demo overview which allows highlight the project requirements from a POV perspective. 

The stack i chose was nodejs/express/ and I didnt fully understood the API responses yet.Before creating any code, I strived to understand how payment Intent would work. For instance, Stripe docs referred to client-front-end and Server-back-end. This is the section where I was confused on initially.The Front-end will use the publishableKey to interact with Stripe.js e.g creating the payment UI form and the secret key is used for sensistive operations to confirm payment. 

Therefore, I tested this a few times to understand the approach - for instance, the client_secret is required to submit or stripe.confirmPayment sdk. using Stripe.dev Github samples, 
https://github.com/stripe-samples/accept-a-payment/blob/main/custom-payment-flow/server/node/server.js, i was able to understand the approach better. 

The outout is returned for a successful payment below: 
Payment Intent created: pi_3SarNA60m4YgZHoW1u66hQU7
Secret pi_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
pi_3SarNA60m4YgZHoW1u66hQU7
{
  id: 'pi_3SarNA60m4YgZHoW1u66hQU7',
  object: 'payment_intent',
  amount: 2500,
  amount_capturable: 0,
  amount_details: { tip: {} },
  amount_received: 2500,
  application: null,
  application_fee_amount: null,
  automatic_payment_methods: { allow_redirects: 'always', enabled: true },
  canceled_at: null,
  cancellation_reason: null,
  capture_method: 'automatic_async',
  client_secret: 'pi_',
  confirmation_method: 'automatic',
  created: 1764911764,
  currency: 'usd',
  customer: null,
  description: null,
  excluded_payment_method_types: null,
  last_payment_error: null,
  latest_charge: 'ch_3SarNA60m4YgZHoW1awxqL2F',
  livemode: false,
  metadata: {},
  next_action: null,
  on_behalf_of: null,
  payment_method: 'pm_1SarNR60m4YgZHoWM0STP5wS',
  payment_method_configuration_details: { id: 'pmc_1SNZw860m4YgZHoWjKthcX5D', parent: null },
  payment_method_options: {
    card: {
      installments: null,
      mandate_options: null,
      network: null,
      request_three_d_secure: 'automatic'
    },
    link: { persistent_token: null }
  },
  payment_method_types: [ 'card', 'link' ],
  processing: null,
  receipt_email: null,
  review: null,
  setup_future_usage: null,
  shipping: null,
  source: null,
  statement_descriptor: null,
  statement_descriptor_suffix: null,
  status: 'succeeded',
  transfer_data: null,
  transfer_group: null
}

3. Define Solution Scope 

The scope of the solution in this case:

A customer will order  the product, go to the payment landing page, enter required details and submit an order request. 

Stripe Press frontend Client route  generates the intended parameters and relays to the backend route 

The backend route will in turn, make a Stripe PaymentIntent system call to Stripe 

Stripe returns a new Payment Intent to the e-store frontend 

Customer will provide payment billing detail like credit card number  and submits the payment 

Stripe will proceed to process the payment and return the payment status via webhook events, triggering business workflows. 

The front end is updated with the payment status for the client. 

4. Implementation & Outcome 
Based on this i have added more routes to the existing assignment code, this is by far, the fastest route to showcase a working payment process based on https://docs.stripe.com/payments/quickstart. 
As in an actual production system, there would be a database to store item purchase and payment Id records, i used a global variable to simulate this in the backend service to return payment status when a successful 
payment was conducted. 

The APIs used in this demo: 
Stripe.paymentIntent creation whenever a user clicked on the checkout flow using a Purchase button link -> http://localhost:3000/checkout?item=1

the backend service will invoke this code:
     try {
      const paymentIntent = await stripe.paymentIntents.create({
        amount: amount, // Amount in cents Required Field
        currency: 'usd', // Currency Required Field 
        automatic_payment_methods: {
          enabled: true,
          },
        });

This creates an Intent with the amount required and currency in USD. 
The paymentIntentId is then stored in the global variable in later section. 

On the frontend, it will proceed to load the payment form in checkout.hbs - i embedded this directly in checkout.hbs as a fast demo. 

 <script> const stripe = Stripe('{{spk}}');
              const options = {
              clientSecret: '{{client_secret}}',
              }; 
             const elements = stripe.elements(options); 
             const paymentElement = elements.create('payment');
              paymentElement.mount('#payment-element') </script> 
            
This mounts the payment element into the form, the clientSecret is loaded as an option so when the user clicks on the paybutton, the form will commit and transmits - this is written as a client side 
checkout.js script. I could have combined the above script into checkout.js as a matter of fact. 

Stripe.ConfirmPayment 

const {error} = await stripe.confirmPayment({
    //elements instance that was used to create the Payment Element
    elements,
    confirmParams: {
      return_url: 'http://localhost:3000/success',
    },
  });

  if (error) {
    const messageContainer = document.querySelector('#error-message');
    messageContainer.textContent = error.message;
  }

This returns confirmPayment sdk call and since elements have already a clientSecret called upon, the payment was succesful and got redirected to success. 

at the success page, 
i have included the below handlebar responses on the frontend route 
      <p>Payment Id: {{paymentId}}</p>
      <p>Book title: {{bookTitle}}</p>
      <p>Paid: ${{paymentValue}} USD</p>
      <p>Status: {{paymentStatus}}</p>
      <br>
      <p>Thank you for your purchase</p>
    </div>
  </div>
</main>

on the backend success route: 
a sdk call was made to retrieve the pid or paymentIntentid and get the payment response and status. 
const paymentIntent = await stripe.paymentIntents.retrieve(
  pid
   );
  console.log(paymentIntent);
  let paymentValue = (paymentIntent.amount * 0.01);
  let paymentId = (paymentIntent.id);
  let paymentStatus = (paymentIntent.status);
  console.log(paymentValue);
  res.render('success', {
    bookTitle: bookTitle,
    paymentValue: paymentValue,
    paymentId: paymentId,
    paymentStatus: paymentStatus


  });


Outcome: 
http://localhost:3000/success?payment_intent=pi_3SaxVc60m4YgZHoW1H1IbS8o&redirect_status=succeeded is the result when a payment is made successfully via Stripe Elements. 
Information such as book title, payment value and the payment intent Id and status were displayed.  


5. Additional Scope I would like to add:
This only showcases payment but not adding of shipping address, billing or taxes. An actual payment will require all these information and also sending of email notifications once the order is completed.
Webhook integration on business logic such as inventory mapping when a successful payment is conducted can be considered to help customers.


I have also included instructions to clone and run this in the customer demo solution PDF. 


  







