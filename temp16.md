How to bypass WSJ and NYTimes paywalls?
===========================================================

#### Motivation
*	You know why ;)
*	It is a fun weekend gig!

#### Initial thoughts:
*	Definitely use chrome extension to modify local web content.
*	Nee to learn how to build a chrome extension.
*	Modify cookie or header or something to get full content from WSJ and NYT.

#### Basic understanding of how chrome extension works:
*	To inject javascript into a web page, you can either add [Content Scripts](https://developer.chrome.com/extensions/content_scripts) or add a background page with javascript.
*	Content Script can read & change DOM of current web page, but it cannot use `chrome.webRequst` API to modify cookies and headers and other stuffs.
*	Complex logic can be done in the background JS, which can access all chrome.* API
*	How can Content Script interact with background JS? Through `chrome.runtime.sendMessage` function and `chrome.runtime.onMessage` event handler. Read this awesome tutorial: [how-to-make-a-chrome-extension](https://robots.thoughtbot.com/how-to-make-a-chrome-extension), and [chrome doc: messaging](https://developer.chrome.com/extensions/messaging)

#### Implementation Details:

##### Wall Street Journal:
*	Good news! Search github, there is already someone broke WSJ [wsj-walljumper](https://github.com/hatboysam/wsj-walljumper). The logic is: in `chrome.webRequest.onBeforeSendHeaders`, add a listener to remove cookie, and change `referer` to `google.com`. Cool!

##### New York Times:
*	I cann't find a existing working code, and I try to clear all cookies, session storages, and local storages, but none of these is working. NYT is very smart about this.

*	Then I get the first approach: Notice the fact that there is no limitaion to read NYT content if your chrome is in `incognito mode`. So I can just add a `click` event handler for content link to open a new incognito window. Easy to implement. Basic logic:
	
	```javascript
	// in content_script.js
	$(body).on( "click", "a", sendMsgToBackground );

	function sendMsgToBackground( event ){
		chrome.runtime.sendMessage( { "message": "open_new_tab", "url": event.currentTarget.href } );
	}
	```
	```javascript
	// in background.js
	chrome.runtime.onMessage.addListener(
	  function(request, sender, sendResponse) {
	    if( request.message === "open_new_tab" ) {
			chrome.windows.create({"url": request.url, "incognito": true});
	    }
	  }
	);
	```
*	There are a few issues with this approach, the obvious one is that every time you click a link, it opens a new window. It is not the normal way to browser news pages. Need a seamless way!

*	I notice that the full content is already in my local NYT webpage, but it is hide by javascript. Well, why not get rid of that javascript file? haha!

*	In chrome dev tool, search a few keywords like `gateway` in all scripts to find the javascript file that obviously does the pop up blocker job. Here is [How to search all loaded scripts in Chrome Developer Tools](http://stackoverflow.com/questions/4145266/how-to-search-all-loaded-scripts-in-chrome-developer-tools). Or, just one by one, read "pretty-printed" suspicious nyt javascript files, we can find `js/mtr.js` is the file we want to get rid of. (BTW: NYT is using `backbonejs`, good choice! )

*	Now let's just use awesome [`chrome.webRequest` API](https://developer.chrome.com/extensions/webRequest) to block this file from being downloaded

	```javascript
	chrome.webRequest.onBeforeRequest.addListener(
		function() {
			console.log( "we are going to block nytimes gateway javascript" );
			return { cancel: true };
		}, {
			// it takes some time & tricks to find out these gateway javascript files!
			urls: [ "*://*.com/*mtr.js" ],
			// target is script
			types: [ "script" ]
		},
		[ "blocking" ]
	);
	```
*	Notice how the url patterns are matched. Read [Match Pattern doc](https://developer.chrome.com/extensions/match_patterns) for details.

*	This trick is actually very easy to counter. I would suggest NYT developers to concatenate and minify your Javascript files, and maybe attach a random string in your concatenated Javascript filenames. If that happens, I might just block that concatenated javascript file, but it would break other functionalities of the web page... Or I will find another way.


#### Show me the code!
*	The complete source code is in [my github repo](https://github.com/njuljsong/wsjUnblock), feel free to take a look, clone & test it, and give me feedbacks!

*	This trick is actually very easy to counter. I would suggest NYT developers to concatenate and minify your Javascript files, and maybe attach a random string in your concatenated Javascript filenames. If that happens, I might just block that concatenated javascript file, but it would break other functionalities of the web page... Or I will find another way.



Tags: Javascript, Chrome Extension
