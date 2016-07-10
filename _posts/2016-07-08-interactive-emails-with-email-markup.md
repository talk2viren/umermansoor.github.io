---
layout: post
title: Interactive Emails with Email Markup
comments: True
excerpt_separator: <!--more-->
---

It was a pleasant surprise when itinerary for my upcoming flight automatically showed up on my [Galaxy S6](https://en.wikipedia.org/wiki/Samsung_Galaxy_S6). I didn't have open my Gmail and search for the confirmation email. It was right there, just when I needed it to check the departure time.

![google-flight-trip]({{ site.url }}/img/blogs/google-flight-trip.png)
*(disclaimer: not my itinerary)*

<!--more-->

I immediately started wondering how it did that. At first, I thought Google did this by parsing and analyzing the text in the confirmation email and making a best-effort extraction of the flight information. But that wouldn't be very reliable and luckily that's not how it works. In reality, the confirmation email that was sent to me **contained additional information, as metadata, which Google used to display the itinerary**. Google calls it the [Email Markup](https://developers.google.com/gmail/markup/overview). In essence, senders create and put [markup](http://schema.org/) in their emails to instruct Google to display an interactive [action](https://developers.google.com/gmail/markup/actions/actions-overview) to the recipients, such as RSVP an event, confirm subscription, fetch boarding passes, etc.

> Email is an important part of how we get things done -- from planning an event with friends to organizing a trip to Paris. So much information is contained inside emails -- like the details of a dinner party or travel itinerary -- **and so many emails require action -- like RSVP, or flight checkin**.
>
> By **adding [schema.org](http://schema.org/) markup to the emails you send your users, you can make that information available across their Google experience, and make it easy for users to take quick action**. Inbox, Gmail, Google Calendar, Google Search, and Google Now all already use this structured data.

Some actions show up as buttons next to the subject line in the inbox. While this feature has been around for a couple of years at least, I believe Google has only recently started using this information across other services like Google Now and Google Calendar.

![gmail-action-buttons]({{ site.url }}/img/blogs/gmail-action-buttons.png)

I personally find this very useful and it has some interesting [use cases](https://moz.com/blog/markup-for-emails):

>
- [Flight reservations](https://developers.google.com/gmail/markup/reference/flight-reservation) - Includes options for displaying basic flight confirmation information, boarding pass, check-in, update a flight, cancel a flight, and additional options. This Highlight is also supported in Google Now.
>
- [Orders](https://developers.google.com/gmail/markup/reference/order) - Includes options for displaying basic order information, view order action, and order with billing details.
>
- [Parcel deliveries](https://developers.google.com/gmail/markup/reference/parcel-delivery) - Includes options for displaying basic parcel delivery information and detailed shipping information.
>
- [Hotel reservations](https://developers.google.com/gmail/markup/reference/hotel-reservation) - Includes options for displaying basic hotel reservation information, updating a reservation, and canceling a reservation. This Highlight is also supported in Google Now.
>
- [Restaurant reservations](https://developers.google.com/gmail/markup/reference/restaurant-reservation) - Includes options for displaying basic restaurant reservation information, updating a reservation, and canceling a reservation. This Highlight is also supported in Google Now.
>
- [Event reservation](https://developers.google.com/gmail/markup/reference/event-reservation) - Includes options for basic event reminders without a ticket, event with ticket & no reserved seating, sports or music event with ticket, event with ticket & reserved seating, multiple tickets, updating an event, and canceling an event. This Highlight is also supported in Google Now.

For more information:

 - Kristi Hines has written a good [article](https://moz.com/blog/markup-for-emails) if you are interested in using Email Markup for your product your services.
 - [Guide](https://developers.google.com/gmail/markup/getting-started) from Google.
