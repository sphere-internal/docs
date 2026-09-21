# Tabs Integration

Integrating Tabs with Sphere enables seamless transaction data import and live tax calculation within Tabs.&#x20;

This guide provides step-by-step instructions on generating an API key in Tabs, linking it to Sphere for a secure integration.

It also covers configuring the Sphere Tax API within Tabs to ensure accurate tax calculations.\
\
**Testing:** To test the integration, we recommend connecting your Tabs sandbox tenant to a test Sphere organization to confirm. If you do not have Tabs sandbox tenant, please contact your Zuora account team to provision one.

### Part 1: Configure Data Synchronization from Tabs to Sphere

1. In your Sphere account, click on the Connect button on the Zuora tile.&#x20;

<figure><img src="../../../.gitbook/assets/Screenshot 2026-07-02 at 11.39.59 AM (1).png" alt=""><figcaption></figcaption></figure>

2. In another window, open your Tabs account and navigate to **Developers** tab on the side navigation bar.

<figure><img src="../../../.gitbook/assets/Screenshot 2026-07-10 at 1.00.20 PM.png" alt=""><figcaption></figcaption></figure>

3. Click the **+ Add API Key** button on the top right of the screen. On the popup, fill out the fields:
   1. **API Key Name** -> Description name of the API Key such as **Sphere Integration**
   2. **Access Level** -> Reporter (read-only)
   3. Click **Create API Key**

<figure><img src="../../../.gitbook/assets/Screenshot 2026-07-10 at 12.32.22 PM.png" alt=""><figcaption></figcaption></figure>

4. Click the **Copy** button to save the API Key to your clickboard and click **I've saved my key!**

<figure><img src="../../../.gitbook/assets/Screenshot 2026-07-10 at 12.35.18 PM.png" alt=""><figcaption></figcaption></figure>

5. Navigate back to your Sphere dashboard, and past the API Key into the Sphere integration and click **Next**

<figure><img src="../../../.gitbook/assets/Screenshot 2026-07-02 at 11.40.17 AM (1).png" alt=""><figcaption></figcaption></figure>

6. In order for us to track liability of your historical transactions, click the **Connect to Quickbooks** button

<figure><img src="../../../.gitbook/assets/Screenshot 2026-07-02 at 11.40.55 AM (1).png" alt=""><figcaption></figcaption></figure>

7. Click **Generate API Key** and copy the Sphere API Key for Part 2 below

<figure><img src="../../../.gitbook/assets/Screenshot 2026-07-02 at 11.41.08 AM (1).png" alt=""><figcaption></figcaption></figure>

### Part 2: Configure Sphere Tax API Key for Tax Calculation

In order to configure tax calculation in Tabs, you will need to setup a sales tax item in your ERP for Tabs to use. Please refer to Tabs' documentation to configure this: [https://help.tabs.com/articles/4812993466-how-do-i-set-up-a-sales-tax-item-in-my-erp](https://help.tabs.com/articles/4812993466-how-do-i-set-up-a-sales-tax-item-in-my-erp)



1. Navigate back to your Tabs account, and select Integrations tab on the side navigation bar. Scroll down to the **Tax** section and click **Connect** on the Sphere integration

<figure><img src="../../../.gitbook/assets/Screenshot 2026-07-10 at 12.59.05 PM.png" alt=""><figcaption></figcaption></figure>

2. Copy the Sphere API Key saved in Part 1 and click **Connect**

<figure><img src="../../../.gitbook/assets/Screenshot 2026-07-10 at 12.56.05 PM.png" alt=""><figcaption></figcaption></figure>

3. Click **Edit** next to **Sales tax integration item**

<figure><img src="../../../.gitbook/assets/Screenshot 2026-07-10 at 12.57.13 PM.png" alt=""><figcaption></figcaption></figure>

4. Select the sales tax integration item from the drop down and click **Save**

<figure><img src="../../../.gitbook/assets/Screenshot 2026-07-10 at 12.57.58 PM.png" alt=""><figcaption></figcaption></figure>
