# Cars-Club-backend-reminders

My <a href="https://github.com/Aleksandar15/Cars-CLub-backend">Cars Club Backend</a> Reminders

---

<h3>APP INFO ENDS HERE.</h3>

--- 
<h3 style="text-align: center;">NO NEED TO READ BELOW ARE MY REMINDERS & JOURNALS & PLANS 😊</h3>

---

##### Extras (_reminders for me_)

1.  I've updated `users`' table's column `refresh_tokens` array to instead be a separate table `refresh_tokens` which has a _one to many_ relationship with `users` table (_multiple 'refresh tokens' can belong to a single user_) referencing a `FOREIGN KEY` of `user_id` with `ON DELETE CASCADE` constraint ensuring if a user deletes their account then all of their "refresh_token"'s rows would be removed with that action.

2.  NOTES about `refreshTokenController.ts` (or rather a _warning_):
    - On the frontend, in my React app, I **_must_** use a state of `flag` (_update_: or rather the `'loading'`Redux Toolkit state)
      in-between the `/refreshtoken` endpoint HTTP (Axios interceptors) requests, because
      it serves a purpose for the slow-internet users, whereas: without it
      if they made a rapid fast requests before response is received
      they'll have a failed response on the 2nd (or 3rd, etc.) request
      because this `refreshTokenController` will read the same `refreshToken`
      cookie twice -> and will 'think' the 2nd request was an attempt
      to re-use token, since I don't like the #1st Fix: a queue and
      flag on the backend (or use a caching library like `node-cache`): which would mean I keep the `refreshToken` in a
      cache for a little while & allow re-use for a brief period of time
      for the sake of slow-internet users & thus breaking my rules of
      1 refreshToken = 1 request; and further making my server a little bit less
      secure: by openning a window for a fast enough hacker to re-use
      the token on behalf of the victimized user without getting caught in my "detect refreshToken reuse" logic. I don't wanted to
      make such a sacrifice so instead leave the backend's `refreshTokenController` logic as-is: if
      slow-internet conneciton user rapidly fires 2 requests very fast
      the 2nd would fail -> HOWEVER that's where the fix #2 comes with the
      `flag` state on the frontend side in React: which would be set to `true` (boolean) initially & just before the request is made then the `setFlag(false)` would be called & once
      response has arrived ONLY then I'll `setFlag(true)` again & thus
      allowing for further request ONLY AFTER the previous response has
      arrived, in similar fashion to what `useEffect` does with its cleanup function when making GET HTTP requests.
