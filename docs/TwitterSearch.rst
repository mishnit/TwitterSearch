The TwitterSearch class
=======================

This class contains the actual functionality of this library. It is responsible for correctly transmitting your data to the Twitter API and returning the results to your program afterwards.

If you're looking for the exceptions thrown by this class based on the HTTP status, please read the article about `TwitterSearchException <TwitterSearchException.html>`_.

Available methods
-----------------

=============================================== ====================================================================================================================================================================================================== ================================================================================================================
Method                                          Description                                                                                                                                                                                            Example
----------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------
``setProxy(<dict>)``                            Sets a proxy. Because only HTTPS requests are done, it's sufficient to only input a dict containing a ``https`` entry                                                                                  ``setProxy({'https':'10.0.0.1:123'})``
----------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------
``authenticate(verify=<boolean>)``              Creates an authenticated oauth2 client and if ``verify`` is true, it also checkes if the user credentials are valid. The **default** value is *True*                                                   ``authenticate()``, ``authenticate(True)``
----------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------
``searchTweetsIterable(<TwitterSearchOrder>)``  Queries the Twitter API, iterates through tweets and reloads available older tweets automatically                                                                                                      see `Basic usage <basic_usage.html>`_
----------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------
``searchTweets(<TwitterSearchOrder>)``          Queries the Twitter API **without** iterating or reloading of further results and returns response                                                                                                     see `Advanced usage`_
----------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------
``sentSearch(<string>)``                        Queries the Twitter Search API with a given query string, determines if there are more results available in API and returns response.                                                                                                                          ``sentSearch('?q=One+Two&count=100')``
----------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------
``searchNextResults()``                         Queries the API for more tweets and returns response                                                                                                                                                   see `Advanced usage`_
----------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------
``getMetadata()``                               Returns a dict of meta information about the last query                                                                                                                                                see `Advanced usage`_
----------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------
``getTweets()``                                 Returns a dict of all tweets returned by last query                                                                                                                                                    see `Advanced usage`_
----------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------
``getStatistics()``                             Returns a dict of the type ``{ 'queries' : <int>, 'tweets' : <int> }`` with statistical values about the number of queries and the sum of all tweets recieved by this very instance of *TwitterSearch* ``print "Queries done %i / Tweets received %i" % (ts.getStatistics()['queries'], ts.getStatistics()['tweets'])``
----------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------
``checkHTTPStatus(<int>)``                      Checks if given HTTP status code is in ``TwitterSearch.exceptions`` and raises ``TwitterSearchException`` if this is the case                                                                          ``checkHTTPStatus(200)``, ``checkHTTPStatus(401)``
----------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------
``setSupportedLanguages(<TwitterSearchOrder>)`` Loads currently supported languages from Twitter and stores them in a ``TwitterSearchOrder`` instance                                                                                                  see `Advanced usage`_
=============================================== ====================================================================================================================================================================================================== ================================================================================================================

The methods ``next()``, ``__next__()`` and ``__iter__()`` are used during the iteration process. For more information about those methods please consult the `official Python documentation <http://docs.python.org/2/library/stdtypes.html#iterator-types>`_.

Constructor
+++++++++++

The constructor needed to set your credentials for the Twitter API. The parameters are ``__init__( consumer_key, consumer_secret, access_token, access_token_secret, verify=True)``.

If you're new to Python take a look at the following example:

.. code-block:: python

    ts1 = TwitterSearch(
        consumer_key = 'aaabbb',
        consumer_secret = 'cccddd',
        access_token = '111222',
        access_token_secret = '333444'
    )

    # equals

    ts2 = TwitterSearch('aaabbb', 'cccddd', '111222', '333444', verify=True)

Authentication and verification
+++++++++++++++++++++++++++++++

Please be aware that there is **no further check** whether or not your credentials are valid if you set ``verify=False`` in the constructor. If you're skipping the verification process of *TwitterSearch* you can avoid some traffic and one query (which is also rate-limited by Twitter).

But be aware that you're only saving **one** request at all by avoiding the automatic verification process. Due to the fact that json doesn't consume much traffic at all, this may only be a way for very conservative developers or some exotic scenarios.

Proxy usage
+++++++++++

To use a HTTPS proxy at initialization of the *TwitterSearch* class, an addition argument named ``proxy={ 'https' : 'some.proxy:888' }`` can be used. Otherwise the authentication will fail if the client has no direct access to the    Twitter API.

Returned tweets
+++++++++++++++

*TwitterSearch* is trying to not hide anything from your eyes except the complexity of it's functions. Due to this you're able to get all the information available (which can be quite a lot).

Example output with only one tweet included:

