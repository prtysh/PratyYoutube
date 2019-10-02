# PratyYoutube

### Changes to make

After installing Pytube,

move the files : *mixins.py* & *cipher.py* to the installed directory. (In my case: *~/lib/python3.5/site-packages/pytube*)

After that re-import pytube and everythig should work fine.

In case replacing file does not work:

change the following lines:

In *mixins.py*:


```
(Line number 41)

# old code commented

# if any([x in url for x in ['signature=', 'sig=']]):
        # if ('signature=' in url or ('s' not in stream and ('&sig=' in url or '&lsig=' in url))):
        #     # For certain videos, YouTube will just provide them pre-signed, in
        #     # which case there's no real magic to download them and we can skip
        #     # the whole signature descrambling entirely.
        #     logger.debug('signature found, skip decipher')
        #     continue

        # if js is not None:
        #     signature = cipher.get_signature(js, stream['s'])
        # else:
        #     # signature not present in url (line 33), need js to descramble
        #     # TypeError caught in __main__
        #     raise TypeError('JS is None')

        # logger.debug(
        #     'finished descrambling signature for itag=%s\n%s',
        #     stream['itag'], pprint.pformat(
        #         {
        #             's': stream['s'],
        #             'signature': signature,
        #         }, indent=2,
        #     ),
        # )
        # stream_manifest[i]['url'] = url + '&sig=' + signature

# new added code

        if ('signature=' in url or
                ('s' not in stream and
                ('&sig=' in url or '&lsig=' in url))):

            # For certain videos, YouTube will just provide them pre-signed, in
            # which case there's no real magic to download them and we can skip
            # the whole signature descrambling entirely.
            logger.debug('signature found, skip decipher')
            continue

        if js is not None:
            signature = cipher.get_signature(js, stream['s'])
        else:
            # signature not present in url (line 33), need js to descramble
            # TypeError caught in __main__
            raise TypeError('JS is None')

        logger.debug(
            'finished descrambling signature for itag=%s\n%s',
            stream['itag'], pprint.pformat(
                {
                    's': stream['s'],
                    'signature': signature,
                }, indent=2,
            ),
        )
        stream_manifest[i]['url'] = url + '&sig=' + signature
```

in *cipher.py*:


```
line number 37:

# old code commented

# c&&d.set("signature", EE(c));
    # pattern = [
    #     r'yt\.akamaized\.net/\)\s*\|\|\s*'
    #     r'.*?\s*c\s*&&\s*d\.set\([^,]+\s*,\s*(?:encodeURIComponent'
    #     r'\s*\()?(?P<sig>[a-zA-Z0-9$]+)\(',
    #     r'\.sig\|\|(?P<sig>[a-zA-Z0-9$]+)\(',
    #     r'\bc\s*&&\s*d\.set\([^,]+\s*,\s*(?:encodeURIComponent'
    #     r'\s*\()?(?P<sig>[a-zA-Z0-9$]+)\(',
    # ]

# new added

    pattern = [
        r'\b[cs]\s*&&\s*[adf]\.set\([^,]+\s*,\s*encodeURIComponent\s*\(\s*(?P<sig>[a-zA-Z0-9$]+)\(',
        r'\b[a-zA-Z0-9]+\s*&&\s*[a-zA-Z0-9]+\.set\([^,]+\s*,\s*encodeURIComponent\s*\(\s*(?P<sig>[a-zA-Z0-9$]+)\(',
        r'(?P<sig>[a-zA-Z0-9$]+)\s*=\s*function\(\s*a\s*\)\s*{\s*a\s*=\s*a\.split\(\s*""\s*\)',
        r'(["\'])signature\1\s*,\s*(?P<sig>[a-zA-Z0-9$]+)\(',
        r'\.sig\|\|(?P<sig>[a-zA-Z0-9$]+)\(',
        r'yt\.akamaized\.net/\)\s*\|\|\s*.*?\s*[cs]\s*&&\s*[adf]\.set\([^,]+\s*,\s*(?:encodeURIComponent\s*\()?\s*(?P<si$',
        r'\b[cs]\s*&&\s*[adf]\.set\([^,]+\s*,\s*(?P<sig>[a-zA-Z0-9$]+)\(',
        r'\b[a-zA-Z0-9]+\s*&&\s*[a-zA-Z0-9]+\.set\([^,]+\s*,\s*(?P<sig>[a-zA-Z0-9$]+)\(',
        r'\bc\s*&&\s*a\.set\([^,]+\s*,\s*\([^)]*\)\s*\(\s*(?P<sig>[a-zA-Z0-9$]+)\(',
        r'\bc\s*&&\s*[a-zA-Z0-9]+\.set\([^,]+\s*,\s*\([^)]*\)\s*\(\s*(?P<sig>[a-zA-Z0-9$]+)\(',
        r'\bc\s*&&\s*[a-zA-Z0-9]+\.set\([^,]+\s*,\s*\([^)]*\)\s*\(\s*(?P<sig>[a-zA-Z0-9$]+)\('
    ]

```

You can find more info [here](https://github.com/nficano/pytube/issues/399#issuecomment-506193198):