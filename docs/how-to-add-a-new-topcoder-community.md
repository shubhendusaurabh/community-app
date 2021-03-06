# How to Add a New Topcoder Community?
*NOTE: Related code is not quite stable yet, be aware that this document might be out of sync with the actual behavior. Current version of this instruction corresponds to the code commit `ec6730c9fc02ee8a4bece061cae5a3eb9d2ce779`*

To add a new community with the name **demo**, we should follow the following protocol:

1.  All community-specific assets should be added to the `/src/assets/themes/demo` folder.
2.  Community meta-data file should be created under the path `/src/server/tc-communities/demo/metadata.json`:
    ```json
    {
      "authorizedGroupIds": [
        "12345"
      ],
      "challengeFilter": {
        "groupIds": ["12345"]
      },
      "challengeListing": {},
      "communityId": "demo",
      "communitySelector": [{
        "label": "Demo Community",
        "value": "1"
      }, {
        "label": "Cognitive Community",
        "redirect": "http://cognitive.topcoder.com/",
        "value": "2"
      }, {
        "label": "iOS Community",
        "redirect": "https://ios.topcoder.com/",
        "value": "3"
      }],
      "groupId": "12345",
      "leaderboardApiUrl": "https://api.topcoder.com/v4/looks/0/run/json/",
      "logos": [
        "/themes/demo/logo_topcoder_with_name.svg"
      ],
      "additionalLogos": [
        "/themes/demo/logo_topcoder_with_name.svg"
      ],
      "hideSearch": true,
      "chevronOverAvatar": true,
      "menuItems": [
        {
          "title": "Home",
          "url": "."
        }, {
          "title": "Learn",
          "url": "learn"
        }, {
          "title": "Challenges",
          "url": "challenges"
        }, {
          "title": "Leaderboard",
          "url": "leaderboard"
        }
      ],
      "newsFeed": "http://www.topcoder.com/feed",
      "description": "A berief description which will be displayed in dashboard",
      "image": "1.jpg"
    }
    ```
    Its fields serve the following purposes:
    -   `authorizedGroupIds` - *String Array* - Optional. Array of group IDs. If specified, access to the community will be restricted only to authenticated visitors, included into, at least, one of the groups listed in this array. If undefined, community will be accessible to any visitors (including non-authenticated ones).
    -   `challengeFilter` - *Object* - Challenge filter matching challenges related to the community. This object can include any options known to the `/src/utils/challenge-listing/filter.js` module, though in many cases you want to use just one of these:
        ```js
        /* Matches challenges belonging to any of the groups listed by ID. */
        {
          "groupIds": ["12345"]
        }

        /* Matches challenges tagged with at least one of the tags. */
        {
          "tags": ["JavaScript"]
        }

        /* Matches challenges belonging to any of the groups AND tagged with
         * at least one of the tags. */
        {
          "groupIds": ["12345"],
          "tags": ["JavaScript"]
        }
        ```
    -   `challengeListing` - *Object* - Optional. When provided, it holds configuration for the challenge listing shown inside the community. This config object may have the following fields:
        - `openChallengesInNewTabs` - *Boolean* - Optional. When set, challenge listing opens challenge details pages in new tabs. Defaults to `true`.
        - `prizeMode` - *String* - Optional. Modifies the way the prizes are shown in challenge cards. Valid values are:
            - `hidden` - Prize components are just hidden;
            - `money-eur` - Prizes are converted to EUR;
            - `money-inr` - Prizes are converted to INR;
            - `money-usd` - Prizes are shown in USD (no actual conversion needed);
            - `points` - Points are shown rather than the prizes. The points are taken from `drPoints` field of challenge objects. There is no prizes tooltip in this case.
    -   `communityId` - *String* - Unique ID of this community.
    -   `communitySelector` - *Object Array* - Specifies data for the community selection dropdown inside the community header. Each object MUST HAVE `label` and `value` string fields, and MAY HAVE `redirect` field. If `redirect` field is specified, a click on that option in the dropdown will redirect user to the specified URL.
    -   `groupId` - *String* - This value of group ID is now used to fetch community statistics. Probably, it makes sense to use this value everywhere where `authorizedGroupIds` array is used, however, at the moment, these two are independent.
    -   `leaderboardApiUrl` - *String* - Endpoint from where the leaderboard data should be loaded.
    -   `logos` - *String Array | Object Array* - Array of image URLs to insert as logos into the left corner of community's header, alternatively the array may contain JS objects of shape
        ```
        {
          "img": "<SOME-IMAGE-URL>",
          "url": "https://www.topcoder.com"
        }
        ```
        For such elements `img` will be used as the image source, and `url` will be the redirection URL triggered by a click on the logo.
    -   `additionalLogos` - *String Array* - Array of image URLs to insert as logos into the right corner of community's header.
    -   `hideSearch` - *Boolean* - Hide/Show the search icon.
    -   `chevronOverAvatar` - *Boolean* - Render a *chevron-down* instead of the user avatar.
    -   `menuItems` - *Object Array* - Specifies options for the community navigation menu (both in the header and footer). Each object MUST HAVE `title` and `url` fields. For now, `url` field should be a relative link inside the community, within the same path segment.
    -   `newsFeed` - *String* - Optional. URL of an XML blog feed to be used to render news section at a custom page. To actually render the news section, you should include it into the page code like (also see as example `/src/shared/components/tc-communities/communities/wipro/Home/index.jsx`):
        ``` js
        /* This goes inside the import section in the beginning of the file. */
        import NewsSection from 'components/tc-communities/NewsSection';

        /* This goes into appropriate place of the render function. */
        <NewsSection news={props.news} />
        ```
        The `<NewsSection />` component does not render anything, if its `news` property is *null* or an empty array, thus it can be kept inside the page code even when there is no news feed configured for a community.
    - `description`: A berief description which will be displayed in dashboard.
    - `image`: A image that located at `/assets/images/tc-communities/background` will be displayed in dashboard
