---
layout: post
title: Splunk Field Extraction Permissions
categories: 
  - splunk
  - tech
tags: 
  - fieldextractions
  - permissions
  - splunk
description: Teachable moment Splunk field extractions have permissions!
comments: true
published: true
---

As with almost every object in Splunk, field extractions have permissions. If you are creating awesome field extractions to make searching easier don't forget to modify the permissions afterwards or other people won't be able to benifit from your work.

On the Splunk Search Head go to:
1. **Settings**
2. **Fields**
3. **Field Extractions**
4. On the very right hand side find **Permissions**
5. In **Object should appear in** select **All apps**
6. Under **Permissions** select **Everyone** under **Read**

## Sources
- https://answers.splunk.com/answers/71353/search-returned-no-results.html

