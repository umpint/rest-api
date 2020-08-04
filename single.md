# Sign a single file

Sends the hash and singature of a single file to the umpint.com service. Once a file has been signed if a user drags and drops that file onto the checking area of the umpint.com website they will see it as valid.

**URL** : 

`/api/v1/sign/:url?hash=:hash&sig=:sig`

**URL Parameters** : 

`url=[string]` where `url` is the url of the site that is signing the file. You must have access to the private key of the ssl certificate of the site to be able to compute a valid signature.

**Query Parameters** :

`:hash=[string]` where `:hash` is the hex encoded string of the sha256 hash of the file being signed.

`:sig=[string]` where `:sigh` is the url encoded ( `+` => `-`, `=` => `_`, `/` => `~`) of the base64 encoding of the signature of the hash string passed as the other parameter.

See: https://github.com/umpint/bash-api/blob/master/sign.sh for an example of computing both the hash and the signature required.

**Method** : `GET`

**Auth required** : NO

**Data**: `{}`

## Success Response

**Condition** : If the hash was correctly signed and unique.

**Code** : `200 OK`

**Content example**

```
signed OK dummybank.rowit.co.uk 2804fbcd69ab84e985955431a5dcdb20b0d999be2bf9e90280cdf030e94c15dc eJjT99Tm52IDe8s/K9agRvTz2t7EmJYWjV5pOGyUEaAg4m8XZXmyg5fPbN7hFK64SHKIcdLheovr4+svsqx6USlr0rsaZODAAZ8Nv4habUIw/cZgVQ1adKjeF4lEqVUBlcepYbukkiI1dLs2dOgBunVBaLXY8POrTCKrFNEcw5udwGC/hUUTvKKxGYwFU3sn3mCCZQIReQ57UjFBJ7rScJaRAqJdfQSzEbqs9Zz+byUEYLEfqYdXLePzrryDqT3/SmxmhnsLKBHzBpYXY630D92ZETqTj1yuGy57LIk0SbC9OXetYWKzDI4JeWk0TSh77a 
```

## Error Responses

**Condition** : If the hash was correctly signed, but the hash already exists in the database.

**Code** : `200 OK`

**Content** :
```
duplicate already in db 2804fbcd69ab84e985955431a5dcdb20b0d999be2bf9e90280cdf030e94c15dc
```
### Or

**Condition** : If the signature was not correct for this URL and hash. This can be that the format was wrong - e.g. incorrect base64 then url encoding or just it is wrong.

**Code** : `200 OK`

**Content** :

```
Invalid signature
```
