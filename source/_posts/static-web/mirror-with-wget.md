---
title: Static Site Mirror with Wget
date: 2024-12-21 11:49:43
tags:
  - wget
  - shell
categories:
  - static web
---
### My example: 

`/usr/bin/wget --no-host-directories --directory-prefix="/var/www/<mirror_site>/www/" -4 -w1 --convert-links -r --no-parent --cut-dirs=1 --no-check-certificate "http://siteurl.ru/subdirectory/"`

### Deniskins example: 

`/usr/bin/wget --no-host-directories --directory-prefix="<mirror_site>/public_html/"  --convert-links  -r -l inf  --cut-dirs=1 --no-if-modified-since --no-parent -N "http://siteurl.ru/subdirectory/"`

## Options

_explained in `man wget`_

`--no-host-directories`
Disable generation of host-prefixed directories. By default, invoking Wget with `-r http://fly.srk.fer.hr/` will create a structure of directories beginning with `fly.srk.fer.hr/`. This option disables such behavior.

`-P`
`--directory-prefix="/var/www/site.directory/www/"`
The  directory  prefix  is  the  directory  where  all  other files and subdirectories will be saved to, i.e. the top  of  the  retrieval  tree.   The  default  is  `.`  (the  current         directory).

`-4`
With --inet4-only or -4, Wget will only connect to  IPv4  hosts, ignoring  AAAA records in DNS, and refusing to connect to IPv6 addresses specified in URLs.  Conversely, with --inet6-only or -6, Wget will only connect to IPv6 hosts and ignore A records and IPv4 addresses.

`-w1`
`-w seconds`
`--wait=seconds`
Wait  the  specified  number  of  seconds  between  the retrievals.  Use of this option is recommended, as it lightens the server load by making the requests less frequent.  Instead  of  in  seconds,  the  time  can  be specified in minutes using the "m" suffix, in hours using "h" suffix, or in days using "d" suffix.

`--convert-links`
`-k`
After  the  download  is complete, convert the links in the document to make them suitable for local viewing. This affects not only the visible hyperlinks, but any part of the document that links  to  external  content, such as embedded images, links to style sheets, hyperlinks to non-HTML content, etc.

Each link will be changed in one of the two ways:

- The  links  to files that have been downloaded by Wget will be changed to refer to the file they point to as a relative link.
Example: if the downloaded file `/foo/doc.html` links to `/bar/img.gif`, also downloaded, then  the  link  in `doc.html`  will  be  modified  to point to `../bar/img.gif.`  This kind of transformation works reliably for arbitrary combinations of directories.

- The links to files that have not been downloaded by Wget  will  be  changed  to  include  host  name  and absolute path of the location they point to.
Example: if the downloaded file `/foo/doc.html` links to `/bar/img.gif` (or to `../bar/img.gif`), then the link in doc.html will be modified to point to `http://hostname/bar/img.gif`.

Because  of  this, local browsing works reliably: if a linked file was downloaded, the link will refer to its local name; if it was not downloaded, the link will refer to its full Internet address rather than presenting a broken link.  The fact that the former links are converted to relative links ensures that you can move  the downloaded hierarchy to another directory.

Note  that  only at the end of the download can Wget know which links have been downloaded.  Because of that, the work done by -k will be performed at the end of all the downloads.

`-r`
`--recursive`
Turn on recursive retrieving. The default maximum depth is 5.

`--no-parent`
Do not ever ascend to the parent directory when retrieving recursively.  This is a useful  option,  since  it guarantees that only the files below a certain hierarchy will be downloaded.

`--cut-dirs=1`
Ignore `number` directory components.  This is useful for getting a fine-grained  control  over  the  directory where recursive retrieval will be saved.

Take, for example, the directory at `ftp://ftp.xemacs.org/pub/xemacs/`.  If you retrieve it with `-r`, it will be saved  locally  under  `ftp.xemacs.org/pub/xemacs/`.  While the `-nH` option can remove the `ftp.xemacs.org/` part, you are still stuck with `pub/xemacs`.  This is where `--cut-dirs` comes in handy; it makes Wget not "see" number remote directory components.  Here are several examples of how `--cut-dirs` option works.

