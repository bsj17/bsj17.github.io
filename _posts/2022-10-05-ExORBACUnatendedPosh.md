---
layout: post
title: ExchangeOnline RBAC and PoSh unattended scripts
subtitle: Azure apps with Exo permissions in general
tags: [ExchangeOnline,ExO,Powershell,ModernAuth]
comments: true
---
## Intro
So, it happened. Microsoft had finally started to shutdown basic auth on M365 tenants. You could not avoid to hear this news, it was all over M365 portal, blogs, Twitter... and it happend on [1st of October 2022.](https://techcommunity.microsoft.com/t5/exchange-team-blog/exchange-online-email-applications-stopped-signing-in-or-keep/ba-p/3641943) in case you were living under rock and missed it.

Prior killing basic auth it was needed to update unattended PowerShell scripts to use app-only authentication. Microsoft did made this possible by introducing new [ExO PoSh module](https://learn.microsoft.com/en-us/powershell/exchange/app-only-auth-powershell-v2?view=exchange-ps). It's a streight forward process, but you need to create [Azure app](https://learn.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals) to use it. 

## Scope(s) and roles
This were I come to the point of writing this blog post. If you're using this approach not being mindful of **scope**, application/script that is going to be using this service principal will have access on ExO tenant level.

**Why would I care?**  Well, it's a matter of least privilage principle. You want to give application only access that is needs and write scope on objects that it needs to mange. 

## Exchange RBAC primer

Exchange Role Based Access Control (RBAC) it's quite complex topic and it offers granularity to great extent. I will not get into details, plenty of articles on the net do good explanation.
![RBAC](../assets/img/rbac.jpg){: .mx-auto.d-block :}


In short RBAC controls what account (**Who?** admin or end user) can do. It does this based on Role group membership. Role group is a collection of Roles.
Each role consist of set's of PowerShell cmdlets (**What?** can account do). Each cmdlet consists of parameters and switches (Yes, this is how deep you go when customizing roles).

Management role scopes define targets "**Where**?" cmdlets can be used. 

(I wanted to skip this but still... In case you're wondering what Role assigment is. It's a "glue" that links role, role group and scope)

When account logs into ExO PowerShell session using interactive logon session it will pick-up everything that is defined in ExO role groups (roles, scopes) that they are member of.

{: .box-note}
Since basic auth is turned off and interactive logon is no good for unatended script logon. Only way forward is to use app-only authentication. 

How can I control "**What?**" my unatended script will be able to change/modify?
You can obviously [assign roles](https://learn.microsoft.com/en-us/powershell/exchange/app-only-auth-powershell-v2?view=exchange-ps#step-5-assign-azure-ad-roles-to-the-application) but not in ExO directly. But there's a catch... Only two ExO built-in roles from Azure AD are available. Custom AAD roles for ExO are not availiable and roles created in ExO are not visible in AAD. 

But what about scoping "**Where?**" part now becomes whole ExO tenant. That might be fine in some cases where you need to manage whole tenant. But for [paranoid](https://www.youtube.com/watch?v=uk_wUT1CvWM) people, like myself I don't want that some unattended script holds ultimate power and modify all objects on my tenant. Especially if it's some "other" team's responsibility. How do I control it and restrict access to specific scope?

Well at this point in time, if you're using app-only authentication you can't. I ran in excellent article by [Vasil Michev
](https://www.michev.info/Blog/Post/4027/more-on-service-principal-permissions-in-exchange-online) where he reveals what's visible behind the curtains in ExO environment. So, one would think that ServicePrincipal if added to the role will actually inherit the role priviliges. However, it looks like you can't "skip" AAD role groups. I tried this and as expected it resulted in error.

```PowerShell
#Create custom Role,Scope and RoleGroup
New-ManagementScope -Name "Custom-EU-Corp" -RecipientRestrictionFilter "(PrimarySmtpAddress -like '*@evilcorp.com'"
new-ManagementRole Custom_MailRecipients_EU-Corp -Parent "Mail Recipients"
New-RoleGroup -Name Custom-EU-Corp -Roles Custom_MailRecipients_EU-Corp -CustomRecipientWriteScope "Custom-EU-Corp"
Add-RoleGroupMember -Identity Custom-EU-Corp -Member 722eae44-xxxx-xxxx-xxxx-ee2932067dd9 
New-ServicePrincipal -AppId 722eae44-xxxx-xxxx-xxxx-ee2932067dd9  -ServiceId 00000002-0000-0ff1-ce00-000000000000

#Connect to ExO via app-only 
Connect-ExchangeOnline -CertificateThumbprint "4528C77ADE7869B4E6BFE23EEE9FBE70B48181F0" -AppId 722eae44-xxxx-xxxx-xxxx-ee2932067dd9 -Organization M365Bxxxxxxx.onmicrosoft.com

Exception: Processing data from remote server outlook.office365.com failed with the following error message: [AuthZRequestId=ac4bc3f3-795d-444f-81dc-b94c8bc24941][FailureCategory=AuthZ-CmdletAccessDeniedException] The
role assigned to application 722eae44-xxxx-xxxx-xxxx-ee2932067dd9 isn't supported in this scenario. Please check online documentation for assigning correct Directory Roles to Azure AD Application for EXO
App-Only Authentication. For more information, see the about_Remote_Troubleshooting Help topic.
```

Organizations commonly used role legacy is ApplicationImperonation. Graph would be your way forward. Again you get


Introdicing [Application access policies](https://learn.microsoft.com/en-us/graph/auth-limit-mailbox-access) 



Here's a definition of what role group is:

**Management role group:** 
The management role group is a special universal security group (USG) that contains mailboxes, users, USGs, and other role groups that are members of the role group. This is where you add and remove members, and it's also what management roles are assigned to....(omitted for clarity) 


{: .box-note}
**Management role scope:** 
Management role scopes enable you to define the specific scope of impact or influence of a management...(omitted for clarity) 




This is a demo post to show you how to write blog posts with markdown.  I strongly encourage you to [take 5 minutes to learn how to write in markdown](https://markdowntutorial.com/) - it'll teach you how to transform regular text into bold/italics/headings/tables/etc.

**Here is some bold text**

## Here is a secondary heading

Here's a useless table:
<div class="foo">

| Number | Next number | Previous number |
| :------ |:--- | :--- |
| Five | Six | Four |
| Ten | Eleven | Nine |
| Seven | Eight | Six |
| Two | Three | One |

.foo table {
  table th:first-of-type {
    width: 30%;
  }
}

</div>
.Rtable {
  display: flex;
  flex-wrap: wrap;
  margin: 0 0 3em 0;
  padding: 0;
}
.Rtable-cell {
  box-sizing: border-box;
  flex-grow: 1;
  width: 100%;  // Default to full width
  padding: 0.8em 1.2em;
  overflow: hidden; // Or flex might break
  list-style: none;
  border: solid @bw white;
  background: fade(slategrey,20%);
  > h1, > h2, > h3, > h4, > h5, > h6 { margin: 0; }
}

/* Table column sizing
================================== */
.Rtable--2cols > .Rtable-cell  { width: 50%; }
.Rtable--3cols > .Rtable-cell  { width: 33.33%; }
.Rtable--4cols > .Rtable-cell  { width: 25%; }
.Rtable--5cols > .Rtable-cell  { width: 20%; }
.Rtable--6cols > .Rtable-cell  { width: 16.6%; }

How about a yummy crepe?

![Crepe](https://s3-media3.fl.yelpcdn.com/bphoto/cQ1Yoa75m2yUFFbY2xwuqw/348s.jpg)

It can also be centered!

![Crepe](https://s3-media3.fl.yelpcdn.com/bphoto/cQ1Yoa75m2yUFFbY2xwuqw/348s.jpg){: .mx-auto.d-block :}

Here's a code chunk:

~~~
var foo = function(x) {
  return(x + 5);
}
foo(3)
~~~

And here is the same code with syntax highlighting:

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
```

And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification



### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.