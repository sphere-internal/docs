# Stripe Integration (Native)

Downloading Sphere's Stripe app enables a.) seamless transaction data import and b.) live tax calculation within Stripe Billing and Checkout products. Speak with your Sphere account rep to determine your eligibility.

This guide provides step-by-step instructions on how to download Sphere's Stripe app and turn on live tax calculation within Stripe products.

### Part 1: Download Sphere's Stripe App

1. In your Sphere account click **Connect** on the Stripe tile.

<figure><img src="../../../.gitbook/assets/Stripe (3).png" alt=""><figcaption></figcaption></figure>

2. You will be redirected to a new tab where you will be required to **select your Stripe account** that should be integrated with Sphere.

<figure><img src="../../../.gitbook/assets/Group 81361.png" alt=""><figcaption></figcaption></figure>

3. Select **Continue** to install Sphere's Stripe app.

<figure><img src="../../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

4. If the connection is successful, you'll see a success message on screen and you'll be redirected back to the Sphere app.

### Part 2: Enable automatic tax calculation in your Stripe account

1. Ensure all your 'Subscribed Regions' in Sphere have 'Tax Calculations' settings switched to Yes (see video [here](https://www.loom.com/share/6254e72b130c44808a59513046ee60ea?sid=463c87f3-0683-4622-a479-0f960620d332) on how to ensure this is done).

<div><figure><img src="../../../.gitbook/assets/Calc_new (1).png" alt=""><figcaption></figcaption></figure> <figure><img src="../../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure></div>

2.  Once you have the Stripe app installed (refer to Part 1), go to your Stripe Dashboard, click on the gear icon in the top right hand corner of your screen, click Tax, go to the Integrations tab and ensure that the automatic tax' toggle is switched on\
    <br>

    <figure><img src="../../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>
3. Then navigate to the Advanced options tab and ensure your Tax calculation provider is set to Sphere

<figure><img src="../../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Note: If you create invoices / subscriptions / checkout sessions via Stripe's API you must ensure that the `automatic_tax` parameter is set to `enabled` (more info [here](https://docs.stripe.com/api/subscriptions/create#create_subscription-automatic_tax)).
{% endhint %}

4. In the Business information tab, ensure your tax inclusive / exclusive pricing setting in Stripe is set to 'Automatic' (this ensures tax inclusive pricing in regions that require it, e.g. EU, UK etc).

<figure><img src="../../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

5. Once all the above steps are complete, be sure to test out your existing billing flows and reach out to your Sphere representative if you have any issues or questions.
