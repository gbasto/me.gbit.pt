---
title: "Public disclosure: Madeira not so Safe"
date: 2023-06-29 11:58:47 +00:00
modified: 2023-06-29 11:58:47 +00:00
tags: [public-disclosure, security]
description: Thousands of people who used Madeira Safe free COVID tests had their personal information exposed. That information included full name and address, contacts, legal IDs, flight tickets and staying details.
---

# TL;DR

**Thousands of people who used Madeira Safe free COVID tests had their personal information exposed. That information included full name and address, contacts, legal IDs, flight tickets and staying details. If you travelled to Madeira and used the app before October 2021, you had your information exposed.**

# Context

In the summer of 2021, I had a trip booked to Madeira. At that time, COVID-19 was still prevalent in our daily lives and there were many travel restrictions. Madeira government required every person entering the island to have a PCR test done. To minimize the impact of this constraint, the [tests were made free](https://eco.sapo.pt/2020/07/01/vai-viajar-para-a-madeira-a-partir-de-lisboa-pode-fazer-teste-ao-covid-19-gratuitamente/) for those flying to Madeira. To get a free test, one would have to sign up for the Madeira Safe app, present a flight ticket as proof of travel to the island, and book the test. The [site to book tests](https://cedoc.masterinsoft.com/#/) is still online at the time of writing.
So I follow the procedure: signed up in the app by filling a bunch of personal data, uploaded a copy of my flight tickets and booked my test. All good so far.

# The problem

Some days after I took my COVID test, I received an email with the test result. The email not only had an attached PDF file with the report but also a link to a website and a code to validate my test result:

<img src="/assets/img/madeira-not-so-safe/result_email.png" alt="shell">

I open that website and used the provided code:

<img src="/assets/img/madeira-not-so-safe/result_website.png" alt="shell">

The website showed the date and result of my test, but I was curious about how that result was fetched based on the code I inserted. What I found was not good. It was a simple HTTP request that would return my test date and result, but a lot more:

<img src="/assets/img/madeira-not-so-safe/request.png" alt="shell">

Although only the test date and result was shown in the page, the request would also return a good amount of personal information, including (full sample<sup >[[1]](#sample)</sup>):

- Full name
- Email
- Phone number
- VAT number
- Civil ID
- SNS ID
- Full address, including zip code
- Birthday
- Place of staying in Madeira
- Copy of the flight tickets

That was not good. Exposing so much information was bad but it wouldn’t be so bad if it were behind an unpredictable token, so I grabbed my result code and incremented it by one. My worst fear came true when someone else’s data was returned. I incremented again by one and the same person’s data was returned. I repeated this multiple times and finally another person’s data was returned. What I found was that every X increments, a new person’s data was returned, and all the other codes returned the same person, behaving like a default in case a certain code was not found.

It took me around 30 minutes to take my COVID test and during that time period I saw about 50 people that followed the same process and application waiting to be tested as well, so I guess the number of people could be some hundreds every day. Given that same process and application have been in place for months at that time being, this means that **someone could easily iterate the test ID code and collect tens or hundreds of thousand people's personal information**.

# Reporting the vulnerability

Given the severity of the vulnerability, I wanted to report it to the application owner so it could be fixed as soon as possible. After looking at the page URL and source code, I noticed that the application had been developed by [MASTERINSOFT](https://masterinsoft.com/). I made the first contact with them on 07/09/21, asking for the proper contact person to report a security issue on their software. I made a second attempt a couple of weeks later but received no response. So I decided to reach out to [CEDOC](https://novaresearch.unl.pt/en/organisations/centro-de-estudos-de-doen%C3%A7as-cr%C3%B3nicas-cedoc) (Nova Medical School) which was the facility conducting those tests. Again, no one replied to me, so I reported the security issue to [CNCS](https://www.cncs.gov.pt/).
CNCS promptly responded and asked for more details of my findings, which I provided right away. I haven't received any update or message since then, but I was able to confirm that the vulnerability was fixed some weeks later.

# Aftermath

It has been 2 years since I found the vulnerability and the issue was fixed. The website is not even online anymore. Nevertheless we had a huge amount of PII exposed for several months, which could and still can expose affected people to attacks, and no communication was done. Not only is an ethical obligation to inform people that their data can be exposed, but it's also a legal obligation. [GDPR Art. 34](https://gdpr-info.eu/art-34-gdpr/) states:

> 1. When the personal data breach is likely to result in a high risk to the rights and freedoms of natural persons, the controller shall communicate the personal data breach to the data subject without undue delay.

I have found and reported dozens of security issues over the years and I had never disclosed my findings publicly before. The experience was never great, people and companies are still not prepared to lead with cybersecurity incidents, but we were able to fix, mitigate and recover from those findings. However I decided I should disclose this one, not only because of the extent of it but also because how it was handled and how people can still be affected by it. I hope this will reach affected people and ideally alert companies that they should invest on educating people on cybersecurity not only to have fewer vulnerabilities arise, but also to better handle them whenever they do.

<small id="sample"><sup>[[1]](#sample)</sup> Response sample</small>

```
{
  "cdatetime": "<date>",
  "rdatetime": "<date>",
  "result": "<result>",
  "data": {
    "bookId": "<number>",
    "roomId": "<number>",
    "unitId": "<number>",
    "roomQty": "<number>",
    "status": "<number>",
    "substatus": "<number>",
    "firstNight": "<date>",
    "lastNight": "<date>",
    "numAdult": "<number>",
    "numChild": "<date>",
    "guestTitle": "<flight booking number>",
    "guestFirstName": "<full name>",
    "guestName": "",
    "guestEmail": "<email>",
    "guestPhone": "<civil id>",
    "guestMobile": "<phone number>",
    "guestFax": "<vat number>",
    "guestCompany": "<sns id>",
    "guestAddress": "<address>",
    "guestCity": "<city>",
    "guestState": "",
    "guestPostcode": "<zip code>",
    "guestCountry": "<country>",
    "guestCountry2": "<country>",
    "guestArrivalTime": "",
    "guestVoucher": "",
    "guestComments": "",
    "notes": "",
    "message": "",
    "groupNote": "",
    "custom1": "<birthday>",
    "custom2": "<destination country>",
    "custom3": "<place of staying address>",
    "custom4": "<checkbox>",
    "custom5": "<genre>",
    "custom6": "<date of travel>",
    "custom7": "<copy of flight tickets>",
    "custom8": "<checkbox>",
    "custom9": "<string>",
    "custom10": "<string>",
    "flagColor": "<color code>",
    "flagText": "<string>",
    "statusCode": "<numbwe>",
    "lang": "<string>",
    "price": "<number>",
    "deposit": "<number>",
    "tax": "<number>",
    "commission": "<number>",
    "currency": "<string>",
    "rateDescription": "<date>",
    "offerId": "<number>",
    "referer": "<string>",
    "refererEditable": "<string>",
    "reference": "",
    "apiSource": "<number>",
    "apiReference": "",
    "apiMessage": "",
    "allowChannelUpdate": "<number>",
    "allowAutoAction": "<number>",
    "allowReview": "<number>",
    "cancelUntil": null,
    "stripeToken": "",
    "propId": "<number>",
    "ownerId": "<number>",
    "invoiceeId": "",
    "bookingTime": "<date>",
    "modified": "<date>",
    "cancelTime": "",
    "masterId": "",
    "infoItems": [
      {
        "infoItemId": "<number>",
        "time": "<date>",
        "code": "<string>",
        "text": "<string>"
      },
      {
        "infoItemId": "<number>",
        "time": "<date>",
        "code": "<string>",
        "text": "<string>"
      },
      {
        "infoItemId": "<number>",
        "time": "<date>",
        "code": "<string>",
        "text": "<string>"
      },
      {
        "infoItemId": "<number>",
        "time": "<date>",
        "code": "<string>",
        "text": "<string>"
      },
      {
        "infoItemId": "<number>",
        "time": "<date>",
        "code": "<string>",
        "text": "<string>"
      },
    ]
  }
}
```
