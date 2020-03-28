## Installation

This documentation is for the base JavaScript plugin. If you're looking for the Wordpress plugin installation, see [here](https://commentbox.io/docs/wordpress).

Before you begin, please create a project in the [CommentBox.io Dashboard](https://dashboard.commentbox.io). Click [here](https://commentbox.io/docs/dashboard) to learn more about the dashboard and how to use it.


```bash
npm install commentbox.io --save
```
or
```html
<script src="https://unpkg.com/commentbox.io/dist/commentBox.min.js"></script>
```

## Usage

In your HTML, place an empty `div` tag with the "commentbox" class:
```html
<div class="commentbox"></div>
```

Then, in your JavaScript, initialize with your project ID:
```js
import commentBox from 'commentbox.io';
// or
const commentBox = require('commentbox.io');
// or if using the CDN, it will be available as a global "commentBox" variable.

commentBox('my-project-id');
```

That's it!

## Usage in React

Supporting React is pretty simple:

```jsx
import React from 'react';
import commentBox from 'commentbox.io';

class PageWithComments extends React.Component {
    
    componentDidMount() {

        this.removeCommentBox = commentBox('my-project-id');
    }

    componentWillUnmount() {

        this.removeCommentBox();
    }
    
    render() {
        
        return (
            <div className="commentbox" />
        );
    }
}
```
The principles are the same as in the non-React example, except that you'll want to be sure to call the `commentBox` function in the `componentDidMount` lifecycle event. The `commentBox` function returns a handy function to clean itself up, so you can call this cleanup function when your component unmounts in`componentWillUnmount`.

## Advanced Usage

With its default options, CommentBox.io works well for most use cases, however, there are some cases that require extra customization. This is handled by the optional second argument to the `commentBox` function, which is an options object. Here are its defaults:
```js
commentBox('my-project-id', {
    className: 'commentbox', // the class of divs to look for
    defaultBoxId: 'commentbox', // the default ID to associate to the div
    tlcParam: 'tlc', // used for identifying links to comments on your page
    backgroundColor: null, // default transparent
    textColor: null, // default black
    subtextColor: null, // default grey
    singleSignOn: null, // enables Single Sign-On (for Professional plans only)
    /**
     * Creates a unique URL to each box on your page.
     * 
     * @param {string} boxId
     * @param {Location} pageLocation - a copy of the current window.location
     * @returns {string}
     */
    createBoxUrl(boxId, pageLocation) {

        pageLocation.search = ''; // removes query string!
        pageLocation.hash = boxId; // creates link to this specific Comment Box on your page
        return pageLocation.href; // return url string
    },
    /**
     * Fires once the plugin loads its comments.
     * May fire multiple times in its lifetime.
     * 
     * @param {number} count
     */
    onCommentCount(count) {
        
    }
});
```

We will explore these options via various use cases:

### Styling the plugin

By default, the plugin's background is transparent, to adapt to your website. Since most websites tend to have light backgrounds, the default text color is black. However, you may change these colors with the `backgroundColor`, `textColor`, and `subtextColor` options (the "subtext" is the text below the comment form).

For example, here's a "dark mode" theme:
```js
commentBox('my-project-id', {
    backgroundColor: '#000',
    textColor: '#fff'
});
```

### Using a different identifying class

If you're using the "commentbox" class for some other purpose, you can simply pass in your preferred class as the `className` option.
```html
<div class="my-box-of-comments"></div>
```
```js
commentBox('my-project-id', { className: 'my-box-of-comments' });
```

### Using a different box ID

If you're using the "commentbox" ID for some other purpose, you can simply pass in your preferred ID as the `defaultBoxId` option.
```html
<div class="commentbox"></div>
```
```js
commentBox('my-project-id', { defaultBoxId: 'my-commentbox' });
```

Alternatively, you may give the `div` whatever ID you wish:
```html
<div class="commentbox" id="my-commentbox"></div>
```
```js
commentBox('my-project-id');
```

### Multiple boxes on the same page

In order to support multiple boxes on the same page, each with its own set of comments, simply add a unique ID to each box:
```html
<div class="commentbox" id="article"></div>
<div class="commentbox" id="sidebar"></div>
```

### Using a different "tlc" param

In order for us to link directly to a comment made on your page, we pass in an extra query parameter to your URL. By default, this parameter is "tlc". For example, if a user posted a comment to `https://example.com/my-page`, we would link to that comment using this URL: `https://example.com/my-page?tlc={commentId}`.

If you are already using "tlc", or simply wish to rename it to something else, you can replace the `tlcParam` option:
```js
commentBox('my-project-id', { tlcParam: 'c_id' });
```

### Displaying comment count outside of the plugin

Some implementations may choose to have a "comments" button that displays the box only when clicked, and may want to show the number of comments available before the box is shown. To get this number, simply pass in the `onCommentCount` option:
```js
commentBox('my-project-id', {
    onCommentCount(count) {
        // use the count however you wish.
    }
});
```
Note that this event may fire multiple times.

### Uniquely identifying boxes on your site

The plugin determines which boxes are unique based on the URL to that box, minus the query parameters. For example, if your page at `https://example.com/my-page` contained a single box with all it's default options, the URL to that box would be `https://example.com/my-page#commentbox`. 

As mentioned above, you can also change the box ID, or have multiple boxes with their own unique IDs, which would generate box URLs using those IDs as the hash instead of "#commentbox".

By default, the plugin ignores query parameters. This is because for most use cases, query parameters are not widely used to generate page content, and are instead used as either tracking mechanisms or to supply extra arguments to the same content. This means that both `https://example.com/my-page` and `https://example.com/my-page?foo=bar` will contain the same box with the same comments, which is usually desirable.

There are of course cases where this is not desirable, for example if your website content relied on a "page_id" parameter: `https://example.com/pages.php?page_id=5`. In this case, all your pages would have the exact same set of comments!

In order to account for this situation, you can modify how the plugin creates box URLs using the `createBoxUrl` option.

In the following example, we will leverage the [`qs` module](https://www.npmjs.com/package/qs) in order to get the "page_id" parameter:
```js
import qs from 'qs';

commentBox('my-project-id', {
    createBoxUrl(boxId, pageLocation) {
    
        const queryParams = qs.parse(pageLocation.search.replace('?', ''));
        const relevantParams = {
            'page_id': queryParams['page_id']
        };
        pageLocation.search = qs.stringify(relevantParams); // we will now include "?page_id=5" in the box URL
        pageLocation.hash = boxId; // creates link to this specific Comment Box on your page
        return pageLocation.href; // return url string
    }
});
```
Note that `pageLocation` is a `Location` object, which you can read more about here: https://developer.mozilla.org/en-US/docs/Web/API/Location.

Another such case is if you are using the URL hash for routing purposes. In this case, you would not want the hash to be replaced with the hash of the box, but you would still want each box to be unique based on its ID. We could accomplish this in many ways, but one such way would be to include the box ID in the query params, instead of the hash:


```js
commentBox('my-project-id', {
    createBoxUrl(boxId, pageLocation) {
    
        pageLocation.search = `?box=${encodeURIComponent(boxId)}`; // we will now include "?box=commentbox" in the box URL
        //pageLocation.hash = boxId; leave the hash alone
        return pageLocation.href; // return url string
    }
});
```

### Enabling Single Sign-On

This option is available to projects on the Professional plan only.

Single Sign-On lets your users sign in using your site's own native authentication system. It's flexible and powerful enough to automatically utilize existing user sessions, creating a zero-friction method for your website members to leave comments.

To enable, specify an object as the  `singleSignOn` option. Here it is with its defaults:
```js
commentBox('my-project-id', {
    singleSignOn: {
        buttonText: 'Single Sign-On', // The text to show on the sign in button.
        buttonIcon: '', // The icon to show on the sign in button. Must be an absolute URL.
        buttonColor: '', // The sign in button's color. Default is black.
        autoSignOn: false, // Attempts to automatically log the user into CommentBox.io with custom auth.
        onSignOn(onComplete, onError) {
            // pass in the signed user payload to onComplete.
        },
        onSignOut() {
            // optionally log the user out of your website's native auth system.
        }
    }
});
```
We will go over each option below, but at minimum you must specify an `onSignOn` function.

#### Styling the sign in button

If the user navigates to the sign in screen in the plugin with Single Sign-On enabled, they will encounter an additional button that allows them to use their existing user account from your website to sign in. The `buttonText`, `buttonIcon`,and `buttonColor` options deal with styling this button.

#### `autoSignOn`

This option allows the plugin to attempt to automatically log the user in with their existing user account from your website to sign in. Note that this is only effective if they are not already signed in to the plugin.

With `autoSignOn` set to false (which is the default), plugin users may sign in with their existing user account from your website by navigating to the sign in screen in the plugin.

Here are a few scenarios to illustrate how it works when `autoSignOn` is set to true:

##### Scenario 1
Website login status: not logged in.
Plugin login status: not logged in.

In the plugin, the user will not be automatically logged in, but they will have the option to sign in with Single Sign-On from the plugin's sign in screen (in addition to the usual Social and email options).

##### Scenario 2
Website login status: logged in.
Plugin login status: not logged in.

In the plugin, the user will be automatically logged in using the user's data from your website.

##### Scenario 3
Website login status: logged in OR not logged in.
Plugin login status: logged in.

In the plugin, the user will **not** be automatically logged in using the user's data from your website, since they are already logged in (irrespective to how they chose to log in).

#### `onSignOn`
The `onSignOn` function is how the plugin communicates to your website that a user wants to use their auth data from your website. Your code must then send this data back as a signed [JSON Web Token](https://jwt.io/introduction/) (JWT).

You must write **server-side** code to perform the signing, which uses a **secret** key found in your project's setup screen in the CommentBox.io dashboard. DO NOT expose your secret key in any client-side code. Whenever `onSignOn` is called, you must then call your server-side code to return the signed user data to the plugin using the `onComplete` callback. If an error occurs instead, pass an `Error` object to the `onError` callback.

##### Signing user data
To perform the signing, you must create a payload as described below, and sign it with your secret, using the `HS256` algorithm. Once you have the JWT string, pass it to the `onComplete` callback.

Here's an example payload with all relevant claims:
```json
{
"sub": 1, // integer or string representing your user's unique ID
"email": "jane@example.com", // must be unique as well
"name": "Jane Doe", // required, not empty
"picture": "", // an absolute URL, or empty
"iat": 1541289621, // date issued as a unix timestamp (in seconds)
"exp": 1541289921 // token expiry date, must be no more than 10 minutes after iat
}
```
Here's a Node.js example of signing the token:
```js
const express = require('express');
const jwt = require('jsonwebtoken');
const app = express();

app.get('/sso', (req, res) => {
    
    const now = Math.round(Date.now() / 1000); // now in seconds
    const payload = {
       sub: 1, // normally you'd get the user ID from your own cookies or bearer token.
       email: 'shaun@example.com',
       name: 'Shaun', 
       picture: '',
       exp: now + (60 * 5), // expires 5 minutes from now
       iat: now
    };
    const secret = 'this-is-you-secret-from-the-dashboard';
    const token = jwt.sign(payload, secret, { algorithm: 'HS256'});
    
    res.send(token);
});
```
Then, in your client-side code:
```js
commentBox('my-project-id', {
    singleSignOn: {
        onSignOn(onComplete, onError) {
            
            fetch('/sso').then(response => {
                
                if(response.ok) {
                    return response.text();
                }
                throw new Error('Could not sign in.');
            })
            .then(onComplete)
            .catch(onError);
        }
    }
});
```

A few notes on the payload:
- Notice that these tokens are **short-lived**. The maximum lifespan of an acceptable token is 10 minutes.
- The email must be unique to the user. If you have users with different IDs and the same email, only the account that logs in first will be accepted. The other accounts will fail silently (at this time).

#### `onSignOut`
This function gets called whenever the user is logged in via Single Sign-On and logs out in the plugin. You may use this callback to log the user out of your website as well, but it's not required. It depends on the experience you're aiming for. One common use case is if you wish to make the comment box only accessible to users who are logged in to your website. When they log out, you can use `onSignOut` to hide the comment box again.











