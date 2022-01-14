---
layout: layout.njk
title: Cloud Address Book with Nextcloud (CardDAV)
tags: blog
date: 2022-01-07
---
# Setting Up a Cloud Address Book with CardDAV and Nextcloud

1. Set up Nextcloud on a server, use all the usual bits and bobs around LetsEncrypt, Apache, PHP, UFW. mySQL optional, current set up uses sqlite just fine, but may not scale?
2. Install the 'Contacts' app on Nextcloud.
3. In Thunderbird, install the 'Cardbook' extension.
4. In Cardbook, add a new, remote address book.
5. From Nextcloud, go to 'Contacts' > 'Settings', and choose the address book, click the three dots menu, and click Copy Link.
6. Paste the link into Cardbook as the remote address, plus your Nextcloud username and password.
7. Add contacts as normal.

N.B.: For distribution lists, use groups, not lists in Thunderbird so that they sync to Nextcloud.
