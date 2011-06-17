## Updated for Rails 3 & PLUpload
This is just a sample app using Rails 3 which allows you to upload directly to Amazon S3 and bypass the Rails server entirely.  This is useful for cloud hosting like Heroku which has a 30 second request timeout and resets the connection on any uploads which go over that limit.

- Uploading:  [PLUpload](http://plupload.com)
- Uploading Technology:  **jQuery API** using **Flash, Google Gears, Silverlight, BrowserPlus, HTML5** to do the heavy lifting

**Note**: Currently Amazon S3 **DOES NOT** support HTML5 uploading.  [More info here](https://forums.aws.amazon.com/thread.jspa?threadID=34281)

## Debugging Flash Uploaders
For debugging purposes involving Flash uploading, I would highly recommend using the [WireShark](http://www.wireshark.org/download.html) tool which allows you to sniff the network traffic for XML responses sent back from Amazon S3.

Link to download WireShark:  http://www.wireshark.org/download.html
How to install on MacOSX: http://www.youtube.com/watch?v=IxeHm0BKdwc

Amazon S3 will always send back an XML response to the Flash uploader and currently the uploader does nothing with it except give a vague error response which isn't helpful.  

So what you have to do is first close out other items that may be using the internet at the moment (to minimize noise) and then run WireShark and capture the network traffic while you try and upload a file.  You'll start to see a whole bunch of stuff show up in WireShark so go ahead and filter it out by typing "xml" into the Filter box at the top of the window.  This will show only the XML responses back from S3 like this:  http://i.imgur.com/VZ5ZC.png   

The one that is highlighted in that image is a 403 forbidden response which could mean that you are missing something in your Amazon S3 policy or that you have extra keys that it doesn't know about.  Some uploaders actually send extra data with the request (like the **name** item shown) that Amazon doesn't know about so you have to tell it to accept the params with any value, which is what the blank `starts-with` in the policy item does:

    ['starts-with', '$name', ''],

Hopefully WireShark helps you solve many headaches in the future.  Happy uploading!

## Steps to make the example work:

- Clone this repo and run `bundle install`

- Add your S3 bucket and keys to `config/amazon_s3.yml`

- **For Flash**: Make sure a public readable `crossdomain.xml` exists in the root directory of the S3 bucket you specified in `config/amazon_s3.yml`, with the following content (once you get it working, you **should** limit access to your domain to be more secure).  An example can be [found here](https://gist.github.com/995182)

- **For Silverlight**: Make sure a public readable `clientaccesspolicy.xml` exists in the root directory of the S3 bucket you specified in `config/amazon_s3.yml`, with the following content (once you get it working, you **should** limit access to your domain to be more secure).  An example can be [found here](https://gist.github.com/gists/995348)

- Also make sure a folder named **test** exists in that bucket (if not, one will be created). Files will be uploaded to that folder.

- Run the server locally or push it up to Heroku and start uploading files. 

- If you want to explore the code, the bulk of it is in the `helpers/uploads_helper.rb` file and then a convenient helper for using/customizing the uploader can be found in the `views/uploads/new.html.haml` file 

**Note** PLUpload uses CSS to absolutely position the Flash/Silverlight objects over top of your custom "Upload" button, so if you move that button at all during the upload process the Flash/Silverlight object will still be there.

## Collaboration
I am always open to others collaborating on this example and I welcome any pull requests.  If you find any issues, please report them in the **Issues** area above.  Thanks!!

## Credits
### Original Author
Nico Ritsche:  https://github.com/ncri
### Original Blog Post 
http://www.railstoolkit.com/posts/rails-amazon-s3-uploader-with-progress-bar-2010-update