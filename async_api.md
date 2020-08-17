# Sign up to 100,000 files  asynchronously

Sends the hash and signature of multiple files to the umpint.com service in one request. 
Once a file has been signed if a user drags and drops that file onto the checking area of the 
umpint.com website they will see it as valid.

Recommended maximum size is 100,000 pairs of hashes and signatures. As this is about 45Meg as any 
larger is an issue with HTTPS.

When you submit a batch we reply instantly with a UUID. You can then use this UUID to retrieve the 
status of the upload. Generally 100,000 signatures will be processed in 10 minutes. 
If this is too slow for you please say and as we can easily scale outwards on our cloud infrastructure.

If you need to send more just send multiple batches either in series or parallel. 
If this is an issue for your use-case contact us explaining why and we will test with 
higher numbers and then increase the recommended maximum. Or provide an alternative interface 
e.g. we could change the interface so you sftp the data, or we get it from your website. 
We are happy to consider any suggestion that makes your life easier, and we think others will also use.

**URL** : 

`/api/v1/asyncsign/:url`

**URL Parameters** : 

`url=[string]` where `url` is the url of the site that is signing the file. You must have access to the private key of the ssl certificate of the site to be able to compute a valid signature.

**Query Parameters** : `None`

**Method** : `POST`

**Auth required** : `NO`

**Data**: `[ [ "hash1", "signature1" ], [ "hash2", "signature2" ], ...., [ "hashN", "signatureN" ] ]`

Data is a JSON list of list of strings.

`hash1...N` : The hexstring hash of the files you want to authenticate.

`signature1..N` : The url encoded base64 encoded signature of the sha256 hash of the hexstring. 
Note the url encoding - this is to be consistent with the single signing api call.
Note 2. There should be no \n at the end of the hash string that you sign - this is an easy mistake to make.

## Success Response

**Condition** : Data received.

**Code** : `200 OK`

**Content example**

Below shows 2345 hashes having been succesfully added to the queue and we are processing them in 3 batches. This is the expected response if you sent 2345 pairs of hash and signatures.
```
{
  "UUID" : "06b0ac02-bb62-4052-b48b-d875e26561f8",
  "Batches" : "3",
  "Hashes" : "2345"
}

```

## Polling - Summary

Once you have sent a request you can poll the status. We recommend waiting about 1 minute between polls.

**URL** : 

`/api/v1/resultasync/:uuid`

**URL Parameters** : 

`uuid=[string]` where `uuid` is the uuid that was returned from the initial upload call above.

**Query Parameters** : `None`

**Method** : `GET`

**Auth required** : `NO`

## Success Response

**Condition** : UUID is correct and was sent in last 48 hours.

**Code** : `200 OK`

**Content example**

```{"waitingBatch":1,"doneBatch":2,"okHash":2000,"badSigHash":0,"dupHash":0,"errorHash":0}```

`waitingBatch` is the number of batches of 1000 hashes that are still to be processed.

`doneBatch` is the number of batches that have been completed.

`okHash` is the number of hashes that we confirmed were signed correctly and have been added to our database.

`badSigHash` is the number of hashes that had an incorrect signature and have not been added.

`dupHash` is the number of hashes that were already in our database and so were not added.

`errorHash` is number of other errors we had.

Once `waitingBatch` is zero your upload has completed. If okHash matches the number of files you sent then all is OK. If not you can check details of the issues using the call below.


#Polling - Details

**URL** : 

`/api/v1/resultasync/:uuid/:batch`

**URL Parameters** : 

`:uuid=[string]` where `:uuid` is the uuid that was returned from the initial upload call above.
`:batch=[string]` where `:batch` is the batch id. It must be between 1 and the total number of `doneBatch` from other call.

**Query Parameters** : `None`

**Method** : `GET`

**Auth required** : `NO`

## Success Response

**Condition** : UUID is correct and was sent in last 48 hours and the batch id existed.

**Code** : `200 OK`

**Content example**

```
{
  "addedOk" : [ "6f4205fbedcd7711304ecff56f9170d578377a5a335cb7e47bc2bcf08858d824", "67b6a0619e2e6f06b3117e1428df14091627f183074249c65e1cd4ebc7d3083b" ],
  "badSignature" : [ ],
  "duplicate" : [ ],
  "errors" : { }
}
```

The output is identical to the output for the [Sign Multiple Documents](batch_sign.md) API. 
See that for details of the 4 sections.


