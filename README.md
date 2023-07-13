### description

![Node.js CI](https://github.com/yogthos/mastodon-bot/workflows/Node.js%20CI/badge.svg)

the bot will post the timeline from the specified Twitter/Tumblr accounts and RSS feeds to Mastodon

[![NPM](https://nodei.co/npm/mastodon-bot.png?compact=true)](https://www.npmjs.com/package/mastodon-bot)

### installation

1. prerequisits: should be installed: [Node.js](https://nodejs.org/en/), npm
2. install mastodon-bot with: `sudo npm install mastodon-bot -g`
3. run with: `mastodon-bot <path to config>`


### usage

* create a Mastodon API key following the instructions [here](https://tinysubversions.com/notes/mastodon-bot/)
* create a Twitter API key following the instructions [here](https://developer.twitter.com/en/docs/basics/authentication/guides/access-tokens)
* create a Tumblr API key following the instructions [here](http://www.developerdrive.com/2014/05/how-to-get-started-with-the-tumblr-api-part-1/)
* create a file called `config.edn` with the following contents:

**NOTE**: the bot checks the specified Mastodon account to see the timestamp of the last post, and only posts content 
with later timestamps to avoid duplicate posts. On the first run the timestamp will default to current time.

```clojure
{:auth {;; add Twitter config to mirror Twitter accounts
        :twitter {:consumer_key "XXXX"
                  :consumer_secret "XXXX"
                  :access_token_key "XXXX"
                  :access_token_secret "XXXX"}
        :mastodon {:access_token "XXXX"
                   ;; account number you see when you log in and go to your profile
                   ;; e.g: https://mastodon.social/web/accounts/294795
                   :account-id "XXXX"
                   :api_url "https://botsin.space/api/v1/"}
        :tumblr {:consumer_key "XXXX"
                 :consumer_secret "XXXX"
                 :token "XXXX"
                 :token_secret "XXXX"}}
 :transform 
 [{:source {:source-type :twitter
                       ;; optional, defaults to false
            :include-replies? false
                       ;; optional, defaults to false
            :include-rts? false
                       ;; Replace Twitter links by Nitter
            :nitter-urls? false
                       ;; accounts you wish to mirror
            :accounts ["arstechnica" "WIRED"]}
   :target {:target-type :mastodon
            ;; optional flag specifying wether the name of the account
            ;; will be appended in the post, defaults to false
            :append-screen-name? false
            ;; optional visibility flag: direct, private, unlisted, public
            ;; defaults to public
            :visibility "unlisted"
            ;; optional boolean to mark content as sensitive
            :sensitive? true
            ;; optional boolean defaults to false
            ;; only sources containing media will be posted when set to true
            :media-only? true
            ;; optional limit for the post length
            :max-post-length 300
            ;; optional signature for posts
            :signature "#newsbot"}
   ;; optionally try to resolve URLs in posts to skip URL shorteners
   ;; defaults to false
   :resolve-urls? true
   ;; optional content filter regexes
   ;; any posts matching the regexes will be filtered out
   :content-filters [".*bannedsite.*"]
   ;; optional keyword filter regexes
   ;; any posts not matching the regexes will be filtered out
   :keyword-filters [".*clojure.*"]
   ;; optional option, shows debug output for specific transformations
   ;; value may be one of :all-items :first-item :name-only
   :debug-transform-process :name-only
   ;; optional replacements
   ;; When the strings on the left side of this map are encountered in the source,
   ;; they are replaced with the string on the right side of the map:
   :replacements {
     "@openSUSE " "@opensuse@fosstodon.org ",
     "@archlinux " "",
     "@lolamby " "",
     "@conservancy " "@conservancy@mastodon.technology ",
     "@prototypefund " "@PrototypeFund@mastodon.social ",
     "@coreboot_org " "",
     "@OpenTechFund " "",
     "@OpenWrtSummit " "",
     "@OpenMirage " "",
     "@debian " "@debian@fosstodon.org ",
     "@nixos_org " "",
     "@lwnnet " "",
     "@guixhpc" ""}}

  {:source {:source-type :rss
            ;; add RSS config to follow feeds
            :feeds [["Hacker News" "https://hnrss.org/newest"]
                    ["r/Clojure" "https://www.reddit.com/r/clojure/.rss"]]}
   :target {:target-type :mastodon
            :append-screen-name? false
            :signature "#rssbot"}
   :resolve-urls? true
   :content-filters [".*bannedsite.*"]
   :keyword-filters [".*clojure.*"]
   :replacements nil}
  
  {:source {:source-type :tumblr
            ;; optional limit for number of posts to retrieve, default: 5
            :limit 10
            :accounts ["cyberpunky.tumblr.com" "scipunk.tumblr.com"]}
   :target {:target-type :mastodon
            :signature "#tumblrbot"}}
  
  ]}
```

The bot looks for `config.edn` at its relative path by default, an alternative location can be specified either using the `MASTODON_BOT_CONFIG` environment variable or passing the path to config as an argument

Transformations have source `(s/def ::source-type #{:twitter :rss :tumblr})` und target `(s/def ::target-type #{:mastodon})` you can combine freely. Multiple transformations for same source-target combination are possible. Source and targets refer to the auth section for their credentials.

## Alternatives

There are other good alternatives:
* BirdsiteLive - Follow your twitter-friends on mastodon
  * Public instance: https://birdsite.slashdev.space/
  * Source: https://github.com/NicolasConstant/BirdsiteLive


## License

Copyright © 2018 Dmitri Sotnikov

Distributed under the [MIT License](http://opensource.org/licenses/MIT).