3.  Server URL updated: from `https://cars-clubs-backend.onrender.com` to `https://cars-clubs-server.onrender.com`-> BUT I must **manual deploy** it from my second account (where my Cars Club Database is deployed at!). _EXPLANATIONS_: that "`-server`" subdomain is deployed on my second Render account which is where my Database Cars Club is deployed as well, I had to do it because on the first Render account (with `-backend` subdomain deployed) there's multiple servers and by using Cron Jobs (to keep my Cars Club server running 24/7) I'd run out of free instances on the 31th day (well Render.com offers 750 free hours and Cars Club backend taking up 744, **_BUT_** I have 5 more servers running on my first Render account: ~1.5 hours each (imagine if they run even longer per month/gets more visits -> they'll go down at like 25th day each month)). That's why I instead re-deployed Cars-Clubs-server (named it `server` on the 2nd acc. instead of `backend` as was on my 1st acc.) on my second Render account **_BUT_**: I must click "manual deploy" button whenever I update my MAIN branch because I can't connect multiple Render's account to a single GitHub account (altough I do have an option to log it out and re-connect on the 2nd Render account however I'll lost the deploy for the rest of my 5 instances on the 1st Render account & any updates would require 5x manual deploys clicks) which would have allowed me auto-deploys have I had been connected to my own server. Again that's why I must do "Manual deploy from last commit" in Render.com on my server in my second account.
    - When re-creating a free PostgreSQL Database on Render (because it gets removed after 90 days), a reminder is to set the *optional* name of the database as `cars_club` as for it to match the name that is in [`database.sql`](https://github.com/Aleksandar15/Cars-Club-backend/blob/main/database.sql) file, but leave the "Username" empty. Then connect to the database with CLI command of `psql external_link` and run all of the `CREATE` SQL commands, including the `CREATE VIEW` - I have to scroll down - that is well inside "tests" part which I should perhaps modify that file to move that command above the "tests" comment.
        - `CREATE VIEW posts_view_except_post_image AS
SELECT` is used for the "Edit Post" functionality. Without this `VIEW` the server returns errors when a user tries to modify a post.
        - Sometimes Render.com adds their own letters on the end of my DB name even though I named it "**cars_club**", like today (created on 25 October 2023) they added "_ncwm" on the end of my DB name and now its name is "**cars_club_ncwm**" but it's all working fine. Their default Username of choice was "cars_club_ncwm_user".
    - Bugs that happened is I forgot to add Environment Variables (see `.env#examples` for deployed Apps: and `ACCESS_TOKEN_SECRET` with `REFRESH_TOKEN_SECRET` and `CONNECTION_STRING_POSTGRESQL` (the **external database URL links**)) and **UPDATE**: for Render.com the server crashes if I have `NODE_ENV=production` and deployed with _cache removed_ option I found on on their forum I shouldn't be using it since they add it for me & removing it fixed the "bash tsc not found" even thought `npm i` was success on their part this error occurs when NODE_ENV is set by me.
      - Oh and talking about External Database URL Links bugs fixes with `pg` library returning "SSL/TLS required" error a fix was to add at the end of the url `?ssl=true` read more <a href="https://stackoverflow.com/questions/22301722/ssl-for-postgresql-connection-nodejs">StackOverflow comment</a> & here at Render <a href="https://community.render.com/t/ssl-tls-required/1022">forum</a> self-answered and hyperlinks source to this StackOverflow comment above -> I googled it thinking it's a Render issue but turns out it was `pg` library issue, now fixed.
5.  I thought UI/UX would be much better if I had "Posted by" username in the `Marketplace` component, but my logic was not implemented in the JWT token: and instead I had modified on the backend both `loginController` and `verifyRefreshTokenController` to incldue `user_name` in the response & for the `verifyRefreshTokenController` I must query to my Database instead of including private info like `user_email`/`user_name` in the JWT Token. Then on the Frontend also use Redux Toolkit when `useVerifyRefreshToken` is successful & has received that `user_name`, `user_role`, `user_email`, `user_id` -> For Posts UPDATE & DELETE Buttons to be rendered conditionally based on `post.user_id === user_id`.
6.  IMPORTANT FACT is that the console response of 403 (Forbidden) is an "OKAY" response **if** there's updated data on the screen it means the Axios Interceptor did its job well & the 403 status means the `authorizeJWT` did a good job at protecting the Source & if the data is displayed on the Frontend then `refreshTokenController` did successfully refreshed the accessToken by using User's "session"/'refreshToken' TOKEN & delivering a fresh NEW short-lived "accessToken" (_as well as new 'refreshToken'_). :)
7.  VERY IMPORTANT decision & plans I've made for NOT using Async Thunk with /editpost & /createpost ALIKE, in my setup = accessToken iS to be refreshed & used ONLY once, because it is shortly-lived so I MUST use `useAxiosInterceptor` Hook call which is IMPOSSIBLE inside Redux Files. YES I do can have "editPost" & "createPost" separate Async Thunks in a single REDUX file, but it's #1 NOT that useful
    since there's no reusability usage; and #2 the MOST important **_issue_**
    is the fact that I'll have to make sure I "just" call
    `useAxiosInterceptor`() WITHOUT using its returned Axios (_if I don't need to_) in the
    Component where I'd be calling(/dispatching) that AsyncThunk & all the while using the SAME
    intercepted Axios in the Async Thunk Redux file **BUT** imported from Axios.ts (the **EXACT** 'reference Axios instance object' that's being intercepted in my `useAxiosInterceptor` custom Hook) just to make sure
    my POST/PUT Requests won't fail by using a non-intercepted Axios instance. A lot of workaround indeed. Overall forgetting to call `useAxiosInterceptor` would lead to Async Thunk failing on me and causing bugs to my App.
    - And I also wouldn't be able to call a DISPATCH function in my Redux file to fill my `ModalPost.tsx` input fields with the VALUES matching the `post_id` that the user has clicked on, because `useDispatch` is a Hook that also **can NOT** be called inside Redux files.
8.  I have not added IMAGE Size limits just for easier testing. In a real App I would at least limit the size and even possibly provide a link to my Users of a free service to resize their images or add my own functionality that will resize theirs images either on the Frontend Notice or without their Notice I'd resize them on the backend and store them on the server or database & then frontend will retrieve the resized pictures.
9.  (*Decided not to use `sessionStorage` and keep reading for more info about my *new decision*.*) Next what I am planning to do a UI/UX modifications on the Frontend: is to retain the User's destination in the website by something like storing the `window.scrollY` value in a `sessionStorage` so whenever User refreshes -> they will be scrolled down to their pre-refresh destination: it would be useful for the `Edit Post` button. -> Unless I decide a different workaround: to modify my `editPostController` on the server to make it return the Buffer **_if_** the User has provided a new image - meaning `req.file !== undefined` in the `editPostController` where `req.file` comes from my Multer Middleware -> or actually: I would modify my SQL to `RETURN _`no matter if user updated`image`or didn't -> just so that on the Frontend I will update the`getSortedPosts`by calling the reducer`editSortedPostAction`with those values received as`editedPostData`inside the`editPost`function call inside`ModalPost_Create_or_Edit_Button` component.
    -         The plan is to
      make my `/editpost/:post_id/:user_id` `editPostController`
      to return all of the (`RETURNING *`)
      the `editedPostData` including the `post_image_buffer`
      and then inside `MOdalPost_Create_or_Edit_Button.tsx`
      call the `getSortedPost`'s 'editSortedPostAction' reducer.
    - ->->->->-> **NEW DECISION MADE**: I decided a different workaround: to modify my `editPostController` on the server to make it return the `EditedPost` no matter _if_ the User has provided a new image or not -> meaning in the `editPostController`: I have modified my SQL to `RETURN _`no matter _the cases_ if the User updated`image`or not -> just so that on the Frontend I now am mutating the`Posts`Array state from the`getSortedPosts`Redux file based on the`edittedPostData.post_id`(meaning I'm only mutating the editted Post element of the`Posts`Array state using`.map`method) by calling the reducer`editSortedPostAction`action function with 1 Argument of this Object received (from calling`/editpost/:post_id/:user_id`) as `data?.edittedPostData`(Object) inside the`editPost`function inside`ModalPost_Create_or_Edit_Button` component.
10.  `PaginationMarketplace.tsx` may need a `flag` state as well, to add upon all of the `disabled` logic an extra `... || flag` -> so that they can't trigger another call while one is incoming -> but then I would need to use the `getSortedPosts`'s "status states" like the `postsStatus` because I don't have ANY responses inside of my `PaginationMarketplace` and thus I can't have a local state `flag` but instead I must just use a condition: `'loading'`(or `'idle'` or `'failed'`) value of `postsState` state -> then set `flag = true` otherwise if `postsState` = `'succeeded'` -> then set `flag = false` as that will `active` my buttons. -> AGAIN All of this is needed so that slow internet Users won't re-trigger 2 or 3 or more subsequental requests with the same refresh_token at which point the Server will reject the 2nd or 3rd Request and remova ALL `refresh_token`'s Rows matching that `user_id` from the `refresh_tokens` table and logs the User out as a suspicios activity of where a Hacker might be trying to re-use the `refreshToken` cookie; because the backend Code "doesn't know" if the User is legit or a Hacker & on top of it all: my server-side code-wise rules are 1 Request per 1 Refresh Token -> these rules are set by my `refreshTokenController` code.
11. Future plans about my current LIMITLESS size of the images: I can either block responses if images are larger than let's say 5mb or otherwise I implement a functionality to resize the incoming images before storing them to my Database or if in future I decide to store them on the server's folder of `/images` - still, resize the image first before storing them. - DevTools NETWORK tests: That's an idea that came to my mind because I noticed in a "Slow 3G" running: the `getSortedPostsAsyncThunk` AKA `/getsortedposts` request even though there's 5 small pictures of size in my local folder of around 100-200kB (that's what instagram image sizes are) so that's an average of 0.2mb per 1 image making up for 1mb total size & it's quite slow on "Slow 3G". But, thinking about it it's because it's the Slow 3G being close to unusable, nothing is wrong on my part, since 1mb is such a small Data. Another results on "Slow 3G": whenever I make any other CRUD Operations: Delete a Post / Create a Post / Edit a Post -> the response is received quite fast!:) -> That's because the `createpost` response had a size of 380B (less than half a 1kB) took 4.4s (because it's a protected route with `authorizeJWT` middleware) & `refreshtoken` response had just 1kb took 2s, but my images had 2.2MB (2200kB) and took 45s. -> Note by "my images" I refer to the response by `getsortedposts?limit=5&offset=0&carNameTitle=` (`carNameTitle` can be empty as it's optional value in my getSortedPosts logic). - By those results I decided not to make my `ModalPostSuccessText` be showing `Loading` component if `postsStatus` if `"loading"`, since my `ModalPostSuccessText`'s Loading state is unclickable outside meaning it's unclosable for as long as it takes for response (a quick non-data of ~ 300B - 1kB) to be received: however on "Slow 3G" or even "Fast 3G" I stop "Loading" the `ModalPostSuccessText` and show the response message & an "Okay" button that will clsoe the Modal (or clicks outside or the 'X' button) -> However for slow internet User's my `Post.tsx`'s "Loading" would still be there: but my slow internet Users have the freedom of navigating around the page if they wanna do some other actions or they can await the `Posts` data at the `Post.tsx`'s "Loading" screen coming from a condition of `PostsStatus` Redux State of the `/getsortedposts?limit=5&offset=0&carNameTitle=` request's response. - That gave me a new idea that a `Loading` component can be shown on TOP above the rest of the Posts which will remain at the Bottom. That would be a great UI/UX improvements or rather a decision if I want to keep showing the rest of the posts or not. -> Thinking about it: the current rules of 1 Request per 1 Refresh Token would cause trouble in such a scenario: where if the User tried to remove / "DELETE" some of their (others) Posts just below the `Loading...` or "Edit" one of their post while the `/getsortedposts` is being processed: there's a chance that my Server will request that 2nd request (and log out the User thinking it's a Hacker) -> Re-thinking about it and seeing my tests in DevTools Network tab: the `refreshtoken` being quite a small & fast response of around 1kB & 100ms (in 'No Throttling' mode; and 500ms in "Fast 3G" and 2s in "Slow 3G") it means the until the User decides to either "Delete" or "Edit" one of their Posts it will take up some time and if `refreshtoken` is done: then Server won't reject the 2nd request. -> Except that the Logic of "EDIT A POST" is that it requires yet another GET request so that the `ModalPost` can be filled with the Data matching the `post_id` and allow the User to edit it: meaning slow internet User's will still be logged out by my server thinking it's a Hacker making the 2nd request with the same unchanged `refreshToken` cookie before it has been refreshed after the "CREATE A POST" response has been received. -> BUT, re-thinking again: it's the fact that my `accessToken` lasts 15s: so 1. "CREATE A POST" is made: hopefully `refreshtoken` is triggered: and `accessToken` is used in `authorizeJWT` middleware and allows the `createpost` request, and (if I decided to go with the `Loading` modification as mentioned above, but I'm not doing it yet!) if Slow Internet user clicks the "EDIT A POST" button real quick after having "CREATED A POST" (and my plan of `Loading` above `Posts.tsx`) it means even Slow User will be fine since `accessToken` will last for 14s more: and request won't need to be intercepted & recall to `/refreshtoken` won't happen. That seem good on paper, but in practice maybe some issues will happen and complications are unavoidable until I implement restricttions server-side as well, at which point my Website will look slow and laggy (at least laggish): since if I implement DDoS protection that would still allow at least 15 Requests per 1 Minute to not introduce any slowness or laggy-feeling in my Website. - Anyways, for now I don't show the rest of the `Posts` Array state while `/getsortedposts` request is ongoing instead there's Loading over ALL of the `posts.map(...` data inside my `Post.tsx`; however if I do decide to show the rest of my `Posts` and a Loading on top of the very first `Post` element: this Logic will have to be implemented by creating a new `LoadingPosts` component and render it inside `Marketplace.tsx` or maybe..., I can even render it inside my `Post.tsx` just above `div` element that renders `posts.map(...` data, instead of `Marketplace`. - I'm still thinking to create some guards on the server-side to check if `/refreshtoken` is ongoing or in processs or if such a response like `/refreshtoken` has been sent recently to that IP address or not -> and based on it block the request before it's even reached by my 'is User Hacked' code-guards where I remove all `refresh_token`'s Rows (_based on that `user_id` stored inside the JWT Token_) from `refresh_tokens` table & technically logs out the User from all devices and if they try to access a protected route they'll get a "Please login" `ModalText` message, it's still somewhat bugging me as it's not a perfect UI/UX & can happen for fast internet Users as well if I forget (to implement those Workarounds) to add some `flag` guards to `disable` Buttons or show up `Loading` Component as technically removing the Button from the DOM as to not allow them to make subsequent request real fast (_before 1st request's response has had the time to return or rather to be processed, once the Server process has started, the 2nd Request will succeed as well even if the 1st response hasn't arrived yet_), in like a sub-milliseconds: the 2nd Request would fail & logs out the User from all devices (again, removes all `refresh_token`'s Rows matching that `user_id`) as if they were Hacked -> it makes up for a bad UX which I wanna fix in the future however the code-rules being 1 Reuqest per 1 Refresh Token is too strict and is too good, at which point I must keep using `flag`'s on the Frontend or implement `cache` on the server-side to keep the `refreshToken`'s for 1 minute and after 1 minute remove them from Database to ease up the rules, which reduces the User's protection. A Catch-22! - -> Re-thinking: it might even be a matter of Fast Internet Users where they can send 2 fast requests rather than the slower internet User who can't send the 2nd request faster than the Fast Internet User. Still, overall my tests are Random and I can't predict what the User will do, however the most highest chance for this issue that happens is when the Request is being intercepted & both the 1st-in-the-process-of-interception-request and 2nd request non-intercepted but still being sent with the same `refreshToken` cookie which the server has already removed it from the Database is being processed and that 2nd Request gets rejected by my server logic: `authorizeJWT` checks if `accessToken` is expired -> if true then sends `403` response which gets intercepted on my Frontend's `useAxiosInterceptor` which calls `/refreshtoken` `refreshTokenController` & removes the current `refreshToken` from the Database from the 1st request & then the 2nd request coming in with the same `accessToken` Redux state and of course same `refreshToken` cookie: gets intercepted as well, but when `refreshTokenController` receives the response and that `refreshToken`'s didn't found such `refresh_token` inside the `refresh_tokens` table -> then removes all of the `refresh_token`'s Rows matching that `user_id` & technically logs my User out of the App. I'm thinking of re-testing my `setTimeout` workaround to allow for at least 2nd Request by setting up a removal of the "received `refreshToken`'s from the Database in `delay` of 2seconds or otherwise to use `node-cache` library. -----> And a research of why my `setTimeout` test failed and the ChatGPT answer quote: "Scenario 2: Response Sent Before Timeout Completes:
    If the **response** is sent before the specified **delay** in `setTimeout` elapses (i.e., before 2000 milliseconds), the callback function scheduled by `setTimeout` will not have a chance to execute. The **response** will be sent to the client, and the `timeout` will be effectively **canceled**.".
    - So I'm not sure if even such a workaround exists besides some cron job but then again each Request is unique and doesn't know about preivous Request's data -> and I don't know how ChatGPT's website manages for such a smooth checking: whenever I change my IP Adress using VPN: they immediately refresh my website as if they're pinging each User's in a sub-second with empty responses only to checkif their IP Adress is the same or otherwise they use some `sessionStorage` checking or `setInterval` that checks against my IP Adress every 1 second & refreshes my website if I use VPN, but I don't see how I can keep some kind of backup `refreshToken` inside of `sessionStorage` or something similar. Every other workaround would mean to just weaken my current security setup rules of 1 Request per 1 Refresh Token & I don't like to do that.
    - For now it will be as-is, for real App I can use something like `Redis` to store `refreshToken` for some time (no longer than 2seconds) otherwise google's suggestions is a message queue system such as `RabbitMQ` or `Apache Kafka`, none of which I have ever used yet.
12. UI/UX improved/fixed for the Slow Internet Users (and even affected Fast Internet Users as well) logging out the spam-requests/spam-button-clicks. The **fix**: disabling the Buttons (`disable={flag}`) until the response has arrived seems like a bulletproof way to not accidently log out the User by my too strict code-rules of: 1 Request per 1 Refresh Token -> I've tested my `FormSearchCars` component in React (I spammed 100s of button clicks to call `handleSearchFN`): and _without_ the `flag` where I spam the search button -> keeps logging me out the ~50th Request -> that's on the _no limits / internet speed no throttling mode_ test's result: when it's time for interceptor to work -> meanig the `accessToken` has expired -> the `/refreshtoken` endpoint is being called several times with at least 2 Requests having the same `refreshToken` cookie's value & my backend logs the User out -> but **with `flag`** logic: I tried 100s of clicks: I was **never** logged out so `flag` improved the UX at 100%. Again, the how & why it all works: as in my point above (#10 at the end) I've mentioned that when `accessToken` expires and it's time to intercept the request: then the next request fails -> However with the `flag` at work: it won't `activate` the Button (will keep it `disabled={flag}` until response has arrived: so no "next request" can happen until arrival & thus avoiding any potential errors where the Server logs out the User thinking that they got hacked because of how my code acts against a Request to the `refreshTokenController` where the `refreshToken` cookie's value - a JWT `refresh_token` token - doesn't exist inside the Database: logs them out. But, with the `flag` implementations -> bad UX fixed. / solved. -> By all of this I even solved a potential issue where the User tries to spam a Request until a previous Request's Response has not yet arrived.
    - However I can not neglect the fact that mini-issue remains: if the `accessToken` has expired & if the User really quickly clicks the rest / any of the **UNDISABLED** or still **ACTIVE** buttons; example: if the User clicks the `handleSearchFN` then clicks the `delete` a post button or `edit` a post Real Quick - this will make the 2nd Request fail. -> I do know that by the time the Fast Internet User clicks those buttons: the `refreshToken` cookie would have already been updated with a fresh new `refresh_token` coming from the 1st Request with the Axios interceptor call to `/refreshtoken` Controller -> however the Slow Internet Users or laggy server would remain affected in such a scenario. -> Overall the rule of 1 Request Per 1 Refresh Token is too good and too protective: but anything too good in life is a Catch-22: "too good security" hurts the UX VS "medium security": smoothens up the UX but hurts the overal User's security.
    - The best workaround to avoid my backend security logic logging out my Users in a rapid fast Requests where 2 Requests use the same `refresh_token` _meaning_ the 2nd request was sent with the previous `refreshToken` cookie -> would be to have a file that checks against all states: either local `loading` states or local `flag` states or `Redux Async Thunk` "pending" response being of state `"loading"` as per the `PostsStatus` state -> so grab all those state and make a global `disable` Button states that each and every component will receive and check against this `globalDisable` state (which will be of value `"boolean"`) on top of each Component's local "disable" condition so something like: `disable={flag || globalDisable}`. -> Or since this "global disable" state will take in consideration of all of the local `loading` & Redux /`status` states (_no matter how deeply nested children components & grandchildren components are_), from **every** and **each component**: I then would use this yet-to-be-made "_disable all buttons_" 'while any Request is ongoing & Response hasn't arrived yet'-feature by having it inside of a Redux Slice or the outmost Parent like `Marketplace.tsx` to hold the `globalDisable` state and those Marketplace children Components's Buttons will be passed an attribute `disable={globalDisable}`. This very strict rule and very protective is a big Catch-22!
13. Image-wise I could have used on the backend `const buffer = Buffer.from(valueBYTEA, 'hex')` and then unwrap the `buffer` like so: `const arrayBuffer = buffer.buffer` and pass it in place of `post_image_buffer` so that I wouldn't need to use `Uint8Array` on frontend & only to use `Blob` directly on the received `post_image_buffer`. But as-is currently works well.
14. Explanation of how Multer works in my case: I'm using Multer middleware to process the `FormData` received from a `multipart/form-data` request with the storage option `memoryStorage()`, then using `upload.single` instance middleware to populate the `req.body` with text fields & `req.file` with metadata information about the image which is then handled by my controller to store only the `buffer` data to my PostgreSQL as a `BYTEA` column type.
    - (Again, if I `console.log` the `req.body` inside Multer middleware jsut before the `upload.single` middleware instance: then it's an empty object (`{}`) and if `image` property of what's `upload.single('image')` looks for - is `undefined`: then only the `req.body.title` is processed (`console.log`ged): but luckily my `if` conditional guards are returning a response with status `500` error if `image` field is not found inside that `multipart/form-data` request and `next` function call is never reached - guards serves as a "don't trust the frontend relationship".)
    - => In short Multer middleware's `upload.single` instance middleware populates the `req.body` exactly the same as what `express.json` middleware AKA `bodyParser` middleware under the hood does.
   15. My `refresh_tokens` and `posts` tables both have a MANY-TO-ONE relationship with my `users` table.
   16. My `users` table has a ONE-TO-MANY relationship with my `refresh_tokens` and `posts` tables alike. (both sayings work.)

#### Further plans (_reminders for me_)

1. Have an "edit username" & "edit e-mail" features, but that **will** require me to also run SQL Query against my PostgreSQL database `posts` table to run `UPDATE posts SET post_created_by_username=$1 WHERE user_id=$2` & respectively if I've choosen to show e-mail.
   - For `post_created_by_email` I might even implement a conditional checking in `ModalPost.tsx` (& "edits Posts") to make User decide whether to show "contact number" or "contact e-mail" or both. A toggler.
2. Have a comments section which `comments` table that will relate with each `post` Row of the `posts` table and `FOREIGN KEY` "user_id" column to `REFERENCE` `users` table.
3. Have a replies sections: such `replies` table will have to connect with "comment_id" from `comments` table & the "user_id" from `users` table.
4. On the Frontend part of UI/UX I may implement some alert/modal that will be shown on successful EDITs of the POST.
5. Planning for having a user-profile section (_will write more info in the future_).

##### Further plans (_reminders for me_) in a longer explanation:
