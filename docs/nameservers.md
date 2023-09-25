# Practical Experiences of Managing Personal Domain

## Outline
 - Introduction
 - What happens when you type something like “lab.yashasva.in” in the browser for the first time?
 - History of DNS and evolution to Modern DNS
 - What is an apex domain? Netlify docs on how to set up an apex domain
 - I screwed up… Why lab.yashasva.in stopped working after I changed name servers for my domain from GoDaddy to Netlify
 - What are name servers? Primary and secondary name servers
 - TTL: Time-To-Live. A brief on understanding DNS cache
 - How can you set up your own custom domain in Netlify and GitHub?
 - What are parked domains and how it hindered my SSL certificates?
 - Total cost it took to get my own domain from GoDaddy
 - Conclusion
 - References

## Introduction
A few months back I purchased my custom domain. For a long time, I had wanted a personal internet space where I could keep a record of all the fun things I do in my free time, or write down technical problems that I face in my professional career as a Software Developer.

I had zero practical experience in managing a domain and had very little idea of how things worked internally. Through a mixture of practical and theoretical readings, I was able to document my learnings gathered from this experience. I screwed up many times and learned through many iterations on how to manage and host web applications via a custom domain. I hope (I’m an optimistic person) that someone out there reading this will probably find something useful or just enjoy reading it.

Let’s get started.

What happens when you type something like “lab.yashasva.in” in the browser for the first time?
Every computer connected to the internet has an IP address, which is a unique address in the form of (n.n.n.n, where n lies between 0–255). Through this unique address, you can communicate or locate a computer connected to the internet.

Remembering the IP address of each computer is not possible, even among computer-literates and elites. That’s why we have something like google.com, a name corresponding to an IP. But you need to have a directory, like a phonebook, to look up the address for google.com. Such a name service is called DNS (Domain Name Service)

When I bought my domain, I set up a blog application and assigned a custom subdomain pointing to the application. This domain was lab.yashasva.in .

So what happens when I type lab.yashasva.in, in the browser and hit ENTER?

A few points before:

If the browser has cached the IP for lab.yashasva.in then, you get the IP address from the cache. ( More on cache later, but let’s say your browser already has the IP address for the given name, then it stores it up so that it does not have to ask for it again).
Caching happens at each step going forward(as you know it’s faster to store it up for future use than to ask again and again). But let’s just assume that caching has not happened.
The resolver holds the algorithm for finding the appropriate nameserver. A nameserver is a repository containing the map between domain name and IP.
What happens:

The browser sends a request to the resolver for the IP address of lab.yashasva.in .
The resolver knows the location of the root server, which then asks it to look for it in the “.in” Top Level Domain nameserver (TLD).
TLD asks the resolver to look for it in “yashasva.in” authoritative nameserver for the domain provided by GoDaddy.
Since I had added CNAME for lab.yashasva.in pointing to example.github.io in the same DNS zone file, the resolver will repeat step 1 for example.github.io .
After it reaches the authoritative nameserver for example.github.io it returns the IP to the browser.
A Simpler explanation not involving subdomains can be found here: https://wizardzines.com/comics/life-of-a-dns-query/

## History of DNS and evolution to Modern DNS
It is important to go a little deep into the DNS mentioned in the previous section to explain how I screwed up in the subsequent sections.

The history of DNS started from a file HOSTS.TXT located in a central server. As the size of the file and updates in the file grew, distribution of the file became an issue and a new name service came into the picture.

Domain Name Service contains a set of distributed servers, as the central database, like HOSTS.TXT, would not be suitable for the growing internet. It also avoids a Single Point of Failure as the servers are distributed. The architecture of DNS includes a resolver and distributed nameservers. Applications access DNS through a resolver. The resolver can be accessed through two functions gethostbyname and gethostbyaddr. As mentioned briefly, the resolver would hold the algorithm for finding the right nameserver, and the nameserver has information on either the IP address of the desired host or the location of the nameserver that might have this information. Again, no single computer on the internet has all the information on all of the domains available in the world. The most common implementation of DNS is the BIND (Berkeley Internet Name Domain servers).

Since every person, organization, institute, or government body can have its own nameserver, no single nameserver has all the information. If the information is not present, then the nameservers need not know the location of every single nameserver out there, instead, they have the location of all the root nameservers.