3.  Custom pages of the community (anything beside `Challenges` and `Leaderboard`) should be created inside `/src/shared/components/tc-communities/communities/demo`. At the moment all communities have two custom pages: `Home` and `Learn`, you may just copy these from an existing community, and then customize to your particular needs.

4.  The routing inside community, and code splitting of the related code, should be set up inside `/src/shared/routes/Communities`:
    - Copy/paste one of the existing community folders and rename it into `/src/shared/routes/Communities/Demo`;
    - Inside `/src/shared/routes/Communities/Demo/index.jsx` you should change the name of code chunk in two places it is present (as value of `chunkName` prop, and inside `webpackChunkName` "magic comment");
    - Inside `/src/shared/routes/Communities/Demo/Routes.jsx` you define necesary routing, as with usual `react-router` routing code;
    - Finally, you link this routing code into `/src/shared/routes/Communities/Routes.jsx`.

5.  At this point **demo** community is ready and accessible at the `/community/demo` route of the App (i.e., if we deploy dev version of the App to `community-west.topcoder-dev.com`, community will be accessible as `community-west.topcoder-dev.com/community/demo`).

    To make **demo** community accessible via a dedicated sub-domain, e.g. like `demo.topcoder-dev.com`, you should edit `/src/shared/routes/index.jsx`. In the first `if-else` block inside `Routes()` function add the line
    ```js
    else if (subdomains.indexOf('demo') >= 0) communityId = 'demo';
    ```
    This takes care about proper sub-domain routing from our App's side. Beside it you should:
    -   Ensure that the web-server where the App is deployed allows access to the subdomain `demo.topcoder-dev`, and redirects incoming requests to the App.
    -   Ensure that Topcoder `accounts-app` allows to authenticate from the new subdomain address.
