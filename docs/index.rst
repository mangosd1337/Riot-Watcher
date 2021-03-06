
Welcome to RiotWatcher's documentation!
=======================================

RiotWatcher is a thin wrapper on top of the `Riot Games API for League
of Legends <https://developer.riotgames.com/>`__. All public methods as
of 10/9/2017 are supported in full.

RiotWatcher by default supports a naive rate limiter. This rate limiter will
try to stop you from making too many requests, and in a single threaded test environment
does this rather well. In a multithreaded environment, you may still get some
429 errors. 429 errors are currently NOT retried for you.

To Start...
-----------

To install RiotWatcher:

::

    pip install riotwatcher

OR:

::

    python setup.py install

You also need to have an API key from Riot. Get that from
`here <https://developer.riotgames.com/>`__.

Using it...
-----------

All methods return dictionaries representing the json objects described
by the official Riot API. Any HTTP errors that are returned by the API are
raised as HTTPError exceptions from the Requests library.

.. code:: python

    from riotwatcher import RiotWatcher

    watcher = RiotWatcher('<your-api-key>')

    my_region = 'na1'

    me = watcher.summoner.by_name(my_region, 'pseudonym117')
    print(me)

    # all objects are returned (by default) as a dict
    # get my 1 mastery page i keep changing
    my_mastery_pages = watcher.masteries.by_summoner(my_region, me['id'])
    print(my_mastery_pages)

    # lets see if i got diamond yet (i probably didnt)
    my_ranked_stats = watcher.league.leagues_by_summoner(my_region, me['id'])
    print(my_ranked_stats)

    # Lets some champions
    static_champ_list = watcher.static_data.champions(my_region)
    print(static_champ_list)

    # Error checking requires importing HTTPError from requests

    from requests import HTTPError

    # For Riot's API, the 404 status code indicates that the requested data wasn't found and
    # should be expected to occur in normal operation, as in the case of a an
    # invalid summoner name, match ID, etc.
    #
    # The 429 status code indicates that the user has sent too many requests
    # in a given amount of time ("rate limiting").

    try:
        response = watcher.summoner.by_name(my_region, 'this_is_probably_not_anyones_summoner_name')
    except HTTPError as err:
        if err.response.status_code == 429:
            print('We should retry in {} seconds.'.format(e.headers['Retry-After']))
            print('this retry-after is handled by default by the RiotWatcher library')
            print('future requests wait until the retry-after time passes')
        elif err.response.status_code == 404:
            print('Summoner with that ridiculous name not found.')
        else:
            raise


Main API and other topics
-------------------------

.. toctree::
   :maxdepth: 1

   riotwatcher/riotwatcher.rst
   riotwatcher/HandlerChains.rst
   riotwatcher/testing.rst


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
