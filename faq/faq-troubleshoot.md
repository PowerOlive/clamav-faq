# Troubleshooting FAQ

## After ClamAV is installed, then what? How do I update / refresh the virus database?

You will need to edit the `freshclam.conf.example` file located in `/usr/local/etc`. Once that is done, you will need to run a `sudo freshclam` to download the signatures. You will need to run the command to update signatures often so that ClamAV has the most up to date signatures.

---

## How many times per hour shall I run FreshClam?

You can check for database update as often as 4 times per hour provided that you have the following options in `freshclam.conf`:

`DNSDatabaseInfo current.cvd.clamav.net`

`DatabaseMirror database.clamav.net`

---

## I get this error when running FreshClam: _Invalid DNS reply. Falling back to HTTP mode_ or _ERROR: Can't query current.cvd.clamav.net_ . What does it mean?

There is a problem with your DNS server. Please check the entries in /etc/resolv.conf and verify that you can resolve the TXT record manually:

`$ host -t txt current.cvd.clamav.net`

If you can't, it means your network is broken. You'll be still able to download the updates, but you'll waste a lot of bandwidth checking for updates.

---

## What does _WARNING: DNS record is older than 3 hours_ mean?

Freshclam attempts to detect potential problems with DNS caches and switches to use HTTPS if something looks suspicious. If this message appears seldomly, you can safely ignore it. If you get the error everytime you run FreshClam, check your system clock. If it is set correctly, check your dns settings.  If those didn't help, try putting this at the top of your cronjob:

 `host -t txt current.cvd.clamav.net; perl -e 'printf "%d\n", time;' `

The 4th field of the first line should be less than 3 &lowast; 3600 behind the output of the second line. If not, you have a caching DNS server somewhere  misbehaving.

---

## I get this error when running FreshClam: _ERROR: Connection with ??? failed_ . What shall I do?

Either your dns servers are not working or you are blocking port 53/tcp. You should manually check that you can resolve hostnames with:

`$ host database.clamav.net`

If it doesn't work, check your dns settings in `/etc/resolv.conf`. If it works, check that you can receive dns answers longer than 512 bytes, e.g. check that your firewall is not blocking packets which originate from `port 53/tcp`.

An easy way to find it out is:

`$ dig @ns1.clamav.net db.us.big.clamav.net`

---

## How do I know if my IP address has been blocked?

Run FreshClam in verbose-mode (`-v`) to view the HTTP requests and responses. If you're seeing an HTTP 403 response, then you may have been blocked. If think you've been blocked, feel free to contact us for help getting un-blocked.

If you're seeing an HTTP 429 response, then your IP may have been rate limited because it is downloading the same files from our CDN too frequently. If this happens to you, FreshClam will automatically work again after a cooldown timeout expires.

---

For other questions regarding issues with the signature databases, see our [Virus Database FAQ](https://www.clamav.net/documents/clamav-virus-database-faq).
