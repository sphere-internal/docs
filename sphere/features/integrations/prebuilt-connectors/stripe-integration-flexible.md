---
description: >-
  Configuring Stripe to use Sphere as a tax calculation provider for billing
  flows.
---

# Stripe Integration (Flexible)

Our Stripe app simplifies the configuration of your Stripe account, allowing you to get Sphere's Flexible tax calculation up and running in minutes. This guide provides step-by-step instructions on how to download Sphere's Stripe app and turn on live tax calculation within specific Stripe products.

### Part 1: Download Sphere's Stripe App

1. In your Sphere account click **Connect** on the Stripe tile.

<figure><img src="../../../.gitbook/assets/Stripe (3).png" alt=""><figcaption></figcaption></figure>

2. You will be redirected to a new tab where you will be required to **select your Stripe account** that should be integrated with Sphere.

<figure><img src="../../../.gitbook/assets/Group 81361.png" alt=""><figcaption></figcaption></figure>

3. Select **Continue** to install Sphere's Stripe app.

<figure><img src="../../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

4. If the connection is successful, you'll see a success message on screen and you'll be redirected back to the Sphere app.
5.  Ensure all your 'Subscribed Regions' in Sphere have 'Tax Calculations' settings switched to Yes (see video [here](https://www.loom.com/share/6254e72b130c44808a59513046ee60ea?sid=463c87f3-0683-4622-a479-0f960620d332) on how to ensure this is done).<br>

    <figure><img src="../../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

### Part 2: Disable automatic tax calculation in your Stripe account

1.  Once you have the Stripe app installed (refer to Part 1), go to your Stripe Dashboard, click on the gear icon in the top right hand corner of your screen, click Tax, go to the Integrations tab and ensure that the automatic tax' toggle is switched off.<br>

    <figure><img src="../../../.gitbook/assets/Screenshot 2026-09-04 at 12.03.35 pm (1).png" alt=""><figcaption></figcaption></figure>



    <div data-gb-custom-block data-tag="hint" data-style="info" class="hint hint-info"><p>Note: If you create invoices / subscriptions / checkout sessions via Stripe's API you must ensure that the <code>automatic_tax.enabled</code> parameter is set to <code>false</code> (more info <a href="https://docs.stripe.com/api/subscriptions/create#create_subscription-automatic_tax">here</a>).</p></div>
2. Reach out to your Sphere representative, and ask them to enable Flexible tax calculation for you.
3. Once all the above steps are complete, be sure to test out your existing billing flows and reach out to your Sphere representative if you have any issues or questions.





