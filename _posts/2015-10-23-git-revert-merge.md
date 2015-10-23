---
layout: post
title: Revert A Merge In Git
categories: 
  - git
  - tech
tags: 
  - git
description: How to revert a merge in git.
comments: true
published: true
---

If you ever merged a changed that breaks something or just need undo a merge the following will show you how to revert the merge and also undo the revert so you can re-introduce thoose changes at a later time.

Make sure your on the branch that you want to revert the merge on, choose the merge commit you want to revert in this case its... ddbc8324fb9b48ec1458b225dsd5b7510975df6a8

{% highlight bash %}
ryan@ryan$ git log
commit ddbc8324fb9b48ec1458b225dsd5b7510975df6a8
Merge: 9a7fd8b ac2ff46
Author: Ryan Currah <ryan@currah.ca>
Date:   Fri Oct 23 10:03:32 2015 -0400

    Merge pull request #3 in salt from bugfix/testing-roll-back-plan to develop
    
    * commit 'ac2ff464fb07febda8a4cccf2b74c15ad4f12492':
      added some failing change

commit ac2ff464fb07fh33a8a4cccf2dnm38dc15ad4f12
Author: Ryan Currah <ryan@currah.ca>
Date:   Fri Oct 23 09:54:06 2015 -0400

    added some failing change

commit 9asd3klb237876e9013b7dade79d8cbsad3gj76es
Merge: a85db8f cd303a8
Author: Ryan Currah <ryan@currah.ca>
Date:   Fri Oct 23 09:49:56 2015 -0400

    Merge pull request #2 in salt from bugfix/some-working-change to develop
    
    * commit 'cd303a8bc0706822ef7ff98279cb70e6c9bfb758':
      some working change
{% endhighlight %}


Revert the bad merge using the commit hash of the bad merge

{% highlight bash %}
ryan@ryan$ git revert -m 1 ddbc8324fb9b48ec1458b225dsd5b7510975df6a8
{% endhighlight %}


Now push your changes to the remote origin

{% highlight bash %}
ryan@ryan$ git push origin develop
{% endhighlight %}

When you want to re-intorduce thoose changes you will need to revert the revert

{% highlight bash %}
ryan@ryan$ git revert 88edd6d
{% endhighlight %}


## Sources
- https://git-scm.com/blog/2010/03/02/undoing-merges.html