The DNS namespace is a tree of varied depth where every node has a label. This label can be 63 bytes long and an empty label is reserved for the root of the tree. The domain name of any node in the tree is the concatenation of all the labels from that node going up to the root of the tree joined by the “.” delimiter. The namespace is divided into root nameservers, top-level domains, second second-level domains. Consider my undergrad college website, “nitrr.ac.in”. It has 4 labels(including the null label of the root which is omitted). “.in” is the top-level domain reserved for India. “.ac” is the second-level domain reserved for academic institutes. “nitrr” is again a second-level domain that has an authoritative nameserver. Every node in the tree must have a unique domain name but the same label can be used in different parts of the tree.

Joining the dots now, how does a DNS lookup happen?

You search google.com in the browser, the browser sends a request to the resolver which then requests the root nameserver.
The root nameserver identifies the “.com” top-level domain and asks the resolver to check with TLD “.com”.
The TLD then asks the resolver to check with the authoritative nameserver of the second-level domain “Google”, which then returns the IP address back to the resolver.
The resolver responds back to the browser with the IP address which is then used to connect to the “google.com” server.
What is an apex domain? Netlify docs on how to set up an apex domain
At this point, I have an already hosted web app that I want to make my primary domain. The APEX domain. The apex domain is the root of your domain.

Since I hosted my site on Netlify, I’ll summarize how I added a custom apex domain to my site. This is where the problem began:

