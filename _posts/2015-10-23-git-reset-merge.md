---
layout: post
title: Git - Reset Merge
categories: 
  - git
  - tech
tags: 
  - git
description: How to reset a merge in git.
comments: true
published: true
---

### Note: To do this you need to be able to force push to the remote in some systems like Stash it may be disabled

Choose your last working commit not in the merge for this case it is the merge 9asd3klb237876e9013b7dade79d8cbsad3gj76es.

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


Reset head to the last good merge using the commit hash shown in git log

{% highlight bash %}
ryan@ryan$ git reset --hard 9asd3klb237876e9013b7dade79d8cbsad3gj76es
{% endhighlight %}


Now force push your changes from the local HEAD to the remote origin

{% highlight bash %}
ryan@ryan$ git push origin HEAD --force
{% endhighlight %}


And your done....
