# Oops!  All InnerHTML!
Making a mockery of Securly for Edge (and Securly for Chromebooks) since 2023

## Give me the exploit!

Precompiled versions are available [here](https://1984-was-a-warning-not-a-how-to-guide.bangsparks.com/).
The website also has entrypoints for Securly for Edge and Securly for Chromebooks (untested), including automatic detection of which one is installed.

## Building the bookmarklet

The bookmarklet source (`oops_all_exploitable.src.js`) is designed to be built using [this compiler](https://www.yourjs.com/bookmarklet/).

Copy and paste [the raw source](https://raw.githubusercontent.com/mylesbartlett72/oops-all-innerhtml/main/oops_all_exploitable.src.js?token=GHSAT0AAAAAACDHUW7YGWKXNB4DMFXWQZSAZEJ2OYA) into it (Raw Javascript option) and click [Convert to Data URL].

## Making your own HTML injection payload

In case someone from Securly finds this, I am not going to directly specify how I got HTML injection.

However, you should be able to find it pretty easily.  Once you do, [this](https://gchq.github.io/CyberChef/#recipe=To_Base64('A-Za-z0-9%2B/%3D')URL_Encode(true)) will encode your HTML into the format the URL parameters want.  Make sure your payload isn't too long, or Chrome will truncate the URL and it won't work (I had to set the image you find in my payloads to 50% scale and max PNG compression for it to work - although I could probably have made it larger I couldn't be bothered to find the exact threshold).

## So how does it work?

Its just point-blank (but I made a custom payload, and ported the Securly for Chromebooks entrypoint to Securly for Edge).

Basically, it sets some parameters on an internal extension page in the Securly extension.  These parameters are not sanitised, and control text on the page.  The text is set through a call to `a_span_element.innerHTML()` using the data from the user-controlled parameter.  Although CSP prevents us from doing *really* cool stuff like a direct XSS on the privileged extension page, we can still create HTML elements such as links.  These links can get us an `about:blank` page with access to the extension page.  As `about:blank` now has privileges to the extension page, any code running on it can affect the extension.

Normally, you cannot run bookmarklets on extension pages (at least not on extensions force-installed by enterprise policy).  However, `about:blank` does not have this restriction.

Through this, we can get an `about:blank` page with access to the extension page, run JavaScript code on that, get the extension page, get the background script of the extension from the extension page, and call `extension_background_script.stop()`, which renders the extension inert until the extension is relaunched.

## But why did you do this?

For Educational Purposes[tm].  Also, because it was fun.

Also because I believe that students deserve privacy, not telescreens.

## Answers to other inevitable questions

Q: It's not working!
A: Ensure you followed all the instructions.  It also may have been patched.

Q: I get a page saying `Failed to detect extension!` after doing step 2
A: Either:
Securly may have changed their extension code and the file that the autodetection page relies upon to fingerprint the extension no longer exists.  Click the relevant button for your extension, and continue with step 3
Or:
Securly does not exist (yay!)
Or:
Securly is acting through a different method

Q: I get a popup message from Chrome saying `The code has already been injected - aborting!`
A: You probably double-clicked the bookmarklet.  If you are certain that this is not the case, you can replace `if (document.getElementById("point_blank_bsod_edition_header") === null){` with `if(true){` in the bookmarklet source and build it yourself to patch out this check.

Q: Will this work for (insert other Chrome/Edge extension)?
A: If you can find a suitable entrypoint on your own, the bookmarklet should work.  The entrypoint, of course, will not.

Q: I'm a sysadmin and I don't want people in my organisation using this.
A: If you use Securly, don't.  People in your organisation will no longer have an incentive to use this.

Q: I work for Securly and I don't want your code to break our extension!
A: I refer you to the reply given in Arkell v Pressdram.  Also, George Orwell called and he wants his telescreens back.

Q: I work for Securly and the above answer was not enough for me.
A: I missed the part where thats my problem.

Q: I work for Securly and I am still asking questions!
A: Have you considered that 1984 was a warning, not a how to guide?  People deserve privacy.