I configured A record pointing to Netlify’s load balancer IP address and saved the settings. (Check the fallback option at: https://docs.netlify.com/domains-https/custom-domains/configure-external-dns/#configure-an-apex-domain )
Waited for my domain to take effect for a few hours but couldn’t seem to get any result.
That’s when I decided to delegate my domain to Netlify DNS.
I copied 4 Netlify nameservers, logged in to my GoDaddy account, and changed the authoritative nameserver to Netlify’s nameservers under the “Nameservers” tab in DNS management
The changes took effect and my web app was successfully hosted under my custom domain name.
But, since I changed the DNS management from GoDaddy to Netlify, I was able to accidentally yet successfully, shut down my older services which were mapped in GoDaddy’s zone file.

## I screwed up… Why lab.yashasva.in stopped working after I changed name servers for my domain from GoDaddy to Netlify
You can probably understand why my old apps like lab.yashasva.in stopped working. At that time I wanted a site up and running as I did not want to attend a tech conference without a personal online space. Yes, I was about to attend a tech conference the very next day. The solution at hand was working but rushed. I realized I needed to take a step back and think about some of the few things I encountered.

I didn’t know a thing about nameservers
As I added a custom domain, I could see the site working in a different browser but not in the one I had opened initially.
I needed to know why my previous sites were not working and needed to bring them back.
What are name servers? Primary and secondary name servers
I have mentioned nameservers in previous sections and how they are responsible for loading names and IP addresses. But first, let’s talk about the zone. A zone is a subtree of the DNS namespace administered separately. Usually, an admin is assigned to look after a zone. When a new system is added to the zone, the admin will store its name and IP address in a disk file present in the primary nameserver. On a side note, there is usually a primary nameserver and one or more secondary nameservers, which are redundant and avoid a single point of failure. The primary nameserver loads information from the disk file while secondary servers load it from the primary servers via a zone refresh algorithm.

GoDaddy provides its own zone file. Data inside a DNS is organized in the form of Resource Records. If you go to GoDaddy’s DNS Management portal, you can see the zone file as well as the different types of Resource Records it offers. A few of these are:

A record — directs a domain to an IPv4 address
AAAA — directs a domain to an IPv6 address
CNAME — canonical name
NS — nameservers (You can see the nameservers in the Nameservers nav menu on the same page)
Another thing that you’ll notice in the zone file is the TTL(Time to Live) which we’ll be covering in the next section.

For now, I have realized that changing the authoritative nameserver for the domain means changing the zone file. Perhaps, importing the zone file data would have solved my problem but I did not always want to use Netlify as my hosting platform. And so, I decided to revert the authoritative nameservers back to GoDaddy. Now my domain has no website mapped to it.

I had the application deployed in Github Pages too. So I configured a custom domain in Github Pages. I created new A records and pointed them to the IP of the GitHub Pages.

```
185.199.108.153

185.199.109.153

185.199.110.153

185.199.111.153
```

Since my site is now ready it should be visible in my browser, but it seems I could still see that my site was down. I was using Google Chrome, I tried Brave and it worked. Changing browsers worked. Why?

## TTL: Time-To-Live. A brief understanding on DNS cache
Remember I told you I would discuss cache later? Now is the time to discuss it. The cache is a temporary memory that has faster reads and reduces the latency of overall operation. Basically, you save the result of a computationally heavy operation such as persistent database reads and use that stored result instead of processing it every single time. This reduces latency, but what happens when the value updates? Now the end user must get an updated result but since the old result was cached, the user receives a stale/older result.

To avoid this, the cached result is often expired/deleted, so that a new cache with the updated result gets generated. The amount of time a data exists before it is expired is called TTL. TTL is set by the DNS admin. In GoDaddy, you can set TTL for your records, and is calculated in seconds in the zone file.

The resolver caches the result fetched by each level of servers so that future queries fetch the cached result instead of requesting the server. Caching happens in the server and not the resolver so any other resolver can access the same cached result. When the resolver requests for the “lab” label of the “lab.yashasva.in” domain in GoDaddy’s nameserver, it gets back another URL example.github.io as a CNAME for the subdomain. When this happens, DNS lookup happens again and the same process is repeated until we find the authoritative nameservers for “example” in example.github.io . Adding CNAME might feel like a huge strike on the performance but because of caching, it doesn’t matter much.

One of the features of DNS caching is that it caches negative results. In my case my site was down, and I configured a new one but still, I could not see the reflected changes. This might be due to negative caching. As far as I remember, changing the browser solved the issue. This might be a practical case where we can see that the DNS caches negative results.

## How can you set up your own custom domain in Netlify and GitHub?
Both Netlify and Github Pages documentation on how to set up your own custom domain is pretty comprehensive and should require no further explanations. Since we now know the internal workings, we will have a better idea to experiment and troubleshoot. We just need to point our A/AAAA records to the hosting service’s IP. I will paste the links to the Netlify as well as the Github Pages guide.

Netlify: https://docs.netlify.com/domains-https/custom-domains/configure-external-dns/

GitHub Pages: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site

Perhaps, a more interesting thing that piqued my interest is that both the documentation recommended setting up a www CNAME. Why?

A good reason that I looked up was that when the web came along it was called the World Wide Web (www). Domains back then had www as their subdomain to indicate the World Wide Web. With time, people started omitting www from their URLs, but since www is still used, it is often added as a subdomain. Currently, my website www.yashasva.in redirects to just yashasva.in to avoid two different addresses.

## What are parked domains and how it hindered my SSL certificates?
Now, we move on to the last problem in my pursuit of setting up a custom domain. As I set up a custom domain in Github Pages, I was not able to download my SSL certificate. This was a specifically paid one, and if it can’t pick it up, I would have to re-evaluate my financial decisions.

If you run the command `dig [YASHASVA.IN](<http://yashasva.in/>) +noall +answer -t A` it will dig the IP address mapped to the name yashasva.in . I could see an extra IP that was not part of the Github Pages IP and was present in my GoDaddy Zone file from the start. The solution to my problem was found with the help of GitHub Support Bot.

It turns out that the extra domain was a parked domain and it is generally used to protect registered domains that are not connected to an online service. This parked domain was pointing to some ads page of GoDaddy. Requests resolved to parked domain’s IP never reach Github Pages and prevent it from downloading my SSL certificate. I then deleted this A record and within a few minutes, Github Pages was able to download my SSL certificate and my site was working perfectly.

## Total cost it took to get my own domain from GoDaddy
Before we conclude I would like you to check the GoDaddy pricing model and compare it with other domain providers, like Google and Cloudfare before using it for personal or enterprise purposes.

I got my domain at 500rs/year (incl. taxes), and liked the GoDaddy’s DNS management UI. You can check out the GoDaddy’s domain prices here: https://www.godaddy.com/en-in/domains

## Conclusion
To conclude, my hands-on was motivated by my own curiosity to understand the one thing that I use daily, the Internet. In the process, I understood how DNS works and how it caches both positive and negative results. I encountered some problems, researched about them, and eventually learned better by implementing the solution in a more practical way than just reading about it. I hope this helps someone!

## References
 - How does the Internet Work? https://web.stanford.edu/class/msande91si/www-spr04/readings/week1/InternetWhitepaper.htm
 - TCP/IP Illustrated Vol 1 — https://www.isi.edu/~hussain/TEACH/Spring2014/notes/Steven00a.pdf
 - The Modern Internet — https://cs249i.stanford.edu/
 - Development of the Domain Name System — https://www.cs.cornell.edu/people/egs/615/mockapetris.pdf
 - Domain Name Service(DNS) a distributed system — https://medium.com/@raviraj199602/domain-name-service-dns-a-distributed-system-9e1d825fae11
 - Life of a DNS Query — https://wizardzines.com/comics/life-of-a-dns-query/
 - DNS Cache Explained — https://www.cloudns.net/blog/dns-cache-explained/
 - https://superuser.com/questions/60006/what-is-the-purpose-of-the-www-subdomain