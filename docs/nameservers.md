# I changed my nameservers and it broke everything

In reference to my post on creating a ***static site***, I deployed the site using Github Pages. Now, Github allows you to set a custom domain on any site deployed on Github Pages. I think it would have been good if it worked smoothly but it did not work so well.

So I created a sub-domain `www` and added A records pointing to the Github Pages IP addresses. I actually wanted my custom domain name and not the Github Pages default domain and I couldn't compromise on that. There was only 2 days left for the JS Conf'23 and I was in a rush. The custom domain was configured but somehow for some browsers I was getting redirected to GoDaddy's page. I checked the [Github Pages documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages) again and again but it did not solve my problem. 

My domain which is [yashasva.in](https://yashasva.in) has a security certification. But still I was not getting it configured for my custom domain on Pages (basically waited 24hrs for that) and so I had to try a last resort. I deleted my custom domain and deployed my site on [Netlify](https://netlify.com). But to configure custom domain there, I had to change my nameservers. 

Now nameservers help connect your site's URLs to their IPs. Imagine you type [https://google.com](https://google.com) in your browser and hit enter. Your browser will now send a request to your domain's nameserver and get the IP address of your site. Then the browser will request the IP address to deliver the content for you. Your server respoonds back with the content and Voila!

To add a custom domain in Netlify I had to point my domain from the nameservers provided by GoDaddy(default) to the Netlify nameservers. That means any requests sent to my domain/sub-domains will be catered by Netlify's nameservers. This created a problem.

I couldn't access the sub-domain for [URL shortener](https://shorturl.yashasva.in) application I created earlier. Because the nameservers didn't have the sub-domains pointing to my application. 

There are few observations I came across while playing around nameservers and I'll write them down as steps.
* **Step 1**: I reverted the Netlify's namservers to GoDaddy's default nameservers. I found that I could do better without changing the nameservers and messing everything up. Now I tried to use Github Pages deployment instead of Netlify.
* **Step 2**: To deploy to Github Pages, I first created an HTML file and embedded iframe of the site I wanted to deploy with 100% viewport width and height. I then tried to deploy it on Github Pages since it doesn't prompt me to use another nameserver. I then setup an Apex domain as per the documentation steps and waited for my site to get active.
* **Step 3**: My site started running on http://yashasva.in but I also needed to setup www CNAME for my site so that it directly routes to thedevwonder.github.io. Things seem to be working fine here but Github could not verify my SSL certificate although I own it.
* **Step 4**: I ran the following command `dig YASHASVA.IN +noall +answer -t A` and I saw that I was getting an extra IP which was not provided by the github. I checked Github Support Bot and I found an answer. GoDaddy had configured a Park domain for protecting the domain name. But now that I have a service linked to the apex domain, I can safely delete that and after few minutes, my SSL certificate got validated.

Above is just a trial and error approach to achieving what I wanted and someone stuck in the hell hole could get a hint on how to get out of it. Maybe, who knows.
Putting down the links that helped me through the process.

 - https://kinsta.com/knowledgebase/what-is-a-nameserver/
 - https://blog.hubspot.com/website/what-is-parked-domain
 - https://support.github.com/
 - https://developer.mozilla.org/en-US/docs/Web/CSS/Viewport_concepts
 - https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain