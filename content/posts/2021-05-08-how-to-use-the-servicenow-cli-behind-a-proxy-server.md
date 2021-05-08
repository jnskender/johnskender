---
template: post
title: How to use the ServiceNow CLI behind a proxy server
slug: now-cli-proxy
socialImage: /media/now-cli-proxy.png
draft: true
date: 2021-05-08T19:54:16.114Z
description: "ServiceNow's latest product, the ServiceNow CLI, ventures to allow
  developers to develop custom UI components and more from their terminal. This
  article aims how you can utilize this new tool in the high chance that your
  company tunnels all of their traffic through a proxy server. "
category: ServiceNow
---
The ability to utilize modern tooling introduced by ServiceNow (UI Builder, Now Experience) relies on the ability to access another foundational tool, the ServiceNow CLI. 

## Background

I won't dive into detail on what the CLI is in this article. This article's main purpose is to fill a gap that the ServiceNow product team has introduced with the CLI. In it's current state as of writing this article (05-08-2021) the CLI does not allow you to connect to an instance if your web traffic is routed through a proxy server. This is both suprising and very frustrating as many ServiceNow customer's are in the corporate environment where this is basically standard practice. 

Most modern tooling will utilize your local env variables of ```http_proxy``` and ```https_proxy``` to ensure that the traffic is routed through your desired proxy. The CLI will not honor these env variables when making connections to the instance. I have spoken with ServiceNow support and they have acknowledge they are aware of this limitation and also have no current plans to alleviate this issue for their customers. 

This led me down a path to find a solution on my end so that myself and my development team have the opportunity to leverage these new tools to create new UI experiences for our consumers. 

I'll outline the steps I've taken to provide temporary relief below and preface that it's not an ideal long term solution and may be a little cumbersome for some who may not be comfortable in their terminal. This solution is also purely dedicated to MacOS but hopefully the idea's can be easily replicable in other OS's. 

## The Solution
Luckily we can make a few modifications to the NOW CLI source code to ensure that all of it's https connections are tunneled through your desired proxy. 

Unluckily is you have to do this every-time you update the CLI and that it's possible this solution may break with future versions. 

### Step 1 : Download the SNC CLI
You can follow ServiceNow's documentation for getting the SNC CLI downloaded to your machine. If you've found this article you will most likely already have it downloaded. 

### Step 2 : Try logging into your instance
One of the first things you'll need to do to use the CLI is to make an initial connection to your desired instance using the command ```snc configure profile set```. This will walk you through a few questions (instance url, username, password, etc) and then try to connect to your instance. If this is successful there's no need to continue these steps, this means your traffic is hitting the instance correctly and you don't need to worry about proxy configurations. 

If this step times out with error 

```connect ETIMEDOUT INSTANCEIPADDRESS:443```

This is most likely because your traffic needs to be routed through your proxy to be allowed to find it's way to your instance.

### Step  3: Get to the Source

You'll need to have the ui-component extension installed to get access to the source. You can install the extension with

```snc extension add --name ui-component```

From here we'll need to open the source code that was stored on your root user folder as a result of the above command. The path on mac looks like 

```/Users/username/.snc```

.snc is a configuration folder known as a "[dotfile](https://wiki.archlinux.org/title/Dotfiles)". Because of this your finder/explorer will not show it by default. To show dotfiles in Mac's Finder window you can use the shorcut ```command shift .```

Even easier, if you have visual studio code installed, is to run the following command to open the folder in VS code: ```code ~/Users/username/.snc```. This will open the folder at that path in VS code. 







