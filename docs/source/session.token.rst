.. _`Token API`:

=========
Token API
=========

As mention above there are presently only 3 token types that are defined:

- authorization_code,
- access_token and
- refresh_token

A token is described as follows::

    {
        "type": "authorization_code",
        "issued_at": 1605452123,
        "not_before": 0,
        "expires_at": 1605452423,
        "revoked": false,
        "value": "Z0FBQUFBQmZzVUZieDFWZy1fbjE2ckxvZkFTVC1ZTHJIVlk0Z09tOVk1M0RsOVNDbkdfLTIxTUhILWs4T29kM1lmV015UEN1UGxrWkxLTkVXOEg1WVJLNjh3MGlhMVdSRWhYcUY4cGdBQkJEbzJUWUQ3UGxTUWlJVDNFUHFlb29PWUFKcjNXeHdRM1hDYzRIZnFrYjhVZnIyTFhvZ2Y0NUhjR1VBdzE0STVEWmJ3WkttTk1OYXQtTHNtdHJwYk1nWnl3MUJqSkdWZGFtdVNfY21VNXQxY3VzalpIczBWbGFueVk0TVZ2N2d2d0hVWTF4WG56TDJ6bz0=",
        "usage_rules": {
            "expires_in": 300,
            "supports_minting": [
                "access_token",
                "refresh_token",
                "id_token"
            ],
            "max_usage": 1
            },
        "used": 0,
        "based_on": null,
        "id": "96d19bea275211eba43bacde48001122"
    }


The mwthods are

max_usage_reached
-----------------
.. _max_usage_reached:

If there is an upper limit as to how many times a token can be used
this method will tell you if that limit has been reached.

is_active
---------
.. _is_active:

There might be several reasons why a token can not be used.
It may have expires, been revoked or have reached its max usage limit. This
method will tell you if any of those limits has been reached.

revoke
------
.. _revoke:

Will revoke a token.

.. code-block:: Python

    from oidcendpoint.grant import AccessToken
    token = AccessToken()
    token.revoke()
    assert token.is_active() is False


register_usage
--------------
.. _register_usage:

If you need to keep track on how many times a token has been use, so not
to use it more times then allowed. Use this method to mark every time
the token has been used.

has_been_used
-------------
.. _has_been_used:

Returns the number of times the token has been used. This is dependent
on the method register_usage being used.

.. code-block:: Python

    from oidcendpoint.grant import AccessToken
    token = AccessToken(usage_rules={"max_usage": 2})

    token.register_usage()
    assert token.has_been_used()
    assert token.used == 1
    assert token.max_usage_reached() is False

    token.register_usage()
    assert token.max_usage_reached()
    assert token.used == 2

to_json
-------
.. _to_json:

Convert the information in a Token instance into a JSON representation.

from_json
---------
.. _from_json:

Set the information in a Token instance based on the content of a JSON object.

.. code-block:: python

    from oidcendpoint.grant import AuthorizationCode
    code = AuthorizationCode(value="ABCD",
                             scope=["openid", "foo", "bar"],
                             claims={"userinfo": {"given_name": None}},
                             resources=["https://api.example.com"])

    _json_str = code.to_json()

    _new_code = AuthorizationCode().from_json(_json_str)

    for attr in AuthorizationCode.attributes:
        assert getattr(code, attr) == getattr(_new_code, attr)


supports_minting
----------------
.. _supports_minting:

Returns True if the token can used to mint a new token of a special kind.

.. code-block:: python

    from oidcendpoint.grant import AuthorizationCode
    code = AuthorizationCode(value="ABCD")
    assert code.supports_minting('access_token')
    assert code.supports_minting('refresh_token')
    assert code.supports_minting("authorization_code") is False