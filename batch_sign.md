# Sign a number of file hashes and signatures in one batch and send to umpint.com

Sends the hash and singature of a multiple files to the umpint.com service. 
Once a files has been signed if a user drags and drops that file onto the checking area of the umpint.com website they will see it as valid.

Recommended maximum size is 10,000 pairs of hashes and signatures.
If you need to send more just send multiple batches in either series or parallel. 
If this is an issue for your use-case contact us explaining why and we will test with higher numbers and then increase the recommended maximum.


**URL** : 

`/api/v1/sign/:url`

**URL Parameters** : 

`url=[string]` where `url` is the url of the site that is signing the file. You must have access to the private key of the ssl certificate of the site to be able to compute a valid signature.

**Query Parameters** : `None`

**Method** : `POST`

**Auth required** : `NO`

**Data**: `[ [ hash1, signature1 ], [ hash2, signature2 ], ...., [ hashN, signatureN ] ]`

`hash1...N` : The hexstring hash of the files you want to authenticate - note there should be no \n at the end of the string.

`signature1..N` : The url encoded base64 encoded signature of the sha256 hash of the hexstring. 
Note the url encoding - this is to be consistent with the single signing api call.


## Success Response

**Condition** : If the hash was correctly signed and unique.

**Code** : `200 OK`

**Content example**

Below shows 2 hashes having been succesfully added.
```
{
  "addedOk" : [ "6f4205fbedcd7711304ecff56f9170d578377a5a335cb7e47bc2bcf08858d824", "67b6a0619e2e6f06b3117e1428df14091627f183074249c65e1cd4ebc7d3083b" ],
  "badSignature" : [ ],
  "duplicate" : [ ],
  "errors" : { }
}
```

## Error Responses

**Condition** : If the hash was correctly signed, but the hash already exists in the database.

**Code** : `200 OK`

**Content** :
Here were no hashes were added sucessfully, but 2 were found to be duplicates in the database.
```
{
  "addedOk" : [ ],
  "badSignature" : [ ],
  "duplicate" : [  "6f4205fbedcd7711304ecff56f9170d578377a5a335cb7e47bc2bcf08858d824", "67b6a0619e2e6f06b3117e1428df14091627f183074249c65e1cd4ebc7d3083b" ],
  "errors" : { }
}
``````
### Or

**Condition** : If the signature was not correct for this URL and hash. This can be that the format was wrong - e.g. incorrect base64 then url encoding or just it is wrong.

**Code** : `200 OK`

**Content** :

```
{
  "addedOk" : [ ],
  "badSignature" : [ "6f4205fbedcd7711304ecff56f9170d578377a5a335cb7e47bc2bcf08858d824", "67b6a0619e2e6f06b3117e1428df14091627f183074249c65e1cd4ebc7d3083b" ],
  "duplicate" : [   ],
  "errors" : { }
}
```

### Or

If there was an issue upload any hashes the errors section may contain a message explaining why.

Also it should be noted that since the request may contain many hashes and signatures that each of addedOK, badSignature and duplicate may contain zero or more elements.




