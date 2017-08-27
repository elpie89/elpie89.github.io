---
layout: post
title: SVN-Slack Integration
---
<img src="{{ site.baseurl }}/assets/blog/slack_svn_integration/slack_subversion.jpg" alt="SVN-Slack" style="width: auto;"/>

Today I wanna speak about the integration of Svn in Slack.
Slack is a really powerful tool and Subversion as well.
But in those developer days everything should be connected and customizable.
So what happen if I wont to notify to everyone who are involved in my project on how I'm working on it?
Using just Slack I have to notify manually everyone the states of my work in a particular slack channel or  in a private conversion.
So fortunately there is a way to integrate Subversion with slack and to send all the commit log directly in a channel of Slack.
The reason why I write an article is that is not so simple to integrate this ,because lot of app in slack need just to be installed.
With subversion we have to do some extra-work in the machine who contain the repo.

Let's see how to configure everything.
 <!--more-->

<h2>Install Subversion app into Slack</h2>
<img src="{{ site.baseurl }}/assets/blog/slack_svn_integration/slack_app_integration.jpg" alt="SVN-Slack" style="width: auto;"/>

<h2>Add a configuration for a single channel of Slack</h2>
<img src="{{ site.baseurl }}/assets/blog/slack_svn_integration/add_channel.jpg" alt="Add channel" style="width: auto;"/>

<h2>Keep note of the token</h2>
<img src="{{ site.baseurl }}/assets/blog/slack_svn_integration/token.jpg" alt="Token" style="width: auto;"/>

<h2>Backend work</h2>
 
Now we have to enter in the folder "hook" inside the main repository
In my case I installed my svnserver in a Qnap NAS that's why I have this strange path
/share/MD0_DATA/SVNrepo/SlackIntegrationSample/hooks/

and copy this two file available here

post-commit
slack-posthook.py

-EDIT post-commit
The first one is a simple bash file that is called when a commit is ended
and we have to simply provide this two info
The repo location on our server
REPOS="/share/MD0_DATA/UnityRepo/VisitCommodityProject/"
and the absolute path to the other file "slack-posthook.py"

<p>/share/MD0_DATA/UnityRepo/ProjectName/hooks/slack-posthook.py "$REPOS" "$REV" >> $LOG 2>&1 || exit 1</p>

EDIT slack-posthook.py
Here we have to edit this 3 line

TOKEN = 'token' # token like cg3MI88ufdGWwT5RbojoLJCV
DOMAIN = 'example.slack.com' # for example companyname.slack.com
REPO_BASE_URL = 'svn://example.myqnapcloud.com/share/MD0_DATA/UnityRepo/ProjectName' # for example http://svn.companyname.com/

And eventually,if you server can not see this comand, we have to give the path of the svnlook command 

LOOK='svnlook'

<h2>That’s it, should work</h2>