.. code-block:: python

    {'search_metadata': {'completed_in': 0.08,
                     'count': 1,
                     'max_id': 352072665667878913,
                     'max_id_str': '352072665667878913',
                     'next_results': '?max_id=352072665667878912&q=Germany%20castle&count=1&include_entities=1',
                     'query': 'Germany+castle',
                     'refresh_url': '?since_id=352072665667878913&q=Germany%20castle&include_entities=1',
                     'since_id': 0,
                     'since_id_str': '0'},
                     'statuses': [
                     {'contributors': None,
               'coordinates': None,
               'created_at': 'Tue Jul 02 14:33:59 +0000 2013',
               'entities': {'hashtags': [],
                            'media': [{'display_url': 'pic.twitter.com/Oz77FLEong',
                                       'expanded_url': 'http://twitter.com/ThatsEarth/status/351839174887870464/photo/1',
                                       'id': 351839174896259072,
                                       'id_str': '351839174896259072',
                                       'indices': [117, 139],
                                       'media_url': 'http://pbs.twimg.com/media/BOH73Y3CEAAldKU.jpg',
                                       'media_url_https': 'https://pbs.twimg.com/media/BOH73Y3CEAAldKU.jpg',
                                       'sizes': {'large': {'h': 639,
                                                           'resize': 'fit',
                                                           'w': 960},
                                                 'medium': {'h': 399,
                                                            'resize': 'fit',
                                                            'w': 600},
                                                 'small': {'h': 226,
                                                           'resize': 'fit',
                                                           'w': 340},
                                                 'thumb': {'h': 150,
                                                           'resize': 'crop',
                                                           'w': 150}},
                                       'source_status_id': 351839174887870464,
                                       'source_status_id_str': '351839174887870464',
                                       'type': 'photo',
                                       'url': 'http://t.co/Oz77FLEong'}],
                            'symbols': [],
                            'urls': [],
                            'user_mentions': [{'id': 118504288,
                                               'id_str': '118504288',
                                               'indices': [0, 11],
                                               'name': 'Josh Dallas',
                                               'screen_name': 'joshdallas'},
                                              {'id': 298250825,
                                               'id_str': '298250825',
                                               'indices': [12, 25],
                                               'name': 'Ginnifer Goodwin',
                                               'screen_name': 'ginnygoodwin'},
                                              {'id': 1201661238,
                                               'id_str': '1201661238',
                                               'indices': [49, 60],
                                               'name': 'Earth Pics',
                                               'screen_name': 'ThatsEarth'}]},
               'favorite_count': 0,
               'favorited': False,
               'geo': None,
               'id': 352072665667878913,
               'id_str': '352072665667878913',
               'in_reply_to_screen_name': 'joshdallas',
               'in_reply_to_status_id': None,
               'in_reply_to_status_id_str': None,
               'in_reply_to_user_id': 118504288,
               'in_reply_to_user_id_str': '118504288',
               'lang': 'en',
               'metadata': {'iso_language_code': 'en',
                            'result_type': 'recent'},
               'place': None,
               'possibly_sensitive': False,
               'retweet_count': 0,
               'retweeted': False,
               'source': '<a href="http://twitter.com/download/android" rel="nofollow">Twitter for Android</a>',
               'text': '@joshdallas @ginnygoodwin home during wintertime"@ThatsEarth: Hohenzollern Castle floating above the Clouds,Germany. http://t.co/Oz77FLEong"',
               'truncated': False,
               'user': {'contributors_enabled': False,
                        'created_at': 'Fri Aug 14 09:15:27 +0000 2009',
                        'default_profile': False,
                        'default_profile_image': False,
                        'description': 'Scorpio. 23. MBA Graduate.',
                        'entities': {'description': {'urls': []},
                                     'url': {'urls': [{'display_url': 'fanfiction.net/u/4764512/',
                                                       'expanded_url': 'http://www.fanfiction.net/u/4764512/',
                                                       'indices': [0,
                                                                   22],
                                                       'url': 'http://t.co/sEKQ1M85H2'}]}},
                        'favourites_count': 114,
                        'follow_request_sent': False,
                        'followers_count': 300,
                        'following': False,
                        'friends_count': 229,
                        'geo_enabled': False,
                        'id': 65599486,
                        'id_str': '65599486',
                        'is_translator': False,
                        'lang': 'en',
                        'listed_count': 0,
                        'location': 'Kuwait',
                        'name': 'Amal Behbehani',
                        'notifications': False,
                        'profile_background_color': 'DBE9ED',
                        'profile_background_image_url': 'http://a0.twimg.com/profile_background_images/317569734/tumblr_lqc4ttwuJm1qclkveo1_500.jpg',
                        'profile_background_image_url_https': 'https://si0.twimg.com/profile_background_images/317569734/tumblr_lqc4ttwuJm1qclkveo1_500.jpg',
                        'profile_background_tile': True,
                        'profile_banner_url': 'https://pbs.twimg.com/profile_banners/65599486/1372576102',
                        'profile_image_url': 'http://a0.twimg.com/profile_images/3763288269/57c274f19592f6d190957d8eb86c64f1_normal.png',
                        'profile_image_url_https': 'https://si0.twimg.com/profile_images/3763288269/57c274f19592f6d190957d8eb86c64f1_normal.png',
                        'profile_link_color': 'CC3366',
                        'profile_sidebar_border_color': 'DBE9ED',
                        'profile_sidebar_fill_color': 'E6F6F9',
                        'profile_text_color': '333333',
                        'profile_use_background_image': True,
                        'protected': False,
                        'screen_name': 'TigeyGirl',
                        'statuses_count': 18891,
                        'time_zone': 'Santiago',
                        'url': 'http://t.co/sEKQ1M85H2',
                        'utc_offset': -14400,
                        'verified': False}}]}