No options        -> ftp.xemacs.org/pub/xemacs/
`-nH`               -> pub/xemacs/
`-nH --cut-dirs=1`  -> xemacs/
`-nH --cut-dirs=2`  -> .

`--cut-dirs=1`      -> ftp.xemacs.org/xemacs/
...

If you just want to get rid of the directory structure, this option is similar to a combination  of  `-nd`  and `-P`.  However, unlike `-nd`, `--cut-dirs` does not lose with subdirectories --- for instance, with `-nH --cut-dirs=1`, a `beta/` subdirectory will be placed to `xemacs/beta`, as one would expect.

`--no-check-certificate "http://siteurl.ru/subdirectory/"` Don't check the server certificate against the available certificate authorities.  Also don't require the URL host name to match the common name presented by the certificate.

As of Wget 1.10, the default is to  verify  the  server's  certificate  against  the  recognized  certificate authorities,  breaking  the SSL handshake and aborting the download if the verification fails.  Although this provides more secure downloads, it does break interoperability with some sites that worked with previous Wget versions, particularly those using self-signed, expired, or  otherwise  invalid  certificates.   This  option forces  an  "insecure"  mode  of  operation  that turns the certificate verification errors into warnings and allows you to proceed.

If you encounter "certificate verification" errors or ones saying that "common name doesn't  match  requested host  name", you can use this option to bypass the verification and proceed with the download.  Only use this option if you are otherwise convinced of the site's authenticity, or if  you  really  don't  care  about  the validity  of its certificate.  It is almost always a bad idea not to check the certificates when transmitting confidential or important data.  For self-signed/internal certificates, you should download  the  certificate and  verify  against  that instead of forcing this insecure mode.  If you are really sure of not desiring any certificate verification, you can specify --check-certificate=quiet to tell wget to  not  print  any  warning about invalid certificates, albeit in most cases this is the wrong thing to do.

---

{% blockquote lnotestoself https://lnotestoself.blogspot.com/2011/02/wget-and-ssl-certificates.html %}
<div style="text-align: left">

### To use certificates with wget

First get the root cerificate for the site.

1. **Just have one certificate**

You can either go to the site with Firefox, click on the little lock in the status bar, in the Security tab click View certificate, click on Details, click on the topmost certificate in the “Certificate Hierarchy”, click on “Export…” and save as “.pem”.

Now you can use ssl with wget like this:

`wget --ca-certificate={the_cert_file}  https://www.google.com`

2. **A directory full of certificates**

Or you can have an entire directory full of certificates that wget can choose from. Useful if you want to use all the certificates from the KeyChain.app.

If you export all the certificates from KeyChain.app in one go (you can select multiple and export all at once), then you must split up the file into individual files for each certificate, and name the certificates by their hash and a “.0” at the end.

If the certificates from KeyChain.app are saved into the file Certificates.pem, then this splits these commands splits the files and renames them.

``` sh
mkdir certdir
cd certdir

n=0 ; cat ../Certificates.pem | while read x; do if [ "$x" == "-----BEGIN CERTIFICATE-----" ]; then n=$((n+1)); fi; echo >>cert-$n.pem $x ; done

for f in cert-*; do n=$(openssl x509 -hash -in $f -noout); mv $f $n.0; done

cd ..
```

Now you can use ssl with wget like this:

`wget --ca-directory=certdir  https://www.google.com`

If you want to, you can put the certdir in your ~/.wgetrc file so you won't have to specify it all the time.  Just put the line `ca_directory = {full path to certdir}` and you're done.

Actually, you also need some way to handle revocation of certificates. But that's for another day.

</div>

{% endblockquote %}

---

## Other options

`-l depth`
`--level=depth`
Set the maximum number of subdirectories that Wget will recurse into to depth.  In order to prevent one  from accidentally downloading very large websites when using recursion this is limited to a depth of 5 by default, i.e.,  it  will  traverse  at most 5 directories deep starting from the provided URL.  Set `-l 0` or `-l inf` for infinite recursion depth.

`--no-if-modified-since`

`-N`
`--timestamping`
Turn on time-stamping.

`--no-if-modified-since`
Do  not send If-Modified-Since header in `-N` mode. Send preliminary HEAD request instead. This has only effect in `-N` mode.
