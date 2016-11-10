#ipfs for websites
### A short guide to hosting your site on ipfs

Adding your static website to ipfs is quite simple! Simply turn on your daemon:
```bash
$ ipfs daemon
```

And add the directory containing your website:
```bash
$ ls mysite
img index.html
$ ipfs add -r mysite
added QmcMN2wqoun88SVF5own7D5LUpnHwDA6ALZnVdFXhnYhAs mysite/img/spacecat.jpg
added QmS8tC5NJqajBB5qFhcA1auav14iHMnoMZJWfmr4k3EY6w mysite/img
added QmYh6HbZhHABQXrkQZ4aRRSoSa6bb9vaKoHeumWex6HRsT mysite/index.html
added QmYeAiiK1UfB8MGLRefok1N7vBTyX8hGPuMXZ4Xq1DPyt7 mysite/
```

The very last hash next to the folder name is the one you want, lets call it
`$SITE_HASH` for now.  

Now, you can test it out locally by opening `http://localhost:8080/ipfs/$SITE_HASH`
in your web browser! Next, to view it coming from another ipfs node, you can try
`http://gateway.ipfs.io/ipfs/$SITE_HASH`. Cool, right?  But those hashes are
rather ugly. Lets look at some ways to get rid of them.

First, you can do a simple DNS TXT record, containing `dnslink=/ipfs/$SITE_HASH`.
Once that record propogates, you should be able to view your site at
`http://localhost:8080/ipns/your.domain`. Now thats quite a bit cleaner.
You can also try this on the gateway at `http://gateway.ipfs.io/ipns/your.domain`

Next, you might be asking "well what if i want to change my website, DNS is slow!"
Well let me tell you about this little thing called Ipns (note the 'n'). Ipns is
the Interplanetary Naming System, you might have noticed the above link has
`/ipns/` instead of `/ipfs/`.  Ipns is used for mutable content in the ipfs
network, it's relatively easy to use, and will allow you to change your website
without updating the dns record every time! So how do you use it?

After adding your webpage, simply do:
```bash
$ ipfs name publish $SITE_HASH
Published to <your peer id>: /ipfs/$SITE_HASH
```

(Disclaimer: IPNS is not production ready and assets could be loaded from two different resolved hashes when updating your website, leading to outdated/missing assets. Better to use IPFS hash directly to be sure the right asset is being loaded)

Now, you can test that it worked by viewing: `http://localhost:8080/ipns/<your peer id>`.
And also try the same link on the public gateway. Once you're convinced that works,
lets again hide the hash. Change your DNS TXT record to `dnslink=/ipns/<your peer id>`,
wait for that record to propogate, and then try accessing `http://localhost:8080/ipns/your.domain`.

At this point, you have a website on ipfs/ipns, and you may be wondering how you could expose it at `http://your.domain`, so that the Internet users of today may access it too without them having to know about any of this. It's actually surpisingly simple to do, all you need for this is your previously created TXT record and to point the A record of `your.domain` to the ip address of an ipfs daemon that listens on port 80 for HTTP requests (such as `gateway.ipfs.io`). The users' browsers will send `your.domain` in the Host header of the requests, and you have your dnslink TXT records, so the ipfs gateway will recognize `your.domain` as an IPNS name, and so it will serve from under `/ipns/your.domain/` instead of `/`.

So, if you point `your.domain`'s A record to the IP of `gateway.ipfs.io`, and then wait for the DNS to propogate, then anyone should be able to access your ipfs-hosted site without any extra configuration simply at `http://your.domain`.

Happy Hacking!

By
[Whyrusleeping](https://github.com/whyrusleeping)


