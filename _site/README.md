# Finnie Wallet

## What is it?

  The Finnie Wallet is the newest tool from the Koii Network, where you can create NFTs  
  in under 1 minute, for less than $0.01.
  
  
  
  
## What Can it Do? 
  - Minting Atomic NFTs is extremely affordable. 
  - Finnie uses Arweave to store your content forever. 
  - Content posted earns KOII tokens for every view. 
  - Cross-publish your NFTs to traditional sites like facebook, youtube, or instagram (Coming soon). 
  - Create custom galleries and sell your NFTs direct to collectors with 0% commission (Coming soon). 
  - Multiple blockchain networks in one (Coming soon). 
  
  
## How to connect the extension to your site

  The Finnie wallet is a Chrome extension, much like Metamask, providing its users with an easy-to-use user 
  experience for interacting with their wallets
  
  Finnie Wallet exposes an object to the global `window` object under `window.koiiWallet`
  
  
### Initialising
  In order to initialise your connection with the extension, I recommend implementing a polling function that continously  
  checks for the existence of the the global `koiiWallet` object for at least 5 seconds.
  
  Here's a simple polling function to get you started

  ```js
    /**
   *
   * @param {Function} fn Function to poll for result
   * @param {Number} timeout How long to poll for
   * @param {Number} interval Polling interval
   * @returns {Promise}
   */
  const poll = (fn, timeout, interval) => {
    var endTime = Number(new Date()) + (timeout || 2000);
    interval = interval || 100;

    var checkCondition = function (resolve, reject) {
      // If the condition is met, we're done!
      var result = fn();
      if (result) {
        resolve(result);
      }
      // If the condition isn't met but the timeout hasn't elapsed, go again
      else if (Number(new Date()) < endTime) {
        setTimeout(checkCondition, interval, resolve, reject);
      }
      // Didn't match and too much time, reject!
      else {
        reject(new Error("timed out for " + fn + ": " + arguments));
      }
    };

    return new Promise(checkCondition);
  };

  ```
  
  You can proceed to check for the extension's existence in your browser window by simply calling the above `poll()`
  
  ```js    
    // Does it exist?
    let extensionObj = await poll(() => window.koiiWallet, 5000, 200);
  ```
  
  ### Connecting to the extension

  Once you've confirmed that the extension exists, we will be faced with 2 possibilities
  
  - User is connected to Finnie
  - User is NOT connected to Finnie

  We can check the status of the user by calling the following method on the Finnie wallet
  
  ```js
   // Is it connected?
    let isConnected = await extensionObj.getPermissions();
  ```
  
  
  #### User is Connected to Extension
  One possibility is that the user has already set up the extension and has connected the Finnie wallet to your site.
  This can be confirmed by making sure that the response from `isConnected` is a `200` OK response.
  The response also returns an array of user permissions. 
  
  ```js
   if (isConnected.status === 200) // Finnie is connected to website
    /* ---- OR ---- */
   if (isConnected.data.length) // Finnie is connected to website
  ```
  
  #### User is NOT Connected to Extension
  The other possibility is that the user has Finnie installed but is not connected to your site
  
  As a result, `isConnected` will return a status of `401` and an empty array
  
   ```js
   if (isConnected.status === 401) // Finnie is disconnected from the website
    /* ---- OR ---- */
   if (!isConnected.data.length) // Finnie is disconnected from the website
  ```
  
  #### Connect the User
  
  Once we've established the user's connection status, we can programatically connect our site to the Finnie Wallet 
  by calling the following method on the `extensionObj` we initialised above
  
  ```js
    // Returns a promise
    let res = extensionObj.connect()
  ```
  
  ##### If the user has not previously connected to the site
  The Finnie wallet will greet them with a pop-up window and a 2 step authorisation process that confirms the user 
  wants to add your site to the connected sites on their Finnie wallet.
  
  Once the user completes the process of accepting the connection request, a `promise` is then returned from the 
  `.connect()` method with the following value:
  
  ```js
    {status: 200, data: "Connected."}
  ```

  If the user rejects the connection, the response value is:
  ```js
  {status: 401, data: "Connection rejected."}
  ```
  
  ##### If the user has previously authorised your site to their Finnie wallet
  Calling the `connect()` method will then attempt to automatically connect to the Finnie extension returning the following:
  
  ```js
    {status: 200, data: "Connected."}
  ```
  
  #### Recommended Method of Connecting to the Finnie Wallet
  
  The recommended method of creating a seamless UX for users is to follow the steps below:
   - Poll for Finnie wallet
     - If it doesn't exist, promp user to install it
   - If extension exists, attempt to `getPermissions()`
     - If user has permissions, attempt to `connect()` them automatically
     - Otherwise, provide a way for the user to manually connect via a link/button on your site


 ### Interacting with the Finnie Wallet Methods (after connecting)
 
 #### Disconnect
 ```js
  koiiWallet.disconnect()
 ```
 
 The Finnie wallet exposes a `window.koiiWallet.disconnect()` method or if using the `extensionObj` from above  
 we would call `extensionObj.disconnect()` 
 
 This disconnects Finnie wallet from your website and doing so would result in getting a `401` status from the 
 `getPermissions()` method described earlier. 

 After disconnecting the user will be prompted with a pop-up when attempting to `connect()` to the site again
 
 
 #### Get Address
 
 ```js
  koiiWallet.getAddress()
 ```
 
 

  
  
  
  
