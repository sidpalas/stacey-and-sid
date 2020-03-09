## Stacey and Sid 2018 travel blog

https://staceyandsid.com/

This is a website Stacey made while we were travelling. It was initially on squarespace but the $16/month was a lot once we stopped actively updating it so I decided to migrate it off of squarespace. The below outlines the process (mostly so I can remember what all I did).

### Process of migrating off squarespace

On squarespace site:

`config -> settings -> advanced -> import/export -> export`

--

Importing into wordpress sort of worked... but all of the image galleries were messed up so I decided to try and wrangle the xml file into a markdown format compatible with jekyll.

--

I found a few people's prior work on converting the generated xml which got me started:
 - https://gist.github.com/evanwalsh/6131008
 - https://gist.github.com/spiffytech/e73777e167dc5a8b6a87 (builds on the first)

The script was failing for images that didnt have a `src` so for those I adjusted the script to use the `data-src` attribute.

Also, some of the images were being served from a different cdn which the script was missing.

---

There were some posts showing up as drafts within the export that were not visible from within the squarespace gui (and they had all actually been published, so I modified the script to ignore any non-published items)

the `_attachments` and `_pages` are not useful based on how this squarespace site was structured. All of the relevant images are also included in the `_posts` (the pages were galleries that were being embedded in posts)

---

Conversion from html to markdown mostly worked but there were some quirks with spacing I had to fix with global find and replace:

- Markdown images missing newlines: 
```
![](` -> `\n![](
```

- Removing useless links to _pages: 
```
sed -i.bak '/href=\"\//d' wordpress-no-comments.xml
```

- YouTube video iframes dont get copied properly -- there are only a few. Just copied manually

- Comments in xml showing up in md

```
cat wordpress.xml  | awk '/<!--/ {off=1} /-->/ {off=2} /([\s\S]*)/ {if (off==0) print; if (off==2) off=0}' > wordpress-no-comments.xml
```

- Bulleted lists dont have space:

```
*Sta --> * Sta
*Sid --> * Sid
```

- headers didn't have spaces
```
(^#{2,3}) --> $1 
```

---

After getting things cleaned up, I initialized a new jekyll site and deployed using Netlify linked to this git repo.

