THIS IS ONLY AN IDEA - NO IMPLEMENTATION YET

autocdn
=======

A Content Delivery Network reverse proxy that means any site can use a third-party CDN by simply changing the served subdomain of the required file eg. in the \<img> src to point to autocdn. It also supports image processing by encoding options in the URL.

## How

Assume we have a typical web site with images under an images folder 

eg. http://example.com/images/background.jpg

and that autocdn is set up at cdn.example.com. For the website to use autocdn would simply mean changing the url to 

eg. http://cdn.example.com/images/background.jpg

On the first such request received by the server for that url (ie one client), autocdn would 

1. remove the cdn. prefix
2. request the image from http://example.com/images/background.jpg
3. upload the image to a real CDN, like Amazon Cloudfront, then store the CDN URL locally
4. return a "301 Moved Permanently" code, with the CDN URL
5. the browser would then rerequest the image from the CDN

Thereafter, the browser would request the image direct from the CDN. However, further requests for http://cdn.example.com/images/background.jpg would be handled like this :

1. retrieve the CDN URL from local storage
2. return a "301 Moved Permanently" code, with the CDN URL
3. the browser would then rerequest the image from the CDN, and further by the browser requests would target the CDN

Of course this means that the first request would involve the image being downloaded from the main site, uploaded to the CDN, then downloaded from the CDN - three image transfers instead of one, plus latency from multiple HTTP requests. But first notice that the extra two transfers are server-server, so probably over very fast connections, and the autocdn server location should be optimised for network proximity to the CDN upload location and the site server. 

In reality, on a high traffic site, there may well be more than one client requesting the image before it has been uploaded to the CDN. To solve this, and to speed up that first request, autocdn could return the image after step 2 until it has been uploaded to the CDN.

## Image Processing

autocdn can also render and manage multiple versions of the original source image. Mobile-responsive sites can greatly benefit from the ability to download reduced versions of the images that would be served to a desktop client. autocdn enables this by adding a simple suffix.

### Resizing and cropping

background__100x120s.jpg	stretch image to 100 wide and 120 high
background__wx120s.jpg	stretch image to original width and 120 high
background__100x120pb.jpg	fit image proportionally to 100 wide and 120 high, and pad with black
background__100x120pw.jpg	fit image proportionally to 100 wide and 120 high, and pad with white
background__100xhpb.jpg	fit image proportionally to 100 wide and original height, and pad with black
background__100x120f.jpg fill 100 wide and 120 high with image proportionally, cropping excess pixels
background__100x120ctl.jpg crop image to 100 wide 120 high from top left corner, without scaling
background__100x120cc.jpg crop image to 100 wide 120 high from centre, without scaling

### Double extension

Allows format conversion

background__.png.jpg	-> returns a jpg version of the source png file (obviously won't support png alpha channel or transparency)




background__200xh_