Have a look at the `entities documented by Twitter <https://dev.twitter.com/docs/platform-objects/entities>`_ to figure out what a specific key-value tuple does exactly mean.

Advanced usage
--------------

Sometime the default use-case is not sufficient and you may like to use *TwitterSearch* in unusual scenarios.

Access meta data
++++++++++++++++

An output of the available meta data from the query to the Twitter API is stored in a ``dict``. You can access it by calling ``getMetadata()`` which will return all meta information about the last query.

Example:

.. code-block:: python

    { 
    'content-length': '467129', 
    'x-rate-limit-reset': '1372773784', 
    'x-rate-limit-remaining': '170', 
    'x-xss-protection': '1; mode=block', 
    'cache-control': 'no-cache, no-store, must-revalidate, pre-check=0, post-check=0', 
    'status': '200', 
    'transfer-encoding': 'chunked', 
    'set-cookie': 'lang=de, guest_id=v1%!xxx; Domain=.twitter.com; Path=/; Expires=Thu, 01-Jul-2013 14:02:32 UTC',
    'expires': 'Tue, 31 Mar 1981 05:00:00 GMT',
    'x-access-level': 'read',
    'last-modified': 'Tue, 01 Jul 2013 14:02:32 GMT', 
    '-content-encoding': 'gzip', 
    'pragma': 'no-cache', 
    'date': 'Tue, 01 Jul 2013 14:02:32 GMT',
    'x-rate-limit-limit': '180',
    'content-location': u'https://api.twitter.com/1.1/search/tweets.json?count=100&oauth_body_hash=xxx&oauth_nonce=xxx&oauth_timestamp=xxx&oauth_consumer_key=xxx&oauth_signature_method=HMAC-SHA1&q=Germany+castle&oauth_version=1.0&oauth_token=xxx&oauth_signature=xxx', 
    'x-transaction': 'xxx', 
    'strict-transport-security': 'max-age=631138519',
    'server': 'tfe',
    'x-frame-options': 'SAMEORIGIN',
    'content-type': 'application/json;charset=utf-8'
    }

Be **careful** about those data as it contains sensible data as you can see in ``getMetadata()['content-location']``. Do **NOT** save or output those information to insecure environments!


TwitterSearch without iteration
+++++++++++++++++++++++++++++++

It is also perfectly possible to use *TwitterSearch* without the iteration and to query the Twitter API all by yourself. For example you may like to implement the `suggest max_id procedure of Twitter <https://dev.twitter.com/docs/working-with-timelines>`_ to access the API directly and don't trust the library to do this automatically on it's own.

A possible solution could look like this:

.. code-block:: python

    from TwitterSearch import *

    try:
        tso = TwitterSearchOrder()
        tso.setKeywords(['Germany', 'castle'])

        ts = TwitterSearch('aaabbb', 'cccddd', '111222', '333444')

        # init variables needed in loop
        todo = True
        next_max_id = 0

        # let's start the action
        while(todo):

            # first query the Twitter API
            response = ts.searchTweets(tso)

            # print rate limiting status
            print "Current rate-limiting status: %i" % ts.getMetadata()['x-rate-limit-reset']

            # check if there are statuses returned and whether we still have work to do
            todo = not len(response['content']['statuses']) == 0

            # check all tweets according to their ID
            for tweet in response['content']['statuses']:
                tweet_id = tweet['id']
                print("Seen tweet with ID %i" % tweet_id)

                # current ID is lower than current next_max_id?
                if (tweet_id < next_max_id) or (next_max_id == 0):
                    next_max_id = tweet_id
                    next_max_id -= 1 # decrement to avoid seeing this tweet again

            # set lowest ID as MaxID
            tso.setMaxID(next_max_id)

    except TwitterSearchException as e:
        print(e)

On-the-fly loading of supported languages
+++++++++++++++++++++++++++++++++++++++++

As you may have figured out some languages are not supported by Twitter and those that are may change over time. This is why Twitter does provide `an endpoint <https://dev.twitter.com/docs/api/1.1/get/help/languages>`_ to load all currently supported languages. You may query it to gather current information about the languages in Twitter.


.. code-block:: python

        from TwitterSearch import *

        try:
            tso = TwitterSearchOrder()
            ts = TwitterSearch('aaabbb', 'cccddd', '111222', '333444')

            # load  currently supported languages by Twitter and store them in a TwitterSearchOrder object
            ts.setSupportedLanguages(tso)

            # try to set German (see ISO 639-1) as language 
            ts.setLanguage('de')
            print('German seems to be officially supported by Twitter. Yay!')

        except TwitterSearchException as e:
        
            # if we get an 1002 code it means that 'de' is not supported (see TwitterSearchException)
            if e.code == 1002:
                print('Oh no - German is not supported :(')
            print(e)

