---
description: >-
  Monitoring allows you to keep track of your global tax exposure so that you
  can form a plan around becoming compliant.
icon: earth-africa
---

# Monitoring

{% embed url="https://drive.google.com/file/d/146njlDW1O6X1dwiVehybESSBfWszHe5k/view?usp=drive_link" %}
Walkthrough of the Monitoring feature in Sphere
{% endembed %}

## Overview&#x20;

Our Monitoring feature extracts data from both your billing and HRIS provider to give you a view of where you are exposed around the world from an indirect tax compliance perspective.

We sync your data on a daily basis so that your exposure is kept up to date vs having to pay for expensive, periodic nexus studies from tax advisors / accountants.&#x20;

We explain below the key items and concepts that constitute the Monitoring feature below.

## Regions

A region is anywhere you register and file for indirect tax:

:flag\_us: **United States**&#x20;

* Each individual state is a region
* Home rule states (like CO, AZ, AK, LA, AL) count as one region each as we file via a centralized system
* The only state which requires multiple registrations is Illinois as Chicago has a seperate form of indirect tax (specific to software) called the PPLTT which requires its own registration / filing.

:flag\_ca: **Canada**&#x20;

* GST / HST (federal level tax) = 1 region&#x20;
* PST (provincial taxes in British Columbia, Manitoba, Saskatchewan, Quebec) = each count as 1 region as they require seperate registrations / filings.

:flag\_eu: **Europe** = 1 region as we file via the One Stop Shop scheme (which allows us to report all EU transactions and tax via 1 member state)

:earth\_americas: **Rest of World** = typically each country is 1 region (although there are exceptions)

***

## Nexus

An entity is generally subject to a region’s sales and use tax requirements if that entity has ‘_nexus_’ with the jurisdiction. Generally, nexus is the minimum necessary connection that an entity has with a region that allows that region to impose a tax or, in the case of sales and use tax, a collection obligation on that entity (nexus is largely equivalent to the VAT concept of “permanent establishment”).

Nexus is generally obtained, or triggered, by either a physical in-state presence or an economic presence.

### Physical presence

A physical presence can be triggered by several factors, including the presence of property or payroll in a region. Property, for example, can be considered as maintaining an office, warehouse, distribution center, or a manufacturing plant, whether leased or owned. Property can also be considered as maintaining, using, or storing fixed assets or inventory. Payroll can be considered as maintaining employees or independent contractors, whether full-time, part-time, or temporarily in the region.

Sphere integrates with your HRIS provider to track your FTEs and contractors around the world to see where you have physical presence. This is denoted in the _Physical Presence_ column in the Monitoring feature.

<figure><img src="../.gitbook/assets/Monitor (3).png" alt=""><figcaption><p>Physical presence column in Monitoring</p></figcaption></figure>

### Economic presence&#x20;

An economic presence can be triggered by maintaining a certain sales volume within a region, based on customer location. This sales volume is typically measured by either a gross sales volume (measured in local curency) or a transactional sales volume (measured by count of sales transactions), on an annual basis. Though it can vary greatly from region to region, the typical threshold for an economic presence in the US is $100,000 or 100 sales transactions over a calendar year period. There are some regions that maintain a higher gross sales volume threshold, maintain no sales transaction threshold, or measure their determination period on a rolling annual basis.

Sphere keeps track of all of these economic thresholds globally and integrates with your various billing systems to track where you are approaching or have breached these thresholds.&#x20;

If a region has an _Approaching Exposure_ status (see definitions [below](monitoring.md#monitoring-statuses)), we will show you your progress towards the threshold via the _Nexus Tracker_ column. Note that _Volume ($)_ and _Volume (#)_ denotes volume over a specific determination period that is specific to the region (this can be the prior calendar year, rolling last twelve months etc).

<figure><img src="../.gitbook/assets/Approaching exposure.png" alt=""><figcaption><p>Economic nexus calculation and tracking in Monitoring</p></figcaption></figure>

### Tax liabilities&#x20;

If your product is taxable in a region and you breach either economic or physical nexus, you will start accumulating a tax liability from the day you first breached nexus.&#x20;

Sphere provides you with the exact date where you breach nexus and calculates the tax liability you owe from that date (via the _Nexus Triggered Date_ and _Estimated Tax Liability_ column, respectively).

<figure><img src="../.gitbook/assets/Exposed (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Note that the _Estimated Tax Liability_ column **does not include** the following additional costs:&#x20;

* Late filing penalties - these can be either a % of the tax liability or a flat fee per filing
* Interest on tax liability - usually % applied to the tax liability which compounds over time
{% endhint %}

## Monitoring statuses

Each region is assigned a Monitoring status based on your exposure in that region:

<table><thead><tr><th width="162">Status</th><th width="670">Explanation</th></tr></thead><tbody><tr><td><img src="../.gitbook/assets/Badge (5) (1).svg" alt="" data-size="line"></td><td>No sales in the region</td></tr><tr><td><img src="../.gitbook/assets/Badge (1) (1).svg" alt="" data-size="line"></td><td><p>You have breached economic or physical nexus </p><p></p><p>Your products are NOT taxable in the region </p><p></p><p>Technically you are meant to register and file $0 returns in these regions but a lot of businesses opt not to as there is NO tax liability associated with the region</p></td></tr><tr><td><img src="../.gitbook/assets/Badge (2) (1).svg" alt="" data-size="line"></td><td><p>You are approaching the nexus threshold (we will provide a progress bar that shows how close you are to breaching the threshold)</p><p></p><p>Your products ARE taxable in the region</p><p></p><p>We suggest registering once you pass 60% exposure in these regions OR you know you will hire someone in the region in the next month</p></td></tr><tr><td><img src="../.gitbook/assets/Badge (6).svg" alt="" data-size="line"></td><td><p>You have breached the either economic or physical nexus and are accumulating a tax liability </p><p></p><p>Your products ARE taxable in the region</p><p></p><p>We suggest registering in these regions ASAP as you will both need to pay the liability + associated penalties and interest </p></td></tr><tr><td><img src="../.gitbook/assets/Badge (4) (1).svg" alt="" data-size="line"></td><td>You have registered in the region but you have NOT enabled auto filing </td></tr><tr><td><img src="../.gitbook/assets/Badge (3) (1).svg" alt="" data-size="line"></td><td>You have registered in the region and HAVE enable auto filing</td></tr></tbody></table>

&#x20;

